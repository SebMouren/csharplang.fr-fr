---
ms.openlocfilehash: 340a1dc5a2eac653458d7d29f74551e5fe28b6d5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484558"
---
# <a name="operators-should-be-exposed-for-systemintptr-and-systemuintptr"></a><span data-ttu-id="bc1de-101">Les opérateurs doivent être exposés pour `System.IntPtr` et `System.UIntPtr`</span><span class="sxs-lookup"><span data-stu-id="bc1de-101">Operators should be exposed for `System.IntPtr` and `System.UIntPtr`</span></span>

* <span data-ttu-id="bc1de-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="bc1de-102">[x] Proposed</span></span>
* <span data-ttu-id="bc1de-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="bc1de-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="bc1de-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="bc1de-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="bc1de-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="bc1de-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="bc1de-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="bc1de-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="bc1de-107">Le CLR prend en charge un ensemble d’opérateurs pour les types `System.IntPtr` et `System.UIntPtr` (`native int`).</span><span class="sxs-lookup"><span data-stu-id="bc1de-107">The CLR supports a set of operators for the `System.IntPtr` and `System.UIntPtr` types (`native int`).</span></span> <span data-ttu-id="bc1de-108">Ces opérateurs peuvent être consultés dans `III.1.5` de la spécification Common Language Infrastructure (`ECMA-335`).</span><span class="sxs-lookup"><span data-stu-id="bc1de-108">These operators can be seen in `III.1.5` of the Common Language Infrastructure specification (`ECMA-335`).</span></span> <span data-ttu-id="bc1de-109">Toutefois, ces opérateurs ne sont pas pris C#en charge par.</span><span class="sxs-lookup"><span data-stu-id="bc1de-109">However, these operators are not supported by C#.</span></span>

