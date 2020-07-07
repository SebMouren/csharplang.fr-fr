---
ms.openlocfilehash: bee562d64c402e645879b7811cc82c971d8b61cc
ms.sourcegitcommit: 9b736b5b73321f18a04e02d26596388f7ceb42fc
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86052205"
---
# <a name="ranges"></a>Plages

## <a name="summary"></a>Résumé

Cette fonctionnalité consiste à offrir deux nouveaux opérateurs qui permettent de construire `System.Index` des `System.Range` objets et, et à les utiliser pour indexer/découper des collections au moment de l’exécution.

## <a name="overview"></a>Vue d’ensemble

### <a name="well-known-types-and-members"></a>Types et membres connus

Pour utiliser les nouvelles formes syntaxiques pour `System.Index` et `System.Range` , de nouveaux types et membres connus peuvent être nécessaires, en fonction des formes syntaxiques utilisées.

Pour utiliser l’opérateur « Hat » ( `^` ), les conditions suivantes sont requises :

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

Pour utiliser le `System.Index` type en tant qu’argument dans un accès à un élément de tableau, le membre suivant est requis :

```csharp
int System.Index.GetOffset(int length);
```

La `..` syntaxe de `System.Range` requiert le `System.Range` type, ainsi qu’un ou plusieurs des membres suivants :

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

La `..` syntaxe permet d’absenter l’un ou l’autre, ou aucun de ses arguments. Quel que soit le nombre d’arguments, le `Range` constructeur est toujours suffisant pour utiliser la `Range` syntaxe. Toutefois, si l’un des autres membres est présent et qu’un ou plusieurs `..` arguments manquent, le membre approprié peut être substitué.

Enfin, pour une valeur de type `System.Range` à utiliser dans une expression d’accès à un élément de tableau, le membre suivant doit être présent :

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a>System. index

C# n’a aucun moyen d’indexer une collection à partir de la fin, mais la plupart des indexeurs utilisent la notion « from Start » ou une expression « length-i ». Nous introduisons une nouvelle expression d’index qui signifie « à partir de la fin ». La fonctionnalité introduira un nouvel opérateur « Hat » de préfixe unaire. Son opérande unique doit pouvoir être converti en `System.Int32` . Il sera abaissé dans l' `System.Index` appel de méthode de fabrique approprié.

Nous enrichissons la grammaire de *unary_expression* avec la forme de syntaxe supplémentaire suivante :

```antlr
unary_expression
    : '^' unary_expression
    ;
```

Nous appelons l' *index à partir de* l’opérateur end. L' *index prédéfini à partir des opérateurs end* est le suivant :

```csharp
System.Index operator ^(int fromEnd);
```

Le comportement de cet opérateur est défini uniquement pour les valeurs d’entrée supérieures ou égales à zéro.

Exemples :

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var thirdItem = array[2];    // array[2]
var lastItem = array[^1];    // array[new Index(1, fromEnd: true)]
```

#### <a name="systemrange"></a>System. Range

C# n’offre aucun moyen syntaxique d’accéder aux « plages » ou aux « tranches » de collections. Généralement, les utilisateurs sont obligés d’implémenter des structures complexes pour filtrer/faire fonctionner sur des secteurs de mémoire, ou recourir à des méthodes LINQ comme `list.Skip(5).Take(2)` . Avec l’ajout de `System.Span<T>` et d’autres types similaires, il est plus important d’avoir ce type d’opération pris en charge sur un niveau plus profond dans le langage/l’exécution et de faire en sorte que l’interface soit unifiée.

Le langage introduira un nouvel opérateur de plage `x..y` . Il s’agit d’un opérateur d’infixe binaire qui accepte deux expressions. L’un des opérandes peut être omis (exemples ci-dessous) et doit être convertible en `System.Index` . Il sera abaissé à l' `System.Range` appel de méthode de fabrique approprié.

Nous remplaçons les règles de grammaire C# pour *multiplicative_expression* par les éléments suivants (afin d’introduire un nouveau niveau de priorité) :

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

Toutes les formes de l' *opérateur de plage* ont la même priorité. Ce nouveau groupe de précédence est inférieur aux *opérateurs unaires* et supérieur aux *opérateurs arithmétiques de multiplication*.

Nous appelons l' `..` opérateur *Range*. L’opérateur de plage intégré peut être compris approximativement pour correspondre à l’appel d’un opérateur intégré de ce formulaire :

```csharp
System.Range operator ..(Index start = 0, Index end = ^0);
```

Exemples :

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

En outre, `System.Index` doit avoir une conversion implicite de `System.Int32` , afin d’éviter la nécessité de surcharger le mélange des entiers et des index sur des signatures multidimensionnelles.

## <a name="adding-index-and-range-support-to-existing-library-types"></a>Ajout de la prise en charge des index et des plages aux types de bibliothèques existants

### <a name="implicit-index-support"></a>Prise en charge d’index implicite

Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Index` pour les types qui répondent aux critères suivants :

