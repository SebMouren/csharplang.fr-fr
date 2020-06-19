---
ms.openlocfilehash: 4400cc56fc52fb36cdd19809ff7fb8f13706c161
ms.sourcegitcommit: eb00bb077e46c46807d804e9e1de3d794fb32405
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85070844"
---
# <a name="constant-interpolated-strings"></a><span data-ttu-id="e3501-101">Chaînes interpolées constantes</span><span class="sxs-lookup"><span data-stu-id="e3501-101">Constant Interpolated Strings</span></span>

* <span data-ttu-id="e3501-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="e3501-102">[x] Proposed</span></span>
* <span data-ttu-id="e3501-103">[] Prototype : [non démarré](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="e3501-103">[ ] Prototype: [Not Started](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="e3501-104">[] Implémentation : [non démarrée](https://github.com/dotnet/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="e3501-104">[ ] Implementation: [Not Started](https://github.com/dotnet/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="e3501-105">[] Spécification : [non démarrée](pr/1)</span><span class="sxs-lookup"><span data-stu-id="e3501-105">[ ] Specification: [Not Started](pr/1)</span></span>

## <a name="summary"></a><span data-ttu-id="e3501-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="e3501-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="e3501-107">Permet de générer des constantes à partir de chaînes interpolées de type chaîne constante.</span><span class="sxs-lookup"><span data-stu-id="e3501-107">Enables constants to be generated from interpolated strings of type string constant.</span></span>

## <a name="motivation"></a><span data-ttu-id="e3501-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="e3501-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="e3501-109">Le code suivant est déjà conforme :</span><span class="sxs-lookup"><span data-stu-id="e3501-109">The following code is already legal:</span></span>
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
<span data-ttu-id="e3501-110">Toutefois, il y a eu de nombreuses demandes de la communauté pour que les conditions suivantes soient également valides :</span><span class="sxs-lookup"><span data-stu-id="e3501-110">However, there have been many community requests to make the following also legal:</span></span>
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
<span data-ttu-id="e3501-111">Cette proposition représente l’étape logique suivante pour la génération de chaînes constantes, où la syntaxe de chaîne existante qui fonctionne dans d’autres situations est mise en œuvre pour les constantes.</span><span class="sxs-lookup"><span data-stu-id="e3501-111">This proposal represents the next logical step for constant string generation, where existing string syntax that works in other situations is made to work for constants.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="e3501-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="e3501-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="e3501-113">Les éléments suivants représentent les spécifications mises à jour pour les expressions constantes sous cette nouvelle proposition.</span><span class="sxs-lookup"><span data-stu-id="e3501-113">The following represent the updated specifications for constant expressions under this new proposal.</span></span> <span data-ttu-id="e3501-114">Les spécifications actuelles à partir desquelles ce est basé directement sont disponibles [ici](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).</span><span class="sxs-lookup"><span data-stu-id="e3501-114">Current specifications from which this was directly based on can be found [here](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).</span></span>

### <a name="constant-expressions"></a><span data-ttu-id="e3501-115">Expressions constantes</span><span class="sxs-lookup"><span data-stu-id="e3501-115">Constant Expressions</span></span>

<span data-ttu-id="e3501-116">Une *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e3501-116">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="e3501-117">Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `bool` , `object` , `string` ou tout type énumération.</span><span class="sxs-lookup"><span data-stu-id="e3501-117">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="e3501-118">Seules les constructions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="e3501-118">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="e3501-119">Littéraux (y compris le `null` littéral).</span><span class="sxs-lookup"><span data-stu-id="e3501-119">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="e3501-120">Références aux `const` membres de types classe et struct.</span><span class="sxs-lookup"><span data-stu-id="e3501-120">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="e3501-121">Références aux membres de types énumération.</span><span class="sxs-lookup"><span data-stu-id="e3501-121">References to members of enumeration types.</span></span>
*  <span data-ttu-id="e3501-122">Références à des `const` paramètres ou à des variables locales</span><span class="sxs-lookup"><span data-stu-id="e3501-122">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="e3501-123">Sous-expressions entre parenthèses, qui sont elles-mêmes des expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="e3501-123">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="e3501-124">Expressions de cast, à condition que le type cible soit l’un des types indiqués ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="e3501-124">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="e3501-125">`checked``unchecked`expressions et</span><span class="sxs-lookup"><span data-stu-id="e3501-125">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="e3501-126">Expressions de valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="e3501-126">Default value expressions</span></span>
*  <span data-ttu-id="e3501-127">Expressions Nameof</span><span class="sxs-lookup"><span data-stu-id="e3501-127">Nameof expressions</span></span>
*  <span data-ttu-id="e3501-128">Opérateurs unaires prédéfinis, `+` `-` , `!` et `~` .</span><span class="sxs-lookup"><span data-stu-id="e3501-128">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="e3501-129">Les opérateurs prédéfinis,,,,,, `+` `-` ,,,, `*` `/` `%` `<<` `>>` `&` `|` `^` `&&` , `||` , `==` , `!=` , `<` , `>` , `<=` et `>=` binaires, à condition que chaque opérande soit d’un type indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="e3501-129">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="e3501-130">`?:`Opérateur conditionnel.</span><span class="sxs-lookup"><span data-stu-id="e3501-130">The `?:` conditional operator.</span></span>
*  <span data-ttu-id="e3501-131">*Chaînes interpolées `${}` , à condition que tous les composants soient des expressions constantes de type `string` et que tous les composants interpolés n’aient pas d’alignement et de spécificateurs de format.*</span><span class="sxs-lookup"><span data-stu-id="e3501-131">*Interpolated strings `${}`, provided that all components are constant expressions of type `string` and all interpolated components lack alignment and format specifiers.*</span></span>

<span data-ttu-id="e3501-132">Les conversions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="e3501-132">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="e3501-133">Conversions d’identité</span><span class="sxs-lookup"><span data-stu-id="e3501-133">Identity conversions</span></span>
*  <span data-ttu-id="e3501-134">Conversions numériques</span><span class="sxs-lookup"><span data-stu-id="e3501-134">Numeric conversions</span></span>
*  <span data-ttu-id="e3501-135">Conversions d’énumération</span><span class="sxs-lookup"><span data-stu-id="e3501-135">Enumeration conversions</span></span>
*  <span data-ttu-id="e3501-136">Conversions d’expressions constantes</span><span class="sxs-lookup"><span data-stu-id="e3501-136">Constant expression conversions</span></span>
*  <span data-ttu-id="e3501-137">Conversions de référence implicites et explicites, à condition que la source des conversions soit une expression constante qui prend la valeur null.</span><span class="sxs-lookup"><span data-stu-id="e3501-137">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="e3501-138">Les autres conversions, y compris les conversions boxing, unboxing et implicites des valeurs non null, ne sont pas autorisées dans les expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="e3501-138">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="e3501-139">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e3501-139">For example:</span></span>
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="e3501-140">l’initialisation de i est une erreur, car une conversion boxing est requise.</span><span class="sxs-lookup"><span data-stu-id="e3501-140">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="e3501-141">L’initialisation de str est une erreur, car une conversion de référence implicite à partir d’une valeur non null est requise.</span><span class="sxs-lookup"><span data-stu-id="e3501-141">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="e3501-142">Chaque fois qu’une expression répond aux spécifications mentionnées ci-dessus, l’expression est évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e3501-142">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="e3501-143">Cela est vrai même si l’expression est une sous-expression d’une expression plus grande qui contient des constructions non constantes.</span><span class="sxs-lookup"><span data-stu-id="e3501-143">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="e3501-144">L’évaluation au moment de la compilation des expressions constantes utilise les mêmes règles que l’évaluation au moment de l’exécution des expressions non constantes, à la différence près que lorsque l’évaluation au moment de l’exécution a levé une exception, l’évaluation au moment de la compilation provoque une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e3501-144">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="e3501-145">À moins qu’une expression constante ne soit explicitement placée dans un `unchecked` contexte, les débordements qui se produisent dans les opérations arithmétiques de type intégral et les conversions pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation ([expressions constantes](expressions.md#constant-expressions)).</span><span class="sxs-lookup"><span data-stu-id="e3501-145">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="e3501-146">Les expressions constantes se trouvent dans les contextes listés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e3501-146">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="e3501-147">Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="e3501-147">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="e3501-148">Déclarations de constantes ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="e3501-148">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="e3501-149">Déclarations de membre d’énumération ([membres enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="e3501-149">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="e3501-150">Arguments par défaut des listes de paramètres formels ([paramètres de méthode](classes.md#method-parameters))</span><span class="sxs-lookup"><span data-stu-id="e3501-150">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="e3501-151">`case`étiquettes d’une `switch` instruction ([instruction switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="e3501-151">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="e3501-152">`goto case`instructions ([instruction goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="e3501-152">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="e3501-153">Longueurs de dimension dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="e3501-153">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="e3501-154">Attributs ([attributs](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="e3501-154">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="e3501-155">Une conversion d’expression constante implicite ([conversions d’expressions constantes implicites](conversions.md#implicit-constant-expression-conversions)) permet de convertir une expression constante de type en `int` `sbyte` , `byte` ,, `short` `ushort` , `uint` ou `ulong` , à condition que la valeur de l’expression constante soit comprise dans la plage du type de destination.</span><span class="sxs-lookup"><span data-stu-id="e3501-155">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="e3501-156">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="e3501-156">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="e3501-157">Cette proposition ajoute une complexité supplémentaire au compilateur dans Exchange pour une applicabilité plus large des chaînes interpolées.</span><span class="sxs-lookup"><span data-stu-id="e3501-157">This proposal adds additional complexity to the compiler in exchange for broader applicability of interpolated strings.</span></span> <span data-ttu-id="e3501-158">Étant donné que ces chaînes sont entièrement évaluées au moment de la compilation, les fonctionnalités de mise en forme automatique importantes des chaînes interpolées sont moins nécessaires.</span><span class="sxs-lookup"><span data-stu-id="e3501-158">As these strings are fully evaluated at compile time, the valuable automatic formatting features of interpolated strings are less neccesary.</span></span> <span data-ttu-id="e3501-159">La plupart des cas d’usage peuvent être répliqués en grande partie par le biais des alternatives ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e3501-159">Most use cases can be largely replicated through the alternatives below.</span></span>

## <a name="alternatives"></a><span data-ttu-id="e3501-160">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="e3501-160">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="e3501-161">L' `+` opérateur actuel pour les concatnation de chaîne peut combiner des chaînes de manière similaire à la proposition actuelle.</span><span class="sxs-lookup"><span data-stu-id="e3501-161">The current `+` operator for string concatnation can combine strings in a similar manner to the current proposal.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="e3501-162">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="e3501-162">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="e3501-163">Quelles parties de la conception sont toujours en cours ?</span><span class="sxs-lookup"><span data-stu-id="e3501-163">What parts of the design are still undecided?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="e3501-164">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="e3501-164">Design meetings</span></span>

<span data-ttu-id="e3501-165">Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.</span><span class="sxs-lookup"><span data-stu-id="e3501-165">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>


