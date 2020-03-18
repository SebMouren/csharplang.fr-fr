---
ms.openlocfilehash: 11e9d21bda9e69be692c5c5f5aee80c2da1894ab
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484621"
---
# <a name="unmanaged-type-constraint"></a><span data-ttu-id="6c6b3-101">Contrainte de type non managé</span><span class="sxs-lookup"><span data-stu-id="6c6b3-101">Unmanaged type constraint</span></span>

## <a name="summary"></a><span data-ttu-id="6c6b3-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="6c6b3-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="6c6b3-103">La fonctionnalité de contrainte non managée fournira la mise en œuvre du langage à la classe de types connus sous le nom de C# « types non managés » dans la spécification de langage.  Cela est défini dans la section 18,2 comme un type qui n’est pas un type référence et ne contient pas de champs de type référence à aucun niveau d’imbrication.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-103">The unmanaged constraint feature will give language enforcement to the class of types known as "unmanaged types" in the C# language spec.  This is defined in section 18.2 as a type which is not a reference type and doesn't contain reference type fields at any level of nesting.</span></span>  

## <a name="motivation"></a><span data-ttu-id="6c6b3-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="6c6b3-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="6c6b3-105">La motivation principale est de faciliter la création de code d’interopérabilité de bas C#niveau dans.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-105">The primary motivation is to make it easier to author low level interop code in C#.</span></span> <span data-ttu-id="6c6b3-106">Les types non managés sont l’un des principaux blocs de construction pour le code d’interopérabilité, mais l’absence de prise en charge dans les génériques rend impossible la création de routines réutilisables dans tous les types non managés.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-106">Unmanaged types are one of the core building blocks for interop code, yet the lack of support in generics makes it impossible to create re-usable routines across all unmanaged types.</span></span> <span data-ttu-id="6c6b3-107">Au lieu de cela, les développeurs sont obligés de créer le même code de plaque de chaudière pour chaque type non géré dans leur bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-107">Instead developers are forced to author the same boiler plate code for every unmanaged type in their library:</span></span>

```csharp
int Hash(Point point) { ... } 
int Hash(TimeSpan timeSpan) { ... } 
```

<span data-ttu-id="6c6b3-108">Pour activer ce type de scénario, le langage introduira une nouvelle contrainte : non géré :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-108">To enable this type of scenario the language will be introducing a new constraint: unmanaged:</span></span>

```csharp
void Hash<T>(T value) where T : unmanaged
{
    ...
}
```

<span data-ttu-id="6c6b3-109">Cette contrainte ne peut être satisfaite que par les types qui s’intègrent à la définition de C# type non managé dans la spécification de langage. Une autre façon de le regarder est qu’un type correspond à la contrainte non managée IFF. il peut également être utilisé comme pointeur.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-109">This constraint can only be met by types which fit into the unmanaged type definition in the C# language spec. Another way of looking at it is that a type satisfies the unmanaged constraint iff it can also be used as a pointer.</span></span> 

```csharp
Hash(new Point()); // Okay 
Hash(42); // Okay
Hash("hello") // Error: Type string does not satisfy the unmanaged constraint
```

<span data-ttu-id="6c6b3-110">Les paramètres de type avec la contrainte non managée peuvent utiliser toutes les fonctionnalités disponibles pour les types non managés : pointeurs, fixe, etc.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-110">Type parameters with the unmanaged constraint can use all the features available to unmanaged types: pointers, fixed, etc ...</span></span> 

```csharp
void Hash<T>(T value) where T : unmanaged
{
    // Okay
    fixed (T* p = &value) 
    { 
        ...
    }
}
```

<span data-ttu-id="6c6b3-111">Cette contrainte permettra également d’effectuer des conversions efficaces entre les données structurées et les flux d’octets.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-111">This constraint will also make it possible to have efficient conversions between structured data and streams of bytes.</span></span> <span data-ttu-id="6c6b3-112">Il s’agit d’une opération qui est courante dans les piles de mise en réseau et les couches de sérialisation :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-112">This is an operation that is common in networking stacks and serialization layers:</span></span>

```csharp
Span<byte> Convert<T>(ref T value) where T : unmanaged 
{
    ...
}
```

