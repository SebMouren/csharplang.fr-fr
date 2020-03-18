---
ms.openlocfilehash: 3d10cacef98e802333c8cbe65edb93c19c74cabf
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484495"
---
# <a name="null-conditional-await"></a><span data-ttu-id="5036c-101">NULL-attente conditionnelle</span><span class="sxs-lookup"><span data-stu-id="5036c-101">null-conditional await</span></span>

* <span data-ttu-id="5036c-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="5036c-102">[x] Proposed</span></span>
* <span data-ttu-id="5036c-103">[] Prototype : aucun</span><span class="sxs-lookup"><span data-stu-id="5036c-103">[ ] Prototype: None</span></span>
* <span data-ttu-id="5036c-104">[] Implémentation : aucune</span><span class="sxs-lookup"><span data-stu-id="5036c-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="5036c-105">[] Spécification : démarrée, en dessous</span><span class="sxs-lookup"><span data-stu-id="5036c-105">[ ] Specification: Started, below</span></span>

## <a name="summary"></a><span data-ttu-id="5036c-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="5036c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="5036c-107">Prend en charge une expression de la forme `await? e`, qui attend `e` si la valeur n’est pas null, sinon, elle génère `null`.</span><span class="sxs-lookup"><span data-stu-id="5036c-107">Support an expression of the form `await? e`, which awaits `e` if it is non-null, otherwise it results in `null`.</span></span>

## <a name="motivation"></a><span data-ttu-id="5036c-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="5036c-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="5036c-109">Il s’agit d’un modèle de codage courant, qui aurait une bonne synergie avec les opérateurs de propagation de valeurs NULL et de fusion de null existants.</span><span class="sxs-lookup"><span data-stu-id="5036c-109">This is a common coding pattern, and this feature would have nice synergy with the existing null-propagating and null-coalescing operators.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="5036c-110">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="5036c-110">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="5036c-111">Nous ajoutons une nouvelle forme de la *await_expression*:</span><span class="sxs-lookup"><span data-stu-id="5036c-111">We add a new form of the *await_expression*:</span></span>

```antlr
await_expression
    : 'await' '?' unary_expression
    ;
```

<span data-ttu-id="5036c-112">L’opérateur de `await` conditionnel null attend son opérande uniquement si cet opérande n’est pas null.</span><span class="sxs-lookup"><span data-stu-id="5036c-112">The null-conditional `await` operator awaits its operand only if that operand is non-null.</span></span> <span data-ttu-id="5036c-113">Sinon, le résultat de l’application de l’opérateur est null.</span><span class="sxs-lookup"><span data-stu-id="5036c-113">Otherwise the result of applying the operator is null.</span></span>

<span data-ttu-id="5036c-114">Le type du résultat est calculé à l’aide des [règles pour l’opérateur conditionnel null](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).</span><span class="sxs-lookup"><span data-stu-id="5036c-114">The type of the result is computed using the [rules for the null-conditional operator](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).</span></span>

> <span data-ttu-id="5036c-115">**Remarque :** Si `e` est de type `Task`, `await? e;` ne fait rien si `e` est `null`, et await `e` s’il n’est pas `null`.</span><span class="sxs-lookup"><span data-stu-id="5036c-115">**NOTE:** If `e` is of type `Task`, then `await? e;` would do nothing if `e` is `null`, and await `e` if it is not `null`.</span></span>
>
> <span data-ttu-id="5036c-116">Si `e` est de type `Task<K>` où `K` est un type valeur, `await? e` générerait une valeur de type `K?`.</span><span class="sxs-lookup"><span data-stu-id="5036c-116">If `e` is of type `Task<K>` where `K` is a value type, then `await? e` would yield a value of type `K?`.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="5036c-117">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="5036c-117">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="5036c-118">Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le C# corps des programmes qui tireraient parti de la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="5036c-118">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="5036c-119">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="5036c-119">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="5036c-120">Bien qu’il nécessite un code réutilisable, les utilisations de cet opérateur peuvent souvent être remplacées par une expression comme `(e == null) ? null : await e` ou une instruction comme `if (e != null) await e`.</span><span class="sxs-lookup"><span data-stu-id="5036c-120">Although it requires some boilerplate code, uses of this operator can often be replaced by an expression something like `(e == null) ? null : await e` or a statement like `if (e != null) await e`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="5036c-121">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="5036c-121">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="5036c-122">[] Requiert la revue LDM</span><span class="sxs-lookup"><span data-stu-id="5036c-122">[ ] Requires LDM review</span></span>

## <a name="design-meetings"></a><span data-ttu-id="5036c-123">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="5036c-123">Design meetings</span></span>

<span data-ttu-id="5036c-124">Aucun.</span><span class="sxs-lookup"><span data-stu-id="5036c-124">None.</span></span>
