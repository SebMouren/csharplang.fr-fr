---
ms.openlocfilehash: 3df21c5816be90387a6cd9242e99ba11f43dfd1c
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485167"
---
# <a name="pattern-matching-for-c-7"></a><span data-ttu-id="e99de-101">Critères spéciaux pour C# 7</span><span class="sxs-lookup"><span data-stu-id="e99de-101">Pattern Matching for C# 7</span></span>

<span data-ttu-id="e99de-102">Les extensions de critères C# spéciaux pour permettre un grand nombre des avantages des types de données algébriques et des critères spéciaux des langages fonctionnels, mais d’une manière qui s’intègre parfaitement au langage sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="e99de-102">Pattern matching extensions for C# enable many of the benefits of algebraic data types and pattern matching from functional languages, but in a way that smoothly integrates with the feel of the underlying language.</span></span> <span data-ttu-id="e99de-103">Les fonctionnalités de base sont : les [types d’enregistrements](https://github.com/dotnet/csharplang/blob/master/proposals/records.md), qui sont des types dont la signification sémantique est décrite par la forme des données ; et les critères spéciaux, qui sont un nouveau formulaire d’expression qui permet une décomposition à plusieurs niveaux extrêmement concise de ces types de données.</span><span class="sxs-lookup"><span data-stu-id="e99de-103">The basic features are: [record types](https://github.com/dotnet/csharplang/blob/master/proposals/records.md), which are types whose semantic meaning is described by the shape of the data; and pattern matching, which is a new expression form that enables extremely concise multilevel decomposition of these data types.</span></span> <span data-ttu-id="e99de-104">Les éléments de cette approche sont inspirés par les fonctionnalités associées dans [F#](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Critères spéciaux extensibles via un langage léger") les langages de programmation et [Scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Objets correspondants avec des modèles").</span><span class="sxs-lookup"><span data-stu-id="e99de-104">Elements of this approach are inspired by related features in the programming languages [F#](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Extensible Pattern Matching Via a Lightweight Language") and [Scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Matching Objects With Patterns").</span></span>

## <a name="is-expression"></a><span data-ttu-id="e99de-105">Expression is</span><span class="sxs-lookup"><span data-stu-id="e99de-105">Is expression</span></span>

<span data-ttu-id="e99de-106">L’opérateur `is` est étendu pour tester une expression par rapport à un *modèle*.</span><span class="sxs-lookup"><span data-stu-id="e99de-106">The `is` operator is extended to test an expression against a *pattern*.</span></span>

```antlr
relational_expression
    : relational_expression 'is' pattern
    ;
```

<span data-ttu-id="e99de-107">Cette forme de *relational_expression* s’ajoute aux formulaires existants dans la C# spécification.</span><span class="sxs-lookup"><span data-stu-id="e99de-107">This form of *relational_expression* is in addition to the existing forms in the C# specification.</span></span> <span data-ttu-id="e99de-108">Il s’agit d’une erreur au moment de la compilation si le *relational_expression* à gauche du jeton `is` ne désigne pas une valeur ou n’a pas de type.</span><span class="sxs-lookup"><span data-stu-id="e99de-108">It is a compile-time error if the *relational_expression* to the left of the `is` token does not designate a value or does not have a type.</span></span>

<span data-ttu-id="e99de-109">Chaque *identificateur* du modèle introduit une nouvelle variable locale qui est *définitivement assignée* une fois que l’opérateur `is` est `true` (c’est-à-dire *affecté définitivement lorsque la valeur est true*).</span><span class="sxs-lookup"><span data-stu-id="e99de-109">Every *identifier* of the pattern introduces a new local variable that is *definitely assigned* after the `is` operator is `true` (i.e. *definitely assigned when true*).</span></span>

> <span data-ttu-id="e99de-110">Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié.</span><span class="sxs-lookup"><span data-stu-id="e99de-110">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern*, either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="e99de-111">Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre comme nous le faisons dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type).</span><span class="sxs-lookup"><span data-stu-id="e99de-111">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="e99de-112">Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.</span><span class="sxs-lookup"><span data-stu-id="e99de-112">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

## <a name="patterns"></a><span data-ttu-id="e99de-113">Modèles</span><span class="sxs-lookup"><span data-stu-id="e99de-113">Patterns</span></span>

<span data-ttu-id="e99de-114">Les modèles sont utilisés dans l’opérateur `is` et dans une *switch_Statement* pour exprimer la forme des données par rapport à laquelle les données entrantes doivent être comparées.</span><span class="sxs-lookup"><span data-stu-id="e99de-114">Patterns are used in the `is` operator and in a *switch_statement* to express the shape of data against which incoming data is to be compared.</span></span> <span data-ttu-id="e99de-115">Les modèles peuvent être récursifs afin que certaines parties des données puissent être comparées à des sous-modèles.</span><span class="sxs-lookup"><span data-stu-id="e99de-115">Patterns may be recursive so that parts of the data may be matched against sub-patterns.</span></span>

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    ;

declaration_pattern
    : type simple_designation
    ;

constant_pattern
    : shift_expression
    ;

var_pattern
    : 'var' simple_designation
    ;
```

> <span data-ttu-id="e99de-116">Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié.</span><span class="sxs-lookup"><span data-stu-id="e99de-116">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern*, either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="e99de-117">Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre comme nous le faisons dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type).</span><span class="sxs-lookup"><span data-stu-id="e99de-117">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="e99de-118">Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.</span><span class="sxs-lookup"><span data-stu-id="e99de-118">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

### <a name="declaration-pattern"></a><span data-ttu-id="e99de-119">Modèle de déclaration</span><span class="sxs-lookup"><span data-stu-id="e99de-119">Declaration pattern</span></span>

<span data-ttu-id="e99de-120">Le *declaration_pattern* teste qu’une expression est d’un type donné et la convertit en ce type si le test a la valeur.</span><span class="sxs-lookup"><span data-stu-id="e99de-120">The *declaration_pattern* both tests that an expression is of a given type and casts it to that type if the test succeeds.</span></span> <span data-ttu-id="e99de-121">Si le *simple_designation* est un identificateur, il introduit une variable locale du type donné nommé par l’identificateur donné.</span><span class="sxs-lookup"><span data-stu-id="e99de-121">If the *simple_designation* is an identifier, it introduces a local variable of the given type named by the given identifier.</span></span> <span data-ttu-id="e99de-122">Cette variable locale est *assignée définitivement* lorsque le résultat de l’opération de correspondance de modèle est true.</span><span class="sxs-lookup"><span data-stu-id="e99de-122">That local variable is *definitely assigned* when the result of the pattern-matching operation is true.</span></span>

```antlr
declaration_pattern
    : type simple_designation
    ;
