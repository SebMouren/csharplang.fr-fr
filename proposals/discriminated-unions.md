---
ms.openlocfilehash: 2e4a3a32def6900797c151264c984378b09b4988
ms.sourcegitcommit: 5983461e05be62f39c77383cb7857539518cb04f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "79485125"
---

# <a name="discriminated-unions--enum-class"></a>Unions/`enum class` discriminées

`enum class`es sont un nouveau genre de déclaration de type, parfois appelé unions discriminées, où chaque instance possible du type est répertoriée, et chaque instance ne se chevauche pas.

Un `enum class` est défini à l’aide de la syntaxe suivante :

```antlr
enum_class
    : 'partial'? 'enum class' identifier type_parameter_list? type_parameter_constraints_clause* 
      '{' enum_class_body '}'
    ;

enum_class_body
    : enum_class_cases?
    | enum_class_cases ','
    ;

enum_class_cases
    : enum_class_case
    | enum_class_case ',' enum_class_cases
    ;

enum_class_case
    : enum_class
    | class_declaration
    | identifier type_parameter_list? '(' formal_parameter_list? ')'
    | identifier
    ;

```

Exemple de syntaxe :

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a>Sémantique

Une définition de `enum class` définit un type racine, qui est une classe abstraite du même nom que la déclaration `enum class` et un ensemble de membres, chacun d’entre eux ayant un type qui est un sous-type du type racine. S’il existe plusieurs définitions de `partial enum class`, tous les membres sont considérés comme membres de la définition de la classe Enum. Contrairement à une définition de classe abstraite définie par l’utilisateur, le type racine `enum class` est partiel par défaut et défini pour avoir un constructeur sans paramètre *privé* par défaut.

Notez que, étant donné que le type racine est défini comme une classe abstraite partielle, les définitions partielles du *type racine* peuvent également être ajoutées, où les formes de syntaxe standard pour un corps de classe sont autorisées.
Toutefois, aucun type ne peut hériter directement du type racine dans une déclaration, à l’exception de ceux spécifiés en tant que membres de `enum class`. En outre, aucun constructeur défini par l’utilisateur n’est autorisé pour le type racine.

Il existe quatre types de déclarations de membres de `enum class` :

* Membres de classe simples

* membres de classe complexe

* membres `enum class`

* membres de valeur.

### <a name="simple-class-members"></a>Membres de classe simples

Une déclaration de membre de classe simple définit une nouvelle classe « record » imbriquée (intentionnellement laissée non définie dans ce document) portant le même nom. La classe imbriquée hérite du type racine.

Compte tenu de l’exemple de code ci-dessus,

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

la déclaration de `enum class` a une sémantique équivalente à la déclaration suivante

```C#
partial abstract class Shape
{
    public data class Rectangle(float Width, float Length) : Shape,
    public data class Circle(float Radius) : Shape
}
```

### <a name="complex-class-members"></a>Membres de classe complexe

Vous pouvez également imbriquer une déclaration de classe entière sous une déclaration de `enum class`. Il sera traité comme une classe imbriquée du type racine. La syntaxe autorise toute déclaration de classe, mais elle est requise pour que le membre de classe complexe hérite de la déclaration de `enum class` contenant directement. 

### <a name="enum-class-members"></a>membres `enum class`

les `enum classes` peuvent être imbriqués les uns sous les autres, par exemple

```C#
enum class Expr
{
    enum class Binary
    {
        Addition(Expr left, Expr right),
        Multiplication(Expr left, Expr right)
    }
}
```

Cela est presque identique à la sémantique d’un `enum class`de niveau supérieur, sauf que la classe enum imbriquée définit un type racine imbriqué, et que tout ce qui se trouve sous la classe enum imbriquée est un sous-type du type racine imbriqué, au lieu du type racine de niveau supérieur.

```C#
partial abstract class Expr
{
    partial abstract class Binary : Expr
    {
        public data class Addition(Expr left, Expr right) : Binary,
        public data class Multiplication(Expr left, Expr right) : Binary
    }
}
```

### <a name="value-members"></a>Membres de valeur

`enum classes` peut également contenir des membres de valeur. Les membres de valeur définissent des propriétés statiques de type public en réception seule sur le type racine qui retournent également le type racine, par exemple

```C#
enum class Color
{
    Red,
    Green
}
```

a des propriétés équivalentes à

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

La sémantique complète est considérée comme un détail d’implémentation, mais il est garanti qu’une instance unique sera retournée pour chaque propriété, et la même instance sera retournée sur les appels répétés.


### <a name="switch-expression-and-patterns"></a>Expressions de commutateur et modèles

Il existe des ajustements à apporter aux critères spéciaux et l’expression de commutateur pour gérer `enum classes`. Les expressions de commutateur peuvent déjà correspondre aux types via le modèle de variable, mais pour l’instant pour les types de référence, aucun ensemble d’accoudoirs de commutateur dans l’expression de commutateur n’est considéré comme complet, sauf pour une correspondance avec le type statique de l’argument ou un sous-type.

Les expressions de commutateur sont modifiées de telle sorte que, si le type racine d’un `enum class` est le type statique de l’argument de l’expression de commutateur, et qu’il existe un ensemble de modèles correspondant à tous les membres de l’énumération, le commutateur est considéré comme exhaustif.

Étant donné que les membres de valeur ne sont pas des constantes et ne définissent pas de nouveaux types statiques, ils ne peuvent actuellement pas être mis en correspondance par le modèle. Pour ce faire, un nouveau modèle à l’aide de la syntaxe de modèle de constante sera ajouté pour permettre la correspondance avec les membres de la valeur `enum class`. La correspondance est définie pour être réussie si et seulement si l’argument du modèle correspond à et que la valeur retournée par le membre de la valeur `enum class` serait égale, même si l’implémentation n’est pas requise pour effectuer cette vérification.


## <a name="open-questions"></a>Questions ouvertes

- [] Que disent l’algorithme de type commun sur `enum class` membres ? Ce code est-il valide ?
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- [] L’ajout d’un nouveau modèle uniquement pour les membres de valeur semble lourd. Y a-t-il une construction de version plus générale qui est logique ?
    - [] Les membres de valeur ne correspondent pas non plus bien à une construction de classe imbriquée parallèle

- [] Bascule sur un argument avec un `enum class` type statique garanti comme étant à temps constant ?

- [] Est-il possible de faire en sorte que les `enum class`ne soient pas considérés comme étant terminées dans l’expression de switch ? Préfixe `virtual`?

- [] Quels modificateurs doivent être autorisés sur `enum class`?