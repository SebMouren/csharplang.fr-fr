---
ms.openlocfilehash: 0c8bc2b5072ea7f86189b41a1cdbf2a449661b05
ms.sourcegitcommit: 33a60a1db1d42d21d959acfeb127e647150173aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79509506"
---
# <a name="attributes-on-local-functions"></a><span data-ttu-id="d2aa5-101">Attributs sur des fonctions locales</span><span class="sxs-lookup"><span data-stu-id="d2aa5-101">Attributes on local functions</span></span>

## <a name="attributes"></a><span data-ttu-id="d2aa5-102">Attributs</span><span class="sxs-lookup"><span data-stu-id="d2aa5-102">Attributes</span></span>

<span data-ttu-id="d2aa5-103">Les déclarations de fonction locale sont désormais autorisées à avoir des [attributs](../spec/attributes.md).</span><span class="sxs-lookup"><span data-stu-id="d2aa5-103">Local function declarations are now permitted to have [attributes](../spec/attributes.md).</span></span> <span data-ttu-id="d2aa5-104">Les paramètres et les paramètres de type sur les fonctions locales sont également autorisés à avoir des attributs.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-104">Parameters and type parameters on local functions are also allowed to have attributes.</span></span>

<span data-ttu-id="d2aa5-105">Les attributs ayant une signification spécifiée lorsqu’ils sont appliqués à une méthode, à ses paramètres ou à ses paramètres de type ont la même signification lorsqu’ils sont appliqués à une fonction locale, à ses paramètres ou à ses paramètres de type, respectivement.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-105">Attributes with a specified meaning when applied to a method, its parameters, or its type parameters will have the same meaning when applied to a local function, its parameters, or its type parameters, respectively.</span></span>

<span data-ttu-id="d2aa5-106">Une fonction locale peut être rendue conditionnelle dans le même sens qu’une [méthode conditionnelle](../spec/attributes.md#the-conditional-attribute) en la décorant avec un `[ConditionalAttribute]`.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-106">A local function can be made conditional in the same sense as a [conditional method](../spec/attributes.md#the-conditional-attribute) by decorating it with a `[ConditionalAttribute]`.</span></span> <span data-ttu-id="d2aa5-107">Une fonction locale conditionnelle doit également être `static`.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-107">A conditional local function must also be `static`.</span></span> <span data-ttu-id="d2aa5-108">Toutes les restrictions sur les méthodes conditionnelles s’appliquent également aux fonctions locales conditionnelles, y compris que le type de retour doit être `void`.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-108">All restrictions on conditional methods also apply to conditional local functions, including that the return type must be `void`.</span></span>

## <a name="extern"></a><span data-ttu-id="d2aa5-109">Externes</span><span class="sxs-lookup"><span data-stu-id="d2aa5-109">Extern</span></span>

<span data-ttu-id="d2aa5-110">Le modificateur `extern` est désormais autorisé sur les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-110">The `extern` modifier is now permitted on local functions.</span></span> <span data-ttu-id="d2aa5-111">Cela rend la fonction locale externe dans le même sens qu’une [méthode externe](../spec/classes.md#external-methods).</span><span class="sxs-lookup"><span data-stu-id="d2aa5-111">This makes the local function external in the same sense as an [external method](../spec/classes.md#external-methods).</span></span>

<span data-ttu-id="d2aa5-112">À l’instar d’une méthode externe, le *corps de fonction locale* d’une fonction locale externe doit être un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-112">Similarly to an external method, the *local-function-body* of an external local function must be a semicolon.</span></span> <span data-ttu-id="d2aa5-113">Un *corps de fonction locale* de point-virgule est uniquement autorisé sur une fonction locale externe.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-113">A semicolon *local-function-body* is only permitted on an external local function.</span></span> 

<span data-ttu-id="d2aa5-114">Une fonction locale externe doit également être `static`.</span><span class="sxs-lookup"><span data-stu-id="d2aa5-114">An external local function must also be `static`.</span></span>

## <a name="syntax"></a><span data-ttu-id="d2aa5-115">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="d2aa5-115">Syntax</span></span>

<span data-ttu-id="d2aa5-116">La [grammaire Functions locale](csharp-7.0/local-functions.md#syntax-grammar) est modifiée comme suit :</span><span class="sxs-lookup"><span data-stu-id="d2aa5-116">The [local functions grammar](csharp-7.0/local-functions.md#syntax-grammar) is modified as follows:</span></span>
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```
