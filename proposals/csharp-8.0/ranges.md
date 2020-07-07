---
ms.openlocfilehash: bee562d64c402e645879b7811cc82c971d8b61cc
ms.sourcegitcommit: 9b736b5b73321f18a04e02d26596388f7ceb42fc
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86052205"
---
# <a name="ranges"></a><span data-ttu-id="cb318-101">Plages</span><span class="sxs-lookup"><span data-stu-id="cb318-101">Ranges</span></span>

## <a name="summary"></a><span data-ttu-id="cb318-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="cb318-102">Summary</span></span>

<span data-ttu-id="cb318-103">Cette fonctionnalité consiste à offrir deux nouveaux opérateurs qui permettent de construire `System.Index` des `System.Range` objets et, et à les utiliser pour indexer/découper des collections au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="cb318-103">This feature is about delivering two new operators that allow constructing `System.Index` and `System.Range` objects, and using them to index/slice collections at runtime.</span></span>

## <a name="overview"></a><span data-ttu-id="cb318-104">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="cb318-104">Overview</span></span>

### <a name="well-known-types-and-members"></a><span data-ttu-id="cb318-105">Types et membres connus</span><span class="sxs-lookup"><span data-stu-id="cb318-105">Well-known types and members</span></span>

<span data-ttu-id="cb318-106">Pour utiliser les nouvelles formes syntaxiques pour `System.Index` et `System.Range` , de nouveaux types et membres connus peuvent être nécessaires, en fonction des formes syntaxiques utilisées.</span><span class="sxs-lookup"><span data-stu-id="cb318-106">To use the new syntactic forms for `System.Index` and `System.Range`, new well-known types and members may be necessary, depending on which syntactic forms are used.</span></span>

<span data-ttu-id="cb318-107">Pour utiliser l’opérateur « Hat » ( `^` ), les conditions suivantes sont requises :</span><span class="sxs-lookup"><span data-stu-id="cb318-107">To use the "hat" operator (`^`), the following is required</span></span>

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

<span data-ttu-id="cb318-108">Pour utiliser le `System.Index` type en tant qu’argument dans un accès à un élément de tableau, le membre suivant est requis :</span><span class="sxs-lookup"><span data-stu-id="cb318-108">To use the `System.Index` type as an argument in an array element access, the following member is required:</span></span>

```csharp
int System.Index.GetOffset(int length);
```

<span data-ttu-id="cb318-109">La `..` syntaxe de `System.Range` requiert le `System.Range` type, ainsi qu’un ou plusieurs des membres suivants :</span><span class="sxs-lookup"><span data-stu-id="cb318-109">The `..` syntax for `System.Range` will require the `System.Range` type, as well as one or more of the following members:</span></span>

```csharp
namespace System
{
    public readonly struct Range
    {
        public Range(System.Index start, System.Index end);
        public static Range StartAt(System.Index start);
        public static Range EndAt(System.Index end);
        public static Range All { get; }
    }
}
```

<span data-ttu-id="cb318-110">La `..` syntaxe permet d’absenter l’un ou l’autre, ou aucun de ses arguments.</span><span class="sxs-lookup"><span data-stu-id="cb318-110">The `..` syntax allows for either, both, or none of its arguments to be absent.</span></span> <span data-ttu-id="cb318-111">Quel que soit le nombre d’arguments, le `Range` constructeur est toujours suffisant pour utiliser la `Range` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="cb318-111">Regardless of the number of arguments, the `Range` constructor is always sufficient for using the `Range` syntax.</span></span> <span data-ttu-id="cb318-112">Toutefois, si l’un des autres membres est présent et qu’un ou plusieurs `..` arguments manquent, le membre approprié peut être substitué.</span><span class="sxs-lookup"><span data-stu-id="cb318-112">However, if any of the other members are present and one or more of the `..` arguments are missing, the appropriate member may be substituted.</span></span>

<span data-ttu-id="cb318-113">Enfin, pour une valeur de type `System.Range` à utiliser dans une expression d’accès à un élément de tableau, le membre suivant doit être présent :</span><span class="sxs-lookup"><span data-stu-id="cb318-113">Finally, for a value of type `System.Range` to be used in an array element access expression, the following member must be present:</span></span>

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a><span data-ttu-id="cb318-114">System. index</span><span class="sxs-lookup"><span data-stu-id="cb318-114">System.Index</span></span>

<span data-ttu-id="cb318-115">C# n’a aucun moyen d’indexer une collection à partir de la fin, mais la plupart des indexeurs utilisent la notion « from Start » ou une expression « length-i ».</span><span class="sxs-lookup"><span data-stu-id="cb318-115">C# has no way of indexing a collection from the end, but rather most indexers use the "from start" notion, or do a "length - i" expression.</span></span> <span data-ttu-id="cb318-116">Nous introduisons une nouvelle expression d’index qui signifie « à partir de la fin ».</span><span class="sxs-lookup"><span data-stu-id="cb318-116">We introduce a new Index expression that means "from the end".</span></span> <span data-ttu-id="cb318-117">La fonctionnalité introduira un nouvel opérateur « Hat » de préfixe unaire.</span><span class="sxs-lookup"><span data-stu-id="cb318-117">The feature will introduce a new unary prefix "hat" operator.</span></span> <span data-ttu-id="cb318-118">Son opérande unique doit pouvoir être converti en `System.Int32` .</span><span class="sxs-lookup"><span data-stu-id="cb318-118">Its single operand must be convertible to `System.Int32`.</span></span> <span data-ttu-id="cb318-119">Il sera abaissé dans l' `System.Index` appel de méthode de fabrique approprié.</span><span class="sxs-lookup"><span data-stu-id="cb318-119">It will be lowered into the appropriate `System.Index` factory method call.</span></span>

