---
ms.openlocfilehash: f000dda7eeb1c4f17c26f94c326a12a9d0014288
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281968"
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

## <a name="specification"></a>Caractéristique
[design]: #detailed-design

Une nouvelle forme syntaxique, *target_typed_new* de la *object_creation_expression* est acceptée dans laquelle le *type* est facultatif.

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    | target_typed_new
    ;
target_typed_new
    : 'new' '(' argument_list? ')' object_or_collection_initializer?
    ;
```

Une expression *target_typed_new* n’a pas de type. Toutefois, il existe une nouvelle *conversion de création d’objet* qui est une conversion implicite à partir d’une expression, qui existe d’un *target_typed_new* à chaque type.

Étant donné un type cible `T`, le type `T0` est le type sous-jacent de `T`si `T` est une instance de `System.Nullable`. Sinon `T0` est `T`. La signification d’une expression *target_typed_new* qui est convertie en type `T` est identique à la signification d’un *object_creation_expression* correspondant qui spécifie `T0` comme type.

Il s’agit d’une erreur au moment de la compilation si un *target_typed_new* est utilisé comme opérande d’un opérateur unaire ou binaire, ou s’il est utilisé là où il n’est pas soumis à une *conversion de création d’objet*.

> **Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?

Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct). Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // OK; same as (0, 0)
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a>Divers

Les conséquences de la spécification sont les suivantes :

- `throw new()` est autorisé (le type de cible est `System.Exception`)
- Le `new` de type cible n’est pas autorisé avec les opérateurs binaires.
- Elle n’est pas autorisée lorsqu’il n’y a aucun type à cibler : les opérateurs unaires, la collection d’un `foreach`dans un `using`, dans une déconstruction, dans une expression `await`, en tant que propriété de type anonyme (`new { Prop = new() }`), dans une instruction `lock`, dans une `sizeof`, dans une instruction `fixed`, dans un accès aux membres (`new().field`), dans une opération distribuée dynamiquement (`someDynamic.Method(new())`), dans une requête LINQ, comme opérande de l’opérateur `is`, comme opérande gauche de l’opérateur `??` ,  ...
- Il est également interdit en tant que `ref`.
- Les genres de types suivants ne sont pas autorisés en tant que cibles de la conversion
  - **Types enum :** `new()` fonctionnera (comme `new Enum()` fonctionne pour attribuer la valeur par défaut), mais `new(1)` ne fonctionnera pas car les types enum n’ont pas de constructeur.
  - **Types d’interface :** Cela fonctionnerait de la même façon que l’expression de création correspondante pour les types COM.
  - **Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.    
  - **dynamique :** nous n’autorisons pas `new dynamic()`, donc nous n’autorisons pas `new()` avec `dynamic` comme type de cible.
  - **tuples :** Celles-ci ont la même signification que la création d’un objet à l’aide du type sous-jacent.
  - Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.   

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Il y avait des problèmes avec les `new` de type cible qui créent des catégories de modifications avec rupture, mais nous avons déjà cela avec `null` et `default`, et cela n’a pas été un problème significatif.

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
- [LDM-2018-05-21](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [LDM-2018-06-25](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [LDM-2018-08-22](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [LDM-2018-10-17](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
- [LDM-2020-03-25](https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md)