- Le type est NBVAL.
- Le type a un indexeur d’instance accessible qui accepte un seul `int` comme argument.
- Le type n’a pas d’indexeur d’instance accessible qui prend `Index` comme premier paramètre. `Index`Doit être le seul paramètre ou les paramètres restants doivent être facultatifs.

Un type est ***compté*** s’il a une propriété nommée `Length` ou `Count` avec un accesseur Get accessible et un type de retour `int` . Le langage peut utiliser cette propriété pour convertir une expression de type `Index` en un `int` au point de l’expression sans qu’il soit nécessaire d’utiliser le type `Index` . Si `Length` et sont tous deux `Count` présents, `Length` sera préféré. Par souci de simplicité, la proposition utilise le nom `Length` pour représenter `Count` ou `Length` .

Pour ces types, le langage agit comme s’il s’agissait d’un membre d’indexeur du formulaire `T this[Index index]` où `T` est le type de retour de l' `int` indexeur de base, y compris les `ref` Annotations de style. Le nouveau membre aura les mêmes `get` membres et `set` avec l’accessibilité correspondante que l' `int` indexeur.

Le nouvel indexeur sera implémenté en convertissant l’argument de type `Index` en un `int` et en émettant un appel à l' `int` indexeur de base. À des fins de discussion, utilisons l’exemple de `receiver[expr]` . La conversion de `expr` en `int` se produit comme suit :

- Lorsque l’argument se présente sous la forme `^expr2` et que le type de `expr2` est `int` , il est converti en `receiver.Length - expr2` .
- Dans le cas contraire, elle sera traduite par `expr.GetOffset(receiver.Length)` .

Cela permet aux développeurs d’utiliser la `Index` fonctionnalité sur les types existants sans nécessiter de modification. Par exemple :

``` csharp
List<char> list = ...;
var value = list[^1];

// Gets translated to
var value = list[list.Count - 1];
```

Les expressions et sont débordées `receiver` `Length` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois. Par exemple :

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

Ce code affiche « obtient la longueur 3 ».

### <a name="implicit-range-support"></a>Prise en charge des plages implicites

Le langage fournira un membre d’indexeur d’instance avec un paramètre unique de type `Range` pour les types qui répondent aux critères suivants :

- Le type est NBVAL.
- Le type a un membre accessible nommé `Slice` qui a deux paramètres de type `int` .
- Le type n’a pas d’indexeur d’instance qui accepte un seul `Range` comme premier paramètre. `Range`Doit être le seul paramètre ou les paramètres restants doivent être facultatifs.

Pour ces types, le langage est lié comme s’il s’agissait d’un membre d’indexeur de la forme `T this[Range range]` où `T` est le type de retour de la `Slice` méthode, y compris les `ref` Annotations de style. Le nouveau membre aura également une accessibilité correspondante avec `Slice` .

Lorsque l' `Range` indexeur de base est lié à une expression nommée `receiver` , il est abaissé en convertissant l' `Range` expression en deux valeurs qui sont ensuite passées à la `Slice` méthode. À des fins de discussion, utilisons l’exemple de `receiver[expr]` .

Le premier argument de `Slice` sera obtenu en convertissant l’expression de plage typée de la manière suivante :

- Lorsque a `expr` la forme `expr1..expr2` (où `expr2` peut être omis) et a le `expr1` type `int` , il est émis en tant que `expr1` .
- Lorsque `expr` a la forme `^expr1..expr2` (où `expr2` peut être omis), il est émis en tant que `receiver.Length - expr1` .
- Lorsque `expr` a la forme `..expr2` (où `expr2` peut être omis), il est émis en tant que `0` .
- Dans le cas contraire, il sera émis sous la forme `expr.Start.GetOffset(receiver.Length)` .

