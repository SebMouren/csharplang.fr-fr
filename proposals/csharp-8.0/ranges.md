---
ms.openlocfilehash: d6519ff57b4a98c4eec8ccbf310303432ac3255e
ms.sourcegitcommit: 65ea1e6dc02853e37e7f2088e2b6cc08d01d1044
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "79485027"
---
# <a name="ranges"></a><span data-ttu-id="4a738-101">Plages</span><span class="sxs-lookup"><span data-stu-id="4a738-101">Ranges</span></span>

## <a name="summary"></a><span data-ttu-id="4a738-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="4a738-102">Summary</span></span>

<span data-ttu-id="4a738-103">Cette fonctionnalité consiste à offrir deux nouveaux opérateurs qui permettent de construire des objets `System.Index` et `System.Range` et de les utiliser pour indexer/découper des collections au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="4a738-103">This feature is about delivering two new operators that allow constructing `System.Index` and `System.Range` objects, and using them to index/slice collections at runtime.</span></span>

## <a name="overview"></a><span data-ttu-id="4a738-104">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="4a738-104">Overview</span></span>

### <a name="well-known-types-and-members"></a><span data-ttu-id="4a738-105">Types et membres connus</span><span class="sxs-lookup"><span data-stu-id="4a738-105">Well-known types and members</span></span>

<span data-ttu-id="4a738-106">Pour utiliser les nouvelles formes syntaxiques pour `System.Index` et `System.Range`, de nouveaux types et membres connus peuvent être nécessaires, en fonction des formes syntaxiques utilisées.</span><span class="sxs-lookup"><span data-stu-id="4a738-106">To use the new syntactic forms for `System.Index` and `System.Range`, new well-known types and members may be necessary, depending on which syntactic forms are used.</span></span>

<span data-ttu-id="4a738-107">Pour utiliser l’opérateur « Hat » (`^`), les conditions suivantes sont requises :</span><span class="sxs-lookup"><span data-stu-id="4a738-107">To use the "hat" operator (`^`), the following is required</span></span>

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

<span data-ttu-id="4a738-108">Pour utiliser le type de `System.Index` comme argument dans un accès à un élément de tableau, le membre suivant est requis :</span><span class="sxs-lookup"><span data-stu-id="4a738-108">To use the `System.Index` type as an argument in an array element access, the following member is required:</span></span>

```csharp
int System.Index.GetOffset(int length);
```

<span data-ttu-id="4a738-109">La syntaxe de `..` pour `System.Range` nécessite le type de `System.Range`, ainsi qu’un ou plusieurs des membres suivants :</span><span class="sxs-lookup"><span data-stu-id="4a738-109">The `..` syntax for `System.Range` will require the `System.Range` type, as well as one or more of the following members:</span></span>

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

<span data-ttu-id="4a738-110">La syntaxe de `..` permet d’absenter l’un ou l’autre, ou aucun de ses arguments.</span><span class="sxs-lookup"><span data-stu-id="4a738-110">The `..` syntax allows for either, both, or none of its arguments to be absent.</span></span> <span data-ttu-id="4a738-111">Quel que soit le nombre d’arguments, le constructeur `Range` est toujours suffisant pour utiliser la syntaxe `Range`.</span><span class="sxs-lookup"><span data-stu-id="4a738-111">Regardless of the number of arguments, the `Range` constructor is always sufficient for using the `Range` syntax.</span></span> <span data-ttu-id="4a738-112">Toutefois, si l’un des autres membres est présent et qu’un ou plusieurs arguments `..` sont absents, le membre approprié peut être substitué.</span><span class="sxs-lookup"><span data-stu-id="4a738-112">However, if any of the other members are present and one or more of the `..` arguments are missing, the appropriate member may be substituted.</span></span>

<span data-ttu-id="4a738-113">Enfin, pour une valeur de type `System.Range` à utiliser dans une expression d’accès à un élément de tableau, le membre suivant doit être présent :</span><span class="sxs-lookup"><span data-stu-id="4a738-113">Finally, for a value of type `System.Range` to be used in an array element access expression, the following member must be present:</span></span>

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a><span data-ttu-id="4a738-114">System. index</span><span class="sxs-lookup"><span data-stu-id="4a738-114">System.Index</span></span>

<span data-ttu-id="4a738-115">C#n’a aucun moyen d’indexer une collection à partir de la fin, mais la plupart des indexeurs utilisent la notion « from Start » ou une expression « length-i ».</span><span class="sxs-lookup"><span data-stu-id="4a738-115">C# has no way of indexing a collection from the end, but rather most indexers use the "from start" notion, or do a "length - i" expression.</span></span> <span data-ttu-id="4a738-116">Nous introduisons une nouvelle expression d’index qui signifie « à partir de la fin ».</span><span class="sxs-lookup"><span data-stu-id="4a738-116">We introduce a new Index expression that means "from the end".</span></span> <span data-ttu-id="4a738-117">La fonctionnalité introduira un nouvel opérateur « Hat » de préfixe unaire.</span><span class="sxs-lookup"><span data-stu-id="4a738-117">The feature will introduce a new unary prefix "hat" operator.</span></span> <span data-ttu-id="4a738-118">Son opérande unique doit pouvoir être converti en `System.Int32`.</span><span class="sxs-lookup"><span data-stu-id="4a738-118">Its single operand must be convertible to `System.Int32`.</span></span> <span data-ttu-id="4a738-119">Il sera abaissé dans l’appel de méthode de fabrique `System.Index` approprié.</span><span class="sxs-lookup"><span data-stu-id="4a738-119">It will be lowered into the appropriate `System.Index` factory method call.</span></span>

