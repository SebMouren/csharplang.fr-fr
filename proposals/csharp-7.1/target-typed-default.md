---
ms.openlocfilehash: 1852356b830e29c3537a4de559fef32fd2c2f8b6
ms.sourcegitcommit: f7952cdddf85316a4beec493a0ecc14fcb3241c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "79484985"
---
# <a name="target-typed-default-literal"></a>Littéral « par défaut » de type cible

* [x] proposé
* [x] prototype
* [x] implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

La fonctionnalité de `default` de type cible est une variante plus concise de l’opérateur `default(T)`, qui permet d’omettre le type. Son type est déduit par la cible-typage à la place. À part cela, il se comporte comme `default(T)`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

La motivation principale est d’éviter de taper des informations redondantes.

Par exemple, lors de l’appel de `void Method(ImmutableArray<SomeType> array)`, le littéral *par défaut* autorise `M(default)` à la place de `M(default(ImmutableArray<SomeType>))`.

Cela s’applique dans un certain nombre de scénarios, tels que :

- Déclaration de variables locales (`ImmutableArray<SomeType> x = default;`)
- opérations ternaires (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)
- retour dans les méthodes et les expressions lambda (`return default;`)
- Déclaration des valeurs par défaut pour les paramètres facultatifs (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)
- inclusion de valeurs par défaut dans les expressions de création de tableau (`var x = new[] { default, ImmutableArray.Create(y) };`)


## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Une nouvelle expression est introduite, le littéral *par défaut* . Une expression avec cette classification peut être convertie implicitement en n’importe quel type, par une *conversion de littéral par défaut*. 

L’inférence du type pour le littéral *par défaut* fonctionne de la même façon que pour le littéral *null* , sauf qu’un type est autorisé (pas seulement des types référence).

Cette conversion produit la valeur par défaut du type inféré.

Le littéral *par défaut* peut avoir une valeur de constante, en fonction du type inféré. `const int x = default;` est donc légal, mais `const int? y = default;` ne l’est pas.

Le littéral *par défaut* peut être l’opérande des opérateurs d’égalité, tant que l’autre opérande a un type. Ainsi `default == x` et `x == default` sont des expressions valides, mais `default == default` n’est pas conforme.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Un inconvénient mineur est que le littéral *par défaut* peut être utilisé à la place du littéral *null* dans la plupart des contextes. Deux des exceptions sont `throw null;` et `null == null`, qui sont autorisées pour le littéral *null* , mais pas le littéral *par défaut* .

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Il existe deux alternatives à prendre en compte :

- État actuel : la fonctionnalité n’est pas justifiée par ses propres avantages et les développeurs continuent à utiliser l’opérateur par défaut avec un type explicite.
- Extension du littéral null : il s’agit de l’approche VB avec `Nothing`. Nous pourrions autoriser `int x = null;`.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [x] la *valeur par défaut* doit être autorisée comme opérande des opérateurs *is* ou *As* ? Réponse : interdire `default is T`, autoriser `x is default`, autoriser les `default as RefType` (avec un avertissement Always null)

## <a name="design-meetings"></a>Réunions de conception

- [LDM 3/7/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-07.md)
- [LDM 3/28/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-28.md)
- [LDM 5/31/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md#default-in-operators)
