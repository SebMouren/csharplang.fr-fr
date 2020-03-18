---
ms.openlocfilehash: b8d975a8fc95af6980feaae6be35160646fe2cd2
ms.sourcegitcommit: 32abf01f2e43be29114bfcf8f8ed1cc4e3eaded2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2019
ms.locfileid: "79485139"
---
# <a name="permit-stackalloc-in-nested-contexts"></a>Autoriser les `stackalloc` dans les contextes imbriqués

### <a name="stack-allocation"></a>Allocation de pile

Nous modifions la section [*allocation*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) de pile C# de la spécification de langage pour assouplir les endroits où une expression `stackalloc` peut apparaître. Nous supprimons

``` antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

et remplacez-les par

``` antlr
primary_no_array_creation_expression
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression? ']' array_initializer?
    | 'stackalloc' '[' expression? ']' array_initializer
    ;
```

Notez que l’ajout d’un *array_initializer* à *stackalloc_initializer* (et l’expression d’index facultative) était une [extension dans C# 7,3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) et n’est pas décrite ici.

Le *type d’élément* de l’expression `stackalloc` est le *unmanaged_type* nommé dans l’expression stackalloc, le cas échéant, ou le type commun parmi les éléments de la *array_initializer* dans le cas contraire.

Le type de l' *stackalloc_initializer* avec le *type d’élément* `K` dépend de son contexte syntaxique :
- Si le *stackalloc_initializer* apparaît directement comme *local_variable_initializer* d’une instruction *local_variable_declaration* ou d’un *for_initializer*, son type est `K*`.
- Dans le cas contraire, son type est `System.Span<K>`.

### <a name="stackalloc-conversion"></a>Conversion stackalloc

La *conversion stackalloc* est une nouvelle conversion implicite intégrée à partir de l’expression. Lorsque le type d’un *stackalloc_initializer* est `K*`, il existe une *conversion stackalloc* implicite de l' *stackalloc_initializer* au type `System.Span<K>`.