<span data-ttu-id="4a738-120">Nous enrichissons la grammaire de *unary_expression* avec la forme de syntaxe supplémentaire suivante :</span><span class="sxs-lookup"><span data-stu-id="4a738-120">We augment the grammar for *unary_expression* with the following additional syntax form:</span></span>

```antlr
unary_expression
    : '^' unary_expression
    ;
```

<span data-ttu-id="4a738-121">Nous appelons l' *index à partir de* l’opérateur end.</span><span class="sxs-lookup"><span data-stu-id="4a738-121">We call this the *index from end* operator.</span></span> <span data-ttu-id="4a738-122">L' *index prédéfini à partir des opérateurs end* est le suivant :</span><span class="sxs-lookup"><span data-stu-id="4a738-122">The predefined *index from end* operators are as follows:</span></span>

```csharp
    System.Index operator ^(int fromEnd);
```

<span data-ttu-id="4a738-123">Le comportement de cet opérateur est défini uniquement pour les valeurs d’entrée supérieures ou égales à zéro.</span><span class="sxs-lookup"><span data-stu-id="4a738-123">The behavior of this operator is only defined for input values greater than or equal to zero.</span></span>

<span data-ttu-id="4a738-124">Exemples :</span><span class="sxs-lookup"><span data-stu-id="4a738-124">Examples:</span></span>

```csharp
var thirdItem = list[2];                // list[2]
var lastItem = list[^1];                // list[Index.CreateFromEnd(1)]

var multiDimensional = list[3, ^2]      // list[3, Index.CreateFromEnd(2)]
```

#### <a name="systemrange"></a><span data-ttu-id="4a738-125">System. Range</span><span class="sxs-lookup"><span data-stu-id="4a738-125">System.Range</span></span>

<span data-ttu-id="4a738-126">C#n’offre aucun moyen syntaxique d’accéder aux « plages » ou aux « tranches » de collections.</span><span class="sxs-lookup"><span data-stu-id="4a738-126">C# has no syntactic way to access "ranges" or "slices" of collections.</span></span> <span data-ttu-id="4a738-127">Généralement, les utilisateurs sont obligés d’implémenter des structures complexes pour filtrer/faire fonctionner sur des secteurs de mémoire, ou recourir à des méthodes LINQ comme `list.Skip(5).Take(2)`.</span><span class="sxs-lookup"><span data-stu-id="4a738-127">Usually users are forced to implement complex structures to filter/operate on slices of memory, or resort to LINQ methods like `list.Skip(5).Take(2)`.</span></span> <span data-ttu-id="4a738-128">Avec l’ajout de `System.Span<T>` et d’autres types similaires, il est plus important d’avoir ce type d’opération pris en charge sur un niveau plus profond dans le langage et l’exécution, et de faire en sorte que l’interface soit unifiée.</span><span class="sxs-lookup"><span data-stu-id="4a738-128">With the addition of `System.Span<T>` and other similar types, it becomes more important to have this kind of operation supported on a deeper level in the language/runtime, and have the interface unified.</span></span>

<span data-ttu-id="4a738-129">Le langage introduira un nouvel opérateur de plage `x..y`.</span><span class="sxs-lookup"><span data-stu-id="4a738-129">The language will introduce a new range operator `x..y`.</span></span> <span data-ttu-id="4a738-130">Il s’agit d’un opérateur d’infixe binaire qui accepte deux expressions.</span><span class="sxs-lookup"><span data-stu-id="4a738-130">It is a binary infix operator that accepts two expressions.</span></span> <span data-ttu-id="4a738-131">L’un des opérandes peut être omis (exemples ci-dessous) et doit être convertible en `System.Index`.</span><span class="sxs-lookup"><span data-stu-id="4a738-131">Either operand can be omitted (examples below), and they have to be convertible to `System.Index`.</span></span> <span data-ttu-id="4a738-132">Il sera abaissé à l’appel de méthode de fabrique `System.Range` approprié.</span><span class="sxs-lookup"><span data-stu-id="4a738-132">It will be lowered to the appropriate `System.Range` factory method call.</span></span>

<span data-ttu-id="4a738-133">-Nous remplaçons C# les règles de grammaire de *multiplicative_expression* par le code suivant (afin d’introduire un nouveau niveau de priorité) :</span><span class="sxs-lookup"><span data-stu-id="4a738-133">-We replace the C# grammar rules for *multiplicative_expression* with the following (in order to introduce a new precedence level):</span></span>

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

<span data-ttu-id="4a738-134">Toutes les formes de l' *opérateur de plage* ont la même priorité.</span><span class="sxs-lookup"><span data-stu-id="4a738-134">All forms of the *range operator* have the same precedence.</span></span> <span data-ttu-id="4a738-135">Ce nouveau groupe de précédence est inférieur aux *opérateurs unaires* et supérieur aux *opérateurs arithmétiques mulitiplicative*.</span><span class="sxs-lookup"><span data-stu-id="4a738-135">This new precedence group is lower than the *unary operators* and higher than the *mulitiplicative arithmetic operators*.</span></span>

<span data-ttu-id="4a738-136">Nous appelons l’opérateur de `..` l’opérateur de *plage*.</span><span class="sxs-lookup"><span data-stu-id="4a738-136">We call the `..` operator the *range operator*.</span></span> <span data-ttu-id="4a738-137">L’opérateur de plage intégré peut être compris approximativement pour correspondre à l’appel d’un opérateur intégré de ce formulaire :</span><span class="sxs-lookup"><span data-stu-id="4a738-137">The built-in range operator can roughly be understood to correspond to the invocation of a built-in operator of this form:</span></span>

