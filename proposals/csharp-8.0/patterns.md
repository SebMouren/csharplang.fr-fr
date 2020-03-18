---
ms.openlocfilehash: fad1425e2871f395a2eb1f39faccbc773d88d6a3
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485062"
---
# <a name="recursive-pattern-matching"></a><span data-ttu-id="779a4-101">Critères spéciaux récursifs</span><span class="sxs-lookup"><span data-stu-id="779a4-101">Recursive Pattern Matching</span></span>

## <a name="summary"></a><span data-ttu-id="779a4-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="779a4-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="779a4-103">Les extensions de critères C# spéciaux pour permettre un grand nombre des avantages des types de données algébriques et des critères spéciaux des langages fonctionnels, mais d’une manière qui s’intègre parfaitement au langage sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="779a4-103">Pattern matching extensions for C# enable many of the benefits of algebraic data types and pattern matching from functional languages, but in a way that smoothly integrates with the feel of the underlying language.</span></span> <span data-ttu-id="779a4-104">Les éléments de cette approche sont inspirés par les fonctionnalités associées dans [F#](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Critères spéciaux extensibles via un langage léger") les langages de programmation et [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Objets correspondants avec des modèles, page 273").</span><span class="sxs-lookup"><span data-stu-id="779a4-104">Elements of this approach are inspired by related features in the programming languages [F#](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Extensible Pattern Matching Via a Lightweight Language") and [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Matching Objects With Patterns, page 273").</span></span>

## <a name="detailed-design"></a><span data-ttu-id="779a4-105">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="779a4-105">Detailed design</span></span>
[design]: #detailed-design

### <a name="is-expression"></a><span data-ttu-id="779a4-106">Expression is</span><span class="sxs-lookup"><span data-stu-id="779a4-106">Is Expression</span></span>

<span data-ttu-id="779a4-107">L’opérateur `is` est étendu pour tester une expression par rapport à un *modèle*.</span><span class="sxs-lookup"><span data-stu-id="779a4-107">The `is` operator is extended to test an expression against a *pattern*.</span></span>

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

<span data-ttu-id="779a4-108">Cette forme de *relational_expression* s’ajoute aux formulaires existants dans la C# spécification.</span><span class="sxs-lookup"><span data-stu-id="779a4-108">This form of *relational_expression* is in addition to the existing forms in the C# specification.</span></span> <span data-ttu-id="779a4-109">Il s’agit d’une erreur au moment de la compilation si le *relational_expression* à gauche du jeton `is` ne désigne pas une valeur ou n’a pas de type.</span><span class="sxs-lookup"><span data-stu-id="779a4-109">It is a compile-time error if the *relational_expression* to the left of the `is` token does not designate a value or does not have a type.</span></span>

<span data-ttu-id="779a4-110">Chaque *identificateur* du modèle introduit une nouvelle variable locale qui est *définitivement assignée* une fois que l’opérateur `is` est `true` (c’est-à-dire *affecté définitivement lorsque la valeur est true*).</span><span class="sxs-lookup"><span data-stu-id="779a4-110">Every *identifier* of the pattern introduces a new local variable that is *definitely assigned* after the `is` operator is `true` (i.e. *definitely assigned when true*).</span></span>

> <span data-ttu-id="779a4-111">Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié.</span><span class="sxs-lookup"><span data-stu-id="779a4-111">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern*, either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="779a4-112">Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre, car nous faisons une expression dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type).</span><span class="sxs-lookup"><span data-stu-id="779a4-112">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do an expression in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="779a4-113">Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.</span><span class="sxs-lookup"><span data-stu-id="779a4-113">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

### <a name="patterns"></a><span data-ttu-id="779a4-114">Modèles</span><span class="sxs-lookup"><span data-stu-id="779a4-114">Patterns</span></span>

<span data-ttu-id="779a4-115">Les modèles sont utilisés dans l’opérateur *is_pattern* , dans une *switch_Statement*et dans une *switch_expression* pour exprimer la forme des données par rapport à laquelle les données entrantes (que nous appelons la valeur d’entrée) doivent être comparées.</span><span class="sxs-lookup"><span data-stu-id="779a4-115">Patterns are used in the *is_pattern* operator, in a *switch_statement*, and in a *switch_expression* to express the shape of data against which incoming data  (which we call the input value) is to be compared.</span></span> <span data-ttu-id="779a4-116">Les modèles peuvent être récursifs afin que certaines parties des données puissent être comparées à des sous-modèles.</span><span class="sxs-lookup"><span data-stu-id="779a4-116">Patterns may be recursive so that parts of the data may be matched against sub-patterns.</span></span>

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' subpatterns? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a><span data-ttu-id="779a4-117">Modèle de déclaration</span><span class="sxs-lookup"><span data-stu-id="779a4-117">Declaration Pattern</span></span>

