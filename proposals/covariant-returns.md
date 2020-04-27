---
ms.openlocfilehash: 9cfc0758f16b2153d52faec1d19f0ecd817cde3b
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162085"
---
# <a name="covariant-return-types"></a><span data-ttu-id="61647-101">types de retour covariants</span><span class="sxs-lookup"><span data-stu-id="61647-101">covariant return types</span></span>

* <span data-ttu-id="61647-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="61647-102">[x] Proposed</span></span>
* <span data-ttu-id="61647-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="61647-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="61647-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="61647-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="61647-105">[X] spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="61647-105">[X] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="61647-106">Récapitulatif</span><span class="sxs-lookup"><span data-stu-id="61647-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="61647-107">Prennent en charge les _types de retour covariants_.</span><span class="sxs-lookup"><span data-stu-id="61647-107">Support _covariant return types_.</span></span> <span data-ttu-id="61647-108">En particulier, autorisez la substitution d’une méthode à retourner un type de retour plus dérivé que la méthode qu’elle substitue, et de la même manière pour autoriser la substitution d’une propriété en lecture seule à retourner un type de retour plus dérivé.</span><span class="sxs-lookup"><span data-stu-id="61647-108">Specifically, permit the override of a method to return a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to return a more derived return type.</span></span> <span data-ttu-id="61647-109">Les appelants de la méthode ou de la propriété recevaient statiquement le type de retour plus affiné à partir d’un appel, et les substitutions apparaissant dans des types plus dérivés seraient nécessaires pour fournir un type de retour au moins aussi spécifique que celui qui apparaît dans les substitutions dans ses types de base.</span><span class="sxs-lookup"><span data-stu-id="61647-109">Callers of the method or property would statically receive the more refined return type from an invocation, and overrides appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span>

## <a name="motivation"></a><span data-ttu-id="61647-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="61647-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="61647-111">Il s’agit d’un modèle courant dans le code selon lequel différents noms de méthode doivent être inventés pour contourner la contrainte de langage que les substitutions doivent retourner au même type que la méthode substituée.</span><span class="sxs-lookup"><span data-stu-id="61647-111">It is a common pattern in code that different method names have to be invented to work around the language constraint that overrides must return the same type as the overridden method.</span></span>

<span data-ttu-id="61647-112">Cela peut être utile dans le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="61647-112">This would be useful in the factory pattern.</span></span> <span data-ttu-id="61647-113">Par exemple, dans la base de code Roslyn, nous aurions</span><span class="sxs-lookup"><span data-stu-id="61647-113">For example, in the Roslyn code base we would have</span></span>

``` cs
class Compilation ...
{
    virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    override CSharpCompilation WithOptions(Options options)...
}
```

## <a name="detailed-design"></a><span data-ttu-id="61647-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="61647-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="61647-115">Il s’agit d’un brouillon de spécification proposée pour les [types de retour covariants](https://github.com/dotnet/csharplang/issues/49) en C#.</span><span class="sxs-lookup"><span data-stu-id="61647-115">This is a draft proposed specification for [covariant return types](https://github.com/dotnet/csharplang/issues/49) in C#.</span></span>  <span data-ttu-id="61647-116">Notre intention est de permettre à la substitution d’une méthode de retourner un type de retour plus dérivé que la méthode qu’elle substitue, et de la même façon d’autoriser la substitution d’une propriété en lecture seule pour retourner un type de retour plus dérivé.</span><span class="sxs-lookup"><span data-stu-id="61647-116">Our intent is to permit the override of a method to return a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to return a more derived return type.</span></span>  <span data-ttu-id="61647-117">Les appelants de la méthode ou de la propriété recevaient statiquement le type de retour plus affiné à partir d’un appel, et les substitutions apparaissant dans des types plus dérivés seraient nécessaires pour fournir un type de retour au moins aussi spécifique que celui qui apparaît dans les substitutions dans ses types de base.</span><span class="sxs-lookup"><span data-stu-id="61647-117">Callers of the method or property would statically receive the more refined return type from an invocation, and overrides appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span>

<span data-ttu-id="61647-118">Il s’agit d’un premier brouillon. il a donc été entièrement inventé.</span><span class="sxs-lookup"><span data-stu-id="61647-118">This is a first draft, so it was necessarily invented from scratch.</span></span>  <span data-ttu-id="61647-119">La plupart des idées introduites sont provisoires et peuvent être révisées ou éliminées lors de futures révisions.</span><span class="sxs-lookup"><span data-stu-id="61647-119">Many of the ideas introduced are tentative and may be revised or eliminated in future revisions.</span></span>

