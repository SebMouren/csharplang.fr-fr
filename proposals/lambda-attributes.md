---
ms.openlocfilehash: 9647fff40a1e45bef917f140612ae4e91abea958
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484572"
---
# <a name="lambda-attributes"></a>Attributs lambda

* [x] proposé
* [] Prototype
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

Autorisez l’application d’attributs aux expressions lambda (et aux méthodes anonymes) et aux paramètres de méthode lambda/anonyme, comme ils le peuvent sur les méthodes normales.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Deux motivations principales :

1. Pour fournir des métadonnées visibles aux analyseurs au moment de la compilation.
2. Pour fournir des métadonnées visibles pour la réflexion et les outils au moment de l’exécution.

En guise d’exemple de (1) : pour le code qui respecte les performances, il est utile de pouvoir avoir un analyseur qui signale quand des fermetures et des délégués sont alloués pour les expressions lambda qui se ferment sur l’État.  Souvent, un développeur de ce type de code sortira de sa méthode pour éviter la capture d’un État, de sorte que le compilateur puisse générer une méthode statique et un délégué pouvant être mis en cache pour la méthode, ou le développeur s’assurera que le seul état fermé est `this`, ce qui permet au compilateur d’éviter d’allouer un objet de fermeture.  Toutefois, sans prise en charge linguistique pour limiter ce qui peut être capturé, il est très facile de fermer accidentellement l’État.  Il serait utile si un développeur pouvait annoter des expressions lambda avec des attributs pour indiquer l’État sur lequel ils sont autorisés à se fermer, par exemple :

```csharp
[CaptureNone] // can't close over any instance state
[CaptureThis] // can only capture `this` and no other instance state
[CaptureAny] // can close over any instance state
```

Un analyseur peut ensuite être écrit pour signaler quand l’État est capturé de manière incorrecte, par exemple :

```csharp
var results = collection.Select([CaptureNone](i) => Process(item)); // Analyzer error: [CaptureNone] lambdas captures `this`
...
private U Process(T item) { ... }
```

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

- À l’aide de la même syntaxe d’attribut que sur les méthodes normales, les attributs peuvent être appliqués au début d’une méthode lambda ou anonyme, par exemple :

```csharp
[SomeAttribute(...)] () => { ... }
[SomeAttribute(...)] delegate (int i) { ... }
```

- Pour éviter toute ambiguïté quant au fait qu’un attribut s’applique à la méthode lambda ou à l’un des arguments, les attributs ne peuvent être utilisés que lorsque parenthèses sont utilisés autour d’un argument, par exemple :

```csharp
[SomeAttribute] i => { ... } // ERROR
[SomeAttribute] (i) => { ... } // Ok
[SomeAttribute] (int i) => { ... } // Ok
```

- Avec les méthodes anonymes, les parenthèses ne sont pas nécessaires pour appliquer un attribut à la méthode avant le mot clé `delegate`, par exemple :

```csharp
[SomeAttribute] delegate { ... } // Ok
[SomeAttribute] delegate (int i) => { ... } // Ok
```

- Plusieurs attributs peuvent être appliqués, par le biais d’une syntaxe standard délimitée par des virgules ou par le biais d’une syntaxe d’attribut complet, par exemple :

```csharp
[FirstAttribute, SecondAttribute] (i) => { ... } // Ok
[FirstAttribute] [SecondAttribute] (i) => { .... } // Ok
```

- Les attributs peuvent être appliqués aux paramètres d’une méthode anonyme ou d’une expression lambda, mais uniquement quand les parenthèses sont utilisés autour des arguments, par exemple :

```csharp
[SomeAttribute] i => { ... } // ERROR
([SomeAttribute] i) => { .... } // Ok
([SomeAttribute] int i) => { ... } // Ok
([SomeAttribute] i, [SomeOtherAttribute] j) => { ... } // Ok
```

- Plusieurs attributs peuvent être appliqués aux paramètres d’une méthode anonyme ou d’une expression lambda, à l’aide de la syntaxe à attributs ou délimités par des virgules, par exemple :

```csharp
([FirstAttribute, SecondAttribute] i) => { ... } // Ok
([FirstAttribute] [SecondAttribute] i) => { ... } // Ok
```

- les attributs ciblés `return`peuvent également être utilisés sur les expressions lambda, par exemple :

```csharp
([return: SomeAttribute] (i) => { ... }) // Ok
```

- Le compilateur génère les attributs sur la méthode générée et les arguments de ces méthodes comme c’est le cas pour toute autre méthode.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

n/a

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

n/a

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

n/a

## <a name="design-meetings"></a>Réunions de conception

n/a