```csharp
    System.Range operator ..(Index start = 0, Index end = ^0);
```

<span data-ttu-id="4a738-138">Exemples :</span><span class="sxs-lookup"><span data-stu-id="4a738-138">Examples:</span></span>

```csharp
var slice1 = list[2..^3];               // list[Range.Create(2, Index.CreateFromEnd(3))]
var slice2 = list[..^3];                // list[Range.ToEnd(Index.CreateFromEnd(3))]
var slice3 = list[2..];                 // list[Range.FromStart(2)]
var slice4 = list[..];                  // list[Range.All]

var multiDimensional = list[1..2, ..]   // list[Range.Create(1, 2), Range.All]
```

<span data-ttu-id="4a738-139">De plus, `System.Index` doit avoir une conversion implicite à partir de `System.Int32`, afin de ne pas avoir besoin de surcharger pour mélanger des entiers et des index sur des signatures multidimensionnelles.</span><span class="sxs-lookup"><span data-stu-id="4a738-139">Moreover, `System.Index` should have an implicit conversion from `System.Int32`, in order not to need to overload for mixing integers and indexes over multi-dimensional signatures.</span></span>

## <a name="adding-index-and-range-support-to-existing-library-types"></a><span data-ttu-id="4a738-140">Ajout de la prise en charge des index et des plages aux types de bibliothèques existants</span><span class="sxs-lookup"><span data-stu-id="4a738-140">Adding Index and Range support to existing library types</span></span>

### <a name="implicit-index-support"></a><span data-ttu-id="4a738-141">Prise en charge d’index implicite</span><span class="sxs-lookup"><span data-stu-id="4a738-141">Implicit Index support</span></span>

<span data-ttu-id="4a738-142">Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Index` pour les types qui répondent aux critères suivants :</span><span class="sxs-lookup"><span data-stu-id="4a738-142">The language will provide an instance indexer member with a single parameter of type `Index` for types which meet the following criteria:</span></span>

- <span data-ttu-id="4a738-143">Le type est NBVAL.</span><span class="sxs-lookup"><span data-stu-id="4a738-143">The type is Countable.</span></span>
- <span data-ttu-id="4a738-144">Le type a un indexeur d’instance accessible qui accepte un seul `int` comme argument.</span><span class="sxs-lookup"><span data-stu-id="4a738-144">The type has an accessible instance indexer which takes a single `int` as the argument.</span></span>
- <span data-ttu-id="4a738-145">Le type n’a pas d’indexeur d’instance accessible qui prend une `Index` comme premier paramètre.</span><span class="sxs-lookup"><span data-stu-id="4a738-145">The type does not have an accessible instance indexer which takes an `Index` as the first parameter.</span></span> <span data-ttu-id="4a738-146">Le `Index` doit être le seul paramètre ou les paramètres restants doivent être facultatifs.</span><span class="sxs-lookup"><span data-stu-id="4a738-146">The `Index` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="4a738-147">Un type est ***compté*** s’il a une propriété nommée `Length` ou `Count` avec un accesseur Get accessible et un type de retour `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-147">A type is ***Countable*** if it  has a property named `Length` or `Count` with an accessible getter and a return type of `int`.</span></span> <span data-ttu-id="4a738-148">Le langage peut utiliser cette propriété pour convertir une expression de type `Index` en `int` au point de l’expression, sans qu’il soit nécessaire d’utiliser le type `Index` du tout.</span><span class="sxs-lookup"><span data-stu-id="4a738-148">The language can make use of this property to convert an expression of type `Index` into an `int` at the point of the expression without the need to use the type `Index` at all.</span></span> <span data-ttu-id="4a738-149">Si `Length` et `Count` sont présents, `Length` est préférable.</span><span class="sxs-lookup"><span data-stu-id="4a738-149">In case both `Length` and `Count` are present, `Length` will be preferred.</span></span> <span data-ttu-id="4a738-150">Par souci de simplicité, la proposition utilise le nom `Length` pour représenter `Count` ou `Length`.</span><span class="sxs-lookup"><span data-stu-id="4a738-150">For simplicity going forward, the proposal will use the name `Length` to represent `Count` or `Length`.</span></span>

<span data-ttu-id="4a738-151">Pour ces types, le langage agit comme s’il s’agissait d’un membre d’index de la forme `T this[Index index]` où `T` est le type de retour de l’indexeur `int`, y compris toutes les annotations de style `ref`.</span><span class="sxs-lookup"><span data-stu-id="4a738-151">For such types, the language will act as if there is an index member of the form `T this[Index index]` where `T` is the return type of the `int` based indexer including any `ref` style annotations.</span></span> <span data-ttu-id="4a738-152">Le nouveau membre aura les mêmes membres `get` et `set` avec l’accessibilité correspondante que l’indexeur `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-152">The new member will have the same `get` and `set` members with matching accessibility as the `int` indexer.</span></span> 

<span data-ttu-id="4a738-153">Le nouvel indexeur sera implémenté en convertissant l’argument de type `Index` en `int` et en émettant un appel à l’indexeur `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-153">The new indexer will be implemented by converting the argument of type `Index` into an `int` and emitting a call to the `int` based indexer.</span></span> <span data-ttu-id="4a738-154">À des fins de discussion, permet d’utiliser l’exemple de `receiver[expr]`.</span><span class="sxs-lookup"><span data-stu-id="4a738-154">For discussion purposes, lets use the example of `receiver[expr]`.</span></span> <span data-ttu-id="4a738-155">La conversion de `expr` en `int` se produit comme suit :</span><span class="sxs-lookup"><span data-stu-id="4a738-155">The conversion of `expr` to `int` will occur as follows:</span></span>

