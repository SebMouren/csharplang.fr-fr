---
ms.openlocfilehash: f238a711e710bbac7f5b7400fa938bd85dec00c6
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485251"
---
# <a name="support-for--and--on-tuple-types"></a>Prise en charge de = = et ! = sur les types de Tuple

Autorisez les expressions `t1 == t2` où `t1` et `t2` sont des types de tuple tuple ou Nullable de même cardinalité, et évaluez-les approximativement comme `temp1.Item1 == temp2.Item1 && temp1.Item2 == temp2.Item2` (en supposant `var temp1 = t1; var temp2 = t2;`).

À l’inverse, cela permettrait `t1 != t2` et de l’évaluer comme `temp1.Item1 != temp2.Item1 || temp1.Item2 != temp2.Item2`.

Dans le cas Nullable, des vérifications supplémentaires pour `temp1.HasValue` et `temp2.HasValue` sont utilisées. Par exemple, `nullableT1 == nullableT2` est évalué comme `temp1.HasValue == temp2.HasValue ? (temp1.HasValue ? ... : true) : false`.

Quand une comparaison d’éléments retourne un résultat non bool (par exemple, lorsqu’un `operator ==` ou `operator !=` non booléen défini par l’utilisateur est utilisé, ou dans une comparaison dynamique), ce résultat est converti en `bool` ou exécuté via `operator true` ou `operator false` pour obtenir une `bool`. La comparaison de tuples finit toujours par retourner un `bool`.

À partir C# de 7,2, ce code génère une erreur (`error CS0019: Operator '==' cannot be applied to operands of type '(...)' and '(...)'`), sauf s’il existe une `operator==`définie par l’utilisateur.

## <a name="details"></a>Détails

Lors de la liaison de l’opérateur `==` (ou `!=`), les règles existantes sont : (1) cas dynamique, (2) la résolution de surcharge et (3) échouent.
Cette proposition ajoute un cas de tuple entre (1) et (2) : si les deux opérandes d’un opérateur de comparaison sont des tuples (ayant des types de tuple ou des littéraux de Tuple) et une cardinalité correspondante, la comparaison est effectuée par élément. Cette égalité de tuples est également levée sur des tuples Nullable.

Les deux opérandes (et, dans le cas des littéraux de tuple, leurs éléments) sont évalués dans l’ordre de gauche à droite. Chaque paire d’éléments est ensuite utilisée comme opérande pour lier l’opérateur `==` (ou `!=`), de manière récursive. Tous les éléments avec le type au moment de la compilation `dynamic` provoquent une erreur. Les résultats de ces comparaisons au niveau des éléments sont utilisés comme opérandes dans une chaîne d’opérateurs conditionnels AND (ou OR).

Par exemple, dans le contexte d' `(int, (int, int)) t1, t2;`, `t1 == (1, (2, 3))` évaluerait comme `temp1.Item1 == temp2.Item1 && temp1.Item2.Item1 == temp2.Item2.Item1 && temp2.Item2.Item2 == temp2.Item2.Item2`.

Lorsqu’un littéral de tuple est utilisé en tant qu’opérande (de chaque côté), il reçoit un type de tuple converti formé par les conversions d’éléments qui sont introduites lors de la liaison de l’opérateur `==` (ou `!=`) au niveau de l’élément. 

Par exemple, dans `(1L, 2, "hello") == (1, 2L, null)`, le type converti pour les littéraux de tuple est `(long, long, string)` et le deuxième littéral n’a pas de type naturel.


### <a name="deconstruction-and-conversions-to-tuple"></a>Déconstruction et conversions en Tuple
Dans `(a, b) == x`, le fait que `x` pouvez décomposer en deux éléments ne joue pas un rôle. Cela peut se présenter dans une prochaine proposition, bien qu’elle soulève des questions sur `x == y` (est-ce une comparaison simple ou une comparaison d’éléments, et si vous utilisez la cardinalité ?).
De même, les conversions en tuple ne jouent aucun rôle.

