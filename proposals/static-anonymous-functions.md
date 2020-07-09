---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157196"
---
# <a name="static-anonymous-functions"></a><span data-ttu-id="de1a5-101">Fonctions anonymes statiques</span><span class="sxs-lookup"><span data-stu-id="de1a5-101">Static anonymous functions</span></span>

## <a name="summary"></a><span data-ttu-id="de1a5-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="de1a5-102">Summary</span></span>

<span data-ttu-id="de1a5-103">Autorise un modificateur’static’sur les expressions lambda et les méthodes anonymes, ce qui interdit la capture de paramètres régionaux ou de l’état de l’instance de contenant des étendues.</span><span class="sxs-lookup"><span data-stu-id="de1a5-103">Allow a 'static' modifier on lambdas and anonymous methods, which disallows capture of locals or instance state from containing scopes.</span></span>

## <a name="motivation"></a><span data-ttu-id="de1a5-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="de1a5-104">Motivation</span></span>

<span data-ttu-id="de1a5-105">Évitez de capturer de manière involontaire un État dans le contexte englobant, ce qui peut entraîner une rétention inattendue des objets capturés ou des allocations supplémentaires inattendues.</span><span class="sxs-lookup"><span data-stu-id="de1a5-105">Avoid unintentionally capturing state from the enclosing context, which can result in unexpected retention of captured objects or unexpected additional allocations.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="de1a5-106">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="de1a5-106">Detailed design</span></span>

<span data-ttu-id="de1a5-107">Une méthode lambda ou anonyme peut avoir un `static` modificateur.</span><span class="sxs-lookup"><span data-stu-id="de1a5-107">A lambda or anonymous method may have a `static` modifier.</span></span> <span data-ttu-id="de1a5-108">Le `static` modificateur indique que la méthode lambda ou anonyme est une *fonction anonyme statique*.</span><span class="sxs-lookup"><span data-stu-id="de1a5-108">The `static` modifier indicates that the lambda or anonymous method is a *static anonymous function*.</span></span>

<span data-ttu-id="de1a5-109">Une *fonction anonyme statique* ne peut pas capturer l’État à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="de1a5-109">A *static anonymous function* cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="de1a5-110">Par conséquent, les variables locales, les paramètres et `this` de la portée englobante ne sont pas disponibles dans une *fonction anonyme statique*.</span><span class="sxs-lookup"><span data-stu-id="de1a5-110">As a result, locals, parameters, and `this` from the enclosing scope are not available within a *static anonymous function*.</span></span>

<span data-ttu-id="de1a5-111">Une *fonction anonyme statique* ne peut pas faire référence à des membres d’instance à partir d’une référence implicite ou explicite `this` ou `base` .</span><span class="sxs-lookup"><span data-stu-id="de1a5-111">A *static anonymous function* cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="de1a5-112">Une *fonction anonyme statique* peut faire référence `static` à des membres de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="de1a5-112">A *static anonymous function* may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="de1a5-113">Une *fonction anonyme statique* peut référencer `constant` des définitions de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="de1a5-113">A *static anonymous function* may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="de1a5-114">`nameof()`dans une *fonction anonyme statique* peut faire référence à des variables locales, des paramètres ou `this` ou `base` à partir de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="de1a5-114">`nameof()` in a *static anonymous function* may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="de1a5-115">Les règles d’accessibilité des `private` membres de la portée englobante sont les mêmes pour les `static` fonctions et non `static` anonymes.</span><span class="sxs-lookup"><span data-stu-id="de1a5-115">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` anonymous functions.</span></span>

<span data-ttu-id="de1a5-116">Aucune garantie n’est faite pour déterminer si une définition de *fonction anonyme statique* est émise comme une `static` méthode dans les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="de1a5-116">No guarantee is made as to whether a *static anonymous function* definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="de1a5-117">Il s’agit de l’implémentation du compilateur à optimiser.</span><span class="sxs-lookup"><span data-stu-id="de1a5-117">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="de1a5-118">Une fonction non `static` locale ou une fonction anonyme peut capturer l’État à partir d’une *fonction anonyme statique* englobante, mais ne peut pas capturer l’État en dehors de la *fonction anonyme statique*englobante.</span><span class="sxs-lookup"><span data-stu-id="de1a5-118">A non-`static` local function or anonymous function can capture state from an enclosing *static anonymous function* but cannot capture state outside the enclosing *static anonymous function*.</span></span>

<span data-ttu-id="de1a5-119">La suppression du `static` modificateur d’une fonction anonyme dans un programme valide ne modifie pas la signification du programme.</span><span class="sxs-lookup"><span data-stu-id="de1a5-119">Removing the `static` modifier from an anonymous function in a valid program does not change the meaning of the program.</span></span>
