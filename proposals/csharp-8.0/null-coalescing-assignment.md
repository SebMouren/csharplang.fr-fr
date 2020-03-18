---
ms.openlocfilehash: fdd858c895d56a7a6b410e6ea7be3032e4851fd6
ms.sourcegitcommit: 5a88d5432d32c690c6b870fc4be32cf26cadd76f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "79484957"
---
# <a name="null-coalescing-assignment"></a><span data-ttu-id="f543b-101">Attribution de fusion Null</span><span class="sxs-lookup"><span data-stu-id="f543b-101">null coalescing assignment</span></span>

* <span data-ttu-id="f543b-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="f543b-102">[x] Proposed</span></span>
* <span data-ttu-id="f543b-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="f543b-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="f543b-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="f543b-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="f543b-105">[] Spécification : ci-dessous</span><span class="sxs-lookup"><span data-stu-id="f543b-105">[ ] Specification: Below</span></span>

## <a name="summary"></a><span data-ttu-id="f543b-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="f543b-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="f543b-107">Simplifie un modèle de codage commun dans lequel une valeur est assignée à une variable si elle est null.</span><span class="sxs-lookup"><span data-stu-id="f543b-107">Simplifies a common coding pattern where a variable is assigned a value if it is null.</span></span>

<span data-ttu-id="f543b-108">Dans le cadre de cette proposition, nous relâcherons également les exigences de type sur `??` pour permettre l’utilisation d’une expression dont le type est un paramètre de type sans contrainte à utiliser sur le côté gauche.</span><span class="sxs-lookup"><span data-stu-id="f543b-108">As part of this proposal, we will also loosen the type requirements on `??` to allow an expression whose type is an unconstrained type parameter to be used on the left-hand side.</span></span>

## <a name="motivation"></a><span data-ttu-id="f543b-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="f543b-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="f543b-110">Il est courant de voir le code du formulaire</span><span class="sxs-lookup"><span data-stu-id="f543b-110">It is common to see code of the form</span></span>

```csharp
if (variable == null)
{
    variable = expression;
}
```

<span data-ttu-id="f543b-111">Cette proposition ajoute un opérateur binaire non surchargeable dans le langage qui exécute cette fonction.</span><span class="sxs-lookup"><span data-stu-id="f543b-111">This proposal adds a non-overloadable binary operator to the language that performs this function.</span></span>

<span data-ttu-id="f543b-112">Il y a eu au moins huit demandes de communauté distinctes pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f543b-112">There have been at least eight separate community requests for this feature.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f543b-113">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="f543b-113">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="f543b-114">Nous ajoutons une nouvelle forme d’opérateur d’assignation</span><span class="sxs-lookup"><span data-stu-id="f543b-114">We add a new form of assignment operator</span></span>

``` antlr
assignment_operator
    : '??='
    ;
```

