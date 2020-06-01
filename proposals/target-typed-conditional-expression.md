---
ms.openlocfilehash: bfdd44c81a40c49b26ac15a69d2327f795bd88e6
ms.sourcegitcommit: 85263bfff8512e3e6fa4da7dc75e892937076de8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84239034"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="67875-101">Expression conditionnelle typée cible</span><span class="sxs-lookup"><span data-stu-id="67875-101">Target-Typed Conditional Expression</span></span>

## <a name="conditional-expression-conversion"></a><span data-ttu-id="67875-102">Conversion d’expression conditionnelle</span><span class="sxs-lookup"><span data-stu-id="67875-102">Conditional Expression Conversion</span></span>

<span data-ttu-id="67875-103">Pour une expression conditionnelle `c ? e1 : e2` , lorsque</span><span class="sxs-lookup"><span data-stu-id="67875-103">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="67875-104">Il n’existe aucun type commun pour `e1` et `e2` , ou</span><span class="sxs-lookup"><span data-stu-id="67875-104">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="67875-105">pour lequel un type commun existe, mais l’une des expressions `e1` ou n' `e2` a pas de conversion implicite vers ce type</span><span class="sxs-lookup"><span data-stu-id="67875-105">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="67875-106">Nous définissons une nouvelle *conversion d’expression conditionnelle* implicite qui autorise une conversion implicite de l’expression conditionnelle en n’importe quel type `T` pour lequel il existe une conversion de-from-expression de `e1` en `T` et également de à `e2` `T` .</span><span class="sxs-lookup"><span data-stu-id="67875-106">we define a new implicit *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="67875-107">Il s’agit d’une erreur si une expression conditionnelle n’a pas de type commun entre `e1` et ou `e2` n’est pas soumise à une *conversion d’expression conditionnelle*.</span><span class="sxs-lookup"><span data-stu-id="67875-107">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

## <a name="better-conversion-from-expression"></a><span data-ttu-id="67875-108">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="67875-108">Better Conversion from Expression</span></span>

<span data-ttu-id="67875-109">Nous changeons</span><span class="sxs-lookup"><span data-stu-id="67875-109">We change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="67875-110">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="67875-110">Better conversion from expression</span></span>
> 
> <span data-ttu-id="67875-111">Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="67875-111">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="67875-112">`E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="67875-112">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="67875-113">`T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="67875-113">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="67875-114">to</span><span class="sxs-lookup"><span data-stu-id="67875-114">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="67875-115">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="67875-115">Better conversion from expression</span></span>
> 
> <span data-ttu-id="67875-116">Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="67875-116">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="67875-117">`E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="67875-117">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="67875-118">\*\*`C1`n’est pas une *conversion d’expression conditionnelle* et `C2` est une \* conversion d’expression conditionnelle \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="67875-118">\*\*`C1` is not a *conditional expression conversion* and `C2` is a \*conditional expression conversion\*\*\*.</span></span>
> * <span data-ttu-id="67875-119">`T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target)) \* \* et soit `C1` et `C2` sont des *conversions d’expressions conditionnelles* , soit aucune n’est une conversion d’expression conditionnelle \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="67875-119">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` and `C2` are both *conditional expression conversions* or neither is a \*conditional expression conversion\*\*\*.</span></span>

## <a name="cast-expression"></a><span data-ttu-id="67875-120">Expression cast</span><span class="sxs-lookup"><span data-stu-id="67875-120">Cast Expression</span></span>

<span data-ttu-id="67875-121">La spécification du langage C# actuel indique</span><span class="sxs-lookup"><span data-stu-id="67875-121">The current C# language specification says</span></span>

> <span data-ttu-id="67875-122">*Cast_expression* de la forme `(T)E` , où `T` est un *type* et `E` est un *unary_expression*, effectue une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` en type `T` .</span><span class="sxs-lookup"><span data-stu-id="67875-122">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span>

<span data-ttu-id="67875-123">En présence de la *conversion d’expression conditionnelle* , il peut y avoir plusieurs conversions possibles de `E` à `T` .</span><span class="sxs-lookup"><span data-stu-id="67875-123">In the presence of the *conditional expression conversion* there may be more than one possible conversion from `E` to `T`.</span></span> <span data-ttu-id="67875-124">Avec l’ajout de la *conversion d’expression conditionnelle*, nous préférons toute conversion en une *conversion d’expression conditionnelle*et n’utilisez la *conversion d’expression conditionnelle* qu’en dernier recours.</span><span class="sxs-lookup"><span data-stu-id="67875-124">With the addition of *conditional expression conversion*, we prefer any other conversion to a *conditional expression conversion*, and use the *conditional expression conversion* only as a last resort.</span></span>

