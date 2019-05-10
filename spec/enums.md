---
ms.openlocfilehash: c3b716e6eb331be2ee33fffeb42c1e2406cd3a5c
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488759"
---
# <a name="enums"></a>Enums

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

déclare un type enum nommé `Color` avec les membres `Red`, `Green`, et `Blue`.

## <a name="enum-declarations"></a>Déclarations d’enum

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

Chaque type énumération a un type intégral appelé le ***type sous-jacent*** du type enum. Ce type sous-jacent doit être en mesure de représenter toutes les valeurs de l’énumérateur définies dans l’énumération. Une déclaration enum peut déclarer explicitement un type sous-jacent de `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`. Notez que `char` ne peut pas être utilisé comme un type sous-jacent. Une déclaration enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`.

L’exemple

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

déclare un enum avec un type sous-jacent de `long`. Un développeur peut choisir d’utiliser un type sous-jacent de `long`, comme dans l’exemple, pour activer l’utilisation de valeurs qui se trouvent dans la plage de `long` , mais pas dans la plage de `int`, ou pour conserver cette option pour l’avenir.

## <a name="enum-modifiers"></a>Modificateurs d’énumération

Un *enum_declaration* peut éventuellement inclure une séquence de modificateurs d’énumération :

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration enum.

Les modificateurs d’une déclaration enum ont la même signification que ceux d’une déclaration de classe ([modificateurs de classe](classes.md#class-modifiers)). Notez, cependant, que le `abstract` et `sealed` modificateurs ne sont pas autorisées dans une déclaration enum. Les enums ne peut pas être abstraits et ne permettent pas de dérivation.

## <a name="enum-members"></a>Membres d’enum

Le corps d’une déclaration de type enum définit zéro ou plusieurs membres de l’enum, qui sont des constantes nommées du type enum. Aucun membre enum deux ne peut avoir le même nom.

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

Chaque membre enum a une valeur de constante associée. Le type de cette valeur est le type sous-jacent de l’enum conteneur. La valeur de constante pour chaque membre enum doit être dans la plage du type sous-jacent de l’énumération. L’exemple

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

génère une erreur de compilation, car les valeurs de constante `-1`, `-2`, et `-3` ne sont pas dans la plage du type intégral sous-jacent `uint`.

Les membres enum multiples peuvent partager la même valeur associée. L’exemple

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

montre un enum dans les deux membres enum-- `Blue` et `Max` --ont la même valeur associée.

La valeur associée à un membre enum reçoit implicitement ou explicitement. Si la déclaration du membre enum a un *constant_expression* initialiseur, la valeur de cette expression constante, convertie implicitement en type sous-jacent de l’enum, est la valeur associée du membre enum. Si la déclaration du membre enum n’a aucun initialiseur, sa valeur associée est définie implicitement, comme suit :

*  Si le membre enum est le premier membre enum déclaré dans le type enum, sa valeur associée est zéro.
*  Sinon, la valeur associée du membre enum est obtenue en augmentant la valeur associée au membre enum qui précède par un. Cette valeur doit être dans la plage de valeurs qui peuvent être représentés par le type sous-jacent, sinon une erreur de compilation se produit.

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

Imprime les noms de membre enum et leurs valeurs associées. La sortie est la suivante :

```
Red = 0
Green = 10
Blue = 11
```

pour les raisons suivantes :

*  le membre enum `Red` est automatiquement attribué la valeur zéro (dans la mesure où il n’a aucun initialiseur et est le premier membre enum) ;
*  le membre enum `Green` soit donnée explicitement la valeur `10`;
*  et le membre enum `Blue` est automatiquement attribué la valeur égale à celle du membre qui le précède.

La valeur associée à un membre enum ne peut-être pas, directement ou indirectement, utilisez la valeur de son propre membre enum associé. Hormis cette restriction, les initialiseurs de membres enum librement parlent d’autres initialiseurs de membres enum, quelle que soit leur position du texte. Dans un initialiseur de membre enum, les valeurs d’autres membres enum sont toujours traitées comme ayant le type de leur type sous-jacent, afin que les conversions ne sont pas nécessaires lorsque vous faites référence à d’autres membres de l’enum.

L’exemple

```csharp
enum Circular
{
    A = B,
    B
}
```

génère une erreur de compilation, car les déclarations de `A` et `B` sont circulaires. `A` dépend de `B` explicitement, et `B` dépend `A` implicitement.

Membres enum sont nommés et limités à la manière exactement aux champs dans les classes. L’étendue d’un membre enum est le corps de son type enum conteneur. Dans cette portée, enum membres peuvent être désignés par leur nom simple. Tous les autres codes, le nom d’un membre enum doit être qualifié avec le nom de son type enum. Membres d’enum n’ont pas d’accessibilité déclarée, un membre enum est accessible que si son type enum conteneur est accessible.

## <a name="the-systemenum-type"></a>Le type System.Enum

Le type `System.Enum` est la classe de base abstraite de tous les types enum (c’est distinct et différent du type sous-jacent du type enum) et les membres hérités de `System.Enum` sont disponibles dans n’importe quel type enum. Une conversion boxing ([conversions Boxing](types.md#boxing-conversions)) existe à partir de n’importe quel type enum à `System.Enum`et une conversion unboxing ([les conversions Unboxing](types.md#unboxing-conversions)) existe à partir de `System.Enum` à n’importe quel type enum.

Notez que `System.Enum` n’est pas lui-même un *type_de_liste*. Au lieu de cela, il est un *class_type* à partir de laquelle toutes les *type_de_liste*s sont dérivés. Le type `System.Enum` hérite du type `System.ValueType` ([System.ValueType le type](types.md#the-systemvaluetype-type)), qui, à son tour, hérite du type `object`. Au moment de l’exécution, une valeur de type `System.Enum` peut être `null` ou une référence à une valeur boxed de n’importe quel type enum.

## <a name="enum-values-and-operations"></a>Opérations et les valeurs Enum

Chaque type enum définit un type distinct ; une conversion explicite d’énumération ([conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)) est nécessaire pour convertir entre un type enum et un type intégral, ou entre deux types enum. L’ensemble de valeurs qui peut prendre un type enum n’est pas limité par ses membres enum. En particulier, n’importe quelle valeur du type sous-jacent d’une énumération peut être casté en type enum et est une valeur valide distincte de ce type enum.

Les membres enum ont le type de leur type enum conteneur (sauf dans d’autres initialiseurs de membres enum : consultez [membres Enum](enums.md#enum-members)). La valeur d’un membre enum déclaré dans le type enum `E` avec la valeur associée `v` est `(E)v`.

Les opérateurs suivants peuvent être utilisés sur les valeurs de types enum : `==`, `!=`, `<`, `>`, `<=`, `>=`  ([opérateurs de comparaison d’énumération](expressions.md#enumeration-comparison-operators)) , binaire `+`  ([opérateur d’Addition](expressions.md#addition-operator)) binaire `-`  ([opérateur de soustraction](expressions.md#subtraction-operator)), `^`, `&` , `|`  ([Opérateurs logiques énumération](expressions.md#enumeration-logical-operators)), `~`  ([opérateur de complément de bits](expressions.md#bitwise-complement-operator)), `++` et `--`  ([D’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators) et [Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).

Chaque type enum dérive automatiquement la classe `System.Enum` (qui, à son tour, dérive de `System.ValueType` et `object`). Par conséquent, les méthodes héritées et des propriétés de cette classe peuvent servir sur les valeurs d’un type enum.
