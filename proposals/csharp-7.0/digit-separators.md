---
ms.openlocfilehash: 5476f4438ad79a26b3615154f789d8ed04cb61aa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484502"
---
# <a name="digit-separators"></a><span data-ttu-id="d60fb-101">Séparateurs de chiffres</span><span class="sxs-lookup"><span data-stu-id="d60fb-101">Digit separators</span></span>

<span data-ttu-id="d60fb-102">La possibilité de regrouper des chiffres dans des littéraux numériques volumineux peut avoir un impact sur la lisibilité et aucun inconvénient significatif.</span><span class="sxs-lookup"><span data-stu-id="d60fb-102">Being able to group digits in large numeric literals would have great readability impact and no significant downside.</span></span> 

<span data-ttu-id="d60fb-103">L’ajout de littéraux binaires (#215) augmente la probabilité que les littéraux numériques soient longs, de sorte que les deux fonctionnalités s’améliorent l’une l’autre.</span><span class="sxs-lookup"><span data-stu-id="d60fb-103">Adding binary literals (#215) would increase the likelihood of numeric literals being long, so the two features enhance each other.</span></span> 

<span data-ttu-id="d60fb-104">Nous suivons Java et d’autres, et utilisons un trait de soulignement `_` comme séparateur de chiffres.</span><span class="sxs-lookup"><span data-stu-id="d60fb-104">We would follow Java and others, and use an underscore `_` as a digit separator.</span></span> <span data-ttu-id="d60fb-105">Elle peut se produire partout dans un littéral numérique (sauf le premier et le dernier caractère), dans la mesure où différents regroupements peuvent être judicieux dans différents scénarios et en particulier pour différentes bases numériques :</span><span class="sxs-lookup"><span data-stu-id="d60fb-105">It would be able to occur everywhere in a numeric literal (except as the first and last character), since different groupings may make sense in different scenarios and especially for different numeric bases:</span></span>

```csharp
int bin = 0b1001_1010_0001_0100;
int hex = 0x1b_a0_44_fe;
int dec = 33_554_432;
int weird = 1_2__3___4____5_____6______7_______8________9;
double real = 1_000.111_1e-1_000;
```

<span data-ttu-id="d60fb-106">Toute séquence de chiffres peut être séparée par des traits de soulignement, éventuellement plus d’un trait de soulignement entre deux chiffres consécutifs.</span><span class="sxs-lookup"><span data-stu-id="d60fb-106">Any sequence of digits may be separated by underscores, possibly more than one underscore between two consecutive digits.</span></span> <span data-ttu-id="d60fb-107">Elles sont autorisées dans les décimales et les exposants, mais après la règle précédente, elles peuvent ne pas apparaître en regard de la décimale (`10_.0`), en regard du caractère d’exposant (`1.1e_1`), ou en regard du spécificateur de type (`10_f`).</span><span class="sxs-lookup"><span data-stu-id="d60fb-107">They are allowed in decimals as well as exponents, but following the previous rule, they may not appear next to the decimal (`10_.0`), next to the exponent character (`1.1e_1`), or next to the type specifier (`10_f`).</span></span> <span data-ttu-id="d60fb-108">Lorsqu’ils sont utilisés dans des littéraux binaires et hexadécimaux, ils peuvent ne pas apparaître immédiatement après le `0x` ou `0b`.</span><span class="sxs-lookup"><span data-stu-id="d60fb-108">When used in binary and hexadecimal literals, they may not appear immediately following the `0x` or `0b`.</span></span>

<span data-ttu-id="d60fb-109">La syntaxe est simple et les séparateurs n’ont pas d’impact sémantique. elles sont simplement ignorées.</span><span class="sxs-lookup"><span data-stu-id="d60fb-109">The syntax is straightforward, and the separators have no semantic impact - they are simply ignored.</span></span>

<span data-ttu-id="d60fb-110">Cette valeur est très importante et est facile à implémenter.</span><span class="sxs-lookup"><span data-stu-id="d60fb-110">This has broad value and is easy to implement.</span></span>