## <a name="design-notes"></a><span data-ttu-id="67875-125">Notes de création</span><span class="sxs-lookup"><span data-stu-id="67875-125">Design Notes</span></span>

<span data-ttu-id="67875-126">La raison de la modification pour une *meilleure conversion à partir de l’expression* consiste à gérer un cas de ce type :</span><span class="sxs-lookup"><span data-stu-id="67875-126">The reason for the change to *Better conversion from expression* is to handle a case such as this:</span></span>

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

<span data-ttu-id="67875-127">Cette approche présente deux petits inconvénients.</span><span class="sxs-lookup"><span data-stu-id="67875-127">This approach does have two small downsides.</span></span>  <span data-ttu-id="67875-128">Tout d’abord, il n’est pas tout à fait identique à l’expression de Switch :</span><span class="sxs-lookup"><span data-stu-id="67875-128">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="67875-129">Il s’agit toujours d’une modification avec rupture, mais son étendue est moins susceptible d’affecter des programmes réels :</span><span class="sxs-lookup"><span data-stu-id="67875-129">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="67875-130">Cela devient ambigu, car la conversion en `long` est préférable pour le premier argument (parce qu’il n’utilise pas la *conversion d’expression conditionnelle*), mais la conversion en `short` est préférable pour le deuxième argument (car `short` est une *meilleure cible de conversion* que `long` ).</span><span class="sxs-lookup"><span data-stu-id="67875-130">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="67875-131">Cette modification avec rupture semble moins grave, car elle ne modifie pas le comportement d’un programme existant en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="67875-131">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="67875-132">La raison des notes sur l’expression de cast consiste à gérer un cas de ce type :</span><span class="sxs-lookup"><span data-stu-id="67875-132">The reason for the notes on the cast expression is to handle a case such as this:</span></span>

```csharp
_ = (short)(b ? 1 : 2);
```

<span data-ttu-id="67875-133">Ce programme utilise actuellement la conversion explicite de `int` en `short` et nous souhaitons conserver le sens du langage actuel de ce programme.</span><span class="sxs-lookup"><span data-stu-id="67875-133">This program currently uses the explicit conversion from `int` to `short`, and we want to preserve the current language meaning of this program.</span></span>  <span data-ttu-id="67875-134">La modification serait impossible à observer lors de l’exécution, mais avec le programme suivant, la modification serait observable :</span><span class="sxs-lookup"><span data-stu-id="67875-134">The change would be unobservable at runtime, but with the following program the change would be observable:</span></span>

```csharp
_ = (A)(b ? c : d);
```

<span data-ttu-id="67875-135">où `c` est de type `C` , `d` est de type `D` , et il existe une conversion implicite définie par l’utilisateur de `C` en `D` , et une conversion implicite définie par l’utilisateur de `D` en `A` , et une conversion implicite définie par l’utilisateur de `C` en `A` .</span><span class="sxs-lookup"><span data-stu-id="67875-135">where `c` is of type `C`, `d` is of type `D`, and there is an implicit user-defined conversion from `C` to `D`, and an implicit user-defined conversion from `D` to `A`, and an implicit user-defined conversion from `C` to `A`.</span></span> <span data-ttu-id="67875-136">Si ce code est compilé avant C# 9,0, lorsque `b` a la valeur true, nous convertissons de `c` en en `D` `A` .</span><span class="sxs-lookup"><span data-stu-id="67875-136">If this code is compiled before C# 9.0, when `b` is true we convert from `c` to `D` then to `A`.</span></span> <span data-ttu-id="67875-137">Si nous utilisons la *conversion d’expression conditionnelle*, lorsque `b` a la valeur true, la conversion de `c` en est `A` exécutée directement, ce qui exécute une autre séquence de code utilisateur.</span><span class="sxs-lookup"><span data-stu-id="67875-137">If we use the *conditional expression conversion*, then when `b` is true we convert from `c` to `A` directly, which executes a different sequence of user code.</span></span> <span data-ttu-id="67875-138">Par conséquent, nous traitons la *conversion d’expression conditionnelle* en dernier recours dans un cast pour préserver le comportement existant.</span><span class="sxs-lookup"><span data-stu-id="67875-138">Therefore we treat the *conditional expression conversion* as a last resort in a cast, to preserve existing behavior.</span></span>
