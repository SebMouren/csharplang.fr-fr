---
ms.openlocfilehash: 6f6c24e826e9fe9b9e8c97549add1029f00bcf60
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484425"
---
# <a name="binary-literals"></a>Littéraux binaires

Il existe une requête relativement courante pour ajouter des littéraux binaires à C# et VB. Pour les masques de transparence (par exemple, les énumérations d’indicateur), cela semble très utile, mais il serait également très utile à des fins pédagogiques.

Les littéraux binaires ressemblent à ceci :

```csharp
int nineteen = 0b10011;
```

De manière syntaxique et sémantique, ils sont identiques aux littéraux hexadécimaux, à l’exception de l’utilisation de `b`/`B` au lieu de `x`/`X`, avec uniquement des chiffres `0` et `1` et interprétés en base 2 au lieu de 16.

Il y a peu de frais pour l’implémentation de ces ressources, et peu de surcharge conceptuelle pour les utilisateurs du langage.

## <a name="syntax"></a>Syntaxe

La grammaire serait la suivante :

```antlr
integer-literal:
    : ...
    | binary-integer-literal
    ;
binary-integer-literal:
    : `0b` binary-digits integer-type-suffix-opt
    | `0B` binary-digits integer-type-suffix-opt
    ;
binary-digits:
    : binary-digit
    | binary-digits binary-digit
    ;
binary-digit:
    : `0`
    | `1`
    ;
```
