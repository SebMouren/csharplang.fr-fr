---
ms.openlocfilehash: 6ec94aaabb2c52393a87ee450dbae972b6a50bd5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484712"
---
# <a name="allow-digit-separator-after-0b-or-0x"></a><span data-ttu-id="5f3b8-101">Autoriser le séparateur de chiffres après 0b ou 0x</span><span class="sxs-lookup"><span data-stu-id="5f3b8-101">Allow digit separator after 0b or 0x</span></span>

<span data-ttu-id="5f3b8-102">Dans C# 7,2, nous étendons l’ensemble des emplacements que les séparateurs de chiffres (le caractère de soulignement) peuvent apparaître dans des littéraux intégraux.</span><span class="sxs-lookup"><span data-stu-id="5f3b8-102">In C# 7.2, we extend the set of places that digit separators (the underscore character) can appear in integral literals.</span></span> <span data-ttu-id="5f3b8-103">[À compter C# de 7,0, les séparateurs sont autorisés entre les chiffres d’un littéral](../csharp-7.0/digit-separators.md).</span><span class="sxs-lookup"><span data-stu-id="5f3b8-103">[Beginning in C# 7.0, separators are permitted between the digits of a literal](../csharp-7.0/digit-separators.md).</span></span> <span data-ttu-id="5f3b8-104">Désormais, dans C# 7,2, nous autorisons également les séparateurs de chiffres avant le premier chiffre significatif d’un littéral binaire ou hexadécimal, après le préfixe.</span><span class="sxs-lookup"><span data-stu-id="5f3b8-104">Now, in C# 7.2, we also permit digit separators before the first significant digit of a binary or hexadecimal literal, after the prefix.</span></span>

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

<span data-ttu-id="5f3b8-105">Nous n’autorisons pas un littéral d’entier décimal à avoir un trait de soulignement de début.</span><span class="sxs-lookup"><span data-stu-id="5f3b8-105">We do not permit a decimal integer literal to have a leading underscore.</span></span> <span data-ttu-id="5f3b8-106">Un jeton tel que `_123` est un identificateur.</span><span class="sxs-lookup"><span data-stu-id="5f3b8-106">A token such as `_123` is an identifier.</span></span>
