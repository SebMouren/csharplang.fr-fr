---
ms.openlocfilehash: 7ea62713416ef82034963aef06f3cb11703342ed
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484607"
---
# <a name="stackalloc-array-initializers"></a>Initialiseurs de tableaux stackalloc

## <a name="summary"></a>Résumé
[summary]: #summary

Autorise l’utilisation de la syntaxe d’initialiseur de tableau avec `stackalloc`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Les éléments des tableaux ordinaires peuvent être initialisés au moment de la création. Il semble raisonnable de l’autoriser dans `stackalloc` cas.

La question de la raison pour laquelle cette syntaxe n’est pas autorisée avec `stackalloc` se produit assez fréquemment.  
Voir, par exemple, [#1112](https://github.com/dotnet/csharplang/issues/1112)

## <a name="detailed-design"></a>Conception détaillée

Vous pouvez créer des tableaux ordinaires à l’aide de la syntaxe suivante :

```csharp
new int[3]
new int[3] { 1, 2, 3 }
new int[] { 1, 2, 3 }
new[] { 1, 2, 3 }
```

Nous devons autoriser la création de tableaux alloués par pile via :  

```csharp
stackalloc int[3]               // currently allowed
stackalloc int[3] { 1, 2, 3 }
stackalloc int[] { 1, 2, 3 }
stackalloc[] { 1, 2, 3 }
```

La sémantique de tous les cas est à peu près identique à celle des tableaux.  
Par exemple : dans le dernier cas, le type d’élément est déduit de l’initialiseur et doit être un type « non managé ».

Remarque : la fonctionnalité n’est pas dépendante de la cible qui est une `Span<T>`. Il est tout aussi applicable dans `T*` cas, donc il ne semble pas raisonnable de le prédicat sur `Span<T>` cas.  

## <a name="translation"></a>Traduction

L’implémentation naïve peut simplement initialiser le tableau juste après la création via une série d’affectations d’éléments.  

De même que dans le cas des tableaux, il peut être possible et souhaitable de détecter les cas où l’ensemble ou la plupart des éléments sont des types blittables et utilisent des techniques plus efficaces en copiant l’état pré-créé de tous les éléments constants. 

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Il s’agit d’une fonctionnalité pratique. Il est possible de ne rien faire.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Réunions de conception

Aucun. 