<span data-ttu-id="6c6b3-113">Ces routines sont avantageuses, car elles sont prouvées de manière prouvée au moment de la compilation et à l’allocation gratuite.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-113">Such routines are advantageous because they are provably safe at compile time and allocation free.</span></span>  <span data-ttu-id="6c6b3-114">Aujourd’hui, les auteurs d’interopérabilité ne peuvent pas effectuer cette opération (même si elles se trouvent dans une couche où les performances sont critiques).</span><span class="sxs-lookup"><span data-stu-id="6c6b3-114">Interop authors today can not do this (even though it's at a layer where perf is critical).</span></span>  <span data-ttu-id="6c6b3-115">Au lieu de cela, ils doivent s’appuyer sur l’allocation de routines qui ont des vérifications d’exécution coûteuses pour vérifier que les valeurs sont correctement non gérées.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-115">Instead they need to rely on allocating routines that have expensive runtime checks to verify values are correctly unmanaged.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="6c6b3-116">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="6c6b3-116">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="6c6b3-117">La langue introduira une nouvelle contrainte nommée `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-117">The language will introduce a new constraint named `unmanaged`.</span></span> <span data-ttu-id="6c6b3-118">Pour satisfaire cette contrainte, un type doit être un struct et tous les champs du type doivent appartenir à l’une des catégories suivantes :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-118">In order to satisfy this constraint a type must be a struct and all the fields of the type must fall into one of the following categories:</span></span>

- <span data-ttu-id="6c6b3-119">Le type `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `IntPtr` ou `UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-119">Have the type `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `IntPtr` or `UIntPtr`.</span></span>
- <span data-ttu-id="6c6b3-120">N’importe quel type de `enum`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-120">Be any `enum` type.</span></span>
- <span data-ttu-id="6c6b3-121">Être un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-121">Be a pointer type.</span></span>
- <span data-ttu-id="6c6b3-122">Il s’agit d’un struct défini par l’utilisateur qui satsifies la contrainte de `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-122">Be a user defined struct that satsifies the `unmanaged` constraint.</span></span>

<span data-ttu-id="6c6b3-123">Les champs d’instance générés par le compilateur, tels que les propriétés implémentées automatiquement, doivent également respecter ces contraintes.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-123">Compiler generated instance fields, such as those backing auto-implemented properties, must also meet these constraints.</span></span> 

<span data-ttu-id="6c6b3-124">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-124">For example:</span></span>

```csharp
// Unmanaged type
struct Point 
{ 
    int X;
    int Y {get; set;}
}

// Not an unmanaged type
struct Student 
{ 
    string FirstName;
    string LastName;
}
``` 

<span data-ttu-id="6c6b3-125">La contrainte de `unmanaged` ne peut pas être combinée avec `struct`, `class` ou `new()`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-125">The `unmanaged` constraint cannot be combined with `struct`, `class` or `new()`.</span></span> <span data-ttu-id="6c6b3-126">Cette restriction découle du fait que `unmanaged` implique `struct` par conséquent, les autres contraintes n’ont pas de sens.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-126">This restriction derives from the fact that `unmanaged` implies `struct` hence the other constraints do not make sense.</span></span>

<span data-ttu-id="6c6b3-127">La contrainte de `unmanaged` n’est pas appliquée par le CLR, uniquement par le langage.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-127">The `unmanaged` constraint is not enforced by CLR, only by the language.</span></span> <span data-ttu-id="6c6b3-128">Pour empêcher la mauvaise utilisation par d’autres langages, les méthodes qui ont cette contrainte sont protégées par une demande mod-req. Cela empêchera d’autres langages d’utiliser des arguments de type qui ne sont pas des types non managés.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-128">To prevent mis-use by other languages, methods which have this constraint will be protected by a mod-req. This will prevent other languages from using type arguments which are not unmanaged types.</span></span>

<span data-ttu-id="6c6b3-129">Le jeton `unmanaged` dans la contrainte n’est pas un mot clé, ni un mot clé contextuel.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-129">The token `unmanaged` in the constraint is not a keyword, nor a contextual keyword.</span></span> <span data-ttu-id="6c6b3-130">Au lieu de cela, cela revient à `var` dans le fait qu’elle est évaluée à cet emplacement et que :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-130">Instead it is like `var` in that it is evaluated at that location and will either:</span></span>

- <span data-ttu-id="6c6b3-131">Liaison à un type défini par l’utilisateur ou à un type référencé nommé `unmanaged`: cette opération est traitée comme toute autre contrainte de type nommé.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-131">Bind to user defined or referenced type named `unmanaged`: This will be treated just as any other named type constraint is treated.</span></span> 
- <span data-ttu-id="6c6b3-132">Lier à aucun type : il sera interprété comme la contrainte de `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-132">Bind to no type: This will be interpreted as the `unmanaged` constraint.</span></span>

