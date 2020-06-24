---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: aa2d828c824c5d81c5aa20ba4d3c6880633088ed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85279067"
---
# <a name="module-initializers"></a><span data-ttu-id="be588-101">Initialiseurs de module</span><span class="sxs-lookup"><span data-stu-id="be588-101">Module Initializers</span></span>

* <span data-ttu-id="be588-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="be588-102">[x] Proposed</span></span>
* <span data-ttu-id="be588-103">[] Prototype : [en cours](https://github.com/jnm2/roslyn/tree/module_initializer)</span><span class="sxs-lookup"><span data-stu-id="be588-103">[ ] Prototype: [In Progress](https://github.com/jnm2/roslyn/tree/module_initializer)</span></span>
* <span data-ttu-id="be588-104">[] Implémentation : [en cours](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span><span class="sxs-lookup"><span data-stu-id="be588-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span></span>
* <span data-ttu-id="be588-105">[] Spécification : [non démarrée]()</span><span class="sxs-lookup"><span data-stu-id="be588-105">[ ] Specification: [Not Started]()</span></span>

## <a name="summary"></a><span data-ttu-id="be588-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="be588-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="be588-107">Bien que la plateforme .NET dispose d’une [fonctionnalité](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) qui prend directement en charge l’écriture du code d’initialisation de l’assembly (techniquement, le module), elle n’est pas exposée en C#.</span><span class="sxs-lookup"><span data-stu-id="be588-107">Although the .NET platform has a [feature](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) that directly supports writing initialization code for the assembly (technically, the module), it is not exposed in C#.</span></span>  <span data-ttu-id="be588-108">Il s’agit d’un scénario plutôt que des niches, mais une fois que vous y êtes dans, les solutions semblent assez pénibles.</span><span class="sxs-lookup"><span data-stu-id="be588-108">This is a rather niche scenario, but once you run into it the solutions appear to be pretty painful.</span></span>  <span data-ttu-id="be588-109">Il existe des rapports d' [un certain nombre de clients](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (à l’intérieur et à l’extérieur de Microsoft) qui éprouvent le problème et il n’y a aucun doute sur les cas non documentés.</span><span class="sxs-lookup"><span data-stu-id="be588-109">There are reports of [a number of customers](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (inside and outside Microsoft) struggling with the problem, and there are no doubt more undocumented cases.</span></span>

## <a name="motivation"></a><span data-ttu-id="be588-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="be588-110">Motivation</span></span>
[motivation]: #motivation

- <span data-ttu-id="be588-111">Permettez aux bibliothèques d’effectuer des initialisations opportunes et ponctuelles lorsqu’elles sont chargées, avec une charge minimale et sans que l’utilisateur ait besoin d’appeler explicitement quoi que ce soit</span><span class="sxs-lookup"><span data-stu-id="be588-111">Enable libraries to do eager, one-time initialization when loaded, with minimal overhead and without the user needing to explicitly call anything</span></span>
- <span data-ttu-id="be588-112">Un point délicat particulier des approches actuelles du `static` constructeur est que le runtime doit effectuer des vérifications supplémentaires sur l’utilisation d’un type avec un constructeur statique, afin de déterminer si le constructeur statique doit être exécuté ou non.</span><span class="sxs-lookup"><span data-stu-id="be588-112">One particular pain point of current `static` constructor approaches is that the runtime must do additional checks on usage of a type with a static constructor, in order to decide whether the static constructor needs to be run or not.</span></span> <span data-ttu-id="be588-113">Cela ajoute une surcharge mesurable.</span><span class="sxs-lookup"><span data-stu-id="be588-113">This adds measurable overhead.</span></span>
- <span data-ttu-id="be588-114">Activer les générateurs de source pour exécuter une logique d’initialisation globale sans que l’utilisateur doive appeler explicitement quoi que ce soit</span><span class="sxs-lookup"><span data-stu-id="be588-114">Enable source generators to run some global initialization logic without the user needing to explicitly call anything</span></span>

## <a name="detailed-design"></a><span data-ttu-id="be588-115">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="be588-115">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="be588-116">Une méthode peut être désignée en tant qu’initialiseur de module en la décorant avec un `[ModuleInitializer]` attribut.</span><span class="sxs-lookup"><span data-stu-id="be588-116">A method can be designated as a module initializer by decorating it with a `[ModuleInitializer]` attribute.</span></span>

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

<span data-ttu-id="be588-117">L’attribut peut être utilisé comme suit :</span><span class="sxs-lookup"><span data-stu-id="be588-117">The attribute can be used like this:</span></span>

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

<span data-ttu-id="be588-118">Certaines exigences sont imposées sur la méthode ciblée avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="be588-118">Some requirements are imposed on the method targeted with this attribute:</span></span>
1. <span data-ttu-id="be588-119">La méthode doit être `static` .</span><span class="sxs-lookup"><span data-stu-id="be588-119">The method must be `static`.</span></span>
1. <span data-ttu-id="be588-120">La méthode doit être sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="be588-120">The method must be parameterless.</span></span>
1. <span data-ttu-id="be588-121">La méthode doit retourner `void`.</span><span class="sxs-lookup"><span data-stu-id="be588-121">The method must return `void`.</span></span>
1. <span data-ttu-id="be588-122">La méthode ne doit pas être générique ou être contenue dans un type générique.</span><span class="sxs-lookup"><span data-stu-id="be588-122">The method must not be generic or be contained in a generic type.</span></span>
1. <span data-ttu-id="be588-123">La méthode doit être accessible à partir du module conteneur.</span><span class="sxs-lookup"><span data-stu-id="be588-123">The method must be accessible from the containing module.</span></span>
    - <span data-ttu-id="be588-124">Cela signifie que l’accessibilité effective de la méthode doit être `internal` ou `public` .</span><span class="sxs-lookup"><span data-stu-id="be588-124">This means the method's effective accessibility must be `internal` or `public`.</span></span>
    - <span data-ttu-id="be588-125">Cela signifie également que la méthode ne peut pas être une fonction locale.</span><span class="sxs-lookup"><span data-stu-id="be588-125">This also means the method cannot be a local function.</span></span>
    
<span data-ttu-id="be588-126">Quand une ou plusieurs méthodes valides avec cet attribut sont trouvées dans une compilation, le compilateur émet un initialiseur de module qui appelle chacune des méthodes attribuées.</span><span class="sxs-lookup"><span data-stu-id="be588-126">When one or more valid methods with this attribute are found in a compilation, the compiler will emit a module initializer which calls each of the attributed methods.</span></span> <span data-ttu-id="be588-127">Les appels seront émis dans un ordre réservé, mais déterministe.</span><span class="sxs-lookup"><span data-stu-id="be588-127">The calls will be emitted in a reserved, but deterministic order.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="be588-128">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="be588-128">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="be588-129">Pourquoi n’est-ce *pas* ?</span><span class="sxs-lookup"><span data-stu-id="be588-129">Why should we *not* do this?</span></span>

- <span data-ttu-id="be588-130">Les outils tiers existants pour « l’injection » d’initialiseurs de module sont peut-être suffisants pour les utilisateurs qui ont demandé cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="be588-130">Perhaps the existing third-party tooling for "injecting" module initializers is sufficient for users who have been asking for this feature.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="be588-131">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="be588-131">Design meetings</span></span>

### <a name="april-8th-2020"></a>[<span data-ttu-id="be588-132">8 avril 2020</span><span class="sxs-lookup"><span data-stu-id="be588-132">April 8th, 2020</span></span>](/meetings/2020/LDM-2020-04-08.md#module-initializers)