```antlr
declaration_pattern
    : type simple_designation
    ;
```

<span data-ttu-id="779a4-118">Le *declaration_pattern* teste qu’une expression est d’un type donné et la convertit en ce type si le test a la valeur.</span><span class="sxs-lookup"><span data-stu-id="779a4-118">The *declaration_pattern* both tests that an expression is of a given type and casts it to that type if the test succeeds.</span></span> <span data-ttu-id="779a4-119">Cela peut introduire une variable locale du type donné nommé par l’identificateur donné, si la désignation est un *single_variable_designation*.</span><span class="sxs-lookup"><span data-stu-id="779a4-119">This may introduce a local variable of the given type named by the given identifier, if the designation is a *single_variable_designation*.</span></span> <span data-ttu-id="779a4-120">Cette variable locale est *assignée* de manière définitive lorsque le résultat de l’opération de correspondance de modèle est `true`.</span><span class="sxs-lookup"><span data-stu-id="779a4-120">That local variable is *definitely assigned* when the result of the pattern-matching operation is `true`.</span></span>

<span data-ttu-id="779a4-121">La sémantique d’exécution de cette expression est qu’elle teste le type de Runtime de l’opérande de *relational_expression* gauche par rapport au *type* dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="779a4-121">The runtime semantic of this expression is that it tests the runtime type of the left-hand *relational_expression* operand against the *type* in the pattern.</span></span>  <span data-ttu-id="779a4-122">S’il s’agit d’un type de Runtime (ou d’un sous-type) et non `null`, le résultat de la `is operator` est `true`.</span><span class="sxs-lookup"><span data-stu-id="779a4-122">If it is of that runtime type (or some subtype) and not `null`, the result of the `is operator` is `true`.</span></span>

<span data-ttu-id="779a4-123">Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="779a4-123">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="779a4-124">Une valeur de type statique `E` est dite compatible avec les *modèles* avec un type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`, ou si l’un de ces types est un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="779a4-124">A value of static type `E` is said to be *pattern-compatible* with a type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`, or if one of those types is an open type.</span></span> <span data-ttu-id="779a4-125">Il s’agit d’une erreur de compilation si une entrée de type `E` n’est pas *compatible* avec le *type* de modèle dans un modèle de type auquel elle est associée.</span><span class="sxs-lookup"><span data-stu-id="779a4-125">It is a compile-time error if an input of type `E` is not *pattern-compatible* with the *type* in a type pattern that it is matched with.</span></span>

<span data-ttu-id="779a4-126">Le modèle de type est utile pour effectuer des tests de type au moment de l’exécution de types référence et remplace l’idiome</span><span class="sxs-lookup"><span data-stu-id="779a4-126">The type pattern is useful for performing run-time type tests of reference types, and replaces the idiom</span></span>

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

<span data-ttu-id="779a4-127">Avec un peu plus concis</span><span class="sxs-lookup"><span data-stu-id="779a4-127">With the slightly more concise</span></span>

```csharp
if (expr is Type v) { // code using v
```

<span data-ttu-id="779a4-128">Il s’agit d’une erreur si le *type* est un type valeur Nullable.</span><span class="sxs-lookup"><span data-stu-id="779a4-128">It is an error if *type* is a nullable value type.</span></span>

<span data-ttu-id="779a4-129">Le modèle de type peut être utilisé pour tester des valeurs de types Nullable : une valeur de type `Nullable<T>` (ou un `T`boxed) correspond à un modèle de type `T2 id` si la valeur n’est pas null et que le type de `T2` est `T`, ou un type de base ou une interface de `T`.</span><span class="sxs-lookup"><span data-stu-id="779a4-129">The type pattern can be used to test values of nullable types: a value of type `Nullable<T>` (or a boxed `T`) matches a type pattern `T2 id` if the value is non-null and the type of `T2` is `T`, or some base type or interface of `T`.</span></span> <span data-ttu-id="779a4-130">Par exemple, dans le fragment de code</span><span class="sxs-lookup"><span data-stu-id="779a4-130">For example, in the code fragment</span></span>

```csharp
int? x = 3;
if (x is int v) { // code using v
```

<span data-ttu-id="779a4-131">La condition de l’instruction `if` est `true` au moment de l’exécution et la variable `v` contient la valeur `3` de type `int` à l’intérieur du bloc.</span><span class="sxs-lookup"><span data-stu-id="779a4-131">The condition of the `if` statement is `true` at runtime and the variable `v` holds the value `3` of type `int` inside the block.</span></span> <span data-ttu-id="779a4-132">Après le bloc, la variable `v` est dans la portée, mais elle n’est pas définitivement assignée.</span><span class="sxs-lookup"><span data-stu-id="779a4-132">After the block the variable `v` is in scope but not definitely assigned.</span></span>

