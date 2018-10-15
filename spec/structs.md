# <a name="structs"></a>Structs

Les structs sont semblables aux classes car elles représentent des structures de données qui peuvent contenir des membres de données et des fonctions membres. Toutefois, contrairement aux classes, structs sont des types valeur et ne nécessitent pas d’allocation de tas. Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière valeur connue en tant qu’objet.

Les structures sont particulièrement utiles pour les petites structures de données qui ont une sémantique par rapport à leurs valeurs. Les nombres complexes, les points dans un système de coordonnées ou les paires clé-valeur dans un dictionnaire sont de bons exemples de structures. Clé à ces structures de données est qu’ils ont peu de données membres, qu’ils ne nécessitent pas d’utiliser l’héritage ni l’identité référentielle, et qu’ils peuvent être facilement implémentés à l’aide de la sémantique de valeur où assignation copie la valeur au lieu de la référence.

Comme décrit dans [types simples](types.md#simple-types), les types simples fournis par c#, tels que `int`, `double`, et `bool`, sont en fait tous les types struct. Comme ces types prédéfinis sont des structures, il est également possible d’utiliser les structures et la surcharge d’opérateur pour implémenter de nouveaux types « primitifs » dans le langage c#. Deux exemples de ces types sont fournis à la fin de ce chapitre ([exemples de Struct](structs.md#struct-examples)).

## <a name="struct-declarations"></a>Déclarations de struct

Un *struct_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau struct :

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

Un *struct_declaration* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *struct_modifier*s ([modificateurs de Struct](structs.md#struct-modifiers)), suivie éventuellement d’un `partial` modificateur, suivi par le mot clé `struct` et un *identificateur* qui nomme le struct, suivi d’une facultatif *type_parameter_list* spécification ([paramètres de Type](classes.md#type-parameters)), suivie éventuellement d’un *struct_interfaces* spécification ([Modificateur partiel](structs.md#partial-modifier))), suivie éventuellement d’un *type_parameter_constraints_clause*spécification s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), suivi par un *struct_body* ([Struct corps](structs.md#struct-body)), éventuellement suivi par un point-virgule.

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

Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration de struct.

Les modificateurs d’une déclaration de struct ont la même signification que ceux d’une déclaration de classe ([déclarations de classe](classes.md#class-declarations)).

### <a name="partial-modifier"></a>Modificateur partiel

Le `partial` modificateur indique que cela *struct_declaration* est une déclaration de type partiel. Plusieurs déclarations de struct partiel avec le même nom dans une déclaration d’espace de noms ou type englobant se combinent pour former une déclaration de struct, conformément aux règles spécifiées dans [types partiels](classes.md#partial-types).

### <a name="struct-interfaces"></a>Interfaces de struct

Une déclaration de struct peut inclure un *struct_interfaces* spécification, dans lequel cas le struct est dite implémenter directement les types d’interface donné.

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

Implémentations d’interface sont abordées plus en détail dans [implémentations d’Interface](interfaces.md#interface-implementations).

### <a name="struct-body"></a>Corps de struct

Le *struct_body* d’un struct définit les membres du struct.

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a>Membres de struct

Les membres d’un struct se composent des membres introduits par ses *struct_member_declaration*s et les membres héritent à partir du type `System.ValueType`.

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

À part les différences de noter dans [les différences de classe et de struct](structs.md#class-and-struct-differences), les descriptions des membres de classes dans [membres de classe](classes.md#class-members) via [itérateurs](classes.md#iterators) s’appliquent à struct les membres.

## <a name="class-and-struct-differences"></a>Différences entre classe et struct

Les structs diffèrent des classes à plusieurs niveaux importants :

*  Les structs sont des types valeur ([valeur sémantique](structs.md#value-semantics)).
*  Tous les types struct héritent implicitement de la classe `System.ValueType` ([héritage](structs.md#inheritance)).
*  Assignation à une variable d’un type struct crée une copie de la valeur assignée ([attribution](structs.md#assignment)).
*  La valeur par défaut d’un struct est la valeur produite en affectant tous les champs de type valeur leur valeur par défaut et référence de tous les champs de type `null` ([valeurs par défaut](structs.md#default-values)).
*  Opérations de boxing et unboxing servent à effectuer une conversion entre un type struct et `object` ([Boxing et unboxing](structs.md#boxing-and-unboxing)).
*  La signification de `this` est différente pour les structs ([cet accès](expressions.md#this-access)).
*  Déclarations de champ d’instance pour un struct ne sont pas autorisées à inclure des initialiseurs de variable ([initialiseurs de champ](structs.md#field-initializers)).
*  Un struct n’est pas autorisé à déclarer un constructeur d’instance sans paramètre ([constructeurs](structs.md#constructors)).
*  Un struct n’est pas autorisé à déclarer un destructeur ([destructeurs](structs.md#destructors)).

### <a name="value-semantics"></a>Sémantique de valeur

Les structs sont des types valeur ([types valeur](types.md#value-types)) et sont réputées pour avoir la sémantique de valeur. Classes, quant à eux, sont des types référence ([types référence](types.md#reference-types)) et sont réputées pour avoir une sémantique de référence.

Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière valeur connue en tant qu’objet. Lorsqu’un struct `B` contient un champ d’instance de type `A` et `A` est un type struct, c’est une erreur de compilation pour `A` dépendre `B` ou un type construit à partir de `B`. Un struct `X` ***dépend directement de*** un struct `Y` si `X` contient un champ d’instance de type `Y`. Étant donné cette définition, l’ensemble complet des structs dont dépend un struct est la fermeture transitive de la ***dépend directement*** relation.  Exemple :
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
est une erreur car `Node` contient un champ d’instance de son propre type.  Un autre exemple
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
est une erreur, car chacun des types `A`, `B`, et `C` dépendent d’eux.

Les classes, il est possible de deux variables référencent le même objet et par conséquent, les opérations sur une variable peuvent affecter l’objet référencé par l’autre variable. Avec les structures, les variables disposent chacune leur propre copie des données (sauf dans le cas de `ref` et `out` variables de paramètre), et il n’est pas possible pour les opérations sur une d’affecter l’autre. En outre, étant donné que les structs ne sont pas des types référence, il n’est pas possible pour les valeurs d’un type struct pour être `null`.

Étant donné la déclaration
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
le fragment de code
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
Affiche la valeur `10`. L’attribution de `a` à `b` crée une copie de la valeur, et `b` est donc pas affecté par l’assignation à `a.x`. Avait `Point` au lieu de cela été déclaré en tant que classe, le résultat serait `100` car `a` et `b` serait référencent le même objet.

### <a name="inheritance"></a>Héritage

Tous les types struct héritent implicitement de la classe `System.ValueType`, qui, à son tour, hérite de la classe `object`. Une déclaration de struct peut spécifier une liste d’interfaces implémentées, mais il n’est pas possible pour une déclaration de struct spécifier une classe de base.

Les types struct ne sont jamais abstraites et sont toujours implicitement sealed. Le `abstract` et `sealed` par conséquent, les modificateurs ne sont pas autorisés dans une déclaration de struct.

Étant donné que l’héritage n’est pas pris en charge pour les structs, l’accessibilité déclarée d’un membre de struct ne peut pas être `protected` ou `protected internal`.

Les fonctions membres d’un struct ne peut pas être `abstract` ou `virtual`et le `override` modificateur est autorisé uniquement pour substituer les méthodes héritées de `System.ValueType`.

### <a name="assignment"></a>Attribution

Affectation à une variable d’un type struct crée une copie de la valeur assignée. Cela diffère d’affectation à une variable d’un type de classe, qui copie la référence mais pas l’objet identifié par la référence.

Comme pour une assignation, lorsqu’un struct est passé comme un paramètre de valeur ou retourné comme résultat d’une fonction membre, une copie du struct est créée. Un struct peut être passé par référence à une fonction membre à l’aide un `ref` ou `out` paramètre.

Lorsqu’une propriété ou un indexeur d’un struct est la cible d’une assignation, l’expression d’instance associée de l’accès de propriété ou l’indexeur doit être classée en tant que variable. Si l’expression d’instance est classifiée en tant que valeur, une erreur de compilation se produit. Cette opération est décrite plus en détail dans [assignation Simple](expressions.md#simple-assignment).

### <a name="default-values"></a>Valeurs par défaut

Comme décrit dans [valeurs par défaut](variables.md#default-values), plusieurs types de variables sont initialisées automatiquement à leur valeur par défaut lorsqu’elles sont créées. Pour les variables de types de classe et d’autres types de référence, cette valeur par défaut est `null`. Toutefois, étant donné que les structs sont des types de valeur ne peut pas être `null`, la valeur par défaut d’un struct est la valeur produite en affectant tous les champs de type valeur leur valeur par défaut et référence de tous les champs de type `null`.

Faisant référence à la `Point` struct est déclaré, l’exemple ci-dessus
```csharp
Point[] a = new Point[100];
```
Initialise chaque `Point` dans le tableau à la valeur produite en définissant le `x` et `y` champs à zéro.

La valeur par défaut d’un struct correspond à la valeur retournée par le constructeur par défaut du struct ([constructeurs par défaut](types.md#default-constructors)). Contrairement à une classe, un struct n’est pas autorisé pour déclarer un constructeur d’instance sans paramètre. Au lieu de cela, chaque struct possède implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type à `null`.

Les structures doivent être conçus pour prendre en compte l’état de l’initialisation par défaut un état valide. Dans l’exemple
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
le constructeur d’instance défini par l’utilisateur protège contre les valeurs null uniquement où elle est explicitement appelée. Dans le cas où un `KeyValuePair` variable est soumis à l’initialisation de valeurs par défaut, le `key` et `value` champs seront null, et la structure doit être préparée à gérer cet état.

### <a name="boxing-and-unboxing"></a>Boxing et unboxing

Une valeur d’un type de classe peut être convertie en type `object` ou à un type d’interface est implémenté par la classe simplement en traitant la référence comme un autre type au moment de la compilation. De même, une valeur de type `object` ou une valeur d’un type d’interface peut être convertie vers un type de classe sans modifier la référence (mais bien sûr une vérification de type au moment de l’exécution est requise dans ce cas).

Dans la mesure où les structs ne sont pas des types référence, ces opérations sont implémentées différemment pour les types struct. Quand une valeur d’un type struct est convertie en type `object` ou en un type interface implémenté par le struct, une opération de boxing a lieu. De même, lorsqu’une valeur de type `object` ou une valeur d’un type d’interface est convertie vers un type struct, une opération d’unboxing a lieu. La principale différence dans les mêmes opérations sur les types de classes est que la conversion boxing et unboxing copie la valeur du struct ou non par l’instance boxed. Par conséquent, après une opération boxing ou unboxing, modifications apportées à la structure unboxed ne sont pas répercutées dans la structure boxed.

Quand un type struct substitue une méthode virtuelle héritée `System.Object` (tel que `Equals`, `GetHashCode`, ou `ToString`), appel de la méthode virtuelle via une instance du type struct n’entraîne pas de boxing se produise. Cela est vrai même lorsque le struct est utilisé comme un paramètre de type et l’appel s’effectue via une instance du type de paramètre de type. Exemple :
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

La sortie du programme est :
```
1
2
3
```

Bien qu’il soit style incorrecte pour `ToString` pour avoir des effets secondaires, l’exemple montre qu’aucun boxing s’est produite pour les trois appels de `x.ToString()`.

De même, le boxing jamais implicitement se produit lorsque l’accès à un membre sur un paramètre de type contraint. Par exemple, une interface `ICounter` contient une méthode `Increment` qui peut être utilisé pour modifier une valeur. Si `ICounter` est utilisé en tant que contrainte, l’implémentation de la `Increment` méthode est appelée avec une référence à la variable qui `Increment` a été appelé sur jamais une copie convertie.

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

Le premier appel à `Increment` modifie la valeur dans la variable `x`. Ce n’est pas équivalent à la deuxième appel à `Increment`, ce qui modifie la valeur dans une copie convertie de `x`. Par conséquent, la sortie du programme est :
```
0
1
1
```

Pour plus d’informations sur les conversions boxing et unboxing, consultez [Boxing et unboxing](types.md#boxing-and-unboxing).

### <a name="meaning-of-this"></a>Signification de ce

Au sein d’un constructeur d’instance ou une fonction membre d’instance d’une classe, `this` est classé en tant que valeur. Par conséquent, tandis que `this` peut être utilisé pour faire référence à l’instance pour lequel la fonction membre a été appelé, il n’est pas possible d’affecter à `this` dans une fonction membre d’une classe.

Au sein d’un constructeur d’instance d’un struct, `this` correspond à un `out` paramètre du type struct et au sein d’une fonction membre d’instance d’un struct, `this` correspond à un `ref` paramètre du type struct. Dans les deux cas, `this` est classé en tant que variable, et il est possible de modifier le struct entier pour lequel la fonction membre a été appelé en affectant à `this` ou en passant en tant qu’un `ref` ou `out` paramètre.

### <a name="field-initializers"></a>Initialiseurs de champ

Comme décrit dans [valeurs par défaut](structs.md#default-values), la valeur par défaut d’un struct se compose de la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type à `null`. Pour cette raison, un struct n’autorise pas les déclarations de champ d’instance pour inclure des initialiseurs de variable. Cette restriction s’applique uniquement aux champs d’instance. Les champs statiques d’un struct peuvent inclure des initialiseurs de variable.

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

Contrairement à une classe, un struct n’est pas autorisé pour déclarer un constructeur d’instance sans paramètre. Au lieu de cela, chaque struct possède implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type null ([deconstructeurspardéfaut](types.md#default-constructors)). Un struct peut déclarer des constructeurs d’instances ayant des paramètres. Exemple :
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

Étant donné la déclaration ci-dessus, les instructions
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
ils créent un `Point` avec `x` et `y` initialisée à zéro.

Un constructeur d’instance struct n’est pas autorisé à inclure un initialiseur de constructeur du formulaire `base(...)`.

Si le constructeur d’instance struct ne spécifie pas un initialiseur de constructeur, le `this` variable correspond à un `out` paramètre du type struct et est similaire à un `out` paramètre, `this` doit absolument être assigné () [Assignation définie](variables.md#definite-assignment)) à chaque emplacement où le constructeur retourne. Si le constructeur d’instance struct spécifie un initialiseur de constructeur, le `this` variable correspond à un `ref` paramètre du type struct et est similaire à un `ref` paramètre, `this` est considéré comme définitivement assignée sur entrée pour le corps du constructeur. Prenez en compte l’implémentation de constructeur d’instance ci-dessous :
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

Aucune fonction membre d’instance (y compris les accesseurs set des propriétés `X` et `Y`) peut être appelée jusqu'à ce que tous les champs du struct en cours de construction ont été définitivement assignés. La seule exception implique des propriétés implémentées automatiquement ([implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties)). Les règles d’affectation ([expressions d’assignation Simple](variables.md#simple-assignment-expressions)) spécifiquement exempter assignation à une auto-property d’un type struct au sein d’un constructeur d’instance de ce type struct : ce type d’assignation est considéré comme une nette affectation de champ de stockage masqué de l’auto-property. Par conséquent, ce qui suit est autorisé :

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

Les constructeurs statiques pour les structs suivent la plupart des mêmes règles que pour les classes. L’exécution d’un constructeur statique pour un type struct est déclenchée par la première des événements suivants se produise au sein d’un domaine d’application :

*  Un membre statique du type struct est référencé.
*  Un constructeur déclaré explicitement du type struct est appelé.

La création de valeurs par défaut ([valeurs par défaut](structs.md#default-values)) du struct de types ne déclenche pas le constructeur statique. (Un exemple de ceci est la valeur initiale d’éléments dans un tableau.)

## <a name="struct-examples"></a>Exemples de struct

L’exemple suivant montre deux exemples significatifs d’utilisation `struct` types afin de créer des types utilisables de même pour les types prédéfinis du langage, mais avec une sémantique modifiée.

### <a name="database-integer-type"></a>Type d’entier de base de données

Le `DBInt` struct ci-dessous implémente un type entier qui peut représenter le jeu complet de valeurs de la `int` type, plus un état supplémentaire qui indique une valeur inconnue. Un type présentant ces caractéristiques est couramment utilisé dans les bases de données.

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

Le `DBBool` struct ci-dessous implémente un type de logique à trois valeurs. Les valeurs possibles de ce type sont `DBBool.True`, `DBBool.False`, et `DBBool.Null`, où le `Null` membre indique une valeur inconnue. Ces types de logiques à trois valeurs sont couramment utilisés dans les bases de données.

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
