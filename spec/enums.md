---
ms.openlocfilehash: c3b716e6eb331be2ee33fffeb42c1e2406cd3a5c
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488759"
---
# <a name="enums"></a><span data-ttu-id="bc22a-101">Enums</span><span class="sxs-lookup"><span data-stu-id="bc22a-101">Enums</span></span>

<span data-ttu-id="bc22a-102">Un ***type enum*** est un type valeur distinct ([types valeur](types.md#value-types)) qui déclare un ensemble de constantes nommées.</span><span class="sxs-lookup"><span data-stu-id="bc22a-102">An ***enum type*** is a distinct value type ([Value types](types.md#value-types)) that declares a set of named constants.</span></span>

<span data-ttu-id="bc22a-103">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-103">The example</span></span>

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="bc22a-104">déclare un type enum nommé `Color` avec les membres `Red`, `Green`, et `Blue`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-104">declares an enum type named `Color` with members `Red`, `Green`, and `Blue`.</span></span>

## <a name="enum-declarations"></a><span data-ttu-id="bc22a-105">Déclarations d’enum</span><span class="sxs-lookup"><span data-stu-id="bc22a-105">Enum declarations</span></span>

<span data-ttu-id="bc22a-106">Une déclaration enum déclare un nouveau type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-106">An enum declaration declares a new enum type.</span></span> <span data-ttu-id="bc22a-107">Une déclaration enum commence par le mot clé `enum`et définit le nom, l’accessibilité, le type sous-jacent et les membres de l’enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-107">An enum declaration begins with the keyword `enum`, and defines the name, accessibility, underlying type, and members of the enum.</span></span>

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

<span data-ttu-id="bc22a-108">Chaque type énumération a un type intégral appelé le ***type sous-jacent*** du type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-108">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="bc22a-109">Ce type sous-jacent doit être en mesure de représenter toutes les valeurs de l’énumérateur définies dans l’énumération.</span><span class="sxs-lookup"><span data-stu-id="bc22a-109">This underlying type must be able to represent all the enumerator values defined in the enumeration.</span></span> <span data-ttu-id="bc22a-110">Une déclaration enum peut déclarer explicitement un type sous-jacent de `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-110">An enum declaration may explicitly declare an underlying type of `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` or `ulong`.</span></span> <span data-ttu-id="bc22a-111">Notez que `char` ne peut pas être utilisé comme un type sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="bc22a-111">Note that `char` cannot be used as an underlying type.</span></span> <span data-ttu-id="bc22a-112">Une déclaration enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-112">An enum declaration that does not explicitly declare an underlying type has an underlying type of `int`.</span></span>

<span data-ttu-id="bc22a-113">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-113">The example</span></span>

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="bc22a-114">déclare un enum avec un type sous-jacent de `long`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-114">declares an enum with an underlying type of `long`.</span></span> <span data-ttu-id="bc22a-115">Un développeur peut choisir d’utiliser un type sous-jacent de `long`, comme dans l’exemple, pour activer l’utilisation de valeurs qui se trouvent dans la plage de `long` , mais pas dans la plage de `int`, ou pour conserver cette option pour l’avenir.</span><span class="sxs-lookup"><span data-stu-id="bc22a-115">A developer might choose to use an underlying type of `long`, as in the example, to enable the use of values that are in the range of `long` but not in the range of `int`, or to preserve this option for the future.</span></span>

## <a name="enum-modifiers"></a><span data-ttu-id="bc22a-116">Modificateurs d’énumération</span><span class="sxs-lookup"><span data-stu-id="bc22a-116">Enum modifiers</span></span>

<span data-ttu-id="bc22a-117">Un *enum_declaration* peut éventuellement inclure une séquence de modificateurs d’énumération :</span><span class="sxs-lookup"><span data-stu-id="bc22a-117">An *enum_declaration* may optionally include a sequence of enum modifiers:</span></span>

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

<span data-ttu-id="bc22a-118">Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-118">It is a compile-time error for the same modifier to appear multiple times in an enum declaration.</span></span>

<span data-ttu-id="bc22a-119">Les modificateurs d’une déclaration enum ont la même signification que ceux d’une déclaration de classe ([modificateurs de classe](classes.md#class-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="bc22a-119">The modifiers of an enum declaration have the same meaning as those of a class declaration ([Class modifiers](classes.md#class-modifiers)).</span></span> <span data-ttu-id="bc22a-120">Notez, cependant, que le `abstract` et `sealed` modificateurs ne sont pas autorisées dans une déclaration enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-120">Note, however, that the `abstract` and `sealed` modifiers are not permitted in an enum declaration.</span></span> <span data-ttu-id="bc22a-121">Les enums ne peut pas être abstraits et ne permettent pas de dérivation.</span><span class="sxs-lookup"><span data-stu-id="bc22a-121">Enums cannot be abstract and do not permit derivation.</span></span>

## <a name="enum-members"></a><span data-ttu-id="bc22a-122">Membres d’enum</span><span class="sxs-lookup"><span data-stu-id="bc22a-122">Enum members</span></span>

<span data-ttu-id="bc22a-123">Le corps d’une déclaration de type enum définit zéro ou plusieurs membres de l’enum, qui sont des constantes nommées du type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-123">The body of an enum type declaration defines zero or more enum members, which are the named constants of the enum type.</span></span> <span data-ttu-id="bc22a-124">Aucun membre enum deux ne peut avoir le même nom.</span><span class="sxs-lookup"><span data-stu-id="bc22a-124">No two enum members can have the same name.</span></span>

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

<span data-ttu-id="bc22a-125">Chaque membre enum a une valeur de constante associée.</span><span class="sxs-lookup"><span data-stu-id="bc22a-125">Each enum member has an associated constant value.</span></span> <span data-ttu-id="bc22a-126">Le type de cette valeur est le type sous-jacent de l’enum conteneur.</span><span class="sxs-lookup"><span data-stu-id="bc22a-126">The type of this value is the underlying type for the containing enum.</span></span> <span data-ttu-id="bc22a-127">La valeur de constante pour chaque membre enum doit être dans la plage du type sous-jacent de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="bc22a-127">The constant value for each enum member must be in the range of the underlying type for the enum.</span></span> <span data-ttu-id="bc22a-128">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-128">The example</span></span>

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

<span data-ttu-id="bc22a-129">génère une erreur de compilation, car les valeurs de constante `-1`, `-2`, et `-3` ne sont pas dans la plage du type intégral sous-jacent `uint`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-129">results in a compile-time error because the constant values `-1`, `-2`, and `-3` are not in the range of the underlying integral type `uint`.</span></span>

<span data-ttu-id="bc22a-130">Les membres enum multiples peuvent partager la même valeur associée.</span><span class="sxs-lookup"><span data-stu-id="bc22a-130">Multiple enum members may share the same associated value.</span></span> <span data-ttu-id="bc22a-131">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-131">The example</span></span>

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

<span data-ttu-id="bc22a-132">montre un enum dans les deux membres enum-- `Blue` et `Max` --ont la même valeur associée.</span><span class="sxs-lookup"><span data-stu-id="bc22a-132">shows an enum in which two enum members -- `Blue` and `Max` -- have the same associated value.</span></span>

<span data-ttu-id="bc22a-133">La valeur associée à un membre enum reçoit implicitement ou explicitement.</span><span class="sxs-lookup"><span data-stu-id="bc22a-133">The associated value of an enum member is assigned either implicitly or explicitly.</span></span> <span data-ttu-id="bc22a-134">Si la déclaration du membre enum a un *constant_expression* initialiseur, la valeur de cette expression constante, convertie implicitement en type sous-jacent de l’enum, est la valeur associée du membre enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-134">If the declaration of the enum member has a *constant_expression* initializer, the value of that constant expression, implicitly converted to the underlying type of the enum, is the associated value of the enum member.</span></span> <span data-ttu-id="bc22a-135">Si la déclaration du membre enum n’a aucun initialiseur, sa valeur associée est définie implicitement, comme suit :</span><span class="sxs-lookup"><span data-stu-id="bc22a-135">If the declaration of the enum member has no initializer, its associated value is set implicitly, as follows:</span></span>

*  <span data-ttu-id="bc22a-136">Si le membre enum est le premier membre enum déclaré dans le type enum, sa valeur associée est zéro.</span><span class="sxs-lookup"><span data-stu-id="bc22a-136">If the enum member is the first enum member declared in the enum type, its associated value is zero.</span></span>
*  <span data-ttu-id="bc22a-137">Sinon, la valeur associée du membre enum est obtenue en augmentant la valeur associée au membre enum qui précède par un.</span><span class="sxs-lookup"><span data-stu-id="bc22a-137">Otherwise, the associated value of the enum member is obtained by increasing the associated value of the textually preceding enum member by one.</span></span> <span data-ttu-id="bc22a-138">Cette valeur doit être dans la plage de valeurs qui peuvent être représentés par le type sous-jacent, sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="bc22a-138">This increased value must be within the range of values that can be represented by the underlying type, otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="bc22a-139">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-139">The example</span></span>

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

<span data-ttu-id="bc22a-140">Imprime les noms de membre enum et leurs valeurs associées.</span><span class="sxs-lookup"><span data-stu-id="bc22a-140">prints out the enum member names and their associated values.</span></span> <span data-ttu-id="bc22a-141">La sortie est la suivante :</span><span class="sxs-lookup"><span data-stu-id="bc22a-141">The output is:</span></span>

```
Red = 0
Green = 10
Blue = 11
```

<span data-ttu-id="bc22a-142">pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="bc22a-142">for the following reasons:</span></span>

*  <span data-ttu-id="bc22a-143">le membre enum `Red` est automatiquement attribué la valeur zéro (dans la mesure où il n’a aucun initialiseur et est le premier membre enum) ;</span><span class="sxs-lookup"><span data-stu-id="bc22a-143">the enum member `Red` is automatically assigned the value zero (since it has no initializer and is the first enum member);</span></span>
*  <span data-ttu-id="bc22a-144">le membre enum `Green` soit donnée explicitement la valeur `10`;</span><span class="sxs-lookup"><span data-stu-id="bc22a-144">the enum member `Green` is explicitly given the value `10`;</span></span>
*  <span data-ttu-id="bc22a-145">et le membre enum `Blue` est automatiquement attribué la valeur égale à celle du membre qui le précède.</span><span class="sxs-lookup"><span data-stu-id="bc22a-145">and the enum member `Blue` is automatically assigned the value one greater than the member that textually precedes it.</span></span>

<span data-ttu-id="bc22a-146">La valeur associée à un membre enum ne peut-être pas, directement ou indirectement, utilisez la valeur de son propre membre enum associé.</span><span class="sxs-lookup"><span data-stu-id="bc22a-146">The associated value of an enum member may not, directly or indirectly, use the value of its own associated enum member.</span></span> <span data-ttu-id="bc22a-147">Hormis cette restriction, les initialiseurs de membres enum librement parlent d’autres initialiseurs de membres enum, quelle que soit leur position du texte.</span><span class="sxs-lookup"><span data-stu-id="bc22a-147">Other than this circularity restriction, enum member initializers may freely refer to other enum member initializers, regardless of their textual position.</span></span> <span data-ttu-id="bc22a-148">Dans un initialiseur de membre enum, les valeurs d’autres membres enum sont toujours traitées comme ayant le type de leur type sous-jacent, afin que les conversions ne sont pas nécessaires lorsque vous faites référence à d’autres membres de l’enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-148">Within an enum member initializer, values of other enum members are always treated as having the type of their underlying type, so that casts are not necessary when referring to other enum members.</span></span>

<span data-ttu-id="bc22a-149">L’exemple</span><span class="sxs-lookup"><span data-stu-id="bc22a-149">The example</span></span>

```csharp
enum Circular
{
    A = B,
    B
}
```

<span data-ttu-id="bc22a-150">génère une erreur de compilation, car les déclarations de `A` et `B` sont circulaires.</span><span class="sxs-lookup"><span data-stu-id="bc22a-150">results in a compile-time error because the declarations of `A` and `B` are circular.</span></span> <span data-ttu-id="bc22a-151">`A` dépend de `B` explicitement, et `B` dépend `A` implicitement.</span><span class="sxs-lookup"><span data-stu-id="bc22a-151">`A` depends on `B` explicitly, and `B` depends on `A` implicitly.</span></span>

<span data-ttu-id="bc22a-152">Membres enum sont nommés et limités à la manière exactement aux champs dans les classes.</span><span class="sxs-lookup"><span data-stu-id="bc22a-152">Enum members are named and scoped in a manner exactly analogous to fields within classes.</span></span> <span data-ttu-id="bc22a-153">L’étendue d’un membre enum est le corps de son type enum conteneur.</span><span class="sxs-lookup"><span data-stu-id="bc22a-153">The scope of an enum member is the body of its containing enum type.</span></span> <span data-ttu-id="bc22a-154">Dans cette portée, enum membres peuvent être désignés par leur nom simple.</span><span class="sxs-lookup"><span data-stu-id="bc22a-154">Within that scope, enum members can be referred to by their simple name.</span></span> <span data-ttu-id="bc22a-155">Tous les autres codes, le nom d’un membre enum doit être qualifié avec le nom de son type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-155">From all other code, the name of an enum member must be qualified with the name of its enum type.</span></span> <span data-ttu-id="bc22a-156">Membres d’enum n’ont pas d’accessibilité déclarée, un membre enum est accessible que si son type enum conteneur est accessible.</span><span class="sxs-lookup"><span data-stu-id="bc22a-156">Enum members do not have any declared accessibility -- an enum member is accessible if its containing enum type is accessible.</span></span>

## <a name="the-systemenum-type"></a><span data-ttu-id="bc22a-157">Le type System.Enum</span><span class="sxs-lookup"><span data-stu-id="bc22a-157">The System.Enum type</span></span>

<span data-ttu-id="bc22a-158">Le type `System.Enum` est la classe de base abstraite de tous les types enum (c’est distinct et différent du type sous-jacent du type enum) et les membres hérités de `System.Enum` sont disponibles dans n’importe quel type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-158">The type `System.Enum` is the abstract base class of all enum types (this is distinct and different from the underlying type of the enum type), and the members inherited from `System.Enum` are available in any enum type.</span></span> <span data-ttu-id="bc22a-159">Une conversion boxing ([conversions Boxing](types.md#boxing-conversions)) existe à partir de n’importe quel type enum à `System.Enum`et une conversion unboxing ([les conversions Unboxing](types.md#unboxing-conversions)) existe à partir de `System.Enum` à n’importe quel type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-159">A boxing conversion ([Boxing conversions](types.md#boxing-conversions)) exists from any enum type to `System.Enum`, and an unboxing conversion ([Unboxing conversions](types.md#unboxing-conversions)) exists from `System.Enum` to any enum type.</span></span>

<span data-ttu-id="bc22a-160">Notez que `System.Enum` n’est pas lui-même un *type_de_liste*.</span><span class="sxs-lookup"><span data-stu-id="bc22a-160">Note that `System.Enum` is not itself an *enum_type*.</span></span> <span data-ttu-id="bc22a-161">Au lieu de cela, il est un *class_type* à partir de laquelle toutes les *type_de_liste*s sont dérivés.</span><span class="sxs-lookup"><span data-stu-id="bc22a-161">Rather, it is a *class_type* from which all *enum_type*s are derived.</span></span> <span data-ttu-id="bc22a-162">Le type `System.Enum` hérite du type `System.ValueType` ([System.ValueType le type](types.md#the-systemvaluetype-type)), qui, à son tour, hérite du type `object`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-162">The type `System.Enum` inherits from the type `System.ValueType` ([The System.ValueType type](types.md#the-systemvaluetype-type)), which, in turn, inherits from type `object`.</span></span> <span data-ttu-id="bc22a-163">Au moment de l’exécution, une valeur de type `System.Enum` peut être `null` ou une référence à une valeur boxed de n’importe quel type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-163">At run-time, a value of type `System.Enum` can be `null` or a reference to a boxed value of any enum type.</span></span>

## <a name="enum-values-and-operations"></a><span data-ttu-id="bc22a-164">Opérations et les valeurs Enum</span><span class="sxs-lookup"><span data-stu-id="bc22a-164">Enum values and operations</span></span>

<span data-ttu-id="bc22a-165">Chaque type enum définit un type distinct ; une conversion explicite d’énumération ([conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)) est nécessaire pour convertir entre un type enum et un type intégral, ou entre deux types enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-165">Each enum type defines a distinct type; an explicit enumeration conversion ([Explicit enumeration conversions](conversions.md#explicit-enumeration-conversions)) is required to convert between an enum type and an integral type, or between two enum types.</span></span> <span data-ttu-id="bc22a-166">L’ensemble de valeurs qui peut prendre un type enum n’est pas limité par ses membres enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-166">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="bc22a-167">En particulier, n’importe quelle valeur du type sous-jacent d’une énumération peut être casté en type enum et est une valeur valide distincte de ce type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-167">In particular, any value of the underlying type of an enum can be cast to the enum type, and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="bc22a-168">Les membres enum ont le type de leur type enum conteneur (sauf dans d’autres initialiseurs de membres enum : consultez [membres Enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="bc22a-168">Enum members have the type of their containing enum type (except within other enum member initializers: see [Enum members](enums.md#enum-members)).</span></span> <span data-ttu-id="bc22a-169">La valeur d’un membre enum déclaré dans le type enum `E` avec la valeur associée `v` est `(E)v`.</span><span class="sxs-lookup"><span data-stu-id="bc22a-169">The value of an enum member declared in enum type `E` with associated value `v` is `(E)v`.</span></span>

<span data-ttu-id="bc22a-170">Les opérateurs suivants peuvent être utilisés sur les valeurs de types enum : `==`, `!=`, `<`, `>`, `<=`, `>=`  ([opérateurs de comparaison d’énumération](expressions.md#enumeration-comparison-operators)) , binaire `+`  ([opérateur d’Addition](expressions.md#addition-operator)) binaire `-`  ([opérateur de soustraction](expressions.md#subtraction-operator)), `^`, `&` , `|`  ([Opérateurs logiques énumération](expressions.md#enumeration-logical-operators)), `~`  ([opérateur de complément de bits](expressions.md#bitwise-complement-operator)), `++` et `--`  ([D’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators) et [Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="bc22a-170">The following operators can be used on values of enum types: `==`, `!=`, `<`, `>`, `<=`, `>=` ([Enumeration comparison operators](expressions.md#enumeration-comparison-operators)), binary `+` ([Addition operator](expressions.md#addition-operator)), binary `-` ([Subtraction operator](expressions.md#subtraction-operator)), `^`, `&`, `|` ([Enumeration logical operators](expressions.md#enumeration-logical-operators)), `~` ([Bitwise complement operator](expressions.md#bitwise-complement-operator)), `++` and `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="bc22a-171">Chaque type enum dérive automatiquement la classe `System.Enum` (qui, à son tour, dérive de `System.ValueType` et `object`).</span><span class="sxs-lookup"><span data-stu-id="bc22a-171">Every enum type automatically derives from the class `System.Enum` (which, in turn, derives from `System.ValueType` and `object`).</span></span> <span data-ttu-id="bc22a-172">Par conséquent, les méthodes héritées et des propriétés de cette classe peuvent servir sur les valeurs d’un type enum.</span><span class="sxs-lookup"><span data-stu-id="bc22a-172">Thus, inherited methods and properties of this class can be used on values of an enum type.</span></span>