<span data-ttu-id="bc1de-110">Une prise en charge linguistique doit être fournie pour l’ensemble des opérateurs pris en charge par `System.IntPtr` et `System.UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="bc1de-110">Language support should be provided for the full set of operators supported by `System.IntPtr` and `System.UIntPtr`.</span></span> <span data-ttu-id="bc1de-111">Ces opérateurs sont les suivants : `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft`, `ShiftRight`.</span><span class="sxs-lookup"><span data-stu-id="bc1de-111">These operators are: `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft`, `ShiftRight`.</span></span>

## <a name="motivation"></a><span data-ttu-id="bc1de-112">Motivation</span><span class="sxs-lookup"><span data-stu-id="bc1de-112">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="bc1de-113">Aujourd’hui, les utilisateurs peuvent facilement C# écrire des applications ciblant plusieurs plateformes à l’aide de différents outils et infrastructures, par exemple : `Xamarin`, `.NET Core`, `Mono`, etc.</span><span class="sxs-lookup"><span data-stu-id="bc1de-113">Today, users can easily write C# applications targeting multiple platforms using various tools and frameworks, such as: `Xamarin`, `.NET Core`, `Mono`, etc...</span></span>

<span data-ttu-id="bc1de-114">Lors de l’écriture de code multiplateforme, il est souvent nécessaire d’écrire du code d’interopérabilité qui interagit avec une plateforme cible particulière d’une manière spécifique.</span><span class="sxs-lookup"><span data-stu-id="bc1de-114">When writing cross-platform code, it is often necessary to write interop code that interacts with a particular target platform in a specific manner.</span></span> <span data-ttu-id="bc1de-115">Cela peut inclure l’écriture de code graphique, l’appel d’une API système ou l’interaction avec une bibliothèque native existante.</span><span class="sxs-lookup"><span data-stu-id="bc1de-115">This could include writing graphics code, calling some System API, or interacting with an existing native library.</span></span>

<span data-ttu-id="bc1de-116">Ce code d’interopérabilité doit souvent gérer des handles, de la mémoire non managée ou même seulement des entiers dimensionnés spécifiques à la plateforme.</span><span class="sxs-lookup"><span data-stu-id="bc1de-116">This interop code often has to deal with handles, unmanaged memory, or even just platform-specific sized integers.</span></span>

<span data-ttu-id="bc1de-117">Le Runtime fournit la prise en charge pour cela en définissant un ensemble d’opérateurs qui peuvent être utilisés sur les types primitifs `native int` (`System.IntPtr`) et `native unsigned int` (`System.UIntPtr`).</span><span class="sxs-lookup"><span data-stu-id="bc1de-117">The runtime provides support for this by defining a set of operators that can be used on the `native int` (`System.IntPtr`) and `native unsigned int` (`System.UIntPtr`) primitive types.</span></span>

<span data-ttu-id="bc1de-118">C#n’a jamais pris en charge ces opérateurs et les utilisateurs doivent donc contourner le problème.</span><span class="sxs-lookup"><span data-stu-id="bc1de-118">C# has never supported these operators and so users have to work around the issue.</span></span> <span data-ttu-id="bc1de-119">Cela augmente souvent la complexité du code et réduit la maintenabilité du code.</span><span class="sxs-lookup"><span data-stu-id="bc1de-119">This often increases code complexity and lowers code maintainability.</span></span>

<span data-ttu-id="bc1de-120">Par conséquent, le langage doit commencer à prendre en charge ces opérateurs pour aider à avancer le langage afin de mieux prendre en charge ces exigences.</span><span class="sxs-lookup"><span data-stu-id="bc1de-120">As such, the language should begin to support these operators to help advance the language to better support these requirements.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="bc1de-121">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="bc1de-121">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="bc1de-122">Le jeu complet d’opérateurs pris en charge est défini dans `III.1.5` de la spécification Common Language Infrastructure (`ECMA-335`).</span><span class="sxs-lookup"><span data-stu-id="bc1de-122">The full set of operators supported are defined in `III.1.5` of the Common Language Infrastructure specification (`ECMA-335`).</span></span> <span data-ttu-id="bc1de-123">La spécification est disponible ici : [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).</span><span class="sxs-lookup"><span data-stu-id="bc1de-123">The specification is available here: [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).</span></span>

* <span data-ttu-id="bc1de-124">Un résumé des opérateurs est fourni ci-dessous pour des raisons pratiques.</span><span class="sxs-lookup"><span data-stu-id="bc1de-124">A summary of the operators is provided below for convenience.</span></span>
* <span data-ttu-id="bc1de-125">Les opérateurs non vérifiables définis par les spécifications de l’interface CLI ne sont pas répertoriés et ne font pas partie de cette offre (bien qu’il soit également utile de les envisager).</span><span class="sxs-lookup"><span data-stu-id="bc1de-125">The unverifiable operators defined by the CLI spec are not listed and are not currently part of this proposal (although it may be worth considering these as well).</span></span>
* <span data-ttu-id="bc1de-126">Le fait de fournir un mot clé (par exemple `nint` et `nuint`) ou de fournir un moyen de déclarer des littéraux pour les `System.IntPtr` et les `System.UIntPtr` (tels que 0n) ne fait pas partie de cette proposition (même s’il peut être utile de les considérer également).</span><span class="sxs-lookup"><span data-stu-id="bc1de-126">Providing a keyword (such as `nint` and `nuint`) nor providing a way to for literals to be declared for `System.IntPtr` and `System.UIntPtr` (such as 0n) is not part of this proposal (although it may be worth considering these as well).</span></span>

### <a name="unary-plus-operator"></a><span data-ttu-id="bc1de-127">Opérateur plus unaire</span><span class="sxs-lookup"><span data-stu-id="bc1de-127">Unary Plus Operator</span></span>

```csharp
System.IntPtr operator +(System.IntPtr)
```

```csharp
System.UIntPtr operator +(System.UIntPtr)
```

### <a name="unary-minus-operator"></a><span data-ttu-id="bc1de-128">Opérateur moins unaire</span><span class="sxs-lookup"><span data-stu-id="bc1de-128">Unary Minus Operator</span></span>

```csharp
System.IntPtr operator -(System.IntPtr)
```

### <a name="bitwise-complement-operator"></a><span data-ttu-id="bc1de-129">Opérateur de complément de bits</span><span class="sxs-lookup"><span data-stu-id="bc1de-129">Bitwise Complement Operator</span></span>

```csharp
System.IntPtr operator ~(System.IntPtr)
```

```csharp
System.UIntPtr operator ~(System.UIntPtr)
```

### <a name="cast-operators"></a><span data-ttu-id="bc1de-130">Opérateurs de cast</span><span class="sxs-lookup"><span data-stu-id="bc1de-130">Cast Operators</span></span>

```csharp
explicit operator sbyte(System.IntPtr)               // Truncate
explicit operator short(System.IntPtr)               // Truncate
explicit operator int(System.IntPtr)                 // Truncate
explicit operator long(System.IntPtr)                // Sign Extend