Cette valeur sera réutilisée dans le calcul du deuxième `Slice` argument. Dans ce cas, il est appelé `start` . Le deuxième argument de `Slice` sera obtenu en convertissant l’expression de plage typée de la manière suivante :

- Lorsque a `expr` la forme `expr1..expr2` (où `expr1` peut être omis) et a le `expr2` type `int` , il est émis en tant que `expr2 - start` .
- Lorsque `expr` a la forme `expr1..^expr2` (où `expr1` peut être omis), il est émis en tant que `(receiver.Length - expr2) - start` .
- Lorsque `expr` a la forme `expr1..` (où `expr1` peut être omis), il est émis en tant que `receiver.Length - start` .
- Dans le cas contraire, il sera émis sous la forme `expr.End.GetOffset(receiver.Length) - start` .

Les `receiver` expressions, et sont débordées `Length` `expr` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois. Par exemple :

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

Ce code affiche « obtient la longueur 2 ».

Le langage respecte le cas particulier des types connus suivants :

- `string`: la méthode `Substring` sera utilisée à la place de `Slice` .
- `array`: la méthode `System.Reflection.CompilerServices.GetSubArray` sera utilisée à la place de `Slice` .

## <a name="alternatives"></a>Autres solutions

Les nouveaux opérateurs ( `^` et `..` ) sont des sucres syntaxiques. La fonctionnalité peut être implémentée par des appels explicites à `System.Index` et des `System.Range` méthodes de fabrique, mais elle génère beaucoup plus de code réutilisable, et l’expérience n’est pas intuitive.

## <a name="il-representation"></a>Représentation IL

Ces deux opérateurs seront abaissés aux appels d’indexeur/de méthode normaux, sans modification des couches de compilateur suivantes.

## <a name="runtime-behavior"></a>Comportement au moment de l’exécution

- Le compilateur peut optimiser des indexeurs pour des types intégrés tels que des tableaux et des chaînes, et réduire l’indexation aux méthodes existantes appropriées.
- `System.Index`lève une exception si elle est construite avec une valeur négative.
- `^0`ne lève pas, mais convertit la longueur de la collection/énumérable à laquelle il est fourni.
- `Range.All`est sémantiquement équivalent à `0..^0` , et peut être déconstruite sur ces index.

## <a name="considerations"></a>Considérations

### <a name="detect-indexable-based-on-icollection"></a>Détecter les indexables basés sur ICollection

L’inspiration de ce comportement était des initialiseurs de collection. Utilisation de la structure d’un type pour indiquer qu’il a opté pour une fonctionnalité. Dans le cas des initialiseurs de collection, les types peuvent accepter la fonctionnalité en implémentant l’interface `IEnumerable` (non générique).

Cette proposition nécessitait initialement que les types implémentent `ICollection` pour qualifier comme indexable. Cela nécessitait un certain nombre de cas particuliers :

- `ref struct`: celles-ci ne peuvent pas implémenter `Span<T>` d’interfaces, mais sont idéales pour la prise en charge des index/plages.
- `string`: n’implémente pas `ICollection` et n’ajoute pas qui `interface` a un coût élevé.

Cela signifie que la prise en charge de la casse spéciale des types de clés est déjà nécessaire. La casse spéciale de `string` est moins intéressante, car le langage le fait dans d’autres zones ( `foreach` abaissement, constantes, etc.). La casse spéciale de `ref struct` est davantage en ce qui concerne la casse particulière d’une classe entière de types. Ils sont étiquetés comme indexables s’ils ont simplement une propriété nommée `Count` avec un type de retour `int` .

Après considération, la conception a été normalisée pour indiquer que tout type qui a une propriété `Count`  /  `Length` avec un type de retour `int` est indexable. Qui supprime toute la casse spéciale, même pour les `string` tableaux et.

### <a name="detect-just-count"></a>Détecter uniquement le nombre

La détection des noms de propriété `Count` ou `Length` complique la conception. Le choix d’un seul pour normaliser n’est pas suffisant, car il finit par exclure un grand nombre de types :