<span data-ttu-id="f543b-115">Qui suit les [règles sémantiques existantes pour les opérateurs d’assignation composée](../../spec/expressions.md#compound-assignment), à l’exception du fait que nous Elide l’assignation si le côté gauche n’est pas null.</span><span class="sxs-lookup"><span data-stu-id="f543b-115">Which follows the [existing semantic rules for compound assignment operators](../../spec/expressions.md#compound-assignment), except that we elide the assignment if the left-hand side is non-null.</span></span> <span data-ttu-id="f543b-116">Les règles de cette fonctionnalité sont les suivantes.</span><span class="sxs-lookup"><span data-stu-id="f543b-116">The rules for this feature are as follows.</span></span>

<span data-ttu-id="f543b-117">Étant donné `a ??= b`, où `A` est le type de `a`, `B` est le type de `b`, et `A0` est le type sous-jacent de `A` si `A` est un type valeur Nullable :</span><span class="sxs-lookup"><span data-stu-id="f543b-117">Given `a ??= b`, where `A` is the type of `a`, `B` is the type of `b`, and `A0` is the underlying type of `A` if `A` is a nullable value type:</span></span>

1. <span data-ttu-id="f543b-118">Si `A` n’existe pas ou est un type valeur qui n’autorise pas les valeurs NULL, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f543b-118">If `A` does not exist or is a non-nullable value type, a compile-time error occurs.</span></span>
2. <span data-ttu-id="f543b-119">Si `B` n’est pas implicitement convertible en `A` ou `A0` (si `A0` existe), une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f543b-119">If `B` is not implicitly convertible to `A` or `A0` (if `A0` exists), a compile-time error occurs.</span></span>
3. <span data-ttu-id="f543b-120">Si `A0` existe et que `B` est implicitement convertible en `A0`et `B` n’est pas dynamique, le type de `a ??= b` est `A0`.</span><span class="sxs-lookup"><span data-stu-id="f543b-120">If `A0` exists and `B` is implicitly convertible to `A0`, and `B` is not dynamic, then the type of `a ??= b` is `A0`.</span></span> <span data-ttu-id="f543b-121">`a ??= b` est évaluée au moment de l’exécution comme suit :</span><span class="sxs-lookup"><span data-stu-id="f543b-121">`a ??= b` is evaluated at runtime as:</span></span>
   ```C#
   var tmp = a.GetValueOrDefault();
   if (!a.HasValue) { tmp = b; a = tmp; }
   tmp
   ```
   <span data-ttu-id="f543b-122">Hormis le fait que `a` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="f543b-122">Except that `a` is only evaluated once.</span></span>
4. <span data-ttu-id="f543b-123">Dans le cas contraire, le type de `a ??= b` est `A`.</span><span class="sxs-lookup"><span data-stu-id="f543b-123">Otherwise, the type of `a ??= b` is `A`.</span></span> <span data-ttu-id="f543b-124">`a ??= b` est évaluée au moment de l’exécution en tant que `a ?? (a = b)`, sauf que `a` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="f543b-124">`a ??= b` is evaluated at runtime as `a ?? (a = b)`, except that `a` is only evaluated once.</span></span>


<span data-ttu-id="f543b-125">Pour l’assouplissement des exigences de type de `??`, nous mettons à jour la spécification là où elle déclare actuellement, étant donné `a ?? b`, où `A` est le type de `a`:</span><span class="sxs-lookup"><span data-stu-id="f543b-125">For the relaxation of the type requirements of `??`, we update the spec where it currently states that, given `a ?? b`, where `A` is the type of `a`:</span></span>

> 1. <span data-ttu-id="f543b-126">Si un existe et n’est pas un type Nullable ou un type référence, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f543b-126">If A exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>

<span data-ttu-id="f543b-127">Nous détendons cette exigence pour :</span><span class="sxs-lookup"><span data-stu-id="f543b-127">We relax this requirement to:</span></span>

1. <span data-ttu-id="f543b-128">Si un existe et qu’il s’agit d’un type valeur n’acceptant pas les valeurs NULL, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f543b-128">If A exists and is a non-nullable value type, a compile-time error occurs.</span></span>

<span data-ttu-id="f543b-129">Cela permet à l’opérateur de fusion Null de fonctionner sur des paramètres de type sans contrainte, étant donné que le paramètre de type sans contrainte T existe, qu’il n’est pas un type Nullable et qu’il n’est pas un type référence.</span><span class="sxs-lookup"><span data-stu-id="f543b-129">This allows the null coalescing operator to work on unconstrained type parameters, as the unconstrained type parameter T exists, is not a nullable type, and is not a reference type.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="f543b-130">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="f543b-130">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="f543b-131">Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le C# corps des programmes qui tireraient parti de la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f543b-131">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="f543b-132">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="f543b-132">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="f543b-133">Le programmeur peut écrire à la main `(x = x ?? y)`, `if (x == null) x = y;`ou `x ?? (x = y)`.</span><span class="sxs-lookup"><span data-stu-id="f543b-133">The programmer can write `(x = x ?? y)`, `if (x == null) x = y;`, or `x ?? (x = y)` by hand.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="f543b-134">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="f543b-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="f543b-135">[] Requiert la revue LDM</span><span class="sxs-lookup"><span data-stu-id="f543b-135">[ ] Requires LDM review</span></span>
- <span data-ttu-id="f543b-136">[] Devons-nous également prendre en charge les opérateurs `&&=` et `||=` ?</span><span class="sxs-lookup"><span data-stu-id="f543b-136">[ ] Should we also support `&&=` and `||=` operators?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="f543b-137">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="f543b-137">Design meetings</span></span>

<span data-ttu-id="f543b-138">Aucun.</span><span class="sxs-lookup"><span data-stu-id="f543b-138">None.</span></span>
