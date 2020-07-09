---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157196"
---
# <a name="static-anonymous-functions"></a>Fonctions anonymes statiques

## <a name="summary"></a>Résumé

Autorise un modificateur’static’sur les expressions lambda et les méthodes anonymes, ce qui interdit la capture de paramètres régionaux ou de l’état de l’instance de contenant des étendues.

## <a name="motivation"></a>Motivation

Évitez de capturer de manière involontaire un État dans le contexte englobant, ce qui peut entraîner une rétention inattendue des objets capturés ou des allocations supplémentaires inattendues.

## <a name="detailed-design"></a>Conception détaillée

Une méthode lambda ou anonyme peut avoir un `static` modificateur. Le `static` modificateur indique que la méthode lambda ou anonyme est une *fonction anonyme statique*.

Une *fonction anonyme statique* ne peut pas capturer l’État à partir de la portée englobante.
Par conséquent, les variables locales, les paramètres et `this` de la portée englobante ne sont pas disponibles dans une *fonction anonyme statique*.

Une *fonction anonyme statique* ne peut pas faire référence à des membres d’instance à partir d’une référence implicite ou explicite `this` ou `base` .

Une *fonction anonyme statique* peut faire référence `static` à des membres de la portée englobante.

Une *fonction anonyme statique* peut référencer `constant` des définitions de la portée englobante.

`nameof()`dans une *fonction anonyme statique* peut faire référence à des variables locales, des paramètres ou `this` ou `base` à partir de la portée englobante.

Les règles d’accessibilité des `private` membres de la portée englobante sont les mêmes pour les `static` fonctions et non `static` anonymes.

Aucune garantie n’est faite pour déterminer si une définition de *fonction anonyme statique* est émise comme une `static` méthode dans les métadonnées. Il s’agit de l’implémentation du compilateur à optimiser.

Une fonction non `static` locale ou une fonction anonyme peut capturer l’État à partir d’une *fonction anonyme statique* englobante, mais ne peut pas capturer l’État en dehors de la *fonction anonyme statique*englobante.

La suppression du `static` modificateur d’une fonction anonyme dans un programme valide ne modifie pas la signification du programme.
