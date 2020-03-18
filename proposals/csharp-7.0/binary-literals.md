---
ms.openlocfilehash: 6f6c24e826e9fe9b9e8c97549add1029f00bcf60
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484425"
---
# <a name="binary-literals"></a><span data-ttu-id="cbb0c-101">Littéraux binaires</span><span class="sxs-lookup"><span data-stu-id="cbb0c-101">Binary literals</span></span>

<span data-ttu-id="cbb0c-102">Il existe une requête relativement courante pour ajouter des littéraux binaires à C# et VB.</span><span class="sxs-lookup"><span data-stu-id="cbb0c-102">There’s a relatively common request to add binary literals to C# and VB.</span></span> <span data-ttu-id="cbb0c-103">Pour les masques de transparence (par exemple, les énumérations d’indicateur), cela semble très utile, mais il serait également très utile à des fins pédagogiques.</span><span class="sxs-lookup"><span data-stu-id="cbb0c-103">For bitmasks (e.g. flag enums) this seems genuinely useful, but it would also be great just for educational purposes.</span></span>

<span data-ttu-id="cbb0c-104">Les littéraux binaires ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="cbb0c-104">Binary literals would look like this:</span></span>

```csharp
int nineteen = 0b10011;
```

<span data-ttu-id="cbb0c-105">De manière syntaxique et sémantique, ils sont identiques aux littéraux hexadécimaux, à l’exception de l’utilisation de `b`/`B` au lieu de `x`/`X`, avec uniquement des chiffres `0` et `1` et interprétés en base 2 au lieu de 16.</span><span class="sxs-lookup"><span data-stu-id="cbb0c-105">Syntactically and semantically they are identical to hexadecimal literals, except for using `b`/`B` instead of `x`/`X`, having only digits `0` and `1` and being interpreted in base 2 instead of 16.</span></span>

<span data-ttu-id="cbb0c-106">Il y a peu de frais pour l’implémentation de ces ressources, et peu de surcharge conceptuelle pour les utilisateurs du langage.</span><span class="sxs-lookup"><span data-stu-id="cbb0c-106">There’s little cost to implementing these, and little conceptual overhead to users of the language.</span></span>

## <a name="syntax"></a><span data-ttu-id="cbb0c-107">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="cbb0c-107">Syntax</span></span>

<span data-ttu-id="cbb0c-108">La grammaire serait la suivante :</span><span class="sxs-lookup"><span data-stu-id="cbb0c-108">The grammar would be as follows:</span></span>

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