- <span data-ttu-id="4a738-156">Lorsque l’argument se présente sous la forme `^expr2` et que le type de `expr2` est `int`, il sera converti en `receiver.Length - expr2`.</span><span class="sxs-lookup"><span data-stu-id="4a738-156">When the argument is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="4a738-157">Dans le cas contraire, il sera traduit comme `expr.GetOffset(receiver.Length)`.</span><span class="sxs-lookup"><span data-stu-id="4a738-157">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="4a738-158">Cela permet aux développeurs d’utiliser la fonctionnalité `Index` sur les types existants sans modification.</span><span class="sxs-lookup"><span data-stu-id="4a738-158">This allows for developers to use the `Index` feature on existing types without the need for modification.</span></span> <span data-ttu-id="4a738-159">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a738-159">For example:</span></span>

``` csharp
List<char> list = ...;
var value = list[^1]; 

// Gets translated to 
var value = list[list.Count - 1]; 
```

<span data-ttu-id="4a738-160">Les expressions `receiver` et `Length` seront débordées de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="4a738-160">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="4a738-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a738-161">For example:</span></span>

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

<span data-ttu-id="4a738-162">Ce code affiche « obtient la longueur 3 ».</span><span class="sxs-lookup"><span data-stu-id="4a738-162">This code will print "Get Length 3".</span></span>

### <a name="implicit-range-support"></a><span data-ttu-id="4a738-163">Prise en charge des plages implicites</span><span class="sxs-lookup"><span data-stu-id="4a738-163">Implicit Range support</span></span>

<span data-ttu-id="4a738-164">Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Range` pour les types qui répondent aux critères suivants :</span><span class="sxs-lookup"><span data-stu-id="4a738-164">The language will provide an instance indexer member with a single parameter of type `Range` for types which meet the following criteria:</span></span>

- <span data-ttu-id="4a738-165">Le type est NBVAL.</span><span class="sxs-lookup"><span data-stu-id="4a738-165">The type is Countable.</span></span>
- <span data-ttu-id="4a738-166">Le type a un membre accessible nommé `Slice` qui a deux paramètres de type `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-166">The type has an accessible member named `Slice` which has two parameters of type `int`.</span></span>
- <span data-ttu-id="4a738-167">Le type n’a pas d’indexeur d’instance qui accepte un seul `Range` comme premier paramètre.</span><span class="sxs-lookup"><span data-stu-id="4a738-167">The type does not have an instance indexer which takes a single `Range` as the first parameter.</span></span> <span data-ttu-id="4a738-168">Le `Range` doit être le seul paramètre ou les paramètres restants doivent être facultatifs.</span><span class="sxs-lookup"><span data-stu-id="4a738-168">The `Range` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="4a738-169">Pour ces types, le langage est lié comme s’il s’agissait d’un membre d’index de la forme `T this[Range range]` où `T` est le type de retour de la méthode `Slice`, y compris les annotations de style `ref`.</span><span class="sxs-lookup"><span data-stu-id="4a738-169">For such types, the language will bind as if there is an index member of the form `T this[Range range]` where `T` is the return type of the `Slice` method including any `ref` style annotations.</span></span> <span data-ttu-id="4a738-170">Le nouveau membre aura également une accessibilité correspondante avec `Slice`.</span><span class="sxs-lookup"><span data-stu-id="4a738-170">The new member will also have matching accessibility with `Slice`.</span></span> 

<span data-ttu-id="4a738-171">Lorsque l’indexeur de `Range` est lié à une expression nommée `receiver`, il est abaissé en convertissant l’expression `Range` en deux valeurs qui sont ensuite transmises à la méthode `Slice`.</span><span class="sxs-lookup"><span data-stu-id="4a738-171">When the `Range` based indexer is bound on an expression named `receiver`, it will be lowered by converting the `Range` expression into two values that are then passed to the `Slice` method.</span></span> <span data-ttu-id="4a738-172">À des fins de discussion, permet d’utiliser l’exemple de `receiver[expr]`.</span><span class="sxs-lookup"><span data-stu-id="4a738-172">For discussion purposes, lets use the example of `receiver[expr]`.</span></span>

<span data-ttu-id="4a738-173">Le premier argument de `Slice` est obtenu en convertissant l’expression typée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="4a738-173">The first argument of `Slice` will be obtained by converting the typed expression in the following way:</span></span>

