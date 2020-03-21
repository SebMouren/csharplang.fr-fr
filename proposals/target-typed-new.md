---
ms.openlocfilehash: 38740069a2e105f920fa275c443f4560055e2901
ms.sourcegitcommit: 9aa177443b83116fe1be2ab28e2c7291947fe32d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80108366"
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

### Miscellaneous

`throw new()` is disallowed.

Target-typed `new` is not allowed with binary operators.

It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...

It is also disallowed as a `ref`.

## Drawbacks
[drawbacks]: #drawbacks

There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.

## Alternatives
[alternatives]: #alternatives

Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.

## Questions
[questions]: #questions

- Should we forbid usages in expression trees? (no)
- How the feature interacts with `dynamic` arguments? (no special treatment)
- How IntelliSense should work with `new()`? (only when there is a single target-type)

## Design meetings

- [LDM-2017-10-18](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [LDM-2018-05-21](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [LDM-2018-06-25](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [LDM-2018-08-22](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [LDM-2018-10-17](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
