---
ms.openlocfilehash: 2070cf3b3269585055791adc3427cbd134df444d
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484656"
---
# <a name="pattern-based-fixed-statement"></a><span data-ttu-id="7b3f7-101">Instruction `fixed` basée sur un modèle</span><span class="sxs-lookup"><span data-stu-id="7b3f7-101">Pattern-based `fixed` statement</span></span>

## <a name="summary"></a><span data-ttu-id="7b3f7-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="7b3f7-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="7b3f7-103">Introduisez un modèle qui permettrait aux types de participer aux instructions `fixed`.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-103">Introduce a pattern that would allow types to participate in `fixed` statements.</span></span> 

## <a name="motivation"></a><span data-ttu-id="7b3f7-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="7b3f7-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="7b3f7-105">Le langage fournit un mécanisme permettant d’épingler les données managées et d’obtenir un pointeur natif vers la mémoire tampon sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-105">The language provides a mechanism for pinning managed data and obtain a native pointer to the underlying buffer.</span></span>

```csharp
fixed(byte* ptr = byteArray)
{
   // ptr is a native pointer to the first element of the array
   // byteArray is protected from being moved/collected by the GC for the duration of this block 
}

```

<span data-ttu-id="7b3f7-106">L’ensemble de types qui peut participer à `fixed` est codé en dur et limité aux tableaux et aux `System.String`.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-106">The set of types that can participate in `fixed` is hardcoded and limited to arrays and `System.String`.</span></span> <span data-ttu-id="7b3f7-107">Les types « spéciaux » en dur ne sont pas mis à l’échelle quand de nouvelles primitives telles que `ImmutableArray<T>`, `Span<T>``Utf8String` sont introduites.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-107">Hardcoding "special" types does not scale when new primitives such as `ImmutableArray<T>`, `Span<T>`, `Utf8String` are introduced.</span></span> 

<span data-ttu-id="7b3f7-108">En outre, la solution actuelle pour `System.String` s’appuie sur une API assez rigide.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-108">In addition, the current solution for `System.String` relies on a fairly rigid API.</span></span> <span data-ttu-id="7b3f7-109">La forme de l’API implique que `System.String` est un objet contigu qui incorpore des données encodées en UTF16 à un offset fixe à partir de l’en-tête de l’objet.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-109">The shape of the API implies that `System.String` is a contiguous object that embeds UTF16 encoded data at a fixed offset from the object header.</span></span> <span data-ttu-id="7b3f7-110">Une telle approche a été trouvée problématique dans plusieurs propositions qui pourraient nécessiter des modifications de la disposition sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-110">Such approach has been found problematic in several proposals that could require changes to the underlying layout.</span></span> <span data-ttu-id="7b3f7-111">Il serait souhaitable de pouvoir basculer vers une solution plus souple qui découple `System.String` objet de sa représentation interne à des fins d’interopérabilité non managée.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-111">It would be desirable to be able to switch to something more flexible that decouples `System.String` object from its internal representation for the purpose of unmanaged interop.</span></span> 

## <a name="detailed-design"></a><span data-ttu-id="7b3f7-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="7b3f7-112">Detailed design</span></span>
[design]: #detailed-design

## <a name="pattern"></a><span data-ttu-id="7b3f7-113">*Modèle*</span><span class="sxs-lookup"><span data-stu-id="7b3f7-113">*Pattern*</span></span> ##
<span data-ttu-id="7b3f7-114">Un modèle viable, basé sur un modèle, A besoin d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7b3f7-114">A viable pattern-based “fixed” need to:</span></span>
-   <span data-ttu-id="7b3f7-115">Fournissez les références managées pour épingler l’instance et initialiser le pointeur (de préférence, il s’agit de la même référence)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-115">Provide the managed references to pin the instance and to initialize the pointer (preferably this is the same reference)</span></span>
-   <span data-ttu-id="7b3f7-116">Transmettent sans ambiguïté le type de l’élément non managé (par exemple, « char » pour « String »)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-116">Convey unambiguously the type of the unmanaged element   (i.e. “char” for “string”)</span></span>
-   <span data-ttu-id="7b3f7-117">Prescrire le comportement dans le cas « vide » quand il n’y a rien à référencer.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-117">Prescribe the behavior in "empty" case when there is nothing to refer to.</span></span> 
-   <span data-ttu-id="7b3f7-118">Ne doit pas pousser les auteurs d’API vers des décisions de conception qui ont un impact sur l’utilisation du type en dehors de `fixed`.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-118">Should not push API authors toward design decisions that hurt the use of the type outside of `fixed`.</span></span>

<span data-ttu-id="7b3f7-119">Je pense que ce qui précède peut être respecté en reconnaissant un membre de retour de référence spécialement nommé : `ref [readonly] T GetPinnableReference()`.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-119">I think the above could be satisfied by recognizing a specially named ref-returning member: `ref [readonly] T GetPinnableReference()`.</span></span>

<span data-ttu-id="7b3f7-120">Pour pouvoir être utilisé par l’instruction `fixed`, les conditions suivantes doivent être remplies :</span><span class="sxs-lookup"><span data-stu-id="7b3f7-120">In order to be used by the `fixed` statement the following conditions must be met:</span></span>