explicit operator byte(System.IntPtr)                // Truncate
explicit operator ushort(System.IntPtr)              // Truncate
explicit operator uint(System.IntPtr)                // Truncate
explicit operator ulong(System.IntPtr)               // Zero Extend

explicit operator System.IntPtr(int)                 // Sign Extend
explicit operator System.IntPtr(long)                // Truncate

explicit operator System.IntPtr(uint)                // Sign Extend
explicit operator System.IntPtr(ulong)               // Truncate

explicit operator System.IntPtr(System.IntPtr)
explicit operator System.IntPtr(System.UIntPtr)
```

```csharp
explicit operator sbyte(System.UIntPtr)               // Truncate
explicit operator short(System.UIntPtr)               // Truncate
explicit operator int(System.UIntPtr)                 // Truncate
explicit operator long(System.UIntPtr)                // Sign Extend

explicit operator byte(System.UIntPtr)                // Truncate
explicit operator ushort(System.UIntPtr)              // Truncate
explicit operator uint(System.UIntPtr)                // Truncate
explicit operator ulong(System.UIntPtr)               // Zero Extend

explicit operator System.UIntPtr(int)                 // Zero Extend
explicit operator System.UIntPtr(long)                // Truncate

explicit operator System.UIntPtr(uint)                // Zero Extend
explicit operator System.UIntPtr(ulong)               // Truncate

