---
ms.openlocfilehash: 25e95b3ab8c384a7e66e59a7f9422cc9699074d7
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484719"
---
# <a name="infer-tuple-names-aka-tuple-projection-initializers"></a>Déduire les noms de tuples (alias. initialiseurs de projection de Tuple)

## <a name="summary"></a>Résumé
[summary]: #summary

Dans un certain nombre de cas courants, cette fonctionnalité permet d’omettre les noms d’éléments de tuple et de les déduire. Par exemple, au lieu de taper `(f1: x.f1, f2: x?.f2)`, les noms d’éléments « F1 » et « F2 » peuvent être déduits de `(x.f1, x?.f2)`.

Cela correspond au comportement des types anonymes, qui permettent de déduire les noms de membres lors de la création. Par exemple, `new { x.f1, y?.f2 }` déclare les membres « F1 » et « F2 ».

Ceci est particulièrement pratique lors de l’utilisation de tuples dans LINQ :

```csharp
// "c" and "result" have element names "f1" and "f2"
var result = list.Select(c => (c.f1, c.f2)).Where(t => t.f2 == 1); 
```

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

La modification comporte deux parties :

1.  Essayez de déduire un nom de candidat pour chaque élément de tuple qui n’a pas de nom explicite :
    -   Utilisation des mêmes règles que l’inférence de nom pour les types anonymes.
        - Dans C#, cela autorise trois cas : `y` (identificateur), `x.y` (accès aux membres simples) et `x?.y` (accès conditionnel).
        - Dans VB, cela permet des cas supplémentaires, tels que les `x.y()`.
    -   Rejet des noms de tuple réservés (respectant la C#casse dans, non sensible à la casse dans VB), car ils sont interdits ou déjà implicites. Par exemple, par exemple `ItemN`, `Rest`et `ToString`.
    -   Si les noms des candidats sont des doublons (respect de C#la casse dans, non-respect de la casse dans VB) au sein de l’ensemble du tuple, nous détenons ces candidats,
2.  Lors des conversions (qui vérifient et signalent la suppression des noms des littéraux de Tuple), les noms inférés ne produisent pas d’avertissements. Cela évite de rompre le code de tuple existant.

Notez que la règle de gestion des doublons est différente de celle des types anonymes. Par exemple, `new { x.f1, x.f1 }` génère une erreur, mais `(x.f1, x.f1)` serait toujours autorisé (juste sans aucun nom déduit). Cela évite de rompre le code de tuple existant.

Pour des éléments de cohérence, il en va de même pour les tuples produits par les C#affectations de déconstruction (dans) :

```csharp
// tuple has element names "f1" and "f2" 
var tuple = ((x.f1, x?.f2) = (1, 2));
```

Il en va de même pour les tuples VB, en utilisant les règles spécifiques à VB pour déduire le nom de l’expression et les comparaisons de noms ne respectant pas la casse.

Quand vous utilisez C# le compilateur 7,1 (ou version ultérieure) avec la version de langue "7,0", les noms d’éléments sont déduits (en dépit de la non disponibilité de la fonctionnalité), mais une erreur d’utilisation de site se produira pour tenter d’y accéder. Cela limitera les ajouts de nouveau code qui, par la suite, conviendrait au problème de compatibilité (décrit ci-dessous).

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

L’inconvénient principal est que cela introduit une interruption de compatibilité C# de 7,0 :

```csharp
Action y = () => M();
var t = (x: x, y);
t.y(); // this might have previously picked up an extension method called “y”, but would now call the lambda.
```

Le Conseil de compatibilité a détecté que cette interruption était acceptable, étant donné qu’elle est limitée et que la fenêtre de C# temps depuis les tuples expédiés (dans 7,0) est courte.

## <a name="references"></a>References
- [LDM avril 4e 2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md#tuple-names)
- [Discussion GitHub](https://github.com/dotnet/csharplang/issues/370) (Merci @alrz pour la remise de ce problème)
- [Conception des tuples](https://github.com/dotnet/roslyn/blob/master/docs/features/tuples.md)
