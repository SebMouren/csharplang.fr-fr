---
ms.openlocfilehash: 07b4afe4a3fcbf10c978f05e642dfd8a47d53ea5
ms.sourcegitcommit: 194a043db72b9244f8db45db326cc82de6cec965
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80217201"
---

# <a name="target-typed-new-expressions"></a>Expressions de `new` typées cibles

* [x] proposé
* [x] [prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Récapitulatif
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

- **Tout type struct** (y compris les types Tuple)
- **Tout type référence** (y compris les types délégués)
- **Tout paramètre de type** avec un constructeur ou une contrainte `struct`

avec les exceptions suivantes :

- **Types enum :** tous les types ENUM ne contiennent pas la constante zéro. il est donc préférable d’utiliser le membre enum explicite.
- **Types d’interface :** il s’agit d’une fonctionnalité de niche qui doit être préférable pour mentionner explicitement le type.
- **Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.
- **dynamique :** nous n’autorisons pas `new dynamic()`, donc nous n’autorisons pas `new()` avec `dynamic` comme type de cible.

Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.

Lorsque le type cible est un type valeur Nullable, le `new` de type cible est converti en type sous-jacent au lieu du type Nullable.

> **Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?

Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct). Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a>Divers

`throw new()` n’est pas autorisé.

Le `new` de type cible n’est pas autorisé avec les opérateurs binaires.

Elle n’est pas autorisée lorsqu’il n’y a aucun type à cibler : les opérateurs unaires, la collection d’un `foreach`dans un `using`, dans une déconstruction, dans une expression `await`, en tant que propriété de type anonyme (`new { Prop = new() }`), dans une instruction `lock`, dans une `sizeof`, dans une instruction `fixed`, dans un accès aux membres (`new().field`), dans une opération distribuée dynamiquement (`someDynamic.Method(new())`), dans une requête LINQ, comme opérande de l’opérateur `is`, comme opérande gauche de l’opérateur `??` ,  ...

Il est également interdit en tant que `ref`.

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
