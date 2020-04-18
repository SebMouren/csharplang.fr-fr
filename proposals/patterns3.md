---
ms.openlocfilehash: 57cdb682efd4cb169308e347d63e27c97b9f1b7a
ms.sourcegitcommit: 6901635c383801e4d177085587aaccadaa7b2f11
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641975"
---
# <a name="pattern-matching-changes-for-c-90"></a><span data-ttu-id="660ed-101">Modifications correspondants aux modèles pour le C 9.0</span><span class="sxs-lookup"><span data-stu-id="660ed-101">Pattern-matching changes for C# 9.0</span></span>

<span data-ttu-id="660ed-102">Nous envisageons une petite poignée d’améliorations à l’appariement des motifs pour C 9.0 qui ont une synergie naturelle et fonctionnent bien pour résoudre un certain nombre de problèmes de programmation communs:</span><span class="sxs-lookup"><span data-stu-id="660ed-102">We are considering a small handful of enhancements to pattern-matching for C# 9.0 that have natural synergy and work well to address a number of common programming problems:</span></span>
- <span data-ttu-id="660ed-103">https://github.com/dotnet/csharplang/issues/2925Types de type</span><span class="sxs-lookup"><span data-stu-id="660ed-103">https://github.com/dotnet/csharplang/issues/2925 Type patterns</span></span>
- <span data-ttu-id="660ed-104">https://github.com/dotnet/csharplang/issues/1350Modèles parenthés pour faire respecter ou mettre l’accent sur la préséance des nouveaux combinateurs</span><span class="sxs-lookup"><span data-stu-id="660ed-104">https://github.com/dotnet/csharplang/issues/1350 Parenthesized patterns to enforce or emphasize precedence of the new combinators</span></span>
- <span data-ttu-id="660ed-105">https://github.com/dotnet/csharplang/issues/1350Modèles conjonctifs `and` qui nécessitent deux modèles différents pour correspondre;</span><span class="sxs-lookup"><span data-stu-id="660ed-105">https://github.com/dotnet/csharplang/issues/1350 Conjunctive `and` patterns that require both of two different patterns to match;</span></span>
- <span data-ttu-id="660ed-106">https://github.com/dotnet/csharplang/issues/1350Modèles disjonctifs `or` qui nécessitent l’un ou l’autre des deux modèles différents pour correspondre;</span><span class="sxs-lookup"><span data-stu-id="660ed-106">https://github.com/dotnet/csharplang/issues/1350 Disjunctive `or` patterns that require either of two different patterns to match;</span></span>
- <span data-ttu-id="660ed-107">https://github.com/dotnet/csharplang/issues/1350Modèles `not` niés qui nécessitent un modèle donné pour *ne pas* correspondre; Et</span><span class="sxs-lookup"><span data-stu-id="660ed-107">https://github.com/dotnet/csharplang/issues/1350 Negated `not` patterns that require a given pattern *not* to match; and</span></span>
- <span data-ttu-id="660ed-108">https://github.com/dotnet/csharplang/issues/812Modèles relationnels qui exigent que la valeur d’entrée soit inférieure, inférieure ou égale à, etc une constante donnée.</span><span class="sxs-lookup"><span data-stu-id="660ed-108">https://github.com/dotnet/csharplang/issues/812 Relational patterns that require the input value to be less than, less than or equal to, etc a given constant.</span></span>

## <a name="parenthesized-patterns"></a><span data-ttu-id="660ed-109">Modèles parenthésisés</span><span class="sxs-lookup"><span data-stu-id="660ed-109">Parenthesized Patterns</span></span>

<span data-ttu-id="660ed-110">Les motifs parenthésiens permettent au programmeur de mettre des parenthèses autour de n’importe quel modèle.</span><span class="sxs-lookup"><span data-stu-id="660ed-110">Parenthesized patterns permit the programmer to put parentheses around any pattern.</span></span>  <span data-ttu-id="660ed-111">Ce n’est pas si utile avec les modèles existants dans C 8.0, mais les nouveaux combinateurs de modèle introduisent une priorité que le programmeur peut vouloir remplacer.</span><span class="sxs-lookup"><span data-stu-id="660ed-111">This is not so useful with the existing patterns in C# 8.0, however the new pattern combinators introduce a precedence that the programmer may want to override.</span></span>

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a><span data-ttu-id="660ed-112">Modèles de type</span><span class="sxs-lookup"><span data-stu-id="660ed-112">Type Patterns</span></span>

