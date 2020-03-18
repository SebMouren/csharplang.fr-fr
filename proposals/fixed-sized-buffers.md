---
ms.openlocfilehash: b51f27b2f58fd19851c80beb9cedcbd32b80b165
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484586"
---
# <a name="fixed-sized-buffers"></a><span data-ttu-id="8ff1b-101">Mémoires tampons de taille fixe</span><span class="sxs-lookup"><span data-stu-id="8ff1b-101">Fixed Sized Buffers</span></span>

* <span data-ttu-id="8ff1b-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="8ff1b-102">[x] Proposed</span></span>
* <span data-ttu-id="8ff1b-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="8ff1b-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="8ff1b-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="8ff1b-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="8ff1b-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="8ff1b-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="8ff1b-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="8ff1b-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="8ff1b-107">Fournissez un mécanisme à usage général et sécurisé pour déclarer des mémoires tampons de taille C# fixe dans le langage.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-107">Provide a general-purpose and safe mechanism for declaring fixed sized buffers to the C# language.</span></span>

## <a name="motivation"></a><span data-ttu-id="8ff1b-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="8ff1b-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="8ff1b-109">Aujourd’hui, les utilisateurs ont la possibilité de créer des mémoires tampons de taille fixe dans un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-109">Today, users have the ability to create fixed-sized buffers in an unsafe-context.</span></span> <span data-ttu-id="8ff1b-110">Toutefois, cela oblige l’utilisateur à traiter les pointeurs, à effectuer des vérifications manuelles et ne prend en charge qu’un ensemble limité de types (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`et `double`).</span><span class="sxs-lookup"><span data-stu-id="8ff1b-110">However, this requires the user to deal with pointers, manually perform bounds checks, and only supports a limited set of types (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`, and `double`).</span></span>

<span data-ttu-id="8ff1b-111">La réclamation la plus courante est que les mémoires tampons de taille fixe ne peuvent pas être indexées dans du code sécurisé.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-111">The most common complaint is that fixed-size buffers cannot be indexed in safe code.</span></span> <span data-ttu-id="8ff1b-112">L’impossibilité d’utiliser d’autres types est la seconde.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-112">Inability to use more types is the second.</span></span>

<span data-ttu-id="8ff1b-113">Avec quelques ajustements mineurs, nous pourrions fournir des mémoires tampons de taille fixe à usage général qui prennent en charge tout type, peuvent être utilisées dans un contexte sécurisé et avoir une vérification de limites automatique effectuée.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-113">With a few minor tweaks, we could provide general-purpose fixed-sized buffers which support any type, can be used in a safe context, and have automatic bounds checking performed.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="8ff1b-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="8ff1b-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="8ff1b-115">Vous pouvez déclarer une mémoire tampon de taille fixe sécurisée à l’aide des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="8ff1b-115">One would declare a safe fixed-sized buffer via the following:</span></span>

```csharp
public fixed DXGI_RGB GammaCurve[1025];
```

<span data-ttu-id="8ff1b-116">La déclaration est traduite dans une représentation interne par le compilateur qui est semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="8ff1b-116">The declaration would get translated into an internal representation by the compiler that is similar to the following</span></span>

```csharp
[FixedBuffer(typeof(DXGI_RGB), 1024)]
public ConsoleApp1.<Buffer>e__FixedBuffer_1024<DXGI_RGB> GammaCurve;

// Pack = 0 is the default packing and should result in indexable layout.
[CompilerGenerated, UnsafeValueType, StructLayout(LayoutKind.Sequential, Pack = 0)]
struct <Buffer>e__FixedBuffer_1024<T>
{
    private T _e0;
    private T _e1;
    // _e2 ... _e1023
    private T _e1024;

    public ref T this[int index] => ref (uint)index <= 1024u ?
                                         ref RefAdd<T>(ref _e0, index):
                                         throw new IndexOutOfRange();
}
```

<span data-ttu-id="8ff1b-117">Comme ces mémoires tampons de taille fixe ne nécessitent plus l’utilisation de `fixed`, il est logique d’autoriser tout type d’élément.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-117">Since such fixed-sized buffers no longer require use of `fixed`, it makes sense to allow any element type.</span></span>  

> <span data-ttu-id="8ff1b-118">Remarque : `fixed` sera toujours pris en charge, mais uniquement si le type d’élément est `blittable`</span><span class="sxs-lookup"><span data-stu-id="8ff1b-118">NOTE: `fixed` will still be supported, but only if the element type is `blittable`</span></span>

