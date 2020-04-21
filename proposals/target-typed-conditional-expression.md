---
ms.openlocfilehash: 80ccdb75e2f5a022e367f3c023ea4464195deaaf
ms.sourcegitcommit: 95f5f86ba2e2a23cd4fb37bd9d1ff690c83d1191
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81647133"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="dea5b-101">Expression conditionnelle de type cible</span><span class="sxs-lookup"><span data-stu-id="dea5b-101">Target-Typed Conditional Expression</span></span>

<span data-ttu-id="dea5b-102">Pour une `c ? e1 : e2`expression conditionnelle, quand</span><span class="sxs-lookup"><span data-stu-id="dea5b-102">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="dea5b-103">il n’existe `e1` pas `e2`de type commun pour et , ou</span><span class="sxs-lookup"><span data-stu-id="dea5b-103">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="dea5b-104">pour lequel un type commun n’existe qu’une seule des expressions `e1` ou `e2` n’a pas de conversion implicite à ce type</span><span class="sxs-lookup"><span data-stu-id="dea5b-104">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="dea5b-105">nous définissons une nouvelle *conversion d’expression conditionnelle* qui `T` permet une conversion implicite de `e1` l’expression conditionnelle à tout type pour lequel il y a une conversion de l’expression d’à partir `T` et aussi de `e2` . `T`</span><span class="sxs-lookup"><span data-stu-id="dea5b-105">we define a new *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="dea5b-106">Il s’agit d’une erreur si `e1` une `e2` expression conditionnelle n’a pas de type commun entre et n’est pas soumise à une conversion d’expression *conditionnelle*.</span><span class="sxs-lookup"><span data-stu-id="dea5b-106">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

### <a name="open-issues"></a><span data-ttu-id="dea5b-107">Open Issues</span><span class="sxs-lookup"><span data-stu-id="dea5b-107">Open Issues</span></span>

<span data-ttu-id="dea5b-108">Nous aimerions étendre cette cible de dactylographie aux cas `e1` où l’expression conditionnelle a un type commun et `e2` mais il n’y a pas de conversion de ce type commun au type cible.</span><span class="sxs-lookup"><span data-stu-id="dea5b-108">We would like to extend this target typing to cases in which the conditional expression has a common type for `e1` and `e2` but there is no conversion from that common type to the target type.</span></span> <span data-ttu-id="dea5b-109">Cela amènerait la dactylographie cible de l’expression conditionnelle dans l’alignement de la frappe cible de l’expression de commutateur.</span><span class="sxs-lookup"><span data-stu-id="dea5b-109">That would bring target typing of the conditional expression into alignment of target typing of the switch expression.</span></span> <span data-ttu-id="dea5b-110">Cependant, nous craignons que ce serait un changement radical :</span><span class="sxs-lookup"><span data-stu-id="dea5b-110">However we are concerned that would be a breaking change:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

<span data-ttu-id="dea5b-111">Nous pourrions réduire la portée du changement de rupture en modifiant les règles pour *une meilleure conversion de l’expression*: la conversion d’une expression conditionnelle à T1 est une meilleure conversion de l’expression que la conversion en T2 si la conversion en T1 n’est pas une conversion *d’expression conditionnelle* et la conversion en T2 est une conversion *d’expression conditionnelle.*</span><span class="sxs-lookup"><span data-stu-id="dea5b-111">We could reduce the scope of the breaking change by modifying the rules for *better conversion from expression*: the conversion from a conditional expression to T1 is a better conversion from expression than the conversion to T2 if the conversion to T1 is not a *conditional expression conversion* and the conversion to T2 is a *conditional expression conversion*.</span></span>  <span data-ttu-id="dea5b-112">Cela résout le changement de rupture `M(long)` dans le programme ci-dessus (il appelle avec ou sans cette fonctionnalité).</span><span class="sxs-lookup"><span data-stu-id="dea5b-112">That resolves the breaking change in the above program (it calls `M(long)` with or without this feature).</span></span> <span data-ttu-id="dea5b-113">Cette approche a deux petits inconvénients.</span><span class="sxs-lookup"><span data-stu-id="dea5b-113">This approach does have two small downsides.</span></span>  <span data-ttu-id="dea5b-114">Tout d’abord, il n’est pas tout à fait le même que l’expression de commutateur:</span><span class="sxs-lookup"><span data-stu-id="dea5b-114">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="dea5b-115">Il s’agit encore d’un changement radical, mais sa portée est moins susceptible d’affecter les programmes réels :</span><span class="sxs-lookup"><span data-stu-id="dea5b-115">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="dea5b-116">Cela devient ambigu parce `long` que la conversion est meilleure pour le premier argument (parce `short` qu’elle n’utilise `short` pas la conversion `long` *d’expression conditionnelle),* mais la conversion est meilleure pour le deuxième argument (parce que c’est une meilleure cible de *conversion* que ).</span><span class="sxs-lookup"><span data-stu-id="dea5b-116">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="dea5b-117">Ce changement de rupture semble moins grave parce qu’il ne change pas silencieusement le comportement d’un programme existant.</span><span class="sxs-lookup"><span data-stu-id="dea5b-117">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="dea5b-118">Si nous décidons d’apporter ce changement à la proposition, nous</span><span class="sxs-lookup"><span data-stu-id="dea5b-118">Should we elect to make this change to the proposal, we would change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="dea5b-119">Meilleure conversion de l’expression</span><span class="sxs-lookup"><span data-stu-id="dea5b-119">Better conversion from expression</span></span>
> 
> <span data-ttu-id="dea5b-120">Compte tenu `C1` d’une conversion `E` implicite qui `T1`se convertit `C2` d’une expression `E` à `T2`un `C1` type , et une conversion implicite qui se convertit d’une expression à un type , est une ***meilleure conversion*** `C2` que si `E` n’est pas exactement correspondre `T2` et au moins l’un des points suivants détient:</span><span class="sxs-lookup"><span data-stu-id="dea5b-120">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="dea5b-121">`E`exactement `T1` correspond ([Exactement correspondant Expression](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="dea5b-121">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="dea5b-122">`T1`est une meilleure `T2` cible de conversion que ([Meilleure cible de conversion](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="dea5b-122">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="dea5b-123">to</span><span class="sxs-lookup"><span data-stu-id="dea5b-123">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="dea5b-124">Meilleure conversion de l’expression</span><span class="sxs-lookup"><span data-stu-id="dea5b-124">Better conversion from expression</span></span>
> 
> <span data-ttu-id="dea5b-125">Compte tenu `C1` d’une conversion `E` implicite qui `T1`se convertit `C2` d’une expression `E` à `T2`un `C1` type , et une conversion implicite qui se convertit d’une expression à un type , est une ***meilleure conversion*** `C2` que si `E` n’est pas exactement correspondre `T2` et au moins l’un des points suivants détient:</span><span class="sxs-lookup"><span data-stu-id="dea5b-125">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="dea5b-126">`E`exactement `T1` correspond ([Exactement correspondant Expression](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="dea5b-126">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="dea5b-127">`T1`est une meilleure `T2` cible de conversion que[(meilleure cible de conversion](expressions.md#better-conversion-target)) et n’est `C1` pas une *conversion d’expression conditionnelle* ou `C2` est une conversion d’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="dea5b-127">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` is not a *conditional expression conversion* or `C2` is a \*conditional expression conversion\*\*\*.</span></span>
