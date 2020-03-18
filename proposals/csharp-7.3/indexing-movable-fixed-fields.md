---
ms.openlocfilehash: 36f3e6204d12c2569b3a55f3a47f58337e8a08e4
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484628"
---
# <a name="indexing-fixed-fields-should-not-require-pinning-regardless-of-the-movableunmovable-context"></a><span data-ttu-id="78fcd-101">L’indexation des champs de `fixed` ne doit pas nécessiter l’épinglage, quel que soit le contexte mobile/non déplaçable.</span><span class="sxs-lookup"><span data-stu-id="78fcd-101">Indexing `fixed` fields should not require pinning regardless of the movable/unmovable context.</span></span> #

<span data-ttu-id="78fcd-102">La modification a la taille d’une résolution de bogue.</span><span class="sxs-lookup"><span data-stu-id="78fcd-102">The change has the size of a bug fix.</span></span> <span data-ttu-id="78fcd-103">Il peut être dans 7,3 et n’est pas en conflit avec la direction que nous prenons.</span><span class="sxs-lookup"><span data-stu-id="78fcd-103">It can be in 7.3 and does not conflict with whatever direction we take further.</span></span>
<span data-ttu-id="78fcd-104">Cette modification concerne uniquement la possibilité de fonctionnement du scénario suivant, même si `s` est déplaçable.</span><span class="sxs-lookup"><span data-stu-id="78fcd-104">This change is only about allowing the following scenario to work even though `s` is moveable.</span></span> <span data-ttu-id="78fcd-105">Elle est déjà valide lorsque `s` n’est pas déplaçable.</span><span class="sxs-lookup"><span data-stu-id="78fcd-105">It is already valid when `s` is not moveable.</span></span> 

<span data-ttu-id="78fcd-106">Remarque : dans les deux cas, il requiert toujours `unsafe` contexte.</span><span class="sxs-lookup"><span data-stu-id="78fcd-106">NOTE: in either case, it still requires `unsafe` context.</span></span> <span data-ttu-id="78fcd-107">Il est possible de lire des données non initialisées ou même hors limites.</span><span class="sxs-lookup"><span data-stu-id="78fcd-107">It is possible to read uninitialized data or even out of range.</span></span> <span data-ttu-id="78fcd-108">Cela ne change pas.</span><span class="sxs-lookup"><span data-stu-id="78fcd-108">That is not changing.</span></span>

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int p = s.myFixedField[5]; // indexing fixed-size array fields would be ok
    }
}
```

<span data-ttu-id="78fcd-109">Le « défi » principal que je vois ici est comment expliquer l’assouplissement dans la spécification. En particulier, étant donné que les éléments suivants doivent toujours être épinglés.</span><span class="sxs-lookup"><span data-stu-id="78fcd-109">The main “challenge” that I see here is how to explain the relaxation in the spec. In particular, since the following would still need pinning.</span></span> <span data-ttu-id="78fcd-110">(étant donné que `s` est déplaçable et que nous utilisons explicitement le champ comme pointeur)</span><span class="sxs-lookup"><span data-stu-id="78fcd-110">(because `s` is moveable and we explicitly use the field as a pointer)</span></span>

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int* ptr = s.myFixedField; // taking a pointer explicitly still requires pinning.
        int p = ptr[5];
    }
}
```

<span data-ttu-id="78fcd-111">L’un des motifs justifiant l’épinglage de la cible lorsqu’il est déplaçable est l’artefact de notre stratégie de génération de code,-nous convertissons toujours en pointeur non managé et forcez donc l’utilisateur à épingler via `fixed` instruction.</span><span class="sxs-lookup"><span data-stu-id="78fcd-111">One reason why we require pinning of the target when it is movable is the artifact of our code generation strategy, - we always convert to an unmanaged pointer and thus force the user to pin via `fixed` statement.</span></span> <span data-ttu-id="78fcd-112">Toutefois, la conversion en non managé n’est pas nécessaire lors de l’indexation.</span><span class="sxs-lookup"><span data-stu-id="78fcd-112">However, conversion to unmanaged is unnecessary when doing indexing.</span></span> <span data-ttu-id="78fcd-113">La même mathématique de pointeur non sécurisé est également applicable lorsque nous avons le récepteur sous la forme d’un pointeur managé.</span><span class="sxs-lookup"><span data-stu-id="78fcd-113">The same unsafe pointer math is equally applicable when we have the receiver in the form of a managed pointer.</span></span> <span data-ttu-id="78fcd-114">Dans ce cas, la référence intermédiaire est managée (suivi GC) et l’épinglage n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="78fcd-114">If we do that, then the intermediate ref is managed (GC-tracked) and the pinning is unnecessary.</span></span>

<span data-ttu-id="78fcd-115">La modification https://github.com/dotnet/roslyn/pull/24966 est un prototype PR qui assouplit cette exigence.</span><span class="sxs-lookup"><span data-stu-id="78fcd-115">The change https://github.com/dotnet/roslyn/pull/24966 is a prototype PR that relaxes this requirement.</span></span>