## <a name="drawbacks"></a><span data-ttu-id="8ff1b-119">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="8ff1b-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

* <span data-ttu-id="8ff1b-120">Il peut y avoir des difficultés avec la compatibilité descendante, mais étant donné que les mémoires tampons de taille fixe existantes fonctionnent uniquement avec une sélection de types primitifs, il est possible que le compilateur continue de fonctionner « juste-à-côte » si l’utilisateur traite la mémoire tampon fixe comme un dirigé.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-120">There could be some challenges with backwards compatibility, but given that the existing fixed-sized buffers only work with a selection of primitive types, it should be possible for the compiler to continue "just-working" if the user treats the fixed-buffer as a pointer.</span></span>
* <span data-ttu-id="8ff1b-121">Les constructions incompatibles peuvent avoir besoin d’utiliser un encodage `v2` légèrement différent pour masquer les champs de l’ancien compilateur.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-121">Incompatible constructs may need to use slightly different `v2` encoding to hide the fields from old compiler.</span></span>
* <span data-ttu-id="8ff1b-122">La compression n’est pas bien définie dans la spécification IL pour les types génériques.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-122">Packing is not well defined in IL spec for generic types.</span></span> <span data-ttu-id="8ff1b-123">Alors que l’approche devrait fonctionner, nous recevrons un comportement non documenté.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-123">While the approach should work, we will be bordering on undocumented behavior.</span></span> <span data-ttu-id="8ff1b-124">Nous devons faire en sorte qu’il soit documenté et que d’autres JIT comme mono aient le même comportement.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-124">We should make that documented and make sure other JITs like Mono have the same behavior.</span></span>
* <span data-ttu-id="8ff1b-125">La spécification d’un type distinct pour chaque longueur (éventuellement un autre pour `readonly` champs, si pris en charge) aura un impact sur les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-125">Specifying a separate type for every length (an possibly another for `readonly` fields, if supported) will have impact on metadata.</span></span> <span data-ttu-id="8ff1b-126">Elle sera liée par le nombre de tableaux de tailles différentes dans l’application donnée.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-126">It will be bound by the number of arrays of different sizes in the given app.</span></span>
* <span data-ttu-id="8ff1b-127">la `ref` mathématique n’est pas formellement vérifiable (car elle n’est pas sûre).</span><span class="sxs-lookup"><span data-stu-id="8ff1b-127">`ref` math is not formally verifiable (since it is unsafe).</span></span> <span data-ttu-id="8ff1b-128">Nous devons trouver un moyen de mettre à jour les règles de vérification pour savoir que notre utilisation est correcte.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-128">We will need to find a way to update verification rules to know that our use is ok.</span></span>

## <a name="alternatives"></a><span data-ttu-id="8ff1b-129">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="8ff1b-129">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="8ff1b-130">Déclarez manuellement vos structures et utilisez du code unsafe pour construire des indexeurs.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-130">Manually declare your structures and use unsafe code to construct indexers.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="8ff1b-131">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="8ff1b-131">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="8ff1b-132">devons-nous autoriser `readonly`?</span><span class="sxs-lookup"><span data-stu-id="8ff1b-132">should we allow `readonly`?</span></span>  <span data-ttu-id="8ff1b-133">(avec l’indexeur ReadOnly)</span><span class="sxs-lookup"><span data-stu-id="8ff1b-133">(with readonly indexer)</span></span>
- <span data-ttu-id="8ff1b-134">devons-nous autoriser les initialiseurs de tableau ?</span><span class="sxs-lookup"><span data-stu-id="8ff1b-134">should we allow array initializers?</span></span>
- <span data-ttu-id="8ff1b-135">le mot clé `fixed` est-il nécessaire ?</span><span class="sxs-lookup"><span data-stu-id="8ff1b-135">is `fixed` keyword necessary?</span></span>
- <span data-ttu-id="8ff1b-136">`foreach`?</span><span class="sxs-lookup"><span data-stu-id="8ff1b-136">`foreach`?</span></span>
- <span data-ttu-id="8ff1b-137">uniquement les champs d’instance dans les structs ?</span><span class="sxs-lookup"><span data-stu-id="8ff1b-137">only instance fields in structs?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="8ff1b-138">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="8ff1b-138">Design meetings</span></span>

<span data-ttu-id="8ff1b-139">Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.</span><span class="sxs-lookup"><span data-stu-id="8ff1b-139">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>