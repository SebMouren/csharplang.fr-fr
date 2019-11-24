---
ms.openlocfilehash: 3b142d7dbda8a94a4cf2c973a2e380065dcbf5ee
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703963"
---
# <a name="enums"></a><span data-ttu-id="f6438-101">Énumérations</span><span class="sxs-lookup"><span data-stu-id="f6438-101">Enums</span></span>

<span data-ttu-id="f6438-102">Un ***type enum*** est un type valeur distinct ([types valeur](types.md#value-types)) qui déclare un ensemble de constantes nommées.</span><span class="sxs-lookup"><span data-stu-id="f6438-102">An ***enum type*** is a distinct value type ([Value types](types.md#value-types)) that declares a set of named constants.</span></span>

<span data-ttu-id="f6438-103">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-103">The example</span></span>

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="f6438-104">déclare un type Enum nommé `Color` avec les membres `Red`, `Green`et `Blue`.</span><span class="sxs-lookup"><span data-stu-id="f6438-104">declares an enum type named `Color` with members `Red`, `Green`, and `Blue`.</span></span>

## <a name="enum-declarations"></a><span data-ttu-id="f6438-105">Déclarations d’énumération</span><span class="sxs-lookup"><span data-stu-id="f6438-105">Enum declarations</span></span>

<span data-ttu-id="f6438-106">Une déclaration enum déclare un nouveau type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-106">An enum declaration declares a new enum type.</span></span> <span data-ttu-id="f6438-107">Une déclaration enum commence par le mot clé `enum`et définit le nom, l’accessibilité, le type sous-jacent et les membres de l’enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-107">An enum declaration begins with the keyword `enum`, and defines the name, accessibility, underlying type, and members of the enum.</span></span>

```antlr
enum_declaration
    : attributes? enum_modifier* 'enum' identifier enum_base? enum_body ';'?
    ;

enum_base
    : ':' integral_type
    ;

enum_body
    : '{' enum_member_declarations? '}'
    | '{' enum_member_declarations ',' '}'
    ;
```

<span data-ttu-id="f6438-108">Chaque type enum a un type intégral correspondant appelé le ***type sous-jacent*** du type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-108">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="f6438-109">Ce type sous-jacent doit être en mesure de représenter toutes les valeurs d’énumérateur définies dans l’énumération.</span><span class="sxs-lookup"><span data-stu-id="f6438-109">This underlying type must be able to represent all the enumerator values defined in the enumeration.</span></span> <span data-ttu-id="f6438-110">Une déclaration enum peut déclarer explicitement un type sous-jacent de `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`.</span><span class="sxs-lookup"><span data-stu-id="f6438-110">An enum declaration may explicitly declare an underlying type of `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` or `ulong`.</span></span> <span data-ttu-id="f6438-111">Notez que `char` ne peut pas être utilisé en tant que type sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="f6438-111">Note that `char` cannot be used as an underlying type.</span></span> <span data-ttu-id="f6438-112">Une déclaration enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`.</span><span class="sxs-lookup"><span data-stu-id="f6438-112">An enum declaration that does not explicitly declare an underlying type has an underlying type of `int`.</span></span>

<span data-ttu-id="f6438-113">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-113">The example</span></span>

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="f6438-114">déclare une énumération avec un type sous-jacent de `long`.</span><span class="sxs-lookup"><span data-stu-id="f6438-114">declares an enum with an underlying type of `long`.</span></span> <span data-ttu-id="f6438-115">Un développeur peut choisir d’utiliser un type sous-jacent de `long`, comme dans l’exemple, pour permettre l’utilisation de valeurs qui se trouvent dans la plage de `long` mais pas dans la plage de `int`, ou pour conserver cette option à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="f6438-115">A developer might choose to use an underlying type of `long`, as in the example, to enable the use of values that are in the range of `long` but not in the range of `int`, or to preserve this option for the future.</span></span>

## <a name="enum-modifiers"></a><span data-ttu-id="f6438-116">Modificateurs enum</span><span class="sxs-lookup"><span data-stu-id="f6438-116">Enum modifiers</span></span>

<span data-ttu-id="f6438-117">Une *enum_declaration* peut éventuellement inclure une séquence de modificateurs enum :</span><span class="sxs-lookup"><span data-stu-id="f6438-117">An *enum_declaration* may optionally include a sequence of enum modifiers:</span></span>

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

<span data-ttu-id="f6438-118">Il s’agit d’une erreur au moment de la compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration Enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-118">It is a compile-time error for the same modifier to appear multiple times in an enum declaration.</span></span>

<span data-ttu-id="f6438-119">Les modificateurs d’une déclaration enum ont la même signification que ceux d’une déclaration de classe ([modificateurs de classe](classes.md#class-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="f6438-119">The modifiers of an enum declaration have the same meaning as those of a class declaration ([Class modifiers](classes.md#class-modifiers)).</span></span> <span data-ttu-id="f6438-120">Notez, toutefois, que les modificateurs `abstract` et `sealed` ne sont pas autorisés dans une déclaration Enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-120">Note, however, that the `abstract` and `sealed` modifiers are not permitted in an enum declaration.</span></span> <span data-ttu-id="f6438-121">Les enums ne peuvent pas être abstraits et n’autorisent pas la dérivation.</span><span class="sxs-lookup"><span data-stu-id="f6438-121">Enums cannot be abstract and do not permit derivation.</span></span>

## <a name="enum-members"></a><span data-ttu-id="f6438-122">Énumérer les membres</span><span class="sxs-lookup"><span data-stu-id="f6438-122">Enum members</span></span>

<span data-ttu-id="f6438-123">Le corps d’une déclaration de type enum définit zéro, un ou plusieurs membres enum, qui sont les constantes nommées du type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-123">The body of an enum type declaration defines zero or more enum members, which are the named constants of the enum type.</span></span> <span data-ttu-id="f6438-124">Deux membres enum ne peuvent pas avoir le même nom.</span><span class="sxs-lookup"><span data-stu-id="f6438-124">No two enum members can have the same name.</span></span>

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

<span data-ttu-id="f6438-125">Chaque membre enum a une valeur de constante associée.</span><span class="sxs-lookup"><span data-stu-id="f6438-125">Each enum member has an associated constant value.</span></span> <span data-ttu-id="f6438-126">Le type de cette valeur est le type sous-jacent pour l’enum conteneur.</span><span class="sxs-lookup"><span data-stu-id="f6438-126">The type of this value is the underlying type for the containing enum.</span></span> <span data-ttu-id="f6438-127">La valeur de constante pour chaque membre enum doit être comprise dans la plage du type sous-jacent pour l’enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-127">The constant value for each enum member must be in the range of the underlying type for the enum.</span></span> <span data-ttu-id="f6438-128">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-128">The example</span></span>

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

<span data-ttu-id="f6438-129">génère une erreur de compilation, car les valeurs de constante `-1`, `-2`et `-3` ne sont pas comprises dans la plage du type intégral sous-jacent `uint`.</span><span class="sxs-lookup"><span data-stu-id="f6438-129">results in a compile-time error because the constant values `-1`, `-2`, and `-3` are not in the range of the underlying integral type `uint`.</span></span>

<span data-ttu-id="f6438-130">Plusieurs membres enum peuvent partager la même valeur associée.</span><span class="sxs-lookup"><span data-stu-id="f6438-130">Multiple enum members may share the same associated value.</span></span> <span data-ttu-id="f6438-131">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-131">The example</span></span>

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

<span data-ttu-id="f6438-132">affiche une énumération dans laquelle deux membres enum, `Blue` et `Max`, ont la même valeur associée.</span><span class="sxs-lookup"><span data-stu-id="f6438-132">shows an enum in which two enum members -- `Blue` and `Max` -- have the same associated value.</span></span>

<span data-ttu-id="f6438-133">La valeur associée d’un membre enum est assignée implicitement ou explicitement.</span><span class="sxs-lookup"><span data-stu-id="f6438-133">The associated value of an enum member is assigned either implicitly or explicitly.</span></span> <span data-ttu-id="f6438-134">Si la déclaration du membre enum a un initialiseur *constant_expression* , la valeur de cette expression constante, implicitement convertie dans le type sous-jacent de l’enum, est la valeur associée du membre Enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-134">If the declaration of the enum member has a *constant_expression* initializer, the value of that constant expression, implicitly converted to the underlying type of the enum, is the associated value of the enum member.</span></span> <span data-ttu-id="f6438-135">Si la déclaration du membre enum n’a pas d’initialiseur, sa valeur associée est définie implicitement, comme suit :</span><span class="sxs-lookup"><span data-stu-id="f6438-135">If the declaration of the enum member has no initializer, its associated value is set implicitly, as follows:</span></span>

*  <span data-ttu-id="f6438-136">Si le membre enum est le premier membre enum déclaré dans le type enum, sa valeur associée est zéro.</span><span class="sxs-lookup"><span data-stu-id="f6438-136">If the enum member is the first enum member declared in the enum type, its associated value is zero.</span></span>
*  <span data-ttu-id="f6438-137">Dans le cas contraire, la valeur associée du membre enum est obtenue en accroissant la valeur associée du membre enum qui précède textuellement.</span><span class="sxs-lookup"><span data-stu-id="f6438-137">Otherwise, the associated value of the enum member is obtained by increasing the associated value of the textually preceding enum member by one.</span></span> <span data-ttu-id="f6438-138">Cette valeur augmentée doit être comprise dans la plage de valeurs qui peuvent être représentées par le type sous-jacent ; sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f6438-138">This increased value must be within the range of values that can be represented by the underlying type, otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="f6438-139">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-139">The example</span></span>

```csharp
using System;

enum Color
{
    Red,
    Green = 10,
    Blue
}

class Test
{
    static void Main() {
        Console.WriteLine(StringFromColor(Color.Red));
        Console.WriteLine(StringFromColor(Color.Green));
        Console.WriteLine(StringFromColor(Color.Blue));
    }

    static string StringFromColor(Color c) {
        switch (c) {
            case Color.Red: 
                return String.Format("Red = {0}", (int) c);

            case Color.Green:
                return String.Format("Green = {0}", (int) c);

            case Color.Blue:
                return String.Format("Blue = {0}", (int) c);

            default:
                return "Invalid color";
        }
    }
}
```

<span data-ttu-id="f6438-140">imprime les noms des membres enum et leurs valeurs associées.</span><span class="sxs-lookup"><span data-stu-id="f6438-140">prints out the enum member names and their associated values.</span></span> <span data-ttu-id="f6438-141">La sortie est la suivante :</span><span class="sxs-lookup"><span data-stu-id="f6438-141">The output is:</span></span>

```console
Red = 0
Green = 10
Blue = 11
```

<span data-ttu-id="f6438-142">pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6438-142">for the following reasons:</span></span>

*  <span data-ttu-id="f6438-143">la valeur zéro est assignée automatiquement au membre enum `Red` (car il n’a pas d’initialiseur et est le premier membre enum);</span><span class="sxs-lookup"><span data-stu-id="f6438-143">the enum member `Red` is automatically assigned the value zero (since it has no initializer and is the first enum member);</span></span>
*  <span data-ttu-id="f6438-144">le membre enum `Green` reçoit explicitement la valeur `10`;</span><span class="sxs-lookup"><span data-stu-id="f6438-144">the enum member `Green` is explicitly given the value `10`;</span></span>
*  <span data-ttu-id="f6438-145">et le membre enum `Blue` reçoit automatiquement la valeur une valeur supérieure à celle du membre qui le précède textuellement.</span><span class="sxs-lookup"><span data-stu-id="f6438-145">and the enum member `Blue` is automatically assigned the value one greater than the member that textually precedes it.</span></span>

<span data-ttu-id="f6438-146">La valeur associée d’un membre enum ne peut pas, directement ou indirectement, utiliser la valeur de son propre membre enum associé.</span><span class="sxs-lookup"><span data-stu-id="f6438-146">The associated value of an enum member may not, directly or indirectly, use the value of its own associated enum member.</span></span> <span data-ttu-id="f6438-147">Outre cette restriction de circularité, les initialiseurs de membres enum peuvent librement faire référence à d’autres initialiseurs de membres enum, indépendamment de leur position textuelle.</span><span class="sxs-lookup"><span data-stu-id="f6438-147">Other than this circularity restriction, enum member initializers may freely refer to other enum member initializers, regardless of their textual position.</span></span> <span data-ttu-id="f6438-148">Dans un initialiseur de membre enum, les valeurs d’autres membres enum sont toujours traitées comme ayant le type de leur type sous-jacent, de sorte que les casts ne sont pas nécessaires pour faire référence à d’autres membres enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-148">Within an enum member initializer, values of other enum members are always treated as having the type of their underlying type, so that casts are not necessary when referring to other enum members.</span></span>

<span data-ttu-id="f6438-149">L’exemple</span><span class="sxs-lookup"><span data-stu-id="f6438-149">The example</span></span>

```csharp
enum Circular
{
    A = B,
    B
}
```

<span data-ttu-id="f6438-150">génère une erreur au moment de la compilation, car les déclarations de `A` et `B` sont circulaires.</span><span class="sxs-lookup"><span data-stu-id="f6438-150">results in a compile-time error because the declarations of `A` and `B` are circular.</span></span> <span data-ttu-id="f6438-151">`A` dépend `B` de manière explicite, et `B` dépend `A` implicitement.</span><span class="sxs-lookup"><span data-stu-id="f6438-151">`A` depends on `B` explicitly, and `B` depends on `A` implicitly.</span></span>

<span data-ttu-id="f6438-152">Les membres enum sont nommés et étendus d’une manière exactement analogue aux champs dans les classes.</span><span class="sxs-lookup"><span data-stu-id="f6438-152">Enum members are named and scoped in a manner exactly analogous to fields within classes.</span></span> <span data-ttu-id="f6438-153">La portée d’un membre enum est le corps de son type enum contenant.</span><span class="sxs-lookup"><span data-stu-id="f6438-153">The scope of an enum member is the body of its containing enum type.</span></span> <span data-ttu-id="f6438-154">Au sein de cette étendue, les membres enum peuvent être référencés par leur nom simple.</span><span class="sxs-lookup"><span data-stu-id="f6438-154">Within that scope, enum members can be referred to by their simple name.</span></span> <span data-ttu-id="f6438-155">À partir de tout autre code, le nom d’un membre enum doit être qualifié avec le nom de son type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-155">From all other code, the name of an enum member must be qualified with the name of its enum type.</span></span> <span data-ttu-id="f6438-156">Les membres enum n’ont pas d’accessibilité déclarée : un membre enum est accessible si son type enum conteneur est accessible.</span><span class="sxs-lookup"><span data-stu-id="f6438-156">Enum members do not have any declared accessibility -- an enum member is accessible if its containing enum type is accessible.</span></span>

## <a name="the-systemenum-type"></a><span data-ttu-id="f6438-157">Type System. Enum</span><span class="sxs-lookup"><span data-stu-id="f6438-157">The System.Enum type</span></span>

<span data-ttu-id="f6438-158">Le `System.Enum` de type est la classe de base abstraite de tous les types ENUM (il est distinct et différent du type sous-jacent du type enum), et les membres hérités de `System.Enum` sont disponibles dans tout type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-158">The type `System.Enum` is the abstract base class of all enum types (this is distinct and different from the underlying type of the enum type), and the members inherited from `System.Enum` are available in any enum type.</span></span> <span data-ttu-id="f6438-159">Une conversion boxing ([conversions boxing](types.md#boxing-conversions)) existe à partir de n’importe quel type enum en `System.Enum`, et une conversion unboxing ([conversions unboxing](types.md#unboxing-conversions)) existe de `System.Enum` à n’importe quel type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-159">A boxing conversion ([Boxing conversions](types.md#boxing-conversions)) exists from any enum type to `System.Enum`, and an unboxing conversion ([Unboxing conversions](types.md#unboxing-conversions)) exists from `System.Enum` to any enum type.</span></span>

<span data-ttu-id="f6438-160">Notez que `System.Enum` n’est pas lui-même un *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="f6438-160">Note that `System.Enum` is not itself an *enum_type*.</span></span> <span data-ttu-id="f6438-161">Au lieu de cela, il s’agit d’un *class_type* duquel tous les *enum_type*sont dérivés.</span><span class="sxs-lookup"><span data-stu-id="f6438-161">Rather, it is a *class_type* from which all *enum_type*s are derived.</span></span> <span data-ttu-id="f6438-162">Le type `System.Enum` hérite du type `System.ValueType` ([type System. ValueType](types.md#the-systemvaluetype-type)) qui, à son tour, hérite du type `object`.</span><span class="sxs-lookup"><span data-stu-id="f6438-162">The type `System.Enum` inherits from the type `System.ValueType` ([The System.ValueType type](types.md#the-systemvaluetype-type)), which, in turn, inherits from type `object`.</span></span> <span data-ttu-id="f6438-163">Au moment de l’exécution, une valeur de type `System.Enum` peut être `null` ou une référence à une valeur boxed de n’importe quel type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-163">At run-time, a value of type `System.Enum` can be `null` or a reference to a boxed value of any enum type.</span></span>

## <a name="enum-values-and-operations"></a><span data-ttu-id="f6438-164">Valeurs et opérations enum</span><span class="sxs-lookup"><span data-stu-id="f6438-164">Enum values and operations</span></span>

<span data-ttu-id="f6438-165">Chaque type enum définit un type distinct ; une conversion d’énumération explicite ([conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)) est requise pour la conversion entre un type enum et un type intégral, ou entre deux types ENUM.</span><span class="sxs-lookup"><span data-stu-id="f6438-165">Each enum type defines a distinct type; an explicit enumeration conversion ([Explicit enumeration conversions](conversions.md#explicit-enumeration-conversions)) is required to convert between an enum type and an integral type, or between two enum types.</span></span> <span data-ttu-id="f6438-166">L’ensemble de valeurs qu’un type enum peut utiliser n’est pas limité par ses membres enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-166">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="f6438-167">En particulier, toute valeur du type sous-jacent d’une énumération peut être convertie en type enum, et est une valeur valide distincte de ce type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-167">In particular, any value of the underlying type of an enum can be cast to the enum type, and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="f6438-168">Les membres enum ont le type de leur type enum conteneur (sauf dans d’autres initialiseurs de membres enum : consultez [enum members](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="f6438-168">Enum members have the type of their containing enum type (except within other enum member initializers: see [Enum members](enums.md#enum-members)).</span></span> <span data-ttu-id="f6438-169">La valeur d’un membre enum déclaré dans le type enum `E` avec la valeur associée `v` est `(E)v`.</span><span class="sxs-lookup"><span data-stu-id="f6438-169">The value of an enum member declared in enum type `E` with associated value `v` is `(E)v`.</span></span>

<span data-ttu-id="f6438-170">Les opérateurs suivants peuvent être utilisés sur des valeurs de types enum : `==`, `!=`, `<`, `>`, `<=`, `>=` ([opérateurs de comparaison d’énumération](expressions.md#enumeration-comparison-operators)), binaire `+` ([opérateur d’addition](expressions.md#addition-operator)), binaire `-` (opérateur de[soustraction](expressions.md#subtraction-operator)), `^`, `&`, `|` ([opérateurs logiques d’énumération](expressions.md#enumeration-logical-operators)), `~` ([opérateur de complément au niveau du bit](expressions.md#bitwise-complement-operator)) [Opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).`++``--` </span><span class="sxs-lookup"><span data-stu-id="f6438-170">The following operators can be used on values of enum types: `==`, `!=`, `<`, `>`, `<=`, `>=` ([Enumeration comparison operators](expressions.md#enumeration-comparison-operators)), binary `+` ([Addition operator](expressions.md#addition-operator)), binary `-` ([Subtraction operator](expressions.md#subtraction-operator)), `^`, `&`, `|` ([Enumeration logical operators](expressions.md#enumeration-logical-operators)), `~` ([Bitwise complement operator](expressions.md#bitwise-complement-operator)), `++` and `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="f6438-171">Chaque type enum dérive automatiquement de la classe `System.Enum` (qui dérive à son tour de `System.ValueType` et `object`).</span><span class="sxs-lookup"><span data-stu-id="f6438-171">Every enum type automatically derives from the class `System.Enum` (which, in turn, derives from `System.ValueType` and `object`).</span></span> <span data-ttu-id="f6438-172">Ainsi, les méthodes et les propriétés héritées de cette classe peuvent être utilisées sur les valeurs d’un type enum.</span><span class="sxs-lookup"><span data-stu-id="f6438-172">Thus, inherited methods and properties of this class can be used on values of an enum type.</span></span>