<span data-ttu-id="6c6b3-133">Dans le cas où il existe un type nommé `unmanaged` et qu’il est disponible sans qualification dans le contexte actuel, il n’y aura aucun moyen d’utiliser la contrainte de `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-133">In the case there is a type named `unmanaged` and it is available without qualification in the current context, then there will be no way to use the `unmanaged` constraint.</span></span> <span data-ttu-id="6c6b3-134">Il en va de même pour les règles entourant le `var` et les types définis par l’utilisateur portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-134">This parallels the rules surrounding the feature `var` and user defined types of the same name.</span></span> 

## <a name="drawbacks"></a><span data-ttu-id="6c6b3-135">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="6c6b3-135">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="6c6b3-136">Le principal inconvénient de cette fonctionnalité est qu’elle s’occupe d’un petit nombre de développeurs : généralement des auteurs ou des infrastructures de bibliothèque de bas niveau.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-136">The primary drawback of this feature is that it serves a small number of developers: typically low level library authors or frameworks.</span></span>  <span data-ttu-id="6c6b3-137">C’est pourquoi le temps de langage est précieux pour un petit nombre de développeurs.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-137">Hence it's spending precious language time for a small number of developers.</span></span> 

<span data-ttu-id="6c6b3-138">Toutefois, ces infrastructures sont souvent la base de la majorité des applications .NET.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-138">Yet these frameworks are often the basis for the majority of .NET applications out there.</span></span>  <span data-ttu-id="6c6b3-139">Par conséquent, les performances et l’exactitude des victoires à ce niveau peuvent avoir un effet de ondulation sur l’écosystème .NET.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-139">Hence performance / correctness wins at this level can have a ripple effect on the .NET ecosystem.</span></span>  <span data-ttu-id="6c6b3-140">Cela rend la fonctionnalité intéressante, même avec le public limité.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-140">This makes the feature worth considering even with the limited audience.</span></span>

## <a name="alternatives"></a><span data-ttu-id="6c6b3-141">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="6c6b3-141">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="6c6b3-142">Il existe deux alternatives à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="6c6b3-142">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="6c6b3-143">État actuel : la fonctionnalité n’est pas justifiée par ses propres avantages et les développeurs continuent d’utiliser le comportement d’adhésion implicite.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-143">The status quo:  The feature is not justified on its own merits and developers continue to use the implicit opt in behavior.</span></span>

## <a name="questions"></a><span data-ttu-id="6c6b3-144">Questions</span><span class="sxs-lookup"><span data-stu-id="6c6b3-144">Questions</span></span>
[quesions]: #questions

### <a name="metadata-representation"></a><span data-ttu-id="6c6b3-145">Représentation des métadonnées</span><span class="sxs-lookup"><span data-stu-id="6c6b3-145">Metadata Representation</span></span>

<span data-ttu-id="6c6b3-146">Le F# langage encode la contrainte dans le fichier de signature, ce qui C# signifie qu’il ne peut pas réutiliser sa représentation.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-146">The F# language encodes the constraint in the signature file which means C# cannot re-use their representation.</span></span> <span data-ttu-id="6c6b3-147">Un nouvel attribut doit être choisi pour cette contrainte.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-147">A new attribute will need to be chosen for this constraint.</span></span> <span data-ttu-id="6c6b3-148">En outre, une méthode qui a cette contrainte doit être protégée par un mod-req.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-148">Additionally a method which has this constraint must be protected by a mod-req.</span></span>

### <a name="blittable-vs-unmanaged"></a><span data-ttu-id="6c6b3-149">Blittable et non géré</span><span class="sxs-lookup"><span data-stu-id="6c6b3-149">Blittable vs. Unmanaged</span></span>
<span data-ttu-id="6c6b3-150">Le F# langage a une [fonctionnalité très similaire](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) qui utilise le mot clé non géré.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-150">The F# language has a very [similar feature](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) which uses the keyword unmanaged.</span></span> <span data-ttu-id="6c6b3-151">Le nom blittable provient de l’utilisation de Midori.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-151">The blittable name comes from the use in Midori.</span></span>  <span data-ttu-id="6c6b3-152">Souhaiterez peut-être Rechercher la priorité ici et utiliser non managé à la place.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-152">May want to look to precedence here and use unmanaged instead.</span></span> 

<span data-ttu-id="6c6b3-153">**Résolution** Le langage décide d’utiliser le non géré</span><span class="sxs-lookup"><span data-stu-id="6c6b3-153">**Resolution** The language decide to use unmanaged</span></span> 

### <a name="verifier"></a><span data-ttu-id="6c6b3-154">Vérificateur</span><span class="sxs-lookup"><span data-stu-id="6c6b3-154">Verifier</span></span>

<span data-ttu-id="6c6b3-155">Le vérificateur/Runtime doit-il être mis à jour pour comprendre l’utilisation de pointeurs vers des paramètres de type générique ?</span><span class="sxs-lookup"><span data-stu-id="6c6b3-155">Does the verifier / runtime need to be updated to understand the use of pointers to generic type parameters?</span></span>  <span data-ttu-id="6c6b3-156">Ou peut-il simplement fonctionner en l’absence de modifications ?</span><span class="sxs-lookup"><span data-stu-id="6c6b3-156">Or can it simply work as is without changes?</span></span>

<span data-ttu-id="6c6b3-157">**Résolution** Aucune modification n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-157">**Resolution** No changes needed.</span></span> <span data-ttu-id="6c6b3-158">Tous les types de pointeurs sont simplement non vérifiables.</span><span class="sxs-lookup"><span data-stu-id="6c6b3-158">All pointer types are simply unverifiable.</span></span> 

## <a name="design-meetings"></a><span data-ttu-id="6c6b3-159">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="6c6b3-159">Design meetings</span></span>

<span data-ttu-id="6c6b3-160">n/a</span><span class="sxs-lookup"><span data-stu-id="6c6b3-160">n/a</span></span>