#### <a name="constant-pattern"></a><span data-ttu-id="779a4-133">Modèle de constante</span><span class="sxs-lookup"><span data-stu-id="779a4-133">Constant Pattern</span></span>

```antlr
constant_pattern
    : constant_expression
    ;
```

<span data-ttu-id="779a4-134">Un modèle de constante teste la valeur d’une expression par rapport à une valeur constante.</span><span class="sxs-lookup"><span data-stu-id="779a4-134">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="779a4-135">La constante peut être n’importe quelle expression constante, telle qu’un littéral, le nom d’une variable `const` déclarée ou une constante d’énumération.</span><span class="sxs-lookup"><span data-stu-id="779a4-135">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant.</span></span> <span data-ttu-id="779a4-136">Lorsque la valeur d’entrée n’est pas un type ouvert, l’expression constante est implicitement convertie dans le type de l’expression correspondante ; Si le type de la valeur d’entrée n’est pas *compatible* avec le type de l’expression constante, l’opération de correspondance de modèle est une erreur.</span><span class="sxs-lookup"><span data-stu-id="779a4-136">When the input value is not an open type, the constant expression is implicitly converted to the type of the matched expression; if the type of the input value is not *pattern-compatible* with the type of the constant expression, the pattern-matching operation is an error.</span></span>

<span data-ttu-id="779a4-137">Le modèle *c* est considéré comme correspondant à la valeur d’entrée convertie *e* si `object.Equals(c, e)` retournerait `true`.</span><span class="sxs-lookup"><span data-stu-id="779a4-137">The pattern *c* is considered matching the converted input value *e* if `object.Equals(c, e)` would return `true`.</span></span>

<span data-ttu-id="779a4-138">Nous pensons que `e is null` est le moyen le plus courant de tester `null` dans du code récemment écrit, car il ne peut pas appeler un `operator==`défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="779a4-138">We expect to see `e is null` as the most common way to test for `null` in newly written code, as it cannot invoke a user-defined `operator==`.</span></span>

#### <a name="var-pattern"></a><span data-ttu-id="779a4-139">Modèle var</span><span class="sxs-lookup"><span data-stu-id="779a4-139">Var Pattern</span></span>

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

<span data-ttu-id="779a4-140">Si la *désignation* est un *simple_designation*, une expression *e* correspond au modèle.</span><span class="sxs-lookup"><span data-stu-id="779a4-140">If the *designation* is a *simple_designation*, an expression *e* matches the pattern.</span></span> <span data-ttu-id="779a4-141">En d’autres termes, une correspondance avec un *modèle var* est toujours réussie avec un *simple_designation*.</span><span class="sxs-lookup"><span data-stu-id="779a4-141">In other words, a match to a *var pattern* always succeeds with a *simple_designation*.</span></span> <span data-ttu-id="779a4-142">Si le *simple_designation* est un *single_variable_designation*, la valeur de *e* est liée à une variable locale nouvellement introduite.</span><span class="sxs-lookup"><span data-stu-id="779a4-142">If the *simple_designation* is a *single_variable_designation*, the value of *e* is bounds to a newly introduced local variable.</span></span> <span data-ttu-id="779a4-143">Le type de la variable locale est le type statique de *e*.</span><span class="sxs-lookup"><span data-stu-id="779a4-143">The type of the local variable is the static type of *e*.</span></span>

<span data-ttu-id="779a4-144">Si la *désignation* est un *tuple_designation*, le modèle est équivalent à une *positional_pattern* de la forme `(var` *désignation*,... `)` où les *désignations*sont celles qui se trouvent dans le *tuple_designation*.</span><span class="sxs-lookup"><span data-stu-id="779a4-144">If the *designation* is a *tuple_designation*, then the pattern is equivalent to a *positional_pattern* of the form `(var` *designation*, ... `)` where the *designation*s are those found within the *tuple_designation*.</span></span>  <span data-ttu-id="779a4-145">Par exemple, le modèle `var (x, (y, z))` est équivalent à `(var x, (var y, var z))`.</span><span class="sxs-lookup"><span data-stu-id="779a4-145">For example, the pattern `var (x, (y, z))` is equivalent to `(var x, (var y, var z))`.</span></span>

<span data-ttu-id="779a4-146">Il s’agit d’une erreur si le nom `var` est lié à un type.</span><span class="sxs-lookup"><span data-stu-id="779a4-146">It is an error if the name `var` binds to a type.</span></span>

#### <a name="discard-pattern"></a><span data-ttu-id="779a4-147">Supprimer le modèle</span><span class="sxs-lookup"><span data-stu-id="779a4-147">Discard Pattern</span></span>

```antlr
discard_pattern
    : '_'
    ;
```

