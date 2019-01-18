---
ms.openlocfilehash: 155c1beecddfdfcce2e7948bcb8d6b80428fbd7a
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229646"
---
# <a name="arrays"></a>Tableaux

Un tableau est une structure de données qui contient un nombre de variables qui sont accessibles par le biais des indices calculés. Les variables contenues dans un tableau, également appelé éléments du tableau, sont tous du même type, et ce type est appelé le type d’élément du tableau.

Un tableau a un rang qui détermine le nombre d’indices associés à chaque élément du tableau. Le rang d’un tableau est également appelé les dimensions du tableau. Un tableau avec un classement d’un est appelé un ***tableau unidimensionnel***. Un tableau de rang supérieur à un est appelé un ***tableau multidimensionnel***. Tableaux multidimensionnels de taille spécifiques sont souvent appelées tableaux à deux dimensions, tableaux à trois dimensions et ainsi de suite.

Chaque dimension d’un tableau a une longueur, qui est un nombre entier supérieur ou égal à zéro. Les longueurs de dimensions ne font pas partie du type du tableau, mais elles sont établies lorsqu’une instance du type tableau est créée au moment de l’exécution. La longueur d’une dimension détermine la plage valide d’indices de cette dimension : Pour une dimension de longueur `N`, index peuvent être comprise entre `0` à `N - 1` inclus. Le nombre total d’éléments dans un tableau est le produit des longueurs de chaque dimension du tableau. Si un ou plusieurs des dimensions du tableau ont une longueur de zéro, le tableau est dit être vide.

Le type d’élément d’un tableau peut être de n’importe quel type, y compris un type tableau.

## <a name="array-types"></a>Types de tableaux

Un type tableau est écrit comme un *non_array_type* suivie d’un ou plusieurs *rank_specifier*%s :

```antlr
array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;
```

Un *non_array_type* est any *type* qui est pas elle-même une *array_type*.

Le rang d’un type tableau est fourni par le plus à gauche *rank_specifier* dans le *array_type*: Un *rank_specifier* indique que le tableau est un tableau de rang un plus le nombre de «`,`» des jetons dans le *rank_specifier*.

Le type d’élément d’un type tableau est le type qui résulte de la suppression le plus à gauche *rank_specifier*:

*  Un type de tableau sous la forme `T[R]` est un tableau avec le rang `R` et un type d’élément de tableau non `T`.
*  Un type de tableau sous la forme `T[R][R1]...[Rn]` est un tableau avec le rang `R` et un type d’élément `T[R1]...[Rn]`.

En effet, le *rank_specifier*s sont lues de gauche à droite avant le type d’élément non-tableau final. Le type `int[][,,][,]` est un tableau unidimensionnel de tableaux à trois dimensions de tableaux à deux dimensions de `int`.

Au moment de l’exécution, une valeur d’un type tableau peut être `null` ou une référence à une instance de ce type de tableau.

### <a name="the-systemarray-type"></a>Le type System.Array

Le type `System.Array` est le type de base abstrait de tous les types tableau. Une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) existe à partir de n’importe quel type de tableau à `System.Array`et une conversion de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir de `System.Array` à n’importe quel type de tableau. Notez que `System.Array` n’est pas lui-même un *array_type*. Au lieu de cela, il est un *class_type* à partir de laquelle toutes les *array_type*s sont dérivés.

Au moment de l’exécution, une valeur de type `System.Array` peut être `null` ou une référence à une instance de n’importe quel type de tableau.

### <a name="arrays-and-the-generic-ilist-interface"></a>Tableaux et l’interface IList générique

