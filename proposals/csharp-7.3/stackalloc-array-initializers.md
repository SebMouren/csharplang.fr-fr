---
ms.openlocfilehash: 7ea62713416ef82034963aef06f3cb11703342ed
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484607"
---
# <a name="stackalloc-array-initializers"></a><span data-ttu-id="a30e0-101">Initialiseurs de tableaux stackalloc</span><span class="sxs-lookup"><span data-stu-id="a30e0-101">Stackalloc array initializers</span></span>

## <a name="summary"></a><span data-ttu-id="a30e0-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="a30e0-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="a30e0-103">Autorise l’utilisation de la syntaxe d’initialiseur de tableau avec `stackalloc`.</span><span class="sxs-lookup"><span data-stu-id="a30e0-103">Allow array initializer syntax to be used with `stackalloc`.</span></span>

## <a name="motivation"></a><span data-ttu-id="a30e0-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="a30e0-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="a30e0-105">Les éléments des tableaux ordinaires peuvent être initialisés au moment de la création.</span><span class="sxs-lookup"><span data-stu-id="a30e0-105">Ordinary arrays can have their elements initialized at creation time.</span></span> <span data-ttu-id="a30e0-106">Il semble raisonnable de l’autoriser dans `stackalloc` cas.</span><span class="sxs-lookup"><span data-stu-id="a30e0-106">It seems reasonable to allow that in `stackalloc` case.</span></span>

<span data-ttu-id="a30e0-107">La question de la raison pour laquelle cette syntaxe n’est pas autorisée avec `stackalloc` se produit assez fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a30e0-107">The question of why such syntax is not allowed with `stackalloc` arises fairly frequently.</span></span>  
<span data-ttu-id="a30e0-108">Voir, par exemple, [#1112](https://github.com/dotnet/csharplang/issues/1112)</span><span class="sxs-lookup"><span data-stu-id="a30e0-108">See, for example, [#1112](https://github.com/dotnet/csharplang/issues/1112)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="a30e0-109">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="a30e0-109">Detailed design</span></span>

<span data-ttu-id="a30e0-110">Vous pouvez créer des tableaux ordinaires à l’aide de la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="a30e0-110">Ordinary arrays can be created through the following syntax:</span></span>

```csharp
new int[3]
new int[3] { 1, 2, 3 }
new int[] { 1, 2, 3 }
new[] { 1, 2, 3 }
```

<span data-ttu-id="a30e0-111">Nous devons autoriser la création de tableaux alloués par pile via :</span><span class="sxs-lookup"><span data-stu-id="a30e0-111">We should allow stack allocated arrays be created through:</span></span>  

```csharp
stackalloc int[3]               // currently allowed
stackalloc int[3] { 1, 2, 3 }
stackalloc int[] { 1, 2, 3 }
stackalloc[] { 1, 2, 3 }
```

<span data-ttu-id="a30e0-112">La sémantique de tous les cas est à peu près identique à celle des tableaux.</span><span class="sxs-lookup"><span data-stu-id="a30e0-112">The semantics of all cases is roughly the same as with arrays.</span></span>  
<span data-ttu-id="a30e0-113">Par exemple : dans le dernier cas, le type d’élément est déduit de l’initialiseur et doit être un type « non managé ».</span><span class="sxs-lookup"><span data-stu-id="a30e0-113">For example: in the last case the element type is inferred from the initializer and must be an "unmanaged" type.</span></span>

<span data-ttu-id="a30e0-114">Remarque : la fonctionnalité n’est pas dépendante de la cible qui est une `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="a30e0-114">NOTE: the feature is not dependent on the target being a `Span<T>`.</span></span> <span data-ttu-id="a30e0-115">Il est tout aussi applicable dans `T*` cas, donc il ne semble pas raisonnable de le prédicat sur `Span<T>` cas.</span><span class="sxs-lookup"><span data-stu-id="a30e0-115">It is just as applicable in `T*` case, so it does not seem reasonable to predicate it on `Span<T>` case.</span></span>  

## <a name="translation"></a><span data-ttu-id="a30e0-116">Traduction</span><span class="sxs-lookup"><span data-stu-id="a30e0-116">Translation</span></span>

<span data-ttu-id="a30e0-117">L’implémentation naïve peut simplement initialiser le tableau juste après la création via une série d’affectations d’éléments.</span><span class="sxs-lookup"><span data-stu-id="a30e0-117">The naive implementation could just initialize the array right after creation through a series of element-wise assignments.</span></span>  

<span data-ttu-id="a30e0-118">De même que dans le cas des tableaux, il peut être possible et souhaitable de détecter les cas où l’ensemble ou la plupart des éléments sont des types blittables et utilisent des techniques plus efficaces en copiant l’état pré-créé de tous les éléments constants.</span><span class="sxs-lookup"><span data-stu-id="a30e0-118">Similarly to the case with arrays, it might be possible and desirable to detect cases where all or most of the elements are blittable types and use more efficient techniques by copying over the pre-created state of all the constant elements.</span></span> 

## <a name="drawbacks"></a><span data-ttu-id="a30e0-119">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="a30e0-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

## <a name="alternatives"></a><span data-ttu-id="a30e0-120">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="a30e0-120">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="a30e0-121">Il s’agit d’une fonctionnalité pratique.</span><span class="sxs-lookup"><span data-stu-id="a30e0-121">This is a convenience feature.</span></span> <span data-ttu-id="a30e0-122">Il est possible de ne rien faire.</span><span class="sxs-lookup"><span data-stu-id="a30e0-122">It is possible to just do nothing.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="a30e0-123">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="a30e0-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="a30e0-124">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="a30e0-124">Design meetings</span></span>

<span data-ttu-id="a30e0-125">Aucun.</span><span class="sxs-lookup"><span data-stu-id="a30e0-125">None yet.</span></span> 