- <span data-ttu-id="4a738-174">Lorsque `expr` se présente sous la forme `expr1..expr2` (où `expr2` peut être omis) et `expr1` a le type `int`, il est émis en tant que `expr1`.</span><span class="sxs-lookup"><span data-stu-id="4a738-174">When `expr` is of the form `expr1..expr2` (where `expr2` can be omitted) and `expr1` has type `int`, then it will be emitted as `expr1`.</span></span>
- <span data-ttu-id="4a738-175">Lorsque `expr` se présente sous la forme `^expr1..expr2` (où `expr2` peut être omis), il est émis en tant que `receiver.Length - expr1`.</span><span class="sxs-lookup"><span data-stu-id="4a738-175">When `expr` is of the form `^expr1..expr2` (where `expr2` can be omitted), then it will be emitted as `receiver.Length - expr1`.</span></span>
- <span data-ttu-id="4a738-176">Lorsque `expr` se présente sous la forme `..expr2` (où `expr2` peut être omis), il est émis en tant que `0`.</span><span class="sxs-lookup"><span data-stu-id="4a738-176">When `expr` is of the form `..expr2` (where `expr2` can be omitted), then it will be emitted as `0`.</span></span>
- <span data-ttu-id="4a738-177">Dans le cas contraire, il sera émis en tant que `expr.Start.GetOffset(receiver.Length)`.</span><span class="sxs-lookup"><span data-stu-id="4a738-177">Otherwise, it will be emitted as `expr.Start.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="4a738-178">Cette valeur sera réutilisée dans le calcul du deuxième argument `Slice`.</span><span class="sxs-lookup"><span data-stu-id="4a738-178">This value will be re-used in the calculation of the second `Slice` argument.</span></span> <span data-ttu-id="4a738-179">Dans ce cas, on parle de `start`.</span><span class="sxs-lookup"><span data-stu-id="4a738-179">When doing so it will be referred to as `start`.</span></span> <span data-ttu-id="4a738-180">Le deuxième argument de `Slice` est obtenu en convertissant l’expression de plage typée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="4a738-180">The second argument of `Slice` will be obtained by converting the range typed expression in the following way:</span></span>

- <span data-ttu-id="4a738-181">Lorsque `expr` se présente sous la forme `expr1..expr2` (où `expr1` peut être omis) et `expr2` a le type `int`, il est émis en tant que `expr2 - start`.</span><span class="sxs-lookup"><span data-stu-id="4a738-181">When `expr` is of the form `expr1..expr2` (where `expr1` can be omitted) and `expr2` has type `int`, then it will be emitted as `expr2 - start`.</span></span>
- <span data-ttu-id="4a738-182">Lorsque `expr` se présente sous la forme `expr1..^expr2` (où `expr1` peut être omis), il est émis en tant que `(receiver.Length - expr2) - start`.</span><span class="sxs-lookup"><span data-stu-id="4a738-182">When `expr` is of the form `expr1..^expr2` (where `expr1` can be omitted), then it will be emitted as `(receiver.Length - expr2) - start`.</span></span>
- <span data-ttu-id="4a738-183">Lorsque `expr` se présente sous la forme `expr1..` (où `expr1` peut être omis), il est émis en tant que `receiver.Length - start`.</span><span class="sxs-lookup"><span data-stu-id="4a738-183">When `expr` is of the form `expr1..` (where `expr1` can be omitted), then it will be emitted as `receiver.Length - start`.</span></span>
- <span data-ttu-id="4a738-184">Dans le cas contraire, il sera émis en tant que `expr.End.GetOffset(receiver.Length) - start`.</span><span class="sxs-lookup"><span data-stu-id="4a738-184">Otherwise, it will be emitted as `expr.End.GetOffset(receiver.Length) - start`.</span></span>

<span data-ttu-id="4a738-185">Les expressions `receiver`, `Length` et `expr` seront débordées de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="4a738-185">The `receiver`, `Length` and `expr` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="4a738-186">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a738-186">For example:</span></span>

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

<span data-ttu-id="4a738-187">Ce code affiche « obtient la longueur 2 ».</span><span class="sxs-lookup"><span data-stu-id="4a738-187">This code will print "Get Length 2".</span></span>

<span data-ttu-id="4a738-188">Le langage respecte le cas particulier des types connus suivants :</span><span class="sxs-lookup"><span data-stu-id="4a738-188">The language will special case the following known types:</span></span> 

- <span data-ttu-id="4a738-189">`string`: la méthode `Substring` sera utilisée à la place de `Slice`.</span><span class="sxs-lookup"><span data-stu-id="4a738-189">`string`: the method `Substring` will be used instead of `Slice`.</span></span>
- <span data-ttu-id="4a738-190">`array`: la méthode `System.Reflection.CompilerServices.GetSubArray` sera utilisée à la place de `Slice`.</span><span class="sxs-lookup"><span data-stu-id="4a738-190">`array`: the method `System.Reflection.CompilerServices.GetSubArray` will be used instead of `Slice`.</span></span>

## <a name="alternatives"></a><span data-ttu-id="4a738-191">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="4a738-191">Alternatives</span></span>

<span data-ttu-id="4a738-192">Les nouveaux opérateurs (`^` et `..`) sont des sucres syntaxiques.</span><span class="sxs-lookup"><span data-stu-id="4a738-192">The new operators (`^` and `..`) are syntactic sugar.</span></span> <span data-ttu-id="4a738-193">La fonctionnalité peut être implémentée par des appels explicites à `System.Index` et `System.Range` méthodes de fabrique, mais elle génère beaucoup plus de code réutilisable, et l’expérience n’est pas intuitive.</span><span class="sxs-lookup"><span data-stu-id="4a738-193">The functionality can be implemented by explicit calls to `System.Index` and `System.Range` factory methods, but it will result in a lot more boilerplate code, and the experience will be unintuitive.</span></span>

## <a name="il-representation"></a><span data-ttu-id="4a738-194">Représentation IL</span><span class="sxs-lookup"><span data-stu-id="4a738-194">IL Representation</span></span>

<span data-ttu-id="4a738-195">Ces deux opérateurs seront abaissés aux appels d’indexeur/de méthode normaux, sans modification des couches de compilateur suivantes.</span><span class="sxs-lookup"><span data-stu-id="4a738-195">These two operators will be lowered to regular indexer/method calls, with no change in subsequent compiler layers.</span></span>

## <a name="runtime-behavior"></a><span data-ttu-id="4a738-196">Comportement au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="4a738-196">Runtime behavior</span></span>

- <span data-ttu-id="4a738-197">Le compilateur peut optimiser des indexeurs pour des types intégrés tels que des tableaux et des chaînes, et réduire l’indexation aux méthodes existantes appropriées.</span><span class="sxs-lookup"><span data-stu-id="4a738-197">Compiler can optimize indexers for built-in types like arrays and strings, and lower the indexing to the appropriate existing methods.</span></span>
- <span data-ttu-id="4a738-198">`System.Index` lève une exception si elle est construite avec une valeur négative.</span><span class="sxs-lookup"><span data-stu-id="4a738-198">`System.Index` will throw if constructed with a negative value.</span></span>
- <span data-ttu-id="4a738-199">`^0` ne lève pas d’exception, mais il se traduit par la longueur de la collection/énumérable à laquelle il est fourni.</span><span class="sxs-lookup"><span data-stu-id="4a738-199">`^0` does not throw, but it translates to the length of the collection/enumerable it is supplied to.</span></span>
- <span data-ttu-id="4a738-200">`Range.All` est sémantiquement équivalente à `0..^0`et peut être déconstruite sur ces index.</span><span class="sxs-lookup"><span data-stu-id="4a738-200">`Range.All` is semantically equivalent to `0..^0`, and can be deconstructed to these indices.</span></span>

## <a name="considerations"></a><span data-ttu-id="4a738-201">Considérations</span><span class="sxs-lookup"><span data-stu-id="4a738-201">Considerations</span></span>

### <a name="detect-indexable-based-on-icollection"></a><span data-ttu-id="4a738-202">Détecter les indexables basés sur ICollection</span><span class="sxs-lookup"><span data-stu-id="4a738-202">Detect Indexable based on ICollection</span></span>

<span data-ttu-id="4a738-203">L’inspiration de ce comportement était des initialiseurs de collection.</span><span class="sxs-lookup"><span data-stu-id="4a738-203">The inspiration for this behavior was collection initializers.</span></span> <span data-ttu-id="4a738-204">Utilisation de la structure d’un type pour indiquer qu’il a opté pour une fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="4a738-204">Using the structure of a type to convey that it had opted into a feature.</span></span> <span data-ttu-id="4a738-205">Dans le cas des initialiseurs de collection, les types peuvent s’abonner à la fonctionnalité en implémentant l’interface `IEnumerable` (non générique).</span><span class="sxs-lookup"><span data-stu-id="4a738-205">In the case of collection initializers types can opt into the feature by implementing the interface `IEnumerable` (non generic).</span></span>

<span data-ttu-id="4a738-206">Au départ, cette proposition nécessitait que les types implémentent `ICollection` pour qualifier comme indexable.</span><span class="sxs-lookup"><span data-stu-id="4a738-206">This proposal initially required that types implement `ICollection` in order to qualify as Indexable.</span></span> <span data-ttu-id="4a738-207">Cela nécessitait un certain nombre de cas particuliers :</span><span class="sxs-lookup"><span data-stu-id="4a738-207">That required a number of special cases though:</span></span>

- <span data-ttu-id="4a738-208">`ref struct`: celles-ci ne peuvent pas implémenter des interfaces encore des types comme `Span<T>` sont idéaux pour la prise en charge des index/plages.</span><span class="sxs-lookup"><span data-stu-id="4a738-208">`ref struct`: these cannot implement interfaces yet types like `Span<T>` are ideal for index / range support.</span></span> 
- <span data-ttu-id="4a738-209">`string`: n’implémente pas `ICollection` et ajoute ce `interface` a un coût élevé.</span><span class="sxs-lookup"><span data-stu-id="4a738-209">`string`: does not implement `ICollection` and adding that `interface` has a large cost.</span></span>

<span data-ttu-id="4a738-210">Cela signifie que la prise en charge de la casse spéciale des types de clés est déjà nécessaire.</span><span class="sxs-lookup"><span data-stu-id="4a738-210">This means to support key types special casing is already needed.</span></span> <span data-ttu-id="4a738-211">La casse particulière des `string` est moins intéressante, car le langage le fait dans d’autres zones (`foreach` abaissement, constantes, etc.). La casse particulière de `ref struct` est plus spécifique, car il s’agit d’une classe entière de types.</span><span class="sxs-lookup"><span data-stu-id="4a738-211">The special casing of `string` is less interesting as the language does this in other areas (`foreach` lowering, constants, etc ...). The special casing of `ref struct` is more concerning as it's special casing an entire class of types.</span></span> <span data-ttu-id="4a738-212">Ils sont étiquetés comme indexables s’ils ont simplement une propriété nommée `Count` avec un type de retour `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-212">They get labeled as Indexable if they simply have a property named `Count` with a return type of `int`.</span></span> 

