---
ms.openlocfilehash: a4b0fbbc600eaf1e705ad8e6bd9fcecb44100761
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484439"
---
# <a name="local-functions"></a>Fonctions locales

Nous étendons C# pour prendre en charge la déclaration de fonctions dans la portée de bloc. Les fonctions locales peuvent utiliser (capturer) des variables de la portée englobante.

Le compilateur utilise l’analyse de Flow pour détecter les variables utilisées par une fonction locale avant de lui assigner une valeur. Chaque appel de la fonction requiert que ces variables soient définitivement assignées. De même, le compilateur détermine les variables qui sont définitivement assignées au retour. Ces variables sont considérées comme définitivement assignées après l’appel de la fonction locale.

Les fonctions locales peuvent être appelées à partir d’un point lexical avant sa définition. Les instructions de déclaration de fonction locale ne génèrent pas d’avertissement lorsqu’elles ne sont pas accessibles.

TODO : _écrire la spécification_

## <a name="syntax-grammar"></a>Grammaire de syntaxe

Cette grammaire est représentée comme une différence par rapport à la grammaire actuelle des spécifications.

```diff
declaration-statement
    : local-variable-declaration ';'
    | local-constant-declaration ';'
+   | local-function-declaration
    ;

+local-function-declaration
+   : local-function-header local-function-body
+   ;

+local-function-header
+   : local-function-modifiers? return-type identifier type-parameter-list?
+       ( formal-parameter-list? ) type-parameter-constraints-clauses
+   ;

+local-function-modifiers
+   : (async | unsafe)
+   ;

+local-function-body
+   : block
+   | arrow-expression-body
+   ;
```

Les fonctions locales peuvent utiliser des variables définies dans la portée englobante. L’implémentation actuelle requiert que chaque variable lue à l’intérieur d’une fonction locale soit assignée de manière définitive, comme si vous exécutiez la fonction locale à son point de définition. En outre, la définition de fonction locale doit avoir été « exécutée » à un point d’utilisation quelconque.

Après avoir expérimenté un peu (par exemple, il n’est pas possible de définir deux fonctions locales mutuellement récursives), nous avons depuis revu la manière dont nous voulons que l’assignation définie fonctionne. La révision (pas encore implémentée) est que toutes les variables locales lues dans une fonction locale doivent être assignées de manière définitive à chaque appel de la fonction locale. C’est en fait plus subtil qu’il ne le semble, et il reste beaucoup de travail à faire fonctionner. Une fois cette opération effectuée, vous pouvez déplacer vos fonctions locales à la fin de son bloc englobant.

Les nouvelles règles d’assignation définie sont incompatibles avec l’inférence du type de retour d’une fonction locale. nous allons donc supprimer la prise en charge de l’inférence du type de retour.

Sauf si vous convertissez une fonction locale en délégué, la capture s’effectue dans des frames qui sont des types valeur. Cela signifie que vous n’avez pas de pression GC sur l’utilisation de fonctions locales avec la capture.

### <a name="reachability"></a>Accessibilité

Nous ajoutons à la spécification

> Le corps d’une expression lambda ou d’une fonction locale de l’instruction est considéré comme accessible.