<span data-ttu-id="cb318-120">Nous enrichissons la grammaire de *unary_expression* avec la forme de syntaxe supplémentaire suivante :</span><span class="sxs-lookup"><span data-stu-id="cb318-120">We augment the grammar for *unary_expression* with the following additional syntax form:</span></span>

```antlr
unary_expression
    : '^' unary_expression
    ;
```

<span data-ttu-id="cb318-121">Nous appelons l' *index à partir de* l’opérateur end.</span><span class="sxs-lookup"><span data-stu-id="cb318-121">We call this the *index from end* operator.</span></span> <span data-ttu-id="cb318-122">L' *index prédéfini à partir des opérateurs end* est le suivant :</span><span class="sxs-lookup"><span data-stu-id="cb318-122">The predefined *index from end* operators are as follows:</span></span>

```csharp
System.Index operator ^(int fromEnd);
```

<span data-ttu-id="cb318-123">Le comportement de cet opérateur est défini uniquement pour les valeurs d’entrée supérieures ou égales à zéro.</span><span class="sxs-lookup"><span data-stu-id="cb318-123">The behavior of this operator is only defined for input values greater than or equal to zero.</span></span>

<span data-ttu-id="cb318-124">Exemples :</span><span class="sxs-lookup"><span data-stu-id="cb318-124">Examples:</span></span>

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var thirdItem = array[2];    // array[2]
var lastItem = array[^1];    // array[new Index(1, fromEnd: true)]
```

#### <a name="systemrange"></a><span data-ttu-id="cb318-125">System. Range</span><span class="sxs-lookup"><span data-stu-id="cb318-125">System.Range</span></span>

<span data-ttu-id="cb318-126">C# n’offre aucun moyen syntaxique d’accéder aux « plages » ou aux « tranches » de collections.</span><span class="sxs-lookup"><span data-stu-id="cb318-126">C# has no syntactic way to access "ranges" or "slices" of collections.</span></span> <span data-ttu-id="cb318-127">Généralement, les utilisateurs sont obligés d’implémenter des structures complexes pour filtrer/faire fonctionner sur des secteurs de mémoire, ou recourir à des méthodes LINQ comme `list.Skip(5).Take(2)` .</span><span class="sxs-lookup"><span data-stu-id="cb318-127">Usually users are forced to implement complex structures to filter/operate on slices of memory, or resort to LINQ methods like `list.Skip(5).Take(2)`.</span></span> <span data-ttu-id="cb318-128">Avec l’ajout de `System.Span<T>` et d’autres types similaires, il est plus important d’avoir ce type d’opération pris en charge sur un niveau plus profond dans le langage/l’exécution et de faire en sorte que l’interface soit unifiée.</span><span class="sxs-lookup"><span data-stu-id="cb318-128">With the addition of `System.Span<T>` and other similar types, it becomes more important to have this kind of operation supported on a deeper level in the language/runtime, and have the interface unified.</span></span>

<span data-ttu-id="cb318-129">Le langage introduira un nouvel opérateur de plage `x..y` .</span><span class="sxs-lookup"><span data-stu-id="cb318-129">The language will introduce a new range operator `x..y`.</span></span> <span data-ttu-id="cb318-130">Il s’agit d’un opérateur d’infixe binaire qui accepte deux expressions.</span><span class="sxs-lookup"><span data-stu-id="cb318-130">It is a binary infix operator that accepts two expressions.</span></span> <span data-ttu-id="cb318-131">L’un des opérandes peut être omis (exemples ci-dessous) et doit être convertible en `System.Index` .</span><span class="sxs-lookup"><span data-stu-id="cb318-131">Either operand can be omitted (examples below), and they have to be convertible to `System.Index`.</span></span> <span data-ttu-id="cb318-132">Il sera abaissé à l' `System.Range` appel de méthode de fabrique approprié.</span><span class="sxs-lookup"><span data-stu-id="cb318-132">It will be lowered to the appropriate `System.Range` factory method call.</span></span>

<span data-ttu-id="cb318-133">Nous remplaçons les règles de grammaire C# pour *multiplicative_expression* par les éléments suivants (afin d’introduire un nouveau niveau de priorité) :</span><span class="sxs-lookup"><span data-stu-id="cb318-133">We replace the C# grammar rules for *multiplicative_expression* with the following (in order to introduce a new precedence level):</span></span>

```antlr
range_expression
    : unary_expression
    | range_expression? '..' range_expression?
    ;

multiplicative_expression
    : range_expression
    | multiplicative_expression '*' range_expression
    | multiplicative_expression '/' range_expression
    | multiplicative_expression '%' range_expression
    ;