<span data-ttu-id="4a738-213">Après examen, la conception a été normalisée pour indiquer que tout type qui a une propriété `Count` / `Length` avec un type de retour `int` est indexable.</span><span class="sxs-lookup"><span data-stu-id="4a738-213">After consideration the design was normalized to say that any type which has a property `Count` / `Length` with a return type of `int` is Indexable.</span></span> <span data-ttu-id="4a738-214">Cela supprime toute la casse spéciale, même pour les `string` et les tableaux.</span><span class="sxs-lookup"><span data-stu-id="4a738-214">That removes all special casing, even for `string` and arrays.</span></span>

### <a name="detect-just-count"></a><span data-ttu-id="4a738-215">Détecter uniquement le nombre</span><span class="sxs-lookup"><span data-stu-id="4a738-215">Detect just Count</span></span>

<span data-ttu-id="4a738-216">La détection des noms de propriété `Count` ou `Length` complique légèrement la conception.</span><span class="sxs-lookup"><span data-stu-id="4a738-216">Detecting on the property names `Count` or `Length` does complicate the design a bit.</span></span> <span data-ttu-id="4a738-217">Le choix d’un seul pour normaliser n’est pas suffisant, car il finit par exclure un grand nombre de types :</span><span class="sxs-lookup"><span data-stu-id="4a738-217">Picking just one to standardize though is not sufficient as it ends up excluding a large number of types:</span></span>

