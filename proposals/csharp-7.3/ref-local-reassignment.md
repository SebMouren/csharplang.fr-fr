---
ms.openlocfilehash: c1a77d9337ecd16f5ea1c30d18f6422552b0dfb2
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484600"
---
# <a name="ref-local-reassignment"></a><span data-ttu-id="7a9f5-101">Réaffectation locale de Ref</span><span class="sxs-lookup"><span data-stu-id="7a9f5-101">Ref Local Reassignment</span></span>

<span data-ttu-id="7a9f5-102">Dans C# 7,3, nous ajoutons la prise en charge de la reliaison du référent d’une variable locale ref ou d’un paramètre ref.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-102">In C# 7.3, we add support for rebinding the referent of a ref local variable or a ref parameter.</span></span>

<span data-ttu-id="7a9f5-103">Nous ajoutons ce qui suit au jeu de `assignment_operator`s.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-103">We add the following to the set of `assignment_operator`s.</span></span>

```antlr
assignment_operator
    : '=' 'ref'
    ;
```

<span data-ttu-id="7a9f5-104">L’opérateur `=ref` est appelé ***opérateur d’assignation de référence***.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-104">The `=ref` operator is called the ***ref assignment operator***.</span></span> <span data-ttu-id="7a9f5-105">Il ne s’agit pas d’un *opérateur d’assignation composée*.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-105">It is not a *compound assignment operator*.</span></span> <span data-ttu-id="7a9f5-106">L’opérande gauche doit être une expression qui crée une liaison à une variable locale REF, à un paramètre ref (autre que `this`) ou à un paramètre out.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-106">The left operand must be an expression that binds to a ref local variable, a ref parameter (other than `this`), or an out parameter.</span></span> <span data-ttu-id="7a9f5-107">L’opérande de droite doit être une expression qui produit une lvalue désignant une valeur du même type que l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-107">The right operand must be an expression that yields an lvalue designating a value of the same type as the left operand.</span></span>

<span data-ttu-id="7a9f5-108">L’opérande de droite doit être définitivement assigné au point de l’assignation de référence.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-108">The right operand must be definitely assigned at the point of the ref assignment.</span></span>

<span data-ttu-id="7a9f5-109">Lorsque l’opérande gauche est lié à un paramètre `out`, il s’agit d’une erreur si ce `out` paramètre n’a pas été définitivement assigné au début de l’opérateur d’assignation de référence.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-109">When the left operand binds to an `out` parameter, it is an error if that `out` parameter has not been definitely assigned at the beginning of the ref assignment operator.</span></span>

<span data-ttu-id="7a9f5-110">Si l’opérande de gauche est une référence accessible en écriture (c’est-à-dire qu’elle désigne tout autre chose qu’une `ref readonly` paramètre local ou `in`), l’opérande de droite doit être une lvalue accessible en écriture.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-110">If the left operand is a writeable ref (i.e. it designates anything other than a `ref readonly` local or  `in` parameter), then the right operand must be a writeable lvalue.</span></span>

<span data-ttu-id="7a9f5-111">L’opérateur d’assignation de référence produit une lvalue du type assigné.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-111">The ref assignment operator yields an lvalue of the assigned type.</span></span> <span data-ttu-id="7a9f5-112">Il peut être écrit si l’opérande de gauche est accessible en écriture (c’est-à-dire non `ref readonly` ou `in`).</span><span class="sxs-lookup"><span data-stu-id="7a9f5-112">It is writeable if the left operand is writeable (i.e. not `ref readonly` or `in`).</span></span>

<span data-ttu-id="7a9f5-113">Les règles de sécurité pour cet opérateur sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="7a9f5-113">The safety rules for this operator are:</span></span>

- <span data-ttu-id="7a9f5-114">Pour une réassignation de référence `e1 = ref e2`, le niveau de sécurité de la *référence à l’échappement* de l' `e2` doit être au moins aussi vaste que la portée de la *référence* de `e1`.</span><span class="sxs-lookup"><span data-stu-id="7a9f5-114">For a ref reassignment `e1 = ref e2`, the *ref-safe-to-escape* of `e2` must be at least as wide a scope as the *ref-safe-to-escape* of `e1`.</span></span>

<span data-ttu-id="7a9f5-115">Où *ref-Safe-to-Escape* est défini en [sécurité pour les types](../csharp-7.2/span-safety.md) de type Ref</span><span class="sxs-lookup"><span data-stu-id="7a9f5-115">Where *ref-safe-to-escape* is defined in [Safety for ref-like types](../csharp-7.2/span-safety.md)</span></span>