```

<span data-ttu-id="cb318-134">Toutes les formes de l' *opérateur de plage* ont la même priorité.</span><span class="sxs-lookup"><span data-stu-id="cb318-134">All forms of the *range operator* have the same precedence.</span></span> <span data-ttu-id="cb318-135">Ce nouveau groupe de précédence est inférieur aux *opérateurs unaires* et supérieur aux *opérateurs arithmétiques de multiplication*.</span><span class="sxs-lookup"><span data-stu-id="cb318-135">This new precedence group is lower than the *unary operators* and higher than the *multiplicative arithmetic operators*.</span></span>

<span data-ttu-id="cb318-136">Nous appelons l' `..` opérateur *Range*.</span><span class="sxs-lookup"><span data-stu-id="cb318-136">We call the `..` operator the *range operator*.</span></span> <span data-ttu-id="cb318-137">L’opérateur de plage intégré peut être compris approximativement pour correspondre à l’appel d’un opérateur intégré de ce formulaire :</span><span class="sxs-lookup"><span data-stu-id="cb318-137">The built-in range operator can roughly be understood to correspond to the invocation of a built-in operator of this form:</span></span>

```csharp
System.Range operator ..(Index start = 0, Index end = ^0);
```

<span data-ttu-id="cb318-138">Exemples :</span><span class="sxs-lookup"><span data-stu-id="cb318-138">Examples:</span></span>

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

<span data-ttu-id="cb318-139">En outre, `System.Index` doit avoir une conversion implicite de `System.Int32` , afin d’éviter la nécessité de surcharger le mélange des entiers et des index sur des signatures multidimensionnelles.</span><span class="sxs-lookup"><span data-stu-id="cb318-139">Moreover, `System.Index` should have an implicit conversion from `System.Int32`, in order to avoid the need to overload mixing integers and indexes over multi-dimensional signatures.</span></span>

## <a name="adding-index-and-range-support-to-existing-library-types"></a><span data-ttu-id="cb318-140">Ajout de la prise en charge des index et des plages aux types de bibliothèques existants</span><span class="sxs-lookup"><span data-stu-id="cb318-140">Adding Index and Range support to existing library types</span></span>

### <a name="implicit-index-support"></a><span data-ttu-id="cb318-141">Prise en charge d’index implicite</span><span class="sxs-lookup"><span data-stu-id="cb318-141">Implicit Index support</span></span>

<span data-ttu-id="cb318-142">Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Index` pour les types qui répondent aux critères suivants :</span><span class="sxs-lookup"><span data-stu-id="cb318-142">The language will provide an instance indexer member with a single parameter of type `Index` for types which meet the following criteria:</span></span>

- <span data-ttu-id="cb318-143">Le type est NBVAL.</span><span class="sxs-lookup"><span data-stu-id="cb318-143">The type is Countable.</span></span>
- <span data-ttu-id="cb318-144">Le type a un indexeur d’instance accessible qui accepte un seul `int` comme argument.</span><span class="sxs-lookup"><span data-stu-id="cb318-144">The type has an accessible instance indexer which takes a single `int` as the argument.</span></span>
- <span data-ttu-id="cb318-145">Le type n’a pas d’indexeur d’instance accessible qui prend `Index` comme premier paramètre.</span><span class="sxs-lookup"><span data-stu-id="cb318-145">The type does not have an accessible instance indexer which takes an `Index` as the first parameter.</span></span> <span data-ttu-id="cb318-146">`Index`Doit être le seul paramètre ou les paramètres restants doivent être facultatifs.</span><span class="sxs-lookup"><span data-stu-id="cb318-146">The `Index` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="cb318-147">Un type est ***compté*** s’il a une propriété nommée `Length` ou `Count` avec un accesseur Get accessible et un type de retour `int` .</span><span class="sxs-lookup"><span data-stu-id="cb318-147">A type is ***Countable*** if it  has a property named `Length` or `Count` with an accessible getter and a return type of `int`.</span></span> <span data-ttu-id="cb318-148">Le langage peut utiliser cette propriété pour convertir une expression de type `Index` en un `int` au point de l’expression sans qu’il soit nécessaire d’utiliser le type `Index` .</span><span class="sxs-lookup"><span data-stu-id="cb318-148">The language can make use of this property to convert an expression of type `Index` into an `int` at the point of the expression without the need to use the type `Index` at all.</span></span> <span data-ttu-id="cb318-149">Si `Length` et sont tous deux `Count` présents, `Length` sera préféré.</span><span class="sxs-lookup"><span data-stu-id="cb318-149">In case both `Length` and `Count` are present, `Length` will be preferred.</span></span> <span data-ttu-id="cb318-150">Par souci de simplicité, la proposition utilise le nom `Length` pour représenter `Count` ou `Length` .</span><span class="sxs-lookup"><span data-stu-id="cb318-150">For simplicity going forward, the proposal will use the name `Length` to represent `Count` or `Length`.</span></span>

<span data-ttu-id="cb318-151">Pour ces types, le langage agit comme s’il s’agissait d’un membre d’indexeur du formulaire `T this[Index index]` où `T` est le type de retour de l' `int` indexeur de base, y compris les `ref` Annotations de style.</span><span class="sxs-lookup"><span data-stu-id="cb318-151">For such types, the language will act as if there is an indexer member of the form `T this[Index index]` where `T` is the return type of the `int` based indexer including any `ref` style annotations.</span></span> <span data-ttu-id="cb318-152">Le nouveau membre aura les mêmes `get` membres et `set` avec l’accessibilité correspondante que l' `int` indexeur.</span><span class="sxs-lookup"><span data-stu-id="cb318-152">The new member will have the same `get` and `set` members with matching accessibility as the `int` indexer.</span></span>

<span data-ttu-id="cb318-153">Le nouvel indexeur sera implémenté en convertissant l’argument de type `Index` en un `int` et en émettant un appel à l' `int` indexeur de base.</span><span class="sxs-lookup"><span data-stu-id="cb318-153">The new indexer will be implemented by converting the argument of type `Index` into an `int` and emitting a call to the `int` based indexer.</span></span> <span data-ttu-id="cb318-154">À des fins de discussion, utilisons l’exemple de `receiver[expr]` .</span><span class="sxs-lookup"><span data-stu-id="cb318-154">For discussion purposes, let's use the example of `receiver[expr]`.</span></span> <span data-ttu-id="cb318-155">La conversion de `expr` en `int` se produit comme suit :</span><span class="sxs-lookup"><span data-stu-id="cb318-155">The conversion of `expr` to `int` will occur as follows:</span></span>