<span data-ttu-id="779a4-148">Une expression *e* correspond au modèle `_` toujours.</span><span class="sxs-lookup"><span data-stu-id="779a4-148">An expression *e* matches the pattern `_` always.</span></span> <span data-ttu-id="779a4-149">En d’autres termes, chaque expression correspond au modèle d’abandon.</span><span class="sxs-lookup"><span data-stu-id="779a4-149">In other words, every expression matches the discard pattern.</span></span>

<span data-ttu-id="779a4-150">Un modèle d’abandon ne peut pas être utilisé comme modèle d’un *is_pattern_expression*.</span><span class="sxs-lookup"><span data-stu-id="779a4-150">A discard pattern may not be used as the pattern of an *is_pattern_expression*.</span></span>

#### <a name="positional-pattern"></a><span data-ttu-id="779a4-151">Modèle positionnel</span><span class="sxs-lookup"><span data-stu-id="779a4-151">Positional Pattern</span></span>

<span data-ttu-id="779a4-152">Un modèle positionnel vérifie que la valeur d’entrée n’est pas `null`, appelle une méthode de `Deconstruct` appropriée et effectue d’autres critères spéciaux sur les valeurs résultantes.</span><span class="sxs-lookup"><span data-stu-id="779a4-152">A positional pattern checks that the input value is not `null`, invokes an appropriate `Deconstruct` method, and performs further pattern matching on the resulting values.</span></span>  <span data-ttu-id="779a4-153">Il prend également en charge une syntaxe de modèle de type Tuple (sans le type fourni) quand le type de la valeur d’entrée est le même que le type contenant `Deconstruct`, ou si le type de la valeur d’entrée est un type de tuple, ou si le type de la valeur d’entrée est `object` ou `ITuple` et que le type de Runtime de l’expression implémente `ITuple`.</span><span class="sxs-lookup"><span data-stu-id="779a4-153">It also supports a tuple-like pattern syntax (without the type being provided) when the type of the input value is the same as the type containing `Deconstruct`, or if the type of the input value is a tuple type, or if the type of the input value is `object` or `ITuple` and the runtime type of the expression implements `ITuple`.</span></span>

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

<span data-ttu-id="779a4-154">Si le *type* est omis, nous le prenons comme type statique de la valeur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="779a4-154">If the *type* is omitted, we take it to be the static type of the input value.</span></span>

<span data-ttu-id="779a4-155">Pour une correspondance entre une valeur d’entrée et le *type* de modèle `(` *subpattern_list* `)`, une méthode est sélectionnée en recherchant dans *type* les déclarations accessibles de `Deconstruct` et en en sélectionnant une parmi celles-ci en utilisant les mêmes règles que pour la déclaration de déconstruction.</span><span class="sxs-lookup"><span data-stu-id="779a4-155">Given a match of an input value to the pattern *type* `(` *subpattern_list* `)`, a method is selected by searching in *type* for accessible declarations of `Deconstruct` and selecting one among them using the same rules as for the deconstruction declaration.</span></span>

<span data-ttu-id="779a4-156">Il s’agit d’une erreur si un *positional_pattern* omet le type, a un seul sous- *modèle* sans *identificateur*, n’a pas de *property_subpattern* et n’a pas de *simple_designation*.</span><span class="sxs-lookup"><span data-stu-id="779a4-156">It is an error if a *positional_pattern* omits the type, has a single *subpattern* without an *identifier*, has no *property_subpattern* and has no *simple_designation*.</span></span> <span data-ttu-id="779a4-157">Ce ambiguïté entre une *constant_pattern* entre parenthèses et un *positional_pattern*.</span><span class="sxs-lookup"><span data-stu-id="779a4-157">This disambiguates between a *constant_pattern* that is parenthesized and a *positional_pattern*.</span></span>