<span data-ttu-id="660ed-113">Nous permettons un type comme modèle :</span><span class="sxs-lookup"><span data-stu-id="660ed-113">We permit a type as a pattern:</span></span>

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

<span data-ttu-id="660ed-114">Cela retcons l’expression existante *est-type-expression* d’être un *is-pattern-expression* dans laquelle le modèle est un *type-modèle,* bien que nous ne changerions pas l’arbre syntaxe produit par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="660ed-114">This retcons the existing *is-type-expression* to be an *is-pattern-expression* in which the pattern is a *type-pattern*, though we would not change the syntax tree produced by the compiler.</span></span>

<span data-ttu-id="660ed-115">Une question subtile de mise en œuvre est que cette grammaire est ambigu.</span><span class="sxs-lookup"><span data-stu-id="660ed-115">One subtle implementation issue is that this grammar is ambiguous.</span></span>  <span data-ttu-id="660ed-116">Une chaîne `a.b` telle que peut être analysée soit comme un nom qualifié (dans un contexte de type) ou une expression pointillée (dans un contexte d’expression).</span><span class="sxs-lookup"><span data-stu-id="660ed-116">A string such as `a.b` can be parsed either as a qualified name (in a type context) or a dotted expression (in an expression context).</span></span>  <span data-ttu-id="660ed-117">Le compilateur est déjà capable de traiter un nom qualifié le même `e is Color.Red`qu’une expression pointillée afin de gérer quelque chose comme .</span><span class="sxs-lookup"><span data-stu-id="660ed-117">The compiler is already capable of treating a qualified name the same as a dotted expression in order to handle something like `e is Color.Red`.</span></span>  <span data-ttu-id="660ed-118">L’analyse sémantique du compilateur serait encore étendue pour être capable de lier un modèle constant (syntaxique) (par exemple une expression pointillée) comme un type afin de le traiter comme un modèle de type lié afin de soutenir cette construction.</span><span class="sxs-lookup"><span data-stu-id="660ed-118">The compiler's semantic analysis would be further extended to be capable of binding a (syntactic) constant pattern (e.g. a dotted expression) as a type in order to treat it as a bound type pattern in order to support this construct.</span></span>

<span data-ttu-id="660ed-119">Après ce changement, vous seriez en mesure d’écrire</span><span class="sxs-lookup"><span data-stu-id="660ed-119">After this change, you would be able to write</span></span>
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a><span data-ttu-id="660ed-120">Modèles relationnels</span><span class="sxs-lookup"><span data-stu-id="660ed-120">Relational Patterns</span></span>

<span data-ttu-id="660ed-121">Les modèles relationnels permettent au programmeur d’exprimer qu’une valeur d’entrée doit satisfaire à une contrainte relationnelle par rapport à une valeur constante :</span><span class="sxs-lookup"><span data-stu-id="660ed-121">Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:</span></span>

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

