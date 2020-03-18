---
ms.openlocfilehash: 25e95b3ab8c384a7e66e59a7f9422cc9699074d7
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484719"
---
# <a name="infer-tuple-names-aka-tuple-projection-initializers"></a><span data-ttu-id="ad8a4-101">Déduire les noms de tuples (alias.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-101">Infer tuple names (aka.</span></span> <span data-ttu-id="ad8a4-102">initialiseurs de projection de Tuple)</span><span class="sxs-lookup"><span data-stu-id="ad8a4-102">tuple projection initializers)</span></span>

## <a name="summary"></a><span data-ttu-id="ad8a4-103">Résumé</span><span class="sxs-lookup"><span data-stu-id="ad8a4-103">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ad8a4-104">Dans un certain nombre de cas courants, cette fonctionnalité permet d’omettre les noms d’éléments de tuple et de les déduire.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-104">In a number of common cases, this feature allows the tuple element names to be omitted and instead be inferred.</span></span> <span data-ttu-id="ad8a4-105">Par exemple, au lieu de taper `(f1: x.f1, f2: x?.f2)`, les noms d’éléments « F1 » et « F2 » peuvent être déduits de `(x.f1, x?.f2)`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-105">For instance, instead of typing `(f1: x.f1, f2: x?.f2)`, the element names "f1" and "f2" can be inferred from `(x.f1, x?.f2)`.</span></span>

<span data-ttu-id="ad8a4-106">Cela correspond au comportement des types anonymes, qui permettent de déduire les noms de membres lors de la création.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-106">This parallels the behavior of  anonymous types, which allow inferring member names during creation.</span></span> <span data-ttu-id="ad8a4-107">Par exemple, `new { x.f1, y?.f2 }` déclare les membres « F1 » et « F2 ».</span><span class="sxs-lookup"><span data-stu-id="ad8a4-107">For instance, `new { x.f1, y?.f2 }` declares members "f1" and "f2".</span></span>

<span data-ttu-id="ad8a4-108">Ceci est particulièrement pratique lors de l’utilisation de tuples dans LINQ :</span><span class="sxs-lookup"><span data-stu-id="ad8a4-108">This is particularly handy when using tuples in LINQ:</span></span>

```csharp
// "c" and "result" have element names "f1" and "f2"
var result = list.Select(c => (c.f1, c.f2)).Where(t => t.f2 == 1); 
```

## <a name="detailed-design"></a><span data-ttu-id="ad8a4-109">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="ad8a4-109">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="ad8a4-110">La modification comporte deux parties :</span><span class="sxs-lookup"><span data-stu-id="ad8a4-110">There are two parts to the change:</span></span>