<span data-ttu-id="779a4-158">Afin d’extraire les valeurs pour les faire correspondre aux modèles de la liste,</span><span class="sxs-lookup"><span data-stu-id="779a4-158">In order to extract the values to match against the patterns in the list,</span></span>
- <span data-ttu-id="779a4-159">Si le *type* a été omis et que le type de la valeur d’entrée est un type tuple, le nombre de sous-modèles doit être le même que la cardinalité du tuple.</span><span class="sxs-lookup"><span data-stu-id="779a4-159">If *type* was omitted and the input value's type is a tuple type, then the number of subpatterns is required to be the same as the cardinality of the tuple.</span></span> <span data-ttu-id="779a4-160">Chaque élément de tuple est mis en correspondance avec le sous- *modèle*correspondant, et la correspondance est établie si tous ces éléments sont correctement exécutés.</span><span class="sxs-lookup"><span data-stu-id="779a4-160">Each tuple element is matched against the corresponding *subpattern*, and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="779a4-161">Si un sous- *modèle* a un *identificateur*, cela doit nommer un élément de tuple à la position correspondante dans le type de Tuple.</span><span class="sxs-lookup"><span data-stu-id="779a4-161">If any *subpattern* has an *identifier*, then that must name a tuple element at the corresponding position in the tuple type.</span></span>
- <span data-ttu-id="779a4-162">Dans le cas contraire, si un `Deconstruct` approprié existe en tant que membre de *type*, il s’agit d’une erreur de compilation si le type de la valeur d’entrée n’est pas *compatible* avec le *type*.</span><span class="sxs-lookup"><span data-stu-id="779a4-162">Otherwise, if a suitable `Deconstruct` exists as a member of *type*, it is a compile-time error if the type of the input value is not *pattern-compatible* with *type*.</span></span> <span data-ttu-id="779a4-163">Au moment de l’exécution, la valeur d’entrée est testée par rapport au *type*.</span><span class="sxs-lookup"><span data-stu-id="779a4-163">At runtime the input value is tested against *type*.</span></span> <span data-ttu-id="779a4-164">En cas d’échec, la correspondance du modèle positionnel échoue.</span><span class="sxs-lookup"><span data-stu-id="779a4-164">If this fails then the positional pattern match fails.</span></span> <span data-ttu-id="779a4-165">Si elle réussit, la valeur d’entrée est convertie vers ce type et `Deconstruct` est appelée avec les nouvelles variables générées par le compilateur pour recevoir les paramètres `out`.</span><span class="sxs-lookup"><span data-stu-id="779a4-165">If it succeeds,  the input value is converted to this type and `Deconstruct` is invoked with fresh compiler-generated variables to receive the `out` parameters.</span></span> <span data-ttu-id="779a4-166">Chaque valeur reçue est mise en correspondance avec le sous- *modèle*correspondant, et la correspondance est établie si toutes ces valeurs sont exécutées correctement.</span><span class="sxs-lookup"><span data-stu-id="779a4-166">Each value that was received is matched against the corresponding *subpattern*, and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="779a4-167">Si un sous- *modèle* a un *identificateur*, alors il doit nommer un paramètre à la position correspondante de `Deconstruct`.</span><span class="sxs-lookup"><span data-stu-id="779a4-167">If any *subpattern* has an *identifier*, then that must name a parameter at the corresponding position of `Deconstruct`.</span></span>
- <span data-ttu-id="779a4-168">Sinon, si le *type* a été omis et que la valeur d’entrée est de type `object` ou `ITuple` ou d’un type qui peut être converti en `ITuple` par une conversion de référence implicite, et qu’aucun *identificateur* n’apparaît parmi les sous-modèles, nous effectuons une correspondance à l’aide de `ITuple`.</span><span class="sxs-lookup"><span data-stu-id="779a4-168">Otherwise if *type* was omitted, and the input value is of type `object` or `ITuple` or some type that can be converted to `ITuple` by an implicit reference conversion, and no *identifier* appears among the subpatterns, then we match using `ITuple`.</span></span>
- <span data-ttu-id="779a4-169">Dans le cas contraire, le modèle est une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="779a4-169">Otherwise the pattern is a compile-time error.</span></span>

<span data-ttu-id="779a4-170">L’ordre dans lequel les sous-modèles sont mis en correspondance au moment de l’exécution n’est pas spécifié, et une erreur de correspondance peut ne pas tenter de faire correspondre tous les sous-modèles.</span><span class="sxs-lookup"><span data-stu-id="779a4-170">The order in which subpatterns are matched at runtime is unspecified, and a failed match may not attempt to match all subpatterns.</span></span>

##### <a name="example"></a><span data-ttu-id="779a4-171">Exemple</span><span class="sxs-lookup"><span data-stu-id="779a4-171">Example</span></span>

<span data-ttu-id="779a4-172">Cet exemple utilise un grand nombre des fonctionnalités décrites dans cette spécification.</span><span class="sxs-lookup"><span data-stu-id="779a4-172">This example uses many of the features described in this specification</span></span>

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a><span data-ttu-id="779a4-173">Modèle de propriété</span><span class="sxs-lookup"><span data-stu-id="779a4-173">Property Pattern</span></span>

<span data-ttu-id="779a4-174">Un modèle de propriété vérifie que la valeur d’entrée n’est pas `null` et fait correspondre de manière récursive les valeurs extraites par l’utilisation de propriétés ou de champs accessibles.</span><span class="sxs-lookup"><span data-stu-id="779a4-174">A property pattern checks that the input value is not `null` and recursively matches values extracted by the use of accessible properties or fields.</span></span>

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

<span data-ttu-id="779a4-175">Il s’agit d’une erreur si un sous- _modèle_ d’un _property_pattern_ ne contient pas d' _identificateur_ (il doit être de la deuxième forme, qui a un _identificateur_).</span><span class="sxs-lookup"><span data-stu-id="779a4-175">It is an error if any _subpattern_ of a _property_pattern_ does not contain an _identifier_ (it must be of the second form, which has an _identifier_).</span></span>  <span data-ttu-id="779a4-176">Une virgule de fin après le dernier sous-modèle est facultative.</span><span class="sxs-lookup"><span data-stu-id="779a4-176">A trailing comma after the last subpattern is optional.</span></span>