```

<span data-ttu-id="e99de-123">La sémantique d’exécution de cette expression est qu’elle teste le type de Runtime de l’opérande de *relational_expression* gauche par rapport au *type* dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="e99de-123">The runtime semantic of this expression is that it tests the runtime type of the left-hand *relational_expression* operand against the *type* in the pattern.</span></span> <span data-ttu-id="e99de-124">S’il s’agit d’un type de Runtime (ou d’un sous-type), le résultat de l' `is operator` est `true`.</span><span class="sxs-lookup"><span data-stu-id="e99de-124">If it is of that runtime type (or some subtype), the result of the `is operator` is `true`.</span></span> <span data-ttu-id="e99de-125">Elle déclare une nouvelle variable locale nommée par l' *identificateur* auquel est affectée la valeur de l’opérande de gauche lorsque le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="e99de-125">It declares a new local variable named by the *identifier* that is assigned the value of the left-hand operand when the result is `true`.</span></span>

<span data-ttu-id="e99de-126">Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e99de-126">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="e99de-127">Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`.</span><span class="sxs-lookup"><span data-stu-id="e99de-127">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`.</span></span> <span data-ttu-id="e99de-128">Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.</span><span class="sxs-lookup"><span data-stu-id="e99de-128">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

> <span data-ttu-id="e99de-129">Remarque : [dans C# 7,1, nous étendons cela](../csharp-7.1/generics-pattern-match.md) pour permettre une opération de mise en correspondance de modèle si le type d’entrée ou le type `T` est un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="e99de-129">Note: [In C# 7.1 we extend this](../csharp-7.1/generics-pattern-match.md) to permit a pattern-matching operation if either the input type or the type `T` is an open type.</span></span> <span data-ttu-id="e99de-130">Ce paragraphe est remplacé par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e99de-130">This paragraph is replaced by the following:</span></span>
> 
> <span data-ttu-id="e99de-131">Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e99de-131">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="e99de-132">Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`, **ou si `E` ou `T` est un type ouvert**.</span><span class="sxs-lookup"><span data-stu-id="e99de-132">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`, **or if either `E` or `T` is an open type**.</span></span> <span data-ttu-id="e99de-133">Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.</span><span class="sxs-lookup"><span data-stu-id="e99de-133">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

<span data-ttu-id="e99de-134">Le modèle de déclaration est utile pour effectuer des tests de type au moment de l’exécution de types référence et remplace l’idiome</span><span class="sxs-lookup"><span data-stu-id="e99de-134">The declaration pattern is useful for performing run-time type tests of reference types, and replaces the idiom</span></span>

```csharp
var v = expr as Type;
if (v != null) { // code using v }
```

<span data-ttu-id="e99de-135">Avec un peu plus concis</span><span class="sxs-lookup"><span data-stu-id="e99de-135">With the slightly more concise</span></span>

```csharp
if (expr is Type v) { // code using v }
```

<span data-ttu-id="e99de-136">Il s’agit d’une erreur si le *type* est un type valeur Nullable.</span><span class="sxs-lookup"><span data-stu-id="e99de-136">It is an error if *type* is a nullable value type.</span></span>

<span data-ttu-id="e99de-137">Le modèle de déclaration peut être utilisé pour tester des valeurs de types Nullable : une valeur de type `Nullable<T>` (ou un `T`boxed) correspond à un modèle de type `T2 id` si la valeur n’est pas null et que le type de `T2` est `T`, ou un type de base ou une interface de `T`.</span><span class="sxs-lookup"><span data-stu-id="e99de-137">The declaration pattern can be used to test values of nullable types: a value of type `Nullable<T>` (or a boxed `T`) matches a type pattern `T2 id` if the value is non-null and the type of `T2` is `T`, or some base type or interface of `T`.</span></span> <span data-ttu-id="e99de-138">Par exemple, dans le fragment de code</span><span class="sxs-lookup"><span data-stu-id="e99de-138">For example, in the code fragment</span></span>

```csharp
int? x = 3;
if (x is int v) { // code using v }
```

<span data-ttu-id="e99de-139">La condition de l’instruction `if` est `true` au moment de l’exécution et la variable `v` contient la valeur `3` de type `int` à l’intérieur du bloc.</span><span class="sxs-lookup"><span data-stu-id="e99de-139">The condition of the `if` statement is `true` at runtime and the variable `v` holds the value `3` of type `int` inside the block.</span></span>

### <a name="constant-pattern"></a><span data-ttu-id="e99de-140">Modèle de constante</span><span class="sxs-lookup"><span data-stu-id="e99de-140">Constant pattern</span></span>

```antlr
constant_pattern
    : shift_expression
    ;
```

<span data-ttu-id="e99de-141">Un modèle de constante teste la valeur d’une expression par rapport à une valeur constante.</span><span class="sxs-lookup"><span data-stu-id="e99de-141">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="e99de-142">La constante peut être une expression constante, telle qu’un littéral, le nom d’une variable de `const` déclarée, une constante d’énumération ou une expression de `typeof`.</span><span class="sxs-lookup"><span data-stu-id="e99de-142">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant, or a `typeof` expression.</span></span>

<span data-ttu-id="e99de-143">Si les deux *e* et *c* sont des types intégraux, le modèle est considéré comme mis en correspondance si le résultat de l’expression `e == c` est `true`.</span><span class="sxs-lookup"><span data-stu-id="e99de-143">If both *e* and *c* are of integral types, the pattern is considered matched if the result of the expression `e == c` is `true`.</span></span>

<span data-ttu-id="e99de-144">Dans le cas contraire, le modèle est considéré comme correspondant si `object.Equals(e, c)` retourne `true`.</span><span class="sxs-lookup"><span data-stu-id="e99de-144">Otherwise the pattern is considered matching if `object.Equals(e, c)` returns `true`.</span></span> <span data-ttu-id="e99de-145">Dans ce cas, il s’agit d’une erreur au moment de la compilation si le type statique de *e* n’est pas *compatible* avec le type de la constante.</span><span class="sxs-lookup"><span data-stu-id="e99de-145">In this case it is a compile-time error if the static type of *e* is not *pattern compatible* with the type of the constant.</span></span>

### <a name="var-pattern"></a><span data-ttu-id="e99de-146">Modèle var</span><span class="sxs-lookup"><span data-stu-id="e99de-146">Var pattern</span></span>

```antlr
var_pattern
    : 'var' simple_designation
    ;
```

<span data-ttu-id="e99de-147">Une expression *e* correspond à un *var_pattern* toujours.</span><span class="sxs-lookup"><span data-stu-id="e99de-147">An expression *e* matches a *var_pattern* always.</span></span> <span data-ttu-id="e99de-148">En d’autres termes, une correspondance avec un *modèle var* est toujours réussie.</span><span class="sxs-lookup"><span data-stu-id="e99de-148">In other words, a match to a *var pattern* always succeeds.</span></span> <span data-ttu-id="e99de-149">Si le *simple_designation* est un identificateur, alors, au moment de l’exécution, la valeur de *e* est liée à une variable locale nouvellement introduite.</span><span class="sxs-lookup"><span data-stu-id="e99de-149">If the *simple_designation* is an identifier, then at runtime the value of *e* is bound to a newly introduced local variable.</span></span> <span data-ttu-id="e99de-150">Le type de la variable locale est le type statique de *e*.</span><span class="sxs-lookup"><span data-stu-id="e99de-150">The type of the local variable is the static type of *e*.</span></span>

<span data-ttu-id="e99de-151">Il s’agit d’une erreur si le nom `var` est lié à un type.</span><span class="sxs-lookup"><span data-stu-id="e99de-151">It is an error if the name `var` binds to a type.</span></span>

## <a name="switch-statement"></a><span data-ttu-id="e99de-152">Instruction switch</span><span class="sxs-lookup"><span data-stu-id="e99de-152">Switch statement</span></span>

<span data-ttu-id="e99de-153">L’instruction `switch` est étendue pour sélectionner pour l’exécution le premier bloc ayant un modèle associé qui correspond à l' *expression de switch*.</span><span class="sxs-lookup"><span data-stu-id="e99de-153">The `switch` statement is extended to select for execution the first block having an associated pattern that matches the *switch expression*.</span></span>

```antlr
switch_label
    : 'case' complex_pattern case_guard? ':'
    | 'case' constant_expression case_guard? ':'
    | 'default' ':'
    ;

case_guard
    : 'when' expression
    ;
```

<span data-ttu-id="e99de-154">L’ordre dans lequel les modèles sont mis en correspondance n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="e99de-154">The order in which patterns are matched is not defined.</span></span> <span data-ttu-id="e99de-155">Un compilateur est autorisé à faire correspondre les modèles dans le désordre et à réutiliser les résultats des modèles déjà associés pour calculer le résultat de la correspondance d’autres modèles.</span><span class="sxs-lookup"><span data-stu-id="e99de-155">A compiler is permitted to match patterns out of order, and to reuse the results of already matched patterns to compute the result of matching of other patterns.</span></span>

<span data-ttu-id="e99de-156">Si une *protection de casse* est présente, son expression est de type `bool`.</span><span class="sxs-lookup"><span data-stu-id="e99de-156">If a *case-guard* is present, its expression is of type `bool`.</span></span> <span data-ttu-id="e99de-157">Elle est évaluée comme une condition supplémentaire qui doit être satisfaite pour que le cas soit considéré comme respecté.</span><span class="sxs-lookup"><span data-stu-id="e99de-157">It is evaluated as an additional condition that must be satisfied for the case to be considered satisfied.</span></span>

<span data-ttu-id="e99de-158">Il s’agit d’une erreur si un *switch_label* peut n’avoir aucun effet au moment de l’exécution, car son modèle est inclus dans les cas précédents.</span><span class="sxs-lookup"><span data-stu-id="e99de-158">It is an error if a *switch_label* can have no effect at runtime because its pattern is subsumed by previous cases.</span></span> <span data-ttu-id="e99de-159">[TODO : nous devrions être plus précis concernant les techniques que le compilateur est tenu d’utiliser pour atteindre ce jugement.]</span><span class="sxs-lookup"><span data-stu-id="e99de-159">[TODO: We should be more precise about the techniques the compiler is required to use to reach this judgment.]</span></span>

<span data-ttu-id="e99de-160">Une variable de modèle déclarée dans un *switch_label* est assignée de manière définitive dans son bloc cas si et uniquement si ce bloc de cas contient précisément une *switch_label*.</span><span class="sxs-lookup"><span data-stu-id="e99de-160">A pattern variable declared in a *switch_label* is definitely assigned in its case block if and only if that case block contains precisely one *switch_label*.</span></span>

<span data-ttu-id="e99de-161">[TODO : nous devons spécifier quand un *bloc switch* est accessible.]</span><span class="sxs-lookup"><span data-stu-id="e99de-161">[TODO: We should specify when a *switch block* is reachable.]</span></span>

### <a name="scope-of-pattern-variables"></a><span data-ttu-id="e99de-162">Étendue des variables de modèle</span><span class="sxs-lookup"><span data-stu-id="e99de-162">Scope of pattern variables</span></span>

<span data-ttu-id="e99de-163">La portée d’une variable déclarée dans un modèle est la suivante :</span><span class="sxs-lookup"><span data-stu-id="e99de-163">The scope of a variable declared in a pattern is as follows:</span></span>

- <span data-ttu-id="e99de-164">Si le modèle est une étiquette case, alors l’étendue de la variable est le *bloc case*.</span><span class="sxs-lookup"><span data-stu-id="e99de-164">If the pattern is a case label, then the scope of the variable is the *case block*.</span></span>

<span data-ttu-id="e99de-165">Dans le cas contraire, la variable est déclarée dans une expression *is_pattern* , et sa portée est basée sur la construction qui englobe immédiatement l’expression contenant l’expression *is_pattern* comme suit :</span><span class="sxs-lookup"><span data-stu-id="e99de-165">Otherwise the variable is declared in an *is_pattern* expression, and its scope is based on the construct immediately enclosing the expression containing the *is_pattern* expression as follows:</span></span>

- <span data-ttu-id="e99de-166">Si l’expression se trouve dans une expression lambda avec une expression, son étendue est le corps de l’expression lambda.</span><span class="sxs-lookup"><span data-stu-id="e99de-166">If the expression is in an expression-bodied lambda, its scope is the body of the lambda.</span></span>
- <span data-ttu-id="e99de-167">Si l’expression se trouve dans une méthode ou une propriété à expression nulle, son étendue est le corps de la méthode ou de la propriété.</span><span class="sxs-lookup"><span data-stu-id="e99de-167">If the expression is in an expression-bodied method or property, its scope is the body of the method or property.</span></span>
- <span data-ttu-id="e99de-168">Si l’expression se trouve dans une clause `when` d’une clause `catch`, sa portée correspond à cette clause `catch`.</span><span class="sxs-lookup"><span data-stu-id="e99de-168">If the expression is in a `when` clause of a `catch` clause, its scope is that `catch` clause.</span></span>
- <span data-ttu-id="e99de-169">Si l’expression se trouve dans un *iteration_statement*, son étendue est simplement cette instruction.</span><span class="sxs-lookup"><span data-stu-id="e99de-169">If the expression is in an *iteration_statement*, its scope is just that statement.</span></span>
- <span data-ttu-id="e99de-170">Sinon, si l’expression est dans une autre forme d’instruction, son étendue est l’étendue contenant l’instruction.</span><span class="sxs-lookup"><span data-stu-id="e99de-170">Otherwise if the expression is in some other statement form, its scope is the scope containing the statement.</span></span>

<span data-ttu-id="e99de-171">Dans le but de déterminer l’étendue, un *embedded_statement* est considéré comme étant dans sa propre étendue.</span><span class="sxs-lookup"><span data-stu-id="e99de-171">For the purpose of determining the scope, an *embedded_statement* is considered to be in its own scope.</span></span> <span data-ttu-id="e99de-172">Par exemple, la grammaire d’un *if_statement* est</span><span class="sxs-lookup"><span data-stu-id="e99de-172">For example, the grammar for an *if_statement* is</span></span>

``` antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="e99de-173">Par conséquent, si l’instruction contrôlée d’un *if_statement* déclare une variable de modèle, sa portée est limitée à ce *embedded_statement*:</span><span class="sxs-lookup"><span data-stu-id="e99de-173">So if the controlled statement of an *if_statement* declares a pattern variable, its scope is restricted to that *embedded_statement*:</span></span>

```csharp
if (x) M(y is var z);
```

<span data-ttu-id="e99de-174">Dans ce cas, la portée de `z` est l’instruction incorporée `M(y is var z);`.</span><span class="sxs-lookup"><span data-stu-id="e99de-174">In this case the scope of `z` is the embedded statement `M(y is var z);`.</span></span>

<span data-ttu-id="e99de-175">D’autres cas sont des erreurs pour d’autres raisons (par exemple, dans la valeur par défaut d’un paramètre ou un attribut, les deux étant une erreur parce que ces contextes nécessitent une expression constante).</span><span class="sxs-lookup"><span data-stu-id="e99de-175">Other cases are errors for other reasons (e.g. in a parameter's default value or an attribute, both of which are an error because those contexts require a constant expression).</span></span>

> <span data-ttu-id="e99de-176">[Dans C# 7,3, nous avons ajouté les contextes suivants](../csharp-7.3/expression-variables-in-initializers.md) dans lesquels une variable de modèle peut être déclarée :</span><span class="sxs-lookup"><span data-stu-id="e99de-176">[In C# 7.3 we added the following contexts](../csharp-7.3/expression-variables-in-initializers.md) in which a pattern variable may be declared:</span></span>
> - <span data-ttu-id="e99de-177">Si l’expression se trouve dans un *initialiseur de constructeur*, sa portée est l' *initialiseur de constructeur* et le corps du constructeur.</span><span class="sxs-lookup"><span data-stu-id="e99de-177">If the expression is in a *constructor initializer*, its scope is the *constructor initializer* and the constructor's body.</span></span>
> - <span data-ttu-id="e99de-178">Si l’expression se trouve dans un initialiseur de champ, son étendue est le *equals_value_clause* dans lequel elle apparaît.</span><span class="sxs-lookup"><span data-stu-id="e99de-178">If the expression is in a field initializer, its scope is the *equals_value_clause* in which it appears.</span></span>
> - <span data-ttu-id="e99de-179">Si l’expression se trouve dans une clause de requête spécifiée pour être convertie dans le corps d’une expression lambda, son étendue est uniquement cette expression.</span><span class="sxs-lookup"><span data-stu-id="e99de-179">If the expression is in a query clause that is specified to be translated into the body of a lambda, its scope is just that expression.</span></span>

## <a name="changes-to-syntactic-disambiguation"></a><span data-ttu-id="e99de-180">Modifications apportées à la désambiguïsation syntaxique</span><span class="sxs-lookup"><span data-stu-id="e99de-180">Changes to syntactic disambiguation</span></span>

<span data-ttu-id="e99de-181">Dans C# certaines situations, les génériques sont ambigus, et la spécification du langage indique comment résoudre ces ambiguïtés :</span><span class="sxs-lookup"><span data-stu-id="e99de-181">There are situations involving generics where the C# grammar is ambiguous, and the language spec says how to resolve those ambiguities:</span></span>

> #### <a name="7652-grammar-ambiguities"></a><span data-ttu-id="e99de-182">ambiguïtés de la grammaire 7.6.5.2</span><span class="sxs-lookup"><span data-stu-id="e99de-182">7.6.5.2 Grammar ambiguities</span></span>
> <span data-ttu-id="e99de-183">Les productions pour le *nom simple* (§ 7.6.3) et l' *accès aux membres* (§ 7.6.5) peuvent donner lieu à des ambiguïtés dans la grammaire des expressions.</span><span class="sxs-lookup"><span data-stu-id="e99de-183">The productions for *simple-name* (§7.6.3) and *member-access* (§7.6.5) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="e99de-184">Par exemple, l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="e99de-184">For example, the statement:</span></span>
> ```csharp
> F(G<A,B>(7));
> ```
> <span data-ttu-id="e99de-185">peut être interprété comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`.</span><span class="sxs-lookup"><span data-stu-id="e99de-185">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="e99de-186">Elle peut également être interprétée comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.</span><span class="sxs-lookup"><span data-stu-id="e99de-186">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

> <span data-ttu-id="e99de-187">Si une séquence de jetons peut être analysée (en contexte) en tant que *nom simple* (§ 7.6.3), *accès aux membres* (§ 7.6.5) ou *accès aux membres de pointeurs* (§ 18.5.2) se terminant par un *type-argument-List* (§ 4.4.1), le jeton qui suit immédiatement le jeton `>` de fermeture est examiné.</span><span class="sxs-lookup"><span data-stu-id="e99de-187">If a sequence of tokens can be parsed (in context) as a *simple-name* (§7.6.3), *member-access* (§7.6.5), or *pointer-member-access* (§18.5.2) ending with a *type-argument-list* (§4.4.1), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="e99de-188">S’il s’agit de l’un des</span><span class="sxs-lookup"><span data-stu-id="e99de-188">If it is one of</span></span>
> ```none
> (  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
> ```
> <span data-ttu-id="e99de-189">le *type-argument-List* est conservé dans le cadre du *simple-name*, *member-Access* ou *pointer-Member-Access* et toute autre analyse possible de la séquence de jetons est ignorée.</span><span class="sxs-lookup"><span data-stu-id="e99de-189">then the *type-argument-list* is retained as part of the *simple-name*, *member-access* or *pointer-member-access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="e99de-190">Dans le cas contraire, la *liste d’arguments de type* n’est pas considérée comme faisant partie du *simple-name*, *member-Access* ou > *pointer-Member-Access*, même s’il n’y a aucune autre analyse possible de la séquence de jetons.</span><span class="sxs-lookup"><span data-stu-id="e99de-190">Otherwise, the *type-argument-list* is not considered to be part of the *simple-name*, *member-access* or > *pointer-member-access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="e99de-191">Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type-argument-List* dans un *espace de noms ou un nom de type* (§ 3,8).</span><span class="sxs-lookup"><span data-stu-id="e99de-191">Note that these rules are not applied when parsing a *type-argument-list* in a *namespace-or-type-name* (§3.8).</span></span> <span data-ttu-id="e99de-192">L'instruction</span><span class="sxs-lookup"><span data-stu-id="e99de-192">The statement</span></span>
> ```csharp
> F(G<A,B>(7));
> ```
> <span data-ttu-id="e99de-193">en fonction de cette règle, sera interprété comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.</span><span class="sxs-lookup"><span data-stu-id="e99de-193">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="e99de-194">Les instructions</span><span class="sxs-lookup"><span data-stu-id="e99de-194">The statements</span></span>
> ```csharp
> F(G < A, B > 7);
> F(G < A, B >> 7);
> ```
> <span data-ttu-id="e99de-195">est interprété comme un appel à `F` avec deux arguments.</span><span class="sxs-lookup"><span data-stu-id="e99de-195">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="e99de-196">L'instruction</span><span class="sxs-lookup"><span data-stu-id="e99de-196">The statement</span></span>
> ```csharp
> x = F < A > +y;
> ```
> <span data-ttu-id="e99de-197">est interprété comme un opérateur inférieur à, supérieur à et opérateur unaire, comme si l’instruction avait été écrite `x = (F < A) > (+y)`, et non comme un *simple-name* avec un *type-argument-List* suivi d’un opérateur binaire plus.</span><span class="sxs-lookup"><span data-stu-id="e99de-197">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple-name* with a *type-argument-list* followed by a binary plus operator.</span></span> <span data-ttu-id="e99de-198">Dans l’instruction</span><span class="sxs-lookup"><span data-stu-id="e99de-198">In the statement</span></span>
> ```csharp
> x = y is C<T> + z;
> ```
> <span data-ttu-id="e99de-199">les jetons `C<T>` sont interprétés comme un *espace de noms ou un nom* de type avec un *type-argument-List*.</span><span class="sxs-lookup"><span data-stu-id="e99de-199">the tokens `C<T>` are interpreted as a *namespace-or-type-name* with a *type-argument-list*.</span></span>

<span data-ttu-id="e99de-200">Il y a un certain nombre de modifications introduites dans C# 7 qui rendent ces règles de désambiguïsation n’étant plus suffisantes pour gérer la complexité du langage.</span><span class="sxs-lookup"><span data-stu-id="e99de-200">There are a number of changes being introduced in C# 7 that make these disambiguation rules no longer sufficient to handle the complexity of the language.</span></span>

### <a name="out-variable-declarations"></a><span data-ttu-id="e99de-201">Déclarations de variables out</span><span class="sxs-lookup"><span data-stu-id="e99de-201">Out variable declarations</span></span>

<span data-ttu-id="e99de-202">Il est désormais possible de déclarer une variable dans un argument out :</span><span class="sxs-lookup"><span data-stu-id="e99de-202">It is now possible to declare a variable in an out argument:</span></span>

```csharp
M(out Type name);
```

<span data-ttu-id="e99de-203">Toutefois, le type peut être générique :</span><span class="sxs-lookup"><span data-stu-id="e99de-203">However, the type may be generic:</span></span> 

```csharp
M(out A<B> name);
```

<span data-ttu-id="e99de-204">Étant donné que la grammaire de la langue de l’argument utilise l' *expression*, ce contexte est soumis à la règle de désambiguïsation.</span><span class="sxs-lookup"><span data-stu-id="e99de-204">Since the language grammar for the argument uses *expression*, this context is subject to the disambiguation rule.</span></span> <span data-ttu-id="e99de-205">Dans ce cas, le `>` de fermeture est suivi d’un *identificateur*, qui n’est pas l’un des jetons qui lui permet d’être traité comme un *type-argument-List*.</span><span class="sxs-lookup"><span data-stu-id="e99de-205">In this case the closing `>` is followed by an *identifier*, which is not one of the tokens that permits it to be treated as a *type-argument-list*.</span></span> <span data-ttu-id="e99de-206">Je propose donc d' **Ajouter l' *identificateur* à l’ensemble de jetons qui déclenche la désambiguïsation pour une liste d’arguments de *type*.**</span><span class="sxs-lookup"><span data-stu-id="e99de-206">I therefore propose to **add *identifier* to the set of tokens that triggers the disambiguation to a *type-argument-list*.**</span></span>

### <a name="tuples-and-deconstruction-declarations"></a><span data-ttu-id="e99de-207">Déclarations de tuples et de déconstruction</span><span class="sxs-lookup"><span data-stu-id="e99de-207">Tuples and deconstruction declarations</span></span>

<span data-ttu-id="e99de-208">Un littéral de tuple s’exécute exactement dans le même problème.</span><span class="sxs-lookup"><span data-stu-id="e99de-208">A tuple literal runs into exactly the same issue.</span></span> <span data-ttu-id="e99de-209">Considérer l’expression de Tuple</span><span class="sxs-lookup"><span data-stu-id="e99de-209">Consider the tuple expression</span></span>

```csharp
(A < B, C > D, E < F, G > H)
```

<span data-ttu-id="e99de-210">Dans les 6 C# anciennes règles d’analyse d’une liste d’arguments, celle-ci est interprétée comme un tuple avec quatre éléments, en commençant par `A < B` comme premier.</span><span class="sxs-lookup"><span data-stu-id="e99de-210">Under the old C# 6 rules for parsing an argument list, this would parse as a tuple with four elements, starting with `A < B` as the first.</span></span> <span data-ttu-id="e99de-211">Toutefois, lorsque cela apparaît à gauche d’une déconstruction, nous voulons que la désambiguation soit déclenchée par le jeton d' *identificateur* comme décrit ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="e99de-211">However, when this appears on the left of a deconstruction, we want the disambiguation triggered by the *identifier* token as described above:</span></span>

```csharp
(A<B,C> D, E<F,G> H) = e;
```

<span data-ttu-id="e99de-212">Il s’agit d’une déclaration de déconstruction qui déclare deux variables, la première étant de type `A<B,C>` et nommée `D`.</span><span class="sxs-lookup"><span data-stu-id="e99de-212">This is a deconstruction declaration which declares two variables, the first of which is of type `A<B,C>` and named `D`.</span></span> <span data-ttu-id="e99de-213">En d’autres termes, le littéral de tuple contient deux expressions, chacune d’elles étant une expression de déclaration.</span><span class="sxs-lookup"><span data-stu-id="e99de-213">In other words, the tuple literal contains two expressions, each of which is a declaration expression.</span></span>

<span data-ttu-id="e99de-214">Pour simplifier la spécification et le compilateur, je propose que ce littéral de tuple soit analysé comme un tuple à deux éléments à chaque fois qu’il apparaît (qu’il apparaisse ou non sur le côté gauche d’une assignation).</span><span class="sxs-lookup"><span data-stu-id="e99de-214">For simplicity of the specification and compiler, I propose that this tuple literal be parsed as a two-element tuple wherever it appears (whether or not it appears on the left-hand-side of an assignment).</span></span> <span data-ttu-id="e99de-215">Il s’agit d’un résultat naturel de la désambiguïsation qui est décrite dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="e99de-215">That would be a natural result of the disambiguation described in the previous section.</span></span>

### <a name="pattern-matching"></a><span data-ttu-id="e99de-216">Critères spéciaux</span><span class="sxs-lookup"><span data-stu-id="e99de-216">Pattern-matching</span></span>

<span data-ttu-id="e99de-217">Les critères spéciaux présentent un nouveau contexte dans lequel l’ambiguïté de type expression se produit.</span><span class="sxs-lookup"><span data-stu-id="e99de-217">Pattern matching introduces a new context where the expression-type ambiguity arises.</span></span> <span data-ttu-id="e99de-218">Précédemment, le côté droit d’un opérateur de `is` était un type.</span><span class="sxs-lookup"><span data-stu-id="e99de-218">Previously the right-hand-side of an `is` operator was a type.</span></span> <span data-ttu-id="e99de-219">À présent, il peut s’agir d’un type ou d’une expression, et s’il s’agit d’un type, il peut être suivi d’un identificateur.</span><span class="sxs-lookup"><span data-stu-id="e99de-219">Now it can be a type or expression, and if it is a type it may be followed by an identifier.</span></span> <span data-ttu-id="e99de-220">Cela peut, techniquement, modifier la signification du code existant :</span><span class="sxs-lookup"><span data-stu-id="e99de-220">This can, technically, change the meaning of existing code:</span></span>

```csharp
var x = e is T < A > B;
```

<span data-ttu-id="e99de-221">Cela peut être analysé dans les règles C# 6 comme</span><span class="sxs-lookup"><span data-stu-id="e99de-221">This could be parsed under C#6 rules as</span></span>

```csharp
var x = ((e is T) < A) > B;
```

<span data-ttu-id="e99de-222">mais sous les règles C# 7 (avec la désambiguïsation proposée ci-dessus) sont analysées comme</span><span class="sxs-lookup"><span data-stu-id="e99de-222">but under under C#7 rules (with the disambiguation proposed above) would be parsed as</span></span>

```csharp
var x = e is T<A> B;
```

<span data-ttu-id="e99de-223">qui déclare une variable `B` de type `T<A>`.</span><span class="sxs-lookup"><span data-stu-id="e99de-223">which declares a variable `B` of type `T<A>`.</span></span> <span data-ttu-id="e99de-224">Heureusement, les compilateurs natif et Roslyn ont un bogue qui leur donne une erreur de syntaxe sur le code C# 6.</span><span class="sxs-lookup"><span data-stu-id="e99de-224">Fortunately, the native and Roslyn compilers have a bug whereby they give a syntax error on the C#6 code.</span></span> <span data-ttu-id="e99de-225">Par conséquent, cette modification avec rupture particulière n’est pas un problème.</span><span class="sxs-lookup"><span data-stu-id="e99de-225">Therefore this particular breaking change is not a concern.</span></span>

<span data-ttu-id="e99de-226">La mise en correspondance de modèle introduit des jetons supplémentaires qui doivent conduire la résolution d’ambiguïté à la sélection d’un type.</span><span class="sxs-lookup"><span data-stu-id="e99de-226">Pattern-matching introduces additional tokens that should drive the ambiguity resolution toward selecting a type.</span></span> <span data-ttu-id="e99de-227">Les exemples suivants de code C# 6 valide existant seraient rompus sans règles de désambiguïsation supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="e99de-227">The following examples of existing valid C#6 code would be broken without additional disambiguation rules:</span></span>

```csharp
var x = e is A<B> && f;            // &&
var x = e is A<B> || f;            // ||
var x = e is A<B> & f;             // &
var x = e is A<B>[];               // [
```

### <a name="proposed-change-to-the-disambiguation-rule"></a><span data-ttu-id="e99de-228">Proposition de modification de la règle de désambiguïsation</span><span class="sxs-lookup"><span data-stu-id="e99de-228">Proposed change to the disambiguation rule</span></span>

<span data-ttu-id="e99de-229">Je propose de modifier la spécification pour modifier la liste des jetons disambiguating à partir de</span><span class="sxs-lookup"><span data-stu-id="e99de-229">I propose to revise the specification to change the list of disambiguating tokens from</span></span>

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```

<span data-ttu-id="e99de-230">to</span><span class="sxs-lookup"><span data-stu-id="e99de-230">to</span></span>

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [
```

<span data-ttu-id="e99de-231">Et, dans certains contextes, nous traitons l' *identificateur* en tant que jeton disambiguating.</span><span class="sxs-lookup"><span data-stu-id="e99de-231">And, in certain contexts, we treat *identifier* as a disambiguating token.</span></span> <span data-ttu-id="e99de-232">Ces contextes sont là où la séquence de jetons enlevée est immédiatement précédée de l’un des mots clés `is`, `case`ou `out`, ou se présente lors de l’analyse du premier élément d’un littéral de Tuple (auquel cas les jetons sont précédés de `(` ou `:` et l’identificateur est suivi d’un `,`) ou d’un élément suivant d’un littéral de Tuple.</span><span class="sxs-lookup"><span data-stu-id="e99de-232">Those contexts are where the sequence of tokens being disambiguated is immediately preceded by one of the keywords `is`, `case`, or `out`, or arises while parsing the first element of a tuple literal (in which case the tokens are preceded by `(` or `:` and the identifier is followed by a `,`) or a subsequent element of a tuple literal.</span></span>

### <a name="modified-disambiguation-rule"></a><span data-ttu-id="e99de-233">Règle de désambiguïté modifiée</span><span class="sxs-lookup"><span data-stu-id="e99de-233">Modified disambiguation rule</span></span>

<span data-ttu-id="e99de-234">La règle de désambiguïté révisée serait semblable à celle-ci</span><span class="sxs-lookup"><span data-stu-id="e99de-234">The revised disambiguation rule would be something like this</span></span>

> <span data-ttu-id="e99de-235">Si une séquence de jetons peut être analysée (en contexte) comme un *nom simple* (§ 7.6.3), un *accès aux membres* (§ 7.6.5) ou un *accès aux membres de pointeurs* (§ 18.5.2) se terminant par un *type-argument-List* (§ 4.4.1), le jeton qui suit immédiatement le jeton de `>` de fermeture est examiné pour voir s’il est</span><span class="sxs-lookup"><span data-stu-id="e99de-235">If a sequence of tokens can be parsed (in context) as a *simple-name* (§7.6.3), *member-access* (§7.6.5), or *pointer-member-access* (§18.5.2) ending with a *type-argument-list* (§4.4.1), the token immediately following the closing `>` token is examined, to see if it is</span></span>
> - <span data-ttu-id="e99de-236">L’une des `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [`; ni</span><span class="sxs-lookup"><span data-stu-id="e99de-236">One of `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [`; or</span></span>
> - <span data-ttu-id="e99de-237">L’un des opérateurs relationnels `<  >  <=  >=  is as`; ni</span><span class="sxs-lookup"><span data-stu-id="e99de-237">One of the relational operators `<  >  <=  >=  is as`; or</span></span>
> - <span data-ttu-id="e99de-238">Mot clé de requête contextuelle apparaissant à l’intérieur d’une expression de requête ; ni</span><span class="sxs-lookup"><span data-stu-id="e99de-238">A contextual query keyword appearing inside a query expression; or</span></span>
> - <span data-ttu-id="e99de-239">Dans certains contextes, nous traitons l' *identificateur* en tant que jeton disambiguating.</span><span class="sxs-lookup"><span data-stu-id="e99de-239">In certain contexts, we treat *identifier* as a disambiguating token.</span></span> <span data-ttu-id="e99de-240">Ces contextes sont là où la séquence de jetons enlevée est immédiatement précédée de l’un des mots clés `is`, `case` ou `out`, ou se présente lors de l’analyse du premier élément d’un littéral de Tuple (auquel cas les jetons sont précédés de `(` ou `:` et l’identificateur est suivi d’un `,`) ou d’un élément suivant d’un littéral de Tuple.</span><span class="sxs-lookup"><span data-stu-id="e99de-240">Those contexts are where the sequence of tokens being disambiguated is immediately preceded by one of the keywords `is`, `case` or `out`, or arises while parsing the first element of a tuple literal (in which case the tokens are preceded by `(` or `:` and the identifier is followed by a `,`) or a subsequent element of a tuple literal.</span></span>
> 
> <span data-ttu-id="e99de-241">Si le jeton suivant figure parmi cette liste, ou un identificateur dans un tel contexte, le *type-argument-List* est conservé dans le cadre du *simple-name*, de l' *accès aux membres* ou du *pointeur-Member Access* , et toute autre analyse possible de la séquence de jetons est ignorée.</span><span class="sxs-lookup"><span data-stu-id="e99de-241">If the following token is among this list, or an identifier in such a context, then the *type-argument-list* is retained as part of the *simple-name*, *member-access* or  *pointer-member-access* and any other possible parse of the sequence of tokens is discarded.</span></span>  <span data-ttu-id="e99de-242">Dans le cas contraire, la *liste d’arguments de type* n’est pas considérée comme faisant partie du *simple-name*, d' *accès aux membres* ou d' *accès aux membres du pointeur*, même s’il n’y a pas d’autre analyse possible de la séquence de jetons.</span><span class="sxs-lookup"><span data-stu-id="e99de-242">Otherwise, the *type-argument-list* is not considered to be part of the *simple-name*, *member-access* or *pointer-member-access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="e99de-243">Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type-argument-List* dans un *espace de noms ou un nom de type* (§ 3,8).</span><span class="sxs-lookup"><span data-stu-id="e99de-243">Note that these rules are not applied when parsing a *type-argument-list* in a *namespace-or-type-name* (§3.8).</span></span>

### <a name="breaking-changes-due-to-this-proposal"></a><span data-ttu-id="e99de-244">Modifications avec rupture en raison de cette proposition</span><span class="sxs-lookup"><span data-stu-id="e99de-244">Breaking changes due to this proposal</span></span>

<span data-ttu-id="e99de-245">Aucune modification avec rupture n’est connue en raison de cette règle de désambiguïsation d’ambiguïté proposée.</span><span class="sxs-lookup"><span data-stu-id="e99de-245">No breaking changes are known due to this proposed disambiguation rule.</span></span>

### <a name="interesting-examples"></a><span data-ttu-id="e99de-246">Exemples intéressants</span><span class="sxs-lookup"><span data-stu-id="e99de-246">Interesting examples</span></span>

<span data-ttu-id="e99de-247">Voici quelques résultats intéressants de ces règles de désambiguïsation :</span><span class="sxs-lookup"><span data-stu-id="e99de-247">Here are some interesting results of these disambiguation rules:</span></span>

<span data-ttu-id="e99de-248">L’expression `(A < B, C > D)` est un tuple avec deux éléments, chacun étant une comparaison.</span><span class="sxs-lookup"><span data-stu-id="e99de-248">The expression `(A < B, C > D)` is a tuple with two elements, each a comparison.</span></span>

<span data-ttu-id="e99de-249">L’expression `(A<B,C> D, E)` est un tuple avec deux éléments, le premier étant une expression de déclaration.</span><span class="sxs-lookup"><span data-stu-id="e99de-249">The expression `(A<B,C> D, E)` is a tuple with two elements, the first of which is a declaration expression.</span></span>

<span data-ttu-id="e99de-250">L’appel de `M(A < B, C > D, E)` a trois arguments.</span><span class="sxs-lookup"><span data-stu-id="e99de-250">The invocation `M(A < B, C > D, E)` has three arguments.</span></span>

<span data-ttu-id="e99de-251">L’appel de `M(out A<B,C> D, E)` a deux arguments, le premier étant une déclaration de `out`.</span><span class="sxs-lookup"><span data-stu-id="e99de-251">The invocation `M(out A<B,C> D, E)` has two arguments, the first of which is an `out` declaration.</span></span>

<span data-ttu-id="e99de-252">L’expression `e is A<B> C` utilise une expression de déclaration.</span><span class="sxs-lookup"><span data-stu-id="e99de-252">The expression `e is A<B> C` uses a declaration expression.</span></span>

<span data-ttu-id="e99de-253">L’étiquette case `case A<B> C:` utilise une expression de déclaration.</span><span class="sxs-lookup"><span data-stu-id="e99de-253">The case label `case A<B> C:` uses a declaration expression.</span></span>

## <a name="some-examples-of-pattern-matching"></a><span data-ttu-id="e99de-254">Exemples de critères spéciaux</span><span class="sxs-lookup"><span data-stu-id="e99de-254">Some examples of pattern matching</span></span>

### <a name="is-as"></a><span data-ttu-id="e99de-255">Est de</span><span class="sxs-lookup"><span data-stu-id="e99de-255">Is-As</span></span>

<span data-ttu-id="e99de-256">Nous pouvons remplacer l’idiome</span><span class="sxs-lookup"><span data-stu-id="e99de-256">We can replace the idiom</span></span>

```csharp
var v = expr as Type;   
if (v != null) {
    // code using v
}
```

<span data-ttu-id="e99de-257">Avec un peu plus concis et direct</span><span class="sxs-lookup"><span data-stu-id="e99de-257">With the slightly more concise and direct</span></span>

```csharp
if (expr is Type v) {
    // code using v
}
```

### <a name="testing-nullable"></a><span data-ttu-id="e99de-258">Tester la valeur null</span><span class="sxs-lookup"><span data-stu-id="e99de-258">Testing nullable</span></span>

<span data-ttu-id="e99de-259">Nous pouvons remplacer l’idiome</span><span class="sxs-lookup"><span data-stu-id="e99de-259">We can replace the idiom</span></span>

```csharp
Type? v = x?.y?.z;
if (v.HasValue) {
    var value = v.GetValueOrDefault();
    // code using value
}
```

<span data-ttu-id="e99de-260">Avec un peu plus concis et direct</span><span class="sxs-lookup"><span data-stu-id="e99de-260">With the slightly more concise and direct</span></span>

```csharp
if (x?.y?.z is Type value) {
    // code using value
}
```

### <a name="arithmetic-simplification"></a><span data-ttu-id="e99de-261">Simplification arithmétique</span><span class="sxs-lookup"><span data-stu-id="e99de-261">Arithmetic simplification</span></span>

<span data-ttu-id="e99de-262">Supposons que nous définissons un ensemble de types récursifs pour représenter des expressions (selon une proposition distincte) :</span><span class="sxs-lookup"><span data-stu-id="e99de-262">Suppose we define a set of recursive types to represent expressions (per a separate proposal):</span></span>

```csharp
abstract class Expr;
class X() : Expr;
class Const(double Value) : Expr;
class Add(Expr Left, Expr Right) : Expr;
class Mult(Expr Left, Expr Right) : Expr;
class Neg(Expr Value) : Expr;
```

<span data-ttu-id="e99de-263">Nous pouvons maintenant définir une fonction pour calculer la dérivée (non réduite) d’une expression :</span><span class="sxs-lookup"><span data-stu-id="e99de-263">Now we can define a function to compute the (unreduced) derivative of an expression:</span></span>

```csharp
Expr Deriv(Expr e)
{
  switch (e) {
    case X(): return Const(1);
    case Const(*): return Const(0);
    case Add(var Left, var Right):
      return Add(Deriv(Left), Deriv(Right));
    case Mult(var Left, var Right):
      return Add(Mult(Deriv(Left), Right), Mult(Left, Deriv(Right)));
    case Neg(var Value):
      return Neg(Deriv(Value));
  }
}
```

<span data-ttu-id="e99de-264">Une expression simplifie les modèles positionnels :</span><span class="sxs-lookup"><span data-stu-id="e99de-264">An expression simplifier demonstrates positional patterns:</span></span>

```csharp
Expr Simplify(Expr e)
{
  switch (e) {
    case Mult(Const(0), *): return Const(0);
    case Mult(*, Const(0)): return Const(0);
    case Mult(Const(1), var x): return Simplify(x);
    case Mult(var x, Const(1)): return Simplify(x);
    case Mult(Const(var l), Const(var r)): return Const(l*r);
    case Add(Const(0), var x): return Simplify(x);
    case Add(var x, Const(0)): return Simplify(x);
    case Add(Const(var l), Const(var r)): return Const(l+r);
    case Neg(Const(var k)): return Const(-k);
    default: return e;
  }
}
```
