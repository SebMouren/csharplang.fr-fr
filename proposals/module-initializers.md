---
ms.openlocfilehash: 7522bee6ac14d205aaf2a8491c13a321d2c18d62
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598580"
---
# <a name="module-initializers"></a><span data-ttu-id="db518-101">Initialiseurs de module</span><span class="sxs-lookup"><span data-stu-id="db518-101">Module Initializers</span></span>

* <span data-ttu-id="db518-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="db518-102">[x] Proposed</span></span>
* <span data-ttu-id="db518-103">[] Prototype : [en cours](https://github.com/jnm2/roslyn/tree/module_initializer)</span><span class="sxs-lookup"><span data-stu-id="db518-103">[ ] Prototype: [In Progress](https://github.com/jnm2/roslyn/tree/module_initializer)</span></span>
* <span data-ttu-id="db518-104">[] Implémentation : [en cours](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span><span class="sxs-lookup"><span data-stu-id="db518-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span></span>
* <span data-ttu-id="db518-105">[] Spécification : [non démarrée]()</span><span class="sxs-lookup"><span data-stu-id="db518-105">[ ] Specification: [Not Started]()</span></span>

## <a name="summary"></a><span data-ttu-id="db518-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="db518-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="db518-107">Bien que la plateforme .NET dispose d’une fonctionnalité qui prend directement en charge l’écriture du code d’initialisation de l’assembly (techniquement, le module), elle n’est pas exposée en C#.</span><span class="sxs-lookup"><span data-stu-id="db518-107">Although the .NET platform has a feature that directly supports writing initialization code for the assembly (technically, the module), it is not exposed in C#.</span></span>  <span data-ttu-id="db518-108">Il s’agit d’un scénario plutôt que des niches, mais une fois que vous y êtes dans, les solutions semblent assez pénibles.</span><span class="sxs-lookup"><span data-stu-id="db518-108">This is a rather niche scenario, but once you run into it the solutions appear to be pretty painful.</span></span>  <span data-ttu-id="db518-109">Il existe des rapports d' [un certain nombre de clients](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (à l’intérieur et à l’extérieur de Microsoft) qui éprouvent le problème et il n’y a aucun doute sur les cas non documentés.</span><span class="sxs-lookup"><span data-stu-id="db518-109">There are reports of [a number of customers](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (inside and outside Microsoft) struggling with the problem, and there are no doubt more undocumented cases.</span></span>

## <a name="motivation"></a><span data-ttu-id="db518-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="db518-110">Motivation</span></span>
[motivation]: #motivation

- <span data-ttu-id="db518-111">Permettez aux bibliothèques d’effectuer des initialisations opportunes et ponctuelles lorsqu’elles sont chargées, avec une charge minimale et sans que l’utilisateur ait besoin d’appeler explicitement quoi que ce soit</span><span class="sxs-lookup"><span data-stu-id="db518-111">Enable libraries to do eager, one-time initialization when loaded, with minimal overhead and without the user needing to explicitly call anything</span></span>
- <span data-ttu-id="db518-112">Un point délicat particulier des approches `static` actuelles du constructeur est que le runtime doit effectuer des vérifications supplémentaires sur l’utilisation d’un type avec un constructeur statique, afin de déterminer si le constructeur statique doit être exécuté ou non.</span><span class="sxs-lookup"><span data-stu-id="db518-112">One particular pain point of current `static` constructor approaches is that the runtime must do additional checks on usage of a type with a static constructor, in order to decide whether the static constructor needs to be run or not.</span></span> <span data-ttu-id="db518-113">Cela ajoute une surcharge mesurable.</span><span class="sxs-lookup"><span data-stu-id="db518-113">This adds measurable overhead.</span></span>
- <span data-ttu-id="db518-114">Activer les générateurs de source pour exécuter une logique d’initialisation globale sans que l’utilisateur doive appeler explicitement quoi que ce soit</span><span class="sxs-lookup"><span data-stu-id="db518-114">Enable source generators to run some global initialization logic without the user needing to explicitly call anything</span></span>

## <a name="detailed-design"></a><span data-ttu-id="db518-115">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="db518-115">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="db518-116">Le compilateur C# reconnaît l’attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="db518-116">The C# compiler will recognize the following attribute:</span></span>

``` c#
namespace System.Runtime.CompilerServices
{
    // Note: an Obsolete attribute is not needed here,
    // because only C# 9 compilers will have access to attributes added in .NET 5.
    // LangVersion checks will still be necessary.
    [AttributeUsage(AttributeTargets.Module, AllowMultiple = false)]
    public class ModuleInitializerAttribute : Attribute
    {
        public ModuleInitializerAttribute(Type type) { }
    }
}
```

<span data-ttu-id="db518-117">Vous pouvez l’utiliser comme suit</span><span class="sxs-lookup"><span data-stu-id="db518-117">You would use it like this</span></span>

``` c#
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyModuleInitializer))]

internal static class MyModuleInitializer
{
    static MyModuleInitializer()
    {
        // put your module initializer here
    }
}
```

<span data-ttu-id="db518-118">et le compilateur C# émettra alors un constructeur de module qui provoque le déclenchement du constructeur statique du type identifié :</span><span class="sxs-lookup"><span data-stu-id="db518-118">and the C# compiler would then emit a module constructor that causes the static constructor of the identified type to be triggered:</span></span>

``` c#
void .cctor()
{
    // synthesize and call a dummy method with an unspeakable name,
    // which will cause the runtime to call the static constructor
    MyModuleInitializer.<TriggerClassConstructor>();
}
```

<span data-ttu-id="db518-119">Notez que la surcharge liée à la vérification de l’exécution du constructeur statique sur un type d’initialiseur de module (`MyModuleInitializer` dans cet exemple) peut être réduite au minimum en référençant le `[module: ModuleInitializer(typeof(MyModuleInitializer))]` type uniquement dans l’attribut.</span><span class="sxs-lookup"><span data-stu-id="db518-119">Note that the overhead of checking whether the static constructor has run on a module initializer type (`MyModuleInitializer` in this example) can be kept to a minimum by referencing the type only in the `[module: ModuleInitializer(typeof(MyModuleInitializer))]` attribute.</span></span>

<span data-ttu-id="db518-120">Cette proposition utilise `AttributeUsage(AllowMultiple = false)` pour interdire à l’utilisateur de déclarer plusieurs classes d’initialiseurs sans ajouter de règles de langage spéciales pour y parvenir.</span><span class="sxs-lookup"><span data-stu-id="db518-120">This proposal uses `AttributeUsage(AllowMultiple = false)` to disallow the user from declaring more than one initializer class without adding any special language rules to accomplish that.</span></span> <span data-ttu-id="db518-121">Il est très léger en ce qu’il utilise la syntaxe et les règles sémantiques existantes pour les attributs.</span><span class="sxs-lookup"><span data-stu-id="db518-121">It is very lightweight in that it uses existing syntax and semantic rules for attributes.</span></span> <span data-ttu-id="db518-122">En revanche, il peut être plus difficile pour les utilisateurs de se rendre compte que le constructeur statique qu’ils examinent est l’initialiseur de module, car l’attribut peut être éloigné de la classe qui contient réellement l’initialiseur de module, et il est également nécessaire de décider ce qui se passe si l’attribut est utilisé pour référencer un type d’un autre assembly.</span><span class="sxs-lookup"><span data-stu-id="db518-122">On the other hand, it may make it more difficult for users to realize that the static constructor they are looking at is the module initializer, because the attribute may be far away from the class that actually contains the module initializer, and it also requires us to decide what happens if the attribute is used to reference a type from another assembly.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="db518-123">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="db518-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

#### <a name="should-we-permit-multiple-types-to-be-decorated-with-moduleinitializerattribute-in-a-compilation-if-so-in-what-order-should-the-static-constructors-be-invoked"></a><span data-ttu-id="db518-124">Devons-nous autoriser le décor de plusieurs types avec ModuleInitializerAttribute dans une compilation ?</span><span class="sxs-lookup"><span data-stu-id="db518-124">Should we permit multiple types to be decorated with ModuleInitializerAttribute in a compilation?</span></span> <span data-ttu-id="db518-125">Dans ce cas, dans quel ordre les constructeurs statiques doivent-ils être appelés ?</span><span class="sxs-lookup"><span data-stu-id="db518-125">If so, in what order should the static constructors be invoked?</span></span>

<span data-ttu-id="db518-126">Une option : effectuez la même opération pour les initialiseurs statiques dans les classes partielles.</span><span class="sxs-lookup"><span data-stu-id="db518-126">One option: do the same thing we do for static initializers in partial classes.</span></span> <span data-ttu-id="db518-127">Ordonnez-les en fonction de l’ordre des fichiers et de la position source.</span><span class="sxs-lookup"><span data-stu-id="db518-127">Order them based on file order+source position.</span></span>

#### <a name="should-we-permit-using-a-type-from-another-assembly-as-the-module-initializer"></a><span data-ttu-id="db518-128">Faut-il autoriser l’utilisation d’un type d’un autre assembly comme initialiseur de module ?</span><span class="sxs-lookup"><span data-stu-id="db518-128">Should we permit using a type from another assembly as the module initializer?</span></span>

<span data-ttu-id="db518-129">destin`[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`</span><span class="sxs-lookup"><span data-stu-id="db518-129">e.g.  `[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`</span></span>

<span data-ttu-id="db518-130">Cela peut compliquer l’appel du constructeur de classe en appelant simplement une méthode factice, car nous ne pouvons pas synthétiser une telle méthode sur le type.</span><span class="sxs-lookup"><span data-stu-id="db518-130">This could make it difficult to invoke the class constructor simply by calling a dummy method, because we won't be able to synthesize such a method on the type.</span></span> <span data-ttu-id="db518-131">Nous pourrions envisager de revenir à `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` dans ce cas, mais il introduit une dépendance sur la pile de réflexion.</span><span class="sxs-lookup"><span data-stu-id="db518-131">We could consider falling back to `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` in this case, but it introduces a dependency on the reflection stack.</span></span>

<span data-ttu-id="db518-132">Ce cas d’usage implique que la classe d’initialiseur n’est pas l’initialiseur de module pour l’assembly dans lequel il est déclaré.</span><span class="sxs-lookup"><span data-stu-id="db518-132">This use case implies that the initializer class is not the module initializer for the assembly it is declared in.</span></span> <span data-ttu-id="db518-133">Il semble que ce cas d’usage doit plutôt être géré en exposant une méthode que le consommateur externe doit appeler à partir de son initialiseur de module.</span><span class="sxs-lookup"><span data-stu-id="db518-133">It feels like this use case should instead by handled by exposing a method that the external consumer should call from their module initializer.</span></span>

```cs
// Assembly 1
public class MyLibInit
{
    public static void Init() { }
}

// Assembly 2
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyInit))]

class MyInit
{
    static
    {
        MyLibInit.Init();
    }
}
```

## <a name="drawbacks"></a><span data-ttu-id="db518-134">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="db518-134">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="db518-135">Pourquoi n’est-ce *pas* ?</span><span class="sxs-lookup"><span data-stu-id="db518-135">Why should we *not* do this?</span></span>

- <span data-ttu-id="db518-136">Les outils tiers existants pour « l’injection » d’initialiseurs de module sont peut-être suffisants pour les utilisateurs qui ont demandé cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="db518-136">Perhaps the existing third-party tooling for "injecting" module initializers is sufficient for users who have been asking for this feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="db518-137">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="db518-137">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="db518-138">Quelles autres conceptions ont été prises en compte ?</span><span class="sxs-lookup"><span data-stu-id="db518-138">What other designs have been considered?</span></span> <span data-ttu-id="db518-139">Quel est l’impact de cette action ?</span><span class="sxs-lookup"><span data-stu-id="db518-139">What is the impact of not doing this?</span></span>

<span data-ttu-id="db518-140">Il existe plusieurs façons d’exposer cette fonctionnalité dans le langage :</span><span class="sxs-lookup"><span data-stu-id="db518-140">There are a number of possible ways of exposing this feature in the language:</span></span>

### <a name="1-special-global-method-declaration"></a><span data-ttu-id="db518-141">1. Déclaration de méthode globale spéciale</span><span class="sxs-lookup"><span data-stu-id="db518-141">1. Special global method declaration</span></span>

<span data-ttu-id="db518-142">Un initialiseur de module est fourni en écrivant un genre spécial de méthode dans la portée globale :</span><span class="sxs-lookup"><span data-stu-id="db518-142">A module initializer would be provided by writing a special kind of method in the global scope:</span></span>

```csharp
internal void operator init() ...
```

<span data-ttu-id="db518-143">Cela donne à la nouvelle construction de langage sa propre syntaxe.</span><span class="sxs-lookup"><span data-stu-id="db518-143">This gives the new language construct its own syntax.</span></span> <span data-ttu-id="db518-144">Toutefois, étant donné la rare et la niche du scénario, il s’agit probablement beaucoup trop lourd d’une approche.</span><span class="sxs-lookup"><span data-stu-id="db518-144">However, given how rare and niche the scenario is, this is probably far too heavyweight an approach.</span></span>

### <a name="2-attribute-on-the-type-to-be-initialized"></a><span data-ttu-id="db518-145">2. attribut sur le type à initialiser</span><span class="sxs-lookup"><span data-stu-id="db518-145">2. Attribute on the type to be initialized</span></span>

<span data-ttu-id="db518-146">Au lieu d’un attribut de niveau module, peut-être que l’attribut est placé sur le type à initialiser</span><span class="sxs-lookup"><span data-stu-id="db518-146">Instead of a module-level attribute, perhaps the attribute would be placed on the type to be initialized</span></span>

```csharp
[ModuleInitializer]
class ToInitialize
{
    static ToInitialize() ...
}
```

<span data-ttu-id="db518-147">Avec cette approche, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="db518-147">With this approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="db518-148">Dans les deux cas, il est plus complexe que la proposition d’origine ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="db518-148">Either way, it is more complex than the original proposal above.</span></span>

#### <a name="3-attribute-on-the-static-constructor-to-be-initialized"></a><span data-ttu-id="db518-149">3. attribut sur le constructeur statique à initialiser</span><span class="sxs-lookup"><span data-stu-id="db518-149">3. Attribute on the static constructor to be initialized</span></span>

<span data-ttu-id="db518-150">Au lieu d’un attribut au niveau du module, peut-être que l’attribut serait placé sur le constructeur statique à initialiser</span><span class="sxs-lookup"><span data-stu-id="db518-150">Instead of a module-level attribute, perhaps the attribute would be placed on the static constructor to be initialized</span></span>

```csharp
class ToInitialize
{
    [ModuleInitializer]
    static ToInitialize() ...
}
```

<span data-ttu-id="db518-151">Avec cette approche, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="db518-151">With this approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="db518-152">Dans les deux cas, il est plus complexe que la proposition d’origine ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="db518-152">Either way, it is more complex than the original proposal above.</span></span>

#### <a name="4-attribute-on-a-static-method-to-be-called"></a><span data-ttu-id="db518-153">4. attribut sur une méthode statique à appeler</span><span class="sxs-lookup"><span data-stu-id="db518-153">4. Attribute on a static method to be called</span></span>

<span data-ttu-id="db518-154">Au lieu d’un attribut au niveau du module, peut-être que l’attribut serait placé sur la méthode à appeler pour effectuer l’initialisation</span><span class="sxs-lookup"><span data-stu-id="db518-154">Instead of a module-level attribute, perhaps the attribute would be placed on the method to be called to perform the initialization</span></span>

```csharp
class Any
{
    [ModuleInitializer]
    static void Initializer() ...
}
```

<span data-ttu-id="db518-155">Comme dans l’approche précédente, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="db518-155">As in the previous approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="db518-156">Dans les deux cas, il est plus complexe que la proposition d’origine.</span><span class="sxs-lookup"><span data-stu-id="db518-156">Either way, it is more complex than the original proposal.</span></span> <span data-ttu-id="db518-157">Nous aurions également probablement besoin de rejeter toute méthode avec cet attribut si elle a des paramètres ou un type`void` de retour non-.</span><span class="sxs-lookup"><span data-stu-id="db518-157">We also would probably need to reject any method with this attribute if it has parameters or a non-`void` return type.</span></span>

#### <a name="5-do-nothing"></a><span data-ttu-id="db518-158">5. ne rien faire</span><span class="sxs-lookup"><span data-stu-id="db518-158">5. Do nothing</span></span>
<span data-ttu-id="db518-159">Si nous n’implémentons pas cette fonctionnalité, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="db518-159">If we do not implement this feature, then:</span></span>
- <span data-ttu-id="db518-160">Les utilisateurs qui ont vraiment besoin d’initialiseurs de module continuent de s’appuyer sur des outils tiers pour les injecter dans leurs assemblys.</span><span class="sxs-lookup"><span data-stu-id="db518-160">Users who really need module initializers continue to rely on third-party tooling to inject them into their assemblies.</span></span>
- <span data-ttu-id="db518-161">Les générateurs de code source doivent s’appuyer sur des constructeurs statiques qui ajoutent de la surcharge ou sur les méthodes Init () que les utilisateurs doivent appeler explicitement.</span><span class="sxs-lookup"><span data-stu-id="db518-161">Source generators will have to rely on static constructors which add overhead or on Init() methods that users must explicitly call.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="db518-162">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="db518-162">Design meetings</span></span>

### <a name="april-8th-2020"></a>[<span data-ttu-id="db518-163">8 avril 2020</span><span class="sxs-lookup"><span data-stu-id="db518-163">April 8th, 2020</span></span>](/meetings/2020/LDM-2020-04-08.md#module-initializers)
<span data-ttu-id="db518-164">Permettons à toute méthode statique d’être un initialiseur de module (option 4 dans la proposition ci-dessus) et de marquer cette méthode à l’aide d’un attribut connu.</span><span class="sxs-lookup"><span data-stu-id="db518-164">Let's let any static method be a module initializer (option 4 in the above proposal), and mark that method using a well-known attribute.</span></span> <span data-ttu-id="db518-165">Nous allons également autoriser plusieurs méthodes d’initialiseur de module, et elles seront appelées dans un ordre réservé, mais déterministe.</span><span class="sxs-lookup"><span data-stu-id="db518-165">We'll also allow multiple module initializer methods, and they will each be called in a reserved, but deterministic order.</span></span>