1. <span data-ttu-id="7b3f7-121">Il n’y a qu’un seul membre de ce type fourni pour un type.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-121">There is only one such member provided for a type.</span></span>
1. <span data-ttu-id="7b3f7-122">Retourne par `ref` ou `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-122">Returns by `ref` or `ref readonly`.</span></span> <span data-ttu-id="7b3f7-123">(`readonly` est autorisé afin que les auteurs de types immuables/ReadOnly puissent implémenter le modèle sans ajouter une API inscriptible pouvant être utilisée dans du code sécurisé)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-123">(`readonly` is permitted so that authors of immutable/readonly types could implement the pattern without adding writeable API that could be used in safe code)</span></span>
1. <span data-ttu-id="7b3f7-124">T est un type non managé.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-124">T is an unmanaged type.</span></span>
<span data-ttu-id="7b3f7-125">(étant donné que `T*` devient le type pointeur.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-125">(since `T*` becomes the pointer type.</span></span> <span data-ttu-id="7b3f7-126">La restriction se développe naturellement si/lorsque la notion de « non géré » est développée)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-126">The restriction will naturally expand if/when the notion of "unmanaged" is expanded)</span></span>
1. <span data-ttu-id="7b3f7-127">Retourne les `nullptr` managés lorsqu’il n’y a aucune donnée à épingler, ce qui est probablement le moyen le plus économique pour transmettre des éléments de vidage.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-127">Returns managed `nullptr` when there is no data to pin – probably the cheapest way to convey emptiness.</span></span>
<span data-ttu-id="7b3f7-128">(Notez que « » la chaîne retourne une référence à «\ 0 », car les chaînes se terminent par un caractère null)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-128">(note that “” string returns a ref to '\0' since strings are null-terminated)</span></span>

<span data-ttu-id="7b3f7-129">Pour le `#3`, nous pouvons également autoriser le résultat dans les cas vides à être non défini ou spécifique à l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-129">Alternatively for the `#3` we can allow the result in empty cases be undefined or implementation-specific.</span></span> <span data-ttu-id="7b3f7-130">Cela peut toutefois rendre l’API plus dangereuse et sujette à des abus et à des charges de compatibilité involontaires.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-130">That, however, may make the API more dangerous and prone to abuse and unintended compatibility burdens.</span></span> 

## <a name="translation"></a><span data-ttu-id="7b3f7-131">*Traduction*</span><span class="sxs-lookup"><span data-stu-id="7b3f7-131">*Translation*</span></span> ##

```csharp
fixed(byte* ptr = thing)
{ 
    // <BODY>
}
```

<span data-ttu-id="7b3f7-132">devient le pseudo-code suivant (pas tous C#les éléments exprimables dans)</span><span class="sxs-lookup"><span data-stu-id="7b3f7-132">becomes the following pseudocode (not all expressible in C#)</span></span>

```csharp
byte* ptr;
// specially decorated "pinned" IL local slot, not visible to user code.
pinned ref byte _pinned;

try
{
    // NOTE: null check is omitted for value types 
    // NOTE: `thing` is evaluated only once (temporary is introduced if necessary) 
    if (thing != null)
    {
        // obtain and "pin" the reference
        _pinned = ref thing.GetPinnableReference();

        // unsafe cast in IL
        ptr = (byte*)_pinned;
    }
    else
    {
        ptr = default(byte*);
    }

    // <BODY> 
}
finally   // finally can be omitted when not observable
{
    // "unpin" the object
    _pinned = nullptr;
}
```

## <a name="drawbacks"></a><span data-ttu-id="7b3f7-133">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="7b3f7-133">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="7b3f7-134">GetPinnableReference est destiné à être utilisé uniquement dans `fixed`, mais rien n’empêche son utilisation dans du code sécurisé. par conséquent, l’implémenteur doit garder cela à l’esprit.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-134">GetPinnableReference is intended to be used only in `fixed`, but nothing prevents its use in safe code, so implementor must keep that in mind.</span></span>

## <a name="alternatives"></a><span data-ttu-id="7b3f7-135">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="7b3f7-135">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="7b3f7-136">Les utilisateurs peuvent introduire GetPinnableReference ou un membre similaire et l’utiliser en tant que</span><span class="sxs-lookup"><span data-stu-id="7b3f7-136">Users can introduce GetPinnableReference or similar member and use it as</span></span>
 
```csharp
fixed(byte* ptr = thing.GetPinnableReference())
{ 
    // <BODY>
}
```

<span data-ttu-id="7b3f7-137">Il n’existe aucune solution pour `System.String` si une autre solution est souhaitée.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-137">There is no solution for `System.String` if alternative solution is desired.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="7b3f7-138">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="7b3f7-138">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="7b3f7-139">[] Comportement dans l’état « vide ».</span><span class="sxs-lookup"><span data-stu-id="7b3f7-139">[ ] Behavior in "empty" state.</span></span><span data-ttu-id="7b3f7-140"> - `nullptr` ou `undefined` ?</span><span class="sxs-lookup"><span data-stu-id="7b3f7-140"> - `nullptr` or `undefined` ?</span></span> 
- <span data-ttu-id="7b3f7-141">[] Les méthodes d’extension doivent-elles être prises en compte ?</span><span class="sxs-lookup"><span data-stu-id="7b3f7-141">[ ] Should the extension methods be considered ?</span></span> 
- <span data-ttu-id="7b3f7-142">[] Si un modèle est détecté sur `System.String`, dois-je le gagner ?</span><span class="sxs-lookup"><span data-stu-id="7b3f7-142">[ ] If a pattern is detected on `System.String`, should it win over ?</span></span> 

## <a name="design-meetings"></a><span data-ttu-id="7b3f7-143">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="7b3f7-143">Design meetings</span></span>

<span data-ttu-id="7b3f7-144">Aucun.</span><span class="sxs-lookup"><span data-stu-id="7b3f7-144">None yet.</span></span> 