--------------

### <a name="class-method-override"></a><span data-ttu-id="61647-120">Substitution de méthode de classe</span><span class="sxs-lookup"><span data-stu-id="61647-120">Class Method Override</span></span>

<span data-ttu-id="61647-121">[Contrainte existante sur les méthodes de substitution de classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods)</span><span class="sxs-lookup"><span data-stu-id="61647-121">The [existing constraint on class override](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods) methods</span></span>

> - <span data-ttu-id="61647-122">La méthode de substitution et la méthode de base substituée ont le même type de retour.</span><span class="sxs-lookup"><span data-stu-id="61647-122">The override method and the overridden base method have the same return type.</span></span>

<span data-ttu-id="61647-123">est modifié en</span><span class="sxs-lookup"><span data-stu-id="61647-123">is modified to</span></span>

> - <span data-ttu-id="61647-124">La méthode de substitution doit avoir un type de retour convertible par une identité ou une conversion de référence implicite en type de retour de la méthode de base substituée.</span><span class="sxs-lookup"><span data-stu-id="61647-124">The override method must have have a return type that is convertible by an identity or implicit reference conversion to the return type of the overridden base method.</span></span>

<span data-ttu-id="61647-125">Et les conditions supplémentaires suivantes sont ajoutées à cette liste :</span><span class="sxs-lookup"><span data-stu-id="61647-125">And the following additional requirements are appended to that list:</span></span>

