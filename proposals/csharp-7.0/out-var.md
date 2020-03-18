---
ms.openlocfilehash: a0d80afc47e9f0073237db9b8d7a4f0b045c1b0b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484509"
---
# <a name="out-variable-declarations"></a><span data-ttu-id="d3849-101">Déclarations de variables out</span><span class="sxs-lookup"><span data-stu-id="d3849-101">Out variable declarations</span></span>

<span data-ttu-id="d3849-102">La fonctionnalité de *déclaration de variable out* permet à une variable d’être déclarée à l’emplacement où elle est passée en tant qu’argument `out`.</span><span class="sxs-lookup"><span data-stu-id="d3849-102">The *out variable declaration* feature enables a variable to be declared at the location that it is being passed as an `out` argument.</span></span>

```antlr
argument_value
    : 'out' type identifier
    | ...
    ;
```

<span data-ttu-id="d3849-103">Une variable déclarée de cette façon est appelée une *variable out*.</span><span class="sxs-lookup"><span data-stu-id="d3849-103">A variable declared this way is called an *out variable*.</span></span> <span data-ttu-id="d3849-104">Vous pouvez utiliser le mot clé contextuel `var` pour le type de la variable.</span><span class="sxs-lookup"><span data-stu-id="d3849-104">You may use the contextual keyword `var` for the variable's type.</span></span> <span data-ttu-id="d3849-105">L’étendue est la même que pour une *variable de modèle* introduite via la mise en correspondance de modèle.</span><span class="sxs-lookup"><span data-stu-id="d3849-105">The scope will be the same as for a *pattern-variable* introduced via pattern-matching.</span></span>

<span data-ttu-id="d3849-106">Conformément à la spécification du langage (section 7.6.7 Element Access), la liste d’arguments d’un élément d’accès (expression d’indexation) ne contient pas d’arguments ref ou out.</span><span class="sxs-lookup"><span data-stu-id="d3849-106">According to Language Specification (section 7.6.7 Element access) the argument-list of an element-access (indexing expression) does not contain ref or out arguments.</span></span> <span data-ttu-id="d3849-107">Toutefois, ils sont autorisés par le compilateur pour différents scénarios, par exemple les indexeurs déclarés dans les métadonnées qui acceptent `out`.</span><span class="sxs-lookup"><span data-stu-id="d3849-107">However, they are permitted by the compiler for various scenarios, for example indexers declared in metadata that accept `out`.</span></span>

<span data-ttu-id="d3849-108">Dans l’étendue d’une variable locale introduite par un argument_value, il s’agit d’une erreur au moment de la compilation pour faire référence à cette variable locale dans une position textuelle qui précède sa déclaration.</span><span class="sxs-lookup"><span data-stu-id="d3849-108">Within the scope of a local variable introduced by an argument_value, it is a compile-time error to refer to that local variable in a textual position that precedes its declaration.</span></span>

<span data-ttu-id="d3849-109">Il s’agit également d’une erreur pour référencer une variable de sortie implicitement typée (§ 8.5.1) dans la même liste d’arguments qui contient immédiatement sa déclaration.</span><span class="sxs-lookup"><span data-stu-id="d3849-109">It is also an error to reference an implicitly-typed (§8.5.1) out variable in the same argument list that immediately contains its declaration.</span></span>

<span data-ttu-id="d3849-110">La résolution de surcharge est modifiée comme suit :</span><span class="sxs-lookup"><span data-stu-id="d3849-110">Overload resolution is modified as follows:</span></span>

<span data-ttu-id="d3849-111">Nous ajoutons une nouvelle conversion :</span><span class="sxs-lookup"><span data-stu-id="d3849-111">We add a new conversion:</span></span>

> <span data-ttu-id="d3849-112">Il y a une *conversion de l’expression* d’une déclaration de variable de type implicitement typée à chaque type.</span><span class="sxs-lookup"><span data-stu-id="d3849-112">There is a *conversion from expression* from an implicitly-typed out variable declaration to every type.</span></span>

<span data-ttu-id="d3849-113">Même</span><span class="sxs-lookup"><span data-stu-id="d3849-113">Also</span></span>

> <span data-ttu-id="d3849-114">Le type d’un argument de variable out explicitement typé est le type déclaré.</span><span class="sxs-lookup"><span data-stu-id="d3849-114">The type of an explicitly-typed out variable argument is the declared type.</span></span>

<span data-ttu-id="d3849-115">and</span><span class="sxs-lookup"><span data-stu-id="d3849-115">and</span></span>

> <span data-ttu-id="d3849-116">Un argument de variable out implicitement typé n’a aucun type.</span><span class="sxs-lookup"><span data-stu-id="d3849-116">An implicitly-typed out variable argument has no type.</span></span>

<span data-ttu-id="d3849-117">La *conversion d’une expression* à partir d’une déclaration de variable de sortie implicitement typée n’est pas considérée comme meilleure que toute autre *conversion à partir d’une expression*.</span><span class="sxs-lookup"><span data-stu-id="d3849-117">The *conversion from expression* from an implicitly-typed out variable declaration is not considered better than any other *conversion from expression*.</span></span>

<span data-ttu-id="d3849-118">Le type d’une variable de sortie implicitement typée est le type du paramètre correspondant dans la signature de la méthode sélectionnée par la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="d3849-118">The type of an implicitly-typed out variable is the type of the corresponding parameter in the signature of the method selected by overload resolution.</span></span>

<span data-ttu-id="d3849-119">Le nouveau nœud de syntaxe `DeclarationExpressionSyntax` est ajouté pour représenter la déclaration dans un argument var.</span><span class="sxs-lookup"><span data-stu-id="d3849-119">The new syntax node `DeclarationExpressionSyntax` is added to represent the declaration in an out var argument.</span></span>
