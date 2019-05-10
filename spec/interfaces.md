---
ms.openlocfilehash: 0a09585f4f885647230354c66a2449abb7ef1f44
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488777"
---
# <a name="interfaces"></a>Interfaces

Une interface définit un contrat. Une classe ou un struct qui implémente une interface doit adhérer à ce contrat. Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.

Interfaces peuvent contenir des méthodes, propriétés, événements et indexeurs. L’interface elle-même ne fournit pas d’implémentations pour les membres qu’elle définit. L’interface spécifie simplement les membres qui doivent être fournis par les classes ou structs qui implémentent l’interface.

## <a name="interface-declarations"></a>Déclarations d’interface

Un *interface_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau type d’interface.

```antlr
interface_declaration
    : attributes? interface_modifier* 'partial'? 'interface'
      identifier variant_type_parameter_list? interface_base?
      type_parameter_constraints_clause* interface_body ';'?
    ;
```

Un *interface_declaration* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *interface_modifier*s ([modificateurs d’Interface](interfaces.md#interface-modifiers)), suivie éventuellement d’un `partial` modificateur, suivi par le mot clé `interface` et un *identificateur* qui nomme l’interface, suivi d’un texte facultatif *variant_type_parameter_list* spécification ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)), suivie éventuellement d’un *interface_base* spécification ([interfaces de Base](interfaces.md#base-interfaces)), suivie éventuellement d’un *type_parameter_constraints_clause*spécification s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) , suivie d’une *interface_body* ([corps de l’Interface](interfaces.md#interface-body)), éventuellement suivi par un point-virgule.

### <a name="interface-modifiers"></a>Modificateurs d’interface

Un *interface_declaration* peut éventuellement inclure une séquence de modificateurs d’interface :

```antlr
interface_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | interface_modifier_unsafe
    ;
```

Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration d’interface.

Le `new` modificateur est autorisé uniquement sur les interfaces définies dans une classe. Il spécifie que l’interface masque un membre hérité par le même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).

Le `public`, `protected`, `internal`, et `private` modificateurs contrôlent l’accessibilité de l’interface. En fonction du contexte dans lequel la déclaration d’interface se produit, seuls certains de ces modificateurs peuvent être autorisée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

### <a name="partial-modifier"></a>Modificateur partiel

Le `partial` modificateur indique que cela *interface_declaration* est une déclaration de type partiel. Plusieurs déclarations partielles interface portant le même nom dans une déclaration d’espace de noms ou type englobant combinent à la déclaration d’une interface de formulaire, conformément aux règles spécifiées dans [types partiels](classes.md#partial-types).

### <a name="variant-type-parameter-lists"></a>Listes de paramètres de type Variant

Listes de paramètres de type Variant peuvent se produire uniquement sur les types d’interface et délégué. La différence d’ordinaire *type_parameter_list*s est facultatif *variance_annotation* sur chaque paramètre de type.

```antlr
variant_type_parameter_list
    : '<' variant_type_parameters '>'
    ;

variant_type_parameters
    : attributes? variance_annotation? type_parameter
    | variant_type_parameters ',' attributes? variance_annotation? type_parameter
    ;

variance_annotation
    : 'in'
    | 'out'
    ;
```

Si l’annotation de variance est `out`, le paramètre de type est dite ***covariant***. Si l’annotation de variance est `in`, le paramètre de type est dite ***contravariant***. S’il n’existe aucune annotation de variance, le paramètre de type est dite ***invariant***.

Dans l’exemple
```csharp
interface C<out X, in Y, Z> 
{
  X M(Y y);
  Z P { get; set; }
}
```
`X` est covariant, `Y` est contravariant et `Z` est invariant.

#### <a name="variance-safety"></a>Sécurité de variance

L’occurrence des annotations de variance dans la liste de paramètres de type d’un type restreint les emplacements où les types peuvent se produire dans la déclaration de type.

Un type `T` est ***sortie non sécurisés*** si dont l’un des éléments suivants :

*  `T` est un paramètre de type contravariant
*  `T` est un type de tableau avec un type d’élément de sortie non sécurisés
*  `T` est un type d’interface ou un délégué `S<A1,...,Ak>` construit à partir d’un type générique `S<X1,...,Xk>` where pour au moins un `Ai` dont l’un des éléments suivants :
   * `Xi` est covariant ou invariants et `Ai` n’est pas sûre de sortie.
   * `Xi` est contravariant ou invariant et `Ai` est entrée-safe.
   
Un type `T` est ***entrée non fiables*** si dont l’un des éléments suivants :

*  `T` est un paramètre de type covariant
*  `T` est un type de tableau avec un type d’élément d’entrée non fiables
*  `T` est un type d’interface ou un délégué `S<A1,...,Ak>` construit à partir d’un type générique `S<X1,...,Xk>` where pour au moins un `Ai` dont l’un des éléments suivants :
   * `Xi` est covariant ou invariants et `Ai` est entrée non fiables.
   * `Xi` est contravariant ou invariant et `Ai` n’est pas sûre de sortie.

Intuitivement, un type non sécurisé de sortie est interdit dans une position de sortie, et un type d’entrée non fiables est interdit dans une position d’entrée.

Est un type ***sortie-safe*** s’il n’est pas dangereux de la sortie, et ***entrée-safe*** si elle n’est pas entrée non fiables.

#### <a name="variance-conversion"></a>Conversion de variance

Les annotations de variance vise à fournir pour les conversions des types interface et délégué plus modérées (mais toujours type sécurisé). À cette fin les définitions d’implicite ([conversions implicites](conversions.md#implicit-conversions)) et les conversions explicites ([conversions explicites](conversions.md#explicit-conversions)) rendre utilisent la notion de la convertibilité de variance, qui est définie comme suit :

Un type `T<A1,...,An>` est convertible en variance en un type `T<B1,...,Bn>` si `T` est une interface ou un type délégué déclaré avec les paramètres de type variant `T<X1,...,Xn>`et pour chaque paramètre de type variant `Xi` une des opérations suivantes contient :

*  `Xi` est covariant et il existe une conversion implicite de l’identité ou de référence à partir de `Ai` à `Bi`
*  `Xi` est contravariant et une référence implicite ou la conversion d’identité existe à partir de `Bi` à `Ai`
*  `Xi` est invariant et une identité conversion existe entre `Ai` à `Bi`

### <a name="base-interfaces"></a>Interfaces de base

Une interface peut hériter de zéro ou plusieurs types d’interface, qui sont appelés les ***interfaces de base explicites*** de l’interface. Lorsqu’une interface possède une ou plusieurs interfaces de base explicites, dans la déclaration de cette interface, l’identificateur d’interface est suivie par un signe deux-points et d’une virgule liste séparée par des types d’interface de base.

```antlr
interface_base
    : ':' interface_type_list
    ;
```

Pour un type interface construite, les interfaces de base explicites sont formés en prenant les déclarations d’interface de base explicite sur la déclaration de type générique et en remplaçant, pour chaque *type_parameter* dans l’interface de base déclaration, correspondants *type_argument* du type construit.

Les interfaces de base explicites d’une interface doivent être au moins aussi accessibles que l’interface elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)). Par exemple, il est une erreur de compilation pour spécifier un `private` ou `internal` interface dans le *interface_base* d’un `public` interface.

Il s’agit d’une erreur de compilation pour qu’une interface hérite directement ou indirectement d’elle-même.

Le ***interfaces de base*** d’une interface sont les interfaces de base explicites et leurs interfaces de base. En d’autres termes, le jeu d’interfaces de base est la fermeture transitive complète des interfaces de base explicites, leurs interfaces de base explicites et ainsi de suite. Une interface hérite de tous les membres de ses interfaces de base. Dans l’exemple
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
les interfaces de base de `IComboBox` sont `IControl`, `ITextBox`, et `IListBox`.

En d’autres termes, le `IComboBox` interface ci-dessus hérite des membres `SetText` et `SetItems` ainsi que `Paint`.

Chaque interface de base d’une interface doit être compatibles avec la sortie ([sécurité de Variance](interfaces.md#variance-safety)). Une classe ou un struct qui implémente une interface également implicitement implémente toutes les interfaces de base de l’interface.

### <a name="interface-body"></a>Corps de l’interface

Le *interface_body* d’une interface définit les membres de l’interface.

```antlr
interface_body
    : '{' interface_member_declaration* '}'
    ;
```

## <a name="interface-members"></a>Membres de l’interface

Les membres d’une interface sont les membres hérités des interfaces de base et les membres déclarés par l’interface elle-même.

```antlr
interface_member_declaration
    : interface_method_declaration
    | interface_property_declaration
    | interface_event_declaration
    | interface_indexer_declaration
    ;
```

Une déclaration d’interface peut déclarer zéro ou plusieurs membres. Les membres d’une interface doivent être des méthodes, propriétés, événements ou indexeurs. Une interface ne peut pas contenir de constantes, champs, opérateurs, constructeurs d’instance, destructeurs ou types, ni une interface peut contenir les membres statiques d’aucune sorte.

Tous les membres d’interface sont implicitement publics. Il s’agit d’une erreur lors de la compilation des déclarations de membre d’interface comprennent des modificateurs. En particulier, les membres d’interface ne peuvent pas être déclarés avec les modificateurs `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, ou `static`.

L’exemple
```csharp
public delegate void StringListEvent(IStringList sender);

public interface IStringList
{
    void Add(string s);
    int Count { get; }
    event StringListEvent Changed;
    string this[int index] { get; set; }
}
```
déclare une interface qui contient l’un des types possibles de membres : Une méthode, une propriété, un événement et un indexeur.

Un *interface_declaration* crée un nouvel espace de déclaration ([déclarations](basic-concepts.md#declarations)) et le *interface_member_declaration*s immédiatement contenus par le *interface_declaration* introduire de nouveaux membres dans cet espace de déclaration. Les règles suivantes s’appliquent aux *interface_member_declaration*%s :

*  Le nom d’une méthode doit différer des noms de toutes les propriétés et les événements déclarés dans la même interface. En outre, la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même interface, et deux méthodes déclarées dans la même interface ne peuvent pas avoir signatures qui diffèrent uniquement par `ref` et `out`.
*  Le nom d’une propriété ou un événement doit différer des noms de tous les autres membres déclarés dans la même interface.
*  La signature d’un indexeur doit se distinguer de tous les autres indexeurs déclarés dans la même interface.

Les membres hérités d’une interface sont spécifiquement ne fait pas partie de l’espace de déclaration de l’interface. Par conséquent, une interface est autorisée à déclarer un membre portant le même nom ou la même signature qu’un membre hérité. Lorsque cela se produit, le membre d’interface dérivée est dite pour masquer le membre de l’interface de base. Masquer un membre hérité n'est pas considéré comme une erreur, mais elle entraîne le compilateur à émettre un avertissement. Pour supprimer l’avertissement, la déclaration du membre d’interface dérivée doit inclure un `new` modificateur pour indiquer que le membre dérivé est conçu pour masquer le membre de base. Ce sujet est abordé plus en détail dans [masquage par héritage](basic-concepts.md#hiding-through-inheritance).

Si un `new` modificateur est inclus dans une déclaration qui ne masque pas un membre hérité, un avertissement est émis à cet effet. Cet avertissement est supprimé en supprimant le `new` modificateur.

Notez que les membres de classe `object` ne sont pas, strictement parlant, les membres de n’importe quelle interface ([membres de l’Interface](interfaces.md#interface-members)). Toutefois, les membres de classe `object` sont disponibles via la recherche de membres dans n’importe quel type d’interface ([recherche de membres](expressions.md#member-lookup)).

### <a name="interface-methods"></a>Méthodes d’interface

Méthodes d’interface sont déclarés à l’aide de *interface_method_declaration*%s :

```antlr
interface_method_declaration
    : attributes? 'new'? return_type identifier type_parameter_list
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;
```

Le *attributs*, *return_type*, *identificateur*, et *formal_parameter_list* d’une déclaration de méthode d’interface ont la même ce qui signifie que ceux d’une déclaration de méthode dans une classe ([méthodes](classes.md#methods)). Une déclaration de méthode d’interface n’est pas autorisée à spécifier un corps de méthode et la déclaration par conséquent se termine toujours par un point-virgule.

Chaque type de paramètre formel d’une méthode d’interface doit être entrée-safe ([sécurité de Variance](interfaces.md#variance-safety)), et le type de retour doit être `void` ou compatibles avec la sortie. En outre, chaque contrainte de type classe, la contrainte de type interface et la contrainte de paramètre de type sur n’importe quel paramètre de type de la méthode doivent être entrée-safe.

Ces règles garantissent que tout covariant ou contravariant d’utilisation de l’interface reste type sécurisé. Par exemple :
```csharp
interface I<out T> { void M<U>() where U : T; }
```
n’est pas conforme, car l’utilisation de `T` comme une contrainte de paramètre de type sur `U` n’est pas entrée-safe.

Cette restriction n’étaient pas en place il serait possible d’enfreindre la sécurité de type de la manière suivante :
```csharp
class B {}
class D : B{}
class E : B {}
class C : I<D> { public void M<U>() {...} }
...
I<B> b = new C();
b.M<E>();
```
Il s’agit en fait un appel à `C.M<E>`. Mais cet appel nécessite que `E` dérivent `D`, de sorte que la sécurité de type sera enfreinte ici.

### <a name="interface-properties"></a>Propriétés de l'interface

Propriétés d’interface sont déclarées à l’aide de *interface_property_declaration*%s :

```antlr
interface_property_declaration
    : attributes? 'new'? type identifier '{' interface_accessors '}'
    ;

interface_accessors
    : attributes? 'get' ';'
    | attributes? 'set' ';'
    | attributes? 'get' ';' attributes? 'set' ';'
    | attributes? 'set' ';' attributes? 'get' ';'
    ;
```

Le *attributs*, *type*, et *identificateur* d’une déclaration de propriété d’interface ont la même signification que ceux d’une déclaration de propriété dans une classe ([ Propriétés](classes.md#properties)).

Les accesseurs d’une déclaration de propriété d’interface correspondent aux accesseurs d’une déclaration de propriété de classe ([accesseurs](classes.md#accessors)), sauf que le corps de l’accesseur doit toujours être un point-virgule. Par conséquent, les accesseurs indiquent simplement si la propriété est en lecture-écriture, en lecture seule ou en écriture seule.

Le type d’une propriété d’interface doit être sécurisé de sortie s’il existe un accesseur get et doit être entrée-safe s’il existe un accesseur set.

### <a name="interface-events"></a>Événements d’interface

Événements d’interface sont déclarés à l’aide de *interface_event_declaration*%s :

```antlr
interface_event_declaration
    : attributes? 'new'? 'event' type identifier ';'
    ;
```

Le *attributs*, *type*, et *identificateur* d’une déclaration event d’interface ont la même signification que ceux d’une déclaration d’événement dans une classe ([événements ](classes.md#events)).

Le type d’un événement d’interface doit être entrée-safe.

### <a name="interface-indexers"></a>Indexeurs d’interface

Indexeurs d’interface sont déclarés à l’aide de *interface_indexer_declaration*%s :

```antlr
interface_indexer_declaration
    : attributes? 'new'? type 'this' '[' formal_parameter_list ']' '{' interface_accessors '}'
    ;
```

Le *attributs*, *type*, et *formal_parameter_list* d’une déclaration d’indexeur interface ont la même signification que ceux d’une déclaration d’indexeur dans une classe ([ Indexeurs](classes.md#indexers)).

Les accesseurs d’une déclaration d’indexeur interface correspondent aux accesseurs d’une déclaration d’indexeur de classe ([indexeurs](classes.md#indexers)), sauf que le corps de l’accesseur doit toujours être un point-virgule. Par conséquent, les accesseurs indiquent simplement si l’indexeur est en lecture-écriture, en lecture seule ou en écriture seule.

Tous les types de paramètres formels d’un indexeur d’interface doivent être entrée-safe. En outre, n’importe quel `out` ou `ref` les types de paramètre formel doivent également être compatibles avec la sortie. Notez que même `out` paramètres sont requis pour être sécurisé à l’entrée, en raison d’une limitation de la plateforme sous-jacente de l’exécution.

Le type d’un indexeur d’interface doit être sécurisé de sortie s’il existe un accesseur get et doit être entrée-safe s’il existe un accesseur set.

### <a name="interface-member-access"></a>Accès aux membres d’interface

Membres d’interface sont accessibles via l’accès au membre ([l’accès au membre](expressions.md#member-access)) et accès d’indexeur ([accès indexeur](expressions.md#indexer-access)) expressions de la forme `I.M` et `I[A]`, où `I` est un type interface, `M` est une méthode, une propriété ou un événement de ce type d’interface, et `A` est une liste d’arguments indexeur.

Pour les interfaces qui sont strictement à héritage unique (chaque interface dans la chaîne d’héritage possède exactement zéro ou une interface de base directe), les effets de la recherche de membres ([recherche de membres](expressions.md#member-lookup)), appel de méthode ([ Appels de méthode](expressions.md#method-invocations)) et accès d’indexeur ([accès indexeur](expressions.md#indexer-access)) règles sont exactement les mêmes que pour les classes et structs : Plus dérivés masquer des membres moins dérivée de membres avec le même nom ou la signature. Toutefois, pour les interfaces de l’héritage multiple, ambiguïtés peuvent se produire lorsque deux ou plus des interfaces de base non liées déclarent des membres avec le même nom ou la même signature. Cette section montre plusieurs exemples de telles situations. Dans tous les cas, des casts explicites peuvent être utilisés pour résoudre les ambiguïtés.

Dans l’exemple
```csharp
interface IList
{
    int Count { get; set; }
}

interface ICounter
{
    void Count(int i);
}

interface IListCounter: IList, ICounter {}

class C
{
    void Test(IListCounter x) {
        x.Count(1);                  // Error
        x.Count = 1;                 // Error
        ((IList)x).Count = 1;        // Ok, invokes IList.Count.set
        ((ICounter)x).Count(1);      // Ok, invokes ICounter.Count
    }
}
```
les deux premières instructions provoquent des erreurs de compilation, car la recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `Count` dans `IListCounter` est ambigu. Comme l’illustre l’exemple, l’ambiguïté est résolue en effectuant un cast `x` au type d’interface de base appropriée. De telles conversions n’ont aucun coût d’exécution, il s’agit simplement d’affichage de l’instance comme un type moins dérivé au moment de la compilation.

Dans l’exemple
```csharp
interface IInteger
{
    void Add(int i);
}

interface IDouble
{
    void Add(double d);
}

interface INumber: IInteger, IDouble {}

class C
{
    void Test(INumber n) {
        n.Add(1);                // Invokes IInteger.Add
        n.Add(1.0);              // Only IDouble.Add is applicable
        ((IInteger)n).Add(1);    // Only IInteger.Add is a candidate
        ((IDouble)n).Add(1);     // Only IDouble.Add is a candidate
    }
}
```
l’appel `n.Add(1)` sélectionne `IInteger.Add` en appliquant les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). De même, l’appel `n.Add(1.0)` sélectionne `IDouble.Add`. Lorsque des casts explicites sont insérés, il y a des candidate qu’une seule méthode et donc pas d’ambiguïté.

Dans l’exemple
```csharp
interface IBase
{
    void F(int i);
}

interface ILeft: IBase
{
    new void F(int i);
}

interface IRight: IBase
{
    void G();
}

interface IDerived: ILeft, IRight {}

class A
{
    void Test(IDerived d) {
        d.F(1);                 // Invokes ILeft.F
        ((IBase)d).F(1);        // Invokes IBase.F
        ((ILeft)d).F(1);        // Invokes ILeft.F
        ((IRight)d).F(1);       // Invokes IBase.F
    }
}
```
le `IBase.F` membre est masqué par le `ILeft.F` membre. L’appel `d.F(1)` sélectionne le `ILeft.F`, même si `IBase.F` semble ne pas être masqué dans le chemin d’accès qui mène à `IRight`.

La règle intuitive de masquage des interfaces à héritage multiple est simple : Si un membre est masqué dans n’importe quel chemin d’accès, il est masqué dans tous les chemins d’accès. Étant donné que le chemin d’accès à partir de `IDerived` à `ILeft` à `IBase` masque `IBase.F`, le membre est également masqué dans le chemin d’accès à partir de `IDerived` à `IRight` à `IBase`.

## <a name="fully-qualified-interface-member-names"></a>Noms de membre d’interface qualifié complet

Un membre d’interface est parfois désigné par son ***nom qualifié complet***. Le nom qualifié complet d’un membre d’interface se compose du nom de l’interface dans laquelle le membre est déclaré par un point, suivi par le nom du membre. Le nom qualifié complet d’un membre fait référence à l’interface dans laquelle le membre est déclaré. Par exemple, compte tenu des déclarations
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}
```
le nom qualifié complet de `Paint` est `IControl.Paint` et le nom qualifié complet de `SetText` est `ITextBox.SetText`.

Dans l’exemple ci-dessus, il n’est pas possible de faire référence à `Paint` comme `ITextBox.Paint`.

Lorsqu’une interface fait partie d’un espace de noms, le nom qualifié complet d’un membre d’interface inclut le nom de l’espace de noms. Exemple :
```csharp
namespace System
{
    public interface ICloneable
    {
        object Clone();
    }
}
```

Ici, le nom qualifié complet de le `Clone` méthode est `System.ICloneable.Clone`.

## <a name="interface-implementations"></a>Implémentations d'interfaces

Interfaces peuvent être implémentées par les classes et structs. Pour indiquer qu’une classe ou un struct implémente directement une interface, l’identificateur d’interface est inclus dans la liste de classe de base de la classe ou structure. Exemple :
```csharp
interface ICloneable
{
    object Clone();
}

interface IComparable
{
    int CompareTo(object other);
}

class ListEntry: ICloneable, IComparable
{
    public object Clone() {...}
    public int CompareTo(object other) {...}
}
```

Une classe ou un struct qui implémente directement une interface également directement implémente toutes les interfaces de base de l’interface implicitement. Cela est vrai même si la classe ou structure ne répertorie pas explicitement toutes les interfaces de base dans la liste de classe de base. Exemple :
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    public void Paint() {...}
    public void SetText(string text) {...}
}
```

Ici, la classe `TextBox` implémente à la fois `IControl` et `ITextBox`.

Lorsqu’une classe `C` directement implémente une interface, toutes les classes dérivées de C également implémente l’interface implicitement. Les interfaces de base spécifiés dans une déclaration de classe peuvent être des types d’interface construite ([types construits](types.md#constructed-types)). Une interface de base ne peut pas être un paramètre de type sur son propre, même si elle peut impliquer les paramètres de type qui sont dans la portée. Le code suivant illustre comment une classe peut implémenter et étendre des types construits :
```csharp
class C<U,V> {}

interface I1<V> {}

class D: C<string,int>, I1<string> {}

class E<T>: C<int,T>, I1<T> {}
```

Les interfaces de base d’une déclaration de classe générique doivent respecter la règle de l’unicité décrite dans [l’unicité des interfaces implémentées](interfaces.md#uniqueness-of-implemented-interfaces).

### <a name="explicit-interface-member-implementations"></a>Implémentations de membres d’interface explicite

Dans le cadre de l’implémentation des interfaces, une classe ou un struct peut déclarer ***implémentations de membres d’interface explicite***. Une implémentation de membre d’interface explicite est une déclaration de méthode, propriété, événement ou l’indexeur qui fait référence à un nom de membre d’interface qualifié complet. Exemple :
```csharp
interface IList<T>
{
    T[] GetElements();
}

interface IDictionary<K,V>
{
    V this[K key];
    void Add(K key, V value);
}

class List<T>: IList<T>, IDictionary<int,T>
{
    T[] IList<T>.GetElements() {...}
    T IDictionary<int,T>.this[int index] {...}
    void IDictionary<int,T>.Add(int index, T value) {...}
}
```

Ici `IDictionary<int,T>.this` et `IDictionary<int,T>.Add` est des implémentations de membres d’interface explicite.

Dans certains cas, le nom d’un membre d’interface n’est peut-être pas approprié pour la classe d’implémentation, dans lequel cas le membre d’interface peut être implémentée à l’aide d’implémentation de membre d’interface explicite. Implémente une classe qui implémente une abstraction de fichier, par exemple, probablement un `Close` fonction membre qui a pour effet de libérer la ressource de fichier et implémenter le `Dispose` méthode de la `IDisposable` interface à l’aide d’interface explicite implémentation de membre :
```csharp
interface IDisposable
{
    void Dispose();
}

class MyFile: IDisposable
{
    void IDisposable.Dispose() {
        Close();
    }

    public void Close() {
        // Do what's necessary to close the file
        System.GC.SuppressFinalize(this);
    }
}
```

Il n’est pas possible d’accéder à une implémentation de membre d’interface explicite via son nom qualifié complet dans un appel de méthode, propriété ou accès indexeur. Une implémentation de membre d’interface explicite est uniquement accessible via une instance d’interface et est référencée dans ce cas simplement par son nom de membre.

Il s’agit d’une erreur de compilation pour une implémentation de membre d’interface explicite inclure des modificateurs d’accès, et c’est une erreur de compilation pour inclure les modificateurs `abstract`, `virtual`, `override`, ou `static`.

Implémentations de membres d’interface explicites ont des caractéristiques d’une accessibilité différente que d’autres membres. Étant donné que les implémentations de membres d’interface explicites ne sont jamais accessibles par leur nom qualifié complet dans un appel de méthode ou d’un accès à la propriété, elles se trouvent dans un sens privé. Toutefois, dans la mesure où ils sont accessibles via une instance d’interface, ils sont dans un sens également public.

Implémentations de membres d’interface explicites ont pour objectif principal :

*  Étant donné que les implémentations de membres d’interface explicites ne sont pas accessibles via des instances de classe ou un struct, ils permettent d’implémentations d’interface à exclure de l’interface publique d’une classe ou un struct. Cela est particulièrement utile lorsqu’une classe ou struct implémente une interface interne qui ne présente aucun intérêt pour le consommateur de cette classe ou struct.
*  Implémentations de membres d’interface explicites permettent de lever l’ambiguïté de membres d’interface avec la même signature. Sans les implémentations de membres d’interface explicite il serait impossible pour une classe ou un struct d’avoir différentes implémentations de membres avec la même signature de l’interface et le type de retour, comme il serait impossible pour une classe ou un struct d’avoir une implémentation tous les membres d’interface avec la même signature, mais avec différents types de retournés.

Pour une implémentation de membre d’interface explicite soit valide, la classe ou structure doit nommer une interface dans sa liste de classe de base qui contient un membre dont le nom qualifié complet, type et les types de paramètres correspondent exactement à ceux du membre d’interface explicite mise en œuvre. Par conséquent, dans la classe suivante
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
    int IComparable.CompareTo(object other) {...}    // invalid
}
```
la déclaration de `IComparable.CompareTo` génère une erreur de compilation, car `IComparable` n’est pas répertorié dans la liste de classe de base de `Shape` et n’est pas une interface de base de `ICloneable`. De même, dans les déclarations
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
}

class Ellipse: Shape
{
    object ICloneable.Clone() {...}    // invalid
}
```
la déclaration de `ICloneable.Clone` dans `Ellipse` génère une erreur de compilation, car `ICloneable` n’est pas explicitement répertorié dans la liste de classe de base de `Ellipse`.

Le nom qualifié complet d’un membre d’interface doit faire référence à l’interface dans laquelle le membre a été déclaré. Par conséquent, dans les déclarations
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
}
```
l’implémentation de membre d’interface explicite de `Paint` doit être écrit en tant que `IControl.Paint`.

### <a name="uniqueness-of-implemented-interfaces"></a>Unicité des interfaces implémentées

Les interfaces implémentées par une déclaration de type générique doivent rester uniques pour tous les types construits possible. Sans cette règle, il serait impossible de déterminer la méthode à appeler pour certains types construits. Par exemple, une déclaration de classe générique ont été autorisés à être écrit comme suit :
```csharp
interface I<T>
{
    void F();
}

class X<U,V>: I<U>, I<V>                    // Error: I<U> and I<V> conflict
{
    void I<U>.F() {...}
    void I<V>.F() {...}
}
```

Ont été cela autorisé, il serait impossible de déterminer le code à exécuter dans le cas suivant :
```csharp
I<int> x = new X<int,int>();
x.F();
```

Pour déterminer si la liste de l’interface d’une déclaration de type générique est valide, les étapes suivantes sont effectuées :

*  Laisser `L` la liste des interfaces spécifiées directement dans une classe générique, un struct ou une déclaration d’interface `C`.
*  Ajouter à `L` les interfaces des interfaces déjà de base `L`.
*  Supprimer les doublons de `L`.
*  Si tout construit type créé à partir de `C` serait, une fois que les arguments de type sont substitués dans `L`, provoquer deux interfaces dans `L` sont identiques, puis la déclaration de `C` n’est pas valide. Déclarations de contrainte ne sont pas considérées lors de la détermination de tous les types construits possible.

Dans la déclaration de classe `X` ci-dessus, la liste des interfaces `L` se compose de `I<U>` et `I<V>`. La déclaration n’est pas valide, car un type avec construit `U` et `V` en cours du même type entraînerait ces deux interfaces comme des types identiques.

Il est possible pour les interfaces spécifiées aux niveaux d’héritage différents pour unifier :
```csharp
interface I<T>
{
    void F();
}

class Base<U>: I<U>
{
    void I<U>.F() {...}
}

class Derived<U,V>: Base<U>, I<V>    // Ok
{
    void I<V>.F() {...}
}
```

Ce code est valid, même si `Derived<U,V>` implémente à la fois `I<U>` et `I<V>`. Le code
```csharp
I<int> x = new Derived<int,int>();
x.F();
```
appelle la méthode dans `Derived`, étant donné que `Derived<int,int>` efficacement ré-implémente `I<int>` ([réimplémenter l’Interface](interfaces.md#interface-re-implementation)).

### <a name="implementation-of-generic-methods"></a>Implémentation des méthodes génériques

Lorsqu’une méthode générique implicitement implémente une méthode d’interface, les contraintes données chaque paramètre de type de méthode doit être équivalente dans les deux déclarations (après n’importe quel type d’interface paramètres sont remplacés par les arguments de type approprié), où (méthode) paramètres de type sont identifiées par les positions ordinales, de gauche à droite.

Lorsqu’une méthode générique implémente explicitement une méthode d’interface, toutefois, aucune contrainte n’est autorisées sur la méthode d’implémentation. Au lieu de cela, les contraintes sont héritées de la méthode d’interface

```csharp
interface I<A,B,C>
{
    void F<T>(T t) where T: A;
    void G<T>(T t) where T: B;
    void H<T>(T t) where T: C;
}

class C: I<object,C,string>
{
    public void F<T>(T t) {...}                    // Ok
    public void G<T>(T t) where T: C {...}         // Ok
    public void H<T>(T t) where T: string {...}    // Error
}
```

La méthode `C.F<T>` implémente implicitement les `I<object,C,string>.F<T>`. Dans ce cas, `C.F<T>` n’est pas nécessaire (ni autorisés) pour spécifier la contrainte `T:object` dans la mesure où `object` est une contrainte implicite sur tous les paramètres de type. La méthode `C.G<T>` implémente implicitement `I<object,C,string>.G<T>` , car les contraintes correspondent à celles dans l’interface, une fois que les paramètres de type d’interface sont remplacés par les arguments de type correspondant. La contrainte pour la méthode `C.H<T>` est une erreur, car les types sealed (`string` dans ce cas) ne peut pas être utilisé en tant que contraintes. L’omission de la contrainte également serait une erreur, car les contraintes d’implémentations de méthode d’interface implicite sont requises pour faire correspondre. Par conséquent, il est impossible d’implémenter implicitement `I<object,C,string>.H<T>`. Cette méthode d’interface peut uniquement être implémentée à l’aide d’une implémentation de membre d’interface explicite :
```csharp
class C: I<object,C,string>
{
    ...

    public void H<U>(U u) where U: class {...}

    void I<object,C,string>.H<T>(T t) {
        string s = t;    // Ok
        H<T>(t);
    }
}
```

Dans cet exemple, l’implémentation de membre d’interface explicite appelle une méthode publique avec des contraintes strictement plus faibles. Notez que l’affectation de `t` à `s` est valide depuis `T` hérite d’une contrainte de `T:string`, même si cette contrainte n’est pas pouvant être exprimée dans le code source.

### <a name="interface-mapping"></a>Mappage d’interface

Une classe ou un struct doit fournir des implémentations de tous les membres des interfaces qui sont répertoriées dans la liste de classe de base de la classe ou structure. Le processus de localisation des implémentations de membres d’interface dans une classe ou struct d’implémentation est appelé ***mappage d’interface***.

Mappage d’interface pour une classe ou un struct `C` recherche une implémentation pour chaque membre de chaque interface spécifiée dans la liste de classe de base de `C`. L’implémentation d’un membre d’interface particulier `I.M`, où `I` est l’interface dans laquelle le membre `M` est déclaré, est déterminée en examinant chaque classe ou struct `S`, en commençant par `C` et répétition pour chaque classe de base consécutive de `C`, jusqu'à ce qu’il trouve une correspondance :

*  Si `S` contient une déclaration d’une implémentation de membre d’interface explicite qui correspond à `I` et `M`, ce membre est l’implémentation de `I.M`.
*  Sinon, si `S` contient une déclaration d’un membre public non statique qui correspond à `M`, ce membre est l’implémentation de `I.M`. Si plusieurs correspondances d’un seul membre, il n’est pas spécifié le membre qui est l’implémentation de `I.M`. Cette situation peut se produire uniquement si `S` est un type construit, où les deux membres tel que déclaré dans le type générique ont des signatures différentes, mais les arguments de type que leurs signatures identique.

Une erreur de compilation se produit si les implémentations ne peut pas être localisées pour tous les membres de toutes les interfaces spécifiées dans la liste de classe de base de `C`. Notez que les membres d’une interface incluent les membres qui sont héritées d’interfaces de base.

À des fins de mappage d’interface, un membre de classe `A` correspond à un membre d’interface `B` lorsque :

*  `A` et `B` sont des méthodes et le nom, le type, et les listes de paramètres formels de `A` et `B` sont identiques.
*  `A` et `B` sont des propriétés, le nom et le type de `A` et `B` sont identiques, et `A` a les mêmes accesseurs que `B` (`A` est autorisé à avoir des accesseurs supplémentaires s’il n’est pas une interface explicite implémentation de membre).
*  `A` et `B` sont des événements et le nom et le type de `A` et `B` sont identiques.
*  `A` et `B` sont des indexeurs, le type et les listes de paramètres formels de `A` et `B` sont identiques, et `A` a les mêmes accesseurs que `B` (`A` est autorisé à avoir des accesseurs supplémentaires s’il n’est pas un implémentation de membre d’interface explicite).

Les implications notables de l’algorithme de mappage d’interface sont :

*  Implémentations de membres d’interface explicites sont prioritaires sur les autres membres dans la même classe ou struct lors de la détermination du membre de classe ou struct qui implémente un membre d’interface.
*  Les membres non publics ni statiques participent au mappage d’interface.

Dans l’exemple
```csharp
interface ICloneable
{
    object Clone();
}

class C: ICloneable
{
    object ICloneable.Clone() {...}
    public object Clone() {...}
}
```
le `ICloneable.Clone` membre `C` devient l’implémentation de `Clone` dans `ICloneable` , car les implémentations de membres d’interface explicites sont prioritaires sur les autres membres.

Si une classe ou un struct implémente deux ou plusieurs interfaces contenant un membre avec le même nom, type et les types de paramètre, il est possible de mapper chacun de ces membres d’interface à un seul membre de classe ou un struct. Exemple :
```csharp
interface IControl
{
    void Paint();
}

interface IForm
{
    void Paint();
}

class Page: IControl, IForm
{
    public void Paint() {...}
}
```

Ici, le `Paint` méthodes des deux `IControl` et `IForm` sont mappées sur le `Paint` méthode dans `Page`. Il est bien sûr également possible d’avoir des implémentations de membres d’interface explicite distincte pour les deux méthodes.

Si une classe ou un struct implémente une interface qui contient les membres masqués, certains membres doivent nécessairement être implémentés via des implémentations de membres d’interface explicite. Exemple :
```csharp
interface IBase
{
    int P { get; }
}

interface IDerived: IBase
{
    new int P();
}
```

Une implémentation de cette interface nécessite au moins une implémentation de membre d’interface explicite et prendrait l’une des formes suivantes
```csharp
class C: IDerived
{
    int IBase.P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    public int P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    int IBase.P { get {...} }
    public int P() {...}
}
```

Lorsqu’une classe implémente plusieurs interfaces qui ont la même interface de base, il peut y avoir qu’une seule implémentation de l’interface de base. Dans l’exemple
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

class ComboBox: IControl, ITextBox, IListBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
    void IListBox.SetItems(string[] items) {...}
}
```
Il n’est pas possible d’avoir des implémentations distinctes pour le `IControl` nommé dans la liste de classe de base, le `IControl` héritée par `ITextBox`et le `IControl` héritée par `IListBox`. En effet, il n’existe aucune notion d’identité séparée pour ces interfaces. Au lieu de cela, les implémentations de `ITextBox` et `IListBox` partagent la même implémentation de `IControl`, et `ComboBox` est considéré comme il vous suffit d’implémenter trois interfaces, `IControl`, `ITextBox`, et `IListBox`.

Les membres de classe de base participent au mappage d’interface. Dans l’exemple
```csharp
interface Interface1
{
    void F();
}

class Class1
{
    public void F() {}
    public void G() {}
}

class Class2: Class1, Interface1
{
    new public void G() {}
}
```
la méthode `F` dans `Class1` est utilisée dans `Class2`d’implémentation de `Interface1`.

### <a name="interface-implementation-inheritance"></a>Héritage de l’implémentation d’interface

Une classe hérite de toutes les implémentations d’interface fournies par ses classes de base.

Sans explicitement ***ré-implémenter*** une interface, une classe dérivée ne peut en aucun cas modifier les mappages d’interface hérite de ses classes de base. Par exemple, dans les déclarations
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public void Paint() {...}
}

class TextBox: Control
{
    new public void Paint() {...}
}
```
le `Paint` méthode dans `TextBox` masque la `Paint` méthode dans `Control`, mais il ne modifie pas le mappage de `Control.Paint` sur `IControl.Paint`et les appels à `Paint` via la classe seront instances et les instances de l’interface avoir les effets suivants
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes Control.Paint();
```

Toutefois, lorsqu’une méthode d’interface est mappée à une méthode virtuelle dans une classe, il est possible pour les classes dérivées de substituer la méthode virtuelle et de modifier l’implémentation de l’interface. Par exemple, les déclarations ci-dessus de réécriture
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public virtual void Paint() {...}
}

class TextBox: Control
{
    public override void Paint() {...}
}
```
les effets suivants seront maintenant appliqués
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes TextBox.Paint();
```

Dans la mesure où les implémentations de membres d’interface explicite ne peut pas être déclarées comme virtuelles, il n’est pas possible de remplacer une implémentation de membre d’interface explicite. Toutefois, il est parfaitement valide pour une implémentation de membre d’interface explicite appeler une autre méthode, et que les autres méthode peut être déclarée virtuel pour autoriser les classes dérivées pour la remplacer. Exemple :
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() { PaintControl(); }
    protected virtual void PaintControl() {...}
}

class TextBox: Control
{
    protected override void PaintControl() {...}
}
```

Ici, les classes dérivées de `Control` pouvez le spécialiser l’implémentation de `IControl.Paint` en substituant le `PaintControl` (méthode).

### <a name="interface-re-implementation"></a>Réimplémentation de l’interface

Une classe qui hérite d’une implémentation d’interface est autorisée à ***ré-implémenter*** l’interface en l’incluant dans la liste de classe de base.

Une nouvelle implémentation d’une interface suit exactement les règles de mappage interface même que l’implémentation initiale d’une interface. Par conséquent, le mappage d’interface hérité n’a aucun effet sur le mappage d’interface établie pour la nouvelle implémentation de l’interface. Par exemple, dans les déclarations
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() {...}
}

class MyControl: Control, IControl
{
    public void Paint() {}
}
```
le fait que `Control` mappe `IControl.Paint` sur `Control.IControl.Paint` n’affecte pas la réimplémentation dans `MyControl`, laquelle correspond `IControl.Paint` sur `MyControl.Paint`.

Hérité des déclarations de membre public et membre d’interface explicite hérité déclarations participent dans le processus de mappage d’interface pour les interfaces implémentées de nouveau. Exemple :
```csharp
interface IMethods
{
    void F();
    void G();
    void H();
    void I();
}

class Base: IMethods
{
    void IMethods.F() {}
    void IMethods.G() {}
    public void H() {}
    public void I() {}
}

class Derived: Base, IMethods
{
    public void F() {}
    void IMethods.H() {}
}
```

Ici, l’implémentation de `IMethods` dans `Derived` mappe les méthodes d’interface sur `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, et `Base.I`.

Lorsqu’une classe implémente une interface, il implicitement toutes les interfaces de base de cette interface implémente également. De même, une nouvelle implémentation d’une interface est également implicitement une réimplémentation de toutes les interfaces de base de l’interface. Exemple :
```csharp
interface IBase
{
    void F();
}

interface IDerived: IBase
{
    void G();
}

class C: IDerived
{
    void IBase.F() {...}
    void IDerived.G() {...}
}

class D: C, IDerived
{
    public void F() {...}
    public void G() {...}
}
```

Ici, la réimplémentation de `IDerived` nouveau implémente également `IBase`, le mappage `IBase.F` sur `D.F`.

### <a name="abstract-classes-and-interfaces"></a>Interfaces et classes abstraites

Comme une classe non abstraite, une classe abstraite doit fournir des implémentations de tous les membres des interfaces qui sont répertoriées dans la liste de classe de base de la classe. Toutefois, une classe abstraite est autorisée à mapper les méthodes d’interface à des méthodes abstraites. Exemple :
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    public abstract void F();
    public abstract void G();
}
```

Ici, l’implémentation de `IMethods` mappe `F` et `G` à des méthodes abstraites, qui doit être substitué dans les classes non abstraites dérivées de `C`.

Notez que les implémentations de membres d’interface explicite ne peut pas être abstraites, mais implémentations de membres d’interface explicites sont bien entendu autorisées à appeler les méthodes abstraites. Exemple :
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    void IMethods.F() { FF(); }
    void IMethods.G() { GG(); }
    protected abstract void FF();
    protected abstract void GG();
}
```

Ici, les classes non abstraites dérivées de `C` serait nécessaire pour remplacer `FF` et `GG`, et offrir ainsi l’implémentation réelle de `IMethods`.