- <span data-ttu-id="cb318-156">Lorsque l’argument se présente sous la forme `^expr2` et que le type de `expr2` est `int` , il est converti en `receiver.Length - expr2` .</span><span class="sxs-lookup"><span data-stu-id="cb318-156">When the argument is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="cb318-157">Dans le cas contraire, elle sera traduite par `expr.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="cb318-157">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="cb318-158">Cela permet aux développeurs d’utiliser la `Index` fonctionnalité sur les types existants sans nécessiter de modification.</span><span class="sxs-lookup"><span data-stu-id="cb318-158">This allows for developers to use the `Index` feature on existing types without the need for modification.</span></span> <span data-ttu-id="cb318-159">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cb318-159">For example:</span></span>

``` csharp
List<char> list = ...;
var value = list[^1];

// Gets translated to
var value = list[list.Count - 1];
```

<span data-ttu-id="cb318-160">Les expressions et sont débordées `receiver` `Length` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="cb318-160">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="cb318-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cb318-161">For example:</span></span>

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int this[int index] => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get()[^1];
        Console.WriteLine(i);
    }
}
```

<span data-ttu-id="cb318-162">Ce code affiche « obtient la longueur 3 ».</span><span class="sxs-lookup"><span data-stu-id="cb318-162">This code will print "Get Length 3".</span></span>

### <a name="implicit-range-support"></a><span data-ttu-id="cb318-163">Prise en charge des plages implicites</span><span class="sxs-lookup"><span data-stu-id="cb318-163">Implicit Range support</span></span>

<span data-ttu-id="cb318-164">Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Range` pour les types qui répondent aux critères suivants :</span><span class="sxs-lookup"><span data-stu-id="cb318-164">The language will provide an instance indexer member with a single parameter of type `Range` for types which meet the following criteria:</span></span>

- <span data-ttu-id="cb318-165">Le type est NBVAL.</span><span class="sxs-lookup"><span data-stu-id="cb318-165">The type is Countable.</span></span>
- <span data-ttu-id="cb318-166">Le type a un membre accessible nommé `Slice` qui a deux paramètres de type `int` .</span><span class="sxs-lookup"><span data-stu-id="cb318-166">The type has an accessible member named `Slice` which has two parameters of type `int`.</span></span>
- <span data-ttu-id="cb318-167">Le type n’a pas d’indexeur d’instance qui accepte un seul `Range` comme premier paramètre.</span><span class="sxs-lookup"><span data-stu-id="cb318-167">The type does not have an instance indexer which takes a single `Range` as the first parameter.</span></span> <span data-ttu-id="cb318-168">`Range`Doit être le seul paramètre ou les paramètres restants doivent être facultatifs.</span><span class="sxs-lookup"><span data-stu-id="cb318-168">The `Range` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="cb318-169">Pour ces types, le langage est lié comme s’il s’agissait d’un membre d’indexeur de la forme `T this[Range range]` où `T` est le type de retour de la `Slice` méthode, y compris les `ref` Annotations de style.</span><span class="sxs-lookup"><span data-stu-id="cb318-169">For such types, the language will bind as if there is an indexer member of the form `T this[Range range]` where `T` is the return type of the `Slice` method including any `ref` style annotations.</span></span> <span data-ttu-id="cb318-170">Le nouveau membre aura également une accessibilité correspondante avec `Slice` .</span><span class="sxs-lookup"><span data-stu-id="cb318-170">The new member will also have matching accessibility with `Slice`.</span></span>

<span data-ttu-id="cb318-171">Lorsque l' `Range` indexeur de base est lié à une expression nommée `receiver` , il est abaissé en convertissant l' `Range` expression en deux valeurs qui sont ensuite passées à la `Slice` méthode.</span><span class="sxs-lookup"><span data-stu-id="cb318-171">When the `Range` based indexer is bound on an expression named `receiver`, it will be lowered by converting the `Range` expression into two values that are then passed to the `Slice` method.</span></span> <span data-ttu-id="cb318-172">À des fins de discussion, utilisons l’exemple de `receiver[expr]` .</span><span class="sxs-lookup"><span data-stu-id="cb318-172">For discussion purposes, let's use the example of `receiver[expr]`.</span></span>

<span data-ttu-id="cb318-173">Le premier argument de `Slice` sera obtenu en convertissant l’expression de plage typée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="cb318-173">The first argument of `Slice` will be obtained by converting the range typed expression in the following way:</span></span>

- <span data-ttu-id="cb318-174">Lorsque a `expr` la forme `expr1..expr2` (où `expr2` peut être omis) et a le `expr1` type `int` , il est émis en tant que `expr1` .</span><span class="sxs-lookup"><span data-stu-id="cb318-174">When `expr` is of the form `expr1..expr2` (where `expr2` can be omitted) and `expr1` has type `int`, then it will be emitted as `expr1`.</span></span>
- <span data-ttu-id="cb318-175">Lorsque `expr` a la forme `^expr1..expr2` (où `expr2` peut être omis), il est émis en tant que `receiver.Length - expr1` .</span><span class="sxs-lookup"><span data-stu-id="cb318-175">When `expr` is of the form `^expr1..expr2` (where `expr2` can be omitted), then it will be emitted as `receiver.Length - expr1`.</span></span>
- <span data-ttu-id="cb318-176">Lorsque `expr` a la forme `..expr2` (où `expr2` peut être omis), il est émis en tant que `0` .</span><span class="sxs-lookup"><span data-stu-id="cb318-176">When `expr` is of the form `..expr2` (where `expr2` can be omitted), then it will be emitted as `0`.</span></span>
- <span data-ttu-id="cb318-177">Dans le cas contraire, il sera émis sous la forme `expr.Start.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="cb318-177">Otherwise, it will be emitted as `expr.Start.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="cb318-178">Cette valeur sera réutilisée dans le calcul du deuxième `Slice` argument.</span><span class="sxs-lookup"><span data-stu-id="cb318-178">This value will be re-used in the calculation of the second `Slice` argument.</span></span> <span data-ttu-id="cb318-179">Dans ce cas, il est appelé `start` .</span><span class="sxs-lookup"><span data-stu-id="cb318-179">When doing so it will be referred to as `start`.</span></span> <span data-ttu-id="cb318-180">Le deuxième argument de `Slice` sera obtenu en convertissant l’expression de plage typée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="cb318-180">The second argument of `Slice` will be obtained by converting the range typed expression in the following way:</span></span>

- <span data-ttu-id="cb318-181">Lorsque a `expr` la forme `expr1..expr2` (où `expr1` peut être omis) et a le `expr2` type `int` , il est émis en tant que `expr2 - start` .</span><span class="sxs-lookup"><span data-stu-id="cb318-181">When `expr` is of the form `expr1..expr2` (where `expr1` can be omitted) and `expr2` has type `int`, then it will be emitted as `expr2 - start`.</span></span>
- <span data-ttu-id="cb318-182">Lorsque `expr` a la forme `expr1..^expr2` (où `expr1` peut être omis), il est émis en tant que `(receiver.Length - expr2) - start` .</span><span class="sxs-lookup"><span data-stu-id="cb318-182">When `expr` is of the form `expr1..^expr2` (where `expr1` can be omitted), then it will be emitted as `(receiver.Length - expr2) - start`.</span></span>
- <span data-ttu-id="cb318-183">Lorsque `expr` a la forme `expr1..` (où `expr1` peut être omis), il est émis en tant que `receiver.Length - start` .</span><span class="sxs-lookup"><span data-stu-id="cb318-183">When `expr` is of the form `expr1..` (where `expr1` can be omitted), then it will be emitted as `receiver.Length - start`.</span></span>
- <span data-ttu-id="cb318-184">Dans le cas contraire, il sera émis sous la forme `expr.End.GetOffset(receiver.Length) - start` .</span><span class="sxs-lookup"><span data-stu-id="cb318-184">Otherwise, it will be emitted as `expr.End.GetOffset(receiver.Length) - start`.</span></span>

<span data-ttu-id="cb318-185">Les `receiver` expressions, et sont débordées `Length` `expr` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="cb318-185">The `receiver`, `Length`, and `expr` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="cb318-186">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cb318-186">For example:</span></span>

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int[] Slice(int start, int length) {
        var slice = new int[length];
        Array.Copy(_array, start, slice, 0, length);
        return slice;
    }
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        var array = Get()[0..2];
        Console.WriteLine(array.length);
    }
}
```