- <span data-ttu-id="4a738-218">Utilisez `Length`: exclut à peu près chaque collection dans System. Collections et les sous-espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="4a738-218">Use `Length`: excludes pretty much every collection in System.Collections and sub-namespaces.</span></span> <span data-ttu-id="4a738-219">Celles-ci ont tendance à dériver de `ICollection` et donc préfèrent `Count` longueur.</span><span class="sxs-lookup"><span data-stu-id="4a738-219">Those tend to derive from `ICollection` and hence prefer `Count` over length.</span></span>
- <span data-ttu-id="4a738-220">Utilisez `Count`: exclut les `string`, les tableaux, les `Span<T>` et la plupart des types basés sur des `ref struct`</span><span class="sxs-lookup"><span data-stu-id="4a738-220">Use `Count`: excludes `string`, arrays, `Span<T>` and most `ref struct` based types</span></span>

<span data-ttu-id="4a738-221">La plus grande complication de la détection initiale de types indexables est dépensée par sa simplification dans d’autres aspects.</span><span class="sxs-lookup"><span data-stu-id="4a738-221">The extra complication on the initial detection of Indexable types is outweighed by its simplification in other aspects.</span></span>

### <a name="choice-of-slice-as-a-name"></a><span data-ttu-id="4a738-222">Choix d’un secteur en tant que nom</span><span class="sxs-lookup"><span data-stu-id="4a738-222">Choice of Slice as a name</span></span>

<span data-ttu-id="4a738-223">Le nom `Slice` a été choisi, car il s’agit du nom standard de facto pour les opérations de style de découpage dans .NET.</span><span class="sxs-lookup"><span data-stu-id="4a738-223">The name `Slice` was chosen as it's the de-facto standard name for slice style operations in .NET.</span></span> <span data-ttu-id="4a738-224">À compter de netcoreapp 2.1 tous les types de style span, utilisez le nom `Slice` pour les opérations de découpage.</span><span class="sxs-lookup"><span data-stu-id="4a738-224">Starting with netcoreapp2.1 all span style types use the name `Slice` for slicing operations.</span></span> <span data-ttu-id="4a738-225">Avant netcoreapp 2.1, il n’y a pas vraiment d’exemples de découpage à rechercher pour obtenir un exemple.</span><span class="sxs-lookup"><span data-stu-id="4a738-225">Prior to netcoreapp2.1 there really aren't any examples of slicing to look to for an example.</span></span> <span data-ttu-id="4a738-226">Les types tels que `List<T>`, `ArraySegment<T>``SortedList<T>` seraient idéaux pour le découpage, mais le concept n’existait pas lors de l’ajout de types.</span><span class="sxs-lookup"><span data-stu-id="4a738-226">Types like `List<T>`, `ArraySegment<T>`, `SortedList<T>` would've been ideal for slicing but the concept didn't exist when types were added.</span></span> 

<span data-ttu-id="4a738-227">Ainsi, `Slice` étant le seul exemple, il a été choisi comme nom.</span><span class="sxs-lookup"><span data-stu-id="4a738-227">Thus, `Slice` being the sole example, it was chosen as the name.</span></span>

### <a name="index-target-type-conversion"></a><span data-ttu-id="4a738-228">Conversion du type de cible d’index</span><span class="sxs-lookup"><span data-stu-id="4a738-228">Index target type conversion</span></span>

