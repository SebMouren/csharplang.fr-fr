---
ms.openlocfilehash: cd73a3d7289205f65f5144a98d32da06dfed3efc
ms.sourcegitcommit: e355841daad8c4672fae6a49c98653952d89a9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775423"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="5046a-101">Expression conditionnelle typée cible</span><span class="sxs-lookup"><span data-stu-id="5046a-101">Target-Typed Conditional Expression</span></span>

<span data-ttu-id="5046a-102">Pour une expression conditionnelle `c ? e1 : e2` , lorsque</span><span class="sxs-lookup"><span data-stu-id="5046a-102">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="5046a-103">Il n’existe aucun type commun pour `e1` et `e2` , ou</span><span class="sxs-lookup"><span data-stu-id="5046a-103">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="5046a-104">pour lequel un type commun existe, mais l’une des expressions `e1` ou n' `e2` a pas de conversion implicite vers ce type</span><span class="sxs-lookup"><span data-stu-id="5046a-104">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="5046a-105">Nous définissons une nouvelle *conversion d’expression conditionnelle* qui autorise une conversion implicite de l’expression conditionnelle en n’importe quel type `T` pour lequel il existe une conversion de-from-expression de `e1` en `T` et également de à `e2` `T` .</span><span class="sxs-lookup"><span data-stu-id="5046a-105">we define a new *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="5046a-106">Il s’agit d’une erreur si une expression conditionnelle n’a pas de type commun entre `e1` et ou `e2` n’est pas soumise à une *conversion d’expression conditionnelle*.</span><span class="sxs-lookup"><span data-stu-id="5046a-106">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

### <a name="open-issues"></a><span data-ttu-id="5046a-107">Problèmes ouverts</span><span class="sxs-lookup"><span data-stu-id="5046a-107">Open Issues</span></span>

<span data-ttu-id="5046a-108">Nous souhaitons étendre ce type de cible aux cas dans lesquels l’expression conditionnelle a un type commun pour `e1` et, `e2` mais il n’existe aucune conversion de ce type commun vers le type cible.</span><span class="sxs-lookup"><span data-stu-id="5046a-108">We would like to extend this target typing to cases in which the conditional expression has a common type for `e1` and `e2` but there is no conversion from that common type to the target type.</span></span> <span data-ttu-id="5046a-109">Cela entraînerait le typage de la cible de l’expression conditionnelle en alignement du typage de la cible de l’expression de commutateur.</span><span class="sxs-lookup"><span data-stu-id="5046a-109">That would bring target typing of the conditional expression into alignment of target typing of the switch expression.</span></span> <span data-ttu-id="5046a-110">Toutefois, il s’agit d’une modification avec rupture :</span><span class="sxs-lookup"><span data-stu-id="5046a-110">However we are concerned that would be a breaking change:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

<span data-ttu-id="5046a-111">Nous pourrions réduire l’étendue de la modification avec rupture en modifiant les règles pour *une meilleure conversion à partir de l’expression*: la conversion d’une expression conditionnelle en T1 est une meilleure conversion d’expression que la conversion en T2 si la conversion en T1 n’est pas une *conversion d’expression conditionnelle* et que la conversion en T2 est une *conversion d’expression conditionnelle*.</span><span class="sxs-lookup"><span data-stu-id="5046a-111">We could reduce the scope of the breaking change by modifying the rules for *better conversion from expression*: the conversion from a conditional expression to T1 is a better conversion from expression than the conversion to T2 if the conversion to T1 is not a *conditional expression conversion* and the conversion to T2 is a *conditional expression conversion*.</span></span>  <span data-ttu-id="5046a-112">Cela résout la modification avec rupture dans le programme ci-dessus (il appelle `M(long)` avec ou sans cette fonctionnalité).</span><span class="sxs-lookup"><span data-stu-id="5046a-112">That resolves the breaking change in the above program (it calls `M(long)` with or without this feature).</span></span> <span data-ttu-id="5046a-113">Cette approche présente deux petits inconvénients.</span><span class="sxs-lookup"><span data-stu-id="5046a-113">This approach does have two small downsides.</span></span>  <span data-ttu-id="5046a-114">Tout d’abord, il n’est pas tout à fait identique à l’expression de Switch :</span><span class="sxs-lookup"><span data-stu-id="5046a-114">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="5046a-115">Il s’agit toujours d’une modification avec rupture, mais son étendue est moins susceptible d’affecter des programmes réels :</span><span class="sxs-lookup"><span data-stu-id="5046a-115">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="5046a-116">Cela devient ambigu, car la conversion en `long` est préférable pour le premier argument (parce qu’il n’utilise pas la *conversion d’expression conditionnelle*), mais la conversion en `short` est préférable pour le deuxième argument (car `short` est une *meilleure cible de conversion* que `long` ).</span><span class="sxs-lookup"><span data-stu-id="5046a-116">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="5046a-117">Cette modification avec rupture semble moins grave, car elle ne modifie pas le comportement d’un programme existant en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="5046a-117">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="5046a-118">Si nous choisissons d’apporter cette modification à la proposition, nous modifierons</span><span class="sxs-lookup"><span data-stu-id="5046a-118">Should we elect to make this change to the proposal, we would change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="5046a-119">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="5046a-119">Better conversion from expression</span></span>
> 
> <span data-ttu-id="5046a-120">Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5046a-120">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="5046a-121">`E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="5046a-121">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="5046a-122">`T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="5046a-122">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="5046a-123">to</span><span class="sxs-lookup"><span data-stu-id="5046a-123">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="5046a-124">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="5046a-124">Better conversion from expression</span></span>
> 
> <span data-ttu-id="5046a-125">Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5046a-125">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="5046a-126">`E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="5046a-126">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="5046a-127">\*\*`C1`n’est pas une *conversion d’expression conditionnelle* et `C2` est une \* conversion d’expression conditionnelle \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="5046a-127">\*\*`C1` is not a *conditional expression conversion* and `C2` is a \*conditional expression conversion\*\*\*.</span></span>
> * <span data-ttu-id="5046a-128">`T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target)) \* \* et soit `C1` et `C2` sont des *conversions d’expressions conditionnelles* , soit aucune n’est une conversion d’expression conditionnelle \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="5046a-128">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` and `C2` are both *conditional expression conversions* or neither is a \*conditional expression conversion\*\*\*.</span></span>
