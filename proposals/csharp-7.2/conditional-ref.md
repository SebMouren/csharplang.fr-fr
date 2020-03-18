---
ms.openlocfilehash: 63dfdfee9ea6c16e162f483aa1298feed297daef
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484698"
---
# <a name="conditional-ref-expressions"></a><span data-ttu-id="2d3d1-101">Expressions de référence conditionnelle</span><span class="sxs-lookup"><span data-stu-id="2d3d1-101">Conditional ref expressions</span></span>

<span data-ttu-id="2d3d1-102">Le modèle de liaison d’une variable Ref à une ou une autre expression de manière conditionnelle n’est C#pas exprimable actuellement dans.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-102">The pattern of binding a ref variable to one or another expression conditionally is not currently expressible in C#.</span></span>

<span data-ttu-id="2d3d1-103">La solution de contournement classique consiste à introduire une méthode comme :</span><span class="sxs-lookup"><span data-stu-id="2d3d1-103">The typical workaround is to introduce a method like:</span></span>

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

<span data-ttu-id="2d3d1-104">Notez qu’il ne s’agit pas d’un remplacement exact d’un ternaire, car tous les arguments doivent être évalués sur le site d’appel.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-104">Note that this is not an exact replacement of a ternary since all arguments must be evaluated at the call site.</span></span>

<span data-ttu-id="2d3d1-105">Les éléments suivants ne fonctionnent pas comme prévu :</span><span class="sxs-lookup"><span data-stu-id="2d3d1-105">The following will not work as expected:</span></span>

```csharp
       // will crash with NRE because 'arr[0]' will be executed unconditionally
      ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

<span data-ttu-id="2d3d1-106">La syntaxe proposée devrait ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="2d3d1-106">The proposed syntax would look like:</span></span>

```csharp
     <condition> ? ref <consequence> : ref <alternative>;
```

<span data-ttu-id="2d3d1-107">La tentative ci-dessus avec « Choice » peut être écrite _correctement_ à l’aide de Ref ternaire en tant que :</span><span class="sxs-lookup"><span data-stu-id="2d3d1-107">The above attempt with "Choice" can be _correctly_ written using ref ternary as:</span></span>

```csharp
     ref var r = ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="2d3d1-108">La différence par rapport au choix est que la conséquence et les expressions de remplacement sont accessibles de manière _véritablement_ conditionnelle, donc aucun blocage ne s’affiche si ```arr == null```</span><span class="sxs-lookup"><span data-stu-id="2d3d1-108">The difference from Choice is that consequence and alternative expressions are accessed in a _truly_ conditional manner, so we do not see a crash if ```arr == null```</span></span>

<span data-ttu-id="2d3d1-109">La référence ternaire est juste un ternaire où les deux alternatives et les conséquences sont des références.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-109">The ternary ref is just a ternary where both alternative and consequence are refs.</span></span> <span data-ttu-id="2d3d1-110">Cela nécessitera naturellement que la conséquence/les opérandes alternatifs soient LValues.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-110">It will naturally require that consequence/alternative operands are LValues.</span></span> <span data-ttu-id="2d3d1-111">Elle nécessitera également que les types de conséquence et de remplacement soient convertibles les uns des autres.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-111">It will also require that consequence and alternative have types that are identity convertible to each other.</span></span>

<span data-ttu-id="2d3d1-112">Le type de l’expression sera calculé de la même manière que celui du ternaire standard.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-112">The type of the expression will be computed similarly to the one for the regular ternary.</span></span> <span data-ttu-id="2d3d1-113">savoir</span><span class="sxs-lookup"><span data-stu-id="2d3d1-113">I.E.</span></span> <span data-ttu-id="2d3d1-114">dans un cas, si la conséquence et l’alternative ont une identité convertible, mais des types différents, les règles de fusion de type existantes s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-114">in a case if consequence and alternative have identity convertible, but different types, the existing type-merging rules will apply.</span></span>

<span data-ttu-id="2d3d1-115">La sécurité à retourner est considérée comme prudente par rapport aux opérandes conditionnels.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-115">Safe-to-return will be assumed conservatively from the conditional operands.</span></span> <span data-ttu-id="2d3d1-116">Si l’un ou l’autre risque de retourner la totalité de l’élément, il est risqué de retourner.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-116">If either is unsafe to return the whole thing is unsafe to return.</span></span>

<span data-ttu-id="2d3d1-117">La référence ternaire est une LValue et, par conséquent, elle peut être passée/assignée/retournée par référence ;</span><span class="sxs-lookup"><span data-stu-id="2d3d1-117">Ref ternary is an LValue and as such it can be passed/assigned/returned by reference;</span></span>

```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="2d3d1-118">Étant une LValue, elle peut également être assignée à.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-118">Being an LValue, it can also be assigned to.</span></span> 

```csharp
    // assign to
    (arr != null ? ref arr[0]: ref otherArr[0]) = 1;
```

<span data-ttu-id="2d3d1-119">Ref ternaire peut également être utilisé dans un contexte standard (non Ref).</span><span class="sxs-lookup"><span data-stu-id="2d3d1-119">Ref ternary can be used in a regular (not ref) context as well.</span></span> <span data-ttu-id="2d3d1-120">Bien qu’il ne soit pas courant, il est possible d’utiliser un ternaire standard.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-120">Although it would not be common since you could as well just use a regular ternary.</span></span>

```csharp
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```


___

<span data-ttu-id="2d3d1-121">Remarques sur l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="2d3d1-121">Implementation notes:</span></span> 

<span data-ttu-id="2d3d1-122">La complexité de l’implémentation semble être la taille d’un correctif de bogue de taille moyenne à grande.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-122">The complexity of the implementation would seem to be the size of a moderate-to-large bug fix.</span></span> <span data-ttu-id="2d3d1-123">-I. E pas très cher.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-123">- I.E not very expensive.</span></span>
<span data-ttu-id="2d3d1-124">Je ne pense pas que les modifications apportées à la syntaxe ou à l’analyse ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-124">I do not think we need any changes to the syntax or parsing.</span></span>
<span data-ttu-id="2d3d1-125">Il n’y a aucun effet sur les métadonnées ou l’interopérabilité.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-125">There is no effect on metadata or interop.</span></span> <span data-ttu-id="2d3d1-126">La fonctionnalité est entièrement basée sur une expression.</span><span class="sxs-lookup"><span data-stu-id="2d3d1-126">The feature is completely expression based.</span></span>
<span data-ttu-id="2d3d1-127">Aucun effet sur le débogage/PDB</span><span class="sxs-lookup"><span data-stu-id="2d3d1-127">No effect on debugging/PDB either</span></span>
