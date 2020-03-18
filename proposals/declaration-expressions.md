---
ms.openlocfilehash: d2064ec1637e50962262c9380281abd5e1711402
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484579"
---
# <a name="declaration-expressions"></a><span data-ttu-id="95693-101">Expressions de déclaration</span><span class="sxs-lookup"><span data-stu-id="95693-101">Declaration expressions</span></span>

<span data-ttu-id="95693-102">Prendre en charge les assignations de déclaration en tant qu’expressions.</span><span class="sxs-lookup"><span data-stu-id="95693-102">Support declaration assignments as expressions.</span></span>

## <a name="motivation"></a><span data-ttu-id="95693-103">Motivation</span><span class="sxs-lookup"><span data-stu-id="95693-103">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="95693-104">Autorisez l’initialisation au point de déclaration dans plus de cas, en simplifiant le code et en autorisant l’utilisation de `var`.</span><span class="sxs-lookup"><span data-stu-id="95693-104">Allow initialization at the point of declaration in more cases, simplifying code, and allowing `var` to be used.</span></span>

```csharp
SpecialType ReferenceType =>
    (var st = _type.SpecialType).IsValueType() ? SpecialType.None : st;
```

<span data-ttu-id="95693-105">Autorisez les déclarations pour les arguments `ref`, de la même façon que `out var`.</span><span class="sxs-lookup"><span data-stu-id="95693-105">Allow declarations for `ref` arguments, similar to `out var`.</span></span>

```csharp
Convert(source, destination, ref List<Diagnostic> diagnostics = null);
```

## <a name="detailed-design"></a><span data-ttu-id="95693-106">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="95693-106">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="95693-107">Les expressions sont étendues pour inclure l’assignation de déclaration.</span><span class="sxs-lookup"><span data-stu-id="95693-107">Expressions are extended to include declaration assignment.</span></span> <span data-ttu-id="95693-108">La priorité est identique à l’affectation.</span><span class="sxs-lookup"><span data-stu-id="95693-108">Precedence is the same as assignment.</span></span>

```antlr
expression
    : non_assignment_expression
    | assignment
    | declaration_assignment_expression // new
    ;
declaration_assignment_expression // new
    : declaration_expression '=' local_variable_initializer
    ;
declaration_expression // C# 7.0
    | type variable_designation
    ;
```

<span data-ttu-id="95693-109">L’assignation de déclaration n’est pas d’un seul local.</span><span class="sxs-lookup"><span data-stu-id="95693-109">The declaration assignment is of a single local.</span></span>

<span data-ttu-id="95693-110">Le type d’une expression d’assignation de déclaration est le type de la déclaration.</span><span class="sxs-lookup"><span data-stu-id="95693-110">The type of a declaration assignment expression is the type of the declaration.</span></span>
<span data-ttu-id="95693-111">Si le type est `var`, le type déduit est le type de l’expression d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="95693-111">If the type is `var`, the inferred type is the type of the initializing expression.</span></span> 

<span data-ttu-id="95693-112">L’expression d’assignation de déclaration peut être une l-value, pour `ref` valeurs d’argument en particulier.</span><span class="sxs-lookup"><span data-stu-id="95693-112">The declaration assignment expression may be an l-value, for `ref` argument values in particular.</span></span>

<span data-ttu-id="95693-113">Si l’expression d’assignation de déclaration déclare un type valeur et que l’expression est une valeur r, la valeur de l’expression est une copie.</span><span class="sxs-lookup"><span data-stu-id="95693-113">If the declaration assignment expression declares a value type, and the expression is an r-value, the value of the expression is a copy.</span></span>

<span data-ttu-id="95693-114">L’expression d’assignation de déclaration peut déclarer un `ref` local.</span><span class="sxs-lookup"><span data-stu-id="95693-114">The declaration assignment expression may declare a `ref` local.</span></span>
<span data-ttu-id="95693-115">Il existe une ambiguïté lorsque `ref` est utilisé pour une expression de déclaration dans un argument `ref`.</span><span class="sxs-lookup"><span data-stu-id="95693-115">There is an ambiguity when `ref` is used for a declaration expression in a `ref` argument.</span></span>
<span data-ttu-id="95693-116">L’initialiseur de variable locale détermine si la déclaration est un `ref` local.</span><span class="sxs-lookup"><span data-stu-id="95693-116">The local variable initializer determines whether the declaration is a `ref` local.</span></span>

```csharp
F(ref int x = IntFunc());    // int x;
F(ref int y = RefIntFunc()); // ref int y;
```

<span data-ttu-id="95693-117">La portée des variables locales déclarées dans les expressions d’assignation de déclaration est identique à celle des expressions de déclaration correspondantes de C # 7.0.</span><span class="sxs-lookup"><span data-stu-id="95693-117">The scope of locals declared in declaration assignment expressions is the same the scope of corresponding declaration expressions from C#7.0.</span></span>

<span data-ttu-id="95693-118">Il s’agit d’une erreur au moment de la compilation pour faire référence à un local dans le texte précédant l’expression de déclaration.</span><span class="sxs-lookup"><span data-stu-id="95693-118">It is a compile time error to refer to a local in text preceding the declaration expression.</span></span>

## <a name="alternatives"></a><span data-ttu-id="95693-119">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="95693-119">Alternatives</span></span>
[alternatives]: #alternatives
<span data-ttu-id="95693-120">Aucune modification.</span><span class="sxs-lookup"><span data-stu-id="95693-120">No change.</span></span> <span data-ttu-id="95693-121">Cette fonctionnalité est simplement abrégée syntaxique après tout.</span><span class="sxs-lookup"><span data-stu-id="95693-121">This feature is just syntactic shorthand after all.</span></span>

<span data-ttu-id="95693-122">Autres expressions de séquence générales : consultez [#377](https://github.com/dotnet/csharplang/issues/377).</span><span class="sxs-lookup"><span data-stu-id="95693-122">More general sequence expressions: see [#377](https://github.com/dotnet/csharplang/issues/377).</span></span>

<span data-ttu-id="95693-123">Pour autoriser l’utilisation de `var` dans d’autres cas, autorisez la déclaration et l’assignation distinctes de `var` variables locales et déduire le type à partir des assignations de tous les chemins de code.</span><span class="sxs-lookup"><span data-stu-id="95693-123">To allow use of `var` in more cases, allow separate declaration and assignment of `var` locals, and infer the type from assignments from all code paths.</span></span>

## <a name="see-also"></a><span data-ttu-id="95693-124">Voir aussi</span><span class="sxs-lookup"><span data-stu-id="95693-124">See also</span></span>
[see-also]: #see-also
<span data-ttu-id="95693-125">Consultez expression de déclaration de base dans [#595](https://github.com/dotnet/csharplang/issues/595).</span><span class="sxs-lookup"><span data-stu-id="95693-125">See Basic Declaration Expression in [#595](https://github.com/dotnet/csharplang/issues/595).</span></span>

<span data-ttu-id="95693-126">Consultez déconstruction d’une déclaration dans la fonctionnalité de [déconstruction](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) .</span><span class="sxs-lookup"><span data-stu-id="95693-126">See Deconstruction Declaration in the [deconstruction](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) feature.</span></span>
