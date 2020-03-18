---
ms.openlocfilehash: c1a77d9337ecd16f5ea1c30d18f6422552b0dfb2
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484600"
---
# <a name="ref-local-reassignment"></a>Réaffectation locale de Ref

Dans C# 7,3, nous ajoutons la prise en charge de la reliaison du référent d’une variable locale ref ou d’un paramètre ref.

Nous ajoutons ce qui suit au jeu de `assignment_operator`s.

```antlr
assignment_operator
    : '=' 'ref'
    ;
```

L’opérateur `=ref` est appelé ***opérateur d’assignation de référence***. Il ne s’agit pas d’un *opérateur d’assignation composée*. L’opérande gauche doit être une expression qui crée une liaison à une variable locale REF, à un paramètre ref (autre que `this`) ou à un paramètre out. L’opérande de droite doit être une expression qui produit une lvalue désignant une valeur du même type que l’opérande gauche.

L’opérande de droite doit être définitivement assigné au point de l’assignation de référence.

Lorsque l’opérande gauche est lié à un paramètre `out`, il s’agit d’une erreur si ce `out` paramètre n’a pas été définitivement assigné au début de l’opérateur d’assignation de référence.

Si l’opérande de gauche est une référence accessible en écriture (c’est-à-dire qu’elle désigne tout autre chose qu’une `ref readonly` paramètre local ou `in`), l’opérande de droite doit être une lvalue accessible en écriture.

L’opérateur d’assignation de référence produit une lvalue du type assigné. Il peut être écrit si l’opérande de gauche est accessible en écriture (c’est-à-dire non `ref readonly` ou `in`).

Les règles de sécurité pour cet opérateur sont les suivantes :

- Pour une réassignation de référence `e1 = ref e2`, le niveau de sécurité de la *référence à l’échappement* de l' `e2` doit être au moins aussi vaste que la portée de la *référence* de `e1`.

Où *ref-Safe-to-Escape* est défini en [sécurité pour les types](../csharp-7.2/span-safety.md) de type Ref
