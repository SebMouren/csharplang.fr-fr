---
ms.openlocfilehash: 4e2a536bab00859b003e8d967cb1927a99a9fa21
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484537"
---

# <a name="target-typed-new-expressions"></a>Expressions de `new` typées cibles

* [x] proposé
* [x] [prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

Ne requièrent pas de spécification de type pour les constructeurs lorsque le type est connu. 

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Autorisez l’initialisation des champs sans dupliquer le type.
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```
Autoriser l’omission du type lorsqu’il peut être déduit de l’utilisation.
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```
Instanciez un objet sans épeler le type.
```cs
private readonly static object s_syncObj = new();
```
## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

La syntaxe de *object_creation_expression* est modifiée pour rendre le *type* facultatif quand les parenthèses sont présentes. Cela est nécessaire pour résoudre l’ambiguïté avec *anonymous_object_creation_expression*.
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```
Un `new` typé cible peut être converti en n’importe quel type. Par conséquent, il ne contribue pas à la résolution de surcharge. Cela permet essentiellement d’éviter les modifications importantes non prévisibles.

La liste d’arguments et les expressions de l’initialiseur sont liées une fois que le type est déterminé.

Le type de l’expression est déduit à partir du type cible qui doit être l’un des éléments suivants :

- **Tout type struct**
- **Tout type référence**
- **Tout paramètre de type** avec un constructeur ou une contrainte `struct`

avec les exceptions suivantes :

- **Types enum :** tous les types ENUM ne contiennent pas la constante zéro. il est donc préférable d’utiliser le membre enum explicite.
- **Types d’interface :** il s’agit d’une fonctionnalité de niche qui doit être préférable pour mentionner explicitement le type.
- **Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.
- **Constructeur par défaut de struct**: cette règle élimine tous les types primitifs et la plupart des types valeur. Si vous souhaitez utiliser la valeur par défaut de ces types, vous pouvez écrire `default` à la place.

Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.

> **Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?

Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct). Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found

var x = new() == (1, 2); // ruled out by "use of struct default constructor"
var x = new(1, 2) == (1, 2) // "new" is redundant
```


> **Problème d’ouverture :** devons-nous autoriser `throw new()` avec `Exception` comme type cible ?

Nous avons `throw null` aujourd’hui, mais pas `throw default` (même si cela aurait le même effet). En revanche, `throw new()` peut être utile en tant que raccourci pour `throw new Exception(...)`. Notez qu’il est déjà autorisé par la spécification actuelle. `Exception` est un type référence, et la spécification de l’instruction throw indique que l’expression est convertie en `Exception`.

> **Problème d’ouverture :** devons-nous autoriser les utilisations d’un `new` de type cible avec des opérateurs arithmétiques et de comparaison définis par l’utilisateur ?

À des fins de comparaison, `default` prend en charge uniquement les opérateurs d’égalité (définis par l’utilisateur et intégrés). Serait-il judicieux de prendre en charge d’autres opérateurs aussi bien pour `new()` ?

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Aucun.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

La plupart des plaintes sur les types trop longs pour être dupliqués dans l’initialisation de champ concerne les *arguments de type* qui ne sont pas le type lui-même, mais nous pourrions déduire uniquement les arguments de type comme `new Dictionary(...)` (ou similaire) et déduire les arguments de type localement à partir des arguments ou de l’initialiseur de collection.

## <a name="questions"></a>Questions
[questions]: #questions

- Devons-nous interdire les utilisations dans les arborescences d’expressions ? º
- Comment la fonctionnalité interagit avec `dynamic` arguments ? (aucun traitement spécial)
- Comment IntelliSense doit fonctionner avec `new()`? (uniquement s’il existe un seul type cible)
## <a name="design-meetings"></a>Réunions de conception

- [LDM-2017-10-18](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
