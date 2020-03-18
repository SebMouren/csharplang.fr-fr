---
ms.openlocfilehash: 11e9d21bda9e69be692c5c5f5aee80c2da1894ab
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484621"
---
# <a name="unmanaged-type-constraint"></a>Contrainte de type non managé

## <a name="summary"></a>Résumé
[summary]: #summary

La fonctionnalité de contrainte non managée fournira la mise en œuvre du langage à la classe de types connus sous le nom de C# « types non managés » dans la spécification de langage.  Cela est défini dans la section 18,2 comme un type qui n’est pas un type référence et ne contient pas de champs de type référence à aucun niveau d’imbrication.  

## <a name="motivation"></a>Motivation
[motivation]: #motivation

La motivation principale est de faciliter la création de code d’interopérabilité de bas C#niveau dans. Les types non managés sont l’un des principaux blocs de construction pour le code d’interopérabilité, mais l’absence de prise en charge dans les génériques rend impossible la création de routines réutilisables dans tous les types non managés. Au lieu de cela, les développeurs sont obligés de créer le même code de plaque de chaudière pour chaque type non géré dans leur bibliothèque :

```csharp
int Hash(Point point) { ... } 
int Hash(TimeSpan timeSpan) { ... } 
```

Pour activer ce type de scénario, le langage introduira une nouvelle contrainte : non géré :

```csharp
void Hash<T>(T value) where T : unmanaged
{
    ...
}
```

Cette contrainte ne peut être satisfaite que par les types qui s’intègrent à la définition de C# type non managé dans la spécification de langage. Une autre façon de le regarder est qu’un type correspond à la contrainte non managée IFF. il peut également être utilisé comme pointeur. 

```csharp
Hash(new Point()); // Okay 
Hash(42); // Okay
Hash("hello") // Error: Type string does not satisfy the unmanaged constraint
```

Les paramètres de type avec la contrainte non managée peuvent utiliser toutes les fonctionnalités disponibles pour les types non managés : pointeurs, fixe, etc. 

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

Cette contrainte permettra également d’effectuer des conversions efficaces entre les données structurées et les flux d’octets. Il s’agit d’une opération qui est courante dans les piles de mise en réseau et les couches de sérialisation :

```csharp
Span<byte> Convert<T>(ref T value) where T : unmanaged 
{
    ...
}
```

Ces routines sont avantageuses, car elles sont prouvées de manière prouvée au moment de la compilation et à l’allocation gratuite.  Aujourd’hui, les auteurs d’interopérabilité ne peuvent pas effectuer cette opération (même si elles se trouvent dans une couche où les performances sont critiques).  Au lieu de cela, ils doivent s’appuyer sur l’allocation de routines qui ont des vérifications d’exécution coûteuses pour vérifier que les valeurs sont correctement non gérées.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

La langue introduira une nouvelle contrainte nommée `unmanaged`. Pour satisfaire cette contrainte, un type doit être un struct et tous les champs du type doivent appartenir à l’une des catégories suivantes :

- Le type `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `IntPtr` ou `UIntPtr`.
- N’importe quel type de `enum`.
- Être un type pointeur.
- Il s’agit d’un struct défini par l’utilisateur qui satsifies la contrainte de `unmanaged`.

Les champs d’instance générés par le compilateur, tels que les propriétés implémentées automatiquement, doivent également respecter ces contraintes. 

Par exemple :

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

La contrainte de `unmanaged` ne peut pas être combinée avec `struct`, `class` ou `new()`. Cette restriction découle du fait que `unmanaged` implique `struct` par conséquent, les autres contraintes n’ont pas de sens.

La contrainte de `unmanaged` n’est pas appliquée par le CLR, uniquement par le langage. Pour empêcher la mauvaise utilisation par d’autres langages, les méthodes qui ont cette contrainte sont protégées par une demande mod-req. Cela empêchera d’autres langages d’utiliser des arguments de type qui ne sont pas des types non managés.

Le jeton `unmanaged` dans la contrainte n’est pas un mot clé, ni un mot clé contextuel. Au lieu de cela, cela revient à `var` dans le fait qu’elle est évaluée à cet emplacement et que :

- Liaison à un type défini par l’utilisateur ou à un type référencé nommé `unmanaged`: cette opération est traitée comme toute autre contrainte de type nommé. 
- Lier à aucun type : il sera interprété comme la contrainte de `unmanaged`.

Dans le cas où il existe un type nommé `unmanaged` et qu’il est disponible sans qualification dans le contexte actuel, il n’y aura aucun moyen d’utiliser la contrainte de `unmanaged`. Il en va de même pour les règles entourant le `var` et les types définis par l’utilisateur portant le même nom. 

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Le principal inconvénient de cette fonctionnalité est qu’elle s’occupe d’un petit nombre de développeurs : généralement des auteurs ou des infrastructures de bibliothèque de bas niveau.  C’est pourquoi le temps de langage est précieux pour un petit nombre de développeurs. 

Toutefois, ces infrastructures sont souvent la base de la majorité des applications .NET.  Par conséquent, les performances et l’exactitude des victoires à ce niveau peuvent avoir un effet de ondulation sur l’écosystème .NET.  Cela rend la fonctionnalité intéressante, même avec le public limité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Il existe deux alternatives à prendre en compte :

- État actuel : la fonctionnalité n’est pas justifiée par ses propres avantages et les développeurs continuent d’utiliser le comportement d’adhésion implicite.

## <a name="questions"></a>Questions
[quesions]: #questions

### <a name="metadata-representation"></a>Représentation des métadonnées

Le F# langage encode la contrainte dans le fichier de signature, ce qui C# signifie qu’il ne peut pas réutiliser sa représentation. Un nouvel attribut doit être choisi pour cette contrainte. En outre, une méthode qui a cette contrainte doit être protégée par un mod-req.

### <a name="blittable-vs-unmanaged"></a>Blittable et non géré
Le F# langage a une [fonctionnalité très similaire](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) qui utilise le mot clé non géré. Le nom blittable provient de l’utilisation de Midori.  Souhaiterez peut-être Rechercher la priorité ici et utiliser non managé à la place. 

**Résolution** Le langage décide d’utiliser le non géré 

### <a name="verifier"></a>Vérificateur

Le vérificateur/Runtime doit-il être mis à jour pour comprendre l’utilisation de pointeurs vers des paramètres de type générique ?  Ou peut-il simplement fonctionner en l’absence de modifications ?

**Résolution** Aucune modification n’est nécessaire. Tous les types de pointeurs sont simplement non vérifiables. 

## <a name="design-meetings"></a>Réunions de conception

n/a
