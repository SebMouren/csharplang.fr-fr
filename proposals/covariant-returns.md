---
ms.openlocfilehash: 392d932459ff0a4cb0d6d32c1606f73f9b913c68
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484453"
---
# <a name="covariant-return-types"></a><span data-ttu-id="9676e-101">types de retour covariants</span><span class="sxs-lookup"><span data-stu-id="9676e-101">covariant return types</span></span>

* <span data-ttu-id="9676e-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="9676e-102">[x] Proposed</span></span>
* <span data-ttu-id="9676e-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="9676e-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="9676e-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="9676e-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="9676e-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="9676e-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="9676e-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="9676e-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="9676e-107">Prennent en charge les _types de retour covariants_.</span><span class="sxs-lookup"><span data-stu-id="9676e-107">Support _covariant return types_.</span></span> <span data-ttu-id="9676e-108">En particulier, autorisez une méthode de substitution à avoir un type de référence plus dérivé que la méthode qu’elle substitue.</span><span class="sxs-lookup"><span data-stu-id="9676e-108">Specifically, allow an overriding method to have a more derived reference type than the method it overrides.</span></span>

## <a name="motivation"></a><span data-ttu-id="9676e-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="9676e-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="9676e-110">Il s’agit d’un modèle courant dans le code selon lequel différents noms de méthode doivent être inventés pour contourner la contrainte de langage que les substitutions doivent retourner au même type que la méthode substituée.</span><span class="sxs-lookup"><span data-stu-id="9676e-110">It is a common pattern in code that different method names have to be invented to work around the language constraint that overrides must return the same type as the overridden method.</span></span> <span data-ttu-id="9676e-111">Voir ci-dessous un exemple de la base de code Roslyn.</span><span class="sxs-lookup"><span data-stu-id="9676e-111">See below for an example from the Roslyn code base.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="9676e-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="9676e-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="9676e-113">Prennent en charge les _types de retour covariants_.</span><span class="sxs-lookup"><span data-stu-id="9676e-113">Support _covariant return types_.</span></span> <span data-ttu-id="9676e-114">En particulier, autorisez une méthode de substitution à avoir un type de référence plus dérivé que la méthode qu’elle substitue.</span><span class="sxs-lookup"><span data-stu-id="9676e-114">Specifically, allow an overriding method to have a more derived reference type than the method it overrides.</span></span> <span data-ttu-id="9676e-115">Cela s’applique aux méthodes et aux propriétés, et est pris en charge dans les classes et les interfaces.</span><span class="sxs-lookup"><span data-stu-id="9676e-115">This would apply to methods and properties, and be supported in classes and interfaces.</span></span>

<span data-ttu-id="9676e-116">Cela peut être utile dans le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="9676e-116">This would be useful in the factory pattern.</span></span> <span data-ttu-id="9676e-117">Par exemple, dans la base de code Roslyn, nous aurions</span><span class="sxs-lookup"><span data-stu-id="9676e-117">For example, in the Roslyn code base we would have</span></span>

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

<span data-ttu-id="9676e-118">L’implémentation de cela serait que le compilateur émette la méthode de substitution comme une nouvelle méthode virtuelle qui masque la méthode de la classe de base, avec une _méthode de pont_ qui implémente la méthode de la classe de base avec un appel à la méthode de la classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="9676e-118">The implementation of this would be for the compiler to emit the overriding method as a "new" virtual method that hides the base class method, along with a _bridge method_ that implements the base class method with a call to the derived class method.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="9676e-119">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="9676e-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="9676e-120">[] Chaque modification de langue doit payer pour elle-même.</span><span class="sxs-lookup"><span data-stu-id="9676e-120">[ ] Every language change must pay for itself.</span></span>
- <span data-ttu-id="9676e-121">[] Nous devons nous assurer que les performances sont raisonnables, même dans le cas des hiérarchies d’héritage profond</span><span class="sxs-lookup"><span data-stu-id="9676e-121">[ ] We should ensure that the performance is reasonable, even in the case of deep inheritance hierarchies</span></span>
- <span data-ttu-id="9676e-122">[] Nous devons nous assurer que les artefacts de la stratégie de traduction n’affectent pas la sémantique de la langue, même lors de la consommation du nouveau langage intermédiaire à partir des compilateurs anciens.</span><span class="sxs-lookup"><span data-stu-id="9676e-122">[ ] We should ensure that artifacts of the translation strategy do not affect language semantics, even when consuming new IL from old compilers.</span></span>

## <a name="alternatives"></a><span data-ttu-id="9676e-123">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="9676e-123">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="9676e-124">Nous pourrions assouplir légèrement les règles de langage pour autoriser, dans la source,</span><span class="sxs-lookup"><span data-stu-id="9676e-124">We could relax the language rules slightly to allow, in source,</span></span>

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

## <a name="unresolved-questions"></a><span data-ttu-id="9676e-125">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="9676e-125">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="9676e-126">[] Comment les API qui ont été compilées pour utiliser cette fonctionnalité fonctionnent-elles dans des versions antérieures du langage ?</span><span class="sxs-lookup"><span data-stu-id="9676e-126">[ ] How will APIs that have been compiled to use this feature work in older versions of the language?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="9676e-127">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="9676e-127">Design meetings</span></span>

<span data-ttu-id="9676e-128">Aucun.</span><span class="sxs-lookup"><span data-stu-id="9676e-128">None yet.</span></span> <span data-ttu-id="9676e-129">Il y a eu une discussion sur <https://github.com/dotnet/roslyn/issues/357>.</span><span class="sxs-lookup"><span data-stu-id="9676e-129">There has been some discussion at <https://github.com/dotnet/roslyn/issues/357>.</span></span>