Un tableau unidimensionnel `T[]` implémente l’interface `System.Collections.Generic.IList<T>` (`IList<T>` en abrégé) et ses interfaces de base. En conséquence, il existe une conversion implicite de `T[]` à `IList<T>` et ses interfaces de base. En outre, s’il existe une conversion de référence implicite à partir de `S` à `T` puis `S[]` implémente `IList<T>` et il existe une conversion de référence implicite à partir de `S[]` à `IList<T>` et sa base d’interfaces () [Conversions de référence implicite](conversions.md#implicit-reference-conversions)). S’il existe une conversion de référence explicite à partir de `S` à `T` , il y a une conversion de référence explicite à partir de `S[]` à `IList<T>` et ses interfaces de base ([conversions de référence explicite](conversions.md#explicit-reference-conversions)). Exemple :
```csharp
using System.Collections.Generic;

class Test
{
    static void Main() {
        string[] sa = new string[5];
        object[] oa1 = new object[5];
        object[] oa2 = sa;

        IList<string> lst1 = sa;                    // Ok
        IList<string> lst2 = oa1;                   // Error, cast needed
        IList<object> lst3 = sa;                    // Ok
        IList<object> lst4 = oa1;                   // Ok

        IList<string> lst5 = (IList<string>)oa1;    // Exception
        IList<string> lst6 = (IList<string>)oa2;    // Ok
    }
}
```

L’attribution de `lst2 = oa1` génère une erreur de compilation depuis la conversion de `object[]` à `IList<string>` est une conversion explicite, implicite pas. Le cast `(IList<string>)oa1` provoquera une exception levée au moment de l’exécution depuis `oa1` références un `object[]` et non un `string[]`. Toutefois le cast `(IList<string>)oa2` n’entraîne pas une exception levée depuis `oa2` références un `string[]`.

Chaque fois qu’il existe une conversion de référence implicite ou explicite de `S[]` à `IList<T>`, il existe également une conversion de référence explicite à partir de `IList<T>` et les interfaces à sa base `S[]` ([référence explicite conversions](conversions.md#explicit-reference-conversions)).

Lorsqu’un type tableau `S[]` implémente `IList<T>`, certains membres de l’interface implémentée peuvent lever des exceptions. Le comportement précis de l’implémentation de l’interface n’entre pas dans le cadre de cette spécification.

## <a name="array-creation"></a>Création d’un tableau

Les instances de tableau sont créées par *array_creation_expression*s ([expressions de création de tableau](expressions.md#array-creation-expressions)) ou par champ ou des déclarations de variables locales qui incluent un *array_initializer*([Les initialiseurs de tableaux](arrays.md#array-initializers)).

Lorsqu’une instance de tableau est créée, le rang et la longueur de chaque dimension sont établis, puis il restera constante pour toute la durée de vie de l’instance. En d’autres termes, il n’est pas possible de modifier le rang d’une instance de tableau existante, ni est-il possible de redimensionner ses dimensions.

Une instance de tableau est toujours d’un type tableau. Le `System.Array` type est un type abstrait qui ne peut pas être instancié.

Éléments des tableaux créés par *array_creation_expression*s sont toujours initialisés à leur valeur par défaut ([valeurs par défaut](variables.md#default-values)).

## <a name="array-element-access"></a>Accès aux éléments de tableau

Éléments de tableau sont accessibles à l’aide de *element_access* expressions ([tableau accès](expressions.md#array-access)) sous la forme `A[I1, I2, ..., In]`, où `A` est une expression de type tableau et chaque `Ix` est un expression de type `int`, `uint`, `long`, `ulong`, ou peuvent être converties implicitement à un ou plusieurs de ces types. Le résultat d’un accès aux éléments de tableau est une variable, à savoir l’élément de tableau sélectionné par l’index.

Les éléments d’un tableau peuvent être énumérés à l’aide un `foreach` instruction ([l’instruction foreach](statements.md#the-foreach-statement)).

## <a name="array-members"></a>Membres de tableau

Le type de chaque tableau hérite des membres déclarés par le `System.Array` type.

## <a name="array-covariance"></a>Covariance de tableau

Pour les deux *reference_type*s `A` et `B`, si une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) ou conversion de référence explicite ([ Conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir de `A` à `B`, puis la même conversion de référence existe également du type de tableau `A[R]` pour le type de tableau `B[R]`, où `R` est any donné *rank_specifier* (mais le même pour les deux types de tableaux). Cette relation est appelée ***covariance de tableau***. Covariance de tableau signifie en particulier qu’une valeur d’un type tableau `A[R]` peut être une référence à une instance d’un type tableau `B[R]`, à condition qu’existe une conversion de référence implicite de `B` à `A`.

En raison de la covariance de tableau, les assignations aux éléments de tableaux de type référence incluent une vérification de l’exécution qui permet de s’assurer que la valeur assignée à l’élément de tableau est réellement de type autorisé ([assignation Simple](expressions.md#simple-assignment)). Exemple :
```csharp
class Test
{
    static void Fill(object[] array, int index, int count, object value) {
        for (int i = index; i < index + count; i++) array[i] = value;
    }

    static void Main() {
        string[] strings = new string[100];
        Fill(strings, 0, 100, "Undefined");
        Fill(strings, 0, 10, null);
        Fill(strings, 90, 10, 0);
    }
}
```

L’assignation à `array[i]` dans le `Fill` méthode inclut implicitement une vérification de l’exécution qui garantit que l’objet référencé par `value` est soit `null` ou une instance qui est compatible avec le type d’élément réel de `array`. Dans `Main`, les deux premiers appels de `Fill` réussissent, mais les causes d’appel troisième un `System.ArrayTypeMismatchException` levée lors de l’exécution de la première assignation à `array[i]`. L’exception se produit, car un boxed `int` ne peut pas être stocké dans un `string` tableau.

Covariance de tableau en particulier ne s’étend pas aux tableaux de *value_type*s. Par exemple, aucune conversion n’existe qui permet à un `int[]` doit être traité comme un `object[]`.

## <a name="array-initializers"></a>Initialiseurs de tableau

Initialiseurs de tableau ne peuvent être spécifiées dans les déclarations de champ ([champs](classes.md#fields)), déclarations de variables locales ([déclarations de variables locales](statements.md#local-variable-declarations)) et les expressions de création de tableau ([création de tableau expressions](expressions.md#array-creation-expressions)) :

```antlr
array_initializer
    : '{' variable_initializer_list? '}'
    | '{' variable_initializer_list ',' '}'
    ;

variable_initializer_list
    : variable_initializer (',' variable_initializer)*
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

Un initialiseur de tableau se compose d’une séquence d’initialiseurs de variable, délimitée par «`{`« et »`}`« jetons et séparés par »`,`« jetons. Chaque initialiseur de variable est une expression ou, dans le cas d’un tableau multidimensionnel, un initialiseur de tableau imbriqué.

Le contexte dans lequel un initialiseur de tableau est utilisé détermine le type du tableau en cours d’initialisation. Dans une expression de création de tableau, le type de tableau immédiatement précède l’initialiseur ou est déduit à partir d’expressions dans l’initialiseur de tableau. Dans un champ ou une déclaration de variable, le type de tableau est le type du champ ou de variable déclarée. Quand un initialiseur de tableau est utilisé dans un champ ou une déclaration de variable, tel que :
```csharp
int[] a = {0, 2, 4, 6, 8};
```
Il est simplement d’un raccourci pour une expression de création de tableau équivalente :
```csharp
int[] a = new int[] {0, 2, 4, 6, 8};
```

Pour un tableau unidimensionnel, l’initialiseur de tableau doit se composer d’une séquence d’expressions qui sont compatibles avec le type d’élément du tableau de l’assignation. Les expressions initialisent les éléments du tableau dans l’ordre croissant, en commençant par l’élément à l’index zéro. Le nombre d’expressions dans l’initialiseur de tableau détermine la longueur de l’instance de tableau en cours de création. Par exemple, l’initialiseur de tableau ci-dessus crée un `int[]` instance de longueur 5, puis initialise l’instance avec les valeurs suivantes :
```csharp
a[0] = 0; a[1] = 2; a[2] = 4; a[3] = 6; a[4] = 8;
```

Pour un tableau multidimensionnel, l’initialiseur de tableau doit avoir autant de niveaux d’imbrication qu’il existe des dimensions dans le tableau. Le niveau d’imbrication le plus externe correspond à la dimension la plus à gauche et le niveau d’imbrication plus profond correspond à la dimension la plus à droite. La longueur de chaque dimension du tableau est déterminée par le nombre d’éléments au niveau d’imbrication correspondant dans l’initialiseur de tableau. Pour chaque initialiseur de tableau imbriqué, le nombre d’éléments doit être le même que les initialiseurs de tableau au même niveau. L’exemple :
```csharp
int[,] b = {{0, 1}, {2, 3}, {4, 5}, {6, 7}, {8, 9}};
```
Crée un tableau à deux dimensions avec une longueur de cinq pour la dimension la plus à gauche et une longueur de deux pour la dimension la plus à droite :
```csharp
int[,] b = new int[5, 2];
```
et puis initialise l’instance de tableau avec les valeurs suivantes :
```csharp
b[0, 0] = 0; b[0, 1] = 1;
b[1, 0] = 2; b[1, 1] = 3;
b[2, 0] = 4; b[2, 1] = 5;
b[3, 0] = 6; b[3, 1] = 7;
b[4, 0] = 8; b[4, 1] = 9;
```

Si une dimension autre que le plus à droite est fournie avec une longueur égale à zéro, les dimensions suivantes sont supposées pour être également de longueur zéro. L’exemple :
```csharp
int[,] c = {};
```
Crée un tableau à deux dimensions avec une longueur de zéro pour le plus à gauche et la dimension la plus à droite :
```csharp
int[,] c = new int[0, 0];
```

Lorsqu’une expression de création de tableau comprend des longueurs de dimensions explicites et un initialiseur de tableau, les longueurs doivent être des expressions constantes et le nombre d’éléments à chaque niveau d’imbrication doit correspondre à la longueur de la dimension correspondante. Voici quelques exemples :
```csharp
int i = 3;
int[] x = new int[3] {0, 1, 2};        // OK
int[] y = new int[i] {0, 1, 2};        // Error, i not a constant
int[] z = new int[3] {0, 1, 2, 3};     // Error, length/initializer mismatch
```

Ici, l’initialiseur pour `y` entraîne une erreur de compilation, car l’expression de longueur de dimension n’est pas une constante et l’initialiseur pour `z` génère une erreur de compilation, car la longueur et le nombre d’éléments dans le initialiseur ne correspondent pas.
