---
ms.openlocfilehash: 3b142d7dbda8a94a4cf2c973a2e380065dcbf5ee
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703963"
---
# <a name="enums"></a>Énumérations

Un ***type enum*** est un type valeur distinct ([types valeur](types.md#value-types)) qui déclare un ensemble de constantes nommées.

L’exemple

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

déclare un type Enum nommé `Color` avec les membres `Red`, `Green`et `Blue`.

## <a name="enum-declarations"></a>Déclarations d’énumération

Une déclaration enum déclare un nouveau type enum. Une déclaration enum commence par le mot clé `enum`et définit le nom, l’accessibilité, le type sous-jacent et les membres de l’enum.

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

Chaque type enum a un type intégral correspondant appelé le ***type sous-jacent*** du type enum. Ce type sous-jacent doit être en mesure de représenter toutes les valeurs d’énumérateur définies dans l’énumération. Une déclaration enum peut déclarer explicitement un type sous-jacent de `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`. Notez que `char` ne peut pas être utilisé en tant que type sous-jacent. Une déclaration enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`.

L’exemple

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

déclare une énumération avec un type sous-jacent de `long`. Un développeur peut choisir d’utiliser un type sous-jacent de `long`, comme dans l’exemple, pour permettre l’utilisation de valeurs qui se trouvent dans la plage de `long` mais pas dans la plage de `int`, ou pour conserver cette option à l’avenir.

## <a name="enum-modifiers"></a>Modificateurs enum

Une *enum_declaration* peut éventuellement inclure une séquence de modificateurs enum :

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

Il s’agit d’une erreur au moment de la compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration Enum.

Les modificateurs d’une déclaration enum ont la même signification que ceux d’une déclaration de classe ([modificateurs de classe](classes.md#class-modifiers)). Notez, toutefois, que les modificateurs `abstract` et `sealed` ne sont pas autorisés dans une déclaration Enum. Les enums ne peuvent pas être abstraits et n’autorisent pas la dérivation.

## <a name="enum-members"></a>Énumérer les membres

Le corps d’une déclaration de type enum définit zéro, un ou plusieurs membres enum, qui sont les constantes nommées du type enum. Deux membres enum ne peuvent pas avoir le même nom.

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

Chaque membre enum a une valeur de constante associée. Le type de cette valeur est le type sous-jacent pour l’enum conteneur. La valeur de constante pour chaque membre enum doit être comprise dans la plage du type sous-jacent pour l’enum. L’exemple

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

génère une erreur de compilation, car les valeurs de constante `-1`, `-2`et `-3` ne sont pas comprises dans la plage du type intégral sous-jacent `uint`.

Plusieurs membres enum peuvent partager la même valeur associée. L’exemple

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

affiche une énumération dans laquelle deux membres enum, `Blue` et `Max`, ont la même valeur associée.

La valeur associée d’un membre enum est assignée implicitement ou explicitement. Si la déclaration du membre enum a un initialiseur *constant_expression* , la valeur de cette expression constante, implicitement convertie dans le type sous-jacent de l’enum, est la valeur associée du membre Enum. Si la déclaration du membre enum n’a pas d’initialiseur, sa valeur associée est définie implicitement, comme suit :

*  Si le membre enum est le premier membre enum déclaré dans le type enum, sa valeur associée est zéro.
*  Dans le cas contraire, la valeur associée du membre enum est obtenue en accroissant la valeur associée du membre enum qui précède textuellement. Cette valeur augmentée doit être comprise dans la plage de valeurs qui peuvent être représentées par le type sous-jacent ; sinon, une erreur de compilation se produit.

L’exemple

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

imprime les noms des membres enum et leurs valeurs associées. La sortie est la suivante :

```console
Red = 0
Green = 10
Blue = 11
```

pour les raisons suivantes :

*  la valeur zéro est assignée automatiquement au membre enum `Red` (car il n’a pas d’initialiseur et est le premier membre enum);
*  le membre enum `Green` reçoit explicitement la valeur `10`;
*  et le membre enum `Blue` reçoit automatiquement la valeur une valeur supérieure à celle du membre qui le précède textuellement.

La valeur associée d’un membre enum ne peut pas, directement ou indirectement, utiliser la valeur de son propre membre enum associé. Outre cette restriction de circularité, les initialiseurs de membres enum peuvent librement faire référence à d’autres initialiseurs de membres enum, indépendamment de leur position textuelle. Dans un initialiseur de membre enum, les valeurs d’autres membres enum sont toujours traitées comme ayant le type de leur type sous-jacent, de sorte que les casts ne sont pas nécessaires pour faire référence à d’autres membres enum.

L’exemple

```csharp
enum Circular
{
    A = B,
    B
}
```

génère une erreur au moment de la compilation, car les déclarations de `A` et `B` sont circulaires. `A` dépend `B` de manière explicite, et `B` dépend `A` implicitement.

Les membres enum sont nommés et étendus d’une manière exactement analogue aux champs dans les classes. La portée d’un membre enum est le corps de son type enum contenant. Au sein de cette étendue, les membres enum peuvent être référencés par leur nom simple. À partir de tout autre code, le nom d’un membre enum doit être qualifié avec le nom de son type enum. Les membres enum n’ont pas d’accessibilité déclarée : un membre enum est accessible si son type enum conteneur est accessible.

## <a name="the-systemenum-type"></a>Type System. Enum

Le `System.Enum` de type est la classe de base abstraite de tous les types ENUM (il est distinct et différent du type sous-jacent du type enum), et les membres hérités de `System.Enum` sont disponibles dans tout type enum. Une conversion boxing ([conversions boxing](types.md#boxing-conversions)) existe à partir de n’importe quel type enum en `System.Enum`, et une conversion unboxing ([conversions unboxing](types.md#unboxing-conversions)) existe de `System.Enum` à n’importe quel type enum.

Notez que `System.Enum` n’est pas lui-même un *enum_type*. Au lieu de cela, il s’agit d’un *class_type* duquel tous les *enum_type*sont dérivés. Le type `System.Enum` hérite du type `System.ValueType` ([type System. ValueType](types.md#the-systemvaluetype-type)) qui, à son tour, hérite du type `object`. Au moment de l’exécution, une valeur de type `System.Enum` peut être `null` ou une référence à une valeur boxed de n’importe quel type enum.

## <a name="enum-values-and-operations"></a>Valeurs et opérations enum

Chaque type enum définit un type distinct ; une conversion d’énumération explicite ([conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)) est requise pour la conversion entre un type enum et un type intégral, ou entre deux types ENUM. L’ensemble de valeurs qu’un type enum peut utiliser n’est pas limité par ses membres enum. En particulier, toute valeur du type sous-jacent d’une énumération peut être convertie en type enum, et est une valeur valide distincte de ce type enum.

Les membres enum ont le type de leur type enum conteneur (sauf dans d’autres initialiseurs de membres enum : consultez [enum members](enums.md#enum-members)). La valeur d’un membre enum déclaré dans le type enum `E` avec la valeur associée `v` est `(E)v`.

Les opérateurs suivants peuvent être utilisés sur des valeurs de types enum : `==`, `!=`, `<`, `>`, `<=`, `>=` ([opérateurs de comparaison d’énumération](expressions.md#enumeration-comparison-operators)), binaire `+` ([opérateur d’addition](expressions.md#addition-operator)), binaire `-` (opérateur de[soustraction](expressions.md#subtraction-operator)), `^`, `&`, `|` ([opérateurs logiques d’énumération](expressions.md#enumeration-logical-operators)), `~` ([opérateur de complément au niveau du bit](expressions.md#bitwise-complement-operator)) [Opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).`++``--` 

Chaque type enum dérive automatiquement de la classe `System.Enum` (qui dérive à son tour de `System.ValueType` et `object`). Ainsi, les méthodes et les propriétés héritées de cette classe peuvent être utilisées sur les valeurs d’un type enum.
