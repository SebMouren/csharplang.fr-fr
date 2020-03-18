---
ms.openlocfilehash: b8d975a8fc95af6980feaae6be35160646fe2cd2
ms.sourcegitcommit: 32abf01f2e43be29114bfcf8f8ed1cc4e3eaded2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2019
ms.locfileid: "79485139"
---
# <a name="permit-stackalloc-in-nested-contexts"></a><span data-ttu-id="318f7-101">Autoriser les `stackalloc` dans les contextes imbriqués</span><span class="sxs-lookup"><span data-stu-id="318f7-101">Permit `stackalloc` in nested contexts</span></span>

### <a name="stack-allocation"></a><span data-ttu-id="318f7-102">Allocation de pile</span><span class="sxs-lookup"><span data-stu-id="318f7-102">Stack allocation</span></span>

<span data-ttu-id="318f7-103">Nous modifions la section [*allocation*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) de pile C# de la spécification de langage pour assouplir les endroits où une expression `stackalloc` peut apparaître.</span><span class="sxs-lookup"><span data-stu-id="318f7-103">We modify the section [*Stack allocation*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) of the C# language specification to relax the places when a `stackalloc` expression may appear.</span></span> <span data-ttu-id="318f7-104">Nous supprimons</span><span class="sxs-lookup"><span data-stu-id="318f7-104">We delete</span></span>

``` antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

<span data-ttu-id="318f7-105">et remplacez-les par</span><span class="sxs-lookup"><span data-stu-id="318f7-105">and replace them with</span></span>

``` antlr
primary_no_array_creation_expression
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression? ']' array_initializer?
    | 'stackalloc' '[' expression? ']' array_initializer
    ;
```

<span data-ttu-id="318f7-106">Notez que l’ajout d’un *array_initializer* à *stackalloc_initializer* (et l’expression d’index facultative) était une [extension dans C# 7,3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) et n’est pas décrite ici.</span><span class="sxs-lookup"><span data-stu-id="318f7-106">Note that the addition of an *array_initializer* to *stackalloc_initializer* (and making the index expression optional) was an [extension in C# 7.3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) and is not described here.</span></span>

<span data-ttu-id="318f7-107">Le *type d’élément* de l’expression `stackalloc` est le *unmanaged_type* nommé dans l’expression stackalloc, le cas échéant, ou le type commun parmi les éléments de la *array_initializer* dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="318f7-107">The *element type* of the `stackalloc` expression is the *unmanaged_type* named in the stackalloc expression, if any, or the common type among the elements of the *array_initializer* otherwise.</span></span>

<span data-ttu-id="318f7-108">Le type de l' *stackalloc_initializer* avec le *type d’élément* `K` dépend de son contexte syntaxique :</span><span class="sxs-lookup"><span data-stu-id="318f7-108">The type of the *stackalloc_initializer* with *element type* `K` depends on its syntactic context:</span></span>
- <span data-ttu-id="318f7-109">Si le *stackalloc_initializer* apparaît directement comme *local_variable_initializer* d’une instruction *local_variable_declaration* ou d’un *for_initializer*, son type est `K*`.</span><span class="sxs-lookup"><span data-stu-id="318f7-109">If the *stackalloc_initializer* appears directly as the *local_variable_initializer* of a *local_variable_declaration* statement or a *for_initializer*, then its type is `K*`.</span></span>
- <span data-ttu-id="318f7-110">Dans le cas contraire, son type est `System.Span<K>`.</span><span class="sxs-lookup"><span data-stu-id="318f7-110">Otherwise its type is `System.Span<K>`.</span></span>

### <a name="stackalloc-conversion"></a><span data-ttu-id="318f7-111">Conversion stackalloc</span><span class="sxs-lookup"><span data-stu-id="318f7-111">Stackalloc Conversion</span></span>

<span data-ttu-id="318f7-112">La *conversion stackalloc* est une nouvelle conversion implicite intégrée à partir de l’expression.</span><span class="sxs-lookup"><span data-stu-id="318f7-112">The *stackalloc conversion* is a new built-in implicit conversion from expression.</span></span> <span data-ttu-id="318f7-113">Lorsque le type d’un *stackalloc_initializer* est `K*`, il existe une *conversion stackalloc* implicite de l' *stackalloc_initializer* au type `System.Span<K>`.</span><span class="sxs-lookup"><span data-stu-id="318f7-113">When the type of a *stackalloc_initializer* is `K*`, there is an implicit *stackalloc conversion* from the *stackalloc_initializer* to the type `System.Span<K>`.</span></span>
