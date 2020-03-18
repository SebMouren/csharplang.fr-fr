---
ms.openlocfilehash: 032cb8590a0b6e83f8ab6201e10720f1b254c605
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484530"
---
# <a name="nullable-enhanced-common-type"></a><span data-ttu-id="b8786-101">Type commun amélioré avec la valeur null</span><span class="sxs-lookup"><span data-stu-id="b8786-101">Nullable-Enhanced Common Type</span></span>

* <span data-ttu-id="b8786-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="b8786-102">[x] Proposed</span></span>
* <span data-ttu-id="b8786-103">[] Prototype : aucun</span><span class="sxs-lookup"><span data-stu-id="b8786-103">[ ] Prototype: None</span></span>
* <span data-ttu-id="b8786-104">[] Implémentation : aucune</span><span class="sxs-lookup"><span data-stu-id="b8786-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="b8786-105">[] Spécification : voir ci-dessous</span><span class="sxs-lookup"><span data-stu-id="b8786-105">[ ] Specification: See below</span></span>

## <a name="summary"></a><span data-ttu-id="b8786-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="b8786-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="b8786-107">Il existe une situation dans laquelle les résultats actuels de l’algorithme de type commun sont de type Counter-intuitif et les résultats du programmeur ajoutent ce qui ressemble à une conversion redondante dans le code.</span><span class="sxs-lookup"><span data-stu-id="b8786-107">There is a situation in which the current common-type algorithm results are counter-intuitive, and results in the programmer adding what feels like a redundant cast to the code.</span></span> <span data-ttu-id="b8786-108">Avec cette modification, une expression telle que `condition ? 1 : null` entraînerait une valeur de type `int?`, et une expression telle que `condition ? x : 1.0` où `x` est de type `int?` entraînerait une valeur de type `double?`.</span><span class="sxs-lookup"><span data-stu-id="b8786-108">With this change, an expression such as `condition ? 1 : null` would result in a value of type `int?`, and an expression such as `condition ? x : 1.0` where `x` is of type `int?` would result in a value of type `double?`.</span></span>

## <a name="motivation"></a><span data-ttu-id="b8786-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="b8786-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="b8786-110">Il s’agit d’une cause courante de ce qui semble être le programmeur comme un code réutilisable.</span><span class="sxs-lookup"><span data-stu-id="b8786-110">This is a common cause of what feels to the programmer like needless boilerplate code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="b8786-111">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="b8786-111">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="b8786-112">Nous modifions la spécification permettant de [trouver le meilleur type commun d’un ensemble d’expressions](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) pour affecter les situations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8786-112">We modify the specification for [finding the best common type of a set of expressions](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) to affect the following situations:</span></span>

- <span data-ttu-id="b8786-113">Si une expression est d’un type valeur n’acceptant pas les valeurs NULL `T` et que l’autre est un littéral null, le résultat est de type `T?`.</span><span class="sxs-lookup"><span data-stu-id="b8786-113">If one expression is of a non-nullable value type `T` and the other is a null literal, the result is of type `T?`.</span></span>
- <span data-ttu-id="b8786-114">Si une expression est d’un type valeur Nullable `T?` et que l’autre est d’un type valeur `U`, et qu’il existe une conversion implicite de `T` en `U`, le résultat est de type `U?`.</span><span class="sxs-lookup"><span data-stu-id="b8786-114">If one expression is of a nullable value type `T?` and the other is of a value type `U`, and there is an implicit conversion from `T` to `U`, then the result is of type `U?`.</span></span>

<span data-ttu-id="b8786-115">Cela devrait affecter les aspects suivants du langage :</span><span class="sxs-lookup"><span data-stu-id="b8786-115">This is expected to affect the following aspects of the language:</span></span>

- <span data-ttu-id="b8786-116">l' [expression ternaire](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span><span class="sxs-lookup"><span data-stu-id="b8786-116">the [ternary expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span></span>
- <span data-ttu-id="b8786-117">[expression de création de tableau](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions) implicitement typé</span><span class="sxs-lookup"><span data-stu-id="b8786-117">implicitly typed [array creation expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions)</span></span>
- <span data-ttu-id="b8786-118">déduction du [type de retour d’une expression lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) pour l’inférence de type</span><span class="sxs-lookup"><span data-stu-id="b8786-118">inferring the [return type of a lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) for type inference</span></span>
- <span data-ttu-id="b8786-119">les cas impliquant des génériques, tels que l’appel de `M<T>(T a, T b)` en tant que `M(1, null)`.</span><span class="sxs-lookup"><span data-stu-id="b8786-119">cases involving generics, such as invoking `M<T>(T a, T b)` as `M(1, null)`.</span></span>

<span data-ttu-id="b8786-120">Plus précisément, nous modifions les sections suivantes de la spécification (insertions en gras, suppressions dans le texte barré) :</span><span class="sxs-lookup"><span data-stu-id="b8786-120">More precisely, we change the following sections of the specification (insertions in bold, deletions in strikethrough):</span></span>

