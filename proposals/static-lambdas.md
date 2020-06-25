---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345196"
---
# <a name="static-lambdas"></a><span data-ttu-id="4e873-101">Expressions lambda statiques</span><span class="sxs-lookup"><span data-stu-id="4e873-101">Static lambdas</span></span>

## <a name="summary"></a><span data-ttu-id="4e873-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="4e873-102">Summary</span></span>

<span data-ttu-id="4e873-103">Prennent en charge les expressions lambda qui interdisent la capture de l’état de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-103">Support lambdas that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="4e873-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="4e873-104">Motivation</span></span>

<span data-ttu-id="4e873-105">Évitez de capturer de manière involontaire un État dans le contexte englobant.</span><span class="sxs-lookup"><span data-stu-id="4e873-105">Avoid unintentionally capturing state from the enclosing context.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="4e873-106">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="4e873-106">Detailed design</span></span>

<span data-ttu-id="4e873-107">Les expressions lambda avec `static` ne peuvent pas capturer l’État à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-107">A lambdas with `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="4e873-108">Par conséquent, les variables locales, les paramètres et `this` de la portée englobante ne sont pas disponibles dans une `static` expression lambda.</span><span class="sxs-lookup"><span data-stu-id="4e873-108">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` lambda.</span></span>

<span data-ttu-id="4e873-109">Une `static` expression lambda ne peut pas faire référence à des membres d’instance à partir d’une référence implicite ou explicite `this` ou `base` .</span><span class="sxs-lookup"><span data-stu-id="4e873-109">A `static` lambda cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="4e873-110">Une `static` expression lambda peut faire référence `static` à des membres de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-110">A `static` lambda may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="4e873-111">Une `static` expression lambda peut référencer `constant` des définitions de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-111">A `static` lambda may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="4e873-112">`nameof()`dans une `static` expression lambda, peut faire référence à des variables locales, des paramètres ou `this` ou `base` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-112">`nameof()` in a `static` lambda may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="4e873-113">Les règles d’accessibilité pour les `private` membres de la portée englobante sont les mêmes pour `static` et non- `static` lambdas.</span><span class="sxs-lookup"><span data-stu-id="4e873-113">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` lambdas.</span></span>

<span data-ttu-id="4e873-114">Aucune garantie n’est faite pour déterminer si une `static` définition lambda est émise comme une `static` méthode dans les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="4e873-114">No guarantee is made as to whether a `static` lambda definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="4e873-115">Il s’agit de l’implémentation du compilateur à optimiser.</span><span class="sxs-lookup"><span data-stu-id="4e873-115">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="4e873-116">Une `static` fonction ou une expression lambda non locale peut capturer l’État à partir d’un lambda englobant `static` , mais ne peut pas capturer l’État en dehors de l' `static` expression lambda englobante.</span><span class="sxs-lookup"><span data-stu-id="4e873-116">A non-`static` local function or lambda can capture state from an enclosing `static` lambda but cannot capture state outside the enclosing `static` lambda.</span></span>

<span data-ttu-id="4e873-117">La suppression du `static` modificateur d’une expression lambda dans un programme valide ne modifie pas la signification du programme.</span><span class="sxs-lookup"><span data-stu-id="4e873-117">Removing the `static` modifier from a lambda in a valid program does not change the meaning of the program.</span></span>
