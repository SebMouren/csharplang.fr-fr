---
ms.openlocfilehash: fecd5a6c1e0f6c7a7a7beac0e4e60445281c7846
ms.sourcegitcommit: 1b488e76c2c07aafc377bc5e8a7197252c82f425
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "79485097"
---
# <a name="static-local-functions"></a>Fonctions locales statiques

## <a name="summary"></a>Résumé

Prend en charge les fonctions locales qui interdisent la capture de l’État à partir de la portée englobante.

## <a name="motivation"></a>Motivation

Évitez de capturer de manière involontaire un État dans le contexte englobant.
Autorisez l’utilisation de fonctions locales dans les scénarios où une méthode `static` est requise.

## <a name="detailed-design"></a>Conception détaillée

Une fonction locale déclarée `static` ne peut pas capturer l’État à partir de la portée englobante.
Par conséquent, les variables locales, les paramètres et les `this` de la portée englobante ne sont pas disponibles dans une fonction locale `static`.

Une fonction locale `static` ne peut pas faire référence à des membres d’instance à partir d’une référence `this` ou `base` implicite ou explicite.

Une fonction locale `static` peut faire référence à des membres `static` à partir de la portée englobante.

Une fonction locale `static` peut faire référence à des définitions de `constant` à partir de la portée englobante.

`nameof()` dans une fonction locale `static` peut faire référence à des variables locales, des paramètres ou des `this` ou `base` à partir de la portée englobante.

Les règles d’accessibilité pour les membres `private` dans la portée englobante sont les mêmes pour les fonctions locales `static` et non-`static`.

Une définition de fonction locale `static` est émise en tant que méthode `static` dans les métadonnées, même si elle est utilisée uniquement dans un délégué.

Une fonction locale non`static` ou une expression lambda peut capturer l’État à partir d’une fonction locale `static` englobante, mais ne peut pas capturer l’État en dehors de la fonction locale `static` englobante.

Une fonction locale `static` ne peut pas être appelée dans une arborescence de l’expression.

Un appel à une fonction locale est émis en tant que `call` plutôt que `callvirt`, que la fonction locale soit `static`ou non.

Résolution de surcharge d’un appel dans une fonction locale non affectée par le fait que la fonction locale est `static`.

La suppression du modificateur `static` d’une fonction locale dans un programme valide ne modifie pas la signification du programme.

## <a name="design-meetings"></a>Réunions de conception

https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
