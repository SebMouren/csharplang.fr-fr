---
ms.openlocfilehash: 340a1dc5a2eac653458d7d29f74551e5fe28b6d5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484558"
---
# <a name="operators-should-be-exposed-for-systemintptr-and-systemuintptr"></a>Les opérateurs doivent être exposés pour `System.IntPtr` et `System.UIntPtr`

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Le CLR prend en charge un ensemble d’opérateurs pour les types `System.IntPtr` et `System.UIntPtr` (`native int`). Ces opérateurs peuvent être consultés dans `III.1.5` de la spécification Common Language Infrastructure (`ECMA-335`). Toutefois, ces opérateurs ne sont pas pris C#en charge par.

Une prise en charge linguistique doit être fournie pour l’ensemble des opérateurs pris en charge par `System.IntPtr` et `System.UIntPtr`. Ces opérateurs sont les suivants : `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft`, `ShiftRight`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Aujourd’hui, les utilisateurs peuvent facilement C# écrire des applications ciblant plusieurs plateformes à l’aide de différents outils et infrastructures, par exemple : `Xamarin`, `.NET Core`, `Mono`, etc.

Lors de l’écriture de code multiplateforme, il est souvent nécessaire d’écrire du code d’interopérabilité qui interagit avec une plateforme cible particulière d’une manière spécifique. Cela peut inclure l’écriture de code graphique, l’appel d’une API système ou l’interaction avec une bibliothèque native existante.

Ce code d’interopérabilité doit souvent gérer des handles, de la mémoire non managée ou même seulement des entiers dimensionnés spécifiques à la plateforme.

Le Runtime fournit la prise en charge pour cela en définissant un ensemble d’opérateurs qui peuvent être utilisés sur les types primitifs `native int` (`System.IntPtr`) et `native unsigned int` (`System.UIntPtr`).

C#n’a jamais pris en charge ces opérateurs et les utilisateurs doivent donc contourner le problème. Cela augmente souvent la complexité du code et réduit la maintenabilité du code.

Par conséquent, le langage doit commencer à prendre en charge ces opérateurs pour aider à avancer le langage afin de mieux prendre en charge ces exigences.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Le jeu complet d’opérateurs pris en charge est défini dans `III.1.5` de la spécification Common Language Infrastructure (`ECMA-335`). La spécification est disponible ici : [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).

* Un résumé des opérateurs est fourni ci-dessous pour des raisons pratiques.
* Les opérateurs non vérifiables définis par les spécifications de l’interface CLI ne sont pas répertoriés et ne font pas partie de cette offre (bien qu’il soit également utile de les envisager).
* Le fait de fournir un mot clé (par exemple `nint` et `nuint`) ou de fournir un moyen de déclarer des littéraux pour les `System.IntPtr` et les `System.UIntPtr` (tels que 0n) ne fait pas partie de cette proposition (même s’il peut être utile de les considérer également).

### <a name="unary-plus-operator"></a>Opérateur plus unaire

```csharp
System.IntPtr operator +(System.IntPtr)
```

```csharp
System.UIntPtr operator +(System.UIntPtr)
```

### <a name="unary-minus-operator"></a>Opérateur moins unaire

```csharp
System.IntPtr operator -(System.IntPtr)
```

### <a name="bitwise-complement-operator"></a>Opérateur de complément de bits

```csharp
System.IntPtr operator ~(System.IntPtr)
```

```csharp
System.UIntPtr operator ~(System.UIntPtr)
```

### <a name="cast-operators"></a>Opérateurs de cast

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

### <a name="multiplication-operator"></a>Multiplication (opérateur)

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

### <a name="division-operator"></a>Opérateur de division

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

### <a name="remainder-operator"></a>Remainder (opérateur)

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

### <a name="addition-operator"></a>Opérateur d’addition

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

### <a name="subtraction-operator"></a>Soustraction (opérateur)

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

### <a name="shift-operators"></a>Opérateurs de décalage

```csharp
System.IntPtr operator <<(System.IntPtr, int)
System.IntPtr operator >>(System.IntPtr, int)
```

```csharp
System.UIntPtr operator <<(System.UIntPtr, int)
System.UIntPtr operator >>(System.UIntPtr, int)
```

### <a name="integer-comparison-operators"></a>Opérateurs de comparaison d’entiers

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

### <a name="integer-logical-operators"></a>Opérateurs logiques Integer

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

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

L’utilisation réelle de ces opérateurs peut être limitée aux utilisateurs finaux qui écrivent des bibliothèques de niveau inférieur ou du code d’interopérabilité. La plupart des utilisateurs finaux utiliseront probablement ces bibliothèques de niveau inférieur elles-mêmes, ce qui aurait pour effet d’extraire les entiers de taille native, les handles et le code d’interopérabilité. Par conséquent, ils n’auraient pas besoin des opérateurs eux-mêmes.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Faire en sorte que l’infrastructure implémente les opérateurs requis en les écrivant directement dans IL. En outre, le runtime peut fournir une prise en charge intrinsèque pour les opérateurs définis par l’infrastructure, afin de mieux optimiser les performances de fin.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Quelles sont les parties de la conception toujours TBD ?

## <a name="design-meetings"></a>Réunions de conception

Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.