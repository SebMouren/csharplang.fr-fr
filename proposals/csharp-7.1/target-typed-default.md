---
ms.openlocfilehash: 1852356b830e29c3537a4de559fef32fd2c2f8b6
ms.sourcegitcommit: f7952cdddf85316a4beec493a0ecc14fcb3241c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "79484985"
---
# <a name="target-typed-default-literal"></a><span data-ttu-id="4e1ba-101">Littéral « par défaut » de type cible</span><span class="sxs-lookup"><span data-stu-id="4e1ba-101">Target-typed "default" literal</span></span>

* <span data-ttu-id="4e1ba-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="4e1ba-102">[x] Proposed</span></span>
* <span data-ttu-id="4e1ba-103">[x] prototype</span><span class="sxs-lookup"><span data-stu-id="4e1ba-103">[x] Prototype</span></span>
* <span data-ttu-id="4e1ba-104">[x] implémentation</span><span class="sxs-lookup"><span data-stu-id="4e1ba-104">[x] Implementation</span></span>
* <span data-ttu-id="4e1ba-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="4e1ba-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="4e1ba-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="4e1ba-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="4e1ba-107">La fonctionnalité de `default` de type cible est une variante plus concise de l’opérateur `default(T)`, qui permet d’omettre le type.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-107">The target-typed `default` feature is a shorter form variation of the `default(T)` operator, which allows the type to be omitted.</span></span> <span data-ttu-id="4e1ba-108">Son type est déduit par la cible-typage à la place.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-108">Its type is inferred by target-typing instead.</span></span> <span data-ttu-id="4e1ba-109">À part cela, il se comporte comme `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-109">Aside from that, it behaves like `default(T)`.</span></span>

## <a name="motivation"></a><span data-ttu-id="4e1ba-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="4e1ba-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="4e1ba-111">La motivation principale est d’éviter de taper des informations redondantes.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-111">The main motivation is to avoid typing redundant information.</span></span>

<span data-ttu-id="4e1ba-112">Par exemple, lors de l’appel de `void Method(ImmutableArray<SomeType> array)`, le littéral *par défaut* autorise `M(default)` à la place de `M(default(ImmutableArray<SomeType>))`.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-112">For instance, when invoking `void Method(ImmutableArray<SomeType> array)`, the *default* literal allows `M(default)` in place of `M(default(ImmutableArray<SomeType>))`.</span></span>

<span data-ttu-id="4e1ba-113">Cela s’applique dans un certain nombre de scénarios, tels que :</span><span class="sxs-lookup"><span data-stu-id="4e1ba-113">This is applicable in a number of scenarios, such as:</span></span>

- <span data-ttu-id="4e1ba-114">Déclaration de variables locales (`ImmutableArray<SomeType> x = default;`)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-114">declaring locals (`ImmutableArray<SomeType> x = default;`)</span></span>
- <span data-ttu-id="4e1ba-115">opérations ternaires (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-115">ternary operations (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)</span></span>
- <span data-ttu-id="4e1ba-116">retour dans les méthodes et les expressions lambda (`return default;`)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-116">returning in methods and lambdas (`return default;`)</span></span>
- <span data-ttu-id="4e1ba-117">Déclaration des valeurs par défaut pour les paramètres facultatifs (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-117">declaring default values for optional parameters (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)</span></span>
- <span data-ttu-id="4e1ba-118">inclusion de valeurs par défaut dans les expressions de création de tableau (`var x = new[] { default, ImmutableArray.Create(y) };`)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-118">including default values in array creation expressions (`var x = new[] { default, ImmutableArray.Create(y) };`)</span></span>


## <a name="detailed-design"></a><span data-ttu-id="4e1ba-119">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="4e1ba-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="4e1ba-120">Une nouvelle expression est introduite, le littéral *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="4e1ba-120">A new expression is introduced, the *default* literal.</span></span> <span data-ttu-id="4e1ba-121">Une expression avec cette classification peut être convertie implicitement en n’importe quel type, par une *conversion de littéral par défaut*.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-121">An expression with this classification can be implicitly converted to any type, by a *default literal conversion*.</span></span> 

<span data-ttu-id="4e1ba-122">L’inférence du type pour le littéral *par défaut* fonctionne de la même façon que pour le littéral *null* , sauf qu’un type est autorisé (pas seulement des types référence).</span><span class="sxs-lookup"><span data-stu-id="4e1ba-122">The inference of the type for the *default* literal works the same as that for the *null* literal, except that any type is allowed (not just reference types).</span></span>

<span data-ttu-id="4e1ba-123">Cette conversion produit la valeur par défaut du type inféré.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-123">This conversion produces the default value of the inferred type.</span></span>

<span data-ttu-id="4e1ba-124">Le littéral *par défaut* peut avoir une valeur de constante, en fonction du type inféré.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-124">The *default* literal may have a constant value, depending on the inferred type.</span></span> <span data-ttu-id="4e1ba-125">`const int x = default;` est donc légal, mais `const int? y = default;` ne l’est pas.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-125">So `const int x = default;` is legal, but `const int? y = default;` is not.</span></span>

<span data-ttu-id="4e1ba-126">Le littéral *par défaut* peut être l’opérande des opérateurs d’égalité, tant que l’autre opérande a un type.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-126">The *default* literal can be the operand of equality operators, as long as the other operand has a type.</span></span> <span data-ttu-id="4e1ba-127">Ainsi `default == x` et `x == default` sont des expressions valides, mais `default == default` n’est pas conforme.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-127">So `default == x` and `x == default` are valid expressions, but `default == default` is illegal.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="4e1ba-128">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="4e1ba-128">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="4e1ba-129">Un inconvénient mineur est que le littéral *par défaut* peut être utilisé à la place du littéral *null* dans la plupart des contextes.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-129">A minor drawback is that *default* literal can be used in place of *null* literal in most contexts.</span></span> <span data-ttu-id="4e1ba-130">Deux des exceptions sont `throw null;` et `null == null`, qui sont autorisées pour le littéral *null* , mais pas le littéral *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="4e1ba-130">Two of the exceptions are `throw null;` and `null == null`, which are allowed for the *null* literal, but not the *default* literal.</span></span>

## <a name="alternatives"></a><span data-ttu-id="4e1ba-131">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="4e1ba-131">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="4e1ba-132">Il existe deux alternatives à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="4e1ba-132">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="4e1ba-133">État actuel : la fonctionnalité n’est pas justifiée par ses propres avantages et les développeurs continuent à utiliser l’opérateur par défaut avec un type explicite.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-133">The status quo:  The feature is not justified on its own merits and developers continue to use the default operator with an explicit type.</span></span>
- <span data-ttu-id="4e1ba-134">Extension du littéral null : il s’agit de l’approche VB avec `Nothing`.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-134">Extending the null literal: This is the VB approach with `Nothing`.</span></span> <span data-ttu-id="4e1ba-135">Nous pourrions autoriser `int x = null;`.</span><span class="sxs-lookup"><span data-stu-id="4e1ba-135">We could allow `int x = null;`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="4e1ba-136">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="4e1ba-136">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="4e1ba-137">[x] la *valeur par défaut* doit être autorisée comme opérande des opérateurs *is* ou *As* ?</span><span class="sxs-lookup"><span data-stu-id="4e1ba-137">[x] Should *default* be allowed as the operand of the *is* or *as* operators?</span></span> <span data-ttu-id="4e1ba-138">Réponse : interdire `default is T`, autoriser `x is default`, autoriser les `default as RefType` (avec un avertissement Always null)</span><span class="sxs-lookup"><span data-stu-id="4e1ba-138">Answer:  disallow `default is T`, allow `x is default`, allow `default as RefType` (with always-null warning)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="4e1ba-139">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="4e1ba-139">Design meetings</span></span>

- [<span data-ttu-id="4e1ba-140">LDM 3/7/2017</span><span class="sxs-lookup"><span data-stu-id="4e1ba-140">LDM 3/7/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-07.md)
- [<span data-ttu-id="4e1ba-141">LDM 3/28/2017</span><span class="sxs-lookup"><span data-stu-id="4e1ba-141">LDM 3/28/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-28.md)
- [<span data-ttu-id="4e1ba-142">LDM 5/31/2017</span><span class="sxs-lookup"><span data-stu-id="4e1ba-142">LDM 5/31/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md#default-in-operators)
