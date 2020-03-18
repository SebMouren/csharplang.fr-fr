---
ms.openlocfilehash: 6f05bbc1365e59d737103b586db9d4a242c6d306
ms.sourcegitcommit: e9afb74cc1abd56db93b4b50bd5e6765e27c1c5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "79485083"
---
# <a name="static-lambdas"></a>Expressions lambda statiques

## <a name="summary"></a>Résumé

Prennent en charge les expressions lambda qui interdisent la capture de l’état de la portée englobante.

## <a name="motivation"></a>Motivation

Évitez de capturer de manière involontaire un État dans le contexte englobant.

## <a name="detailed-design"></a>Conception détaillée

Les expressions lambda avec `static` ne peuvent pas capturer l’État à partir de la portée englobante.
Par conséquent, les variables locales, les paramètres et les `this` de la portée englobante ne sont pas disponibles dans une `static` lambda.

Une `static` lambda ne peut pas faire référence à des membres d’instance à partir d’une référence `this` ou `base` implicite ou explicite.

Une `static` lambda peut faire référence à des membres `static` à partir de la portée englobante.

Une `static` lambda peut faire référence à des définitions de `constant` à partir de la portée englobante.

`nameof()` dans une expression lambda `static` peut faire référence à des variables locales, des paramètres ou des `this` ou `base` à partir de la portée englobante.

Les règles d’accessibilité pour les membres `private` dans la portée englobante sont les mêmes pour les expressions lambda `static` et non-`static`.

Aucune garantie n’est faite pour déterminer si une `static` définition lambda est émise en tant que méthode de `static` dans les métadonnées. Il s’agit de l’implémentation du compilateur à optimiser.

Une fonction locale non`static` ou une expression lambda peut capturer l’État à partir d’un `static` lambda englobant, mais ne peut pas capturer l’État en dehors du `static` lambda englobant.

Un `static` lambda peut être utilisé dans une arborescence de l’expression.

La suppression du modificateur `static` d’une expression lambda dans un programme valide ne modifie pas la signification du programme.
