---
ms.openlocfilehash: 6dd1dde67597b2125de9a1aa2fab9144128d533f
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704012"
---
# <a name="structs"></a>Structs

Les structs sont similaires aux classes en ce qu’ils représentent des structures de données qui peuvent contenir des membres de données et des fonctions membres. Toutefois, contrairement aux classes, les structs sont des types valeur et ne nécessitent pas d’allocation de tas. Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière appelée objet.

Les structures sont particulièrement utiles pour les petites structures de données qui ont une sémantique par rapport à leurs valeurs. Les nombres complexes, les points dans un système de coordonnées ou les paires clé-valeur dans un dictionnaire sont de bons exemples de structures. La clé de ces structures de données est qu’elles ont peu de membres de données, qu’elles ne nécessitent pas l’utilisation de l’héritage ou de l’identité référentielle, et qu’elles peuvent être implémentées facilement à l’aide de la sémantique de valeur où l’assignation copie la valeur au lieu de la référence.

Comme décrit dans [types simples](types.md#simple-types), les types simples fournis par C#, tels que `int`, `double` et `bool`, sont en fait tous des types struct. Tout comme ces types prédéfinis sont des structs, il est également possible d’utiliser des structs et la surcharge d’opérateur pour implémenter de nouveaux types « C# primitifs » dans le langage. Deux exemples de ces types sont fournis à la fin de ce chapitre ([exemples de struct](structs.md#struct-examples)).

## <a name="struct-declarations"></a>Déclarations de struct

Un *struct_declaration* est un *type_declaration* ([déclarations de type](namespaces.md#type-declarations)) qui déclare un nouveau struct :

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

Un *struct_declaration* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *struct_modifier*s ([modificateurs de struct](structs.md#struct-modifiers)), suivi d’un modificateur facultatif `partial`, suivi de l’option mot clé `struct` et *identificateur* qui nomme le struct, suivi d’une spécification *type_parameter_list* facultative ([paramètres de type](classes.md#type-parameters)), suivie d’une spécification *struct_interfaces* facultative ([partielle ),](structs.md#partial-modifier)suivis d’une spécification *type_parameter_constraints_clause*s facultative (contraintes de[paramètre de type](classes.md#type-parameter-constraints)), suivie d’un *struct_body* ([corps de struct](structs.md#struct-body)), éventuellement suivi d’un point-virgule.

### <a name="struct-modifiers"></a>Modificateurs de struct

Un *struct_declaration* peut éventuellement inclure une séquence de modificateurs de struct :

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

Il s’agit d’une erreur au moment de la compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration de struct.

Les modificateurs d’une déclaration de struct ont la même signification que ceux d’une déclaration de classe ([déclarations de classe](classes.md#class-declarations)).

### <a name="partial-modifier"></a>Modificateur partiel

Le modificateur `partial` indique que ce *struct_declaration* est une déclaration de type partielle. Plusieurs déclarations de struct partielles portant le même nom dans un espace de noms englobant ou une déclaration de type sont combinées pour former une déclaration de struct, suivant les règles spécifiées dans les [types partiels](classes.md#partial-types).

### <a name="struct-interfaces"></a>Interfaces de struct

Une déclaration de struct peut inclure une spécification *struct_interfaces* , auquel cas le struct est dit d’implémenter directement les types d’interfaces donnés.

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

Les implémentations d’interface sont abordées plus en détail dans [implémentations d’interface](interfaces.md#interface-implementations).

### <a name="struct-body"></a>Corps du struct

Le *struct_body* d’un struct définit les membres du struct.

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a>Membres de struct

Les membres d’un struct se composent des membres introduits par ses *struct_member_declaration*et des membres hérités du type `System.ValueType`.

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

À l’exception des différences constatées dans les [différences entre les classes et les structs](structs.md#class-and-struct-differences), les descriptions des membres de classe fournis dans [les membres de classe](classes.md#class-members) par le biais d' [itérateurs](classes.md#iterators) s’appliquent également aux membres de la structure.

## <a name="class-and-struct-differences"></a>Différences entre les classes et les structs

Les structs diffèrent des classes de plusieurs manières importantes :

*  Les structs sont des types valeur ([sémantique de valeur](structs.md#value-semantics)).
*  Tous les types struct héritent implicitement de la classe `System.ValueType` ([héritage](structs.md#inheritance)).
*  L’assignation à une variable d’un type struct crée une copie de la valeur assignée ([affectation](structs.md#assignment)).
*  La valeur par défaut d’un struct correspond à la valeur produite en affectant à tous les champs de type valeur leur valeur par défaut et à tous les champs de type référence la valeur `null` ([valeurs par défaut](structs.md#default-values)).
*  Les opérations de boxing et d’unboxing sont utilisées pour effectuer une conversion entre un type struct et `object` ([boxing et unboxing](structs.md#boxing-and-unboxing)).
*  La signification de `this` est différente pour les structs ([cet accès](expressions.md#this-access)).
*  Les déclarations de champ d’instance pour un struct ne sont pas autorisées à inclure des initialiseurs de variable ([initialiseurs de champ](structs.md#field-initializers)).
*  Un struct n’est pas autorisé à déclarer un constructeur d’instance sans paramètre ([constructeurs](structs.md#constructors)).
*  Un struct n’est pas autorisé à déclarer un destructeur ([destructeurs](structs.md#destructors)).

### <a name="value-semantics"></a>Sémantique de valeur

Les structs sont des types valeur ([types valeur](types.md#value-types)) et sont dits sémantiques de valeur. En revanche, les classes sont des types référence ([types référence](types.md#reference-types)) et sont dites avoir une sémantique de référence.

Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière appelée objet. Lorsqu’un struct `B` contient un champ d’instance de type `A` et `A` est un type struct, il s’agit d’une erreur de compilation pour `A` qui dépend de `B` ou d’un type construit à partir de `B`. Un struct `X` ***dépend directement d'*** un struct `Y` si `X` contient un champ d’instance de type `Y`. Compte tenu de cette définition, l’ensemble complet des structs dont dépend un struct est la fermeture transitive de l' ***directement dépend*** de la relation.  Exemple :
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
est une erreur, car `Node` contient un champ d’instance de son propre type.  Autre exemple
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
est une erreur, car chacun des types `A`, `B` et `C` dépend l’un de l’autre.

Avec les classes, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter l’objet référencé par l’autre variable. Avec les structs, les variables ont chacune leur propre copie des données (sauf dans le cas des variables de paramètre `ref` et `out`), et il n’est pas possible pour les opérations sur l’une d’affecter l’autre. En outre, étant donné que les structs ne sont pas des types référence, il n’est pas possible que les valeurs d’un type struct soient `null`.

Compte tenu de la déclaration
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
fragment de code
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
génère la valeur `10`. L’assignation de `a` à `b` crée une copie de la valeur et `b` n’est donc pas affectée par l’assignation à `a.x`. Si `Point` a été déclarée en tant que classe, la sortie est `100`, car `a` et `b` référencent le même objet.

### <a name="inheritance"></a>Héritage

Tous les types struct héritent implicitement de la classe `System.ValueType`, qui hérite à son tour de la classe `object`. Une déclaration de struct peut spécifier une liste d’interfaces implémentées, mais il n’est pas possible pour une déclaration de struct de spécifier une classe de base.

Les types struct ne sont jamais abstraits et sont toujours implicitement sealed. Les modificateurs `abstract` et `sealed` ne sont donc pas autorisés dans une déclaration de struct.

Étant donné que l’héritage n’est pas pris en charge pour les structs, l’accessibilité déclarée d’un membre de struct ne peut pas être `protected` ou `protected internal`.

Les fonctions membres d’un struct ne peuvent pas être `abstract` ou `virtual`, et le modificateur `override` est autorisé uniquement à substituer des méthodes héritées de `System.ValueType`.

### <a name="assignment"></a>Attribution

L’assignation à une variable d’un type struct crée une copie de la valeur assignée. Cela diffère d’une assignation à une variable d’un type de classe, qui copie la référence, mais pas l’objet identifié par la référence.

Semblable à une assignation, lorsqu’un struct est passé en tant que paramètre de valeur ou retourné comme résultat d’un membre de fonction, une copie de la structure est créée. Un struct peut être passé par référence à un membre de fonction à l’aide d’un paramètre `ref` ou `out`.

Quand une propriété ou un indexeur d’un struct est la cible d’une assignation, l’expression d’instance associée à l’accès à la propriété ou à l’indexeur doit être classée en tant que variable. Si l’expression d’instance est classée en tant que valeur, une erreur de compilation se produit. Cela est décrit plus en détail dans la section [affectation simple](expressions.md#simple-assignment).

### <a name="default-values"></a>Valeurs par défaut

Comme décrit dans [valeurs par défaut](variables.md#default-values), plusieurs types de variables sont automatiquement initialisés à leur valeur par défaut lors de leur création. Pour les variables de types classe et autres types référence, cette valeur par défaut est `null`. Toutefois, étant donné que les structs sont des types valeur qui ne peuvent pas être `null`, la valeur par défaut d’un struct est la valeur produite en affectant à tous les champs de type valeur leur valeur par défaut et tous les champs de type référence à `null`.

En faisant référence à la structure `Point` déclarée ci-dessus, l’exemple
```csharp
Point[] a = new Point[100];
```
Initialise chaque `Point` dans le tableau à la valeur produite en affectant la valeur zéro aux champs `x` et `y`.

La valeur par défaut d’un struct correspond à la valeur retournée par le constructeur par défaut du struct ([constructeurs par défaut](types.md#default-constructors)). Contrairement à une classe, un struct n’est pas autorisé à déclarer un constructeur d’instance sans paramètre. Au lieu de cela, chaque struct a implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de l’affectation de la valeur par défaut à tous les champs de type de valeur et de l' `null` à tous les champs de type référence.

Les structs doivent être conçus pour considérer l’état d’initialisation par défaut comme un état valide. Dans l’exemple
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
le constructeur d’instance défini par l’utilisateur est protégé contre les valeurs NULL uniquement lorsqu’il est appelé explicitement. Dans les cas où une variable `KeyValuePair` est sujette à l’initialisation de la valeur par défaut, les champs `key` et `value` ont la valeur null, et la structure doit être préparée pour gérer cet État.

### <a name="boxing-and-unboxing"></a>Boxing et unboxing

Une valeur d’un type de classe peut être convertie en type `object` ou en un type interface implémenté par la classe en traitant simplement la référence comme un autre type au moment de la compilation. De même, une valeur de type `object` ou une valeur d’un type d’interface peut être reconvertie en un type de classe sans modifier la référence (mais bien évidemment, une vérification de type au moment de l’exécution est requise dans ce cas).

Étant donné que les structs ne sont pas des types référence, ces opérations sont implémentées différemment pour les types struct. Quand une valeur d’un type struct est convertie en type `object` ou en un type interface implémenté par le struct, une opération boxing est effectuée. De même, lorsqu’une valeur de type `object` ou une valeur d’un type d’interface est convertie en type struct, une opération de conversion unboxing a lieu. Une différence essentielle par rapport aux mêmes opérations sur les types de classe est que le boxing et l’unboxing copient la valeur de struct à l’intérieur ou à l’extérieur de l’instance boxed. Ainsi, à la suite d’une opération boxing ou unboxing, les modifications apportées au struct unboxed ne sont pas reflétées dans le struct boxed.

Lorsqu’un type struct substitue une méthode virtuelle héritée de `System.Object` (comme `Equals`, `GetHashCode` ou `ToString`), l’appel de la méthode virtuelle via une instance du type struct ne provoque pas de conversion boxing. Cela est vrai même lorsque le struct est utilisé comme paramètre de type et que l’appel se produit via une instance du type de paramètre de type. Exemple :
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

La sortie du programme est la suivante :
```console
1
2
3
```

Bien qu’il s’agisse d’un mauvais style pour `ToString` pour avoir des effets secondaires, l’exemple montre qu’aucune conversion boxing ne s’est produite pour les trois appels de `x.ToString()`.

De même, le boxing ne se produit jamais implicitement lors de l’accès à un membre sur un paramètre de type restreint. Par exemple, supposons qu’une interface `ICounter` contient une méthode `Increment` qui peut être utilisée pour modifier une valeur. Si `ICounter` est utilisé en tant que contrainte, l’implémentation de la méthode `Increment` est appelée avec une référence à la variable sur laquelle `Increment` a été appelé, jamais une copie boxed.

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

Le premier appel à `Increment` modifie la valeur dans la variable `x`. Cela n’est pas équivalent au deuxième appel à `Increment`, qui modifie la valeur dans une copie boxed de `x`. Ainsi, la sortie du programme est la suivante :
```console
0
1
1
```

Pour plus d’informations sur le boxing et l’unboxing, consultez [conversion boxing et unboxing](types.md#boxing-and-unboxing).

### <a name="meaning-of-this"></a>Signification de ce

Dans un constructeur d’instance ou un membre de fonction d’instance d’une classe, `this` est classée en tant que valeur. Ainsi, bien que `this` puisse être utilisé pour faire référence à l’instance pour laquelle la fonction membre a été appelée, il n’est pas possible d’assigner à `this` dans une fonction membre d’une classe.

Dans un constructeur d’instance d’un struct, `this` correspond à un paramètre `out` du type struct, et dans une fonction membre d’instance d’un struct, `this` correspond à un paramètre `ref` du type struct. Dans les deux cas, `this` est classée en tant que variable et il est possible de modifier l’ensemble du struct pour lequel la fonction membre a été appelée en affectant à `this` ou en passant ce en tant que paramètre `ref` ou `out`.

### <a name="field-initializers"></a>Initialiseurs de champ

Comme décrit dans [valeurs par défaut](structs.md#default-values), la valeur par défaut d’un struct se compose de la valeur qui résulte de l’affectation de la valeur par défaut à tous les champs de type de valeur et de l' `null` à tous les champs de type référence. Pour cette raison, un struct n’autorise pas les déclarations de champ d’instance à inclure des initialiseurs de variable. Cette restriction s’applique uniquement aux champs d’instance. Les champs statiques d’un struct sont autorisés à inclure des initialiseurs de variable.

L’exemple
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
est erroné, car les déclarations de champ d’instance incluent des initialiseurs de variable.

### <a name="constructors"></a>Constructeurs

Contrairement à une classe, un struct n’est pas autorisé à déclarer un constructeur d’instance sans paramètre. Au lieu de cela, chaque struct a implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de l’affectation de la valeur par défaut à tous les champs de type de valeur et de tous les champs de type référence à la valeur null ([constructeurs par défaut](types.md#default-constructors)). Un struct peut déclarer des constructeurs d’instance ayant des paramètres. Exemple :
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

Compte tenu de la déclaration ci-dessus, les instructions
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
les deux créent un `Point` avec `x` et `y` initialisé à zéro.

Un constructeur d’instance de struct n’est pas autorisé à inclure un initialiseur de constructeur de la forme `base(...)`.

Si le constructeur d’instance de struct ne spécifie pas d’initialiseur de constructeur, la variable `this` correspond à un paramètre `out` du type struct et est semblable à un paramètre `out`, `this` doit être assignée définitivement ([assignation définie ](variables.md#definite-assignment)) à chaque emplacement où le constructeur retourne. Si le constructeur d’instance de struct spécifie un initialiseur de constructeur, la variable `this` correspond à un paramètre `ref` du type struct et est semblable à un paramètre `ref`, `this` est considéré comme définitivement assigné à l’entrée dans le corps du constructeur . Examinez l’implémentation du constructeur d’instance ci-dessous :
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

Aucune fonction membre d’instance (y compris les accesseurs set des propriétés `X` et `Y`) ne peut être appelée tant que tous les champs du struct en cours de construction n’ont pas été définitivement assignés. La seule exception concerne les propriétés implémentées automatiquement ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)). Les règles d’assignation définie ([expressions d’assignation simples](variables.md#simple-assignment-expressions)) exemptent spécifiquement l’assignation à une propriété automatique d’un type struct dans un constructeur d’instance de ce type struct : une telle assignation est considérée comme une assignation définie du masqué champ de stockage de la propriété automatique. Ainsi, les éléments suivants sont autorisés :

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a>Destructeurs

Un struct n’est pas autorisé à déclarer un destructeur.

### <a name="static-constructors"></a>Constructeurs statiques

Les constructeurs statiques pour les structs suivent la plupart des règles de la même façon que pour les classes. L’exécution d’un constructeur statique pour un type struct est déclenchée par le premier des événements suivants pour se produire au sein d’un domaine d’application :

*  Un membre statique du type struct est référencé.
*  Un constructeur déclaré explicitement du type struct est appelé.

La création de valeurs par défaut ([valeurs par défaut](structs.md#default-values)) de types struct ne déclenche pas le constructeur statique. (Par exemple, il s’agit de la valeur initiale des éléments d’un tableau).

## <a name="struct-examples"></a>Exemples de struct

L’exemple suivant montre deux exemples significatifs de l’utilisation de types `struct` pour créer des types qui peuvent être utilisés de la même manière que les types prédéfinis du langage, mais avec la sémantique modifiée.

### <a name="database-integer-type"></a>Type d’entier de base de données

Le struct `DBInt` ci-dessous implémente un type entier qui peut représenter l’ensemble complet des valeurs du type `int`, ainsi qu’un État supplémentaire qui indique une valeur inconnue. Un type avec ces caractéristiques est couramment utilisé dans les bases de données.

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a>Type booléen de base de données

Le struct `DBBool` ci-dessous implémente un type logique à trois valeurs. Les valeurs possibles de ce type sont `DBBool.True`, `DBBool.False` et `DBBool.Null`, où le membre `Null` indique une valeur inconnue. Ces types logiques à trois valeurs sont couramment utilisés dans les bases de données.

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```
