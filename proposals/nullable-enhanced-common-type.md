---
ms.openlocfilehash: 032cb8590a0b6e83f8ab6201e10720f1b254c605
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484530"
---
# <a name="nullable-enhanced-common-type"></a>Type commun amélioré avec la valeur null

* [x] proposé
* [] Prototype : aucun
* [] Implémentation : aucune
* [] Spécification : voir ci-dessous

## <a name="summary"></a>Résumé
[summary]: #summary

Il existe une situation dans laquelle les résultats actuels de l’algorithme de type commun sont de type Counter-intuitif et les résultats du programmeur ajoutent ce qui ressemble à une conversion redondante dans le code. Avec cette modification, une expression telle que `condition ? 1 : null` entraînerait une valeur de type `int?`, et une expression telle que `condition ? x : 1.0` où `x` est de type `int?` entraînerait une valeur de type `double?`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il s’agit d’une cause courante de ce qui semble être le programmeur comme un code réutilisable.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Nous modifions la spécification permettant de [trouver le meilleur type commun d’un ensemble d’expressions](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) pour affecter les situations suivantes :

- Si une expression est d’un type valeur n’acceptant pas les valeurs NULL `T` et que l’autre est un littéral null, le résultat est de type `T?`.
- Si une expression est d’un type valeur Nullable `T?` et que l’autre est d’un type valeur `U`, et qu’il existe une conversion implicite de `T` en `U`, le résultat est de type `U?`.

Cela devrait affecter les aspects suivants du langage :

- l' [expression ternaire](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)
- [expression de création de tableau](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions) implicitement typé
- déduction du [type de retour d’une expression lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) pour l’inférence de type
- les cas impliquant des génériques, tels que l’appel de `M<T>(T a, T b)` en tant que `M(1, null)`.

Plus précisément, nous modifions les sections suivantes de la spécification (insertions en gras, suppressions dans le texte barré) :

> #### <a name="output-type-inferences"></a>Inférences de type de sortie
> 
> Une *inférence de type de sortie* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :
> 
> *  Si `E` est une fonction anonyme avec un type de retour déduit `U`[(type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou un type d’arborescence d’expression avec le type de retour `Tb`, une *inférence à la limite inférieure* ([inférences à liaison inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.
> *  Sinon, si `E` est un groupe de méthodes et que `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `T1...Tk` et `Tb`de type de retour, et que la résolution de surcharge de `E` avec les types `T1...Tk` produit une méthode unique avec le type de retour `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Tb`.
> *  \* * Sinon, si `E` est une expression avec un type de valeur Nullable `U?`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T` et une *limite null* est ajoutée à `T`. **
> *  Sinon, si `E` est une expression de type `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T`.
> *  **Sinon, si `E` est une expression constante avec la valeur `null`, une *limite null* est ajoutée à `T`** 
> *  Dans le cas contraire, aucune inférence n’est effectuée.

> #### <a name="fixing"></a>Résolution
> 
> Une variable de type non *fixe* `Xi` avec un ensemble de limites est *fixe* comme suit :
> 
> *  L’ensemble de *types candidats* `Uj` commence par le jeu de tous les types dans le jeu de limites de `Xi`.
> *  Nous examinons ensuite chaque limite pour `Xi` à son tour : pour chaque `U` à liaison exacte de `Xi` tous les types `Uj` qui ne sont pas identiques à `U` sont supprimés du jeu de candidats. Pour chaque `U` de limite inférieure de `Xi` tous les types `Uj` pour lesquels il n’existe *pas* de conversion implicite de `U` sont supprimés du jeu de candidats. Pour chaque `U` de la limite supérieure de `Xi` tous les types `Uj` à partir duquel il n’existe *pas* de conversion implicite en `U` sont supprimés du jeu de candidats.
> *  Si parmi les types candidats restants `Uj` il existe un type unique `V` à partir duquel il existe une conversion implicite vers tous les autres types candidats, ~~`Xi` est fixé à `V`.~~
>     -  **Si `V` est un type valeur et qu’il existe une *limite null* pour `Xi`, `Xi` est fixe à `V?`**
>     -  **Sinon `Xi` est fixe à `V`**
> *  Sinon, l’inférence de type échoue.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Il peut y avoir des incompatibilités introduites par cette proposition.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Aucun.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Quel est le niveau de gravité de l’incompatibilité introduit par cette proposition, le cas échéant, et comment peut-il être modéré ?

## <a name="design-meetings"></a>Réunions de conception

Aucun.