<span data-ttu-id="4a738-229">Une autre façon d’afficher la transformation de `Index` dans une expression d’indexeur est de convertir un type cible.</span><span class="sxs-lookup"><span data-stu-id="4a738-229">Another way to view the `Index` transformation in an indexer expression is as a target type conversion.</span></span> <span data-ttu-id="4a738-230">Au lieu de créer une liaison comme s’il s’agissait d’un membre de la forme `return_type this[Index]`, le langage assigne à la place une conversion cible typée en `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-230">Instead of binding as if there is a member of the form `return_type this[Index]`, the language instead assigns a target typed conversion to `int`.</span></span> 

<span data-ttu-id="4a738-231">Ce concept peut être généralisé à l’accès de tous les membres sur les types dénombrables.</span><span class="sxs-lookup"><span data-stu-id="4a738-231">This concept could be generalized to all member access on Countable types.</span></span> <span data-ttu-id="4a738-232">Chaque fois qu’une expression de type `Index` est utilisée en tant qu’argument pour un appel de membre d’instance et que le récepteur est compté, l’expression aura une conversion de type cible en `int`.</span><span class="sxs-lookup"><span data-stu-id="4a738-232">Whenever an expression with type `Index` is used as an argument to an instance member invocation and the receiver is Countable then the expression will have a target type conversion to `int`.</span></span> <span data-ttu-id="4a738-233">Les appels de membres applicables pour cette conversion incluent les méthodes, les indexeurs, les propriétés, les méthodes d’extension, etc. Seuls les constructeurs sont exclus, car ils n’ont pas de récepteur.</span><span class="sxs-lookup"><span data-stu-id="4a738-233">The member invocations applicable for this conversion include methods, indexers, properties, extension methods, etc ... Only constructors are excluded as they have no receiver.</span></span> 

<span data-ttu-id="4a738-234">La conversion de type cible sera implémentée comme suit pour toute expression qui a un type de `Index`.</span><span class="sxs-lookup"><span data-stu-id="4a738-234">The target type conversion will be implemented as follows for any expression which has a type of `Index`.</span></span> <span data-ttu-id="4a738-235">À des fins de discussion, vous pouvez utiliser l’exemple de `receiver[expr]`:</span><span class="sxs-lookup"><span data-stu-id="4a738-235">For discussion purposes lets use the example of `receiver[expr]`:</span></span>

- <span data-ttu-id="4a738-236">Lorsque `expr` se présente sous la forme `^expr2` et que le type de `expr2` est `int`, il est converti en `receiver.Length - expr2`.</span><span class="sxs-lookup"><span data-stu-id="4a738-236">When `expr` is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="4a738-237">Dans le cas contraire, il sera traduit comme `expr.GetOffset(receiver.Length)`.</span><span class="sxs-lookup"><span data-stu-id="4a738-237">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="4a738-238">Les expressions `receiver` et `Length` seront débordées de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="4a738-238">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="4a738-239">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a738-239">For example:</span></span>

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

<span data-ttu-id="4a738-240">Ce code affiche « obtient la longueur 3 ».</span><span class="sxs-lookup"><span data-stu-id="4a738-240">This code will print "Get Length 3".</span></span> 

<span data-ttu-id="4a738-241">Cette fonctionnalité est bénéfique pour tout membre qui avait un paramètre qui représentait un index.</span><span class="sxs-lookup"><span data-stu-id="4a738-241">This feature would be beneficial to any member which had a parameter that represented an index.</span></span> <span data-ttu-id="4a738-242">Par exemple, `List<T>.InsertAt`.</span><span class="sxs-lookup"><span data-stu-id="4a738-242">For example `List<T>.InsertAt`.</span></span> <span data-ttu-id="4a738-243">Cela présente également un risque de confusion, car le langage ne peut pas vous aider à déterminer si une expression est destinée à l’indexation.</span><span class="sxs-lookup"><span data-stu-id="4a738-243">This also has the potential for confusion as the language can't give any guidance as to whether or not an expression is meant for indexing.</span></span> <span data-ttu-id="4a738-244">Tout ce qu’il peut faire est de convertir n’importe quelle expression `Index` en `int` lors de l’appel d’un membre sur un type compté.</span><span class="sxs-lookup"><span data-stu-id="4a738-244">All it can do is convert any `Index` expression to `int` when invoking a member on a Countable type.</span></span> 

<span data-ttu-id="4a738-245">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="4a738-245">Restrictions:</span></span>

- <span data-ttu-id="4a738-246">Cette conversion s’applique uniquement lorsque l’expression de type `Index` est directement un argument du membre.</span><span class="sxs-lookup"><span data-stu-id="4a738-246">This conversion is only applicable when the expression with type `Index` is directly an argument to the member.</span></span> <span data-ttu-id="4a738-247">Elle ne s’applique pas aux expressions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="4a738-247">It would not apply to any nested expressions.</span></span>

## <a name="decisions-made-during-implementation"></a><span data-ttu-id="4a738-248">Décisions prises pendant l’implémentation</span><span class="sxs-lookup"><span data-stu-id="4a738-248">Decisions made during implementation</span></span>

- <span data-ttu-id="4a738-249">Tous les membres du modèle doivent être membres d’instance</span><span class="sxs-lookup"><span data-stu-id="4a738-249">All members in the pattern must be instance members</span></span>
- <span data-ttu-id="4a738-250">Si une méthode de longueur est trouvée mais que son type de retour est incorrect, continuez à rechercher le nombre</span><span class="sxs-lookup"><span data-stu-id="4a738-250">If a Length method is found but it has the wrong return type, continue looking for Count</span></span>
- <span data-ttu-id="4a738-251">L’indexeur utilisé pour le modèle d’index doit avoir exactement un paramètre int</span><span class="sxs-lookup"><span data-stu-id="4a738-251">The indexer used for the Index pattern must have exactly one int parameter</span></span>
- <span data-ttu-id="4a738-252">La méthode Slice utilisée pour le modèle Range doit avoir exactement deux paramètres int</span><span class="sxs-lookup"><span data-stu-id="4a738-252">The Slice method used for the Range pattern must have exactly two int parameters</span></span>
- <span data-ttu-id="4a738-253">Lorsque vous recherchez les membres du modèle, nous cherchons les définitions d’origine, et non les membres construits.</span><span class="sxs-lookup"><span data-stu-id="4a738-253">When looking for the pattern members, we look for original definitions, not constructed members</span></span>

## <a name="design-meetings"></a><span data-ttu-id="4a738-254">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="4a738-254">Design Meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md

## <a name="questions"></a><span data-ttu-id="4a738-255">Questions</span><span class="sxs-lookup"><span data-stu-id="4a738-255">Questions</span></span>