> #### <a name="output-type-inferences"></a><span data-ttu-id="b8786-121">Inférences de type de sortie</span><span class="sxs-lookup"><span data-stu-id="b8786-121">Output type inferences</span></span>
> 
> <span data-ttu-id="b8786-122">Une *inférence de type de sortie* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="b8786-122">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>
> 
> *  <span data-ttu-id="b8786-123">Si `E` est une fonction anonyme avec un type de retour déduit `U`[(type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou un type d’arborescence d’expression avec le type de retour `Tb`, une *inférence à la limite inférieure* ([inférences à liaison inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="b8786-123">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="b8786-124">Sinon, si `E` est un groupe de méthodes et que `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `T1...Tk` et `Tb`de type de retour, et que la résolution de surcharge de `E` avec les types `T1...Tk` produit une méthode unique avec le type de retour `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="b8786-124">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="b8786-125">\* \* Sinon, si `E` est une expression avec un type de valeur Nullable `U?`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T` et une *limite null* est ajoutée à `T`.</span><span class="sxs-lookup"><span data-stu-id="b8786-125">\*\*Otherwise, if `E` is an expression with nullable value type `U?`, then a *lower-bound inference* is made *from* `U` *to* `T` and a *null bound* is added to `T`.</span></span> **
> *  <span data-ttu-id="b8786-126">Sinon, si `E` est une expression de type `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T`.</span><span class="sxs-lookup"><span data-stu-id="b8786-126">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
> *  <span data-ttu-id="b8786-127">**Sinon, si `E` est une expression constante avec la valeur `null`, une *limite null* est ajoutée à `T`**</span><span class="sxs-lookup"><span data-stu-id="b8786-127">**Otherwise, if `E` is a constant expression with value `null`, then a *null bound* is added to `T`**</span></span> 
> *  <span data-ttu-id="b8786-128">Dans le cas contraire, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b8786-128">Otherwise, no inferences are made.</span></span>

> #### <a name="fixing"></a><span data-ttu-id="b8786-129">Résolution</span><span class="sxs-lookup"><span data-stu-id="b8786-129">Fixing</span></span>
> 
> <span data-ttu-id="b8786-130">Une variable de type non *fixe* `Xi` avec un ensemble de limites est *fixe* comme suit :</span><span class="sxs-lookup"><span data-stu-id="b8786-130">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>
> 
> *  <span data-ttu-id="b8786-131">L’ensemble de *types candidats* `Uj` commence par le jeu de tous les types dans le jeu de limites de `Xi`.</span><span class="sxs-lookup"><span data-stu-id="b8786-131">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
> *  <span data-ttu-id="b8786-132">Nous examinons ensuite chaque limite pour `Xi` à son tour : pour chaque `U` à liaison exacte de `Xi` tous les types `Uj` qui ne sont pas identiques à `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b8786-132">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="b8786-133">Pour chaque `U` de limite inférieure de `Xi` tous les types `Uj` pour lesquels il n’existe *pas* de conversion implicite de `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b8786-133">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="b8786-134">Pour chaque `U` de la limite supérieure de `Xi` tous les types `Uj` à partir duquel il n’existe *pas* de conversion implicite en `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b8786-134">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
> *  <span data-ttu-id="b8786-135">Si parmi les types candidats restants `Uj` il existe un type unique `V` à partir duquel il existe une conversion implicite vers tous les autres types candidats, ~~`Xi` est fixé à `V`.~~</span><span class="sxs-lookup"><span data-stu-id="b8786-135">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then ~~`Xi` is fixed to `V`.~~</span></span>
>     -  <span data-ttu-id="b8786-136">**Si `V` est un type valeur et qu’il existe une *limite null* pour `Xi`, `Xi` est fixe à `V?`**</span><span class="sxs-lookup"><span data-stu-id="b8786-136">**If `V` is a value type and there is a *null bound* for `Xi`, then `Xi` is fixed to `V?`**</span></span>
>     -  <span data-ttu-id="b8786-137">**Sinon `Xi` est fixe à `V`**</span><span class="sxs-lookup"><span data-stu-id="b8786-137">**Otherwise   `Xi` is fixed to `V`**</span></span>
> *  <span data-ttu-id="b8786-138">Sinon, l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="b8786-138">Otherwise, type inference fails.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="b8786-139">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="b8786-139">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="b8786-140">Il peut y avoir des incompatibilités introduites par cette proposition.</span><span class="sxs-lookup"><span data-stu-id="b8786-140">There may be some incompatibilities introduced by this proposal.</span></span>

## <a name="alternatives"></a><span data-ttu-id="b8786-141">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="b8786-141">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="b8786-142">Aucun.</span><span class="sxs-lookup"><span data-stu-id="b8786-142">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="b8786-143">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="b8786-143">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="b8786-144">[] Quel est le niveau de gravité de l’incompatibilité introduit par cette proposition, le cas échéant, et comment peut-il être modéré ?</span><span class="sxs-lookup"><span data-stu-id="b8786-144">[ ] What is the severity of incompatibility introduced by this proposal, if any, and how can it be moderated?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="b8786-145">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="b8786-145">Design meetings</span></span>

<span data-ttu-id="b8786-146">Aucun.</span><span class="sxs-lookup"><span data-stu-id="b8786-146">None.</span></span>
