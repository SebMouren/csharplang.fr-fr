---
ms.openlocfilehash: 6ec94aaabb2c52393a87ee450dbae972b6a50bd5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484712"
---
# <a name="allow-digit-separator-after-0b-or-0x"></a>Autoriser le séparateur de chiffres après 0b ou 0x

Dans C# 7,2, nous étendons l’ensemble des emplacements que les séparateurs de chiffres (le caractère de soulignement) peuvent apparaître dans des littéraux intégraux. [À compter C# de 7,0, les séparateurs sont autorisés entre les chiffres d’un littéral](../csharp-7.0/digit-separators.md). Désormais, dans C# 7,2, nous autorisons également les séparateurs de chiffres avant le premier chiffre significatif d’un littéral binaire ou hexadécimal, après le préfixe.

```csharp
    123      // permitted in C# 1.0 and later
    1_2_3    // permitted in C# 7.0 and later
    0x1_2_3  // permitted in C# 7.0 and later
    0b101    // binary literals added in C# 7.0
    0b1_0_1  // permitted in C# 7.0 and later

    // in C# 7.2, _ is permitted after the `0x` or `0b`
    0x_1_2   // permitted in C# 7.2 and later
    0b_1_0_1 // permitted in C# 7.2 and later
```

Nous n’autorisons pas un littéral d’entier décimal à avoir un trait de soulignement de début. Un jeton tel que `_123` est un identificateur.