explicit operator System.UIntPtr(System.IntPtr)
explicit operator System.UIntPtr(System.UIntPtr)
```

### <a name="multiplication-operator"></a><span data-ttu-id="bc1de-131">Multiplication (opérateur)</span><span class="sxs-lookup"><span data-stu-id="bc1de-131">Multiplication Operator</span></span>

```csharp
System.IntPtr operator *(int, System.IntPtr)
System.IntPtr operator *(System.IntPtr, int)
System.IntPtr operator *(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator *(uint, System.UIntPtr)
System.UIntPtr operator *(System.UIntPtr, uint)
System.UIntPtr operator *(System.UIntPtr, System.UIntPtr)
```

### <a name="division-operator"></a><span data-ttu-id="bc1de-132">Opérateur de division</span><span class="sxs-lookup"><span data-stu-id="bc1de-132">Division Operator</span></span>

```csharp
System.IntPtr operator /(int, System.IntPtr)
System.IntPtr operator /(System.IntPtr, int)
System.IntPtr operator /(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator /(uint, System.UIntPtr)
System.UIntPtr operator /(System.UIntPtr, uint)
System.UIntPtr operator /(System.UIntPtr, System.UIntPtr)
```

### <a name="remainder-operator"></a><span data-ttu-id="bc1de-133">Remainder (opérateur)</span><span class="sxs-lookup"><span data-stu-id="bc1de-133">Remainder Operator</span></span>

```csharp
System.IntPtr operator %(int, System.IntPtr)
System.IntPtr operator %(System.IntPtr, int)
System.IntPtr operator %(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator %(uint, System.UIntPtr)
System.UIntPtr operator %(System.UIntPtr, uint)
System.UIntPtr operator %(System.UIntPtr, System.UIntPtr)
```

### <a name="addition-operator"></a><span data-ttu-id="bc1de-134">Opérateur d’addition</span><span class="sxs-lookup"><span data-stu-id="bc1de-134">Addition Operator</span></span>

```csharp
System.IntPtr operator +(int, System.IntPtr)
System.IntPtr operator +(System.IntPtr, int)
System.IntPtr operator +(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator +(uint, System.UIntPtr)
System.UIntPtr operator +(System.UIntPtr, uint)
System.UIntPtr operator +(System.UIntPtr, System.UIntPtr)
```

### <a name="subtraction-operator"></a><span data-ttu-id="bc1de-135">Soustraction (opérateur)</span><span class="sxs-lookup"><span data-stu-id="bc1de-135">Subtraction Operator</span></span>

```csharp
System.IntPtr operator -(int, System.IntPtr)
System.IntPtr operator -(System.IntPtr, int)
System.IntPtr operator -(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator -(uint, System.UIntPtr)
System.UIntPtr operator -(System.UIntPtr, uint)
System.UIntPtr operator -(System.UIntPtr, System.UIntPtr)
```

### <a name="shift-operators"></a><span data-ttu-id="bc1de-136">Opérateurs de décalage</span><span class="sxs-lookup"><span data-stu-id="bc1de-136">Shift Operators</span></span>

```csharp
System.IntPtr operator <<(System.IntPtr, int)
System.IntPtr operator >>(System.IntPtr, int)
```

```csharp
System.UIntPtr operator <<(System.UIntPtr, int)
System.UIntPtr operator >>(System.UIntPtr, int)
```

### <a name="integer-comparison-operators"></a><span data-ttu-id="bc1de-137">Opérateurs de comparaison d’entiers</span><span class="sxs-lookup"><span data-stu-id="bc1de-137">Integer Comparison Operators</span></span>

```csharp
bool operator ==(int, System.IntPtr)
bool operator ==(System.IntPtr, int)
bool operator ==(System.IntPtr, System.IntPtr)

bool operator !=(int, System.IntPtr)
bool operator !=(System.IntPtr, int)
bool operator !=(System.IntPtr, System.IntPtr)

bool operator  <(int, System.IntPtr)
bool operator  <(System.IntPtr, int)
bool operator  <(System.IntPtr, System.IntPtr)

bool operator  >(int, System.IntPtr)
bool operator  >(System.IntPtr, int)
bool operator  >(System.IntPtr, System.IntPtr)

bool operator <=(int, System.IntPtr)
bool operator <=(System.IntPtr, int)
bool operator <=(System.IntPtr, System.IntPtr)

bool operator >=(int, System.IntPtr)
bool operator >=(System.IntPtr, int)
bool operator >=(System.IntPtr, System.IntPtr)
```

```csharp
bool operator ==(uint, System.UIntPtr)
bool operator ==(System.UIntPtr, uint)
bool operator ==(System.UIntPtr, System.UIntPtr)

bool operator !=(uint, System.UIntPtr)
bool operator !=(System.UIntPtr, uint)
bool operator !=(System.UIntPtr, System.UIntPtr)

bool operator  <(uint, System.UIntPtr)
bool operator  <(System.UIntPtr, uint)
bool operator  <(System.UIntPtr, System.UIntPtr)

bool operator  >(uint, System.UIntPtr)
bool operator  >(System.UIntPtr, uint)
bool operator  >(System.UIntPtr, System.UIntPtr)

bool operator <=(uint, System.UIntPtr)
bool operator <=(System.UIntPtr, uint)
bool operator <=(System.UIntPtr, System.UIntPtr)

bool operator >=(uint, System.UIntPtr)
bool operator >=(System.UIntPtr, uint)
bool operator >=(System.UIntPtr, System.UIntPtr)
```

### <a name="integer-logical-operators"></a><span data-ttu-id="bc1de-138">Opérateurs logiques Integer</span><span class="sxs-lookup"><span data-stu-id="bc1de-138">Integer Logical Operators</span></span>

```csharp
System.IntPtr operator &(int, System.IntPtr)
System.IntPtr operator &(System.IntPtr, int)
System.IntPtr operator &(System.IntPtr, System.IntPtr)

System.IntPtr operator |(int, System.IntPtr)
System.IntPtr operator |(System.IntPtr, int)
System.IntPtr operator |(System.IntPtr, System.IntPtr)

System.IntPtr operator ^(int, System.IntPtr)
System.IntPtr operator ^(System.IntPtr, int)
System.IntPtr operator ^(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator &(uint, System.UIntPtr)
System.UIntPtr operator &(System.UIntPtr, uint)
System.UIntPtr operator &(System.UIntPtr, System.UIntPtr)

System.UIntPtr operator |(uint, System.UIntPtr)
System.UIntPtr operator |(System.UIntPtr, uint)
System.UIntPtr operator |(System.UIntPtr, System.UIntPtr)

System.UIntPtr operator ^(uint, System.UIntPtr)
System.UIntPtr operator ^(System.UIntPtr, uint)
System.UIntPtr operator ^(System.UIntPtr, System.UIntPtr)
```

## <a name="drawbacks"></a><span data-ttu-id="bc1de-139">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="bc1de-139">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="bc1de-140">L’utilisation réelle de ces opérateurs peut être limitée aux utilisateurs finaux qui écrivent des bibliothèques de niveau inférieur ou du code d’interopérabilité.</span><span class="sxs-lookup"><span data-stu-id="bc1de-140">The actual use of these operators may be small and limited to end-users who are writing lower level libraries or interop code.</span></span> <span data-ttu-id="bc1de-141">La plupart des utilisateurs finaux utiliseront probablement ces bibliothèques de niveau inférieur elles-mêmes, ce qui aurait pour effet d’extraire les entiers de taille native, les handles et le code d’interopérabilité.</span><span class="sxs-lookup"><span data-stu-id="bc1de-141">Most end-users would likely be consuming these lower level libraries themselves which would have the native sized integers, handles, and interop code abstracted away.</span></span> <span data-ttu-id="bc1de-142">Par conséquent, ils n’auraient pas besoin des opérateurs eux-mêmes.</span><span class="sxs-lookup"><span data-stu-id="bc1de-142">As such, they would not have need of the operators themselves.</span></span>

## <a name="alternatives"></a><span data-ttu-id="bc1de-143">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="bc1de-143">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="bc1de-144">Faire en sorte que l’infrastructure implémente les opérateurs requis en les écrivant directement dans IL.</span><span class="sxs-lookup"><span data-stu-id="bc1de-144">Have the framework implement the required operators by writing them directly in IL.</span></span> <span data-ttu-id="bc1de-145">En outre, le runtime peut fournir une prise en charge intrinsèque pour les opérateurs définis par l’infrastructure, afin de mieux optimiser les performances de fin.</span><span class="sxs-lookup"><span data-stu-id="bc1de-145">Additionally, the runtime could provide intrinsic support for the operators defined by the framework, so as to better optimize the end performance.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="bc1de-146">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="bc1de-146">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="bc1de-147">Quelles sont les parties de la conception toujours TBD ?</span><span class="sxs-lookup"><span data-stu-id="bc1de-147">What parts of the design are still TBD?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="bc1de-148">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="bc1de-148">Design meetings</span></span>

<span data-ttu-id="bc1de-149">Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.</span><span class="sxs-lookup"><span data-stu-id="bc1de-149">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>