> - <span data-ttu-id="61647-126">La méthode de substitution doit avoir un type de retour convertible par une identité ou une conversion de référence implicite en type de retour de chaque substitution de la méthode de base substituée qui est déclarée dans un type de base (direct ou indirect) de la méthode de substitution.</span><span class="sxs-lookup"><span data-stu-id="61647-126">The override method must have have a return type that is convertible by an identity or implicit reference conversion to the return type of every override of the overridden base method that is declared in a (direct or indirect) base type of the override method.</span></span>
> - <span data-ttu-id="61647-127">Le type de retour de la méthode de substitution doit être au moins aussi accessible que la méthode de substitution ([domaines d’accessibilité](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).</span><span class="sxs-lookup"><span data-stu-id="61647-127">The override method's return type must be at least as accessible as the override method  ([Accessibility domains](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).</span></span>

<span data-ttu-id="61647-128">Cette contrainte permet à une méthode de substitution dans `private` une classe d’avoir `private` un type de retour.</span><span class="sxs-lookup"><span data-stu-id="61647-128">This constraint permits an override method in a `private` class to have a `private` return type.</span></span>  <span data-ttu-id="61647-129">Toutefois, `public` une méthode de substitution dans un `public` type doit avoir un `public` type de retour.</span><span class="sxs-lookup"><span data-stu-id="61647-129">However it requires a `public` override method in a `public` type to have a `public` return type.</span></span>

### <a name="class-property-and-indexer-override"></a><span data-ttu-id="61647-130">Substitution de la propriété de classe et de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="61647-130">Class Property and Indexer Override</span></span>

<span data-ttu-id="61647-131">[Contrainte existante sur les propriétés de substitution de classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors)</span><span class="sxs-lookup"><span data-stu-id="61647-131">The [existing constraint on class override](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors) properties</span></span>

> <span data-ttu-id="61647-132">Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité et nom que la propriété héritée, et il doit y avoir une conversion d’identité ~~entre le type de la substitution et la propriété héritée~~.</span><span class="sxs-lookup"><span data-stu-id="61647-132">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion ~~between the type of the overriding and the inherited property~~.</span></span> <span data-ttu-id="61647-133">Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur.</span><span class="sxs-lookup"><span data-stu-id="61647-133">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="61647-134">Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs.</span><span class="sxs-lookup"><span data-stu-id="61647-134">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="61647-135">est modifié en</span><span class="sxs-lookup"><span data-stu-id="61647-135">is modified to</span></span>

> <span data-ttu-id="61647-136">Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité et nom que la propriété héritée, et il doit y avoir une conversion d’identité **ou (si la propriété héritée est en lecture seule) une conversion de référence implicite du type de la propriété de substitution vers le type de la propriété héritée**.</span><span class="sxs-lookup"><span data-stu-id="61647-136">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion **or (if the inherited property is read-only) implicit reference conversion from the type of the overriding property to the type of the inherited property**.</span></span> <span data-ttu-id="61647-137">Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur.</span><span class="sxs-lookup"><span data-stu-id="61647-137">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="61647-138">Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs.</span><span class="sxs-lookup"><span data-stu-id="61647-138">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span> <span data-ttu-id="61647-139">**Le type de la propriété de substitution doit être au moins aussi accessible que la propriété de substitution ([domaines d’accessibilité](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**</span><span class="sxs-lookup"><span data-stu-id="61647-139">**The overriding property's type must be at least as accessible as the overriding property ([Accessibility domains](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**</span></span>

<span data-ttu-id="61647-140">***Le reste de la spécification préliminaire ci-dessous propose une extension supplémentaire des retours covariants de méthodes d’interface à prendre en compte ultérieurement.***</span><span class="sxs-lookup"><span data-stu-id="61647-140">***The remainder of the draft specification below proposes a further extension to covariant returns of interface methods to be considered later.***</span></span>

### <a name="interface-method-property-and-indexer-override"></a><span data-ttu-id="61647-141">Substitution de la méthode d’interface, de la propriété et de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="61647-141">Interface Method, Property, and Indexer Override</span></span>

<span data-ttu-id="61647-142">En ajoutant aux genres de membres autorisés dans une interface avec l’ajout de la fonctionnalité DIM en C# 8,0, nous ajoutons davantage de prise en `override` charge des membres avec les retours covariants.</span><span class="sxs-lookup"><span data-stu-id="61647-142">Adding to the kinds of members that are permitted in an interface with the addition of the DIM feature in C# 8.0, we further add support for `override` members along with covariant returns.</span></span>  <span data-ttu-id="61647-143">Celles-ci suivent les `override` règles des membres telles qu’elles sont spécifiées pour les classes, avec les différences suivantes :</span><span class="sxs-lookup"><span data-stu-id="61647-143">These follow the rules of `override` members as specified for classes, with the following differences:</span></span>

<span data-ttu-id="61647-144">Le texte suivant dans les classes :</span><span class="sxs-lookup"><span data-stu-id="61647-144">The following text in classes:</span></span>

> <span data-ttu-id="61647-145">La méthode substituée par une déclaration override est connue sous le nom de ***méthode de base substituée***.</span><span class="sxs-lookup"><span data-stu-id="61647-145">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="61647-146">`M` Pour une méthode de substitution déclarée dans une `C`classe, la méthode de base substituée est déterminée en examinant chaque `C`classe de base de, en commençant par `C` la classe de base directe de et en continuant avec chaque classe de base directe, jusqu’à ce que dans un type de classe de base donné `M` , au moins une méthode accessible ait la même signature que après la substitution des arguments de type.</span><span class="sxs-lookup"><span data-stu-id="61647-146">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class of `C`, starting with the direct base class of `C` and continuing with each successive direct base class, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span>

<span data-ttu-id="61647-147">reçoit la spécification correspondante pour les interfaces :</span><span class="sxs-lookup"><span data-stu-id="61647-147">is given the corresponding specification for interfaces:</span></span>

> <span data-ttu-id="61647-148">La méthode substituée par une déclaration override est connue sous le nom de ***méthode de base substituée***.</span><span class="sxs-lookup"><span data-stu-id="61647-148">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="61647-149">Pour `M` une méthode de substitution déclarée dans une `I`interface, la méthode de base substituée est déterminée en examinant chaque interface de base `I`directe ou indirecte de, en collectant le jeu d’interfaces déclarant une méthode `M` accessible qui a la même signature qu’après la substitution des arguments de type.</span><span class="sxs-lookup"><span data-stu-id="61647-149">For an override method `M` declared in an interface `I`, the overridden base method is determined by examining each direct or indirect base interface of `I`, collecting the set of interfaces declaring an accessible method which has the same signature as `M` after substitution of type arguments.</span></span>  <span data-ttu-id="61647-150">Si cet ensemble d’interfaces a un *type le plus dérivé*, sur lequel il existe une conversion d’identité ou de référence implicite à partir de chaque type de cet ensemble, et que ce type contient une déclaration de méthode unique, il s’agit de la *méthode de base substituée*.</span><span class="sxs-lookup"><span data-stu-id="61647-150">If this set of interfaces has a *most derived type*, to which there is an identity or implicit reference conversion from every type in this set, and that type contains a unique such method declaration, then that is the *overridden base method*.</span></span>

<span data-ttu-id="61647-151">Nous autorisons `override` de manière similaire les propriétés et les indexeurs dans les interfaces comme spécifié pour les classes dans les *accesseurs 15.7.6 Virtual, sealed, override et abstract*.</span><span class="sxs-lookup"><span data-stu-id="61647-151">We similarly permit `override` properties and indexers in interfaces as specified for classes in *15.7.6 Virtual, sealed, override, and abstract accessors*.</span></span>

### <a name="name-lookup"></a><span data-ttu-id="61647-152">Recherche de nom</span><span class="sxs-lookup"><span data-stu-id="61647-152">Name Lookup</span></span>

<span data-ttu-id="61647-153">La recherche de nom en présence de `override` déclarations de classe modifie actuellement le résultat de la recherche de nom en imposant sur les détails de membre `override` trouvés à partir de la déclaration la plus dérivée dans la hiérarchie de classes à partir du `this` type du qualificateur de l’identificateur (ou en l’absence de qualificateur).</span><span class="sxs-lookup"><span data-stu-id="61647-153">Name lookup in the presence of class `override` declarations currently modify the result of name lookup by imposing on the found member details from the most derived `override` declaration in the class hierarchy starting from the type of the identifier's qualifier (or `this` when there is no qualifier).</span></span>  <span data-ttu-id="61647-154">Par exemple, dans *12.6.2.2 paramètres correspondants* , nous avons</span><span class="sxs-lookup"><span data-stu-id="61647-154">For example, in *12.6.2.2 Corresponding parameters* we have</span></span>

> <span data-ttu-id="61647-155">Pour les méthodes et les indexeurs virtuels définis dans les classes, la liste de paramètres est choisie à partir de la première déclaration ou substitution du membre de fonction trouvé lors du démarrage avec le type statique du récepteur, et la recherche dans ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="61647-155">For virtual methods and indexers defined in classes, the parameter list is picked from the first  declaration or override of the function member found when starting with the static type of the receiver, and searching through its base classes.</span></span>

<span data-ttu-id="61647-156">pour cela, nous ajoutons</span><span class="sxs-lookup"><span data-stu-id="61647-156">to this we add</span></span>

> <span data-ttu-id="61647-157">Pour les méthodes et les indexeurs virtuels définis dans les interfaces, la liste de paramètres est prélevée à partir de la déclaration ou de la substitution du membre de fonction trouvé dans le type le plus dérivé parmi les types contenant la déclaration de substitution de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="61647-157">For virtual methods and indexers defined in interfaces, the parameter list is picked from the declaration or override of the function member found in the most derived type among those types containing the declaration of override of the function member.</span></span>  <span data-ttu-id="61647-158">Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.</span><span class="sxs-lookup"><span data-stu-id="61647-158">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="61647-159">Pour le type de résultat d’un accès à une propriété ou un indexeur, le texte existant</span><span class="sxs-lookup"><span data-stu-id="61647-159">For the result type of a property or indexer access, the existing text</span></span>

> - <span data-ttu-id="61647-160">Si I identifie une propriété d’instance, le résultat est un accès à une propriété avec une expression d’instance associée de E et un type associé qui est le type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="61647-160">If I identifies an instance property, then the result is a property access with an associated instance expression of E and an associated type that is the type of the property.</span></span> <span data-ttu-id="61647-161">Si T est un type de classe, le type associé est choisi dans la première déclaration ou la substitution de la propriété trouvée lors du démarrage de T, et la recherche dans ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="61647-161">If T is a class type, the associated type is picked from the first declaration or override of the property found when starting with T, and searching through its base classes.</span></span>

<span data-ttu-id="61647-162">est augmenté avec</span><span class="sxs-lookup"><span data-stu-id="61647-162">is augmented with</span></span>

> <span data-ttu-id="61647-163">Si T est un type interface, le type associé est choisi dans la déclaration ou la substitution de la propriété trouvée dans le le plus dérivé de T ou ses interfaces de base directes ou indirectes.</span><span class="sxs-lookup"><span data-stu-id="61647-163">If T is an interface type, the associated type is picked from the declaration or override of the property found in the most derived of T or its direct or indirect base interfaces.</span></span>  <span data-ttu-id="61647-164">Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.</span><span class="sxs-lookup"><span data-stu-id="61647-164">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="61647-165">Une modification similaire doit être apportée à l’accès à l' *indexeur 12.7.7.3*</span><span class="sxs-lookup"><span data-stu-id="61647-165">A similar change should be made in *12.7.7.3 Indexer access*</span></span>

<span data-ttu-id="61647-166">Dans les *expressions d’appel 12.7.6* , nous enrichissons le texte existant</span><span class="sxs-lookup"><span data-stu-id="61647-166">In *12.7.6 Invocation expressions* we augment the existing text</span></span>

> - <span data-ttu-id="61647-167">Sinon, le résultat est une valeur, avec un type associé du type de retour de la méthode ou du délégué.</span><span class="sxs-lookup"><span data-stu-id="61647-167">Otherwise, the result is a value, with an associated type of the return type of the method or delegate.</span></span> <span data-ttu-id="61647-168">Si l’appel est d’une méthode d’instance et que le récepteur est d’un type de classe T, le type associé est choisi dans la première déclaration ou la substitution de la méthode trouvée lors du démarrage avec T et la recherche dans ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="61647-168">If the invocation is of an instance method, and the receiver is of a class type T, the associated type is picked from the first declaration or override of the method found when starting with T and searching through its base classes.</span></span>

<span data-ttu-id="61647-169">par</span><span class="sxs-lookup"><span data-stu-id="61647-169">with</span></span>

> <span data-ttu-id="61647-170">Si l’appel est d’une méthode d’instance et que le récepteur est d’un type d’interface T, le type associé est choisi dans la déclaration ou la substitution de la méthode trouvée dans l’interface la plus dérivée parmi T et ses interfaces de base directes et indirectes.</span><span class="sxs-lookup"><span data-stu-id="61647-170">If the invocation is of an instance method, and the receiver is of an interface type T, the associated type is picked from the declaration or override of the method found in the most derived interface from among T and its direct and indirect base interfaces.</span></span>  <span data-ttu-id="61647-171">Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.</span><span class="sxs-lookup"><span data-stu-id="61647-171">It is a compile-time error if no unique such type exists.</span></span>

### <a name="implicit-interface-implementations"></a><span data-ttu-id="61647-172">Implémentations d’interface implicites</span><span class="sxs-lookup"><span data-stu-id="61647-172">Implicit Interface Implementations</span></span>

<span data-ttu-id="61647-173">Cette section de la spécification</span><span class="sxs-lookup"><span data-stu-id="61647-173">This section of the specification</span></span>

> <span data-ttu-id="61647-174">Dans le cadre du mappage d’interface, un `A` membre de classe correspond `B` à un membre d’interface lorsque :</span><span class="sxs-lookup"><span data-stu-id="61647-174">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="61647-175">`A`et `B` sont des méthodes, et le nom, le type et les listes de `A` paramètres `B` formels de et sont identiques.</span><span class="sxs-lookup"><span data-stu-id="61647-175">`A` and `B` are methods, and the name, type, and formal parameter lists of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61647-176">`A`et `B` sont des propriétés, le nom et le `A` type `B` de et sont identiques `A` et ont les mêmes accesseurs `B` que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface explicite).</span><span class="sxs-lookup"><span data-stu-id="61647-176">`A` and `B` are properties, the name and type of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>
> - <span data-ttu-id="61647-177">`A`et `B` sont des événements, et le nom et le `A` type `B` de et sont identiques.</span><span class="sxs-lookup"><span data-stu-id="61647-177">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61647-178">`A`et `B` sont des indexeurs, les listes de paramètres de type `A` et `B` formels de et `A` sont identiques, et ont les `B` mêmes`A` accesseurs que (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface explicite).</span><span class="sxs-lookup"><span data-stu-id="61647-178">`A` and `B` are indexers, the type and formal parameter lists of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>

<span data-ttu-id="61647-179">est modifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="61647-179">is modified as follows:</span></span>

> <span data-ttu-id="61647-180">Dans le cadre du mappage d’interface, un `A` membre de classe correspond `B` à un membre d’interface lorsque :</span><span class="sxs-lookup"><span data-stu-id="61647-180">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="61647-181">`A`et `B` sont des méthodes, et les listes de noms et de `A` paramètres `B` formels de et sont identiques, et `A` le type de retour de est convertible `B` en type de retour via une identité de conversion de référence implicite `B`vers le type de retour de.</span><span class="sxs-lookup"><span data-stu-id="61647-181">`A` and `B` are methods, and the name and formal parameter lists of `A` and `B` are identical, and the return type of `A` is convertible to the return type of `B` via an identity of implicit reference convertion to the return type of `B`.</span></span>
> - <span data-ttu-id="61647-182">`A`et `B` sont des propriétés, le nom `A` de `B` et sont identiques `A` , a les mêmes accesseurs `B` que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface `A` explicite) et le type de est `B` convertible en type de retour via une `A` conversion d’identité ou, si est une propriété ReadOnly, une conversion de référence implicite.</span><span class="sxs-lookup"><span data-stu-id="61647-182">`A` and `B` are properties, the name of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly property, an implicit reference conversion.</span></span>
> - <span data-ttu-id="61647-183">`A`et `B` sont des événements, et le nom et le `A` type `B` de et sont identiques.</span><span class="sxs-lookup"><span data-stu-id="61647-183">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61647-184">`A`et `B` sont des indexeurs, les listes de paramètres `A` formels de `B` et `A` sont identiques, ont les mêmes `B` accesseurs que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre `A` d’interface explicite) et le type `B` de est convertible en type de retour `A` via une conversion d’identité ou, si est un indexeur ReadOnly, une conversion de référence implicite.</span><span class="sxs-lookup"><span data-stu-id="61647-184">`A` and `B` are indexers, the formal parameter lists of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly indexer, an implicit reference conversion.</span></span>

<span data-ttu-id="61647-185">Il s’agit techniquement d’une modification avec rupture, car le programme ci-dessous affiche « C1 ». M « aujourd’hui, mais cela imprimerait «C2 ». M» sous la révision proposée.</span><span class="sxs-lookup"><span data-stu-id="61647-185">This is technically a breaking change, as the program below prints "C1.M" today, but would print "C2.M" under the proposed revision.</span></span>

``` c#
using System;

interface I1 { object M(); }
class C1 : I1 { public object M() { return "C1.M"; } }
class C2 : C1, I1 { public new string M() { return "C2.M"; } }
class Program
{
    static void Main()
    {
        I1 i = new C2();
        Console.WriteLine(i.M());
    }
}
```

<span data-ttu-id="61647-186">En raison de cette modification avec rupture, nous pouvons envisager de ne pas prendre en charge les types de retour covariants sur les implémentations implicites.</span><span class="sxs-lookup"><span data-stu-id="61647-186">Due to this breaking change, we might consider not supporting covariant return types on implicit implementations.</span></span>

### <a name="constraints-on-interface-implementation"></a><span data-ttu-id="61647-187">Contraintes sur l’implémentation de l’interface</span><span class="sxs-lookup"><span data-stu-id="61647-187">Constraints on Interface Implementation</span></span>

<span data-ttu-id="61647-188">**Nous aurons besoin d’une règle qui indique qu’une implémentation d’interface explicite doit déclarer un type de retour non moins dérivé que le type de retour déclaré dans une substitution de ses interfaces de base.**</span><span class="sxs-lookup"><span data-stu-id="61647-188">**We will need a rule that an explicit interface implementation must declare a return type no less derived than the return type declared in any override in its base interfaces.**</span></span>

### <a name="api-compatibility-implications"></a><span data-ttu-id="61647-189">Implications en matière de compatibilité des API</span><span class="sxs-lookup"><span data-stu-id="61647-189">API Compatibility Implications</span></span>

<span data-ttu-id="61647-190">*TBD*</span><span class="sxs-lookup"><span data-stu-id="61647-190">*TBD*</span></span>

### <a name="open-issues"></a><span data-ttu-id="61647-191">Open Issues</span><span class="sxs-lookup"><span data-stu-id="61647-191">Open Issues</span></span>

<span data-ttu-id="61647-192">La spécification n’indique pas comment l’appelant obtient le type de retour le plus affiné.</span><span class="sxs-lookup"><span data-stu-id="61647-192">The specification does not say how the caller gets the more refined return type.</span></span> <span data-ttu-id="61647-193">Vraisemblablement, cela se fait de manière similaire à la façon dont les appelants obtiennent les spécifications de paramètre de substitution les plus dérivées.</span><span class="sxs-lookup"><span data-stu-id="61647-193">Presumably that would be done in a way similar to the way that callers get the most derived override's parameter specifications.</span></span>

--------------

<span data-ttu-id="61647-194">Si nous disposons des interfaces suivantes :</span><span class="sxs-lookup"><span data-stu-id="61647-194">If we have the following interfaces:</span></span>

```csharp
interface I1 { I1 M(); }
interface I2 { I2 M(); }
interface I3: I1, I2 { override I3 M(); }
```

<span data-ttu-id="61647-195">Notez que dans `I3`, les méthodes `I1.M()` et `I2.M()` ont été fusionnées.</span><span class="sxs-lookup"><span data-stu-id="61647-195">Note that in `I3`, the methods `I1.M()` and `I2.M()` have been “merged”.</span></span>  <span data-ttu-id="61647-196">Lors de `I3`l’implémentation de, il est nécessaire de les implémenter ensemble.</span><span class="sxs-lookup"><span data-stu-id="61647-196">When implementing `I3`, it is necessary to implement them both together.</span></span>

<span data-ttu-id="61647-197">En général, nous avons besoin d’une implémentation explicite pour faire référence à la méthode d’origine.</span><span class="sxs-lookup"><span data-stu-id="61647-197">Generally, we require an explicit implementation to refer to the original method.</span></span>  <span data-ttu-id="61647-198">La question est, dans une classe</span><span class="sxs-lookup"><span data-stu-id="61647-198">The question is, in a class</span></span>

```csharp
class C : I1, I2, I3
{
    C IN.M();
}
```

<span data-ttu-id="61647-199">Qu’est-ce que cela signifie ?</span><span class="sxs-lookup"><span data-stu-id="61647-199">What does that mean here?</span></span>  <span data-ttu-id="61647-200">Que dois *-* je faire ?</span><span class="sxs-lookup"><span data-stu-id="61647-200">What should *N* be?</span></span>

<span data-ttu-id="61647-201">Je vous suggère de permettre l' `I1.M` implémentation de `I2.M` ou (mais pas les deux), et de le considérer comme une implémentation des deux.</span><span class="sxs-lookup"><span data-stu-id="61647-201">I suggest that we permit implementing either `I1.M` or `I2.M` (but not both), and treat that as an implementation of both.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="61647-202">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="61647-202">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="61647-203">[] Chaque modification de langue doit payer pour elle-même.</span><span class="sxs-lookup"><span data-stu-id="61647-203">[ ] Every language change must pay for itself.</span></span>
- <span data-ttu-id="61647-204">[] Nous devons nous assurer que les performances sont raisonnables, même dans le cas des hiérarchies d’héritage profond</span><span class="sxs-lookup"><span data-stu-id="61647-204">[ ] We should ensure that the performance is reasonable, even in the case of deep inheritance hierarchies</span></span>
- <span data-ttu-id="61647-205">[] Nous devons nous assurer que les artefacts de la stratégie de traduction n’affectent pas la sémantique de la langue, même lors de la consommation du nouveau langage intermédiaire à partir des compilateurs anciens.</span><span class="sxs-lookup"><span data-stu-id="61647-205">[ ] We should ensure that artifacts of the translation strategy do not affect language semantics, even when consuming new IL from old compilers.</span></span>

## <a name="alternatives"></a><span data-ttu-id="61647-206">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="61647-206">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="61647-207">Nous pourrions assouplir légèrement les règles de langage pour autoriser, dans la source,</span><span class="sxs-lookup"><span data-stu-id="61647-207">We could relax the language rules slightly to allow, in source,</span></span>

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a><span data-ttu-id="61647-208">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="61647-208">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="61647-209">[] Comment les API qui ont été compilées pour utiliser cette fonctionnalité fonctionnent-elles dans des versions antérieures du langage ?</span><span class="sxs-lookup"><span data-stu-id="61647-209">[ ] How will APIs that have been compiled to use this feature work in older versions of the language?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="61647-210">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="61647-210">Design meetings</span></span>

- <span data-ttu-id="61647-211">une discussion sur <https://github.com/dotnet/roslyn/issues/357>.</span><span class="sxs-lookup"><span data-stu-id="61647-211">some discussion at <https://github.com/dotnet/roslyn/issues/357>.</span></span>
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-01-08.md
- <span data-ttu-id="61647-212">Discussion hors connexion vers une décision pour prendre en charge la substitution des méthodes de classe uniquement en C# 9,0.</span><span class="sxs-lookup"><span data-stu-id="61647-212">Offline discussion toward a decision to support overriding of class methods only in C# 9.0.</span></span>