<span data-ttu-id="660ed-122">Les modèles relationnels `<`soutiennent `<=` `>`les `>=` opérateurs relationnels, , , et sur tous les types intégrés qui prennent en charge ces opérateurs relationnels binaires avec deux opérandes du même type dans une expression.</span><span class="sxs-lookup"><span data-stu-id="660ed-122">Relational patterns support the relational operators `<`, `<=`, `>`, and `>=` on all of the built-in types that support such binary relational operators with two operands of the same type in an expression.</span></span> <span data-ttu-id="660ed-123">Spécifiquement, nous soutenons tous ces `sbyte`modèles `byte`relationnels pour `char`, `float` `double`, `decimal` `nint` `short`, `ushort`, `int` `uint`, `long`, , `ulong`, , , , , , , , , , et `nuint`.</span><span class="sxs-lookup"><span data-stu-id="660ed-123">Specifically, we support all of these relational patterns for `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `nint`, and `nuint`.</span></span>

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

<span data-ttu-id="660ed-124">L’expression est nécessaire pour évaluer à une valeur constante.</span><span class="sxs-lookup"><span data-stu-id="660ed-124">The expression is required to evaluate to a constant value.</span></span>  <span data-ttu-id="660ed-125">C’est une erreur si `double.NaN` `float.NaN`cette valeur constante est ou .</span><span class="sxs-lookup"><span data-stu-id="660ed-125">It is an error if that constant value is `double.NaN` or `float.NaN`.</span></span>  <span data-ttu-id="660ed-126">C’est une erreur si l’expression est une constante nulle.</span><span class="sxs-lookup"><span data-stu-id="660ed-126">It is an error if the expression is a null constant.</span></span>

<span data-ttu-id="660ed-127">Lorsque l’entrée est un type pour lequel un opérateur relationnel binaire intégré approprié est défini qui est applicable avec l’entrée comme son opératment gauche et la constante donnée comme son opéra droit, l’évaluation de cet opérateur est prise comme la signification du modèle relationnel.</span><span class="sxs-lookup"><span data-stu-id="660ed-127">When the input is a type for which a suitable built-in binary relational operator is defined that is applicable with the input as its left operand and the given constant as its right operand, the evaluation of that operator is taken as the meaning of the relational pattern.</span></span>  <span data-ttu-id="660ed-128">Sinon, nous convertissons l’entrée au type d’expression à l’aide d’une conversion nulle ou déballage explicite.</span><span class="sxs-lookup"><span data-stu-id="660ed-128">Otherwise we convert the input to the type of the expression using an explicit nullable or unboxing conversion.</span></span>  <span data-ttu-id="660ed-129">Il s’agit d’une erreur de compilation-temps si aucune conversion de ce genre n’existe.</span><span class="sxs-lookup"><span data-stu-id="660ed-129">It is a compile-time error if no such conversion exists.</span></span>  <span data-ttu-id="660ed-130">Le modèle est considéré comme ne correspondant pas si la conversion échoue.</span><span class="sxs-lookup"><span data-stu-id="660ed-130">The pattern is considered not to match if the conversion fails.</span></span>  <span data-ttu-id="660ed-131">Si la conversion réussit, le résultat de l’opération de `e OP v` jumelage des motifs est le résultat de l’évaluation de l’expression où `e` se trouve l’entrée convertie, `OP` est l’opérateur relationnel, et `v` est l’expression constante.</span><span class="sxs-lookup"><span data-stu-id="660ed-131">If the conversion succeeds then the result of the pattern-matching operation is the result of evaluating the expression `e OP v` where `e` is the converted input, `OP` is the relational operator, and `v` is the constant expression.</span></span>

## <a name="pattern-combinators"></a><span data-ttu-id="660ed-132">Combinateurs de modèle</span><span class="sxs-lookup"><span data-stu-id="660ed-132">Pattern Combinators</span></span>

<span data-ttu-id="660ed-133">Les *combinateurs* de modèle permettent `and` d’apparier les deux modèles différents utilisant `and`(ceci peut être `or` étendu à n’importe quel nombre de modèles par l’utilisation répétée de ), soit de deux modèles différents utilisant (ditto), ou la *négation* d’un modèle utilisant `not`.</span><span class="sxs-lookup"><span data-stu-id="660ed-133">Pattern *combinators* permit matching both of two different patterns using `and` (this can be extended to any number of patterns by the repeated use of `and`), either of two different patterns using `or` (ditto), or the *negation* of a pattern using `not`.</span></span>

<span data-ttu-id="660ed-134">Une utilisation commune d’un combinateur sera l’idiome</span><span class="sxs-lookup"><span data-stu-id="660ed-134">A common use of a combinator will be the idiom</span></span>

``` c#
if (e is not null) ...
```

<span data-ttu-id="660ed-135">Plus lisible que l’idiome `e is object`actuel, ce modèle exprime clairement que l’on vérifie une valeur non nulle.</span><span class="sxs-lookup"><span data-stu-id="660ed-135">More readable than the current idiom `e is object`, this pattern clearly expresses that one is checking for a non-null value.</span></span>

<span data-ttu-id="660ed-136">Les `and` `or` combinateurs et les combinateurs seront utiles pour tester des gammes de valeurs</span><span class="sxs-lookup"><span data-stu-id="660ed-136">The `and` and `or` combinators will be useful for testing ranges of values</span></span>

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

<span data-ttu-id="660ed-137">Cet exemple illustre `and` que la priorité d’analyse sera plus élevée (c’est-à-dire qu’elle se liera plus étroitement) que `or`.</span><span class="sxs-lookup"><span data-stu-id="660ed-137">This example illustrates that `and` will have a higher parsing priority (i.e. will bind more closely) than `or`.</span></span>  <span data-ttu-id="660ed-138">Le programmeur peut utiliser le *modèle parenthèse* pour rendre la préséance explicite :</span><span class="sxs-lookup"><span data-stu-id="660ed-138">The programmer can use the *parenthesized pattern* to make the precedence explicit:</span></span>

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

<span data-ttu-id="660ed-139">Comme tous les modèles, ces combinateurs peuvent être utilisés dans n’importe quel contexte dans lequel un modèle est attendu, y compris les modèles imbriqués, *l’expression est-modèle,* *l’expression de commutateur,* et le modèle de l’étiquette de cas d’une déclaration de commutateur.</span><span class="sxs-lookup"><span data-stu-id="660ed-139">Like all patterns, these combinators can be used in any context in which a pattern is expected, including nested patterns, the *is-pattern-expression*, the *switch-expression*, and the pattern of a switch statement's case label.</span></span>

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="open-issues-with-proposed-changes"></a><span data-ttu-id="660ed-140">Questions ouvertes avec les changements proposés</span><span class="sxs-lookup"><span data-stu-id="660ed-140">Open Issues with Proposed Changes</span></span>

### <a name="syntax-for-relational-operators"></a><span data-ttu-id="660ed-141">Syntaxe pour les opérateurs relationnels</span><span class="sxs-lookup"><span data-stu-id="660ed-141">Syntax for relational operators</span></span>

<span data-ttu-id="660ed-142">Sont `and` `or`, `not` , et une sorte de mot-clé contextuel?</span><span class="sxs-lookup"><span data-stu-id="660ed-142">Are `and`, `or`, and `not` some kind of contextual keyword?</span></span>  <span data-ttu-id="660ed-143">Si c’est le cas, y *a-t-il*un changement de rupture (par exemple par rapport à leur utilisation en tant que désignateur dans un modèle de déclaration).</span><span class="sxs-lookup"><span data-stu-id="660ed-143">If so, is there a breaking change (e.g. compared to their use as a designator in a *declaration-pattern*).</span></span>

### <a name="semantics-eg-type-for-relational-operators"></a><span data-ttu-id="660ed-144">Sémantique (p. ex. type) pour les opérateurs relationnels</span><span class="sxs-lookup"><span data-stu-id="660ed-144">Semantics (e.g. type) for relational operators</span></span>

<span data-ttu-id="660ed-145">Nous nous attendons à soutenir tous les types primitifs qui peuvent être comparés dans une expression à l’aide d’un opérateur relationnel.</span><span class="sxs-lookup"><span data-stu-id="660ed-145">We expect to support all of the primitive types that can be compared in an expression using a relational operator.</span></span>  <span data-ttu-id="660ed-146">Le sens dans les cas simples est clair</span><span class="sxs-lookup"><span data-stu-id="660ed-146">The meaning in simple cases is clear</span></span>

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

<span data-ttu-id="660ed-147">Mais lorsque l’entrée n’est pas un type aussi primitif, à quel type essayons-nous de le convertir?</span><span class="sxs-lookup"><span data-stu-id="660ed-147">But when the input is not such a primitive type, what type do we attempt to convert it to?</span></span>

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

<span data-ttu-id="660ed-148">Nous avons proposé que lorsque le type d’entrée est déjà un primitif comparable, c’est le type de comparaison.</span><span class="sxs-lookup"><span data-stu-id="660ed-148">We have proposed that when the input type is already a comparable primitive, that is the type of the comparison.</span></span> <span data-ttu-id="660ed-149">Cependant, lorsque l’entrée n’est pas un primitif comparable, nous traitons le relationnel comme incluant un test de type implicite au type de la constante sur le côté droit de la relationnelle.</span><span class="sxs-lookup"><span data-stu-id="660ed-149">However, when the input is not a comparable primitive, we treat the relational as including an implicit type test to the type of the constant on the right-hand-side of the relational.</span></span>  <span data-ttu-id="660ed-150">Si le programmeur a l’intention de prendre en charge plus d’un type d’entrée, cela doit être fait explicitement :</span><span class="sxs-lookup"><span data-stu-id="660ed-150">If the programmer intends to support more than one input type, that must be done explicitly:</span></span>

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a><span data-ttu-id="660ed-151">Informations de type fluide de la gauche à droite de`and`</span><span class="sxs-lookup"><span data-stu-id="660ed-151">Flowing type information from the left to the right of `and`</span></span>

<span data-ttu-id="660ed-152">Il a été suggéré que `and` lorsque vous écrivez un combinateur, les informations de type apprises à gauche sur le type de haut niveau pourraient circuler vers la droite.</span><span class="sxs-lookup"><span data-stu-id="660ed-152">It has been suggested that when you write an `and` combinator, type information learned on the left about the top-level type could flow to the right.</span></span>  <span data-ttu-id="660ed-153">Par exemple</span><span class="sxs-lookup"><span data-stu-id="660ed-153">For example</span></span>

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

<span data-ttu-id="660ed-154">Ici, le *type d’entrée* au deuxième modèle est réduit par `and`le type de *rétrécissement* des exigences de gauche de la .</span><span class="sxs-lookup"><span data-stu-id="660ed-154">Here, the *input type* to the second pattern is narrowed by the *type narrowing* requirements of left of the `and`.</span></span>  <span data-ttu-id="660ed-155">Nous définirions la sémantique de rétrécissement de type pour tous les modèles comme suit.</span><span class="sxs-lookup"><span data-stu-id="660ed-155">We would define type narrowing semantics for all patterns as follows.</span></span>  <span data-ttu-id="660ed-156">Le *type rétréci* d’un modèle `P` est défini comme suit :</span><span class="sxs-lookup"><span data-stu-id="660ed-156">The *narrowed type* of a pattern `P` is defined as follows:</span></span>
1. <span data-ttu-id="660ed-157">S’il `P` s’agit d’un modèle de type, le *type rétréci* est le type du type de modèle.</span><span class="sxs-lookup"><span data-stu-id="660ed-157">If `P` is a type pattern, the *narrowed type* is the type of the type pattern's type.</span></span>
2. <span data-ttu-id="660ed-158">S’il `P` s’agit d’un modèle de déclaration, le *type rétréci* est le type du type de modèle de déclaration.</span><span class="sxs-lookup"><span data-stu-id="660ed-158">If `P` is a declaration pattern, the *narrowed type* is the type of the declaration pattern's type.</span></span>
3. <span data-ttu-id="660ed-159">S’il `P` s’agit d’un modèle récursif qui donne un type explicite, le *type rétréci* est ce type.</span><span class="sxs-lookup"><span data-stu-id="660ed-159">If `P` is a recursive pattern that gives an explicit type, the *narrowed type* is that type.</span></span>
4. <span data-ttu-id="660ed-160">Si `P` est un modèle constant où la constante n’est pas la constante nulle et où l’expression n’a pas *de conversion d’expression constante* au type *d’entrée,* le *type rétréci* est le type de la constante.</span><span class="sxs-lookup"><span data-stu-id="660ed-160">If `P` is a constant pattern where the constant is not the null constant and where the expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
5. <span data-ttu-id="660ed-161">S’il `P` s’agit d’un modèle relationnel où l’expression constante n’a pas *de conversion d’expression constante* au type *d’entrée,* le *type rétréci* est le type de la constante.</span><span class="sxs-lookup"><span data-stu-id="660ed-161">If `P` is a relational pattern where the constant expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
6. <span data-ttu-id="660ed-162">`P` S’il `or` s’agit d’un modèle, le *type rétréci* est le type commun du *type rétréci* des sous-délinquants si un tel type commun existe.</span><span class="sxs-lookup"><span data-stu-id="660ed-162">If `P` is an `or` pattern, the *narrowed type* is the common type of the *narrowed type* of the subpatterns if such a common type exists.</span></span> <span data-ttu-id="660ed-163">À cette fin, l’algorithme de type commun ne considère que l’identité, la boxe `or` et les conversions implicites de référence, et il considère tous les sous-délinquants d’une séquence de modèles (ignorant les modèles parenthés).</span><span class="sxs-lookup"><span data-stu-id="660ed-163">For this purpose, the common type algorithm considers only identity, boxing, and implicit reference conversions, and it considers all subpatterns of a sequence of `or` patterns (ignoring parenthesized patterns).</span></span>
7. <span data-ttu-id="660ed-164">`P` S’il `and` s’agit d’un modèle, le *type rétréci* est le *type rétréci* du bon modèle.</span><span class="sxs-lookup"><span data-stu-id="660ed-164">If `P` is an `and` pattern, the *narrowed type* is the *narrowed type* of the right pattern.</span></span> <span data-ttu-id="660ed-165">En outre, le *type rétréci* du modèle gauche est le type *d’entrée* du modèle droit.</span><span class="sxs-lookup"><span data-stu-id="660ed-165">Moreover, the *narrowed type* of the left pattern is the *input type* of the right pattern.</span></span>
8. <span data-ttu-id="660ed-166">Sinon, le type `P` `P` *rétréci* est 'type d’entrée.</span><span class="sxs-lookup"><span data-stu-id="660ed-166">Otherwise the *narrowed type* of `P` is `P`'s input type.</span></span>

### <a name="variable-definitions-and-definite-assignment"></a><span data-ttu-id="660ed-167">Définitions variables et affectation définitive</span><span class="sxs-lookup"><span data-stu-id="660ed-167">Variable definitions and definite assignment</span></span>

<span data-ttu-id="660ed-168">L’ajout `or` `not` et les modèles crée de nouveaux problèmes intéressants autour des variables de modèle et de l’affectation définie.</span><span class="sxs-lookup"><span data-stu-id="660ed-168">The addition of `or` and `not` patterns creates some interesting new problems around pattern variables and definite assignment.</span></span>  <span data-ttu-id="660ed-169">Étant donné que les variables peuvent normalement être déclarées tout au `or` plus une fois, il semblerait que toute variable de modèle déclarée d’un côté d’un modèle ne serait pas définitivement attribuée lorsque le modèle correspond.</span><span class="sxs-lookup"><span data-stu-id="660ed-169">Since variables can normally be declared at most once, it would seem any pattern variable declared on one side of an `or` pattern would not be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="660ed-170">De même, on `not` ne s’attendrait pas à ce qu’une variable déclarée à l’intérieur d’un modèle soit définitivement attribuée lorsque le modèle correspond.</span><span class="sxs-lookup"><span data-stu-id="660ed-170">Similarly, a variable declared inside a `not` pattern would not be expected to be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="660ed-171">La façon la plus simple d’y remédier est d’interdire de déclarer des variables de modèle dans ces contextes.</span><span class="sxs-lookup"><span data-stu-id="660ed-171">The simplest way to address this is to forbid declaring pattern variables in these contexts.</span></span>  <span data-ttu-id="660ed-172">Toutefois, cela peut être trop restrictif.</span><span class="sxs-lookup"><span data-stu-id="660ed-172">However, this may be too restrictive.</span></span>  <span data-ttu-id="660ed-173">Il y a d’autres approches à considérer.</span><span class="sxs-lookup"><span data-stu-id="660ed-173">There are other approaches to consider.</span></span>

<span data-ttu-id="660ed-174">Un scénario qui mérite d’être envisagé est le</span><span class="sxs-lookup"><span data-stu-id="660ed-174">One scenario that is worth considering is this</span></span>

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

<span data-ttu-id="660ed-175">Cela ne fonctionne pas aujourd’hui parce que, pour un *est-modèle-expression,* les variables de modèle sont considérés *définitivement attribués* seulement où *l’expression est-modèle* est vrai ("définitivement attribué quand vrai").</span><span class="sxs-lookup"><span data-stu-id="660ed-175">This does not work today because, for an *is-pattern-expression*, the pattern variables are considered *definitely assigned* only where the *is-pattern-expression* is true ("definitely assigned when true").</span></span>

<span data-ttu-id="660ed-176">Soutenir cela serait plus simple (du point de vue du programmeur) `if` que d’ajouter un soutien à une déclaration de condition niée.</span><span class="sxs-lookup"><span data-stu-id="660ed-176">Supporting this would be simpler (from the programmer's perspective) than also adding support for a negated-condition `if` statement.</span></span>  <span data-ttu-id="660ed-177">Même si nous ajoutons un tel soutien, les programmeurs se demanderaient pourquoi l’extrait ci-dessus ne fonctionne pas.</span><span class="sxs-lookup"><span data-stu-id="660ed-177">Even if we add such support, programmers would wonder why the above snippet does not work.</span></span>  <span data-ttu-id="660ed-178">D’autre part, le même `switch` scénario dans un est moins logique, car il n’y a pas de point correspondant dans le programme où *certainement attribué quand faux* serait significatif.</span><span class="sxs-lookup"><span data-stu-id="660ed-178">On the other hand, the same scenario in a `switch` makes less sense, as there is no corresponding point in the program where *definitely assigned when false* would be meaningful.</span></span>  <span data-ttu-id="660ed-179">Permettrons-nous cela dans une *expression de modèle,* mais pas dans d’autres contextes où les modèles sont autorisés?</span><span class="sxs-lookup"><span data-stu-id="660ed-179">Would we permit this in an *is-pattern-expression* but not in other contexts where patterns are permitted?</span></span>  <span data-ttu-id="660ed-180">Cela semble irrégulier.</span><span class="sxs-lookup"><span data-stu-id="660ed-180">That seems irregular.</span></span>

<span data-ttu-id="660ed-181">A cela s’inttûment le problème de l’affectation définitive dans un *modèle disjonctif*.</span><span class="sxs-lookup"><span data-stu-id="660ed-181">Related to this is the problem of definite assignment in a *disjunctive-pattern*.</span></span>

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

<span data-ttu-id="660ed-182">Nous ne `i` nous attendrions à être définitivement affectés lorsque l’entrée n’est pas nulle.</span><span class="sxs-lookup"><span data-stu-id="660ed-182">We would only expect `i` to be definitely assigned when the input is not zero.</span></span>  <span data-ttu-id="660ed-183">Mais puisque nous ne savons pas si l’entrée `i` est nulle ou non à l’intérieur du bloc, n’est pas définitivement attribué.</span><span class="sxs-lookup"><span data-stu-id="660ed-183">But since we don't know whether the input is zero or not inside the block, `i` is not definitely assigned.</span></span>  <span data-ttu-id="660ed-184">Cependant, que se `i` passe-t-il si nous permettons d’être déclarés dans différents modèles mutuellement exclusifs?</span><span class="sxs-lookup"><span data-stu-id="660ed-184">However, what if we permit `i` to be declared in different mutually exclusive patterns?</span></span>

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

<span data-ttu-id="660ed-185">Ici, la `i` variable est définitivement assignée à l’intérieur du bloc, et prend la valeur de l’autre élément du tuple quand un élément zéro est trouvé.</span><span class="sxs-lookup"><span data-stu-id="660ed-185">Here, the variable `i` is definitely assigned inside the block, and takes it value from the other element of the tuple when a zero element is found.</span></span>

<span data-ttu-id="660ed-186">Il a également été suggéré de permettre que les variables soient (multipliées) définies dans tous les cas d’un bloc de cas :</span><span class="sxs-lookup"><span data-stu-id="660ed-186">It has also been suggested to permit variables to be (multiply) defined in every case of a case block:</span></span>

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

<span data-ttu-id="660ed-187">Pour que tout cela fonctionne, nous devrons définir soigneusement où de telles définitions multiples sont autorisées et dans quelles conditions une telle variable est considérée comme définitivement attribuée.</span><span class="sxs-lookup"><span data-stu-id="660ed-187">To make any of this work, we would have to carefully define where such multiple definitions are permitted and under what conditions such a variable is considered definitely assigned.</span></span>

<span data-ttu-id="660ed-188">Si nous décidons de reporter ce travail à plus tard (ce que je conseille), nous pourrions dire dans C 9</span><span class="sxs-lookup"><span data-stu-id="660ed-188">Should we elect to defer such work until later (which I advise), we could say in C# 9</span></span>
- <span data-ttu-id="660ed-189">sous `not` un `or`ou , variables de modèle ne peuvent pas être déclarés.</span><span class="sxs-lookup"><span data-stu-id="660ed-189">beneath a `not` or `or`, pattern variables may not be declared.</span></span>

<span data-ttu-id="660ed-190">Ensuite, nous aurions le temps de développer une expérience qui donnerait un aperçu de la valeur possible de se détendre plus tard.</span><span class="sxs-lookup"><span data-stu-id="660ed-190">Then, we would have time to develop some experience that would provide insight into the possible value of relaxing that later.</span></span>

### <a name="diagnostics-subsumption-and-exhaustiveness"></a><span data-ttu-id="660ed-191">Diagnostics, sous-ssumption et exhaustivité</span><span class="sxs-lookup"><span data-stu-id="660ed-191">Diagnostics, subsumption, and exhaustiveness</span></span>

<span data-ttu-id="660ed-192">Ces nouveaux formulaires de modèle introduisent de nombreuses nouvelles possibilités d’erreur de programmeur diagnostique.</span><span class="sxs-lookup"><span data-stu-id="660ed-192">These new pattern forms introduce many new opportunities for diagnosable programmer error.</span></span>  <span data-ttu-id="660ed-193">Nous devrons décider quels types d’erreurs nous diagnostiquerons et comment le faire.</span><span class="sxs-lookup"><span data-stu-id="660ed-193">We will need to decide what kinds of errors we will diagnose, and how to do so.</span></span>  <span data-ttu-id="660ed-194">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="660ed-194">Here are some examples:</span></span>

``` csharp
case >= 0 and <= 100D:
```

<span data-ttu-id="660ed-195">Ce cas ne peut jamais correspondre (parce que l’entrée ne peut pas être à la fois un `int` et un `double`).</span><span class="sxs-lookup"><span data-stu-id="660ed-195">This case can never match (because the input cannot be both an `int` and a `double`).</span></span>  <span data-ttu-id="660ed-196">Nous avons déjà une erreur lorsque nous détectons un cas qui ne peut jamais correspondre, mais son libellé ("Le boîtier de commutateur a déjà été traité par un cas précédent" et "Le modèle a déjà été manipulé par un bras précédent de l’expression de commutateur") peut être trompeur dans de nouveaux scénarios.</span><span class="sxs-lookup"><span data-stu-id="660ed-196">We already have an error when we detect a case that can never match, but its wording ("The switch case has already been handled by a previous case" and "The pattern has already been handled by a previous arm of the switch expression") may be misleading in new scenarios.</span></span>  <span data-ttu-id="660ed-197">Nous devrons peut-être modifier le libellé pour simplement dire que le modèle ne correspondra jamais à l’entrée.</span><span class="sxs-lookup"><span data-stu-id="660ed-197">We may have to modify the wording to just say that the pattern will never match the input.</span></span>

``` csharp
case 1 and 2:
```

<span data-ttu-id="660ed-198">De même, ce serait une erreur `1` parce `2`qu’une valeur ne peut pas être à la fois et .</span><span class="sxs-lookup"><span data-stu-id="660ed-198">Similarly, this would be an error because a value cannot be both `1` and `2`.</span></span>

``` csharp
case 1 or 2 or 3 or 1:
```

<span data-ttu-id="660ed-199">Ce cas est possible de `or 1` correspondre, mais à la fin n’ajoute aucun sens au modèle.</span><span class="sxs-lookup"><span data-stu-id="660ed-199">This case is possible to match, but the `or 1` at the end adds no meaning to the pattern.</span></span>  <span data-ttu-id="660ed-200">Je suggère que nous devrions viser à produire une erreur chaque fois qu’une certaine conjonction ou disjonction d’un modèle composé ne définit pas une variable de modèle ou n’affecte pas l’ensemble des valeurs assorties.</span><span class="sxs-lookup"><span data-stu-id="660ed-200">I suggest we should aim to produce an error whenever some conjunct or disjunct of a compound pattern does not either define a pattern variable or affect the set of matched values.</span></span>

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

<span data-ttu-id="660ed-201">En `0 or 1 or` l’espèce, rien n’ajoute à la deuxième affaire, car ces valeurs auraient été traitées par le premier cas.</span><span class="sxs-lookup"><span data-stu-id="660ed-201">Here, `0 or 1 or` adds nothing to the second case, as those values would have been handled by the first case.</span></span>  <span data-ttu-id="660ed-202">Cela mérite aussi une erreur.</span><span class="sxs-lookup"><span data-stu-id="660ed-202">This too deserves an error.</span></span>

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

<span data-ttu-id="660ed-203">Une expression de commutateur comme celle-ci doit être considérée comme *exhaustive* (elle gère toutes les valeurs d’entrée possibles).</span><span class="sxs-lookup"><span data-stu-id="660ed-203">A switch expression such as this should be considered *exhaustive* (it handles all possible input values).</span></span>

<span data-ttu-id="660ed-204">Dans C 8.0, une expression de `byte` commutateur avec une entrée de type n’est considérée comme exhaustive que si elle contient un bras final dont le motif correspond à tout (un *modèle de rejet* ou *var-modèle).*</span><span class="sxs-lookup"><span data-stu-id="660ed-204">In C# 8.0, a switch expression with an input of type `byte` is only considered exhaustive if it contains a final arm whose pattern matches everything (a *discard-pattern* or *var-pattern*).</span></span>  <span data-ttu-id="660ed-205">Même une expression de commutateur qui `byte` a un bras pour chaque valeur distincte n’est pas considérée comme exhaustive dans C 8.</span><span class="sxs-lookup"><span data-stu-id="660ed-205">Even a switch expression that has an arm for every distinct `byte` value is not considered exhaustive in C# 8.</span></span>  <span data-ttu-id="660ed-206">Afin de gérer correctement l’exhaustivité des modèles relationnels, nous devrons traiter ce cas aussi.</span><span class="sxs-lookup"><span data-stu-id="660ed-206">In order to properly handle exhaustiveness of relational patterns, we will have to handle this case too.</span></span>  <span data-ttu-id="660ed-207">Il s’agira techniquement d’un changement de rupture, mais aucun utilisateur n’est susceptible de remarquer.</span><span class="sxs-lookup"><span data-stu-id="660ed-207">This will technically be a breaking change, but no user is likely to notice.</span></span>
