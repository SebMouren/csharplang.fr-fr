---
ms.openlocfilehash: 2532a24e867930d2f27614f19c77585dbce80dfa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484544"
---
# <a name="throw-expression"></a>Expression Throw

Nous étendons l’ensemble de formulaires d’expression à inclure

```antlr
throw_expression
    : 'throw' null_coalescing_expression
    ;

null_coalescing_expression
    : throw_expression
    ;
```

Les règles de type sont les suivantes :

- Un *throw_expression* n’a aucun type.
- Une *throw_expression* est convertible à chaque type par une conversion implicite.

Une *expression Throw* lève la valeur produite en évaluant l' *null_coalescing_expression*, qui doit indiquer une valeur du type de classe `System.Exception`, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de type qui a `System.Exception` (ou une sous-classe de ce type) comme classe de base effective. Si l’évaluation de l’expression produit `null`, un `System.NullReferenceException` est levé à la place.

Le comportement au moment de l’exécution de l’évaluation d’une *expression Throw* est le même [que celui spécifié pour une *instruction throw*](../../spec/statements.md#the-throw-statement).

Les règles d’analyse de flow sont les suivantes :

- Pour chaque variable *v*, *v* est définitivement assignée avant la *null_coalescing_expression* d’un *throw_expression* IFF elle est définitivement assignée avant le *throw_expression*.
- Pour chaque variable *v*, *v* est définitivement assignée après *throw_expression*.

Une *expression Throw* n’est autorisée que dans les contextes syntaxiques suivants :
- En tant que deuxième ou troisième opérande d’un opérateur conditionnel ternaire `?:`
- Comme second opérande d’un opérateur de fusion Null `??`
- En tant que corps d’une expression lambda ou d’une méthode.
