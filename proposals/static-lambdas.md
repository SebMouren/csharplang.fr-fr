---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345196"
---
# <a name="static-lambdas"></a>Expressions lambda statiques

## <a name="summary"></a>Résumé

Prennent en charge les expressions lambda qui interdisent la capture de l’état de la portée englobante.

## <a name="motivation"></a>Motivation

Évitez de capturer de manière involontaire un État dans le contexte englobant.

## <a name="detailed-design"></a>Conception détaillée

Les expressions lambda avec `static` ne peuvent pas capturer l’État à partir de la portée englobante.
Par conséquent, les variables locales, les paramètres et `this` de la portée englobante ne sont pas disponibles dans une `static` expression lambda.

Une `static` expression lambda ne peut pas faire référence à des membres d’instance à partir d’une référence implicite ou explicite `this` ou `base` .

Une `static` expression lambda peut faire référence `static` à des membres de la portée englobante.

Une `static` expression lambda peut référencer `constant` des définitions de la portée englobante.

`nameof()`dans une `static` expression lambda, peut faire référence à des variables locales, des paramètres ou `this` ou `base` à partir de la portée englobante.

Les règles d’accessibilité pour les `private` membres de la portée englobante sont les mêmes pour `static` et non- `static` lambdas.

Aucune garantie n’est faite pour déterminer si une `static` définition lambda est émise comme une `static` méthode dans les métadonnées. Il s’agit de l’implémentation du compilateur à optimiser.

Une `static` fonction ou une expression lambda non locale peut capturer l’État à partir d’un lambda englobant `static` , mais ne peut pas capturer l’État en dehors de l' `static` expression lambda englobante.

La suppression du `static` modificateur d’une expression lambda dans un programme valide ne modifie pas la signification du programme.