<span data-ttu-id="779a4-177">Notez qu’un modèle de vérification null est issu d’un modèle de propriété trivial.</span><span class="sxs-lookup"><span data-stu-id="779a4-177">Note that a null-checking pattern falls out of a trivial property pattern.</span></span> <span data-ttu-id="779a4-178">Pour vérifier si la `s` de chaîne n’est pas null, vous pouvez écrire l’un des formulaires suivants</span><span class="sxs-lookup"><span data-stu-id="779a4-178">To check if the string `s` is non-null, you can write any of the following forms</span></span>

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

<span data-ttu-id="779a4-179">En raison de la correspondance d’une expression *e* au *type* de modèle `{` *property_pattern_list* `}`, il s’agit d’une erreur de compilation si l’expression *e* n’est pas *compatible* avec le type *t* désigné par *type*.</span><span class="sxs-lookup"><span data-stu-id="779a4-179">Given a match of an expression *e* to the pattern *type* `{` *property_pattern_list* `}`, it is a compile-time error if the expression *e* is not *pattern-compatible* with the type *T* designated by *type*.</span></span> <span data-ttu-id="779a4-180">Si le type est absent, nous le prenons comme type statique de *e*.</span><span class="sxs-lookup"><span data-stu-id="779a4-180">If the type is absent, we take it to be the static type of *e*.</span></span> <span data-ttu-id="779a4-181">Si l' *identificateur* est présent, il déclare une variable de modèle *de type type.*</span><span class="sxs-lookup"><span data-stu-id="779a4-181">If the *identifier* is present, it declares a pattern variable of type *type*.</span></span> <span data-ttu-id="779a4-182">Chacun des identificateurs figurant sur le côté gauche de son *property_pattern_list* doit désigner une propriété ou un champ accessible en lecture accessible de *T*. Si le *simple_designation* du *property_pattern* est présent, il définit une variable de modèle de type *T*.</span><span class="sxs-lookup"><span data-stu-id="779a4-182">Each of the identifiers appearing on the left-hand-side of its *property_pattern_list* must designate an accessible readable property or field of *T*. If the *simple_designation* of the *property_pattern* is present, it defines a pattern variable of type *T*.</span></span>

<span data-ttu-id="779a4-183">Au moment de l’exécution, l’expression est testée par rapport à *T*. En cas d’échec, la correspondance du modèle de propriété échoue et le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="779a4-183">At runtime, the expression is tested against *T*. If this fails then the property pattern match fails and the result is `false`.</span></span> <span data-ttu-id="779a4-184">Si elle est réussie, chaque *property_subpattern* champ ou propriété est lu et sa valeur correspond à son modèle correspondant.</span><span class="sxs-lookup"><span data-stu-id="779a4-184">If it succeeds, then each *property_subpattern* field or property is read and its value matched against its corresponding pattern.</span></span> <span data-ttu-id="779a4-185">Le résultat de la correspondance entière est `false` uniquement si le résultat de l’une d’entre elles est `false`.</span><span class="sxs-lookup"><span data-stu-id="779a4-185">The result of the whole match is `false` only if the result of any of these is `false`.</span></span> <span data-ttu-id="779a4-186">L’ordre dans lequel les sous-modèles sont mis en correspondance n’est pas spécifié, et une correspondance qui a échoué peut ne pas correspondre à tous les sous-modèles au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="779a4-186">The order in which subpatterns are matched is not specified, and a failed match may not match all subpatterns at runtime.</span></span> <span data-ttu-id="779a4-187">Si la correspondance est établie et que la *simple_designation* du *property_pattern* est un *single_variable_designation*, elle définit une variable de type *T* à laquelle est assignée la valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="779a4-187">If the match succeeds and the *simple_designation* of the *property_pattern* is a *single_variable_designation*, it defines a variable of type *T* that is assigned the matched value.</span></span>

> <span data-ttu-id="779a4-188">Remarque : le modèle de propriété peut être utilisé pour effectuer une correspondance de modèle avec des types anonymes.</span><span class="sxs-lookup"><span data-stu-id="779a4-188">Note: The property pattern can be used to pattern-match with anonymous types.</span></span>

##### <a name="example"></a><span data-ttu-id="779a4-189">Exemple</span><span class="sxs-lookup"><span data-stu-id="779a4-189">Example</span></span>

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a><span data-ttu-id="779a4-190">Expression de switch</span><span class="sxs-lookup"><span data-stu-id="779a4-190">Switch Expression</span></span>

<span data-ttu-id="779a4-191">Un *switch_expression* est ajouté pour prendre en charge la sémantique de type `switch`pour un contexte d’expression.</span><span class="sxs-lookup"><span data-stu-id="779a4-191">A *switch_expression* is added to support `switch`-like semantics for an expression context.</span></span>

