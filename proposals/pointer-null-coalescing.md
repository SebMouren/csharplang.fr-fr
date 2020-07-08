---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074296"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a>Étendre la fusion Null ( ??) et l’affectation de fusion Null ( ?? =) opérateurs aux pointeurs

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : en cours

## <a name="summary"></a>Résumé
[summary]: #summary

Cette proposition étend la prise en charge d’une fonctionnalité couramment utilisée en C# ( ?? et ?? =) en types de code et de pointeur non sécurisés specifcally. 

## <a name="motivation"></a>Motivation
[motivation]: #motivation


Il n’y a aucune raison pour que les types pointeur soient exclus de cette fonctionnalité, car ils sont sémantiquement adaptés au cas d’usage de la fonctionnalité. La prise en charge de cette fonctionnalité étend l’étendue de la fonctionnalité à ce qu’elle attendrait qu’elle prenne en charge logiquement. Ce concept est déjà pris en charge dans les expressions ternaires, par exemple :

 `T *foo = bar != null ? bar : baz;`

Les mêmes options syntaxiques proposées aux types non-pointeur doivent donc être étendues aux types pointeur.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Pour cet ajout au langage, aucune modification grammaticale n’est requise. Nous ajoutons simplement des types pris en charge à un opérateur existant. Les règles de conversion actuelles détermineront alors le type résultant de l’opération ?? expression.
Les autres règles restent les mêmes, à l’exception des règles de type souple qui devront être modifiées.

La spécification C# devra être mise à jour pour refléter cet ajout avec la modification de la ligne.
> Une expression de fusion Null de la forme `a` ?? `b`requiert `a` un type Nullable ou un type référence.

to

> Une expression de fusion Null de la forme `a` ?? `b`requiert `a` un type Nullable, un type référence ou un type pointeur.

ainsi que 
> Si `A` Exists et n’est pas un type Nullable ou un type référence, une erreur de compilation se produit.

to

> Si `A` Exists et n’est pas un type Nullable, un type référence ou un type pointeur, une erreur de compilation se produit.

Actuellement, les règles pour `??=` n’interdisent pas les types de pointeur ; Toutefois, l’opérateur n’a pas été implémenté comme celui-ci.


## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Le code unsafe peut être considéré comme confus et un vecteur pour les bogues qui n’auraient pas lieu autrement. L’amélioration des options syntaxiques disponibles pour les types pointeur peut entraîner des bogues dans lesquels le développeur n’a pas eu connaissance de la signification sémantique de ce qui a été écrit. 

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Aucune autre conception n’a été prise en compte, car il ne s’agit pas d’une nouvelle fonctionnalité, mais il s’agit plutôt d’une extension d’une fonctionnalité déjà implémentée.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Une question possible est si la prise en charge doit être étendue au déréférencement implicite dans la même veine que les conversions implicites pour `Nullable<T>` sont prises en charge.

PAR exemple

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a>Réunions de conception

https://github.com/dotnet/csharplang/issues/418