<span data-ttu-id="cb318-187">Ce code affiche « obtient la longueur 2 ».</span><span class="sxs-lookup"><span data-stu-id="cb318-187">This code will print "Get Length 2".</span></span>

<span data-ttu-id="cb318-188">Le langage respecte le cas particulier des types connus suivants :</span><span class="sxs-lookup"><span data-stu-id="cb318-188">The language will special case the following known types:</span></span>

- <span data-ttu-id="cb318-189">`string`: la méthode `Substring` sera utilisée à la place de `Slice` .</span><span class="sxs-lookup"><span data-stu-id="cb318-189">`string`: the method `Substring` will be used instead of `Slice`.</span></span>
- <span data-ttu-id="cb318-190">`array`: la méthode `System.Reflection.CompilerServices.GetSubArray` sera utilisée à la place de `Slice` .</span><span class="sxs-lookup"><span data-stu-id="cb318-190">`array`: the method `System.Reflection.CompilerServices.GetSubArray` will be used instead of `Slice`.</span></span>

## <a name="alternatives"></a><span data-ttu-id="cb318-191">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="cb318-191">Alternatives</span></span>

<span data-ttu-id="cb318-192">Les nouveaux opérateurs ( `^` et `..` ) sont des sucres syntaxiques.</span><span class="sxs-lookup"><span data-stu-id="cb318-192">The new operators (`^` and `..`) are syntactic sugar.</span></span> <span data-ttu-id="cb318-193">La fonctionnalité peut être implémentée par des appels explicites à `System.Index` et des `System.Range` méthodes de fabrique, mais elle génère beaucoup plus de code réutilisable, et l’expérience n’est pas intuitive.</span><span class="sxs-lookup"><span data-stu-id="cb318-193">The functionality can be implemented by explicit calls to `System.Index` and `System.Range` factory methods, but it will result in a lot more boilerplate code, and the experience will be unintuitive.</span></span>

## <a name="il-representation"></a><span data-ttu-id="cb318-194">Représentation IL</span><span class="sxs-lookup"><span data-stu-id="cb318-194">IL Representation</span></span>

<span data-ttu-id="cb318-195">Ces deux opérateurs seront abaissés aux appels d’indexeur/de méthode normaux, sans modification des couches de compilateur suivantes.</span><span class="sxs-lookup"><span data-stu-id="cb318-195">These two operators will be lowered to regular indexer/method calls, with no change in subsequent compiler layers.</span></span>

## <a name="runtime-behavior"></a><span data-ttu-id="cb318-196">Comportement au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="cb318-196">Runtime behavior</span></span>

