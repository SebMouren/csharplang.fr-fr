---
ms.openlocfilehash: 5476f4438ad79a26b3615154f789d8ed04cb61aa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484502"
---
# <a name="digit-separators"></a>Séparateurs de chiffres

La possibilité de regrouper des chiffres dans des littéraux numériques volumineux peut avoir un impact sur la lisibilité et aucun inconvénient significatif. 

L’ajout de littéraux binaires (#215) augmente la probabilité que les littéraux numériques soient longs, de sorte que les deux fonctionnalités s’améliorent l’une l’autre. 

Nous suivons Java et d’autres, et utilisons un trait de soulignement `_` comme séparateur de chiffres. Elle peut se produire partout dans un littéral numérique (sauf le premier et le dernier caractère), dans la mesure où différents regroupements peuvent être judicieux dans différents scénarios et en particulier pour différentes bases numériques :

```csharp
int bin = 0b1001_1010_0001_0100;
int hex = 0x1b_a0_44_fe;
int dec = 33_554_432;
int weird = 1_2__3___4____5_____6______7_______8________9;
double real = 1_000.111_1e-1_000;
```

Toute séquence de chiffres peut être séparée par des traits de soulignement, éventuellement plus d’un trait de soulignement entre deux chiffres consécutifs. Elles sont autorisées dans les décimales et les exposants, mais après la règle précédente, elles peuvent ne pas apparaître en regard de la décimale (`10_.0`), en regard du caractère d’exposant (`1.1e_1`), ou en regard du spécificateur de type (`10_f`). Lorsqu’ils sont utilisés dans des littéraux binaires et hexadécimaux, ils peuvent ne pas apparaître immédiatement après le `0x` ou `0b`.

La syntaxe est simple et les séparateurs n’ont pas d’impact sémantique. elles sont simplement ignorées.

Cette valeur est très importante et est facile à implémenter.
