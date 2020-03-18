---
ms.openlocfilehash: 6695664c3d5ca73f66e792b7ce2ec9993aceea05
ms.sourcegitcommit: 42ef673ecc883009c865f8384249881a546df216
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2019
ms.locfileid: "79485153"
---
# <a name="lambda-discard-parameters"></a><span data-ttu-id="0866e-101">Paramètres d’abandon lambda</span><span class="sxs-lookup"><span data-stu-id="0866e-101">Lambda discard parameters</span></span>

## <a name="summary"></a><span data-ttu-id="0866e-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="0866e-102">Summary</span></span>

<span data-ttu-id="0866e-103">Autorisez l’utilisation des éléments ignorés (`_`) comme paramètres des expressions lambda et des méthodes anonymes.</span><span class="sxs-lookup"><span data-stu-id="0866e-103">Allow discards (`_`) to be used as parameters of lambdas and anonymous methods.</span></span>
<span data-ttu-id="0866e-104">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0866e-104">For example:</span></span>
- <span data-ttu-id="0866e-105">expressions lambda : `(_, _) => 0`, `(int _, int _) => 0`</span><span class="sxs-lookup"><span data-stu-id="0866e-105">lambdas: `(_, _) => 0`, `(int _, int _) => 0`</span></span>
- <span data-ttu-id="0866e-106">méthodes anonymes : `delegate(int _, int _) { return 0; }`</span><span class="sxs-lookup"><span data-stu-id="0866e-106">anonymous methods: `delegate(int _, int _) { return 0; }`</span></span>

## <a name="motivation"></a><span data-ttu-id="0866e-107">Motivation</span><span class="sxs-lookup"><span data-stu-id="0866e-107">Motivation</span></span>

<span data-ttu-id="0866e-108">Les paramètres inutilisés n’ont pas besoin d’être nommés.</span><span class="sxs-lookup"><span data-stu-id="0866e-108">Unused parameters do not need to be named.</span></span> <span data-ttu-id="0866e-109">L’objectif des éléments ignorés est clair, c’est-à-dire qu’ils sont inutilisés/ignorés.</span><span class="sxs-lookup"><span data-stu-id="0866e-109">The intent of discards is clear, i.e. they are unused/discarded.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="0866e-110">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="0866e-110">Detailed design</span></span>

<span data-ttu-id="0866e-111">[Paramètres de méthode](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) Dans la liste des paramètres d’une méthode lambda ou anonyme avec plusieurs paramètres nommés `_`, ces paramètres sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="0866e-111">[Method parameters](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) In the parameter list of a lambda or anonymous method with more than one parameter named `_`, such parameters are discard parameters.</span></span>
<span data-ttu-id="0866e-112">Remarque : si un seul paramètre est nommé `_`, il s’agit d’un paramètre régulier pour des raisons de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="0866e-112">Note: if a single parameter is named `_` then it is a regular parameter for backwards compatibility reasons.</span></span>

<span data-ttu-id="0866e-113">Les paramètres d’abandon n’introduisent aucun nom dans les étendues.</span><span class="sxs-lookup"><span data-stu-id="0866e-113">Discard parameters do not introduce any names to any scopes.</span></span>
<span data-ttu-id="0866e-114">Notez que cela signifie qu’ils n’entraînent pas le masquage des noms de `_` (trait de soulignement).</span><span class="sxs-lookup"><span data-stu-id="0866e-114">Note this implies they do not cause any `_` (underscore) names to be hidden.</span></span>

<span data-ttu-id="0866e-115">[Noms simples](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) Si `K` est égal à zéro et que le *simple_name* apparaît dans un *bloc* et que l’espace de déclaration de variable locale du *bloc*(ou du *bloc*englobant[) contient](basic-concepts.md#declarations)une variable locale, un paramètre (à l’exception de paramètres ignorés) ou une constante portant le nom `I`, le *simple_name* fait référence à cette variable locale, à ce paramètre ou à cette constante</span><span class="sxs-lookup"><span data-stu-id="0866e-115">[Simple names](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter (with the exception of discard parameters) or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>

<span data-ttu-id="0866e-116">[Étendues](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) À l’exception des paramètres d’abandon, la portée d’un paramètre déclaré dans *une lambda_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est la *anonymous_function_body* de cette *lambda_expression* à l’exception des paramètres d’abandon, la portée d’un paramètre déclaré dans une *anonymous_method_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est le *bloc* de cette *anonymous_method_expression*.</span><span class="sxs-lookup"><span data-stu-id="0866e-116">[Scopes](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) With the exception of discard parameters, the scope of a parameter declared in a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) is the *anonymous_function_body* of that *lambda_expression* With the exception of discard parameters, the scope of a parameter declared in an *anonymous_method_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) is the *block* of that *anonymous_method_expression*.</span></span>

## <a name="related-spec-sections"></a><span data-ttu-id="0866e-117">Sections relatives aux spécifications</span><span class="sxs-lookup"><span data-stu-id="0866e-117">Related spec sections</span></span>
- [<span data-ttu-id="0866e-118">Paramètres correspondants</span><span class="sxs-lookup"><span data-stu-id="0866e-118">Corresponding parameters</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#corresponding-parameters)
