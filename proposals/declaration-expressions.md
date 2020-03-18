---
ms.openlocfilehash: d2064ec1637e50962262c9380281abd5e1711402
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484579"
---
# <a name="declaration-expressions"></a>Expressions de déclaration

Prendre en charge les assignations de déclaration en tant qu’expressions.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Autorisez l’initialisation au point de déclaration dans plus de cas, en simplifiant le code et en autorisant l’utilisation de `var`.

```csharp
SpecialType ReferenceType =>
    (var st = _type.SpecialType).IsValueType() ? SpecialType.None : st;
```

Autorisez les déclarations pour les arguments `ref`, de la même façon que `out var`.

```csharp
Convert(source, destination, ref List<Diagnostic> diagnostics = null);
```

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Les expressions sont étendues pour inclure l’assignation de déclaration. La priorité est identique à l’affectation.

```antlr
expression
    : non_assignment_expression
    | assignment
    | declaration_assignment_expression // new
    ;
declaration_assignment_expression // new
    : declaration_expression '=' local_variable_initializer
    ;
declaration_expression // C# 7.0
    | type variable_designation
    ;
```

L’assignation de déclaration n’est pas d’un seul local.

Le type d’une expression d’assignation de déclaration est le type de la déclaration.
Si le type est `var`, le type déduit est le type de l’expression d’initialisation. 

L’expression d’assignation de déclaration peut être une l-value, pour `ref` valeurs d’argument en particulier.

Si l’expression d’assignation de déclaration déclare un type valeur et que l’expression est une valeur r, la valeur de l’expression est une copie.

L’expression d’assignation de déclaration peut déclarer un `ref` local.
Il existe une ambiguïté lorsque `ref` est utilisé pour une expression de déclaration dans un argument `ref`.
L’initialiseur de variable locale détermine si la déclaration est un `ref` local.

```csharp
F(ref int x = IntFunc());    // int x;
F(ref int y = RefIntFunc()); // ref int y;
```

La portée des variables locales déclarées dans les expressions d’assignation de déclaration est identique à celle des expressions de déclaration correspondantes de C # 7.0.

Il s’agit d’une erreur au moment de la compilation pour faire référence à un local dans le texte précédant l’expression de déclaration.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives
Aucune modification. Cette fonctionnalité est simplement abrégée syntaxique après tout.

Autres expressions de séquence générales : consultez [#377](https://github.com/dotnet/csharplang/issues/377).

Pour autoriser l’utilisation de `var` dans d’autres cas, autorisez la déclaration et l’assignation distinctes de `var` variables locales et déduire le type à partir des assignations de tous les chemins de code.

## <a name="see-also"></a>Voir aussi
[see-also]: #see-also
Consultez expression de déclaration de base dans [#595](https://github.com/dotnet/csharplang/issues/595).

Consultez déconstruction d’une déclaration dans la fonctionnalité de [déconstruction](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) .