- <span data-ttu-id="cb318-197">Le compilateur peut optimiser des indexeurs pour des types intégrés tels que des tableaux et des chaînes, et réduire l’indexation aux méthodes existantes appropriées.</span><span class="sxs-lookup"><span data-stu-id="cb318-197">Compiler can optimize indexers for built-in types like arrays and strings, and lower the indexing to the appropriate existing methods.</span></span>
- <span data-ttu-id="cb318-198">`System.Index`lève une exception si elle est construite avec une valeur négative.</span><span class="sxs-lookup"><span data-stu-id="cb318-198">`System.Index` will throw if constructed with a negative value.</span></span>
- <span data-ttu-id="cb318-199">`^0`ne lève pas, mais convertit la longueur de la collection/énumérable à laquelle il est fourni.</span><span class="sxs-lookup"><span data-stu-id="cb318-199">`^0` does not throw, but it translates to the length of the collection/enumerable it is supplied to.</span></span>
- <span data-ttu-id="cb318-200">`Range.All`est sémantiquement équivalent à `0..^0` , et peut être déconstruite sur ces index.</span><span class="sxs-lookup"><span data-stu-id="cb318-200">`Range.All` is semantically equivalent to `0..^0`, and can be deconstructed to these indices.</span></span>

## <a name="considerations"></a><span data-ttu-id="cb318-201">Considérations</span><span class="sxs-lookup"><span data-stu-id="cb318-201">Considerations</span></span>

### <a name="detect-indexable-based-on-icollection"></a><span data-ttu-id="cb318-202">Détecter les indexables basés sur ICollection</span><span class="sxs-lookup"><span data-stu-id="cb318-202">Detect Indexable based on ICollection</span></span>

<span data-ttu-id="cb318-203">L’inspiration de ce comportement était des initialiseurs de collection.</span><span class="sxs-lookup"><span data-stu-id="cb318-203">The inspiration for this behavior was collection initializers.</span></span> <span data-ttu-id="cb318-204">Utilisation de la structure d’un type pour indiquer qu’il a opté pour une fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="cb318-204">Using the structure of a type to convey that it had opted into a feature.</span></span> <span data-ttu-id="cb318-205">Dans le cas des initialiseurs de collection, les types peuvent accepter la fonctionnalité en implémentant l’interface `IEnumerable` (non générique).</span><span class="sxs-lookup"><span data-stu-id="cb318-205">In the case of collection initializers types can opt into the feature by implementing the interface `IEnumerable` (non generic).</span></span>

<span data-ttu-id="cb318-206">Cette proposition nécessitait initialement que les types implémentent `ICollection` pour qualifier comme indexable.</span><span class="sxs-lookup"><span data-stu-id="cb318-206">This proposal initially required that types implement `ICollection` in order to qualify as Indexable.</span></span> <span data-ttu-id="cb318-207">Cela nécessitait un certain nombre de cas particuliers :</span><span class="sxs-lookup"><span data-stu-id="cb318-207">That required a number of special cases though:</span></span>

- <span data-ttu-id="cb318-208">`ref struct`: celles-ci ne peuvent pas implémenter `Span<T>` d’interfaces, mais sont idéales pour la prise en charge des index/plages.</span><span class="sxs-lookup"><span data-stu-id="cb318-208">`ref struct`: these cannot implement interfaces yet types like `Span<T>` are ideal for index / range support.</span></span>
- <span data-ttu-id="cb318-209">`string`: n’implémente pas `ICollection` et n’ajoute pas qui `interface` a un coût élevé.</span><span class="sxs-lookup"><span data-stu-id="cb318-209">`string`: does not implement `ICollection` and adding that `interface` has a large cost.</span></span>

<span data-ttu-id="cb318-210">Cela signifie que la prise en charge de la casse spéciale des types de clés est déjà nécessaire.</span><span class="sxs-lookup"><span data-stu-id="cb318-210">This means to support key types special casing is already needed.</span></span> <span data-ttu-id="cb318-211">La casse spéciale de `string` est moins intéressante, car le langage le fait dans d’autres zones ( `foreach` abaissement, constantes, etc.). La casse spéciale de `ref struct` est davantage en ce qui concerne la casse particulière d’une classe entière de types.</span><span class="sxs-lookup"><span data-stu-id="cb318-211">The special casing of `string` is less interesting as the language does this in other areas (`foreach` lowering, constants, etc ...). The special casing of `ref struct` is more concerning as it's special casing an entire class of types.</span></span> <span data-ttu-id="cb318-212">Ils sont étiquetés comme indexables s’ils ont simplement une propriété nommée `Count` avec un type de retour `int` .</span><span class="sxs-lookup"><span data-stu-id="cb318-212">They get labeled as Indexable if they simply have a property named `Count` with a return type of `int`.</span></span>

<span data-ttu-id="cb318-213">Après considération, la conception a été normalisée pour indiquer que tout type qui a une propriété `Count`  /  `Length` avec un type de retour `int` est indexable.</span><span class="sxs-lookup"><span data-stu-id="cb318-213">After consideration the design was normalized to say that any type which has a property `Count` / `Length` with a return type of `int` is Indexable.</span></span> <span data-ttu-id="cb318-214">Qui supprime toute la casse spéciale, même pour les `string` tableaux et.</span><span class="sxs-lookup"><span data-stu-id="cb318-214">That removes all special casing, even for `string` and arrays.</span></span>

### <a name="detect-just-count"></a><span data-ttu-id="cb318-215">Détecter uniquement le nombre</span><span class="sxs-lookup"><span data-stu-id="cb318-215">Detect just Count</span></span>

<span data-ttu-id="cb318-216">La détection des noms de propriété `Count` ou `Length` complique la conception.</span><span class="sxs-lookup"><span data-stu-id="cb318-216">Detecting on the property names `Count` or `Length` does complicate the design a bit.</span></span> <span data-ttu-id="cb318-217">Le choix d’un seul pour normaliser n’est pas suffisant, car il finit par exclure un grand nombre de types :</span><span class="sxs-lookup"><span data-stu-id="cb318-217">Picking just one to standardize though is not sufficient as it ends up excluding a large number of types:</span></span>