### <a name="tuple-element-names"></a>Noms d’éléments de Tuple

Lors de la conversion d’un littéral de tuple, nous avertissons quand un nom d’élément de tuple explicite a été fourni dans le littéral, mais il ne correspond pas au nom d’élément de tuple cible.
Nous utilisons la même règle dans la comparaison des tuples, afin de supposer `(int a, int b) t` nous avertissons sur `d` dans `t == (c, d: 0)`.

### <a name="non-bool-element-wise-comparison-results"></a>Résultats de la comparaison non-bool au niveau des éléments

Si une comparaison au niveau des éléments est dynamique dans une égalité de tuples, nous utilisons un appel dynamique de l’opérateur `false` et annulons cela pour obtenir une `bool` et poursuivre avec d’autres comparaisons au niveau des éléments. 

Si une comparaison au niveau des éléments retourne un autre type non booléen dans une égalité de tuples, il y a deux cas :
- Si le type non bool convertit en `bool`, nous appliquons cette conversion,
- s’il n’existe aucune conversion de ce type, mais que le type a un opérateur `false`, nous l’utiliserons et nie le résultat.

Dans une inégalité de tuple, les mêmes règles s’appliquent, sauf que nous allons utiliser l’opérateur `true` (sans négation) au lieu de l’opérateur `false`.

Ces règles sont similaires aux règles impliquées dans l’utilisation d’un type non booléen dans une instruction `if` et d’autres contextes existants.

## <a name="evaluation-order-and-special-cases"></a>Ordre d’évaluation et cas spéciaux
La valeur de gauche est évaluée en premier, puis la valeur de droite, puis les comparaisons au niveau des éléments de gauche à droite (y compris les conversions et avec la sortie précoce en fonction des règles existantes pour les opérateurs conditionnels et/ou).

Par exemple, s’il existe une conversion du type `A` en type `B` et une méthode `(A, A) GetTuple()`, l’évaluation de `(new A(1), (new B(2), new B(3))) == (new B(4), GetTuple())` signifie :
- `new A(1)`
- `new B(2)`
- `new B(3)`
- `new B(4)`
- `GetTuple()`
- Ensuite, les conversions et les comparaisons de type élément et la logique conditionnelle sont évaluées (convertissez `new A(1)` en type `B`, puis comparez-les à `new B(4)`, etc.).

### <a name="comparing-null-to-null"></a>Comparaison de `null` à `null`

Il s’agit d’un cas spécial de comparaisons régulières, qui effectue des comparaisons de tuples. La comparaison `null == null` est autorisée, et les littéraux de `null` n’obtiennent aucun type.
Dans l’égalité des tuples, cela signifie que `(0, null) == (0, null)` est également autorisé et que les littéraux de `null` et de tuple n’obtiennent pas non plus de type.

### <a name="comparing-a-nullable-struct-to-null-without-operator"></a>Comparaison d’un struct Nullable à `null` sans `operator==`

Il s’agit d’un autre cas spécial issu de comparaisons régulières, qui effectue des comparaisons de tuples.
Si vous avez un `struct S` sans `operator==`, la comparaison `(S?)x == null` est autorisée et est interprétée comme `((S?).x).HasValue`.
Dans l’égalité des tuples, la même règle est appliquée, donc `(0, (S?)x) == (0, null)` est autorisé.

## <a name="compatibility"></a>Compatibilité

Si quelqu’un a écrit ses propres `ValueTuple` types avec une implémentation de l’opérateur de comparaison, il aurait été précédemment récupéré par la résolution de surcharge. Toutefois, étant donné que le nouveau cas de tuple vient avant la résolution de surcharge, nous traiterons ce cas avec la comparaison de tuples au lieu de compter sur la comparaison définie par l’utilisateur.

----

Se rapporte aux [opérateurs relationnels et de test de type liés](../../spec/expressions.md#relational-and-type-testing-operators) à [#190](https://github.com/dotnet/csharplang/issues/190)