- Utilisation `Length` : exclut à peu près chaque collection dans System. Collections et les sous-espaces de noms. Celles-ci ont tendance à dériver de `ICollection` et, par conséquent, préfèrent dépasser la `Count` longueur.
- Utilisation `Count` : exclut `string` , `Span<T>` les tableaux et les types les plus `ref struct` basés

La plus grande complication de la détection initiale de types indexables est dépensée par sa simplification dans d’autres aspects.

### <a name="choice-of-slice-as-a-name"></a>Choix d’un secteur en tant que nom

Le nom `Slice` a été choisi, car il s’agit du nom standard de facto pour les opérations de style de découpage dans .net. À compter de netcoreapp 2.1, tous les types de style d’étendue utilisent le nom `Slice` pour les opérations de découpage. Avant netcoreapp 2.1, il n’y a pas vraiment d’exemples de découpage à rechercher pour obtenir un exemple. Les types tels que `List<T>` , `ArraySegment<T>` , `SortedList<T>` seraient idéaux pour le découpage, mais le concept n’existait pas lors de l’ajout de types.

Par conséquent, `Slice` en tant que seul exemple, il a été choisi comme nom.

### <a name="index-target-type-conversion"></a>Conversion du type de cible d’index

Une autre façon d’afficher la `Index` transformation dans une expression d’indexeur est de convertir un type cible. Au lieu de créer une liaison comme s’il s’agissait d’un membre du formulaire `return_type this[Index]` , le langage assigne à la place une conversion de cible typée en `int` .

Ce concept peut être généralisé à l’accès de tous les membres sur les types dénombrables. Chaque fois qu’une expression de type `Index` est utilisée en tant qu’argument pour un appel de membre d’instance et que le récepteur est compté, l’expression aura une conversion de type cible en `int` . Les appels de membres applicables pour cette conversion incluent les méthodes, les indexeurs, les propriétés, les méthodes d’extension, etc. Seuls les constructeurs sont exclus, car ils n’ont pas de récepteur.

La conversion de type cible sera implémentée comme suit pour toute expression qui a un type `Index` . À des fins de discussion, vous pouvez utiliser l’exemple de `receiver[expr]` :

- Quand `expr` a la forme `^expr2` et que le type de `expr2` est `int` , il est converti en `receiver.Length - expr2` .
- Dans le cas contraire, elle sera traduite par `expr.GetOffset(receiver.Length)` .

Les expressions et sont débordées `receiver` `Length` de manière appropriée pour garantir que tous les effets secondaires ne sont exécutés qu’une seule fois. Par exemple :

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

Ce code affiche « obtient la longueur 3 ».

Cette fonctionnalité est bénéfique pour tout membre qui avait un paramètre qui représentait un index. Par exemple, `List<T>.InsertAt`. Cela présente également un risque de confusion, car le langage ne peut pas vous aider à déterminer si une expression est destinée à l’indexation. Tout ce qu’il peut faire est de convertir n’importe quelle `Index` expression en lors de l' `int` appel d’un membre sur un type compté.

Restrictions :

- Cette conversion s’applique uniquement lorsque l’expression avec le type `Index` est directement un argument du membre. Elle ne s’applique pas aux expressions imbriquées.

## <a name="decisions-made-during-implementation"></a>Décisions prises pendant l’implémentation

- Tous les membres du modèle doivent être membres d’instance
- Si une méthode de longueur est trouvée mais que son type de retour est incorrect, continuez à rechercher le nombre
- L’indexeur utilisé pour le modèle d’index doit avoir exactement un paramètre int
- La méthode Slice utilisée pour le modèle Range doit avoir exactement deux paramètres int
- Lorsque vous recherchez les membres du modèle, nous cherchons les définitions d’origine, et non les membres construits.

## <a name="design-meetings"></a>Réunions de conception

- [10 janvier 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-10.md)
- [18 janvier 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-18.md)
- [22 janvier, 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-22.md)
- [3 décembre 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-12-03.md)
- [25 mars, 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-03-25.md#pattern-based-indexing-with-index-and-range)
- [1er avril, 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md)
- [15 avril 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-15.md#follow-up-decisions-for-pattern-based-indexrange)
