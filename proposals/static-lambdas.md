---
ms.openlocfilehash: 6f05bbc1365e59d737103b586db9d4a242c6d306
ms.sourcegitcommit: e9afb74cc1abd56db93b4b50bd5e6765e27c1c5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "79485083"
---
# <a name="static-lambdas"></a><span data-ttu-id="8e3ec-101">Expressions lambda statiques</span><span class="sxs-lookup"><span data-stu-id="8e3ec-101">Static lambdas</span></span>

## <a name="summary"></a><span data-ttu-id="8e3ec-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="8e3ec-102">Summary</span></span>

<span data-ttu-id="8e3ec-103">Prennent en charge les expressions lambda qui interdisent la capture de l’état de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-103">Support lambdas that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="8e3ec-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="8e3ec-104">Motivation</span></span>

<span data-ttu-id="8e3ec-105">Évitez de capturer de manière involontaire un État dans le contexte englobant.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-105">Avoid unintentionally capturing state from the enclosing context.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="8e3ec-106">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="8e3ec-106">Detailed design</span></span>

<span data-ttu-id="8e3ec-107">Les expressions lambda avec `static` ne peuvent pas capturer l’État à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-107">A lambdas with `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="8e3ec-108">Par conséquent, les variables locales, les paramètres et les `this` de la portée englobante ne sont pas disponibles dans une `static` lambda.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-108">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` lambda.</span></span>

<span data-ttu-id="8e3ec-109">Une `static` lambda ne peut pas faire référence à des membres d’instance à partir d’une référence `this` ou `base` implicite ou explicite.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-109">A `static` lambda cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="8e3ec-110">Une `static` lambda peut faire référence à des membres `static` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-110">A `static` lambda may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="8e3ec-111">Une `static` lambda peut faire référence à des définitions de `constant` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-111">A `static` lambda may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="8e3ec-112">`nameof()` dans une expression lambda `static` peut faire référence à des variables locales, des paramètres ou des `this` ou `base` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-112">`nameof()` in a `static` lambda may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="8e3ec-113">Les règles d’accessibilité pour les membres `private` dans la portée englobante sont les mêmes pour les expressions lambda `static` et non-`static`.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-113">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` lambdas.</span></span>

<span data-ttu-id="8e3ec-114">Aucune garantie n’est faite pour déterminer si une `static` définition lambda est émise en tant que méthode de `static` dans les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-114">No guarantee is made as to whether a `static` lambda definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="8e3ec-115">Il s’agit de l’implémentation du compilateur à optimiser.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-115">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="8e3ec-116">Une fonction locale non`static` ou une expression lambda peut capturer l’État à partir d’un `static` lambda englobant, mais ne peut pas capturer l’État en dehors du `static` lambda englobant.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-116">A non-`static` local function or lambda can capture state from an enclosing `static` lambda but cannot capture state outside the enclosing `static` lambda.</span></span>

<span data-ttu-id="8e3ec-117">Un `static` lambda peut être utilisé dans une arborescence de l’expression.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-117">A `static` lambda can be used in an expression tree.</span></span>

<span data-ttu-id="8e3ec-118">La suppression du modificateur `static` d’une expression lambda dans un programme valide ne modifie pas la signification du programme.</span><span class="sxs-lookup"><span data-stu-id="8e3ec-118">Removing the `static` modifier from a lambda in a valid program does not change the meaning of the program.</span></span>