- <span data-ttu-id="cb318-218">Utilisation `Length` : exclut à peu près chaque collection dans System. Collections et les sous-espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="cb318-218">Use `Length`: excludes pretty much every collection in System.Collections and sub-namespaces.</span></span> <span data-ttu-id="cb318-219">Celles-ci ont tendance à dériver de `ICollection` et, par conséquent, préfèrent dépasser la `Count` longueur.</span><span class="sxs-lookup"><span data-stu-id="cb318-219">Those tend to derive from `ICollection` and hence prefer `Count` over length.</span></span>
- <span data-ttu-id="cb318-220">Utilisation `Count` : exclut `string` , `Span<T>` les tableaux et les types les plus `ref struct` basés</span><span class="sxs-lookup"><span data-stu-id="cb318-220">Use `Count`: excludes `string`, arrays, `Span<T>` and most `ref struct` based types</span></span>

<span data-ttu-id="cb318-221">La plus grande complication de la détection initiale de types indexables est dépensée par sa simplification dans d’autres aspects.</span><span class="sxs-lookup"><span data-stu-id="cb318-221">The extra complication on the initial detection of Indexable types is outweighed by its simplification in other aspects.</span></span>

### <a name="choice-of-slice-as-a-name"></a><span data-ttu-id="cb318-222">Choix d’un secteur en tant que nom</span><span class="sxs-lookup"><span data-stu-id="cb318-222">Choice of Slice as a name</span></span>

<span data-ttu-id="cb318-223">Le nom `Slice` a été choisi, car il s’agit du nom standard de facto pour les opérations de style de découpage dans .net.</span><span class="sxs-lookup"><span data-stu-id="cb318-223">The name `Slice` was chosen as it's the de-facto standard name for slice style operations in .NET.</span></span> <span data-ttu-id="cb318-224">À compter de netcoreapp 2.1, tous les types de style d’étendue utilisent le nom `Slice` pour les opérations de découpage.</span><span class="sxs-lookup"><span data-stu-id="cb318-224">Starting with netcoreapp2.1 all span style types use the name `Slice` for slicing operations.</span></span> <span data-ttu-id="cb318-225">Avant netcoreapp 2.1, il n’y a pas vraiment d’exemples de découpage à rechercher pour obtenir un exemple.</span><span class="sxs-lookup"><span data-stu-id="cb318-225">Prior to netcoreapp2.1 there really aren't any examples of slicing to look to for an example.</span></span> <span data-ttu-id="cb318-226">Les types tels que `List<T>` , `ArraySegment<T>` , `SortedList<T>` seraient idéaux pour le découpage, mais le concept n’existait pas lors de l’ajout de types.</span><span class="sxs-lookup"><span data-stu-id="cb318-226">Types like `List<T>`, `ArraySegment<T>`, `SortedList<T>` would've been ideal for slicing but the concept didn't exist when types were added.</span></span>

<span data-ttu-id="cb318-227">Par conséquent, `Slice` en tant que seul exemple, il a été choisi comme nom.</span><span class="sxs-lookup"><span data-stu-id="cb318-227">Thus, `Slice` being the sole example, it was chosen as the name.</span></span>

### <a name="index-target-type-conversion"></a><span data-ttu-id="cb318-228">Conversion du type de cible d’index</span><span class="sxs-lookup"><span data-stu-id="cb318-228">Index target type conversion</span></span>

<span data-ttu-id="cb318-229">Une autre façon d’afficher la `Index` transformation dans une expression d’indexeur est de convertir un type cible.</span><span class="sxs-lookup"><span data-stu-id="cb318-229">Another way to view the `Index` transformation in an indexer expression is as a target type conversion.</span></span> <span data-ttu-id="cb318-230">Au lieu de créer une liaison comme s’il s’agissait d’un membre du formulaire `return_type this[Index]` , le langage assigne à la place une conversion de cible typée en `int` .</span><span class="sxs-lookup"><span data-stu-id="cb318-230">Instead of binding as if there is a member of the form `return_type this[Index]`, the language instead assigns a target typed conversion to `int`.</span></span>

<span data-ttu-id="cb318-231">Ce concept peut être généralisé à l’accès de tous les membres sur les types dénombrables.</span><span class="sxs-lookup"><span data-stu-id="cb318-231">This concept could be generalized to all member access on Countable types.</span></span> <span data-ttu-id="cb318-232">Chaque fois qu’une expression de type `Index` est utilisée en tant qu’argument pour un appel de membre d’instance et que le récepteur est compté, l’expression aura une conversion de type cible en `int` .</span><span class="sxs-lookup"><span data-stu-id="cb318-232">Whenever an expression with type `Index` is used as an argument to an instance member invocation and the receiver is Countable then the expression will have a target type conversion to `int`.</span></span> <span data-ttu-id="cb318-233">Les appels de membres applicables pour cette conversion incluent les méthodes, les indexeurs, les propriétés, les méthodes d’extension, etc. Seuls les constructeurs sont exclus, car ils n’ont pas de récepteur.</span><span class="sxs-lookup"><span data-stu-id="cb318-233">The member invocations applicable for this conversion include methods, indexers, properties, extension methods, etc ... Only constructors are excluded as they have no receiver.</span></span>

<span data-ttu-id="cb318-234">La conversion de type cible sera implémentée comme suit pour toute expression qui a un type `Index` .</span><span class="sxs-lookup"><span data-stu-id="cb318-234">The target type conversion will be implemented as follows for any expression which has a type of `Index`.</span></span> <span data-ttu-id="cb318-235">À des fins de discussion, vous pouvez utiliser l’exemple de `receiver[expr]` :</span><span class="sxs-lookup"><span data-stu-id="cb318-235">For discussion purposes lets use the example of `receiver[expr]`:</span></span>