<span data-ttu-id="779a4-192">La C# syntaxe du langage est augmentée avec les productions syntaxiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="779a4-192">The C# language syntax is augmented with the following syntactic productions:</span></span>

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

<span data-ttu-id="779a4-193">Le *switch_expression* n’est pas autorisé en tant que *expression_statement*.</span><span class="sxs-lookup"><span data-stu-id="779a4-193">The *switch_expression* is not permitted as an *expression_statement*.</span></span>

> <span data-ttu-id="779a4-194">Nous allons nous pencher sur la prochaine révision.</span><span class="sxs-lookup"><span data-stu-id="779a4-194">We are looking at relaxing this in a future revision.</span></span>

<span data-ttu-id="779a4-195">Le type de l' *switch_expression* est le [*meilleur type commun*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) des expressions apparaissant à droite des jetons de `=>` des *switch_expression_arm*s si un tel type existe et l’expression de chaque ARM de l’expression de commutateur peut être convertie implicitement en ce type.</span><span class="sxs-lookup"><span data-stu-id="779a4-195">The type of the *switch_expression* is the [*best common type*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) of the expressions appearing to the right of the `=>` tokens of the *switch_expression_arm*s if such a type exists and the expression in every arm of the switch expression can be implicitly converted to that type.</span></span>  <span data-ttu-id="779a4-196">En outre, nous ajoutons une nouvelle *conversion d’expression de commutateur*, qui est une conversion implicite prédéfinie d’une expression de commutateur à chaque type `T` pour laquelle il existe une conversion implicite de l’expression de chaque arm en `T`.</span><span class="sxs-lookup"><span data-stu-id="779a4-196">In addition, we add a new *switch expression conversion*, which is a predefined implicit conversion from a switch expression to every type `T` for which there exists an implicit conversion from each arm's expression to `T`.</span></span>

<span data-ttu-id="779a4-197">Il s’agit d’une erreur si le modèle de *switch_expression_arm*ne peut pas affecter le résultat, car un modèle précédent et une protection correspondent toujours.</span><span class="sxs-lookup"><span data-stu-id="779a4-197">It is an error if some *switch_expression_arm*'s pattern cannot affect the result because some previous pattern and guard will always match.</span></span>

<span data-ttu-id="779a4-198">Une expression de commutateur est dite *exhaustive* si une partie de l’expression de commutateur gère chaque valeur de son entrée.</span><span class="sxs-lookup"><span data-stu-id="779a4-198">A switch expression is said to be *exhaustive* if some arm of the switch expression handles every value of its input.</span></span>  <span data-ttu-id="779a4-199">Le compilateur génère un avertissement si une expression de commutateur n’est pas *exhaustive*.</span><span class="sxs-lookup"><span data-stu-id="779a4-199">The compiler shall produce a warning if a switch expression is not *exhaustive*.</span></span>

<span data-ttu-id="779a4-200">Lors de l’exécution, le résultat de l' *switch_expression* est la valeur de l' *expression* de la première *switch_expression_arm* pour laquelle l’expression située à gauche du *switch_expression* correspond au modèle de l' *switch_expression_arm*, et pour laquelle la *case_guard* du *switch_expression_arm*, le cas échéant, est évaluée à `true`.</span><span class="sxs-lookup"><span data-stu-id="779a4-200">At runtime, the result of the *switch_expression* is the value of the *expression* of the first *switch_expression_arm* for which the expression on the left-hand-side of the *switch_expression* matches the *switch_expression_arm*'s pattern, and for which the *case_guard* of the *switch_expression_arm*, if present, evaluates to `true`.</span></span> <span data-ttu-id="779a4-201">S’il n’y a pas de *switch_expression_arm*de ce type, la *switch_expression* lève une instance de l’exception `System.Runtime.CompilerServices.SwitchExpressionException`.</span><span class="sxs-lookup"><span data-stu-id="779a4-201">If there is no such *switch_expression_arm*, the *switch_expression* throws an instance of the exception `System.Runtime.CompilerServices.SwitchExpressionException`.</span></span>

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a><span data-ttu-id="779a4-202">Parenthèses facultatif lors du basculement sur un littéral de Tuple</span><span class="sxs-lookup"><span data-stu-id="779a4-202">Optional parens when switching on a tuple literal</span></span>

<span data-ttu-id="779a4-203">Pour activer un littéral de tuple à l’aide de la *switch_Statement*, vous devez écrire ce qui semble être redondant parenthèses</span><span class="sxs-lookup"><span data-stu-id="779a4-203">In order to switch on a tuple literal using the *switch_statement*, you have to write what appear to be redundant parens</span></span>

```csharp
switch ((a, b))
{
```

