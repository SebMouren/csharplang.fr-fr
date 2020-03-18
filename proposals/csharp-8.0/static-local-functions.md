---
ms.openlocfilehash: fecd5a6c1e0f6c7a7a7beac0e4e60445281c7846
ms.sourcegitcommit: 1b488e76c2c07aafc377bc5e8a7197252c82f425
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "79485097"
---
# <a name="static-local-functions"></a><span data-ttu-id="5162a-101">Fonctions locales statiques</span><span class="sxs-lookup"><span data-stu-id="5162a-101">Static local functions</span></span>

## <a name="summary"></a><span data-ttu-id="5162a-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="5162a-102">Summary</span></span>

<span data-ttu-id="5162a-103">Prend en charge les fonctions locales qui interdisent la capture de l’État à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-103">Support local functions that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="5162a-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="5162a-104">Motivation</span></span>

<span data-ttu-id="5162a-105">Évitez de capturer de manière involontaire un État dans le contexte englobant.</span><span class="sxs-lookup"><span data-stu-id="5162a-105">Avoid unintentionally capturing state from the enclosing context.</span></span>
<span data-ttu-id="5162a-106">Autorisez l’utilisation de fonctions locales dans les scénarios où une méthode `static` est requise.</span><span class="sxs-lookup"><span data-stu-id="5162a-106">Allow local functions to be used in scenarios where a `static` method is required.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="5162a-107">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="5162a-107">Detailed design</span></span>

<span data-ttu-id="5162a-108">Une fonction locale déclarée `static` ne peut pas capturer l’État à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-108">A local function declared `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="5162a-109">Par conséquent, les variables locales, les paramètres et les `this` de la portée englobante ne sont pas disponibles dans une fonction locale `static`.</span><span class="sxs-lookup"><span data-stu-id="5162a-109">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` local function.</span></span>

<span data-ttu-id="5162a-110">Une fonction locale `static` ne peut pas faire référence à des membres d’instance à partir d’une référence `this` ou `base` implicite ou explicite.</span><span class="sxs-lookup"><span data-stu-id="5162a-110">A `static` local function cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="5162a-111">Une fonction locale `static` peut faire référence à des membres `static` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-111">A `static` local function may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="5162a-112">Une fonction locale `static` peut faire référence à des définitions de `constant` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-112">A `static` local function may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="5162a-113">`nameof()` dans une fonction locale `static` peut faire référence à des variables locales, des paramètres ou des `this` ou `base` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-113">`nameof()` in a `static` local function may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="5162a-114">Les règles d’accessibilité pour les membres `private` dans la portée englobante sont les mêmes pour les fonctions locales `static` et non-`static`.</span><span class="sxs-lookup"><span data-stu-id="5162a-114">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` local functions.</span></span>

<span data-ttu-id="5162a-115">Une définition de fonction locale `static` est émise en tant que méthode `static` dans les métadonnées, même si elle est utilisée uniquement dans un délégué.</span><span class="sxs-lookup"><span data-stu-id="5162a-115">A `static` local function definition is emitted as a `static` method in metadata, even if only used in a delegate.</span></span>

<span data-ttu-id="5162a-116">Une fonction locale non`static` ou une expression lambda peut capturer l’État à partir d’une fonction locale `static` englobante, mais ne peut pas capturer l’État en dehors de la fonction locale `static` englobante.</span><span class="sxs-lookup"><span data-stu-id="5162a-116">A non-`static` local function or lambda can capture state from an enclosing `static` local function but cannot capture state outside the enclosing `static` local function.</span></span>

<span data-ttu-id="5162a-117">Une fonction locale `static` ne peut pas être appelée dans une arborescence de l’expression.</span><span class="sxs-lookup"><span data-stu-id="5162a-117">A `static` local function cannot be invoked in an expression tree.</span></span>

<span data-ttu-id="5162a-118">Un appel à une fonction locale est émis en tant que `call` plutôt que `callvirt`, que la fonction locale soit `static`ou non.</span><span class="sxs-lookup"><span data-stu-id="5162a-118">A call to a local function is emitted as `call` rather than `callvirt`, regardless of whether the local function is `static`.</span></span>

<span data-ttu-id="5162a-119">Résolution de surcharge d’un appel dans une fonction locale non affectée par le fait que la fonction locale est `static`.</span><span class="sxs-lookup"><span data-stu-id="5162a-119">Overload resolution of a call within a local function not affected by whether the local function is `static`.</span></span>

<span data-ttu-id="5162a-120">La suppression du modificateur `static` d’une fonction locale dans un programme valide ne modifie pas la signification du programme.</span><span class="sxs-lookup"><span data-stu-id="5162a-120">Removing the `static` modifier from a local function in a valid program does not change the meaning of the program.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="5162a-121">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="5162a-121">Design meetings</span></span>

https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
