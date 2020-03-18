---
ms.openlocfilehash: 3d10cacef98e802333c8cbe65edb93c19c74cabf
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484495"
---
# <a name="null-conditional-await"></a>NULL-attente conditionnelle

* [x] proposé
* [] Prototype : aucun
* [] Implémentation : aucune
* [] Spécification : démarrée, en dessous

## <a name="summary"></a>Résumé
[summary]: #summary

Prend en charge une expression de la forme `await? e`, qui attend `e` si la valeur n’est pas null, sinon, elle génère `null`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il s’agit d’un modèle de codage courant, qui aurait une bonne synergie avec les opérateurs de propagation de valeurs NULL et de fusion de null existants.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Nous ajoutons une nouvelle forme de la *await_expression*:

```antlr
await_expression
    : 'await' '?' unary_expression
    ;
```

L’opérateur de `await` conditionnel null attend son opérande uniquement si cet opérande n’est pas null. Sinon, le résultat de l’application de l’opérateur est null.

Le type du résultat est calculé à l’aide des [règles pour l’opérateur conditionnel null](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).

> **Remarque :** Si `e` est de type `Task`, `await? e;` ne fait rien si `e` est `null`, et await `e` s’il n’est pas `null`.
>
> Si `e` est de type `Task<K>` où `K` est un type valeur, `await? e` générerait une valeur de type `K?`.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le C# corps des programmes qui tireraient parti de la fonctionnalité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Bien qu’il nécessite un code réutilisable, les utilisations de cet opérateur peuvent souvent être remplacées par une expression comme `(e == null) ? null : await e` ou une instruction comme `if (e != null) await e`.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Requiert la revue LDM

## <a name="design-meetings"></a>Réunions de conception

Aucun.