<span data-ttu-id="779a4-204">Pour autoriser</span><span class="sxs-lookup"><span data-stu-id="779a4-204">To permit</span></span>

```csharp
switch (a, b)
{
```

<span data-ttu-id="779a4-205">les parenthèses de l’instruction switch sont facultatives lorsque l’expression qui est basculée est un littéral de Tuple.</span><span class="sxs-lookup"><span data-stu-id="779a4-205">the parentheses of the switch statement are optional when the expression being switched on is a tuple literal.</span></span>

### <a name="order-of-evaluation-in-pattern-matching"></a><span data-ttu-id="779a4-206">Ordre d’évaluation dans la mise en correspondance des modèles</span><span class="sxs-lookup"><span data-stu-id="779a4-206">Order of evaluation in pattern-matching</span></span>

<span data-ttu-id="779a4-207">Donner à la flexibilité du compilateur la possibilité de réorganiser les opérations exécutées pendant la mise en correspondance des modèles peut permettre de bénéficier d’une certaine flexibilité pour améliorer l’efficacité de la mise en correspondance des modèles.</span><span class="sxs-lookup"><span data-stu-id="779a4-207">Giving the compiler flexibility in reordering the operations executed during pattern-matching can permit flexibility that can be used to improve the efficiency of pattern-matching.</span></span> <span data-ttu-id="779a4-208">L’exigence (non appliquée) serait que les propriétés accessibles dans un modèle et les méthodes de déconstruction doivent être « pures » (effet secondaire gratuit, idempotent, etc.).</span><span class="sxs-lookup"><span data-stu-id="779a4-208">The (unenforced) requirement would be that properties accessed in a pattern, and the Deconstruct methods, are required to be "pure" (side-effect free, idempotent, etc).</span></span> <span data-ttu-id="779a4-209">Cela ne signifie pas que nous ajouterions la pureté comme un concept de langage, seulement que nous autoriserions la flexibilité du compilateur à réorganiser les opérations.</span><span class="sxs-lookup"><span data-stu-id="779a4-209">That doesn't mean that we would add purity as a language concept, only that we would allow the compiler flexibility in reordering operations.</span></span>

<span data-ttu-id="779a4-210">**Résolution 2018-04-04 LDM**: confirmée : le compilateur est autorisé à réorganiser les appels à `Deconstruct`, les accès aux propriétés et les appels de méthodes dans `ITuple`, et peut supposer que les valeurs retournées sont identiques à celles des appels multiples.</span><span class="sxs-lookup"><span data-stu-id="779a4-210">**Resolution 2018-04-04 LDM**: confirmed: the compiler is permitted to reorder calls to `Deconstruct`, property accesses, and invocations of methods in `ITuple`, and may assume that returned values are the same from multiple calls.</span></span> <span data-ttu-id="779a4-211">Le compilateur ne doit pas appeler de fonctions qui ne peuvent pas affecter le résultat, et nous allons faire attention avant d’apporter des modifications à l’ordre d’évaluation généré par le compilateur à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="779a4-211">The compiler should not invoke functions that cannot affect the result, and we will be very careful before making any changes to the compiler-generated order of evaluation in the future.</span></span>

### <a name="some-possible-optimizations"></a><span data-ttu-id="779a4-212">Optimisations possibles</span><span class="sxs-lookup"><span data-stu-id="779a4-212">Some Possible Optimizations</span></span>

<span data-ttu-id="779a4-213">La compilation des critères spéciaux peut tirer parti des parties communes des modèles.</span><span class="sxs-lookup"><span data-stu-id="779a4-213">The compilation of pattern matching can take advantage of common parts of patterns.</span></span> <span data-ttu-id="779a4-214">Par exemple, si le test de type de niveau supérieur de deux modèles successifs dans un *switch_Statement* est du même type, le code généré peut ignorer le test de type pour le deuxième modèle.</span><span class="sxs-lookup"><span data-stu-id="779a4-214">For example, if the top-level type test of two successive patterns in a *switch_statement* is the same type, the generated code can skip the type test for the second pattern.</span></span>

<span data-ttu-id="779a4-215">Quand certains des modèles sont des entiers ou des chaînes, le compilateur peut générer le même type de code qu’il génère pour une instruction switch dans les versions antérieures du langage.</span><span class="sxs-lookup"><span data-stu-id="779a4-215">When some of the patterns are integers or strings, the compiler can generate the same kind of code it generates for a switch-statement in earlier versions of the language.</span></span>

<span data-ttu-id="779a4-216">Pour plus d’informations sur ces types d’optimisations, consultez [[Scott et Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Quand les heuristiques de compilation des correspondances sont-elles importantes ?").</span><span class="sxs-lookup"><span data-stu-id="779a4-216">For more on these kinds of optimizations, see [[Scott and Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "When Do Match-Compilation Heuristics Matter?").</span></span>