1.  <span data-ttu-id="ad8a4-111">Essayez de déduire un nom de candidat pour chaque élément de tuple qui n’a pas de nom explicite :</span><span class="sxs-lookup"><span data-stu-id="ad8a4-111">Try to infer a candidate name for each tuple element which does not have an explicit name:</span></span>
    -   <span data-ttu-id="ad8a4-112">Utilisation des mêmes règles que l’inférence de nom pour les types anonymes.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-112">Using same rules as name inference for anonymous types.</span></span>
        - <span data-ttu-id="ad8a4-113">Dans C#, cela autorise trois cas : `y` (identificateur), `x.y` (accès aux membres simples) et `x?.y` (accès conditionnel).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-113">In C#, this allows three cases: `y` (identifier), `x.y` (simple member access) and `x?.y` (conditional access).</span></span>
        - <span data-ttu-id="ad8a4-114">Dans VB, cela permet des cas supplémentaires, tels que les `x.y()`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-114">In VB, this allows for additional cases, such as `x.y()`.</span></span>
    -   <span data-ttu-id="ad8a4-115">Rejet des noms de tuple réservés (respectant la C#casse dans, non sensible à la casse dans VB), car ils sont interdits ou déjà implicites.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-115">Rejecting reserved tuple names (case-sensitive in C#, case-insensitive in VB), as they are either forbidden or already implicit.</span></span> <span data-ttu-id="ad8a4-116">Par exemple, par exemple `ItemN`, `Rest`et `ToString`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-116">For instance, such as `ItemN`, `Rest`, and `ToString`.</span></span>
    -   <span data-ttu-id="ad8a4-117">Si les noms des candidats sont des doublons (respect de C#la casse dans, non-respect de la casse dans VB) au sein de l’ensemble du tuple, nous détenons ces candidats,</span><span class="sxs-lookup"><span data-stu-id="ad8a4-117">If any candidate names are duplicates (case-sensitive in C#, case-insensitive in VB) within the entire tuple, we drop those candidates,</span></span>
2.  <span data-ttu-id="ad8a4-118">Lors des conversions (qui vérifient et signalent la suppression des noms des littéraux de Tuple), les noms inférés ne produisent pas d’avertissements.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-118">During conversions (which check and warn about dropping names from tuple literals), inferred names would not produce any warnings.</span></span> <span data-ttu-id="ad8a4-119">Cela évite de rompre le code de tuple existant.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-119">This avoids breaking existing tuple code.</span></span>

<span data-ttu-id="ad8a4-120">Notez que la règle de gestion des doublons est différente de celle des types anonymes.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-120">Note that the rule for handling duplicates is different than that for anonymous types.</span></span> <span data-ttu-id="ad8a4-121">Par exemple, `new { x.f1, x.f1 }` génère une erreur, mais `(x.f1, x.f1)` serait toujours autorisé (juste sans aucun nom déduit).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-121">For instance, `new { x.f1, x.f1 }` produces an error, but `(x.f1, x.f1)` would still be allowed (just without any inferred names).</span></span> <span data-ttu-id="ad8a4-122">Cela évite de rompre le code de tuple existant.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-122">This avoids breaking existing tuple code.</span></span>

<span data-ttu-id="ad8a4-123">Pour des éléments de cohérence, il en va de même pour les tuples produits par les C#affectations de déconstruction (dans) :</span><span class="sxs-lookup"><span data-stu-id="ad8a4-123">For consistency, the same would apply to tuples produced by deconstruction-assignments (in C#):</span></span>

```csharp
// tuple has element names "f1" and "f2" 
var tuple = ((x.f1, x?.f2) = (1, 2));
```

<span data-ttu-id="ad8a4-124">Il en va de même pour les tuples VB, en utilisant les règles spécifiques à VB pour déduire le nom de l’expression et les comparaisons de noms ne respectant pas la casse.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-124">The same would also apply to VB tuples, using the VB-specific rules for inferring name from expression and case-insensitive name comparisons.</span></span>

<span data-ttu-id="ad8a4-125">Quand vous utilisez C# le compilateur 7,1 (ou version ultérieure) avec la version de langue "7,0", les noms d’éléments sont déduits (en dépit de la non disponibilité de la fonctionnalité), mais une erreur d’utilisation de site se produira pour tenter d’y accéder.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-125">When using the C# 7.1 compiler (or later) with language version "7.0", the element names will be inferred (despite the feature not being available), but there will be a use-site error for trying to access them.</span></span> <span data-ttu-id="ad8a4-126">Cela limitera les ajouts de nouveau code qui, par la suite, conviendrait au problème de compatibilité (décrit ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-126">This will limit additions of new code that would later face the compatibility issue (described below).</span></span>

## <a name="drawbacks"></a><span data-ttu-id="ad8a4-127">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="ad8a4-127">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="ad8a4-128">L’inconvénient principal est que cela introduit une interruption de compatibilité C# de 7,0 :</span><span class="sxs-lookup"><span data-stu-id="ad8a4-128">The main drawback is that this introduces a compatibility break from C# 7.0:</span></span>

```csharp
Action y = () => M();
var t = (x: x, y);
t.y(); // this might have previously picked up an extension method called “y”, but would now call the lambda.
```

<span data-ttu-id="ad8a4-129">Le Conseil de compatibilité a détecté que cette interruption était acceptable, étant donné qu’elle est limitée et que la fenêtre de C# temps depuis les tuples expédiés (dans 7,0) est courte.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-129">The compatibility council found this break acceptable, given that it is limited and the time window since tuples shipped (in C# 7.0) is short.</span></span>

## <a name="references"></a><span data-ttu-id="ad8a4-130">References</span><span class="sxs-lookup"><span data-stu-id="ad8a4-130">References</span></span>
- [<span data-ttu-id="ad8a4-131">LDM avril 4e 2017</span><span class="sxs-lookup"><span data-stu-id="ad8a4-131">LDM April 4th 2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md#tuple-names)
- <span data-ttu-id="ad8a4-132">[Discussion GitHub](https://github.com/dotnet/csharplang/issues/370) (Merci @alrz pour la remise de ce problème)</span><span class="sxs-lookup"><span data-stu-id="ad8a4-132">[Github discussion](https://github.com/dotnet/csharplang/issues/370) (thanks @alrz for bringing this issue up)</span></span>
- [<span data-ttu-id="ad8a4-133">Conception des tuples</span><span class="sxs-lookup"><span data-stu-id="ad8a4-133">Tuples design</span></span>](https://github.com/dotnet/roslyn/blob/master/docs/features/tuples.md)
