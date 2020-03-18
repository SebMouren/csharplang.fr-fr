---
ms.openlocfilehash: 2532a24e867930d2f27614f19c77585dbce80dfa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484544"
---
# <a name="throw-expression"></a><span data-ttu-id="cc3b2-101">Expression Throw</span><span class="sxs-lookup"><span data-stu-id="cc3b2-101">Throw expression</span></span>

<span data-ttu-id="cc3b2-102">Nous étendons l’ensemble de formulaires d’expression à inclure</span><span class="sxs-lookup"><span data-stu-id="cc3b2-102">We extend the set of expression forms to include</span></span>

```antlr
throw_expression
    : 'throw' null_coalescing_expression
    ;

null_coalescing_expression
    : throw_expression
    ;
```

<span data-ttu-id="cc3b2-103">Les règles de type sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc3b2-103">The type rules are as follows:</span></span>

- <span data-ttu-id="cc3b2-104">Un *throw_expression* n’a aucun type.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-104">A *throw_expression* has no type.</span></span>
- <span data-ttu-id="cc3b2-105">Une *throw_expression* est convertible à chaque type par une conversion implicite.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-105">A *throw_expression* is convertible to every type by an implicit conversion.</span></span>

<span data-ttu-id="cc3b2-106">Une *expression Throw* lève la valeur produite en évaluant l' *null_coalescing_expression*, qui doit indiquer une valeur du type de classe `System.Exception`, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de type qui a `System.Exception` (ou une sous-classe de ce type) comme classe de base effective.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-106">A *throw expression* throws the value produced by evaluating the *null_coalescing_expression*, which must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="cc3b2-107">Si l’évaluation de l’expression produit `null`, un `System.NullReferenceException` est levé à la place.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-107">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="cc3b2-108">Le comportement au moment de l’exécution de l’évaluation d’une *expression Throw* est le même [que celui spécifié pour une *instruction throw*](../../spec/statements.md#the-throw-statement).</span><span class="sxs-lookup"><span data-stu-id="cc3b2-108">The behavior at runtime of the evaluation of a *throw expression* is the same [as specified for a *throw statement*](../../spec/statements.md#the-throw-statement).</span></span>

<span data-ttu-id="cc3b2-109">Les règles d’analyse de flow sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc3b2-109">The flow-analysis rules are as follows:</span></span>

- <span data-ttu-id="cc3b2-110">Pour chaque variable *v*, *v* est définitivement assignée avant la *null_coalescing_expression* d’un *throw_expression* IFF elle est définitivement assignée avant le *throw_expression*.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-110">For every variable *v*, *v* is definitely assigned before the *null_coalescing_expression* of a *throw_expression* iff it is definitely assigned before the *throw_expression*.</span></span>
- <span data-ttu-id="cc3b2-111">Pour chaque variable *v*, *v* est définitivement assignée après *throw_expression*.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-111">For every variable *v*, *v* is definitely assigned after *throw_expression*.</span></span>

<span data-ttu-id="cc3b2-112">Une *expression Throw* n’est autorisée que dans les contextes syntaxiques suivants :</span><span class="sxs-lookup"><span data-stu-id="cc3b2-112">A *throw expression* is permitted in only the following syntactic contexts:</span></span>
- <span data-ttu-id="cc3b2-113">En tant que deuxième ou troisième opérande d’un opérateur conditionnel ternaire `?:`</span><span class="sxs-lookup"><span data-stu-id="cc3b2-113">As the second or third operand of a ternary conditional operator `?:`</span></span>
- <span data-ttu-id="cc3b2-114">Comme second opérande d’un opérateur de fusion Null `??`</span><span class="sxs-lookup"><span data-stu-id="cc3b2-114">As the second operand of a null coalescing operator `??`</span></span>
- <span data-ttu-id="cc3b2-115">En tant que corps d’une expression lambda ou d’une méthode.</span><span class="sxs-lookup"><span data-stu-id="cc3b2-115">As the body of an expression-bodied lambda or method.</span></span>