- <span data-ttu-id="cb318-236">Quand `expr` a la forme `^expr2` et que le type de `expr2` est `int` , il est converti en `receiver.Length - expr2` .</span><span class="sxs-lookup"><span data-stu-id="cb318-236">When `expr` is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="cb318-237">Dans le cas contraire, elle sera traduite par `expr.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="cb318-237">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="cb318-238">Les expressions et sont débordées `receiver` `Length` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="cb318-238">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="cb318-239">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cb318-239">For example:</span></span>

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int GetAt(int index) => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get().GetAt(^1);
        Console.WriteLine(i);
    }
}
```

<span data-ttu-id="cb318-240">Ce code affiche « obtient la longueur 3 ».</span><span class="sxs-lookup"><span data-stu-id="cb318-240">This code will print "Get Length 3".</span></span>

<span data-ttu-id="cb318-241">Cette fonctionnalité est bénéfique pour tout membre qui avait un paramètre qui représentait un index.</span><span class="sxs-lookup"><span data-stu-id="cb318-241">This feature would be beneficial to any member which had a parameter that represented an index.</span></span> <span data-ttu-id="cb318-242">Par exemple, `List<T>.InsertAt`.</span><span class="sxs-lookup"><span data-stu-id="cb318-242">For example `List<T>.InsertAt`.</span></span> <span data-ttu-id="cb318-243">Cela présente également un risque de confusion, car le langage ne peut pas vous aider à déterminer si une expression est destinée à l’indexation.</span><span class="sxs-lookup"><span data-stu-id="cb318-243">This also has the potential for confusion as the language can't give any guidance as to whether or not an expression is meant for indexing.</span></span> <span data-ttu-id="cb318-244">Tout ce qu’il peut faire est de convertir n’importe quelle `Index` expression en lors de l' `int` appel d’un membre sur un type compté.</span><span class="sxs-lookup"><span data-stu-id="cb318-244">All it can do is convert any `Index` expression to `int` when invoking a member on a Countable type.</span></span>

<span data-ttu-id="cb318-245">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="cb318-245">Restrictions:</span></span>

- <span data-ttu-id="cb318-246">Cette conversion s’applique uniquement lorsque l’expression avec le type `Index` est directement un argument du membre.</span><span class="sxs-lookup"><span data-stu-id="cb318-246">This conversion is only applicable when the expression with type `Index` is directly an argument to the member.</span></span> <span data-ttu-id="cb318-247">Elle ne s’applique pas aux expressions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="cb318-247">It would not apply to any nested expressions.</span></span>

## <a name="decisions-made-during-implementation"></a><span data-ttu-id="cb318-248">Décisions prises pendant l’implémentation</span><span class="sxs-lookup"><span data-stu-id="cb318-248">Decisions made during implementation</span></span>

- <span data-ttu-id="cb318-249">Tous les membres du modèle doivent être membres d’instance</span><span class="sxs-lookup"><span data-stu-id="cb318-249">All members in the pattern must be instance members</span></span>
- <span data-ttu-id="cb318-250">Si une méthode de longueur est trouvée mais que son type de retour est incorrect, continuez à rechercher le nombre</span><span class="sxs-lookup"><span data-stu-id="cb318-250">If a Length method is found but it has the wrong return type, continue looking for Count</span></span>
- <span data-ttu-id="cb318-251">L’indexeur utilisé pour le modèle d’index doit avoir exactement un paramètre int</span><span class="sxs-lookup"><span data-stu-id="cb318-251">The indexer used for the Index pattern must have exactly one int parameter</span></span>
- <span data-ttu-id="cb318-252">La méthode Slice utilisée pour le modèle Range doit avoir exactement deux paramètres int</span><span class="sxs-lookup"><span data-stu-id="cb318-252">The Slice method used for the Range pattern must have exactly two int parameters</span></span>
- <span data-ttu-id="cb318-253">Lorsque vous recherchez les membres du modèle, nous cherchons les définitions d’origine, et non les membres construits.</span><span class="sxs-lookup"><span data-stu-id="cb318-253">When looking for the pattern members, we look for original definitions, not constructed members</span></span>

## <a name="design-meetings"></a><span data-ttu-id="cb318-254">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="cb318-254">Design meetings</span></span>

- [<span data-ttu-id="cb318-255">10 janvier 2018</span><span class="sxs-lookup"><span data-stu-id="cb318-255">Jan 10, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-10.md)
- [<span data-ttu-id="cb318-256">18 janvier 2018</span><span class="sxs-lookup"><span data-stu-id="cb318-256">Jan 18, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-18.md)
- [<span data-ttu-id="cb318-257">22 janvier, 2018</span><span class="sxs-lookup"><span data-stu-id="cb318-257">Jan 22, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-22.md)
- [<span data-ttu-id="cb318-258">3 décembre 2018</span><span class="sxs-lookup"><span data-stu-id="cb318-258">Dec 3, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-12-03.md)
- [<span data-ttu-id="cb318-259">25 mars, 2019</span><span class="sxs-lookup"><span data-stu-id="cb318-259">Mar 25, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-03-25.md#pattern-based-indexing-with-index-and-range)
- [<span data-ttu-id="cb318-260">1er avril, 2019</span><span class="sxs-lookup"><span data-stu-id="cb318-260">April 1st, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md)
- [<span data-ttu-id="cb318-261">15 avril 2019</span><span class="sxs-lookup"><span data-stu-id="cb318-261">April 15, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-15.md#follow-up-decisions-for-pattern-based-indexrange)
