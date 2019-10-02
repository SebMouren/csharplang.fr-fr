---
ms.openlocfilehash: e0def754174ab8646f9b849abe86d2c375c958b6
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703984"
---
# <a name="classes"></a>Classes

Une classe est une structure de données qui peut contenir des données membres (constantes et champs), des fonctions membres (méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et des types imbriqués. Les types de classe prennent en charge l’héritage, mécanisme par lequel une classe dérivée peut étendre et spécialiser une classe de base.

## <a name="class-declarations"></a>Déclarations de classe

Un *class_declaration* est un *type_declaration* ([déclarations de type](namespaces.md#type-declarations)) qui déclare une nouvelle classe.

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

Un *class_declaration* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *class_modifier*s ([modificateurs de classe](classes.md#class-modifiers)), suivi d’un modificateur facultatif `partial`, suivi du mot clé. `class` et un *identificateur* qui nomme la classe, suivis d’un *type_parameter_list* facultatif ([paramètres de type](classes.md#type-parameters)), suivi d’une spécification *Class_base* facultative (spécification de[base de classe](classes.md#class-base-specification)), suivie de un ensemble facultatif de *type_parameter_constraints_clause*s ([contraintes de paramètre de type](classes.md#type-parameter-constraints)), suivi d’un *class_body* ([corps de classe](classes.md#class-body)), éventuellement suivi d’un point-virgule.

Une déclaration de classe ne peut pas fournir *type_parameter_constraints_clause*, sauf si elle fournit également un *type_parameter_list*.

Une déclaration de classe qui fournit un *type_parameter_list* est une ***déclaration de classe générique***. En outre, toute classe imbriquée dans une déclaration de classe générique ou une déclaration de struct générique est elle-même une déclaration de classe générique, car les paramètres de type pour le type conteneur doivent être fournis pour créer un type construit.

### <a name="class-modifiers"></a>Modificateurs de classe

Un *class_declaration* peut éventuellement inclure une séquence de modificateurs de classe :

```antlr
class_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'abstract'
    | 'sealed'
    | 'static'
    | class_modifier_unsafe
    ;
```

Il s’agit d’une erreur de compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration de classe.

Le `new` modificateur est autorisé sur les classes imbriquées. Il spécifie que la classe masque un membre hérité du même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier). Il s’agit d’une erreur de compilation pour `new` que le modificateur apparaisse sur une déclaration de classe qui n’est pas une déclaration de classe imbriquée.

Les `public`modificateurs `internal`, `protected`, et`private` contrôlent l’accessibilité de la classe. Selon le contexte dans lequel la déclaration de classe se produit, certains de ces modificateurs peuvent ne pas être autorisés ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Les `abstract`modificateurs `static` , `sealed` et sont décrits dans les sections suivantes.

#### <a name="abstract-classes"></a>Classes abstraites

Le `abstract` modificateur est utilisé pour indiquer qu’une classe est incomplète et qu’elle est destinée à être utilisée uniquement comme classe de base. Une classe abstraite diffère d’une classe non abstraite des façons suivantes :

*  Une classe abstraite ne peut pas être instanciée directement, et il s’agit d’une erreur au `new` moment de la compilation pour utiliser l’opérateur sur une classe abstraite. Bien qu’il soit possible d’avoir des variables et des valeurs dont les types au moment de la compilation sont abstraits, ces `null` variables et valeurs doivent être ou contenir des références à des instances de classes non abstraites dérivées des types abstraits.
*  Une classe abstraite est autorisée (mais pas obligatoire) à contenir des membres abstraits.
*  Une classe abstraite ne peut pas être sealed.

Lorsqu’une classe non abstraite est dérivée d’une classe abstraite, la classe non abstraite doit inclure des implémentations réelles de tous les membres abstraits hérités, remplaçant ainsi ces membres abstraits. Dans l’exemple
```csharp
abstract class A
{
    public abstract void F();
}

abstract class B: A
{
    public void G() {}
}

class C: B
{
    public override void F() {
        // actual implementation of F
    }
}
```
la classe `A` abstraite introduit une méthode `F`abstraite. La `B` classe introduit une méthode `G`supplémentaire, mais puisqu’elle ne fournit pas d' `F`implémentation `B` de, doit également être déclarée comme abstract. La `C` classe`F` se substitue à et fournit une implémentation réelle. Étant donné qu’il n’y a `C`aucun `C` membre abstrait dans, est autorisé (mais pas obligatoire) à être non abstrait.

#### <a name="sealed-classes"></a>Classes sealed

Le `sealed` modificateur est utilisé pour empêcher la dérivation à partir d’une classe. Une erreur de compilation se produit si une classe sealed est spécifiée en tant que classe de base d’une autre classe.

Une classe sealed ne peut pas également être une classe abstraite.

Le `sealed` modificateur est principalement utilisé pour empêcher la dérivation involontaire, mais il permet également certaines optimisations au moment de l’exécution. En particulier, étant donné qu’une classe sealed est connue pour ne jamais avoir de classes dérivées, il est possible de transformer des appels de membre de fonction virtuelle sur des instances de classe sealed en appels non virtuels.

#### <a name="static-classes"></a>Classes statiques

Le `static` modificateur est utilisé pour marquer la classe déclarée comme une ***classe statique***. Une classe statique ne peut pas être instanciée, elle ne peut pas être utilisée en tant que type et ne peut contenir que des membres statiques. Seule une classe statique peut contenir des déclarations de méthodes d’extension ([méthodes d’extension](classes.md#extension-methods)).

Une déclaration de classe statique est soumise aux restrictions suivantes :

*  Une classe statique ne peut pas inclure `sealed` un `abstract` modificateur ou. Notez, toutefois, qu’étant donné qu’une classe statique ne peut pas être instanciée ou dérivée de, elle se comporte comme si elle était à la fois sealed et abstract.
*  Une classe statique ne peut pas inclure une spécification *class_base* ([spécification de base de classe](classes.md#class-base-specification)) et ne peut pas spécifier explicitement une classe de base ou une liste d’interfaces implémentées. Une classe statique hérite implicitement du type `object`.
*  Une classe statique ne peut contenir que des membres statiques ([membres statiques et d’instance](classes.md#static-and-instance-members)). Notez que les constantes et les types imbriqués sont classés en tant que membres statiques.
*  Une classe statique ne peut pas avoir `protected` de `protected internal` membres avec ou d’accessibilité déclarée.

Il s’agit d’une erreur au moment de la compilation pour enfreindre l’une de ces restrictions.

Une classe statique n’a pas de constructeurs d’instance. Il n’est pas possible de déclarer un constructeur d’instance dans une classe statique, et aucun constructeur d’instance par défaut ([constructeurs par défaut](classes.md#default-constructors)) n’est fourni pour une classe statique.

Les membres d’une classe statique ne sont pas automatiquement statiques, et les déclarations de membre doivent `static` inclure explicitement un modificateur (à l’exception des constantes et des types imbriqués). Quand une classe est imbriquée dans une classe externe statique, la classe imbriquée n’est pas une classe statique, sauf si elle `static` comprend explicitement un modificateur.

__Référencement des types de classes statiques__

Un *namespace_or_type_name* ([espace de noms et nom de type](basic-concepts.md#namespace-and-type-names)) est autorisé à faire référence à une classe statique si

*  *Namespace_or_type_name* est le `T` dans un *namespace_or_type_name* de la forme `T.I`, ou
*  *Namespace_or_type_name* est le `T` dans un *typeof_expression* ([liste d’arguments](expressions.md#argument-lists)1) de la forme `typeof(T)`.

Un *primary_expression* ([fonction members](expressions.md#function-members)) est autorisé à référencer une classe statique si

*  Le *primary_expression* est le `E` dans un *member_access* ([vérification de la résolution de surcharge dynamique au moment](expressions.md#compile-time-checking-of-dynamic-overload-resolution)de la compilation) sous la forme `E.I`.

Dans tout autre contexte, il s’agit d’une erreur de compilation pour référencer une classe statique. Par exemple, il s’agit d’une erreur pour une classe statique à utiliser comme classe de base, un type constitutif ([types imbriqués](classes.md#nested-types)) d’un membre, un argument de type générique ou une contrainte de paramètre de type. De même, une classe statique ne peut pas être utilisée dans un type de tableau, un `new` type pointeur, une expression, une `is` expression de Cast `as` , une expression `sizeof` , une expression, une expression ou une expression de valeur par défaut.

### <a name="partial-modifier"></a>Modificateur partiel

Le modificateur `partial` est utilisé pour indiquer que ce *class_declaration* est une déclaration de type partielle. Plusieurs déclarations de type partiel portant le même nom dans un espace de noms englobant ou une déclaration de type sont combinées pour former une déclaration de type, suivant les règles spécifiées dans les [types partiels](classes.md#partial-types).

La déclaration d’une classe distribuée sur des segments distincts de texte de programme peut être utile si ces segments sont produits ou maintenus dans des contextes différents. Par exemple, une partie d’une déclaration de classe peut être générée par l’ordinateur, tandis que l’autre est créée manuellement. La séparation du texte des deux empêche les mises à jour par l’une d’un conflit avec les mises à jour par l’autre.

### <a name="type-parameters"></a>Paramètres de type

Un paramètre de type est un identificateur simple qui désigne un espace réservé pour un argument de type fourni pour créer un type construit. Un paramètre de type est un espace réservé formel pour un type qui sera fourni ultérieurement. En revanche, un argument de type ([arguments de type](types.md#type-arguments)) est le type réel qui remplace le paramètre de type lors de la création d’un type construit.

```antlr
type_parameter_list
    : '<' type_parameters '>'
    ;

type_parameters
    : attributes? type_parameter
    | type_parameters ',' attributes? type_parameter
    ;

type_parameter
    : identifier
    ;
```

Chaque paramètre de type dans une déclaration de classe définit un nom dans l’espace de déclaration ([déclarations](basic-concepts.md#declarations)) de cette classe. Par conséquent, il ne peut pas avoir le même nom qu’un autre paramètre de type ou un membre déclaré dans cette classe. Un paramètre de type ne peut pas avoir le même nom que le type lui-même.

### <a name="class-base-specification"></a>Spécification de base de classe

Une déclaration de classe peut inclure une spécification *class_base* , qui définit la classe de base directe de la classe et les interfaces ([interfaces](interfaces.md)) implémentées directement par la classe.

```antlr
class_base
    : ':' class_type
    | ':' interface_type_list
    | ':' class_type ',' interface_type_list
    ;

interface_type_list
    : interface_type (',' interface_type)*
    ;
```

La classe de base spécifiée dans une déclaration de classe peut être un type de classe construit ([types construits](types.md#constructed-types)). Une classe de base ne peut pas être un paramètre de type seul, bien qu’il puisse impliquer les paramètres de type qui se trouvent dans la portée.

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a>Classes de base

Quand un *class_type* est inclus dans le *class_base*, il spécifie la classe de base directe de la classe déclarée. Si une déclaration de classe n’a pas de *class_base*, ou si le *class_base* répertorie uniquement les types d’interface, la classe de base directe est supposée être `object`. Une classe hérite des membres de sa classe de base directe, comme décrit dans [héritage](classes.md#inheritance).

Dans l’exemple
```csharp
class A {}

class B: A {}
```
la `A` classe est considérée comme la classe de base directe `B`de et `B` est dite dérivée `A`de. Étant `A` donné que ne spécifie pas explicitement une classe de base directe, sa classe de `object`base directe est implicitement.

Pour un type de classe construit, si une classe de base est spécifiée dans la déclaration de classe générique, la classe de base du type construit est obtenue en substituant, pour chaque *type_parameter* dans la déclaration de la classe de base, la *type_argument correspondante* du type construit. Compte tenu des déclarations de classes génériques
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
la classe de base du type `G<int>` construit est. `B<string,int[]>`

La classe de base directe d’un type de classe doit être au moins aussi accessible que le type de classe lui-même ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)). Par exemple, il s’agit d’une erreur de compilation pour `public` qu’une classe dérive `private` d' `internal` une classe ou.

La classe de base directe d’un type de classe ne doit pas être de l’un `System.Array`des `System.Delegate`types suivants `System.Enum`:, `System.ValueType`, `System.MulticastDelegate`, ou. En outre, une déclaration de classe générique `System.Attribute` ne peut pas être utilisée en tant que classe de base directe ou indirecte.

Tout en déterminant la `A` signification de la spécification directe de la classe de base d’une classe `B`, la `B` classe de base directe de `object`est temporairement supposée être. Intuitivement, cela garantit que la signification d’une spécification de classe de base ne peut pas dépendre de lui-même de manière récursive. L’exemple suivant :
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
`A<C.B>` est erroné `object`, car dans la spécification de la classe de base, la `C` classe de base directe de est considérée comme étant, et par conséquent (par les règles des [noms de type et d’espace de noms](basic-concepts.md#namespace-and-type-names)) `C` n’est pas considérée comme ayant un membre `B`.

Les classes de base d’un type de classe sont la classe de base directe et ses classes de base. En d’autres termes, l’ensemble de classes de base est la fermeture transitive de la relation de la classe de base directe. En vous référant à l’exemple ci-dessus, `B` les `A` classes `object`de base de sont et. Dans l’exemple
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
les classes de base `D<int>` de `C<int[]>`sont `B<IComparable<int[]>>`, `A`, et `object`.

À l’exception `object`de la classe, chaque type de classe a une seule classe de base directe. La `object` classe n’a pas de classe de base directe et est la classe de base fondamentale de toutes les autres classes.

Quand une classe `B` dérive d’une classe `A`, il s’agit d’une `B`erreur de compilation `A` pour que dépende de. Une classe ***dépend directement de*** sa classe de base directe (le cas échéant) et ***dépend directement de*** la classe dans laquelle elle est immédiatement imbriquée (le cas échéant). À partir de cette définition, l’ensemble complet des classes dont dépend une classe est la fermeture réflexive et transitive de la relation ***directement dépend*** de la relation.

L’exemple
```csharp
class A: A {}
```
est erroné, car la classe dépend elle-même. De même, l’exemple
```csharp
class A: B {}
class B: C {}
class C: A {}
```
est erroné, car les classes dépendent d’elles-mêmes de façon circulaire. Enfin, l’exemple
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
génère une erreur au moment de la compilation `A` , car `B.C` dépend de (sa classe de base directe), `B` qui dépend de (sa `A`classe englobante immédiate), qui dépend de façon circulaire.

Notez qu’une classe ne dépend pas des classes qui sont imbriquées dans celle-ci. Dans l’exemple
```csharp
class A
{
    class B: A {}
}
```
`B`dépend de `A` (car `A` est à la fois sa classe de base directe et sa classe englobante immédiate `A` ), `B` mais ne dépend pas `B` de (étant donné qu’il ne s’agit ni d’une classe de base, ni d’une classe englobante de `A`). Ainsi, l’exemple est valide.

Il n’est pas possible de dériver `sealed` d’une classe. Dans l’exemple
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
la `B` classe est erronée, car elle tente de dériver `A`de la `sealed` classe.

#### <a name="interface-implementations"></a>Implémentations d'interfaces

Une spécification *class_base* peut inclure une liste de types d’interfaces, auquel cas la classe est dite qui implémente directement les types d’interfaces donnés. Les implémentations d’interface sont abordées plus en détail dans [implémentations d’interface](interfaces.md#interface-implementations).

### <a name="type-parameter-constraints"></a>Contraintes de paramètre de type

Les déclarations de type et de méthode génériques peuvent éventuellement spécifier des contraintes de paramètre de type en incluant *type_parameter_constraints_clause*s.

```antlr
type_parameter_constraints_clause
    : 'where' type_parameter ':' type_parameter_constraints
    ;

type_parameter_constraints
    : primary_constraint
    | secondary_constraints
    | constructor_constraint
    | primary_constraint ',' secondary_constraints
    | primary_constraint ',' constructor_constraint
    | secondary_constraints ',' constructor_constraint
    | primary_constraint ',' secondary_constraints ',' constructor_constraint
    ;

primary_constraint
    : class_type
    | 'class'
    | 'struct'
    ;

secondary_constraints
    : interface_type
    | type_parameter
    | secondary_constraints ',' interface_type
    | secondary_constraints ',' type_parameter
    ;

constructor_constraint
    : 'new' '(' ')'
    ;
```

Chaque *type_parameter_constraints_clause* se compose du jeton `where`, suivi du nom d’un paramètre de type, suivi d’un signe deux-points et de la liste de contraintes pour ce paramètre de type. Il peut y avoir au plus `where` une clause pour chaque paramètre de type, `where` et les clauses peuvent être listées dans n’importe quel ordre. À l' `get` instar des jetons et `set` dans un accesseur `where` de propriété, le jeton n’est pas un mot clé.

La liste de contraintes donnée dans une `where` clause peut inclure l’un des composants suivants, dans cet ordre : une seule contrainte primaire, une ou plusieurs contraintes secondaires et la contrainte de constructeur, `new()`.

Une contrainte principale peut être un type de classe ou la contrainte de type ***référence*** `class` ou la ***contrainte*** `struct`de type valeur. Une contrainte secondaire peut être *type_parameter* ou *INTERFACE_TYPE*.

La contrainte de type référence spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type référence. Tous les types de classe, d’interface, de délégué, de tableau et de type connu comme étant un type référence (tel que défini ci-dessous) répondent à cette contrainte.

La contrainte de type valeur spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type valeur n’acceptant pas les valeurs NULL. Tous les types struct non Nullable, les types ENUM et les paramètres de type ayant la contrainte de type valeur satisfont cette contrainte. Notez que, bien qu’il soit classifié comme un type valeur, un type Nullable ([types Nullable](types.md#nullable-types)) ne satisfait pas la contrainte de type valeur. Un paramètre de type ayant la contrainte de type valeur ne peut pas également avoir le *constructor_constraint*.

Les types de pointeurs ne sont jamais autorisés à être des arguments de type et ne sont pas considérés comme répondant aux contraintes de type référence ou de type valeur.

Si une contrainte est un type de classe, un type d’interface ou un paramètre de type, ce type spécifie un « type de base » minimal que chaque argument de type utilisé pour ce paramètre de type doit prendre en charge. Chaque fois qu’un type construit ou une méthode générique est utilisé, l’argument de type est comparé aux contraintes sur le paramètre de type au moment de la compilation. L’argument de type fourni doit satisfaire aux conditions décrites dans [satisfaire les contraintes](types.md#satisfying-constraints).

Une contrainte *class_type* doit respecter les règles suivantes :

*  Le type doit être un type de classe.
*  Le type ne doit pas `sealed`être.
*  Le type ne doit pas être l’un des types suivants `System.Array`: `System.Delegate`, `System.Enum`, ou `System.ValueType`.
*  Le type ne doit pas `object`être. Étant donné que tous les `object`types dérivent de, une telle contrainte n’aura aucun effet si elle était autorisée.
*  Au plus une contrainte pour un paramètre de type donné peut être un type de classe.

Un type spécifié en tant que contrainte *INTERFACE_TYPE* doit respecter les règles suivantes :

*  Le type doit être un type d’interface.
*  Un type ne doit pas être spécifié plus d’une fois dans `where` une clause donnée.

Dans les deux cas, la contrainte peut impliquer l’un des paramètres de type du type ou de la déclaration de méthode associés dans le cadre d’un type construit et peut impliquer le type déclaré.

Tout type de classe ou d’interface spécifié en tant que contrainte de paramètre de type doit être au moins aussi accessible ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)) que le type ou la méthode générique déclaré (e).

Un type spécifié en tant que contrainte *type_parameter* doit respecter les règles suivantes :

*  Le type doit être un paramètre de type.
*  Un type ne doit pas être spécifié plus d’une fois dans `where` une clause donnée.

En outre, il ne doit y avoir aucun cycle dans le graphique de dépendance des paramètres de type, où la dépendance est une relation transitive définie par :

*  Si un `T` paramètre de type est utilisé en tant que contrainte pour le `S` paramètre `S` de type, ***dépend de*** `T`.
*  Si un paramètre `S` de type dépend d’un paramètre `T` de `T` type et dépend d’un `U` paramètre `S` de type, ***dépend de*** `U`.

Compte tenu de cette relation, il s’agit d’une erreur de compilation pour qu’un paramètre de type dépende lui-même (directement ou indirectement).

Toutes les contraintes doivent être cohérentes entre les paramètres de type dépendant. Si le paramètre `S` de type dépend du `T` paramètre de type, alors :

*  `T`ne doit pas avoir la contrainte de type valeur. Dans le `T` cas contraire, est `S` effectivement sealed, ce qui signifie qu’il `T`doit être du même type que, ce qui élimine le besoin de deux paramètres de type.
*  Si `S` a la contrainte de type valeur, `T` ne doit pas avoir de contrainte *class_type* .
*  Si `S` a une contrainte *class_type* `A` et `T` a une contrainte *class_type* `B`, il doit y avoir une conversion d’identité ou une conversion de référence implicite de `A` à `B` ou une conversion de référence implicite de `B` à `A`.
*  Si `S` dépend également du paramètre de type `U` et `U` a une contrainte *class_type* `A` et `T` a une contrainte *class_type* `B`, il doit y avoir une conversion d’identité ou une conversion de référence implicite de `A` pour `B` ou une conversion de référence implicite de 0 à 1.

La contrainte de type `S` valeur et `T` la contrainte de type référence sont valides. Cela limite `T` en fait les types `System.Object`, `System.ValueType` `System.Enum`, et n’importe quel type d’interface.

Si la `where` clause d’un paramètre de type inclut une contrainte de constructeur (qui a `new()`la forme), il est possible d' `new` utiliser l’opérateur pour créer des instances du type ([expressions de création d’objet](expressions.md#object-creation-expressions)). Tout argument de type utilisé pour un paramètre de type avec une contrainte de constructeur doit avoir un constructeur sans paramètre public (ce constructeur existe implicitement pour tout type valeur) ou être un paramètre de type ayant la contrainte de type valeur ou la contrainte de constructeur (consultez [Contraintes de paramètre de type](classes.md#type-parameter-constraints) pour plus d’informations).

Voici quelques exemples de contraintes :
```csharp
interface IPrintable
{
    void Print();
}

interface IComparable<T>
{
    int CompareTo(T value);
}

interface IKeyProvider<T>
{
    T GetKey();
}

class Printer<T> where T: IPrintable {...}

class SortedList<T> where T: IComparable<T> {...}

class Dictionary<K,V>
    where K: IComparable<K>
    where V: IPrintable, IKeyProvider<K>, new()
{
    ...
}
```

L’exemple suivant est une erreur, car elle provoque une circularité dans le graphique de dépendance des paramètres de type :
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

Les exemples suivants illustrent des situations non valides supplémentaires :
```csharp
class Sealed<S,T>
    where S: T
    where T: struct        // Error, T is sealed
{
    ...
}

class A {...}

class B {...}

class Incompat<S,T>
    where S: A, T
    where T: B                // Error, incompatible class-type constraints
{
    ...
}

class StructWithClass<S,T,U>
    where S: struct, T
    where T: U
    where U: A                // Error, A incompatible with struct
{
    ...
}
```

La ***classe de base effective*** d’un paramètre `T` de type est définie comme suit :

*  Si `T` n’a pas de contraintes primaires ni de contraintes de paramètre de type, `object`sa classe de base effective est.
*  Si `T` a la contrainte de type valeur, sa classe de base `System.ValueType`effective est.
*  Si `T` a une contrainte *class_type* `C` mais pas de contraintes *type_parameter* , sa classe de base effective est `C`.
*  Si `T` n’a pas de contrainte *class_type* mais possède une ou plusieurs contraintes *type_parameter* , sa classe de base effective est le type le plus englobé (les[opérateurs de conversion levés](conversions.md#lifted-conversion-operators)) dans l’ensemble des classes de base effectives de son *TYPE_* contraintes de paramètres. Les règles de cohérence garantissent qu’il existe un type le plus englobé.
*  Si `T` a à la fois une contrainte *class_type* et une ou plusieurs contraintes *type_parameter* , sa classe de base effective est le type le plus englobé (les[opérateurs de conversion levés](conversions.md#lifted-conversion-operators)) dans le jeu constitué du *class_type* contrainte de `T` et des classes de base effectives de ses contraintes *type_parameter* . Les règles de cohérence garantissent qu’il existe un type le plus englobé.
*  Si `T` a la contrainte de type référence, mais pas de contraintes *class_type* , sa classe de base effective est `object`.

Dans le cadre de ces règles, si T a une contrainte `V` qui est un *Value_type*, utilisez à la place le type de base le plus spécifique de `V` qui est un *class_type*. Cela ne peut jamais se produire dans une contrainte donnée explicitement, mais peut se produire lorsque les contraintes d’une méthode générique sont implicitement héritées par une déclaration de méthode de substitution ou une implémentation explicite d’une méthode d’interface.

Ces règles garantissent que la classe de base effective est toujours un *class_type*.

L' ***ensemble d’interfaces effectif*** d’un paramètre `T` de type est défini comme suit :

*  Si `T` n’a pas de *secondary_constraints*, son ensemble d’interfaces effectif est vide.
*  Si `T` a des contraintes *INTERFACE_TYPE* mais aucune contrainte *type_parameter* , son ensemble d’interfaces effectif est son ensemble de contraintes *INTERFACE_TYPE* .
*  Si `T` n’a pas de contraintes *INTERFACE_TYPE* mais a des contraintes *type_parameter* , son ensemble d’interfaces effectif est l’Union des ensembles d’interfaces effectifs de ses contraintes *type_parameter* .
*  Si `T` a des contraintes *INTERFACE_TYPE* et *type_parameter* , son ensemble d’interfaces effectif est l’Union de son ensemble de contraintes *INTERFACE_TYPE* et des ensembles d’interfaces effectifs de ses *type_parameter* contraintes.

Un paramètre de type est ***connu comme un type référence*** s’il a la contrainte de type référence ou sa classe de base effective `object` n' `System.ValueType`est pas ou.

Les valeurs d’un type de paramètre de type restreint peuvent être utilisées pour accéder aux membres de l’instance implicite par les contraintes. Dans l’exemple
```csharp
interface IPrintable
{
    void Print();
}

class Printer<T> where T: IPrintable
{
    void PrintOne(T x) {
        x.Print();
    }
}
```
les méthodes de `IPrintable` peuvent être appelées directement sur `x` , `T` car est restreinte pour toujours implémenter `IPrintable`.

### <a name="class-body"></a>Corps de la classe

Le *class_body* d’une classe définit les membres de cette classe.

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a>Types partiels

Une déclaration de type peut être fractionnée entre plusieurs ***déclarations de type partiel***. La déclaration de type est construite à partir de ses parties en suivant les règles de cette section, après quoi elle est traitée comme une déclaration unique pendant le reste du traitement au moment de la compilation et de l’exécution du programme.

Un *class_declaration*, *struct_declaration* ou *interface_declaration* représente une déclaration de type partiel s’il comprend un modificateur `partial`. `partial`n’est pas un mot clé et agit uniquement comme un modificateur s’il apparaît immédiatement avant l’un des `class`mots `struct` clés `interface` , ou dans une déclaration de type, ou `void` avant le type dans une déclaration de méthode. Dans d’autres contextes, il peut être utilisé comme un identificateur normal.

Chaque partie d’une déclaration de type partiel doit inclure `partial` un modificateur. Elle doit avoir le même nom et être déclarée dans le même espace de noms ou déclaration de type que les autres parties. Le `partial` modificateur indique que des parties supplémentaires de la déclaration de type peuvent exister ailleurs, mais que l’existence de ces parties supplémentaires n’est pas une exigence ; il est valide pour un type avec une déclaration `partial` unique pour inclure le modificateur.

Toutes les parties d’un type partiel doivent être compilées ensemble afin que les parties puissent être fusionnées au moment de la compilation dans une seule déclaration de type. En particulier, les types partiels n’autorisent pas l’extension des types déjà compilés.

Les types imbriqués peuvent être déclarés dans plusieurs parties à `partial` l’aide du modificateur. En règle générale, le type conteneur est `partial` déclaré à l’aide de également et chaque partie du type imbriqué est déclarée dans une autre partie du type conteneur.

Le `partial` modificateur n’est pas autorisé sur les déclarations de délégué ou d’énumération.

### <a name="attributes"></a>Attributs

Les attributs d’un type partiel sont déterminés en combinant, dans un ordre non spécifié, les attributs de chacune des parties. Si un attribut est placé sur plusieurs parties, il équivaut à spécifier l’attribut plusieurs fois sur le type. Par exemple, les deux parties :

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
équivalent à une déclaration telle que :
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

Les attributs sur les paramètres de type sont combinés de manière similaire.

### <a name="modifiers"></a>Modificateurs

Lorsqu’une déclaration de type partiel inclut une spécification d’accessibilité `public`(les `internal`modificateurs `private` ,, et), `protected`elle doit accepter toutes les autres parties qui incluent une spécification d’accessibilité. Si aucune partie d’un type partiel n’intègre une spécification d’accessibilité, le type reçoit l’accessibilité par défaut appropriée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Si une ou plusieurs déclarations partielles d’un type imbriqué incluent un `new` modificateur, aucun avertissement n’est signalé si le type imbriqué masque un membre hérité ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)).

Si une ou plusieurs déclarations partielles d’une classe incluent `abstract` un modificateur, la classe est considérée comme abstraite ([classes abstraites](classes.md#abstract-classes)). Dans le cas contraire, la classe est considérée comme non abstraite.

Si une ou plusieurs déclarations partielles d’une classe incluent `sealed` un modificateur, la classe est considérée comme sealed ([classes sealed](classes.md#sealed-classes)). Dans le cas contraire, la classe est considérée comme non scellée.

Notez qu’une classe ne peut pas être à la fois abstract et sealed.

Quand le `unsafe` modificateur est utilisé sur une déclaration de type partiel, seul ce composant particulier est considéré comme un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)).

### <a name="type-parameters-and-constraints"></a>Paramètres de type et contraintes

Si un type générique est déclaré dans plusieurs parties, chaque partie doit indiquer les paramètres de type. Chaque partie doit avoir le même nombre de paramètres de type et le même nom pour chaque paramètre de type, dans l’ordre.

Lorsqu’une déclaration de type générique partielle inclut des`where` contraintes (clauses), les contraintes doivent être conformes à toutes les autres parties qui incluent des contraintes. Plus précisément, chaque partie qui comprend des contraintes doit avoir des contraintes pour le même jeu de paramètres de type, et pour chaque paramètre de type, les ensembles de contraintes primaires, secondaires et de constructeurs doivent être équivalents. Deux ensembles de contraintes sont équivalents s’ils contiennent les mêmes membres. Si aucune partie d’un type générique partiel ne spécifie des contraintes de paramètre de type, les paramètres de type sont considérés comme non restreints.

L’exemple
```csharp
partial class Dictionary<K,V>
    where K: IComparable<K>
    where V: IKeyProvider<K>, IPersistable
{
    ...
}

partial class Dictionary<K,V>
    where V: IPersistable, IKeyProvider<K>
    where K: IComparable<K>
{
    ...
}

partial class Dictionary<K,V>
{
    ...
}
```
est correct, car les parties qui incluent des contraintes (les deux premières) spécifient effectivement le même jeu de contraintes primaires, secondaires et de constructeur pour le même jeu de paramètres de type, respectivement.

### <a name="base-class"></a>Classe de base

Lorsqu’une déclaration de classe partielle inclut une spécification de classe de base, elle doit accepter toutes les autres parties qui incluent une spécification de classe de base. Si aucune partie d’une classe partielle n’intègre une spécification de classe de base, la `System.Object` classe de base devient ([classes de base](classes.md#base-classes)).

### <a name="base-interfaces"></a>Interfaces de base

L’ensemble d’interfaces de base pour un type déclaré en plusieurs parties est l’Union des interfaces de base spécifiées sur chaque composant. Une interface de base particulière ne peut être nommée qu’une seule fois sur chaque partie, mais elle est autorisée pour plusieurs parties à nommer les mêmes interfaces de base. Il ne doit exister qu’une seule implémentation des membres d’une interface de base donnée.

Dans l’exemple
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
l’ensemble d’interfaces de base pour `C` la `IA`classe `IB`est, `IC`et.

En règle générale, chaque partie fournit une implémentation de l’interface déclarée sur cette partie ; Toutefois, cela n’est pas obligatoire. Un composant peut fournir l’implémentation d’une interface déclarée sur une autre partie :
```csharp
partial class X
{
    int IComparable.CompareTo(object o) {...}
}

partial class X: IComparable
{
    ...
}
```

### <a name="members"></a>Membres

À l’exception des méthodes partielles ([méthodes partielles](classes.md#partial-methods)), le jeu de membres d’un type déclaré en plusieurs parties est simplement l’Union de l’ensemble des membres déclarés dans chaque partie. Les corps de toutes les parties de la déclaration de type partagent le même espace de déclaration ([déclarations](basic-concepts.md#declarations)) et l’étendue de chaque membre ([portées](basic-concepts.md#scopes)) s’étend aux corps de toutes les parties. Le domaine d’accessibilité de n’importe quel membre comprend toujours toutes les parties du type englobant ; un `private` membre déclaré dans une partie est librement accessible à partir d’une autre partie. Il s’agit d’une erreur au moment de la compilation pour déclarer le même membre dans plusieurs parties du type, sauf si ce membre est un type `partial` avec le modificateur.

```csharp
partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int y;
    }
}

partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int z;
    }
}
```

L’ordonnancement des membres dans un type est rarement significatif C# pour le code, mais peut être significatif lors de l’interfaçage avec d’autres langages et environnements. Dans ce cas, l’ordre des membres dans un type déclaré dans plusieurs parties n’est pas défini.

### <a name="partial-methods"></a>Méthodes partielles

Les méthodes partielles peuvent être définies dans une partie d’une déclaration de type et implémentées dans une autre. L’implémentation est facultative ; Si aucune partie n’implémente la méthode partielle, la déclaration de méthode partielle et tous les appels à celle-ci sont supprimés de la déclaration de type résultant de la combinaison des parties.

Les méthodes partielles ne peuvent pas définir de modificateurs d' `private`accès, mais sont implicitement. Leur type de retour doit `void`être, et leurs paramètres ne peuvent `out` pas avoir le modificateur. L’identificateur `partial` est reconnu comme un mot clé spécial dans une déclaration de méthode uniquement s’il apparaît juste avant `void` le type ; sinon, il peut être utilisé comme un identificateur normal. Une méthode partielle ne peut pas implémenter explicitement des méthodes d’interface.

Il existe deux types de déclarations de méthode partielles : Si le corps de la déclaration de méthode est un point-virgule, la déclaration est dite « ***définition de déclaration de méthode partielle***». Si le corps est fourni en tant que *bloc*, la déclaration est considérée comme une ***déclaration de méthode partielle d’implémentation***. Dans les parties d’une déclaration de type, il ne peut y avoir qu’une seule déclaration de méthode partielle avec une signature donnée, et il ne peut y avoir qu’une seule déclaration de méthode partielle implémentée avec une signature donnée. Si une déclaration de méthode partielle d’implémentation est fournie, une déclaration de méthode partielle de définition correspondante doit exister et les déclarations doivent correspondre comme indiqué dans l’exemple suivant :

* Les déclarations doivent avoir les mêmes modificateurs (mais pas nécessairement dans le même ordre), le nom de la méthode, le nombre de paramètres de type et le nombre de paramètres.
* Les paramètres correspondants dans les déclarations doivent avoir les mêmes modificateurs (mais pas nécessairement dans le même ordre) et les mêmes types (différences modulo dans les noms de paramètres de type).
* Les paramètres de type correspondants dans les déclarations doivent avoir les mêmes contraintes (différences modulo dans les noms de paramètre de type).

Une déclaration de méthode partielle d’implémentation peut apparaître dans la même partie que la déclaration de méthode partielle de définition correspondante.

Seule une méthode partielle de définition participe à la résolution de surcharge. Par conséquent, qu’une déclaration d’implémentation soit donnée ou non, les expressions d’appel peuvent être résolues en appels de la méthode partielle. Comme une méthode partielle retourne `void`toujours, ces expressions d’appel sont toujours des instructions d’expression. En outre, étant donné qu’une méthode partielle `private`est implicitement, ces instructions sont toujours exécutées dans l’une des parties de la déclaration de type dans laquelle la méthode partielle est déclarée.

Si aucune partie d’une déclaration de type partiel ne contient une déclaration d’implémentation pour une méthode partielle donnée, toute instruction d’expression qui l’appelle est simplement supprimée de la déclaration de type combiné. Ainsi, l’expression d’appel, y compris toutes les expressions constitutives, n’a aucun effet au moment de l’exécution. La méthode partielle elle-même est également supprimée et ne sera pas membre de la déclaration de type combiné.

Si une déclaration d’implémentation existe pour une méthode partielle donnée, les appels des méthodes partielles sont conservés. La méthode partielle donne lieu à une déclaration de méthode similaire à la déclaration de méthode partielle d’implémentation, à l’exception des éléments suivants :

* Le `partial` modificateur n’est pas inclus
* Les attributs de la déclaration de méthode résultante sont les attributs combinés de la définition et de la déclaration de méthode partielle d’implémentation dans un ordre non spécifié. Les doublons ne sont pas supprimés.
* Les attributs sur les paramètres de la déclaration de méthode résultante sont les attributs combinés des paramètres correspondants de la définition et de la déclaration de méthode partielle d’implémentation dans un ordre non spécifié. Les doublons ne sont pas supprimés.

Si une déclaration de définition, mais pas une déclaration d’implémentation, est fournie pour une méthode partielle M, les restrictions suivantes s’appliquent :

* Il s’agit d’une erreur au moment de la compilation pour créer un délégué à une méthode ([expressions de création de délégué](expressions.md#delegate-creation-expressions)).
* Il s’agit d’une erreur de compilation qui se `M` réfère à l’intérieur d’une fonction anonyme convertie en un type d’arborescence d’expression ([évaluation de conversions de fonction anonyme en types d’arborescence d’expression](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).
* Les expressions qui se trouvent dans le cadre d' `M` un appel de n’affectent pas l’état d’assignation définie ([assignation définie](variables.md#definite-assignment)), ce qui peut entraîner des erreurs de compilation.
* `M`ne peut pas être le point d’entrée d’une application (démarrage de l'[application](basic-concepts.md#application-startup)).

Les méthodes partielles sont utiles pour permettre à une partie d’une déclaration de type de personnaliser le comportement d’une autre partie, par exemple, celle qui est générée par un outil. Considérons la déclaration de classe partielle suivante :
```csharp
partial class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    partial void OnNameChanging(string newName);

    partial void OnNameChanged();
}
```

Si cette classe est compilée sans autre partie, les déclarations de méthode partielle de définition et leurs appels seront supprimés, et la déclaration de classe combinée résultante sera équivalente à ce qui suit :
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set { name = value; }
    }
}
```

Supposons qu’une autre partie est fournie, toutefois, qui fournit des déclarations d’implémentation des méthodes partielles :
```csharp
partial class Customer
{
    partial void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    partial void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

La déclaration de classe combinée résultante est alors équivalente à ce qui suit :
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

### <a name="name-binding"></a>Liaison de nom

Bien que chaque partie d’un type extensible doive être déclarée dans le même espace de noms, les parties sont généralement écrites dans des déclarations d’espaces de noms différentes. Ainsi, différentes `using` directives ([directives using](namespaces.md#using-directives)) peuvent être présentes pour chaque partie. Lors de l’interprétation de noms simples ([inférence de type](expressions.md#type-inference)) dans une seule `using` partie, seules les directives de la ou des déclarations d’espace de noms englobant cette partie sont prises en compte. Cela peut entraîner un même identificateur ayant des significations différentes dans différentes parties :
```csharp
namespace N
{
    using List = System.Collections.ArrayList;

    partial class A
    {
        List x;                // x has type System.Collections.ArrayList
    }
}

namespace N
{
    using List = Widgets.LinkedList;

    partial class A
    {
        List y;                // y has type Widgets.LinkedList
    }
}
```

## <a name="class-members"></a>Membres de classe

Les membres d’une classe se composent des membres introduits par ses *class_member_declaration*s et des membres hérités de la classe de base directe.

```antlr
class_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | destructor_declaration
    | static_constructor_declaration
    | type_declaration
    ;
```

Les membres d’un type de classe sont répartis dans les catégories suivantes :

*  Les constantes, qui représentent des valeurs constantes associées à la classe ([constantes](classes.md#constants)).
*  Les champs, qui sont les variables de la classe ([champs](classes.md#fields)).
*  Les méthodes qui implémentent les calculs et les actions qui peuvent être effectuées par la classe ([méthodes](classes.md#methods)).
*  Propriétés, qui définissent les caractéristiques nommées et les actions associées à la lecture et à l’écriture de ces caractéristiques ([Propriétés](classes.md#properties)).
*  Les événements, qui définissent des notifications qui peuvent être générées par la classe ([événements](classes.md#events)).
*  Les indexeurs, qui autorisent l’indexation des instances de la classe de la même façon (syntaxiquement) en tant que tableaux ([indexeurs](classes.md#indexers)).
*  Les opérateurs, qui définissent les opérateurs d’expression qui peuvent être appliqués aux instances de la classe ([opérateurs](classes.md#operators)).
*  Constructeurs d’instance, qui implémentent les actions requises pour initialiser les instances de la classe ([constructeurs d’instance](classes.md#instance-constructors))
*  Les destructeurs, qui implémentent les actions à effectuer avant que les instances de la classe soient définitivement ignorées ([destructeurs](classes.md#destructors)).
*  Constructeurs statiques, qui implémentent les actions requises pour initialiser la classe elle-même ([constructeurs statiques](classes.md#static-constructors)).
*  Types, qui représentent les types qui sont locaux pour la classe ([types imbriqués](classes.md#nested-types)).

Les membres qui peuvent contenir du code exécutable sont collectivement appelés *membres de fonction* du type de classe. Les fonctions membres d’un type de classe sont les méthodes, les propriétés, les événements, les indexeurs, les opérateurs, les constructeurs d’instance, les destructeurs et les constructeurs statiques de ce type de classe.

Un *class_declaration* crée un nouvel espace de déclaration ([déclarations](basic-concepts.md#declarations)) et le *class_member_declaration*s contenu immédiatement dans le *class_declaration* introduit de nouveaux membres dans cet espace de déclaration. Les règles suivantes s’appliquent à *class_member_declaration*s :

*  Les constructeurs d’instance, les destructeurs et les constructeurs statiques doivent avoir le même nom que la classe immédiatement englobante. Tous les autres membres doivent avoir des noms qui diffèrent du nom de la classe englobante immédiatement.
*  Le nom d’une constante, d’un champ, d’une propriété, d’un événement ou d’un type doit différer des noms de tous les autres membres déclarés dans la même classe.
*  Le nom d’une méthode doit être différent des noms de toutes les autres méthodes non déclarées dans la même classe. En outre, la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir de signatures qui `ref` diffèrent uniquement par et `out`.
*  La signature d’un constructeur d’instance doit être différente des signatures de tous les autres constructeurs d’instance déclarés dans la même classe, et deux constructeurs déclarés dans la même classe ne peuvent pas avoir de `ref` signatures `out`qui diffèrent uniquement par et.
*  La signature d’un indexeur doit être différente des signatures de tous les autres indexeurs déclarés dans la même classe.
*  La signature d’un opérateur doit être différente des signatures de tous les autres opérateurs déclarés dans la même classe.

Les membres hérités d’un type de classe ([héritage](classes.md#inheritance)) ne font pas partie de l’espace de déclaration d’une classe. Par conséquent, une classe dérivée est autorisée à déclarer un membre avec le même nom ou la même signature qu’un membre hérité (qui, en effet, masque le membre hérité).

### <a name="the-instance-type"></a>Type d’instance

Chaque déclaration de classe a un type dépendant associé ([types liés et indépendants](types.md#bound-and-unbound-types)), le ***type d’instance***. Pour une déclaration de classe générique, le type d’instance est formé en créant un type construit ([types construits](types.md#constructed-types)) à partir de la déclaration de type, avec chacun des arguments de type fournis étant le paramètre de type correspondant. Étant donné que le type d’instance utilise les paramètres de type, il ne peut être utilisé que lorsque les paramètres de type sont dans la portée ; autrement dit, à l’intérieur de la déclaration de classe. Le type d’instance est le type `this` de pour le code écrit à l’intérieur de la déclaration de classe. Pour les classes non génériques, le type d’instance est simplement la classe déclarée. L’exemple suivant illustre plusieurs déclarations de classe avec leurs types d’instance : 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a>Membres des types construits

Les membres non hérités d’un type construit sont obtenus en substituant, pour chaque *type_parameter* dans la déclaration de membre, les *type_argument* correspondants du type construit. Le processus de substitution est basé sur la signification sémantique des déclarations de type et n’est pas simplement une substitution textuelle.

Par exemple, à partir de la déclaration de classe générique
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
le type `Gen<int[],IComparable<string>>` construit contient les membres suivants :
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

`a` Le type du membre dans la Déclaration `Gen` de classe générique est « tableau à deux dimensions de `T`». par conséquent, le type du `a` membre dans le type construit ci-dessus est «tableau à deux dimensions d’un tableau unidimensionnel de `int`"ou `int[,][]`.

Dans les membres de fonction d’instance, `this` le type de est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la déclaration conteneur.

Tous les membres d’une classe générique peuvent utiliser des paramètres de type de n’importe quelle classe englobante, directement ou dans le cadre d’un type construit. Lorsqu’un type construit fermé particulier ([types ouverts et fermés](types.md#open-and-closed-types)) est utilisé au moment de l’exécution, chaque utilisation d’un paramètre de type est remplacée par l’argument de type réel fourni au type construit. Exemple :
```csharp
class C<V>
{
    public V f1;
    public C<V> f2 = null;

    public C(V x) {
        this.f1 = x;
        this.f2 = this;
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>(1);
        Console.WriteLine(x1.f1);        // Prints 1

        C<double> x2 = new C<double>(3.1415);
        Console.WriteLine(x2.f1);        // Prints 3.1415
    }
}
```

### <a name="inheritance"></a>Héritage

Une classe ***hérite*** des membres de son type de classe de base directe. L’héritage signifie qu’une classe contient implicitement tous les membres de son type de classe de base directe, à l’exception des constructeurs d’instance, des destructeurs et des constructeurs statiques de la classe de base. Voici quelques aspects importants de l’héritage :

*  L’héritage est transitif. Si `C` est dérivé de `B`, et `B` est dérivé de `A`, `C` hérite des membres déclarés dans `B` , ainsi que des membres déclarés `A`dans.
*  Une classe dérivée étend sa classe de base directe. Une classe dérivée peut ajouter des membres hérités, mais ne peut pas supprimer la définition d’un membre hérité.
*  Les constructeurs d’instance, les destructeurs et les constructeurs statiques ne sont pas hérités, mais tous les autres membres sont, indépendamment de leur accessibilité déclarée ([accès aux membres](basic-concepts.md#member-access)). Toutefois, selon l’accessibilité déclarée, les membres hérités peuvent ne pas être accessibles dans une classe dérivée.
*  Une classe dérivée peut ***Masquer*** les membres hérités ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)) en déclarant de nouveaux membres portant le même nom ou la même signature. Notez cependant que le masquage d’un membre hérité ne supprime pas ce membre ; il rend simplement ce membre inaccessible directement via la classe dérivée.
*  Une instance d’une classe contient un ensemble de tous les champs d’instance déclarés dans la classe et ses classes de base, et une conversion implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe d’un type de classe dérivée vers l’un de ses types de classe de base. Ainsi, une référence à une instance d’une classe dérivée peut être traitée comme une référence à une instance de l’une de ses classes de base.
*  Une classe peut déclarer des méthodes, des propriétés et des indexeurs virtuels, et les classes dérivées peuvent substituer l’implémentation de ces membres de fonction. Cela permet aux classes d’exposer un comportement polymorphe dans lequel les actions effectuées par un appel de membre de fonction varient en fonction du type d’exécution de l’instance par le biais duquel cette fonction membre est appelée.

Le membre hérité d’un type de classe construit sont les membres du type de classe de base immédiat ([classes de base](classes.md#base-classes)), qui est trouvé en substituant les arguments de type du type construit pour chaque occurrence des paramètres de type correspondants dans le  *spécification class_base* . Ces membres, à leur tour, sont transformés en substituant, pour chaque *type_parameter* dans la déclaration de membre, les *type_argument* correspondants de la spécification *class_base* .

```csharp
class B<U>
{
    public U F(long index) {...}
}

class D<T>: B<T[]>
{
    public T G(string s) {...}
}
```

Dans l’exemple ci-dessus, le `D<int>` type construit a un membre `public int G(string s)` non hérité obtenu en substituant l’argument `int` de type pour le `T`paramètre de type. `D<int>`a également un membre hérité de la Déclaration `B`de classe. Ce membre hérité est déterminé par la première `B<int[]>` détermination du type de classe de base de `int` `D<int>` en `T` remplaçant dans la spécification `B<T[]>`de la classe de base. Ensuite, en tant qu’argument de `B`type `int[]` pour, est substitué `U` à `public U F(long index)`dans, ce qui donne le `public int[] F(long index)`membre hérité.

### <a name="the-new-modifier"></a>Le nouveau modificateur

Un *class_member_declaration* est autorisé à déclarer un membre avec le même nom ou la même signature qu’un membre hérité. Dans ce cas, le membre de la classe dérivée est dit de ***Masquer*** le membre de la classe de base. Le masquage d’un membre hérité n’est pas considéré comme une erreur, mais il entraîne l’émission d’un avertissement par le compilateur. Pour supprimer l’avertissement, la déclaration du membre de classe dérivée peut inclure `new` un modificateur pour indiquer que le membre dérivé est destiné à masquer le membre de base. Cette rubrique est traitée plus en détail dans [Masquer par héritage](basic-concepts.md#hiding-through-inheritance).

Si un `new` modificateur est inclus dans une déclaration qui ne masque pas un membre hérité, un avertissement est émis à cet effet. Cet avertissement est supprimé en supprimant le `new` modificateur.

### <a name="access-modifiers"></a>Modificateurs d’accès

Un *class_member_declaration* peut avoir l’un des cinq types possibles d’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) : `public`, `protected internal`, `protected`, `internal` ou `private`. À l’exception `protected internal` de la combinaison, il s’agit d’une erreur de compilation pour spécifier plusieurs modificateurs d’accès. Quand un *class_member_declaration* n’inclut pas de modificateur d’accès, `private` est utilisé.

### <a name="constituent-types"></a>Types constitutifs

Les types qui sont utilisés dans la déclaration d’un membre sont appelés types constitutifs de ce membre. Les types constitutifs possibles sont le type d’une constante, d’un champ, d’une propriété, d’un événement ou d’un indexeur, le type de retour d’une méthode ou d’un opérateur, ainsi que les types de paramètres d’une méthode, d’un indexeur, d’un opérateur ou d’un constructeur d’instance. Les types constitutifs d’un membre doivent être au moins aussi accessibles que ce membre lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

### <a name="static-and-instance-members"></a>Membres statiques et d’instance

Les membres d’une classe sont soit des ***membres statiques*** , soit des ***membres d’instance***. En général, il est utile de considérer que les membres statiques appartiennent à des types de classe et des membres d’instance comme appartenant à des objets (instances de types de classe).

Lorsqu’une déclaration de champ, de méthode, de propriété, d’événement, d’opérateur `static` ou de constructeur comprend un modificateur, elle déclare un membre statique. En outre, une déclaration de constante ou de type déclare implicitement un membre statique. Les membres statiques présentent les caractéristiques suivantes :

*  Lorsqu’un membre statique `M` est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, `E` doit désigner un type contenant `M`. Il s’agit d’une erreur au moment `E` de la compilation pour qu’une instance soit dénotée.
*  Un champ statique identifie exactement un emplacement de stockage à partager par toutes les instances d’un type de classe fermé donné. Quel que soit le nombre d’instances d’un type de classe fermé donné, il n’existe qu’une seule copie d’un champ statique.
*  Un membre de fonction statique (méthode, propriété, événement, opérateur ou constructeur) ne fonctionne pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire `this` référence à dans ce type de membre de fonction.

Quand un champ, une méthode, une propriété, un événement, un indexeur, un constructeur ou une déclaration de `static` destructeur n’inclut pas de modificateur, il déclare un membre d’instance. (Un membre d’instance est parfois appelé membre non statique.) Les membres d’instance présentent les caractéristiques suivantes :

*  Lorsqu’un membre d’instance `M` est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, `E` doit désigner une instance d’un type contenant `M`. Il s’agit d’une erreur de liaison `E` au moment de la liaison pour indiquer un type.
*  Chaque instance d’une classe contient un ensemble distinct de tous les champs d’instance de la classe.
*  Un membre de fonction d’instance (méthode, propriété, indexeur, constructeur d’instance ou destructeur) opère sur une instance donnée de la classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)).

L’exemple suivant illustre les règles d’accès aux membres statiques et d’instance :
```csharp
class Test
{
    int x;
    static int y;

    void F() {
        x = 1;            // Ok, same as this.x = 1
        y = 1;            // Ok, same as Test.y = 1
    }

    static void G() {
        x = 1;            // Error, cannot access this.x
        y = 1;            // Ok, same as Test.y = 1
    }

    static void Main() {
        Test t = new Test();
        t.x = 1;          // Ok
        t.y = 1;          // Error, cannot access static member through instance
        Test.x = 1;       // Error, cannot access instance member through type
        Test.y = 1;       // Ok
    }
}
```

La méthode `F` montre que dans une fonction membre d’instance, un *simple_name* ([noms simples](expressions.md#simple-names)) peut être utilisé pour accéder aux membres d’instance et aux membres statiques. La méthode `G` montre que dans une fonction membre statique, il s’agit d’une erreur de compilation pour accéder à un membre d’instance via un *simple_name*. La méthode `Main` indique que dans un *member_access* ([accès aux membres](expressions.md#member-access)), les membres d’instance doivent être accessibles via des instances et les membres statiques doivent être accessibles par le biais de types.

### <a name="nested-types"></a>Types imbriqués

Un type déclaré dans une déclaration de classe ou de struct est appelé un ***type imbriqué***. Un type déclaré dans une unité de compilation ou un espace de noms est appelé ***type non imbriqué***.

Dans l’exemple
```csharp
using System;

class A
{
    class B
    {
        static void F() {
            Console.WriteLine("A.B.F");
        }
    }
}
```
la `B` classe est un type imbriqué, car elle est déclarée `A`dans la classe `A` , et la classe est un type non imbriqué, car elle est déclarée dans une unité de compilation.

#### <a name="fully-qualified-name"></a>Nom complet

Le nom qualifié complet ([noms qualifiés complets](basic-concepts.md#fully-qualified-names)) pour un type imbriqué est `S.N` où `S` est le nom qualifié complet du type dans lequel le type `N` est déclaré.

#### <a name="declared-accessibility"></a>Accessibilité déclarée

Les types non imbriqués peuvent avoir `public` ou `internal` déclarés l’accessibilité `internal` et avoir déclaré l’accessibilité par défaut. Les types imbriqués peuvent également avoir ces formes d’accessibilité déclarée, plus une ou plusieurs formes supplémentaires d’accessibilité déclarée, selon que le type conteneur est une classe ou un struct :

*  Un type imbriqué qui est déclaré dans une classe peut avoir l’une des cinq formes d’accessibilité déclarée`public`( `protected internal`, `protected`, `internal`, ou `private`) et, comme les autres membres de classe, la `private` valeur par défaut est déclarée aux.
*  Un type imbriqué qui est déclaré dans un struct peut avoir l’une des trois formes d’accessibilité déclarée`public`( `internal`, ou `private`) et, `private` comme les autres membres de struct, a comme valeur par défaut l’accessibilité déclarée.

L’exemple
```csharp
public class List
{
    // Private data structure
    private class Node
    { 
        public object Data;
        public Node Next;

        public Node(object data, Node next) {
            this.Data = data;
            this.Next = next;
        }
    }

    private Node first = null;
    private Node last = null;

    // Public interface
    public void AddToFront(object o) {...}
    public void AddToBack(object o) {...}
    public object RemoveFromFront() {...}
    public object RemoveFromBack() {...}
    public int Count { get {...} }
}
```
déclare une classe `Node`imbriquée privée.

#### <a name="hiding"></a>Masquage

Un type imbriqué peut masquer ([nom masquant](basic-concepts.md#name-hiding)) un membre de base. Le `new` modificateur est autorisé sur les déclarations de type imbriqué afin que le masquage puisse être exprimé explicitement. L’exemple
```csharp
using System;

class Base
{
    public static void M() {
        Console.WriteLine("Base.M");
    }
}

class Derived: Base 
{
    new public class M 
    {
        public static void F() {
            Console.WriteLine("Derived.M.F");
        }
    }
}

class Test 
{
    static void Main() {
        Derived.M.F();
    }
}
```
affiche une classe `M` imbriquée qui masque la méthode `M` définie dans `Base`.

#### <a name="this-access"></a>Cet accès

Un type imbriqué et son type conteneur n’ont pas de relation spéciale en ce qui concerne *This_Access* ([cet accès](expressions.md#this-access)). En particulier `this` , dans un type imbriqué ne peut pas être utilisé pour faire référence aux membres d’instance du type conteneur. Dans les cas où un type imbriqué doit accéder aux membres de l’instance de son type conteneur, l’accès peut être fourni en `this` fournissant le pour l’instance du type conteneur comme argument de constructeur pour le type imbriqué. L’exemple suivant
```csharp
using System;

class C
{
    int i = 123;

    public void F() {
        Nested n = new Nested(this);
        n.G();
    }

    public class Nested
    {
        C this_c;

        public Nested(C c) {
            this_c = c;
        }

        public void G() {
            Console.WriteLine(this_c.i);
        }
    }
}

class Test
{
    static void Main() {
        C c = new C();
        c.F();
    }
}
```
illustre cette technique. Une instance de `C` crée une instance de `Nested` et passe son propre `this` au `Nested`constructeur de pour fournir l’accès ultérieur aux `C`membres d’instance de.

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a>Accès aux membres privés et protégés du type conteneur

Un type imbriqué a accès à tous les membres qui sont accessibles à son type conteneur, y compris les membres du type conteneur qui ont `private` et `protected` l’accessibilité déclarée. L’exemple
```csharp
using System;

class C 
{
    private static void F() {
        Console.WriteLine("C.F");
    }

    public class Nested 
    {
        public static void G() {
            F();
        }
    }
}

class Test 
{
    static void Main() {
        C.Nested.G();
    }
}
```
affiche une classe `C` qui contient une classe `Nested`imbriquée. Dans `Nested`, la méthode `G` appelle la méthode `F` statique définie dans `C`et `F` a une accessibilité déclarée privée.

Un type imbriqué peut également accéder à des membres protégés définis dans un type de base de son type conteneur. Dans l’exemple
```csharp
using System;

class Base 
{
    protected void F() {
        Console.WriteLine("Base.F");
    }
}

class Derived: Base 
{
    public class Nested 
    {
        public void G() {
            Derived d = new Derived();
            d.F();        // ok
        }
    }
}

class Test 
{
    static void Main() {
        Derived.Nested n = new Derived.Nested();
        n.G();
    }
}
```
la `Derived.Nested` classe imbriquée accède à la méthode `Derived` `F` protégée définie `Base`dans la classe de base de,, en appelant par le `Derived`biais d’une instance de.

#### <a name="nested-types-in-generic-classes"></a>Types imbriqués dans les classes génériques

Une déclaration de classe générique peut contenir des déclarations de type imbriqué. Les paramètres de type de la classe englobante peuvent être utilisés dans les types imbriqués. Une déclaration de type imbriquée peut contenir des paramètres de type supplémentaires qui s’appliquent uniquement au type imbriqué.

Chaque déclaration de type contenue dans une déclaration de classe générique est implicitement une déclaration de type générique. Lors de l’écriture d’une référence à un type imbriqué dans un type générique, le type construit contenant, y compris ses arguments de type, doit être nommé. Toutefois, à partir de la classe externe, le type imbriqué peut être utilisé sans qualification ; le type d’instance de la classe externe peut être utilisé implicitement lors de la construction du type imbriqué. L’exemple suivant montre trois façons correctes de faire référence à un type construit créé `Inner`à partir de ; les deux premiers sont équivalents :
```csharp
class Outer<T>
{
    class Inner<U>
    {
        public static void F(T t, U u) {...}
    }

    static void F(T t) {
        Outer<T>.Inner<string>.F(t, "abc");      // These two statements have
        Inner<string>.F(t, "abc");               // the same effect

        Outer<int>.Inner<string>.F(3, "abc");    // This type is different

        Outer.Inner<string>.F(t, "abc");         // Error, Outer needs type arg
    }
}
```

Bien qu’il s’agisse d’un style de programmation incorrect, un paramètre de type dans un type imbriqué peut masquer un membre ou un paramètre de type déclaré dans le type externe :
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a>Noms de membres réservés

Pour faciliter l’implémentation C# sous-jacente au moment de l’exécution, pour chaque déclaration de membre source qui est une propriété, un événement ou un indexeur, l’implémentation doit réserver deux signatures de méthode selon le type de la déclaration de membre, son nom et son type. Il s’agit d’une erreur de compilation pour qu’un programme déclare un membre dont la signature correspond à l’une de ces signatures réservées, même si l’implémentation au moment de l’exécution sous-jacente n’utilise pas ces réservations.

Les noms réservés n’introduisent pas de déclarations, donc ils ne participent pas à la recherche de membres. Toutefois, les signatures de méthode réservée associées à une déclaration participent à l’héritage ([héritage](classes.md#inheritance)) et peuvent être masquées avec le `new` modificateur ([le nouveau modificateur](classes.md#the-new-modifier)).

La réservation de ces noms remplit trois fonctions :

*  Pour permettre à l’implémentation sous-jacente d’utiliser un identificateur ordinaire comme nom de méthode pour obtenir ou définir l' C# accès à la fonctionnalité de langage.
*  Pour permettre à d’autres langages d’interagir à l’aide d’un identificateur ordinaire comme nom de méthode pour obtenir ou C# définir l’accès à la fonctionnalité de langage.
*  Pour garantir que la source acceptée par un compilateur conforme est acceptée par un autre, en rendant les détails des noms de membres réservés cohérents dans toutes les C# implémentations.

La déclaration d’un destructeur ([destructeurs](classes.md#destructors)) entraîne également la réservation d’une signature ([noms de membres réservés aux destructeurs](classes.md#member-names-reserved-for-destructors)).

#### <a name="member-names-reserved-for-properties"></a>Noms de membres réservés pour les propriétés

Pour une propriété `P` ([Propriétés](classes.md#properties)) de type `T`, les signatures suivantes sont réservées :

```csharp
T get_P();
void set_P(T value);
```

Les deux signatures sont réservées, même si la propriété est en lecture seule ou en écriture seule.

Dans l’exemple
```csharp
using System;

class A
{
    public int P {
        get { return 123; }
    }
}

class B: A
{
    new public int get_P() {
        return 456;
    }

    new public void set_P(int value) {
    }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        Console.WriteLine(a.P);
        Console.WriteLine(b.P);
        Console.WriteLine(b.get_P());
    }
}
```
une classe `A` définit une propriété `P`en lecture seule, en réservant des signatures `get_P` pour `set_P` les méthodes et. Une classe `B` dérive `A` de et masque ces deux signatures réservées. L’exemple produit la sortie :
```console
123
123
456
```

#### <a name="member-names-reserved-for-events"></a>Noms de membres réservés pour les événements

Pour un événement `E` ([événements](classes.md#events)) de type `T`délégué, les signatures suivantes sont réservées :
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a>Noms de membres réservés pour les indexeurs

Pour un indexeur ([indexeurs](classes.md#indexers)) de type `T` avec Parameter-List `L`, les signatures suivantes sont réservées :
```csharp
T get_Item(L);
void set_Item(L, T value);
```

Les deux signatures sont réservées, même si l’indexeur est en lecture seule ou en écriture seule.

En outre, le `Item` nom du membre est réservé.

#### <a name="member-names-reserved-for-destructors"></a>Noms de membres réservés pour les destructeurs

Pour une classe contenant un destructeur ([destructeurs](classes.md#destructors)), la signature suivante est réservée :
```csharp
void Finalize();
```

## <a name="constants"></a>Constantes

Une ***constante*** est un membre de classe qui représente une valeur de constante : valeur qui peut être calculée au moment de la compilation. Un *constant_declaration* introduit une ou plusieurs constantes d’un type donné.

```antlr
constant_declaration
    : attributes? constant_modifier* 'const' type constant_declarators ';'
    ;

constant_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

Un *constant_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), un modificateur `new` ([le nouveau modificateur](classes.md#the-new-modifier)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)). Les attributs et les modificateurs s’appliquent à tous les membres déclarés par *constant_declaration*. Bien que les constantes soient considérées comme des membres statiques, un *constant_declaration* ne requiert pas et n’autorise pas un modificateur `static`. Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de constante.

Le *type* d’un *constant_declaration* spécifie le type des membres introduits par la déclaration. Le type est suivi d’une liste de *constant_declarator*s, chacun d’entre eux introduisant un nouveau membre. Un *constant_declarator* se compose d’un *identificateur* qui nomme le membre, suivi d’un jeton « `=` », suivi d’un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) qui donne la valeur du membre.

Le *type* spécifié dans une déclaration de constante doit être `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3, 4, *enum_type*ou *reference_ tapez*. Chaque *constant_expression* doit produire une valeur du type cible ou d’un type qui peut être converti vers le type cible par une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).

Le *type* d’une constante doit être au moins aussi accessible que la constante elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

La valeur d’une constante est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)).

Une constante peut elle-même participer à un *constant_expression*. Par conséquent, une constante peut être utilisée dans n’importe quelle construction qui requiert un *constant_expression*. Les exemples de ces constructions incluent `case` les étiquettes `goto case` , les `enum` instructions, les déclarations de membre, les attributs et d’autres déclarations de constante.

Comme décrit dans [expressions constantes](expressions.md#constant-expressions), un *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation. Étant donné que la seule façon de créer une valeur non null d’un *reference_type* autre que `string` consiste à appliquer l’opérateur `new`, et puisque l’opérateur `new` n’est pas autorisé dans un *constant_expression*, la seule valeur possible pour les constantes de  *les reference_types*autres que `string` sont `null`.

Lorsqu’un nom symbolique pour une valeur constante est souhaité, mais lorsque le type de cette valeur n’est pas autorisé dans une déclaration de constante, ou lorsque la valeur ne peut pas être calculée au moment de la compilation par un *constant_expression*, un champ `readonly` ([champs en lecture seule](classes.md#readonly-fields)) peut à utiliser à la place.

Une déclaration de constante qui déclare plusieurs constantes équivaut à plusieurs déclarations de constantes uniques avec les mêmes attributs, modificateurs et type. Exemple :
```csharp
class A
{
    public const double X = 1.0, Y = 2.0, Z = 3.0;
}
```
est équivalent à
```csharp
class A
{
    public const double X = 1.0;
    public const double Y = 2.0;
    public const double Z = 3.0;
}
```

Les constantes sont autorisées à dépendre d’autres constantes au sein du même programme tant que les dépendances ne sont pas de nature circulaire. Le compilateur s’arrange automatiquement pour évaluer les déclarations de constantes dans l’ordre approprié. Dans l’exemple
```csharp
class A
{
    public const int X = B.Z + 1;
    public const int Y = 10;
}

class B
{
    public const int Z = A.Y + 1;
}
```
le compilateur `A.Y`évalue, puis `B.Z`évalue et évalue `A.X`Enfin, en produisant les valeurs `10`, `11`et `12`. Les déclarations de constante peuvent dépendre de constantes d’autres programmes, mais ces dépendances ne sont possibles que dans une seule direction. En `A` `B` `B.Z` `B.Z` vousréférantàl'`A.Y`exemple ci-dessus, si et ont été déclarés dans des programmes distincts, il serait possible quedépendede,maisnedépendaitpassimultanémentde.`A.X`

## <a name="fields"></a>Champs

Un ***champ*** est un membre qui représente une variable associée à un objet ou une classe. Un *field_declaration* introduit un ou plusieurs champs d’un type donné.

```antlr
field_declaration
    : attributes? field_modifier* type variable_declarators ';'
    ;

field_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'readonly'
    | 'volatile'
    | field_modifier_unsafe
    ;

variable_declarators
    : variable_declarator (',' variable_declarator)*
    ;

variable_declarator
    : identifier ('=' variable_initializer)?
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

Un *field_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), un modificateur `new` ([le nouveau modificateur](classes.md#the-new-modifier)), une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) et un modificateur `static` ([ Champs d’instance et statiques](classes.md#static-and-instance-fields)). En outre, un *field_declaration* peut inclure un modificateur `readonly` ([champs ReadOnly](classes.md#readonly-fields)) ou un modificateur `volatile` ([champs volatiles](classes.md#volatile-fields)), mais pas les deux. Les attributs et les modificateurs s’appliquent à tous les membres déclarés par *field_declaration*. Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de champ.

Le *type* d’un *field_declaration* spécifie le type des membres introduits par la déclaration. Le type est suivi d’une liste de *variable_declarator*s, chacun d’entre eux introduisant un nouveau membre. Un *variable_declarator* se compose d’un *identificateur* qui nomme ce membre, éventuellement suivi d’un jeton « `=` » et d’un *variable_initializer* ([initialiseurs de variable](classes.md#variable-initializers)) qui donne la valeur initiale de ce membre.

Le *type* d’un champ doit être au moins aussi accessible que le champ lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

La valeur d’un champ est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)). La valeur d’un champ qui n’est pas en lecture seule est modifiée à l’aide d’une *assignation* ([opérateurs d’affectation](expressions.md#assignment-operators)). La valeur d’un champ non readonly peut être obtenue et modifiée à l’aide des opérateurs suffixés d’incrémentation et de décrémentation ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators)) et des opérateurs de préfixe et d’incrémentation ([incrément et décrémentation de préfixe) opérateurs](expressions.md#prefix-increment-and-decrement-operators)).

Une déclaration de champ qui déclare plusieurs champs équivaut à plusieurs déclarations de champs uniques avec les mêmes attributs, modificateurs et type. Exemple :
```csharp
class A
{
    public static int X = 1, Y, Z = 100;
}
```
est équivalent à
```csharp
class A
{
    public static int X = 1;
    public static int Y;
    public static int Z = 100;
}
```

### <a name="static-and-instance-fields"></a>Champs d’instance et statiques

Lorsqu’une déclaration de champ comprend `static` un modificateur, les champs introduits par la déclaration sont des ***champs statiques***. Si aucun `static` modificateur n’est présent, les champs introduits par la déclaration sont des ***champs d’instance***. Les champs statiques et les champs d’instance sont deux des différents types[de variables](variables.md) (variables C#) pris en charge par, et parfois appelés ***variables statiques*** et ***variables d’instance***, respectivement.

Un champ statique ne fait pas partie d’une instance spécifique ; au lieu de cela, il est partagé entre toutes les instances d’un type fermé ([types ouverts et fermés](types.md#open-and-closed-types)). Quel que soit le nombre d’instances d’un type de classe fermé, il n’existe qu’une seule copie d’un champ statique pour le domaine d’application associé.

Exemple :
```csharp
class C<V>
{
    static int count = 0;

    public C() {
        count++;
    }

    public static int Count {
        get { return count; }
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<double> x2 = new C<double>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<int> x3 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 2
    }
}
```

Un champ d’instance appartient à une instance. Plus précisément, chaque instance d’une classe contient un ensemble distinct de tous les champs d’instance de cette classe.

Lorsqu’un champ est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est un champ statique, `E` doit désigner un type contenant `M`, et si `M` est un champ d’instance, E doit désigner une instance d’un type contenant `M`.

Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="readonly-fields"></a>Champs en lecture seule

Lorsqu’un *field_declaration* comprend un modificateur `readonly`, les champs introduits par la déclaration sont des ***champs en lecture seule***. Les assignations directes aux champs ReadOnly ne peuvent se produire que dans le cadre de cette déclaration ou dans un constructeur d’instance ou un constructeur statique dans la même classe. (Un champ readonly peut être affecté à plusieurs fois dans ces contextes.) Plus précisément, les assignations directes à un `readonly` champ sont autorisées uniquement dans les contextes suivants :

*  Dans le *variable_declarator* qui introduit le champ (en incluant un *variable_initializer* dans la déclaration).
*  Pour un champ d’instance, dans les constructeurs d’instance de la classe qui contient la déclaration de champ ; pour un champ statique, dans le constructeur statique de la classe qui contient la déclaration de champ. Il s’agit également des seuls contextes dans lesquels il est possible de passer `readonly` un champ `out` en tant `ref` que paramètre ou.

Toute tentative d’assignation à `readonly` un champ ou de transmission `out` en tant `ref` que paramètre ou dans un autre contexte est une erreur au moment de la compilation.

#### <a name="using-static-readonly-fields-for-constants"></a>Utilisation de champs ReadOnly statiques pour les constantes

Un `static readonly` champ est utile lorsqu’un nom symbolique pour une valeur constante est souhaité, mais lorsque le type de la valeur n’est pas autorisé dans `const` une déclaration, ou lorsque la valeur ne peut pas être calculée au moment de la compilation. Dans l’exemple
```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);

    private byte red, green, blue;

    public Color(byte r, byte g, byte b) {
        red = r;
        green = g;
        blue = b;
    }
}
```
les `Black`membres `White`,, `Red`, `const` et ne`Blue` peuvent pas être déclarés comme membres, car leurs valeurs ne peuvent pas être calculées au moment de la compilation. `Green` Toutefois, leur `static readonly` déclaration à la place a un effet très similaire.

#### <a name="versioning-of-constants-and-static-readonly-fields"></a>Contrôle de version des constantes et des champs ReadOnly statiques

Les constantes et les champs ReadOnly ont une sémantique de version binaire différente. Lorsqu’une expression fait référence à une constante, la valeur de la constante est obtenue au moment de la compilation, mais lorsqu’une expression fait référence à un champ ReadOnly, la valeur du champ n’est obtenue qu’au moment de l’exécution. Prenons l’exemple d’une application qui se compose de deux programmes distincts :
```csharp
using System;

namespace Program1
{
    public class Utils
    {
        public static readonly int X = 1;
    }
}

namespace Program2
{
    class Test
    {
        static void Main() {
            Console.WriteLine(Program1.Utils.X);
        }
    }
}
```

Les `Program1` espaces `Program2` de noms et désignent deux programmes qui sont compilés séparément. Étant `Program1.Utils.X` donné que est déclaré en tant que champ readonly statique, la valeur `Console.WriteLine` générée par l’instruction n’est pas connue au moment de la compilation, mais est plutôt obtenue au moment de l’exécution. Ainsi, si la valeur de `X` est modifiée et `Program1` est recompilée, `Console.WriteLine` l’instruction génère la nouvelle valeur même si `Program2` n’est pas recompilé. Toutefois, avait `X` été une constante, la valeur de `X` aurait été obtenue au moment `Program2` de la compilation `Program1` et ne serait pas affectée par les modifications apportées `Program2` à jusqu’à la recompilation.

### <a name="volatile-fields"></a>Champs volatiles

Quand un *field_declaration* comprend un modificateur `volatile`, les champs introduits par cette déclaration sont des ***champs volatiles***.

Pour les champs non volatiles, les techniques d’optimisation qui réorganisent les instructions peuvent entraîner des résultats inattendus et imprévisibles dans les programmes multithread qui accèdent aux champs sans synchronisation, comme celui fourni par *lock_statement* ([le instruction lock](statements.md#the-lock-statement)). Ces optimisations peuvent être effectuées par le compilateur, par le système d’exécution ou par le matériel. Pour les champs volatils, ces optimisations de réorganisation sont restreintes :

*  La lecture d’un champ volatile est appelée une ***lecture volatile***. Une lecture volatile a une « sémantique d’acquisition »; autrement dit, il est garanti qu’il se produise avant toute référence à la mémoire qui se trouve après dans la séquence d’instructions.
*  L’écriture d’un champ volatile est appelée une ***écriture volatile***. Une écriture volatile a une « sémantique de version »; autrement dit, il est garanti qu’il se produira après toute référence de mémoire avant l’instruction d’écriture dans la séquence d’instructions.

Ces restrictions garantissent que tous les threads observent les écritures volatiles effectuées par un autre thread dans l’ordre dans lequel elles ont été effectuées. Une implémentation conforme n’est pas requise pour fournir un classement total unique des écritures volatiles, comme vu par tous les threads d’exécution. Le type d’un champ volatile doit être l’un des suivants :

*  *Reference_type*.
*  `byte`Type ,`char` ,,`System.UIntPtr`,,,, ,`System.IntPtr`, ou. `bool` `float` `sbyte` `short` `ushort` `int` `uint`
*  *Enum_type* dont le type de base enum est `byte`, `sbyte`, `short`, `ushort`, `int` ou `uint`.

L’exemple
```csharp
using System;
using System.Threading;

class Test
{
    public static int result;   
    public static volatile bool finished;

    static void Thread2() {
        result = 143;    
        finished = true; 
    }

    static void Main() {
        finished = false;

        // Run Thread2() in a new thread
        new Thread(new ThreadStart(Thread2)).Start();

        // Wait for Thread2 to signal that it has a result by setting
        // finished to true.
        for (;;) {
            if (finished) {
                Console.WriteLine("result = {0}", result);
                return;
            }
        }
    }
}
```
génère cette sortie :
```console
result = 143
```

Dans cet exemple, la méthode `Main` démarre un nouveau thread qui exécute la méthode `Thread2`. Cette méthode stocke une valeur dans un champ non volatile appelé `result`, puis stocke `true` dans le champ `finished`volatile. Le thread principal attend que le champ `finished` soit défini sur `true`, puis lit le champ `result`. Étant `finished` donné que a `volatile`été déclaré, le thread principal doit lire `143` la valeur du `result`champ. Si le champ `finished` n’a pas été `volatile`déclaré, il est possible que le magasin `result` soit visible par le thread principal après le stockage `finished`, et par conséquent, pour que le thread principal Lise la valeur `0` à partir de la champ `result`. La Déclaration `finished` `volatile` en tant que champ empêche toute incohérence.

### <a name="field-initialization"></a>Initialisation de champ

La valeur initiale d’un champ, qu’il s’agisse d’un champ statique ou d’un champ d’instance, est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type du champ. Il n’est pas possible d’observer la valeur d’un champ avant l’initialisation par défaut, et un champ n’est donc jamais « non initialisé ». L’exemple
```csharp
using System;

class Test
{
    static bool b;
    int i;

    static void Main() {
        Test t = new Test();
        Console.WriteLine("b = {0}, i = {1}", b, t.i);
    }
}
```
génère la sortie
```console
b = False, i = 0
```
étant `b` donné `i` que et sont initialisés automatiquement avec les valeurs par défaut.

### <a name="variable-initializers"></a>Initialiseurs de variables

Les déclarations de champ peuvent inclure des *variable_initializer*s. Pour les champs statiques, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées pendant l’initialisation de la classe. Pour les champs d’instance, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées lors de la création d’une instance de la classe.

L’exemple
```csharp
using System;

class Test
{
    static double x = Math.Sqrt(2.0);
    int i = 100;
    string s = "Hello";

    static void Main() {
        Test a = new Test();
        Console.WriteLine("x = {0}, i = {1}, s = {2}", x, a.i, a.s);
    }
}
```
génère la sortie
```console
x = 1.4142135623731, i = 100, s = Hello
```
parce qu’une assignation `x` à se produit lorsque des initialiseurs de champs statiques `s` exécutent des assignations à et se produisent lorsque les initialiseurs de champ d' `i` instance s’exécutent.

L’initialisation de la valeur par défaut décrite dans [initialisation de champ](classes.md#field-initialization) se produit pour tous les champs, y compris les champs qui ont des initialiseurs de variable. Ainsi, lorsqu’une classe est initialisée, tous les champs statiques de cette classe sont d’abord initialisés à leurs valeurs par défaut, puis les initialiseurs de champs statiques sont exécutés dans l’ordre textuel. De même, lorsqu’une instance d’une classe est créée, tous les champs d’instance dans cette instance sont d’abord initialisés à leurs valeurs par défaut, puis les initialiseurs de champ d’instance sont exécutés dans l’ordre textuel.

Il est possible que les champs statiques avec des initialiseurs de variable soient observés dans leur état de valeur par défaut. Toutefois, cela est fortement déconseillé en tant que question de style. L’exemple
```csharp
using System;

class Test
{
    static int a = b + 1;
    static int b = a + 1;

    static void Main() {
        Console.WriteLine("a = {0}, b = {1}", a, b);
    }
}
```
présente ce comportement. Malgré les définitions circulaires de a et b, le programme est valide. Elle génère la sortie
```console
a = 1, b = 2
```
étant donné que les `a` champs `b` statiques et sont `0` initialisés à (valeur `int`par défaut pour) avant l’exécution de leurs initialiseurs. Lorsque l’initialiseur pour `a` s’exécute, la valeur `b` de est égale à zéro `a` , et par conséquent `1`, est initialisé à. Lorsque l’initialiseur pour `b` s’exécute, la valeur `a` de est `1`déjà, et `b` par conséquent, est `2`initialisé à.

#### <a name="static-field-initialization"></a>Initialisation de champ statique

Les initialiseurs de variable de champ statique d’une classe correspondent à une séquence d’assignations qui sont exécutées dans l’ordre textuel dans lequel elles apparaissent dans la déclaration de classe. Si un constructeur statique ([constructeurs statiques](classes.md#static-constructors)) existe dans la classe, l’exécution des initialiseurs de champ statiques se produit immédiatement avant l’exécution de ce constructeur statique. Sinon, les initialiseurs de champs statiques sont exécutés à un moment dépendant de l’implémentation avant la première utilisation d’un champ statique de cette classe. L’exemple
```csharp
using System;

class Test 
{ 
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    public static int X = Test.F("Init A");
}

class B
{
    public static int Y = Test.F("Init B");
}
```
peut produire la sortie :
```console
Init A
Init B
1 1
```
ou la sortie :
```console
Init B
Init A
1 1
```
étant donné que l' `X`exécution de l’initialiseur et `Y`de l’initialiseur de est susceptible de se produire dans l’un ou l’autre ordre, il ne doit se produire que pour les références à ces champs. Toutefois, dans l’exemple :
```csharp
using System;

class Test
{
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    static A() {}

    public static int X = Test.F("Init A");
}

class B
{
    static B() {}

    public static int Y = Test.F("Init B");
}
```
la sortie doit être :
```console
Init B
Init A
1 1
```
étant donné que les règles de moment où les constructeurs statiques s’exécutent (comme défini dans les [constructeurs statiques](classes.md#static-constructors)) fournissent `B`ce `B`constructeur statique (et par conséquent, les `A`initialiseurs de champ statiques) doivent s’exécuter avant la statique de initialiseurs de constructeur et de champ.

#### <a name="instance-field-initialization"></a>Initialisation du champ d’instance

Les initialiseurs de variable de champ d’instance d’une classe correspondent à une séquence d’assignations qui sont exécutées immédiatement après l’entrée de l’un des constructeurs d’instance ([initialiseurs de constructeur](classes.md#constructor-initializers)) de cette classe. Les initialiseurs de variable sont exécutés dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe. Le processus de création et d’initialisation de l’instance de classe est décrit plus en détail dans les [constructeurs d’instance](classes.md#instance-constructors).

Un initialiseur de variable pour un champ d’instance ne peut pas faire référence à l’instance en cours de création. Par conséquent, il s’agit d’une erreur au moment de la compilation pour référencer `this` dans un initialiseur de variable, car il s’agit d’une erreur de compilation pour qu’un initialiseur de variable référence un membre d’instance via un *simple_name*. Dans l’exemple
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
l’initialiseur de variable `y` pour génère une erreur de compilation, car il fait référence à un membre de l’instance en cours de création.

## <a name="methods"></a>Méthodes

Une ***méthode*** est un membre qui implémente un calcul ou une action qui peut être effectuée par un objet ou une classe. Les méthodes sont déclarées à l’aide de *method_declaration*s :

```antlr
method_declaration
    : method_header method_body
    ;

method_header
    : attributes? method_modifier* 'partial'? return_type member_name type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause*
    ;

method_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | 'async'
    | method_modifier_unsafe
    ;

return_type
    : type
    | 'void'
    ;

member_name
    : identifier
    | interface_type '.' identifier
    ;

method_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

Un *method_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).

Une déclaration a une combinaison valide de modificateurs si toutes les conditions suivantes sont vraies :

*  La déclaration comprend une combinaison valide de modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)).
*  La déclaration n’inclut pas le même modificateur plusieurs fois.
*  La déclaration comprend au plus l’un des modificateurs suivants : `static`, `virtual`et `override`.
*  La déclaration comprend au plus l’un des modificateurs suivants : `new` et `override`.
*  Si la déclaration inclut le `abstract` modificateur, la déclaration n’inclut pas l’un des modificateurs suivants : `static`, `virtual` `sealed` ou `extern`.
*  Si la déclaration inclut le `private` modificateur, la déclaration n’inclut pas l’un des modificateurs suivants : `virtual`, `override`ou `abstract`.
*  Si la déclaration comprend le `sealed` modificateur, la déclaration comprend également le `override` modificateur.
*  Si la déclaration inclut le `partial` modificateur, elle n’inclut pas l’un des modificateurs suivants : `new`, `public`, `protected`, `internal`, `private`, `virtual`,, `override` `sealed` , `abstract`ou .`extern`

Une méthode qui a le `async` modificateur est une fonction Async et suit les règles décrites dans [fonctions Async](classes.md#async-functions).

L' *type_retour* d’une déclaration de méthode spécifie le type de la valeur calculée et retournée par la méthode. *Type_retour* est `void` si la méthode ne retourne pas de valeur. Si la déclaration comprend le `partial` modificateur, le type de retour doit être `void`.

*Member_Name* spécifie le nom de la méthode. À moins que la méthode ne soit une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)), *Member_Name* est simplement un *identificateur*. Pour une implémentation de membre d’interface explicite, *Member_Name* se compose d’un *INTERFACE_TYPE* suivi d’un « `.` » et d’un *identificateur*.

Le *type_parameter_list* facultatif spécifie les paramètres de type de la méthode ([paramètres de type](classes.md#type-parameters)). Si un *type_parameter_list* est spécifié, la méthode est une ***méthode générique***. Si la méthode a un modificateur `extern`, un *type_parameter_list* ne peut pas être spécifié.

Le *formal_parameter_list* facultatif spécifie les paramètres de la méthode ([paramètres de méthode](classes.md#method-parameters)).

Les *type_parameter_constraints_clause*facultatifs spécifient des contraintes sur des paramètres de type individuels ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) et peuvent être spécifiés uniquement si un *type_parameter_list* est également fourni, et si la méthode n’a pas de modificateur `override`.

L' *type_retour* et chacun des types référencés dans le *formal_parameter_list* d’une méthode doivent être au moins aussi accessibles que la méthode elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

*Method_body* est un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***. Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque la méthode est appelée. Un corps d’expression se `=>` compose de suivis d’une *expression* et d’un point-virgule, et désigne une expression unique à exécuter lorsque la méthode est appelée. 

Pour les méthodes `abstract` et `extern`, *method_body* se compose simplement d’un point-virgule. Pour les méthodes `partial`, *method_body* peut être constitué soit d’un point-virgule, d’un corps de bloc ou d’un corps d’expression. Pour toutes les autres méthodes, *method_body* est soit un corps de bloc, soit un corps d’expression.

Si le *method_body* se compose d’un point-virgule, la déclaration peut ne pas inclure le modificateur `async`.

Le nom, la liste des paramètres de type et la liste de paramètres formels d’une méthode définissent la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de la méthode. Plus précisément, la signature d’une méthode se compose de son nom, du nombre de paramètres de type et du nombre, des modificateurs et des types de ses paramètres formels. Pour ces raisons, tout paramètre de type de la méthode qui se produit dans le type d’un paramètre formel est identifié par son nom, mais par sa position ordinale dans la liste d’arguments de type de la méthode. Le type de retour ne fait pas partie de la signature d’une méthode, ni les noms des paramètres de type ni les paramètres formels.

Le nom d’une méthode doit être différent des noms de toutes les autres méthodes non déclarées dans la même classe. En outre, la signature d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir de `ref` signatures `out`qui diffèrent uniquement par et.

Le *type_parameter*de la méthode se trouve dans l’étendue dans tout le *method_declaration*, et peut être utilisé pour former des types dans cette portée dans *type_retour*, *method_body*et *type_parameter_constraints_clause*s, mais pas dans *attributs*.

Tous les paramètres formels et les paramètres de type doivent avoir des noms différents.

### <a name="method-parameters"></a>Paramètres de méthode

Les paramètres d’une méthode, le cas échéant, sont déclarés par le *formal_parameter_list*de la méthode.

```antlr
formal_parameter_list
    : fixed_parameters
    | fixed_parameters ',' parameter_array
    | parameter_array
    ;

fixed_parameters
    : fixed_parameter (',' fixed_parameter)*
    ;

fixed_parameter
    : attributes? parameter_modifier? type identifier default_argument?
    ;

default_argument
    : '=' expression
    ;

parameter_modifier
    : 'ref'
    | 'out'
    | 'this'
    ;

parameter_array
    : attributes? 'params' array_type identifier
    ;
```

La liste de paramètres formels se compose d’un ou de plusieurs paramètres séparés par des virgules dont seule la dernière peut être un *parameter_array*.

Un *fixed_parameter* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), d’un modificateur facultatif `ref`, `out` ou `this`, d’un *type*, d’un *identificateur* et d’un *default_argument*facultatif. Chaque *fixed_parameter* déclare un paramètre du type donné avec le nom donné. Le `this` modificateur désigne la méthode en tant que méthode d’extension et est uniquement autorisé sur le premier paramètre d’une méthode statique. Les méthodes d’extension sont décrites plus en détail dans [méthodes d’extension](classes.md#extension-methods).

Un *fixed_parameter* avec un *default_argument* est connu sous le nom de ***paramètre facultatif***, alors qu’un *fixed_parameter* sans *default_argument* est un ***paramètre obligatoire***. Un paramètre obligatoire ne peut pas apparaître après un paramètre facultatif dans un *formal_parameter_list*.

Un paramètre `ref` ou `out` ne peut pas avoir de *default_argument*. L' *expression* d’un *default_argument* doit être l’une des suivantes :

*  a *constant_expression*
*  expression de la forme `new S()` où `S` est un type valeur
*  expression de la forme `default(S)` où `S` est un type valeur

L' *expression* doit être implicitement convertible à l’aide d’une conversion d’identité ou de valeurs NULL dans le type du paramètre.

Si des paramètres facultatifs se produisent dans une déclaration de méthode partielle d’implémentation ([méthodes partielles](classes.md#partial-methods)), une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)) ou dans une déclaration d’indexeur de paramètre unique ([ Indexeurs](classes.md#indexers)) le compilateur doit fournir un avertissement, car ces membres ne peuvent jamais être appelés d’une manière qui autorise l’omission des arguments.

Un *parameter_array* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), d’un modificateur `params`, d’un *array_type*et d’un *identificateur*. Un tableau de paramètres déclare un paramètre unique du type de tableau donné avec le nom donné. Le *array_type* d’un tableau de paramètres doit être un type tableau unidimensionnel ([types tableau](arrays.md#array-types)). Dans un appel de méthode, un tableau de paramètres autorise la spécification d’un seul argument du type de tableau donné, ou elle permet de spécifier zéro ou plusieurs arguments du type d’élément de tableau. Les tableaux de paramètres sont décrits plus en détail dans les [tableaux de paramètres](classes.md#parameter-arrays).

Une *parameter_array* peut se produire après un paramètre facultatif, mais elle ne peut pas avoir de valeur par défaut ; l’omission des arguments pour un *parameter_array* entraînerait la création d’un tableau vide.

L’exemple suivant illustre différents genres de paramètres :
```csharp
public void M(
    ref int      i,
    decimal      d,
    bool         b = false,
    bool?        n = false,
    string       s = "Hello",
    object       o = null,
    T            t = default(T),
    params int[] a
) { }
```

Dans le *formal_parameter_list* pour `M`, `i` est un paramètre ref obligatoire, `d` est un paramètre de valeur obligatoire, `b`, `s`, `o` et `t` sont des paramètres de valeur facultatifs et `a` est un tableau de paramètres.

Une déclaration de méthode crée un espace de déclaration distinct pour les paramètres, les paramètres de type et les variables locales. Les noms sont introduits dans cet espace de déclaration par la liste de paramètres de type et la liste de paramètres formels de la méthode et par les déclarations de variable locale dans le *bloc* de la méthode. Il y a une erreur pour que deux membres d’un espace de déclaration de méthode aient le même nom. L’espace de déclaration de méthode et l’espace de déclaration de variable locale d’un espace de déclaration imbriquée doivent contenir des éléments portant le même nom.

Un appel de méthode ([appel de méthode](expressions.md#method-invocations)) crée une copie, spécifique à cet appel, des paramètres formels et des variables locales de la méthode, et la liste d’arguments de l’appel assigne des valeurs ou des références de variable à l’objet formel nouvellement créé. paramètres. Dans le *bloc* d’une méthode, les paramètres formels peuvent être référencés par leurs identificateurs dans les expressions *Simple_name* ([noms simples](expressions.md#simple-names)).

Il existe quatre genres de paramètres formels :

*  Les paramètres de valeur, qui sont déclarés sans modificateurs.
*  Les paramètres de référence, qui sont déclarés avec le `ref` modificateur.
*  Les paramètres de sortie, qui sont déclarés avec le `out` modificateur.
*  Tableaux de paramètres, qui sont déclarés avec `params` le modificateur.

Comme décrit dans [signatures et surcharge](basic-concepts.md#signatures-and-overloading), les `ref` modificateurs et `out` font partie de la signature d’une méthode, mais le `params` modificateur n’est pas.

#### <a name="value-parameters"></a>Paramètres de valeur

Un paramètre déclaré sans modificateurs est un paramètre de valeur. Un paramètre de valeur correspond à une variable locale qui obtient sa valeur initiale de l’argument correspondant fourni dans l’appel de méthode.

Quand un paramètre formel est un paramètre de valeur, l’argument correspondant dans un appel de méthode doit être une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type de paramètre formel.

Une méthode est autorisée à assigner de nouvelles valeurs à un paramètre de valeur. Ces affectations affectent uniquement l’emplacement de stockage local représenté par le paramètre de valeur, elles n’ont aucun effet sur l’argument réel fourni dans l’appel de méthode.

#### <a name="reference-parameters"></a>Paramètres de référence

Un paramètre déclaré avec un `ref` modificateur est un paramètre de référence. Contrairement à un paramètre de valeur, un paramètre de référence ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de référence représente le même emplacement de stockage que la variable donnée comme argument dans l’appel de méthode.

Quand un paramètre formel est un paramètre de référence, l’argument correspondant dans un appel de méthode doit se composer du mot clé `ref` suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que paramètre formel. Une variable doit être assignée définitivement pour pouvoir être passée en tant que paramètre de référence.

Dans une méthode, un paramètre de référence est toujours considéré comme étant définitivement assigné.

Une méthode déclarée en tant qu’itérateur ([itérateurs](classes.md#iterators)) ne peut pas avoir de paramètres de référence.

L’exemple
```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("i = {0}, j = {1}", i, j);
    }
}
```
génère la sortie
```console
i = 2, j = 1
```

Pour l’appel de `Swap` dans `Main`, `x` représente `i` et `y` représente. `j` Ainsi, l’appel a pour effet de permuter les valeurs de `i` et `j`.

Dans une méthode qui accepte des paramètres de référence, il est possible que plusieurs noms représentent le même emplacement de stockage. Dans l’exemple
```csharp
class A
{
    string s;

    void F(ref string a, ref string b) {
        s = "One";
        a = "Two";
        b = "Three";
    }

    void G() {
        F(ref s, ref s);
    }
}
```
l' `F` appel de `b`dans `G` `s` passe`a` une référence à pour et. Ainsi, pour cet appel, les `s`noms, `a`et `b` font tous référence au même emplacement de stockage, et les trois assignations modifient toutes le `s`champ d’instance.

#### <a name="output-parameters"></a>Paramètres de sortie

Un paramètre déclaré avec un `out` modificateur est un paramètre de sortie. À l’instar d’un paramètre de référence, un paramètre de sortie ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de sortie représente le même emplacement de stockage que la variable donnée comme argument dans l’appel de la méthode.

Quand un paramètre formel est un paramètre de sortie, l’argument correspondant dans un appel de méthode doit se composer du mot clé `out` suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que le paramètre formel. Une variable n’a pas besoin d’être définitivement assignée avant de pouvoir être transmise en tant que paramètre de sortie, mais après un appel où une variable a été passée comme paramètre de sortie, la variable est considérée comme assignée de manière définitive.

Dans une méthode, tout comme une variable locale, un paramètre de sortie est considéré initialement comme non assigné et doit être définitivement assigné avant que sa valeur ne soit utilisée.

Chaque paramètre de sortie d’une méthode doit être assigné définitivement avant le retour de la méthode.

Une méthode déclarée en tant que méthode partielle ([méthodes partielles](classes.md#partial-methods)) ou itérateur ([itérateurs](classes.md#iterators)) ne peut pas avoir de paramètres de sortie.

Les paramètres de sortie sont généralement utilisés dans les méthodes qui produisent plusieurs valeurs de retour. Exemple :
```csharp
using System;

class Test
{
    static void SplitPath(string path, out string dir, out string name) {
        int i = path.Length;
        while (i > 0) {
            char ch = path[i - 1];
            if (ch == '\\' || ch == '/' || ch == ':') break;
            i--;
        }
        dir = path.Substring(0, i);
        name = path.Substring(i);
    }

    static void Main() {
        string dir, name;
        SplitPath("c:\\Windows\\System\\hello.txt", out dir, out name);
        Console.WriteLine(dir);
        Console.WriteLine(name);
    }
}
```

L’exemple produit la sortie :
```console
c:\Windows\System\
hello.txt
```

Notez que les `dir` variables `name` et peuvent être non assignées avant d’être `SplitPath`passées à, et qu’elles sont considérées comme définitivement assignées après l’appel.

#### <a name="parameter-arrays"></a>Tableaux de paramètres

Un paramètre déclaré avec un `params` modificateur est un tableau de paramètres. Si une liste de paramètres formels comprend un tableau de paramètres, elle doit être le dernier paramètre de la liste et doit être de type tableau unidimensionnel. Par exemple, les types `string[]` et `string[][]` peuvent être utilisés comme type d’un tableau de paramètres, mais le type `string[,]` ne peut pas. Il n’est pas possible de combiner `params` le modificateur avec les `ref` modificateurs `out`et.

Un tableau de paramètres permet de spécifier des arguments de l’une des deux manières suivantes dans un appel de méthode :

*  L’argument donné pour un tableau de paramètres peut être une expression unique implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type tableau de paramètres. Dans ce cas, le tableau de paramètres agit précisément comme un paramètre de valeur.
*  L’appel peut également spécifier zéro ou plusieurs arguments pour le tableau de paramètres, où chaque argument est une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type d’élément du tableau de paramètres. Dans ce cas, l’appel crée une instance du type de tableau de paramètres avec une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument données et utilise l’instance de tableau nouvellement créée comme argument.

À l’exception de l’autorisation d’un nombre variable d’arguments dans un appel, un tableau de paramètres équivaut précisément à un paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)) du même type.

L’exemple
```csharp
using System;

class Test
{
    static void F(params int[] args) {
        Console.Write("Array contains {0} elements:", args.Length);
        foreach (int i in args) 
            Console.Write(" {0}", i);
        Console.WriteLine();
    }

    static void Main() {
        int[] arr = {1, 2, 3};
        F(arr);
        F(10, 20, 30, 40);
        F();
    }
}
```
génère la sortie
```console
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

La première invocation de `F` passe simplement le tableau `a` en tant que paramètre de valeur. Le deuxième appel de `F` crée automatiquement un élément à quatre éléments `int[]` avec les valeurs d’élément données et passe cette instance de tableau en tant que paramètre de valeur. De même, le troisième appel de `F` crée un élément `int[]` de zéro et passe cette instance en tant que paramètre de valeur. Le deuxième et le troisième appel sont exactement équivalents à l’écriture :
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

Lors de l’exécution de la résolution de surcharge, une méthode avec un tableau de paramètres peut s’appliquer soit dans sa forme normale, soit dans sa forme développée ([fonction membre applicable](expressions.md#applicable-function-member)). La forme développée d’une méthode est disponible uniquement si la forme normale de la méthode n’est pas applicable et uniquement si une méthode applicable avec la même signature que la forme développée n’est pas déjà déclarée dans le même type.

L’exemple
```csharp
using System;

class Test
{
    static void F(params object[] a) {
        Console.WriteLine("F(object[])");
    }

    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object a0, object a1) {
        Console.WriteLine("F(object,object)");
    }

    static void Main() {
        F();
        F(1);
        F(1, 2);
        F(1, 2, 3);
        F(1, 2, 3, 4);
    }
}
```
génère la sortie
```console
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

Dans l’exemple, deux des formes étendues possibles de la méthode avec un tableau de paramètres sont déjà incluses dans la classe en tant que méthodes normales. Ces formulaires développés ne sont donc pas pris en compte lors de l’exécution de la résolution de surcharge, et les premier et troisième appels de méthode sélectionnent donc les méthodes normales. Quand une classe déclare une méthode avec un tableau de paramètres, il n’est pas rare d’inclure également certains des formulaires développés comme méthodes normales. En procédant ainsi, il est possible d’éviter l’allocation d’une instance de tableau qui se produit lorsqu’une forme développée d’une méthode avec un tableau de paramètres est appelée.

Lorsque le type d’un tableau de paramètres `object[]`est, une ambiguïté potentielle se produit entre la forme normale de la méthode et la forme étendue pour un paramètre `object` unique. L’ambiguïté est due au fait qu’un `object[]` est lui-même convertible implicitement en type. `object` Toutefois, l’ambiguïté ne présente aucun problème, car elle peut être résolue en insérant un cast si nécessaire.

L’exemple
```csharp
using System;

class Test
{
    static void F(params object[] args) {
        foreach (object o in args) {
            Console.Write(o.GetType().FullName);
            Console.Write(" ");
        }
        Console.WriteLine();
    }

    static void Main() {
        object[] a = {1, "Hello", 123.456};
        object o = a;
        F(a);
        F((object)a);
        F(o);
        F((object[])o);
    }
}
```
génère la sortie
```console
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

Dans le premier et le dernier appel de `F`, la forme normale de `F` est applicable, car il existe une conversion implicite du type d’argument vers le type de paramètre (les `object[]`deux sont de type). Ainsi, la résolution de surcharge sélectionne la forme `F`normale de et l’argument est passé en tant que paramètre de valeur régulière. Dans le deuxième et le troisième appel, la forme normale de `F` n’est pas applicable, car il n’existe aucune conversion implicite du type d’argument vers le type de paramètre `object` (le type ne `object[]`peut pas être converti implicitement en type). Toutefois, la forme développée de `F` est applicable, donc elle est sélectionnée par la résolution de surcharge. Par conséquent, un élément `object[]` unique est créé par l’appel, et l’élément unique du tableau est initialisé avec la valeur d’argument donnée (qui est elle-même une référence à un `object[]`).

### <a name="static-and-instance-methods"></a>Méthodes statiques et d’instance

Lorsqu’une déclaration de méthode comprend `static` un modificateur, cette méthode est dite statique. Quand aucun `static` modificateur n’est présent, la méthode est considérée comme une méthode d’instance.

Une méthode statique ne fonctionne pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire `this` référence à dans une méthode statique.

Une méthode d’instance opère sur une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)).

Lorsqu’une méthode est référencée dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est une méthode statique, `E` doit désigner un type contenant `M`, et si `M` est une méthode d’instance, `E` doit désigner une instance d’un type contenant `M`.

Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="virtual-methods"></a>Méthodes virtuelles

Lorsqu’une déclaration de méthode d’instance `virtual` comprend un modificateur, cette méthode est dite méthode virtuelle. Quand aucun `virtual` modificateur n’est présent, la méthode est dite non virtuelle.

L’implémentation d’une méthode non virtuelle est indifférente : L’implémentation est la même que la méthode soit appelée sur une instance de la classe dans laquelle elle est déclarée ou d’une instance d’une classe dérivée. En revanche, l’implémentation d’une méthode virtuelle peut être remplacée par des classes dérivées. Le processus de remplacement de l’implémentation d’une méthode virtuelle héritée est connu sous le nom de ***substitution*** de cette méthode ([méthodes override](classes.md#override-methods)).

Dans un appel de méthode virtuelle, le ***type au moment*** de l’exécution de l’instance pour laquelle cet appel se produit détermine l’implémentation de méthode réelle à appeler. Dans un appel de méthode non virtuel, le ***type au moment*** de la compilation de l’instance est le facteur déterminant. En termes précis, quand une méthode nommée `N` est appelée avec une liste `A` d’arguments sur une instance avec un type `C` au moment de la compilation et un type `R` au moment de `R` l' `C` exécution (où est ou une classe dérivée à `C`partir de), l’appel est traité comme suit :

*  Tout d’abord, la résolution de `C`surcharge `N`est appliquée `A`à, et, pour sélectionner `M` une méthode spécifique à partir de l’ensemble de méthodes `C`déclaré dans et héritée par. Cela est décrit dans [appels de méthode](expressions.md#method-invocations).
*  Ensuite, si `M` est une méthode non virtuelle, `M` est appelée.
*  Sinon, `M` est une méthode virtuelle et l’implémentation la plus dérivée `M` de par rapport `R` à est appelée.

Pour chaque méthode virtuelle déclarée dans ou héritée par une classe, il existe une ***implémentation la plus dérivée*** de la méthode par rapport à cette classe. L’implémentation la plus dérivée d’une `M` méthode virtuelle en ce qui `R` concerne une classe est déterminée comme suit :

*  Si `R` contient la Déclaration `virtual` d’introduction `M`de, il s’agit de l’implémentation la `M`plus dérivée de.
*  Sinon, si `R` contient un `override` de `M`, il s’agit de l’implémentation la plus `M`dérivée de.
*  Sinon, l' `M` implémentation la plus dérivée de par rapport à `R` est identique à l’implémentation la plus `M` dérivée de par rapport à la classe `R`de base directe de.

L’exemple suivant illustre les différences entre les méthodes virtuelles et non virtuelles :
```csharp
using System;

class A
{
    public void F() { Console.WriteLine("A.F"); }

    public virtual void G() { Console.WriteLine("A.G"); }
}

class B: A
{
    new public void F() { Console.WriteLine("B.F"); }

    public override void G() { Console.WriteLine("B.G"); }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        a.F();
        b.F();
        a.G();
        b.G();
    }
}
```

Dans l’exemple, `A` introduit une méthode `F` non virtuelle et une méthode `G`virtuelle. La classe `B` introduit une nouvelle méthode `F`non virtuelle, masquant ainsi la héritée `F`et substitue également la méthode `G`héritée. L’exemple produit la sortie :
```console
A.F
B.F
B.G
B.G
```

Notez que l’instruction `a.G()` `B.G`appelle, et non `A.G`. Cela est dû au fait que le type au moment de l’exécution de `B`l’instance (qui est), et non le type au moment de `A`la compilation de l’instance (c’est-à-dire), détermine l’implémentation réelle de la méthode à appeler.

Étant donné que les méthodes sont autorisées à masquer les méthodes héritées, il est possible qu’une classe contienne plusieurs méthodes virtuelles avec la même signature. Cela ne présente pas de problème d’ambiguïté, puisque toutes les méthodes sauf la plus dérivée sont masquées. Dans l’exemple
```csharp
using System;

class A
{
    public virtual void F() { Console.WriteLine("A.F"); }
}

class B: A
{
    public override void F() { Console.WriteLine("B.F"); }
}

class C: B
{
    new public virtual void F() { Console.WriteLine("C.F"); }
}

class D: C
{
    public override void F() { Console.WriteLine("D.F"); }
}

class Test
{
    static void Main() {
        D d = new D();
        A a = d;
        B b = d;
        C c = d;
        a.F();
        b.F();
        c.F();
        d.F();
    }
}
```
les `C` classes `D` et contiennent deux méthodes virtuelles avec la même signature : Celui introduit par `A` et celui introduit par `C`. La méthode introduite `C` par masque la méthode héritée `A`de. Ainsi, la `D` déclaration override de substitue la méthode introduite par `C`, et il n’est pas possible pour `D` de substituer la méthode introduite `A`par. L’exemple produit la sortie :
```console
B.F
B.F
D.F
D.F
```

Notez qu’il est possible d’appeler la méthode virtuelle masquée en accédant à une `D` instance de via un type moins dérivé dans lequel la méthode n’est pas masquée.

### <a name="override-methods"></a>Substituer les méthodes

Lorsqu’une déclaration de méthode d’instance `override` comprend un modificateur, la méthode est considérée comme une ***méthode de substitution***. Une méthode override remplace une méthode virtuelle héritée avec la même signature. Là où une déclaration de méthode virtuelle présente une nouvelle méthode, une déclaration de méthode de substitution spécialise une méthode virtuelle héritée existante en fournissant une nouvelle implémentation de cette méthode.

La méthode substituée par une `override` déclaration est connue sous le nom de ***méthode de base substituée***. Pour `M` une méthode de substitution déclarée dans une `C`classe, la méthode de base substituée est déterminée en examinant chaque type `C`de classe de base de, en commençant par `C` le type de classe de base directe de et en continuant avec chaque type de classe de base directe, jusqu’à ce que dans un type de classe de base donné, au moins une méthode accessible `M` soit localisée avec la même signature qu’après la substitution des arguments de type. Dans le cadre de la recherche de la méthode de base substituée, une méthode est considérée comme accessible `public`si `protected` `protected internal`c’est le cas, si c’est le cas, ou `internal` si elle est et est déclarée `C`dans le même programme que.

Une erreur de compilation se produit sauf si toutes les conditions suivantes sont vraies pour une déclaration override :

*  Une méthode de base substituée peut être localisée comme décrit ci-dessus.
*  Il existe exactement une méthode de base substituée. Cette restriction n’a d’effet que si le type de classe de base est un type construit où la substitution d’arguments de type rend la signature de deux méthodes identique.
*  La méthode de base substituée est une méthode virtuelle, abstraite ou de substitution. En d’autres termes, la méthode de base substituée ne peut pas être statique ou non virtuelle.
*  La méthode de base substituée n’est pas une méthode sealed.
*  La méthode de substitution et la méthode de base substituée ont le même type de retour.
*  La déclaration override et la méthode de base substituée ont la même accessibilité déclarée. En d’autres termes, une déclaration override ne peut pas modifier l’accessibilité de la méthode virtuelle. Toutefois, si la méthode de base substituée est protégée interne et qu’elle est déclarée dans un assembly différent de l’assembly contenant la méthode de substitution, l’accessibilité déclarée de la méthode de substitution doit être protégée.
*  La déclaration override ne spécifie pas de clauses de type-Parameter-Constraints. Au lieu de cela, les contraintes sont héritées de la méthode de base substituée. Notez que les contraintes qui sont des paramètres de type dans la méthode substituée peuvent être remplacées par des arguments de type dans la contrainte héritée. Cela peut entraîner des contraintes qui ne sont pas autorisées quand elles sont explicitement spécifiées, telles que des types valeur ou des types sealed.

L’exemple suivant montre comment les règles de substitution fonctionnent pour les classes génériques :
```csharp
abstract class C<T>
{
    public virtual T F() {...}
    public virtual C<T> G() {...}
    public virtual void H(C<T> x) {...}
}

class D: C<string>
{
    public override string F() {...}            // Ok
    public override C<string> G() {...}         // Ok
    public override void H(C<T> x) {...}        // Error, should be C<string>
}

class E<T,U>: C<U>
{
    public override U F() {...}                 // Ok
    public override C<U> G() {...}              // Ok
    public override void H(C<T> x) {...}        // Error, should be C<U>
}
```

Une déclaration override peut accéder à la méthode de base substituée à l’aide d’un *base_access* ([accès de base](expressions.md#base-access)). Dans l’exemple
```csharp
class A
{
    int x;

    public virtual void PrintFields() {
        Console.WriteLine("x = {0}", x);
    }
}

class B: A
{
    int y;

    public override void PrintFields() {
        base.PrintFields();
        Console.WriteLine("y = {0}", y);
    }
}
```
l' `base.PrintFields()` appel dans `B` appelle la `PrintFields` méthode déclarée dans `A`. Un *base_access* désactive le mécanisme d’appel virtuel et traite simplement la méthode de base comme une méthode non virtuelle. Si l' `B` appel a été écrit `((A)this).PrintFields()`, il appellerait de manière récursive la `PrintFields` méthode déclarée dans `B`, pas celle qui est déclarée dans `PrintFields` `A`, car est virtuel et le type d’exécution de `((A)this)` est`B`de.

Une méthode ne peut `override` se substituer à une autre méthode qu’en incluant un modificateur. Dans tous les autres cas, une méthode avec la même signature qu’une méthode héritée masque simplement la méthode héritée. Dans l’exemple
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    public virtual void F() {}        // Warning, hiding inherited F()
}
```
la `F` méthode dans `B` n’inclut `override` pas de modificateur et, par conséquent, ne substitue pas `A`la `F` méthode dans. Au lieu de `F` cela, `B` la méthode dans masque la `A`méthode dans et un avertissement est signalé, car la déclaration n’inclut `new` pas de modificateur.

Dans l’exemple
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    new private void F() {}        // Hides A.F within body of B
}

class C: B
{
    public override void F() {}    // Ok, overrides A.F
}
```
la `F` méthode dans `B` masque la méthode virtuelle `F` héritée de `A`. Étant donné que `F` le `B` nouveau dans a un accès privé, son étendue inclut uniquement le `B` corps de classe de et `C`n’étend pas à. Par conséquent, la Déclaration `F` de `C` dans est autorisée à substituer `F` l’héritage `A`de.

### <a name="sealed-methods"></a>Méthodes sealed

Lorsqu’une déclaration de méthode d’instance `sealed` comprend un modificateur, cette méthode est dite ***sealed***. Si une déclaration de méthode d’instance `sealed` inclut le modificateur, elle doit également `override` inclure le modificateur. L' `sealed` utilisation du modificateur empêche une classe dérivée de substituer davantage la méthode.

Dans l’exemple
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }

    public virtual void G() {
        Console.WriteLine("A.G");
    }
}

class B: A
{
    sealed override public void F() {
        Console.WriteLine("B.F");
    } 

    override public void G() {
        Console.WriteLine("B.G");
    } 
}

class C: B
{
    override public void G() {
        Console.WriteLine("C.G");
    } 
}
```
la classe `B` fournit deux méthodes override : une `F` méthode qui a le modificateur et une `G` méthode qui ne le `sealed` fait pas. `B`l’utilisation du sealed `modifier` empêche `C` toute substitution `F`supplémentaire.

### <a name="abstract-methods"></a>Méthodes abstraites

Lorsqu’une déclaration de méthode d’instance `abstract` comprend un modificateur, cette méthode est dite ***abstraite***. Bien qu’une méthode abstraite soit implicitement également une méthode virtuelle, elle ne peut pas `virtual`avoir de modificateur.

Une déclaration de méthode abstraite introduit une nouvelle méthode virtuelle, mais ne fournit pas d’implémentation de cette méthode. Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation en substituant cette méthode. Étant donné qu’une méthode abstraite ne fournit pas d’implémentation réelle, le *method_body* d’une méthode abstraite se compose simplement d’un point-virgule.

Les déclarations de méthode abstraite sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).

Dans l’exemple
```csharp
public abstract class Shape
{
    public abstract void Paint(Graphics g, Rectangle r);
}

public class Ellipse: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawEllipse(r);
    }
}

public class Box: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawRect(r);
    }
}
```
la `Shape` classe définit la notion abstraite d’un objet de forme géométrique qui peut se peindre lui-même. La `Paint` méthode est abstraite, car il n’y a pas d’implémentation significative par défaut. Les `Ellipse` classes `Box` et sont des `Shape` implémentations concrètes. Étant donné que ces classes ne sont pas abstraites, elles doivent substituer `Paint` la méthode et fournir une implémentation réelle.

Il s’agit d’une erreur au moment de la compilation pour qu’un *base_access* ([base Access](expressions.md#base-access)) référence une méthode abstraite. Dans l’exemple
```csharp
abstract class A
{
    public abstract void F();
}

class B: A
{
    public override void F() {
        base.F();                        // Error, base.F is abstract
    }
}
```
une erreur de compilation est signalée pour l' `base.F()` appel, car elle fait référence à une méthode abstraite.

Une déclaration de méthode abstraite est autorisée à substituer une méthode virtuelle. Cela permet à une classe abstraite de forcer la réimplémentation de la méthode dans les classes dérivées et rend l’implémentation d’origine de la méthode indisponible. Dans l’exemple
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }
}

abstract class B: A
{
    public abstract override void F();
}

class C: B
{
    public override void F() {
        Console.WriteLine("C.F");
    }
}
```
la `A` classe déclare une méthode virtuelle, la `B` classe remplace cette méthode par une méthode abstraite, et la `C` classe substitue la méthode abstraite pour fournir sa propre implémentation.

### <a name="external-methods"></a>Méthodes externes

Lorsqu’une déclaration de méthode comprend `extern` un modificateur, cette méthode est dite ***externe***. Les méthodes externes sont implémentées en externe, généralement à l’aide C#d’un langage autre que. Étant donné qu’une déclaration de méthode externe ne fournit aucune implémentation réelle, le *method_body* d’une méthode externe se compose simplement d’un point-virgule. Une méthode externe ne peut pas être générique.

Le `extern` modificateur est généralement utilisé conjointement avec un `DllImport` attribut ([interopérabilité avec les composants COM et Win32](attributes.md#interoperation-with-com-and-win32-components)), ce qui permet d’implémenter des méthodes externes par des dll (bibliothèques de liens dynamiques). L’environnement d’exécution peut prendre en charge d’autres mécanismes par lesquels des implémentations de méthodes externes peuvent être fournies.

Quand une méthode externe inclut un `DllImport` attribut, la déclaration de méthode doit également inclure `static` un modificateur. Cet exemple illustre l’utilisation du `extern` modificateur et de l' `DllImport` attribut :
```csharp
using System.Text;
using System.Security.Permissions;
using System.Runtime.InteropServices;

class Path
{
    [DllImport("kernel32", SetLastError=true)]
    static extern bool CreateDirectory(string name, SecurityAttribute sa);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool RemoveDirectory(string name);

    [DllImport("kernel32", SetLastError=true)]
    static extern int GetCurrentDirectory(int bufSize, StringBuilder buf);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool SetCurrentDirectory(string name);
}
```

### <a name="partial-methods-recap"></a>Méthodes partielles (récapitulatif)

Lorsqu’une déclaration de méthode comprend `partial` un modificateur, cette méthode est dite ***partielle***. Les méthodes partielles ne peuvent être déclarées que comme membres de types partiels ([types partiels](classes.md#partial-types)) et sont soumises à un certain nombre de restrictions. Les méthodes partielles sont décrites plus en détail dans les [méthodes partielles](classes.md#partial-methods).

### <a name="extension-methods"></a>Méthodes d’extension

Quand le premier paramètre d’une méthode comprend le `this` modificateur, cette méthode est considérée comme une ***méthode d’extension***. Les méthodes d’extension ne peuvent être déclarées que dans des classes statiques non imbriquées et non génériques. Le premier paramètre d’une méthode d’extension ne peut pas avoir de modificateurs autres que `this`, et le type de paramètre ne peut pas être un type pointeur.

Voici un exemple de classe statique qui déclare deux méthodes d’extension :
```csharp
public static class Extensions
{
    public static int ToInt32(this string s) {
        return Int32.Parse(s);
    }

    public static T[] Slice<T>(this T[] source, int index, int count) {
        if (index < 0 || count < 0 || source.Length - index < count)
            throw new ArgumentException();
        T[] result = new T[count];
        Array.Copy(source, index, result, 0, count);
        return result;
    }
}
```

Une méthode d’extension est une méthode statique normale. En outre, lorsque sa classe statique englobante est dans la portée, une méthode d’extension peut être appelée à l’aide de la syntaxe d’appel de méthode d’instance ([appels de méthode d’extension](expressions.md#extension-method-invocations)), à l’aide de l’expression de récepteur comme premier argument.

Le programme suivant utilise les méthodes d’extension déclarées ci-dessus :
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in strings.Slice(1, 2)) {
            Console.WriteLine(s.ToInt32());
        }
    }
}
```

La `Slice` méthode est disponible sur le `string[]`, et la `ToInt32` méthode est disponible sur `string`, car elles ont été déclarées comme méthodes d’extension. La signification du programme est la même que celle qui suit, à l’aide d’appels de méthode statique ordinaires :
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in Extensions.Slice(strings, 1, 2)) {
            Console.WriteLine(Extensions.ToInt32(s));
        }
    }
}
```

### <a name="method-body"></a>Corps de la méthode

Le *method_body* d’une déclaration de méthode se compose d’un corps de bloc, d’un corps d’expression ou d’un point-virgule.

Le ***type de résultat*** d’une méthode `void` est si le type de `void`retour est, ou si la méthode est asynchrone et que le `System.Threading.Tasks.Task`type de retour est. Sinon, le type de résultat d’une méthode non asynchrone est son type de retour et le type de résultat d’une méthode Async avec le `System.Threading.Tasks.Task<T>` type `T`de retour est.

Quand une méthode a un `void` type de résultat et un corps de `return` bloc, les instructions ([l’instruction return](statements.md#the-return-statement)) dans le bloc ne sont pas autorisées à spécifier une expression. Si l’exécution du bloc d’une méthode void se termine normalement (autrement dit, le contrôle circule à la fin du corps de la méthode), cette méthode retourne simplement à son appelant actuel.
    
Quand une méthode a un résultat `void` et un corps d’expression, l’expression `E` doit être un *statement_expression*et le corps est exactement équivalent à un corps de bloc de la forme `{ E; }`.
    
Quand une méthode a un type de résultat non void et un corps de bloc, `return` chaque instruction du bloc doit spécifier une expression qui est implicitement convertible en type de résultat. Le point de terminaison d’un corps de bloc d’une méthode retournant une valeur ne doit pas être accessible. En d’autres termes, dans une méthode retournant une valeur avec un corps de bloc, le contrôle n’est pas autorisé à circuler à la fin du corps de la méthode.
    
Quand une méthode a un type de résultat non void et un corps d’expression, l’expression doit être implicitement convertible en type de résultat, et le corps est exactement équivalent à un corps de bloc de `{ return E; }`la forme.
    
Dans l’exemple
```csharp
class A
{
    public int F() {}            // Error, return value required

    public int G() {
        return 1;
    }

    public int H(bool b) {
        if (b) {
            return 1;
        }
        else {
            return 0;
        }
    }

    public int I(bool b) => b ? 1 : 0;
}
```
la méthode qui retourne `F` une valeur génère une erreur au moment de la compilation, car le contrôle peut circuler à la fin du corps de la méthode. Les `G` méthodes `H` et sont correctes, car tous les chemins d’exécution possibles se terminent par une instruction return qui spécifie une valeur de retour. La `I` méthode est correcte, car son corps est équivalent à un bloc d’instructions avec une seule instruction return.

### <a name="method-overloading"></a>Surcharge de méthode

Les règles de résolution de surcharge de méthode sont décrites dans [inférence de type](expressions.md#type-inference).

## <a name="properties"></a>Properties

Une ***propriété*** est un membre qui fournit l’accès à une caractéristique d’un objet ou d’une classe. Les exemples de propriétés incluent la longueur d’une chaîne, la taille d’une police, la légende d’une fenêtre, le nom d’un client, etc. Les propriétés sont une extension naturelle des champs, les deux étant des membres nommés avec des types associés, et la syntaxe d’accès aux champs et aux propriétés est la même. Toutefois, contrairement aux champs, les propriétés ne désignent pas des emplacements de stockage. Au lieu de cela, les propriétés ont des ***accesseurs*** qui spécifient les instructions à exécuter lorsque les valeurs sont lues ou écrites. Les propriétés fournissent donc un mécanisme permettant d’associer des actions à la lecture et à l’écriture des attributs d’un objet. en outre, ils permettent de calculer ces attributs.

Les propriétés sont déclarées à l’aide de *property_declaration*s :

```antlr
property_declaration
    : attributes? property_modifier* type member_name property_body
    ;

property_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | property_modifier_unsafe
    ;

property_body
    : '{' accessor_declarations '}' property_initializer?
    | '=>' expression ';'
    ;

property_initializer
    : '=' variable_initializer ';'
    ;
```

Un *property_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).

Les déclarations de propriété sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs.

Le *type* d’une déclaration de propriété spécifie le type de la propriété introduite par la déclaration, et *Member_Name* spécifie le nom de la propriété. À moins que la propriété ne soit une implémentation de membre d’interface explicite, *Member_Name* est simplement un *identificateur*. Pour une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)), *Member_Name* se compose d’un *interface_type* suivi d’un « `.` » et d’un *identificateur*.

Le *type* d’une propriété doit être au moins aussi accessible que la propriété elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Un *property_body* peut être constitué d’un ***corps d’accesseur*** ou d’un ***corps d’expression***. Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre les jetons « `{` » et « `}` », déclare les accesseurs ([accesseurs](classes.md#accessors)) de la propriété. Les accesseurs spécifient les instructions exécutables associées à la lecture et à l’écriture de la propriété.

Un corps d’expression composé `=>` de suivis d’une *expression* `E` et d’un point-virgule est exactement `{ get { return E; } }`équivalent au corps de l’instruction et ne peut donc être utilisé que pour spécifier des propriétés Getter uniquement lorsque le résultat de l’accesseur Get est fourni par une expression unique.

Un *property_initializer* peut uniquement être fourni pour une propriété implémentée automatiquement ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)) et provoque l’initialisation du champ sous-jacent de telles propriétés avec la valeur donnée par l' *expression* .

Même si la syntaxe d’accès à une propriété est identique à celle d’un champ, une propriété n’est pas classée comme une variable. Par conséquent, il n’est pas possible de passer une propriété `ref` en `out` tant qu’argument ou.

Lorsqu’une déclaration de propriété comprend `extern` un modificateur, la propriété est considérée comme une ***propriété externe***. Étant donné qu’une déclaration de propriété externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.

### <a name="static-and-instance-properties"></a>Propriétés statiques et d’instance

Lorsqu’une déclaration de propriété comprend `static` un modificateur, la propriété est considérée comme une ***propriété statique***. Quand aucun `static` modificateur n’est présent, la propriété est considérée comme une ***propriété d’instance***.

Une propriété statique n’est pas associée à une instance spécifique, et il s’agit d’une erreur de compilation pour `this` faire référence aux accesseurs d’une propriété statique.

Une propriété d’instance est associée à une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)) dans les accesseurs de cette propriété.

Lorsqu’une propriété est référencée dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est une propriété statique, `E` doit désigner un type contenant `M`, et si `M` est une propriété d’instance, E doit désigner une instance d’un type contenant `M`.

Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="accessors"></a>Accesseurs

Le *accessor_declarations* d’une propriété spécifie les instructions exécutables associées à la lecture et à l’écriture de cette propriété.

```antlr
accessor_declarations
    : get_accessor_declaration set_accessor_declaration?
    | set_accessor_declaration get_accessor_declaration?
    ;

get_accessor_declaration
    : attributes? accessor_modifier? 'get' accessor_body
    ;

set_accessor_declaration
    : attributes? accessor_modifier? 'set' accessor_body
    ;

accessor_modifier
    : 'protected'
    | 'internal'
    | 'private'
    | 'protected' 'internal'
    | 'internal' 'protected'
    ;

accessor_body
    : block
    | ';'
    ;
```

Les déclarations d’accesseur se composent d’un *get_accessor_declaration*, d’un *set_accessor_declaration*ou des deux. Chaque déclaration d’accesseur se compose du jeton `get` ou `set` suivi d’un *accessor_modifier* facultatif et d’un *accessor_body*.

L’utilisation de *accessor_modifier*s est régie par les restrictions suivantes :

*  Un *accessor_modifier* ne peut pas être utilisé dans une interface ou dans une implémentation de membre d’interface explicite.
*  Pour une propriété ou un indexeur qui n’a pas de modificateur `override`, un *accessor_modifier* est autorisé uniquement si la propriété ou l’indexeur a à la fois un accesseur `get` et `set`, et n’est autorisé que sur l’un de ces accesseurs.
*  Pour une propriété ou un indexeur qui comprend un modificateur `override`, un accesseur doit correspondre au *accessor_modifier*, le cas échéant, de l’accesseur en cours de substitution.
*  Le *accessor_modifier* doit déclarer une accessibilité qui est strictement plus restrictive que l’accessibilité déclarée de la propriété ou de l’indexeur lui-même. Pour être précis :
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `public`, *accessor_modifier* peut être `protected internal`, `internal`, `protected` ou `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `protected internal`, la valeur de *accessor_modifier* peut être `internal`, `protected` ou `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `internal` ou `protected`, *accessor_modifier* doit être `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `private`, aucun *accessor_modifier* ne peut être utilisé.

Pour les propriétés `abstract` et `extern`, *accessor_body* pour chaque accesseur spécifié est simplement un point-virgule. Une propriété non abstraite et non-extern peut avoir chaque *accessor_body* un point-virgule, auquel cas il s’agit d’une ***propriété implémentée automatiquement*** ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)). Une propriété implémentée automatiquement doit avoir au moins un accesseur Get. Pour les accesseurs de toute autre propriété non-abstract non-extern, *accessor_body* est un *bloc* qui spécifie les instructions à exécuter lorsque l’accesseur correspondant est appelé.

Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété. À l’exception de la cible d’une assignation, lorsqu’une propriété est référencée dans une expression `get` , l’accesseur de la propriété est appelé pour calculer la valeur de la propriété ([valeurs des expressions](expressions.md#values-of-expressions)). Le corps d’un `get` accesseur doit se conformer aux règles pour les méthodes retournant des valeurs décrites dans le corps de la [méthode](classes.md#method-body). En particulier, toutes `return` les instructions dans le corps d' `get` un accesseur doivent spécifier une expression qui est implicitement convertible en type de propriété. En outre, le point de `get` terminaison d’un accesseur ne doit pas être accessible.

Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété `void` et un type de retour. Le paramètre implicite d' `set` un accesseur est `value`toujours nommé. Lorsqu’une propriété est référencée en tant que cible d’une assignation[(opérateurs d’affectation](expressions.md#assignment-operators)), ou en tant `++` qu' `--` opérande de ou ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators), [opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), l’accesseur est appelé avec un argument (dont la valeur est celle du côté droit de l’assignation ou l’opérande de l' `++` opérateur or `--` ) qui fournit la nouvelle valeur ([assignation simple](expressions.md#simple-assignment)). `set` Le corps d’un `set` accesseur doit se conformer `void` aux règles pour les méthodes décrites dans le corps de la [méthode](classes.md#method-body). En particulier, `return` les instructions dans `set` le corps de l’accesseur ne sont pas autorisées à spécifier une expression. Comme un `set` accesseur a implicitement un paramètre `value`nommé, il s’agit d’une erreur de compilation pour une variable locale ou une déclaration `set` de constante dans un accesseur portant ce nom.

En fonction de la présence ou de l' `get` absence `set` des accesseurs et, une propriété est classée comme suit :

*  Une propriété qui comprend à la `get` fois un accesseur et un `set` accesseur est considérée comme une propriété ***en lecture-écriture*** .
*  Une propriété qui a uniquement un `get` accesseur est considérée comme étant une propriété ***en lecture seule*** . Il s’agit d’une erreur de compilation pour qu’une propriété en lecture seule soit la cible d’une assignation.
*  Une propriété qui a uniquement un `set` accesseur est considérée comme étant une propriété en ***écriture seule*** . À l’exception de la cible d’une assignation, il s’agit d’une erreur au moment de la compilation pour référencer une propriété en écriture seule dans une expression.

Dans l’exemple
```csharp
public class Button: Control
{
    private string caption;

    public string Caption {
        get {
            return caption;
        }
        set {
            if (caption != value) {
                caption = value;
                Repaint();
            }
        }
    }

    public override void Paint(Graphics g, Rectangle r) {
        // Painting code goes here
    }
}
```
le `Button` contrôle déclare une propriété publique `Caption` . L' `get` accesseur de `Caption` la propriété retourne la chaîne stockée dans `caption` le champ privé. L' `set` accesseur vérifie si la nouvelle valeur est différente de la valeur actuelle, et si c’est le cas, elle stocke la nouvelle valeur et repeint le contrôle. Les propriétés suivent souvent le modèle ci-dessus : L' `get` accesseur retourne simplement une valeur stockée dans un champ privé, `set` et l’accesseur modifie ce champ privé, puis exécute toutes les actions supplémentaires requises pour mettre à jour complètement l’état de l’objet.

À partir `Button` de la classe ci-dessus, voici un exemple d’utilisation `Caption` de la propriété :
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

Ici, l' `set` accesseur est appelé en affectant une valeur à la propriété, et l' `get` accesseur est appelé en référençant la propriété dans une expression.

Les `get` accesseurs et `set` d’une propriété ne sont pas des membres distincts et il n’est pas possible de déclarer les accesseurs d’une propriété séparément. Par conséquent, il n’est pas possible que les deux accesseurs d’une propriété en lecture-écriture aient une accessibilité différente. L’exemple
```csharp
class A
{
    private string name;

    public string Name {                // Error, duplicate member name
        get { return name; }
    }

    public string Name {                // Error, duplicate member name
        set { name = value; }
    }
}
```
ne déclare pas une propriété en lecture-écriture unique. Au lieu de cela, elle déclare deux propriétés portant le même nom, une seule en lecture seule et une seule en écriture seule. Dans la mesure où deux membres déclarés dans la même classe ne peuvent pas avoir le même nom, l’exemple provoque une erreur au moment de la compilation.

Lorsqu’une classe dérivée déclare une propriété à l’aide du même nom qu’une propriété héritée, la propriété dérivée masque la propriété héritée en ce qui concerne la lecture et l’écriture. Dans l’exemple
```csharp
class A
{
    public int P {
        set {...}
    }
}

class B: A
{
    new public int P {
        get {...}
    }
}
```
la `P` propriété dans `B` masque la propriété `P` dans en `A` ce qui concerne la lecture et l’écriture. Par conséquent, dans les instructions
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
l’assignation à `b.P` provoque le signalement d’une erreur au moment de la compilation, puisque la `P` propriété en `B` lecture seule dans masque la propriété `P` en écriture `A`seule dans. Notez, toutefois, qu’un cast peut être utilisé pour accéder à la `P` propriété Hidden.

Contrairement aux champs publics, les propriétés fournissent une séparation entre l’état interne d’un objet et son interface publique. Prenons l’exemple suivant:
```csharp
class Label
{
    private int x, y;
    private string caption;

    public Label(int x, int y, string caption) {
        this.x = x;
        this.y = y;
        this.caption = caption;
    }

    public int X {
        get { return x; }
    }

    public int Y {
        get { return y; }
    }

    public Point Location {
        get { return new Point(x, y); }
    }

    public string Caption {
        get { return caption; }
    }
}
```

Ici, la `Label` classe utilise deux `int` champs, `x` et `y`, pour stocker son emplacement. L’emplacement est exposé publiquement en tant `X` que `Y` et en tant que propriété `Location` et en tant `Point`que propriété de type. Si, dans une future version de `Label`, il devient plus pratique de stocker l’emplacement `Point` en interne, la modification peut être effectuée sans affecter l’interface publique de la classe :
```csharp
class Label
{
    private Point location;
    private string caption;

    public Label(int x, int y, string caption) {
        this.location = new Point(x, y);
        this.caption = caption;
    }

    public int X {
        get { return location.x; }
    }

    public int Y {
        get { return location.y; }
    }

    public Point Location {
        get { return location; }
    }

    public string Caption {
        get { return caption; }
    }
}
```

Avait `x` `public readonly` `Label` et `y` à la place des champs, il aurait été impossible d’apporter une telle modification à la classe.

L’exposition de l’état via des propriétés n’est pas nécessairement moins efficace que l’exposition directe de champs. En particulier, lorsqu’une propriété est non virtuelle et ne contient qu’une petite quantité de code, l’environnement d’exécution peut remplacer les appels aux accesseurs par le code réel des accesseurs. Ce processus est appelé ***incorporation***, et il rend l’accès aux propriétés aussi efficace que l’accès au champ, tout en préservant la flexibilité accrue des propriétés.

Étant donné que l' `get` appel d’un accesseur est conceptuellement équivalent à la lecture de la valeur d’un champ, il `get` est considéré comme un style de programmation incorrect pour les accesseurs d’avoir des effets secondaires observables. Dans l’exemple
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
la valeur de la `Next` propriété dépend du nombre de fois où l’accès à la propriété a été effectué précédemment. Ainsi, l’accès à la propriété produit un effet secondaire observable, et la propriété doit être implémentée comme une méthode à la place.

La Convention « sans effets secondaires » pour `get` les accesseurs ne signifie pas que `get` les accesseurs doivent toujours être écrits pour renvoyer simplement des valeurs stockées dans des champs. En effet `get` , les accesseurs calculent souvent la valeur d’une propriété en accédant à plusieurs champs ou en appelant des méthodes. Toutefois, un accesseur `get` correctement conçu n’effectue aucune action qui entraîne des modifications observables de l’état de l’objet.

Les propriétés peuvent être utilisées pour différer l’initialisation d’une ressource jusqu’au moment où elle est référencée pour la première fois. Exemple :
```csharp
using System.IO;

public class Console
{
    private static TextReader reader;
    private static TextWriter writer;
    private static TextWriter error;

    public static TextReader In {
        get {
            if (reader == null) {
                reader = new StreamReader(Console.OpenStandardInput());
            }
            return reader;
        }
    }

    public static TextWriter Out {
        get {
            if (writer == null) {
                writer = new StreamWriter(Console.OpenStandardOutput());
            }
            return writer;
        }
    }

    public static TextWriter Error {
        get {
            if (error == null) {
                error = new StreamWriter(Console.OpenStandardError());
            }
            return error;
        }
    }
}
```

La `Console` classe contient trois propriétés, `In`, `Out`et `Error`, qui représentent respectivement les périphériques d’entrée, de sortie et d’erreur standard. En exposant ces membres en tant que propriétés, `Console` la classe peut différer leur initialisation jusqu’à ce qu’elles soient réellement utilisées. Par exemple, lors de la première `Out` référence à la propriété, comme dans
```csharp
Console.Out.WriteLine("hello, world");
```
le sous `TextWriter` -jacent pour le périphérique de sortie est créé. Toutefois, si l’application ne fait pas référence `In` aux `Error` propriétés et, aucun objet n’est créé pour ces appareils.

### <a name="automatically-implemented-properties"></a>Propriétés implémentées automatiquement

Une propriété implémentée automatiquement (ou ***propriété automatique*** pour Short) est une propriété non-extern non abstraite avec des corps d’accesseur de point-virgule uniquement. Les propriétés automatiques doivent avoir un accesseur get et peuvent éventuellement avoir un accesseur Set.

Quand une propriété est spécifiée en tant que propriété implémentée automatiquement, un champ de stockage masqué est automatiquement disponible pour la propriété, et les accesseurs sont implémentés pour lire et écrire dans ce champ de stockage. Si la propriété automatique n’a pas d’accesseur Set, le champ de stockage `readonly` est pris en compte ([champs en lecture seule](classes.md#readonly-fields)). Tout comme un `readonly` champ, une propriété automatique Getter uniquement peut également être assignée à dans le corps d’un constructeur de la classe englobante. Une assignation de ce type est directement assignée au champ de stockage en lecture seule de la propriété.

Une propriété automatique peut éventuellement avoir un *property_initializer*, qui est appliqué directement au champ de stockage en tant que *variable_initializer* ([initialiseurs de variable](classes.md#variable-initializers)).

L’exemple suivant :
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
équivaut à la déclaration suivante :
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

L’exemple suivant :
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
équivaut à la déclaration suivante :
```csharp
public class ReadOnlyPoint
{
    private readonly int __x;
    private readonly int __y;
    public int X { get { return __x; } }
    public int Y { get { return __y; } }
    public ReadOnlyPoint(int x, int y) { __x = x; __y = y; }
}
```

Notez que les assignations au champ ReadOnly sont autorisées, car elles se produisent dans le constructeur.


### <a name="accessibility"></a>Accessibilité

Si un accesseur a un *accessor_modifier*, le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) de l’accesseur est déterminé à l’aide de l’accessibilité déclarée de *accessor_modifier*. Si un accesseur n’a pas de *accessor_modifier*, le domaine d’accessibilité de l’accesseur est déterminé à partir de l’accessibilité déclarée de la propriété ou de l’indexeur.

La présence d’un *accessor_modifier* n’affecte jamais la recherche de membre ([opérateurs](expressions.md#operators)) ou la résolution de surcharge ([résolution de surcharge](expressions.md#overload-resolution)). Les modificateurs de la propriété ou de l’indexeur déterminent toujours la propriété ou l’indexeur auquel il est lié, quel que soit le contexte de l’accès.

Une fois qu’une propriété ou un indexeur particulier a été sélectionné, les domaines d’accessibilité des accesseurs spécifiques impliqués sont utilisés pour déterminer si cette utilisation est valide :

*  Si l’utilisation est en tant que valeur ([valeurs d’expressions](expressions.md#values-of-expressions)), `get` l’accesseur doit exister et être accessible.
*  Si l’utilisation est la cible d’une assignation simple ([assignation simple](expressions.md#simple-assignment)), l' `set` accesseur doit exister et être accessible.
*  Si l’utilisation est la cible de l’assignation composée ([assignation composée](expressions.md#compound-assignment)), ou en tant que cible des `++` opérateurs `--` ou ([fonctions membres](expressions.md#function-members) `get` 0,9, [expressions d’appel](expressions.md#invocation-expressions)), les accesseurs et l' `set` accesseur doit exister et être accessible.

Dans l’exemple suivant, la propriété `A.Text` est masquée par la `B.Text`propriété, même dans les contextes où `set` seul l’accesseur est appelé. En revanche, la propriété `B.Count` n’est pas accessible à `M`la classe, donc la `A.Count` propriété accessible est utilisée à la place.

```csharp
class A
{
    public string Text {
        get { return "hello"; }
        set { }
    }

    public int Count {
        get { return 5; }
        set { }
    }
}

class B: A
{
    private string text = "goodbye"; 
    private int count = 0;

    new public string Text {
        get { return text; }
        protected set { text = value; }
    }

    new protected int Count { 
        get { return count; }
        set { count = value; }
    }
}

class M
{
    static void Main() {
        B b = new B();
        b.Count = 12;             // Calls A.Count set accessor
        int i = b.Count;          // Calls A.Count get accessor
        b.Text = "howdy";         // Error, B.Text set accessor not accessible
        string s = b.Text;        // Calls B.Text get accessor
    }
}
```

Un accesseur utilisé pour implémenter une interface peut ne pas avoir de *accessor_modifier*. Si un seul accesseur est utilisé pour implémenter une interface, l’autre accesseur peut être déclaré avec un *accessor_modifier*:
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public string Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a>Accesseurs de propriété Virtual, sealed, override et abstract

Une `virtual` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels. Le `virtual` modificateur s’applique aux deux accesseurs d’une propriété en lecture-écriture ; il n’est pas possible pour un seul accesseur d’une propriété en lecture-écriture d’être virtuel.

Une `abstract` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels, mais ne fournissent pas d’implémentation réelle des accesseurs. Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation des accesseurs en substituant la propriété. Comme un accesseur pour une déclaration de propriété abstraite ne fournit pas d’implémentation réelle, son *accessor_body* se compose simplement d’un point-virgule.

Une déclaration de propriété qui comprend à `abstract` la `override` fois les modificateurs et spécifie que la propriété est abstraite et substitue une propriété de base. Les accesseurs d’une telle propriété sont également abstraits.

Les déclarations de propriété abstraite sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)). Les accesseurs d’une propriété virtuelle héritée peuvent être substitués dans une classe dérivée en incluant une déclaration de propriété `override` qui spécifie une directive. C’est ce qu’on appelle une ***déclaration de propriété de substitution***. Une déclaration de propriété de substitution ne déclare pas de nouvelle propriété. Au lieu de cela, il spécialise simplement les implémentations des accesseurs d’une propriété virtuelle existante.

Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité, le même type et le même nom que la propriété héritée. Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur. Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs.

Une déclaration de propriété de substitution peut inclure `sealed` le modificateur. L’utilisation de ce modificateur empêche une classe dérivée de substituer davantage la propriété. Les accesseurs d’une propriété sealed sont également sealed.

À l’exception des différences dans la syntaxe de déclaration et d’appel, les accesseurs Virtual, sealed, override et abstract se comportent exactement comme des méthodes virtuelles, sealed, override et abstract. Plus précisément, les règles décrites dans les [méthodes virtuelles](classes.md#virtual-methods), les [méthodes override](classes.md#override-methods), les [méthodes sealed](classes.md#sealed-methods)et les [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si les accesseurs étaient des méthodes d’un formulaire correspondant :

*  Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété et les mêmes modificateurs que la propriété conteneur.
*  Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété `void` , un type de retour et les mêmes modificateurs que la propriété conteneur.

Dans l’exemple
```csharp
abstract class A
{
    int y;

    public virtual int X {
        get { return 0; }
    }

    public virtual int Y {
        get { return y; }
        set { y = value; }
    }

    public abstract int Z { get; set; }
}
```
`X`est une propriété en lecture seule virtuelle, `Y` est une propriété en lecture-écriture virtuelle et `Z` est une propriété en lecture-écriture abstraite. Étant `Z` donné que est abstrait, la `A` classe conteneur doit également être déclarée abstract.

Une classe qui dérive de `A` est affichée ci-dessous :
```csharp
class B: A
{
    int z;

    public override int X {
        get { return base.X + 1; }
    }

    public override int Y {
        set { base.Y = value < 0? 0: value; }
    }

    public override int Z {
        get { return z; }
        set { z = value; }
    }
}
```

Ici, les déclarations de `X`, `Y`et `Z` remplacent les déclarations de propriété. Chaque déclaration de propriété correspond exactement aux modificateurs d’accessibilité, au type et au nom de la propriété héritée correspondante. L' `get` accesseur `X` de et `set` l’accesseur de `base` `Y` utilisent le mot clé pour accéder aux accesseurs hérités. La déclaration de `Z` substitue les deux accesseurs abstraits. par conséquent, il n’existe aucun membre de `B`fonction abstraite en attente dans, et `B` il est autorisé à être une classe non abstraite.

Quand une propriété est déclarée en `override`tant que, tout accesseur substitué doit être accessible au code de substitution. En outre, l’accessibilité déclarée de la propriété ou de l’indexeur lui-même, et des accesseurs, doit correspondre à celle du membre et des accesseurs substitués. Exemple :
```csharp
public class B
{
    public virtual int P {
        protected set {...}
        get {...}
    }
}

public class D: B
{
    public override int P {
        protected set {...}            // Must specify protected here
        get {...}                      // Must not have a modifier here
    }
}
```

## <a name="events"></a>Events

Un ***événement*** est un membre qui permet à un objet ou à une classe de fournir des notifications. Les clients peuvent joindre du code exécutable pour les événements en fournissant des ***gestionnaires d’événements***.

Les événements sont déclarés à l’aide de *event_declaration*s :

```antlr
event_declaration
    : attributes? event_modifier* 'event' type variable_declarators ';'
    | attributes? event_modifier* 'event' type member_name '{' event_accessor_declarations '}'
    ;

event_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | event_modifier_unsafe
    ;

event_accessor_declarations
    : add_accessor_declaration remove_accessor_declaration
    | remove_accessor_declaration add_accessor_declaration
    ;

add_accessor_declaration
    : attributes? 'add' block
    ;

remove_accessor_declaration
    : attributes? 'remove' block
    ;
```

Un *event_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).

Les déclarations d’événements sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs.

Le *type* d’une déclaration d’événement doit être un *delegate_type* ([types référence](types.md#reference-types)) et *delegate_type* doit être au moins aussi accessible que l’événement lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Une déclaration d’événement peut inclure *event_accessor_declarations*. Toutefois, si ce n’est pas le cas pour les événements non-extern et non abstraits, le compilateur les fournit automatiquement ([événements de type champ](classes.md#field-like-events)); pour les événements extern, les accesseurs sont fournis en externe.

Une déclaration d’événement qui omet *event_accessor_declarations* définit un ou plusieurs événements, un pour chacun des *variable_declarator*s. Les attributs et les modificateurs s’appliquent à tous les membres déclarés par un *event_declaration*.

Il s’agit d’une erreur au moment de la compilation pour qu’un *event_declaration* inclue à la fois le modificateur `abstract` et les *event_accessor_declarations*délimités par des accolades.

Lorsqu’une déclaration Event comprend un `extern` modificateur, l’événement est considéré comme un ***événement externe***. Étant donné qu’une déclaration d’événement externe ne fournit aucune implémentation réelle, il y a une erreur pour qu’elle inclue à la fois le modificateur `extern` et *event_accessor_declarations*.

Il s’agit d’une erreur de compilation pour un *variable_declarator* d’une déclaration d’événement avec un modificateur `abstract` ou `external` pour inclure un *variable_initializer*.

Un événement peut être utilisé comme opérande de gauche des opérateurs `+=` et `-=` (assignation d'[événement](expressions.md#event-assignment)). Ces opérateurs sont utilisés, respectivement, pour attacher des gestionnaires d’événements à ou pour supprimer des gestionnaires d’événements d’un événement, et les modificateurs d’accès de l’événement contrôlent les contextes dans lesquels ces opérations sont autorisées.

Étant `+=` donné `-=` que et sont les seules opérations autorisées sur un événement à l’extérieur du type qui déclare l’événement, le code externe peut ajouter et supprimer des gestionnaires pour un événement, mais ne peut pas obtenir ou modifier la liste sous-jacente de l’événement. gestionnaires d'.

Dans une opération de la forme `x += y` ou `x -= y`, quand `x` est un événement et que la référence a lieu à l’extérieur du type qui contient `x`la déclaration de, le résultat de l' `void` opération a le type (par opposition à having type de `x`, avec la valeur de `x` après l’assignation. Cette règle interdit au code externe d’examiner indirectement le délégué sous-jacent d’un événement.

L’exemple suivant montre comment les gestionnaires d’événements sont attachés aux instances `Button` de la classe :
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;
}

public class LoginDialog: Form
{
    Button OkButton;
    Button CancelButton;

    public LoginDialog() {
        OkButton = new Button(...);
        OkButton.Click += new EventHandler(OkButtonClick);
        CancelButton = new Button(...);
        CancelButton.Click += new EventHandler(CancelButtonClick);
    }

    void OkButtonClick(object sender, EventArgs e) {
        // Handle OkButton.Click event
    }

    void CancelButtonClick(object sender, EventArgs e) {
        // Handle CancelButton.Click event
    }
}
```

Ici, le `LoginDialog` constructeur d’instance crée `Button` deux instances et attache des gestionnaires d' `Click` événements aux événements.

### <a name="field-like-events"></a>Événements de type champ

Dans le texte de programme de la classe ou du struct qui contient la déclaration d’un événement, certains événements peuvent être utilisés comme des champs. Pour être utilisée de cette manière, un événement ne doit pas être `abstract` ou `extern`, et ne doit pas inclure explicitement *event_accessor_declarations*. Un tel événement peut être utilisé dans n’importe quel contexte qui autorise un champ. Le champ contient un délégué ([délégués](delegates.md)) qui fait référence à la liste des gestionnaires d’événements qui ont été ajoutés à l’événement. Si aucun gestionnaire d’événements n’a été ajouté, le champ `null`contient.

Dans l’exemple
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;

    protected void OnClick(EventArgs e) {
        if (Click != null) Click(this, e);
    }

    public void Reset() {
        Click = null;
    }
}
```
`Click`est utilisé en tant que champ dans `Button` la classe. Comme le montre l’exemple, le champ peut être examiné, modifié et utilisé dans les expressions d’appel de délégué. La `OnClick` méthode de la `Button` classe « déclenche » l' `Click` événement. La notion de déclenchement d’un événement est équivalente à l’appel de délégué représenté par l’événement. Par conséquent, il n’existe aucune construction de langage particulière pour déclencher des événements. Notez que l’appel de délégué est précédé d’une vérification qui garantit que le délégué est non null.

En dehors de la Déclaration `Button` de la classe `Click` , le membre peut uniquement être utilisé sur le côté gauche des `+=` opérateurs et `-=` , comme dans
```csharp
b.Click += new EventHandler(...);
```
qui ajoute un délégué à la liste d’appel de l' `Click` événement, et
```csharp
b.Click -= new EventHandler(...);
```
qui supprime un délégué de la liste d’appel de l' `Click` événement.

Lors de la compilation d’un événement de type champ, le compilateur crée automatiquement le stockage pour contenir le délégué et crée des accesseurs pour l’événement qui ajoute ou supprime des gestionnaires d’événements dans le champ délégué. Les opérations d’ajout et de suppression sont thread-safe et peuvent (mais n’ont pas besoin d’être) effectuées tout en détenant le verrou ([l’instruction lock](statements.md#the-lock-statement)) sur l’objet conteneur d’un événement d’instance, ou le type Object ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) pour un événement statique.

Ainsi, une déclaration d’événement d’instance de la forme :
```csharp
class X
{
    public event D Ev;
}
```
sera compilé en une valeur équivalente à :
```csharp
class X
{
    private D __Ev;  // field to hold the delegate

    public event D Ev {
        add {
            /* add the delegate in a thread safe way */
        }

        remove {
            /* remove the delegate in a thread safe way */
        }
    }
}
```
Dans la classe `X`, les références `Ev` à sur le côté gauche des `+=` opérateurs et `-=` entraînent l’appel des accesseurs Add et Remove. Toutes les autres références `Ev` à sont compilées pour référencer `__Ev` le champ masqué à la place ([accès aux membres](expressions.md#member-access)). Le nom «`__Ev`» est arbitraire ; le champ masqué peut avoir n’importe quel nom ou aucun nom.

### <a name="event-accessors"></a>Accesseurs d’événement

Les déclarations d’événements omettent généralement *event_accessor_declarations*, comme dans l’exemple `Button` ci-dessus. Une situation pour cela implique le cas où le coût de stockage d’un champ par événement n’est pas acceptable. Dans ce cas, une classe peut inclure des *event_accessor_declarations* et utiliser un mécanisme privé pour stocker la liste des gestionnaires d’événements.

Le *event_accessor_declarations* d’un événement spécifie les instructions exécutables associées à l’ajout et à la suppression de gestionnaires d’événements.

Les déclarations d’accesseur se composent d’un *add_accessor_declaration* et d’un *remove_accessor_declaration*. Chaque déclaration d’accesseur se compose `add` du `remove` jeton ou suivi d’un *bloc*. Le *bloc* associé à un *add_accessor_declaration* spécifie les instructions à exécuter lors de l’ajout d’un gestionnaire d’événements, et le *bloc* associé à un *remove_accessor_declaration* spécifie les instructions à exécuter. Lorsqu’un gestionnaire d’événements est supprimé.

Chaque *add_accessor_declaration* et *remove_accessor_declaration* correspond à une méthode avec un paramètre de valeur unique du type d’événement et un type de retour `void`. Le paramètre implicite d’un accesseur d' `value`événement est nommé. Lorsqu’un événement est utilisé dans une assignation d’événement, l’accesseur d’événement approprié est utilisé. Plus précisément, si l’opérateur d' `+=` assignation est, l’accesseur Add est utilisé, et si l' `-=` opérateur d’assignation est, l’accesseur remove est utilisé. Dans les deux cas, l’opérande droit de l’opérateur d’assignation est utilisé comme argument de l’accesseur d’événement. Le bloc d’un *add_accessor_declaration* ou d’un *remove_accessor_declaration* doit respecter les règles pour les méthodes `void` décrites dans le corps de la [méthode](classes.md#method-body). En particulier, `return` les instructions d’un tel bloc ne sont pas autorisées à spécifier une expression.

Comme un accesseur d’événement a implicitement un `value`paramètre nommé, il s’agit d’une erreur de compilation pour une variable locale ou une constante déclarée dans un accesseur d’événement pour avoir ce nom.

Dans l’exemple
```csharp
class Control: Component
{
    // Unique keys for events
    static readonly object mouseDownEventKey = new object();
    static readonly object mouseUpEventKey = new object();

    // Return event handler associated with key
    protected Delegate GetEventHandler(object key) {...}

    // Add event handler associated with key
    protected void AddEventHandler(object key, Delegate handler) {...}

    // Remove event handler associated with key
    protected void RemoveEventHandler(object key, Delegate handler) {...}

    // MouseDown event
    public event MouseEventHandler MouseDown {
        add { AddEventHandler(mouseDownEventKey, value); }
        remove { RemoveEventHandler(mouseDownEventKey, value); }
    }

    // MouseUp event
    public event MouseEventHandler MouseUp {
        add { AddEventHandler(mouseUpEventKey, value); }
        remove { RemoveEventHandler(mouseUpEventKey, value); }
    }

    // Invoke the MouseUp event
    protected void OnMouseUp(MouseEventArgs args) {
        MouseEventHandler handler; 
        handler = (MouseEventHandler)GetEventHandler(mouseUpEventKey);
        if (handler != null)
            handler(this, args);
    }
}
```
la `Control` classe implémente un mécanisme de stockage interne pour les événements. La `AddEventHandler` méthode associe une valeur de délégué à une clé `GetEventHandler` , la méthode retourne le délégué actuellement associé à une clé, `RemoveEventHandler` et la méthode supprime un délégué en tant que gestionnaire d’événements pour l’événement spécifié. Vraisemblablement, le mécanisme de stockage sous-jacent est conçu de telle sorte qu’il n’y `null` a aucun coût d’association d’une valeur de délégué à une clé, et par conséquent, les événements non gérés ne consomment aucun stockage.

### <a name="static-and-instance-events"></a>Événements statiques et d’instance

Lorsqu’une déclaration Event comprend un `static` modificateur, l’événement est considéré comme un ***événement statique***. Si aucun `static` modificateur n’est présent, l’événement est considéré comme un ***événement d’instance***.

Un événement statique n’est pas associé à une instance spécifique, et il s’agit d’une erreur de compilation pour `this` faire référence aux accesseurs d’un événement statique.

Un événement d’instance est associé à une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)) dans les accesseurs de cet événement.

Lorsqu’un événement est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est un événement statique, `E` doit désigner un type contenant `M`, et si `M` est un événement d’instance, E doit désigner une instance d’un type contenant `M`.

Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a>Accesseurs d’événement virtuels, sealed, override et abstract

Une `virtual` déclaration d’événement spécifie que les accesseurs de cet événement sont virtuels. Le `virtual` modificateur s’applique aux deux accesseurs d’un événement.

Une `abstract` déclaration d’événement spécifie que les accesseurs de l’événement sont virtuels, mais ne fournissent pas d’implémentation réelle des accesseurs. Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation des accesseurs en substituant l’événement. Étant donné qu’une déclaration d’événement abstraite ne fournit pas d’implémentation réelle, elle ne peut pas fournir des *event_accessor_declarations*délimités par des accolades.

Une déclaration d’événement qui comprend à `abstract` la `override` fois les modificateurs et spécifie que l’événement est abstrait et substitue un événement de base. Les accesseurs d’un tel événement sont également abstraits.

Les déclarations d’événements abstract sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).

Les accesseurs d’un événement virtuel hérité peuvent être substitués dans une classe dérivée en incluant une déclaration d’événement qui `override` spécifie un modificateur. C’est ce qu’on appelle une ***déclaration d’événement de substitution***. Une déclaration d’événement de substitution ne déclare pas un nouvel événement. Au lieu de cela, il spécialise simplement les implémentations des accesseurs d’un événement virtuel existant.

Une déclaration d’événement de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité, le même type et le même nom que l’événement substitué.

Une déclaration d’événement de substitution peut inclure `sealed` le modificateur. L’utilisation de ce modificateur empêche une classe dérivée de substituer davantage l’événement. Les accesseurs d’un événement sealed sont également sealed.

Il s’agit d’une erreur au moment de la compilation pour qu’une déclaration d' `new` événement de substitution inclue un modificateur.

À l’exception des différences dans la syntaxe de déclaration et d’appel, les accesseurs Virtual, sealed, override et abstract se comportent exactement comme des méthodes virtuelles, sealed, override et abstract. Plus précisément, les règles décrites dans les [méthodes virtuelles](classes.md#virtual-methods), les [méthodes override](classes.md#override-methods), les [méthodes sealed](classes.md#sealed-methods)et les [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si les accesseurs étaient des méthodes d’un formulaire correspondant. Chaque accesseur correspond à une méthode avec un paramètre de valeur unique du type d’événement `void` , un type de retour et les mêmes modificateurs que l’événement conteneur.

## <a name="indexers"></a>Indexeurs

Un ***indexeur*** est un membre qui permet à un objet d’être indexé de la même manière qu’un tableau. Les indexeurs sont déclarés à l’aide de *indexer_declaration*s :

```antlr
indexer_declaration
    : attributes? indexer_modifier* indexer_declarator indexer_body
    ;

indexer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | indexer_modifier_unsafe
    ;

indexer_declarator
    : type 'this' '[' formal_parameter_list ']'
    | type interface_type '.' 'this' '[' formal_parameter_list ']'
    ;

indexer_body
    : '{' accessor_declarations '}' 
    | '=>' expression ';'
    ;
```

Un *indexer_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `virtual` ([méthodes virtuelles ](classes.md#virtual-methods)), `override` ([méthodes override](classes.md#override-methods)), 0 ([méthodes sealed](classes.md#sealed-methods)), 2 ([méthodes abstraites](classes.md#abstract-methods)) et les modificateurs 4 ([méthodes externes](classes.md#external-methods)).

Les déclarations de l’indexeur sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs, à la seule exception que le modificateur static n’est pas autorisé sur une déclaration d’indexeur.

Les modificateurs `virtual`, `override`et `abstract` s’excluent mutuellement, sauf dans un cas. Les `abstract` modificateurs et `override` peuvent être utilisés ensemble afin qu’un indexeur abstrait puisse remplacer un indexeur virtuel.

Le *type* d’une déclaration d’indexeur spécifie le type d’élément de l’indexeur introduit par la déclaration. À moins que l’indexeur ne soit une implémentation de membre d’interface explicite, le type `this`est suivi du mot clé. Pour une implémentation de membre d’interface explicite, le *type* est suivi d’un *INTERFACE_TYPE*, d’un « `.` » et du mot clé `this`. Contrairement aux autres membres, les indexeurs n’ont pas de noms définis par l’utilisateur.

*Formal_parameter_list* spécifie les paramètres de l’indexeur. La liste de paramètres formels d’un indexeur correspond à celle d’une méthode ([paramètres de méthode](classes.md#method-parameters)), à ceci près qu’au moins un paramètre doit être `ref` spécifié `out` et que les modificateurs de paramètres et ne sont pas autorisés.

Le *type* d’un indexeur et chacun des types référencés dans le *formal_parameter_list* doivent être au moins aussi accessibles que l’indexeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Un *indexer_body* peut être constitué d’un ***corps d’accesseur*** ou d’un ***corps d’expression***. Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre les jetons « `{` » et « `}` », déclare les accesseurs ([accesseurs](classes.md#accessors)) de la propriété. Les accesseurs spécifient les instructions exécutables associées à la lecture et à l’écriture de la propriété.

Un corps d’expression composé de`=>`«» suivi d’une `E` expression et d’un point-virgule est exactement équivalent `{ get { return E; } }`au corps de l’instruction et ne peut donc être utilisé que pour spécifier des indexeurs Getter uniquement où le résultat de l’accesseur Get est fourni par une expression unique.

Même si la syntaxe d’accès à un élément d’indexeur est identique à celle d’un élément de tableau, un élément d’indexeur n’est pas classifié comme une variable. Par conséquent, il n’est pas possible de passer un élément d’indexeur `out` en tant qu' `ref` argument ou.

La liste de paramètres formels d’un indexeur définit la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de l’indexeur. Plus précisément, la signature d’un indexeur se compose du nombre et des types de ses paramètres formels. Le type d’élément et les noms des paramètres formels ne font pas partie de la signature d’un indexeur.

La signature d’un indexeur doit être différente des signatures de tous les autres indexeurs déclarés dans la même classe.

Les indexeurs et les propriétés sont très similaires, mais diffèrent des façons suivantes :

*  Une propriété est identifiée par son nom, tandis qu’un indexeur est identifié par sa signature.
*  Une propriété est accessible par le biais d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)), tandis qu’un élément d’indexeur est accessible via un *element_access* (accès à l'[indexeur](expressions.md#indexer-access)).
*  Une propriété peut être un `static` membre, alors qu’un indexeur est toujours un membre d’instance.
*  Un `get` accesseur d’une propriété correspond à une méthode sans paramètres, alors qu' `get` un accesseur d’un indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur.
*  Un `set` accesseur d’une propriété correspond à une méthode avec un paramètre unique `value`nommé, alors `set` qu’un accesseur d’un indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur, plus un paramètre supplémentaire nommé `value`.
*  Il s’agit d’une erreur de compilation permettant à un accesseur d’indexeur de déclarer une variable locale portant le même nom qu’un paramètre d’indexeur.
*  Dans une déclaration de propriété de substitution, la propriété héritée est accessible à `base.P`l’aide `P` de la syntaxe, où est le nom de la propriété. Dans une déclaration d’indexeur de substitution, l’indexeur hérité est accessible à l' `base[E]`aide de `E` la syntaxe, où est une liste d’expressions séparées par des virgules.
*  Il n’existe aucun concept d’indexeur implémenté automatiquement. Il s’agit d’une erreur d’avoir un indexeur non abstrait non externe avec des accesseurs de point-virgule.

Hormis ces différences, toutes les règles définies dans les [accesseurs](classes.md#accessors) et les [Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties) s’appliquent aux accesseurs d’indexeur ainsi qu’aux accesseurs de propriété.

Lorsqu’une déclaration d’indexeur comprend `extern` un modificateur, l’indexeur est considéré comme un ***indexeur externe***. Étant donné qu’une déclaration d’indexeur externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.

L’exemple ci-dessous déclare `BitArray` une classe qui implémente un indexeur pour accéder aux bits individuels dans le tableau de bits.
```csharp
using System;

class BitArray
{
    int[] bits;
    int length;

    public BitArray(int length) {
        if (length < 0) throw new ArgumentException();
        bits = new int[((length - 1) >> 5) + 1];
        this.length = length;
    }

    public int Length {
        get { return length; }
    }

    public bool this[int index] {
        get {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            return (bits[index >> 5] & 1 << index) != 0;
        }
        set {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            if (value) {
                bits[index >> 5] |= 1 << index;
            }
            else {
                bits[index >> 5] &= ~(1 << index);
            }
        }
    }
}
```

Une instance de la `BitArray` classe consomme sensiblement moins de mémoire qu’une `bool[]` correspondante (puisque chaque valeur du premier n’occupe qu’un seul bit au lieu du dernier octet), mais elle autorise les mêmes opérations qu’un `bool[]`.

La classe `CountPrimes` suivante utilise un `BitArray` et l’algorithme « crible » classique pour calculer le nombre de premiers compris entre 1 et un maximum donné :
```csharp
class CountPrimes
{
    static int Count(int max) {
        BitArray flags = new BitArray(max + 1);
        int count = 1;
        for (int i = 2; i <= max; i++) {
            if (!flags[i]) {
                for (int j = i * 2; j <= max; j += i) flags[j] = true;
                count++;
            }
        }
        return count;
    }

    static void Main(string[] args) {
        int max = int.Parse(args[0]);
        int count = Count(max);
        Console.WriteLine("Found {0} primes between 1 and {1}", count, max);
    }
}
```

Notez que la syntaxe pour accéder aux éléments de `BitArray` est exactement la même que pour un. `bool[]`

L’exemple suivant montre une classe de grille 26 * 10 qui a un indexeur avec deux paramètres. Le premier paramètre doit être une lettre majuscule ou minuscule dans la plage A-Z, tandis que le second doit être un entier compris dans la plage 0-9.

```csharp
using System;

class Grid
{
    const int NumRows = 26;
    const int NumCols = 10;

    int[,] cells = new int[NumRows, NumCols];

    public int this[char c, int col] {
        get {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            return cells[c - 'A', col];
        }

        set {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            cells[c - 'A', col] = value;
        }
    }
}
```

### <a name="indexer-overloading"></a>Surcharge de l’indexeur

Les règles de résolution de surcharge de l’indexeur sont décrites dans [inférence de type](expressions.md#type-inference).

## <a name="operators"></a>Opérateurs

Un ***opérateur*** est un membre qui définit la signification d’un opérateur d’expression qui peut être appliqué aux instances de la classe. Les opérateurs sont déclarés à l’aide de *operator_declaration*s :

```antlr
operator_declaration
    : attributes? operator_modifier+ operator_declarator operator_body
    ;

operator_modifier
    : 'public'
    | 'static'
    | 'extern'
    | operator_modifier_unsafe
    ;

operator_declarator
    : unary_operator_declarator
    | binary_operator_declarator
    | conversion_operator_declarator
    ;

unary_operator_declarator
    : type 'operator' overloadable_unary_operator '(' type identifier ')'
    ;

overloadable_unary_operator
    : '+' | '-' | '!' | '~' | '++' | '--' | 'true' | 'false'
    ;

binary_operator_declarator
    : type 'operator' overloadable_binary_operator '(' type identifier ',' type identifier ')'
    ;

overloadable_binary_operator
    : '+'   | '-'   | '*'   | '/'   | '%'   | '&'   | '|'   | '^'   | '<<'
    | right_shift | '=='  | '!='  | '>'   | '<'   | '>='  | '<='
    ;

conversion_operator_declarator
    : 'implicit' 'operator' type '(' type identifier ')'
    | 'explicit' 'operator' type '(' type identifier ')'
    ;

operator_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

Il existe trois catégories d’opérateurs surchargeables : Opérateurs unaires ([opérateurs unaires](classes.md#unary-operators)), opérateurs binaires ([opérateurs binaires](classes.md#binary-operators)) et opérateurs de conversion ([opérateurs de conversion](classes.md#conversion-operators)).

*Operator_body* est un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***. Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque l’opérateur est appelé. Le *bloc* doit se conformer aux règles pour les méthodes qui retournent des valeurs décrites dans le corps de la [méthode](classes.md#method-body). Un corps d’expression se `=>` compose de suivis d’une expression et d’un point-virgule, et désigne une expression unique à exécuter lorsque l’opérateur est appelé.

Pour les opérateurs `extern`, *operator_body* se compose simplement d’un point-virgule. Pour tous les autres opérateurs, *operator_body* est soit un corps de bloc, soit un corps d’expression.

Les règles suivantes s’appliquent à toutes les déclarations d’opérateur :

*  Une déclaration d’opérateur doit inclure à `public` la fois `static` un et un modificateur.
*  Le (s) paramètre (s) d’un opérateur doivent être des paramètres de valeur ([paramètres de valeur](variables.md#value-parameters)). Il s’agit d’une erreur de compilation pour qu’une déclaration d' `ref` opérateur `out` spécifie des paramètres ou.
*  La signature d’un opérateur ([opérateurs unaires](classes.md#unary-operators), [opérateurs binaires](classes.md#binary-operators), [opérateurs de conversion](classes.md#conversion-operators)) doit être différente des signatures de tous les autres opérateurs déclarés dans la même classe.
*  Tous les types référencés dans une déclaration d’opérateur doivent être au moins aussi accessibles que l’opérateur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).
*  Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration d’opérateur.

Chaque catégorie d’opérateur impose des restrictions supplémentaires, comme décrit dans les sections suivantes.

Comme les autres membres, les opérateurs déclarés dans une classe de base sont hérités par les classes dérivées. Étant donné que les déclarations d’opérateur requièrent toujours la classe ou le struct dans lequel l’opérateur est déclaré pour participer à la signature de l’opérateur, il n’est pas possible pour un opérateur déclaré dans une classe dérivée de masquer un opérateur déclaré dans une classe de base. Ainsi, le `new` modificateur n’est jamais requis et, par conséquent, n’est jamais autorisé dans une déclaration d’opérateur.

Vous trouverez des informations supplémentaires sur les opérateurs unaires et binaires dans [opérateurs](expressions.md#operators).

Vous trouverez des informations supplémentaires sur les opérateurs de conversion dans [les conversions définies par l’utilisateur](conversions.md#user-defined-conversions).

### <a name="unary-operators"></a>Les opérateurs unaires.

Les règles suivantes s’appliquent aux déclarations d’opérateur `T` unaires, où désigne le type d’instance de la classe ou du struct qui contient la déclaration d’opérateur :

*  Un opérateur `+` `-`unaire `~` `T` ,, ou doit accepter un seul paramètre de type ou `T?` et peut retourner n’importe quel type. `!`
*  Un opérateur `++` or `--` unaire doit accepter un seul paramètre de `T` type `T?` ou et doit retourner ce même type ou un type dérivé de celui-ci.
*  Un opérateur `true` or `false` unaire doit accepter un seul paramètre de `T` type `T?` ou et doit retourner `bool`le type.

La signature d’un opérateur unaire se compose du jeton d'`+`opérateur `-`( `!`, `~`, `++`, `--`, `true`,, `false`ou) et du type du paramètre formel unique. Le type de retour ne fait pas partie de la signature d’un opérateur unaire, ni du nom du paramètre formel.

Les `true` opérateurs `false` unaires et requièrent une déclaration de couple. Une erreur de compilation se produit si une classe déclare l’un de ces opérateurs sans également déclarer l’autre. Les `true` opérateurs `false` et sont décrits plus en détail dans les [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators) et les [expressions booléennes](expressions.md#boolean-expressions).

L’exemple suivant illustre une implémentation et une utilisation ultérieure `operator ++` de pour une classe Vector entière :
```csharp
public class IntVector
{
    public IntVector(int length) {...}

    public int Length {...}                 // read-only property

    public int this[int index] {...}        // read-write indexer

    public static IntVector operator ++(IntVector iv) {
        IntVector temp = new IntVector(iv.Length);
        for (int i = 0; i < iv.Length; i++)
            temp[i] = iv[i] + 1;
        return temp;
    }
}

class Test
{
    static void Main() {
        IntVector iv1 = new IntVector(4);    // vector of 4 x 0
        IntVector iv2;

        iv2 = iv1++;    // iv2 contains 4 x 0, iv1 contains 4 x 1
        iv2 = ++iv1;    // iv2 contains 4 x 2, iv1 contains 4 x 2
    }
}
```

Notez comment la méthode d’opérateur retourne la valeur produite par l’ajout de 1 à l’opérande, tout comme les opérateurs d’incrémentation et de décrémentation suffixés ([opérateurs d’incrémentation et de décrémentation suffixés](expressions.md#postfix-increment-and-decrement-operators)) et les opérateurs d’incrémentation et de décrémentation de préfixe ([préfixe opérateurs d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)). Contrairement à C++, cette méthode n’a pas besoin de modifier directement la valeur de son opérande. En fait, la modification de la valeur de l’opérande violerait la sémantique standard de l’opérateur d’incrémentation suffixée.

### <a name="binary-operators"></a>Opérateurs binaires

Les règles suivantes s’appliquent aux déclarations d’opérateur `T` binaire, où dénote le type d’instance de la classe ou du struct qui contient la déclaration d’opérateur :

*  Un opérateur binaire sans décalage doit accepter deux paramètres, au moins un qui doit avoir le type `T` ou `T?`, et peut retourner tout type.
*  Un binaire `<<` ou `>>` un opérateur doit accepter deux paramètres, le premier qui doit avoir le `T` type `T?` ou et le second, le type `int` ou `int?`, et peut retourner n’importe quel type.

La signature d’un opérateur binaire se compose du jeton d’opérateur`+`( `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`,, `==`, `!=`, ,,`>`ou )etlestypesdesdeux`<=`paramètres formels. `<` `>=` Le type de retour et les noms des paramètres formels ne font pas partie de la signature d’un opérateur binaire.

Certains opérateurs binaires requièrent une déclaration de couple. Pour chaque déclaration de l’un des opérateurs d’une paire, il doit y avoir une déclaration correspondante de l’autre opérateur de la paire. Deux déclarations d’opérateur correspondent lorsqu’elles ont le même type de retour et le même type pour chaque paramètre. Les opérateurs suivants requièrent une déclaration de paires :

*  `operator ==` et `operator !=`
*  `operator >` et `operator <`
*  `operator >=` et `operator <=`

### <a name="conversion-operators"></a>Opérateurs de conversion

Une déclaration d’opérateur de conversion introduit une ***conversion définie par l’utilisateur*** ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) qui augmente les conversions implicites et explicites prédéfinies.

Une déclaration d’opérateur de conversion qui `implicit` inclut le mot clé introduit une conversion implicite définie par l’utilisateur. Les conversions implicites peuvent se produire dans diverses situations, y compris les appels de membres de fonction, les expressions de Cast et les assignations. Cela est décrit plus en détail dans [conversions implicites](conversions.md#implicit-conversions).

Une déclaration d’opérateur de conversion qui `explicit` inclut le mot clé introduit une conversion explicite définie par l’utilisateur. Les conversions explicites peuvent se produire dans les expressions de Cast et sont décrites plus en détail dans les [conversions explicites](conversions.md#explicit-conversions).

Un opérateur de conversion convertit un type source, indiqué par le type de paramètre de l’opérateur de conversion, en un type cible, indiqué par le type de retour de l’opérateur de conversion.

Pour un `S` type de source et un type `T`cible donnés, `T` si `S` ou sont des types `S0` Nullable `T0` , Let et font référence à leurs `S0` types `T0` sous-jacents ; sinon, et sont égal à `S` et `T` , respectivement. Une classe ou un struct est autorisé à déclarer une conversion d’un type `S` source en type `T` cible uniquement si toutes les conditions suivantes sont vraies :

*  `S0`et `T0` sont des types différents.
*  `S0` Ou`T0` est le type de classe ou de struct dans lequel la déclaration d’opérateur a lieu.
*  Ni `S0`, ni `T0` n’est un *INTERFACE_TYPE*.
*  En excluant les conversions définies par l’utilisateur, il n' `S` existe `T` pas de conversion `S`de vers ou de `T` à.

Dans le cadre de ces règles, tous les paramètres de type `S` associés `T` à ou sont considérés comme des types uniques qui n’ont aucune relation d’héritage avec d’autres types, et toutes les contraintes sur ces paramètres de type sont ignorées.

Dans l’exemple
```csharp
class C<T> {...}

class D<T>: C<T>
{
    public static implicit operator C<int>(D<T> value) {...}      // Ok
    public static implicit operator C<string>(D<T> value) {...}   // Ok
    public static implicit operator C<T>(D<T> value) {...}        // Error
}
```
les deux premières déclarations d’opérateur sont autorisées, car, à des fins d' [indexeurs](classes.md#indexers) `T` . 3 `int` et `string` et, respectivement, sont considérés comme des types uniques sans relation. Toutefois, le troisième opérateur est une erreur, `C<T>` car est la classe de `D<T>`base de.

À partir de la deuxième règle, il suit qu’un opérateur de conversion doit effectuer une conversion vers ou à partir du type de classe ou de struct dans lequel l’opérateur est déclaré. Par exemple, il est possible pour `C` un type de classe ou de struct de définir une conversion de `C` vers `int` `int` `bool`et `C`de à, mais `int` pas de à.

Il n’est pas possible de redéfinir directement une conversion prédéfinie. Ainsi, les opérateurs de conversion ne sont pas autorisés à effectuer `object` une conversion depuis ou vers, car les conversions implicites et explicites existent déjà entre `object` et tous les autres types. De même, ni la source ni les types cibles d’une conversion ne peuvent être un type de base de l’autre, car une conversion existera déjà.

Toutefois, il est possible de déclarer des opérateurs sur des types génériques qui, pour des arguments de type particuliers, spécifient des conversions qui existent déjà en tant que conversions prédéfinies. Dans l’exemple
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
Quand le `object` type est spécifié en tant qu’argument `T`de type pour, le deuxième opérateur déclare une conversion qui existe déjà (implicite et, par conséquent, une conversion explicite existe de tout type `object`en type).

Dans les cas où une conversion prédéfinie existe entre deux types, toutes les conversions définies par l’utilisateur entre ces types sont ignorées. Plus précisément :

*  Si une conversion implicite prédéfinie ([conversions implicites](conversions.md#implicit-conversions)) existe `S` du type `T`en type, toutes les conversions définies par l’utilisateur (implicites `T` ou explicites) de `S` à sont ignorées.
*  Si une conversion explicite prédéfinie ([conversions explicites](conversions.md#explicit-conversions)) existe du `S` type en `T`type, toutes les conversions explicites définies `S` par `T` l’utilisateur de à sont ignorées. Par

Si `T` est un type interface, les conversions implicites définies par `S` l' `T` utilisateur de à sont ignorées.

Sinon, les conversions implicites définies par `S` l' `T` utilisateur de à sont toujours prises en compte.

Pour tous les types `object`, mais, les opérateurs déclarés par le `Convertible<T>` type ci-dessus ne sont pas en conflit avec les conversions prédéfinies. Exemple :
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

Toutefois, pour les `object`conversions de type, les conversions prédéfinies masquent les conversions définies par l’utilisateur dans tous les cas, à l’exception de :

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

Les conversions définies par l’utilisateur ne sont pas autorisées à convertir à partir de ou en *INTERFACE_TYPE*s. En particulier, cette restriction garantit qu’aucune transformation définie par l’utilisateur ne se produit lors de la conversion vers un *INTERFACE_TYPE*, et qu’une conversion vers un *INTERFACE_TYPE* réussit uniquement si l’objet en cours de conversion implémente réellement le *INTERFACE_TYPE*spécifié.

La signature d’un opérateur de conversion se compose du type de source et du type de cible. (Notez qu’il s’agit de la seule forme de membre pour laquelle le type de retour participe à la signature.) La `implicit` classification `explicit` ou d’un opérateur de conversion ne fait pas partie de la signature de l’opérateur. Ainsi, une classe ou un struct ne peut pas `implicit` déclarer à `explicit` la fois un opérateur de conversion et un opérateur de conversion avec les mêmes types source et cible.

En général, les conversions implicites définies par l’utilisateur doivent être conçues pour ne jamais lever d’exceptions et ne pas perdre d’informations. Si une conversion définie par l’utilisateur peut donner lieu à des exceptions (par exemple, parce que l’argument source est hors limites) ou une perte d’informations (par exemple, en ignorant les bits de poids fort), cette conversion doit être définie comme une conversion explicite.

Dans l’exemple
```csharp
using System;

public struct Digit
{
    byte value;

    public Digit(byte value) {
        if (value < 0 || value > 9) throw new ArgumentException();
        this.value = value;
    }

    public static implicit operator byte(Digit d) {
        return d.value;
    }

    public static explicit operator Digit(byte b) {
        return new Digit(b);
    }
}
```
la conversion de `Digit` en `byte` est implicite, car elle ne lève jamais d’exception ou ne perd pas d' `byte` informations, mais la `Digit` conversion de en `Digit` est explicite, car peut uniquement représenter un sous-ensemble du possible valeurs d’un `byte`.

## <a name="instance-constructors"></a>Constructeurs d’instance

Un ***constructeur d’instance*** est un membre qui implémente les actions requises pour initialiser une instance d’une classe. Les constructeurs d’instance sont déclarés à l’aide de *constructor_declaration*s :

```antlr
constructor_declaration
    : attributes? constructor_modifier* constructor_declarator constructor_body
    ;

constructor_modifier
    : 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'extern'
    | constructor_modifier_unsafe
    ;

constructor_declarator
    : identifier '(' formal_parameter_list? ')' constructor_initializer?
    ;

constructor_initializer
    : ':' 'base' '(' argument_list? ')'
    | ':' 'this' '(' argument_list? ')'
    ;

constructor_body
    : block
    | ';'
    ;
```

Un *constructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) et un modificateur de `extern` ([méthodes externes](classes.md#external-methods)). Une déclaration de constructeur n’est pas autorisée à inclure le même modificateur plusieurs fois.

L' *identificateur* d’un *constructor_declarator* doit nommer la classe dans laquelle le constructeur d’instance est déclaré. Si un autre nom est spécifié, une erreur de compilation se produit.

Le *formal_parameter_list* facultatif d’un constructeur d’instance est soumis aux mêmes règles que les *formal_parameter_list* d’une méthode ([méthodes](classes.md#methods)). La liste de paramètres formels définit la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’un constructeur d’instance et régit le processus par lequel la résolution de surcharge ([inférence de type](expressions.md#type-inference)) sélectionne un constructeur d’instance particulier dans un appel.

Chacun des types référencés dans le *formal_parameter_list* d’un constructeur d’instance doit être au moins aussi accessible que le constructeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Le *constructor_initializer* facultatif spécifie un autre constructeur d’instance à appeler avant d’exécuter les instructions fournies dans le *constructor_body* de ce constructeur d’instance. Cela est décrit plus en détail dans les [initialiseurs de constructeur](classes.md#constructor-initializers).

Lorsqu’une déclaration de constructeur comprend `extern` un modificateur, le constructeur est considéré comme un ***constructeur externe***. Étant donné qu’une déclaration de constructeur externe ne fournit aucune implémentation réelle, son *constructor_body* se compose d’un point-virgule. Pour tous les autres constructeurs, le *constructor_body* se compose d’un *bloc* qui spécifie les instructions pour initialiser une nouvelle instance de la classe. Cela correspond exactement au *bloc* d’une méthode d’instance avec un `void` type de retour ([corps de méthode](classes.md#method-body)).

Les constructeurs d’instance ne sont pas hérités. Par conséquent, une classe n’a pas de constructeurs d’instance autres que ceux réellement déclarés dans la classe. Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est fourni automatiquement ([constructeurs par défaut](classes.md#default-constructors)).

Les constructeurs d’instance sont appelés par *object_creation_expression*s ([expressions de création d’objet](expressions.md#object-creation-expressions)) et par le biais de *constructor_initializer*s.

### <a name="constructor-initializers"></a>Initialiseurs de constructeur

Tous les constructeurs d’instance (sauf ceux de la classe `object`) incluent implicitement un appel d’un autre constructeur d’instance immédiatement avant le *constructor_body*. Le constructeur pour appeler implicitement est déterminé par le *constructor_initializer*:

*  Un initialiseur de constructeur d’instance du `base(argument_list)` formulaire `base()` ou provoque l’appel d’un constructeur d’instance de la classe de base directe. Ce constructeur est sélectionné à l’aide de *argument_list* s’il est présent et les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles contenus dans la classe de base directe, ou du constructeur par défaut ([constructeurs par défaut](classes.md#default-constructors)), si aucun constructeur d’instance n’est déclaré dans la classe de base directe. Si cet ensemble est vide ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de compilation se produit.
*  Un initialiseur de constructeur d’instance du `this(argument-list)` formulaire `this()` ou provoque l’appel d’un constructeur d’instance de la classe elle-même. Le constructeur est sélectionné à l’aide de *argument_list* s’il est présent et les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles déclarés dans la classe elle-même. Si cet ensemble est vide ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de compilation se produit. Si une déclaration de constructeur d’instance comprend un initialiseur de constructeur qui appelle le constructeur lui-même, une erreur de compilation se produit.

Si un constructeur d’instance n’a pas d’initialiseur de constructeur, un initialiseur `base()` de constructeur du formulaire est fourni implicitement. Ainsi, une déclaration de constructeur d’instance de la forme
```csharp
C(...) {...}
```
est exactement équivalent à
```csharp
C(...): base() {...}
```

La portée des paramètres fournis par le *formal_parameter_list* d’une déclaration de constructeur d’instance comprend l’initialiseur de constructeur de cette déclaration. Ainsi, un initialiseur de constructeur est autorisé à accéder aux paramètres du constructeur. Exemple :
```csharp
class A
{
    public A(int x, int y) {}
}

class B: A
{
    public B(int x, int y): base(x + y, x - y) {}
}
```

Un initialiseur de constructeur d’instance ne peut pas accéder à l’instance en cours de création. Par conséquent, il s’agit d’une erreur au moment de la compilation pour référencer `this` dans une expression d’argument de l’initialiseur de constructeur, comme c’est le cas pour une expression d’argument qui fait référence à un membre d’instance par l’intermédiaire d’un *simple_name*.

### <a name="instance-variable-initializers"></a>Initialiseurs de variable d’instance

Lorsqu’un constructeur d’instance n’a pas d’initialiseur de constructeur, ou qu’il a un initialiseur de constructeur de la forme `base(...)`, ce constructeur effectue implicitement les initialisations spécifiées par les *variable_initializer*des champs d’instance déclarés dans sa classe. Cela correspond à une séquence d’assignations qui sont exécutées immédiatement après l’entrée dans le constructeur et avant l’appel implicite du constructeur de classe de base directe. Les initialiseurs de variable sont exécutés dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe.

### <a name="constructor-execution"></a>Exécution du constructeur

Les initialiseurs de variable sont transformés en instructions d’assignation, et ces instructions d’assignation sont exécutées avant l’appel du constructeur d’instance de la classe de base. Ce classement garantit que tous les champs d’instance sont initialisés par leurs initialiseurs de variable avant l’exécution des instructions qui ont accès à cette instance.

À partir de l’exemple
```csharp
using System;

class A
{
    public A() {
        PrintFields();
    }

    public virtual void PrintFields() {}
}

class B: A
{
    int x = 1;
    int y;

    public B() {
        y = -1;
    }

    public override void PrintFields() {
        Console.WriteLine("x = {0}, y = {1}", x, y);
    }
}
```
Lorsque `new B()` est utilisé pour créer une instance de `B`, la sortie suivante est produite :
```console
x = 1, y = 0
```

La valeur de `x` est 1, car l’initialiseur de variable est exécuté avant l’appel du constructeur d’instance de la classe de base. Toutefois, la valeur de `y` est 0 (la valeur par défaut d' `int`un), car l' `y` assignation à n’est pas exécutée tant que le constructeur de classe de base n’a pas été retourné.

Il est utile de considérer les initialiseurs de variable d’instance et les initialiseurs de constructeur comme des instructions qui sont insérées automatiquement avant *constructor_body*. L’exemple
```csharp
using System;
using System.Collections;

class A
{
    int x = 1, y = -1, count;

    public A() {
        count = 0;
    }

    public A(int n) {
        count = n;
    }
}

class B: A
{
    double sqrt2 = Math.Sqrt(2.0);
    ArrayList items = new ArrayList(100);
    int max;

    public B(): this(100) {
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        max = n;
    }
}
```
contient plusieurs initialiseurs de variable ; Il contient également des initialiseurs de constructeur des deux`base` formes `this`(et). L’exemple correspond au code indiqué ci-dessous, où chaque commentaire indique une instruction insérée automatiquement (la syntaxe utilisée pour les appels de constructeur insérés automatiquement n’est pas valide, mais sert simplement à illustrer le mécanisme).

```csharp
using System.Collections;

class A
{
    int x, y, count;

    public A() {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = 0;
    }

    public A(int n) {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = n;
    }
}

class B: A
{
    double sqrt2;
    ArrayList items;
    int max;

    public B(): this(100) {
        B(100);                      // Invoke B(int) constructor
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        sqrt2 = Math.Sqrt(2.0);      // Variable initializer
        items = new ArrayList(100);  // Variable initializer
        A(n - 1);                    // Invoke A(int) constructor
        max = n;
    }
}
```

### <a name="default-constructors"></a>Constructeurs par défaut

Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est fourni automatiquement. Ce constructeur par défaut appelle simplement le constructeur sans paramètre de la classe de base directe. Si la classe est abstraite, l’accessibilité déclarée pour le constructeur par défaut est protégée. Dans le cas contraire, l’accessibilité déclarée pour le constructeur par défaut est public. Par conséquent, le constructeur par défaut est toujours de la forme

```csharp
protected C(): base() {}
```
ou
```csharp
public C(): base() {}
```
où `C` est le nom de la classe. Si la résolution de surcharge ne peut pas déterminer un meilleur candidat unique pour l’initialiseur de constructeur de classe de base, une erreur de compilation se produit.

Dans l’exemple
```csharp
class Message
{
    object sender;
    string text;
}
```
un constructeur par défaut est fourni, car la classe ne contient aucune déclaration de constructeur d’instance. Ainsi, l’exemple est exactement équivalent à
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a>Constructeurs privés

Quand une classe `T` déclare uniquement des constructeurs d’instance privés, il n’est pas possible que les classes situées en dehors `T` du texte de `T` programme de soient dérivées de `T`ou de pour créer directement des instances de. Ainsi, si une classe contient uniquement des membres statiques et n’est pas destinée à être instanciée, l’ajout d’un constructeur d’instance privée vide empêchera l’instanciation. Exemple :
```csharp
public class Trig
{
    private Trig() {}        // Prevent instantiation

    public const double PI = 3.14159265358979323846;

    public static double Sin(double x) {...}
    public static double Cos(double x) {...}
    public static double Tan(double x) {...}
}
```

La `Trig` classe regroupe des constantes et des méthodes connexes, mais n’est pas destinée à être instanciée. Par conséquent, il déclare un seul constructeur d’instance privée vide. Au moins un constructeur d’instance doit être déclaré pour supprimer la génération automatique d’un constructeur par défaut.

### <a name="optional-instance-constructor-parameters"></a>Paramètres facultatifs du constructeur d’instance

La `this(...)` forme de l’initialiseur de constructeur est couramment utilisée conjointement avec la surcharge pour implémenter des paramètres de constructeur d’instance facultatifs. Dans l’exemple
```csharp
class Text
{
    public Text(): this(0, 0, null) {}

    public Text(int x, int y): this(x, y, null) {}

    public Text(int x, int y, string s) {
        // Actual constructor implementation
    }
}
```
les deux premiers constructeurs d’instance fournissent simplement les valeurs par défaut pour les arguments manquants. Les deux utilisent `this(...)` un initialiseur de constructeur pour appeler le troisième constructeur d’instance, qui effectue en fait le travail d’initialisation de la nouvelle instance. L’effet est celui des paramètres de constructeur facultatifs :
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a>Constructeurs statiques

Un ***constructeur statique*** est un membre qui implémente les actions requises pour initialiser un type de classe fermé. Les constructeurs statiques sont déclarés à l’aide de *static_constructor_declaration*s :

```antlr
static_constructor_declaration
    : attributes? static_constructor_modifiers identifier '(' ')' static_constructor_body
    ;

static_constructor_modifiers
    : 'extern'? 'static'
    | 'static' 'extern'?
    | static_constructor_modifiers_unsafe
    ;

static_constructor_body
    : block
    | ';'
    ;
```

Un *static_constructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et un modificateur `extern` ([méthodes externes](classes.md#external-methods)).

L' *identificateur* d’un *static_constructor_declaration* doit nommer la classe dans laquelle le constructeur statique est déclaré. Si un autre nom est spécifié, une erreur de compilation se produit.

Lorsqu’une déclaration de constructeur statique comprend `extern` un modificateur, le constructeur statique est dit comme un ***constructeur statique externe***. Étant donné qu’une déclaration de constructeur statique externe ne fournit aucune implémentation réelle, son *static_constructor_body* se compose d’un point-virgule. Pour toutes les autres déclarations de constructeur statiques, le *static_constructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter pour initialiser la classe. Cela correspond exactement au *method_body* d’une méthode statique avec un type de retour `void` ([corps de méthode](classes.md#method-body)).

Les constructeurs statiques ne sont pas hérités et ne peuvent pas être appelés directement.

Le constructeur statique d’un type de classe fermé s’exécute au plus une fois dans un domaine d’application donné. L’exécution d’un constructeur statique est déclenchée par le premier des événements suivants pour se produire au sein d’un domaine d’application :

*  Une instance du type de classe est créée.
*  Tous les membres statiques du type de classe sont référencés.

Si une classe contient la `Main` méthode ([démarrage](basic-concepts.md#application-startup)de l’application) dans laquelle l’exécution commence, le constructeur statique de cette classe s' `Main` exécute avant l’appel de la méthode.

Pour initialiser un nouveau type de classe fermé, tout d’abord un nouvel ensemble de champs statiques ([champs d’instance et statiques](classes.md#static-and-instance-fields)) pour ce type fermé particulier est créé. Chacun des champs statiques est initialisé à sa valeur par défaut ([valeurs par défaut](variables.md#default-values)). Ensuite, les initialiseurs de champ statique ([initialisation de champ statique](classes.md#static-field-initialization)) sont exécutés pour ces champs statiques. Enfin, le constructeur statique est exécuté.

L’exemple
```csharp
using System;

class Test
{
    static void Main() {
        A.F();
        B.F();
    }
}

class A
{
    static A() {
        Console.WriteLine("Init A");
    }
    public static void F() {
        Console.WriteLine("A.F");
    }
}

class B
{
    static B() {
        Console.WriteLine("Init B");
    }
    public static void F() {
        Console.WriteLine("B.F");
    }
}
```
doit produire la sortie :
```console
Init A
A.F
Init B
B.F
```
étant donné que l' `A`exécution du constructeur statique de est déclenchée par l' `A.F`appel à, et que `B`l’exécution du constructeur statique de est déclenchée par `B.F`l’appel à.

Il est possible de construire des dépendances circulaires qui autorisent le respect des champs statiques avec des initialiseurs de variable dans leur état de valeur par défaut.

L’exemple
```csharp
using System;

class A
{
    public static int X;

    static A() {
        X = B.Y + 1;
    }
}

class B
{
    public static int Y = A.X + 1;

    static B() {}

    static void Main() {
        Console.WriteLine("X = {0}, Y = {1}", A.X, B.Y);
    }
}
```
génère la sortie
```console
X = 1, Y = 2
```

Pour exécuter la `Main` méthode, le système exécute d’abord l’initialiseur `B.Y`pour, avant le `B`constructeur statique de la classe. `Y`l’initialiseur de `A`entraîne l’exécution du constructeur statique de, car la `A.X` valeur de est référencée. Le constructeur statique de `A` à son tour poursuit le calcul de la valeur `X`de, ce qui permet d’extraire la valeur par défaut `Y`de, qui est égale à zéro. `A.X`est donc initialisé à 1. Le processus d’exécution `A`des initialiseurs de champ statiques et du constructeur statique se termine alors, en retournant au calcul `Y`de la valeur initiale de, dont le résultat devient 2.

Étant donné que le constructeur statique est exécuté une seule fois pour chaque type de classe construite fermée, il s’agit d’un emplacement pratique pour appliquer des contrôles au moment de l’exécution sur le paramètre de type qui ne peuvent pas être vérifiés au moment de la compilation via des contraintes ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) . Par exemple, le type suivant utilise un constructeur statique pour appliquer que l’argument de type est une énumération :
```csharp
class Gen<T> where T: struct
{
    static Gen() {
        if (!typeof(T).IsEnum) {
            throw new ArgumentException("T must be an enum");
        }
    }
}
```

## <a name="destructors"></a>Destructeurs

Un ***destructeur*** est un membre qui implémente les actions requises pour détruire une instance d’une classe. Un destructeur est déclaré à l’aide d’un *destructor_declaration*:

```antlr
destructor_declaration
    : attributes? 'extern'? '~' identifier '(' ')' destructor_body
    | destructor_declaration_unsafe
    ;

destructor_body
    : block
    | ';'
    ;
```

Un *destructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)).

L' *identificateur* d’un *destructor_declaration* doit nommer la classe dans laquelle le destructeur est déclaré. Si un autre nom est spécifié, une erreur de compilation se produit.

Lorsqu’une déclaration de destructeur comprend un `extern` modificateur, le destructeur est considéré comme un ***destructeur externe***. Étant donné qu’une déclaration de destructeur externe ne fournit aucune implémentation réelle, son *destructor_body* se compose d’un point-virgule. Pour tous les autres destructeurs, le *destructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter afin de détruire une instance de la classe. Un *destructor_body* correspond exactement au *method_body* d’une méthode d’instance avec un type de retour `void` ([corps de méthode](classes.md#method-body)).

Les destructeurs ne sont pas hérités. Par conséquent, une classe n’a pas de destructeurs autres que celui qui peut être déclaré dans cette classe.

Étant donné qu’un destructeur ne doit pas avoir de paramètres, il ne peut pas être surchargé, donc une classe peut avoir, au plus, un destructeur.

Les destructeurs sont appelés automatiquement et ne peuvent pas être appelés explicitement. Une instance devient éligible pour la destruction lorsqu’il n’est plus possible pour un code d’utiliser cette instance. L’exécution du destructeur pour l’instance peut se produire à tout moment une fois que l’instance est éligible pour la destruction. Lorsqu’une instance est détruite, les destructeurs dans la chaîne d’héritage de cette instance sont appelés, dans l’ordre, de la plus dérivée à la moins dérivée. Un destructeur peut être exécuté sur n’importe quel thread. Pour plus d’informations sur les règles qui régissent le moment et le mode d’exécution d’un destructeur, consultez [gestion automatique](basic-concepts.md#automatic-memory-management)de la mémoire.

La sortie de l’exemple
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("A's destructor");
    }
}

class B: A
{
    ~B() {
        Console.WriteLine("B's destructor");
    }
}

class Test
{
   static void Main() {
        B b = new B();
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
   }
}
```
est
```
B's destructor
A's destructor
```
étant donné que les destructeurs dans une chaîne d’héritage sont appelés dans l’ordre, de la plus dérivée à la moins dérivée.

Les destructeurs sont implémentés en substituant la `Finalize` méthode `System.Object`virtuelle sur. C#les programmes ne sont pas autorisés à substituer cette méthode ou à les appeler (ou à les remplacer) directement. Par exemple, le programme
```csharp
class A 
{
    override protected void Finalize() {}    // error

    public void F() {
        this.Finalize();                     // error
    }
}
```
contient deux erreurs.

Le compilateur se comporte comme si cette méthode, et ses substitutions, n’existent pas du tout. Ainsi, ce programme :
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
est valide, et la méthode affichée masque `System.Object`la méthode de. `Finalize`

Pour une description du comportement lorsqu’une exception est levée à partir d’un destructeur, consultez [la rubrique Comment les exceptions sont gérées](exceptions.md#how-exceptions-are-handled).

## <a name="iterators"></a>Iterators

Une fonction membre ([fonction members](expressions.md#function-members)) implémentée à l’aide d’un bloc itérateur ([blocs](statements.md#blocks)) est appelée un ***itérateur***.

Un bloc itérateur peut être utilisé comme corps d’un membre de fonction tant que le type de retour de la fonction membre correspondante est l’une des interfaces d’énumérateur ([interfaces d’énumérateur](classes.md#enumerator-interfaces)) ou l’une des interfaces énumérables ([interfaces énumérables](classes.md#enumerable-interfaces)) . Elle peut se produire sous forme de *method_body*, *operator_body* ou *accessor_body*, alors que les événements, les constructeurs d’instance, les constructeurs statiques et les destructeurs ne peuvent pas être implémentés en tant qu’itérateurs.

Lorsqu’un membre de fonction est implémenté à l’aide d’un bloc itérateur, il s’agit d’une erreur de compilation pour la liste de paramètres formels `ref` de `out` la fonction membre qui spécifie les paramètres ou.

### <a name="enumerator-interfaces"></a>Interfaces d’énumérateur

Les ***interfaces d’énumérateur*** sont l’interface `System.Collections.IEnumerator` non générique et toutes les instanciations de l’interface `System.Collections.Generic.IEnumerator<T>`générique. Par souci de concision, dans ce chapitre, ces interfaces sont référencées en `IEnumerator` tant `IEnumerator<T>`que et, respectivement.

### <a name="enumerable-interfaces"></a>Interfaces énumérables

Les ***interfaces énumérables*** sont l’interface `System.Collections.IEnumerable` non générique et toutes les instanciations de l' `System.Collections.Generic.IEnumerable<T>`interface générique. Par souci de concision, dans ce chapitre, ces interfaces sont référencées en `IEnumerable` tant `IEnumerable<T>`que et, respectivement.

### <a name="yield-type"></a>Type yield

Un itérateur produit une séquence de valeurs, toutes du même type. Ce type est appelé le ***type yield*** de l’itérateur.

*  Type yield d’un itérateur qui retourne `IEnumerator` ou `IEnumerable` est `object`.
*  Type yield d’un itérateur qui retourne `IEnumerator<T>` ou `IEnumerable<T>` est `T`.

### <a name="enumerator-objects"></a>Objets Enumerator

Lorsqu’un membre de fonction qui retourne un type d’interface énumérateur est implémenté à l’aide d’un bloc itérateur, l’appel du membre de fonction n’exécute pas immédiatement le code dans le bloc itérateur. Au lieu de cela, un ***objet énumérateur*** est créé et retourné. Cet objet encapsule le code spécifié dans le bloc Iterator, et l’exécution du code dans le bloc Iterator se produit lorsque la méthode de `MoveNext` l’objet énumérateur est appelée. Un objet énumérateur a les caractéristiques suivantes :

*  Il implémente `IEnumerator` et `IEnumerator<T>`, où `T` est le type de rendement de l’itérateur.
*  Elle implémente `System.IDisposable`.
*  Elle est initialisée à l’aide d’une copie des valeurs d’argument (le cas échéant) et de la valeur d’instance passée à la fonction membre.
*  Il a quatre États potentiels, ***avant***, ***en cours d’exécution***, ***suspendu***et ***après***, et est initialement dans l’état ***avant*** .

Un objet énumérateur est généralement une instance d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur et implémente les interfaces d’énumérateur, mais d’autres méthodes d’implémentation sont possibles. Si une classe d’énumération est générée par le compilateur, cette classe sera imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, elle aura une accessibilité privée et un nom est réservé à l’utilisation du compilateur ([identificateurs](lexical-structure.md#identifiers)).

Un objet énumérateur peut implémenter plus d’interfaces que celles spécifiées ci-dessus.

Les sections suivantes décrivent le comportement exact des `MoveNext`membres `Current`, et `Dispose` des implémentations `IEnumerable` d' `IEnumerable<T>` interface et fournies par un objet énumérateur.

Notez que les objets Enumerator ne prennent pas `IEnumerator.Reset` en charge la méthode. L’appel de cette méthode entraîne `System.NotSupportedException` la levée d’une exception.

#### <a name="the-movenext-method"></a>Méthode MoveNext

La `MoveNext` méthode d’un objet énumérateur encapsule le code d’un bloc itérateur. L’appel de `MoveNext` la méthode exécute le code dans le bloc Iterator et définit `Current` la propriété de l’objet énumérateur comme il convient. L’action exacte effectuée par `MoveNext` dépend de l’état de l’objet énumérateur lorsque `MoveNext` est appelé :

*  Si l’état de l’objet énumérateur est ***antérieur***, appelez `MoveNext`:
   * Modifie l’État en ***en cours d’exécution***.
   * Initialise les paramètres (y compris `this`) du bloc itérateur aux valeurs d’argument et à la valeur d’instance enregistrées lorsque l’objet énumérateur a été initialisé.
   * Exécute le bloc itérateur à partir du début jusqu’à ce que l’exécution soit interrompue (comme décrit ci-dessous).
*  Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat `MoveNext` de l’appel de n’est pas spécifié.
*  Si l’état de l’objet énumérateur est ***suspendu***, appelez `MoveNext`:
   * Modifie l’État en ***en cours d’exécution***.
   * Restaure les valeurs de tous les paramètres et variables locales (y compris celui-ci) aux valeurs enregistrées lors de la dernière suspension de l’exécution du bloc itérateur. Notez que le contenu de tous les objets référencés par ces variables peut avoir changé depuis l’appel précédent à MoveNext.
   * Reprend l’exécution du bloc itérateur immédiatement après l' `yield return` instruction qui a provoqué la suspension de l’exécution et continue jusqu’à ce que l’exécution soit interrompue (comme décrit ci-dessous).
*  Si l’état de l’objet énumérateur est ***après***, l’appel `MoveNext` de `false`retourne.


Lorsque `MoveNext` exécute le bloc Iterator, l’exécution peut être interrompue de quatre façons : Par une `yield return` instruction, par une `yield break` instruction, en rencontrant la fin du bloc itérateur, et par une exception levée et propagée en dehors du bloc itérateur.

*  Quand une `yield return` instruction est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :
   * L’expression donnée dans l’instruction est évaluée, implicitement convertie en type yield et assignée à la `Current` propriété de l’objet énumérateur.
   * L’exécution du corps de l’itérateur est suspendue. Les valeurs de tous les paramètres et variables locales ( `this`y compris) sont enregistrées, comme c’est l' `yield return` emplacement de cette instruction. Si l' `yield return` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés ne sont pas exécutés pour l’instant.
   * L’état de l’objet énumérateur est modifié en ***suspendu***.
   * La `MoveNext` méthode retourne `true` à son appelant, ce qui indique que l’itération a réussi à avancer jusqu’à la valeur suivante.
*  Quand une `yield break` instruction est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :
   * Si l' `yield break` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés sont exécutés.
   * L’état de l’objet énumérateur est remplacé par ***après***.
   * La `MoveNext` méthode retourne `false` à son appelant, ce qui indique que l’itération est terminée.
*  Quand la fin du corps de l’itérateur est rencontrée :
   * L’état de l’objet énumérateur est remplacé par ***après***.
   * La `MoveNext` méthode retourne `false` à son appelant, ce qui indique que l’itération est terminée.
*  Lorsqu’une exception est levée et propagée en dehors du bloc Iterator :
   * Les `finally` blocs appropriés dans le corps de l’itérateur ont été exécutés par la propagation de l’exception.
   * L’état de l’objet énumérateur est remplacé par ***après***.
   * La propagation de l’exception continue à l’appelant de `MoveNext` la méthode.

#### <a name="the-current-property"></a>La propriété actuelle

La propriété d' `Current` un objet Enumerator est affectée `yield return` par les instructions du bloc Iterator.

Lorsqu’un objet énumérateur est dans l’état ***suspendu*** , la valeur de `Current` est la valeur définie par l’appel précédent à `MoveNext`. Lorsqu’un objet énumérateur se trouve dans les États ***Before***, ***Running***ou ***after*** , `Current` le résultat de l’accès n’est pas spécifié.

Pour un itérateur avec un type yield autre que `object`, le résultat de l' `Current` accès par le biais de l' `IEnumerable` implémentation de l’objet énumérateur `Current` correspond `IEnumerator<T>` à l’accès par le biais de l’objet énumérateur implémenter et effectuer un cast `object`du résultat vers.

#### <a name="the-dispose-method"></a>Méthode dispose

La `Dispose` méthode est utilisée pour nettoyer l’itération en plaçant l’objet énumérateur à l’état ***after*** .

*  Si l’état de l’objet énumérateur est ***antérieur***, l’appel `Dispose` de passe l’État à ***après***.
*  Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat `Dispose` de l’appel de n’est pas spécifié.
*  Si l’état de l’objet énumérateur est ***suspendu***, appelez `Dispose`:
   * Modifie l’État en ***en cours d’exécution***.
   * Exécute tous les blocs finally comme si la dernière `yield return` instruction exécutée `yield break` était une instruction. Si cela entraîne la levée d’une exception et sa propagation en dehors du corps de l’itérateur, l’état de l’objet énumérateur est défini sur ***après*** et l’exception est propagée à l’appelant `Dispose` de la méthode.
   * Passe l’État à ***après***.
*  Si l’état de l’objet énumérateur est ***après***, l’appel `Dispose` de n’a aucun effet.

### <a name="enumerable-objects"></a>Objets énumérables

Lorsqu’un membre de fonction qui retourne un type d’interface énumérable est implémenté à l’aide d’un bloc itérateur, l’appel de la fonction member n’exécute pas immédiatement le code dans le bloc itérateur. Au lieu de cela, un ***objet énumérable*** est créé et retourné. La méthode de `GetEnumerator` l’objet énumérable retourne un objet énumérateur qui encapsule le code spécifié dans le bloc Iterator, et l’exécution du code dans le bloc Iterator se produit lorsque la `MoveNext` méthode de l’objet énumérateur est appelée. Un objet énumérable présente les caractéristiques suivantes :

*  Il implémente `IEnumerable` et `IEnumerable<T>`, où `T` est le type de rendement de l’itérateur.
*  Elle est initialisée à l’aide d’une copie des valeurs d’argument (le cas échéant) et de la valeur d’instance passée à la fonction membre.

Un objet énumérable est généralement une instance d’une classe énumérable générée par le compilateur qui encapsule le code dans le bloc itérateur et implémente les interfaces énumérables, mais d’autres méthodes d’implémentation sont possibles. Si une classe énumérable est générée par le compilateur, cette classe sera imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, elle aura une accessibilité privée et un nom est réservé à l’utilisation du compilateur ([identificateurs](lexical-structure.md#identifiers)).

Un objet énumérable peut implémenter plus d’interfaces que celles spécifiées ci-dessus. En particulier, un objet énumérable peut également `IEnumerator` implémenter et `IEnumerator<T>`, ce qui lui permet de servir à la fois un énumérable et un énumérateur. Dans ce type d’implémentation, la première fois qu’une méthode d' `GetEnumerator` un objet énumérable est appelée, l’objet énumérable lui-même est retourné. Les appels suivants de l’objet `GetEnumerator`énumérable, le cas échéant, retournent une copie de l’objet énumérable. Ainsi, chaque énumérateur retourné a son propre État et les modifications apportées dans un énumérateur n’en affectent pas une autre.

#### <a name="the-getenumerator-method"></a>Méthode GetEnumerator

Un objet énumérable fournit une implémentation des `GetEnumerator` méthodes `IEnumerable` des interfaces et `IEnumerable<T>` . Les deux `GetEnumerator` méthodes partagent une implémentation commune qui acquiert et retourne un objet énumérateur disponible. L’objet énumérateur est initialisé avec les valeurs d’argument et la valeur d’instance enregistrées lorsque l’objet énumérable a été initialisé, mais dans le cas contraire, l’objet énumérateur fonctionne comme décrit dans [objets Enumerator](classes.md#enumerator-objects).

### <a name="implementation-example"></a>Exemple d’implémentation

Cette section décrit une implémentation possible d’itérateurs en termes de constructions C# standard. L’implémentation décrite ici est basée sur les principes utilisés par le compilateur Microsoft C# , mais il ne s’agit pas d’une implémentation obligatoire ou du seul possible.

La classe `Stack<T>` suivante implémente sa `GetEnumerator` méthode à l’aide d’un itérateur. L’itérateur énumère les éléments de la pile dans l’ordre de haut en bas.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

class Stack<T>: IEnumerable<T>
{
    T[] items;
    int count;

    public void Push(T item) {
        if (items == null) {
            items = new T[4];
        }
        else if (items.Length == count) {
            T[] newItems = new T[count * 2];
            Array.Copy(items, 0, newItems, 0, count);
            items = newItems;
        }
        items[count++] = item;
    }

    public T Pop() {
        T result = items[--count];
        items[count] = default(T);
        return result;
    }

    public IEnumerator<T> GetEnumerator() {
        for (int i = count - 1; i >= 0; --i) yield return items[i];
    }
}
```

La `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

```csharp
class Stack<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1: IEnumerator<T>, IEnumerator
    {
        int __state;
        T __current;
        Stack<T> __this;
        int i;

        public __Enumerator1(Stack<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
                case 1: goto __state1;
                case 2: goto __state2;
            }
            i = __this.count - 1;
        __loop:
            if (i < 0) goto __state2;
            __current = __this.items[i];
            __state = 1;
            return true;
        __state1:
            --i;
            goto __loop;
        __state2:
            __state = 2;
            return false;
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

Dans la traduction précédente, le code du bloc itérateur est converti en ordinateur d’État et placé dans la `MoveNext` méthode de la classe Enumerator. En outre, la variable `i` locale est transformée en champ dans l’objet énumérateur afin de pouvoir continuer à exister entre les appels `MoveNext`de.

L’exemple suivant imprime une table de multiplication simple des entiers 1 à 10. La `FromTo` méthode de l’exemple retourne un objet énumérable et est implémentée à l’aide d’un itérateur.

```csharp
using System;
using System.Collections.Generic;

class Test
{
    static IEnumerable<int> FromTo(int from, int to) {
        while (from <= to) yield return from++;
    }

    static void Main() {
        IEnumerable<int> e = FromTo(1, 10);
        foreach (int x in e) {
            foreach (int y in e) {
                Console.Write("{0,3} ", x * y);
            }
            Console.WriteLine();
        }
    }
}
```

La `FromTo` méthode peut être traduite en une instanciation d’une classe énumérable générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

```csharp
using System;
using System.Threading;
using System.Collections;
using System.Collections.Generic;

class Test
{
    ...

    static IEnumerable<int> FromTo(int from, int to) {
        return new __Enumerable1(from, to);
    }

    class __Enumerable1:
        IEnumerable<int>, IEnumerable,
        IEnumerator<int>, IEnumerator
    {
        int __state;
        int __current;
        int __from;
        int from;
        int to;
        int i;

        public __Enumerable1(int __from, int to) {
            this.__from = __from;
            this.to = to;
        }

        public IEnumerator<int> GetEnumerator() {
            __Enumerable1 result = this;
            if (Interlocked.CompareExchange(ref __state, 1, 0) != 0) {
                result = new __Enumerable1(__from, to);
                result.__state = 1;
            }
            result.from = result.__from;
            return result;
        }

        IEnumerator IEnumerable.GetEnumerator() {
            return (IEnumerator)GetEnumerator();
        }

        public int Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
            case 1:
                if (from > to) goto case 2;
                __current = from++;
                __state = 1;
                return true;
            case 2:
                __state = 2;
                return false;
            default:
                throw new InvalidOperationException();
            }
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

La classe Enumerable implémente à la fois les interfaces Enumerable et les interfaces d’énumérateur, ce qui lui permet de servir à la fois un énumérable et un énumérateur. La première fois que `GetEnumerator` la méthode est appelée, l’objet énumérable lui-même est retourné. Les appels suivants de l’objet `GetEnumerator`énumérable, le cas échéant, retournent une copie de l’objet énumérable. Ainsi, chaque énumérateur retourné a son propre État et les modifications apportées dans un énumérateur n’en affectent pas une autre. La `Interlocked.CompareExchange` méthode est utilisée pour garantir l’opération thread-safe.

Les `from` paramètres `to` et sont convertis en champs dans la classe Enumerable. Étant `from` donné que est modifié dans le bloc Iterator, `__from` un champ supplémentaire est introduit pour contenir la valeur initiale `from` donnée à dans chaque énumérateur.

La `MoveNext` méthode lève une `InvalidOperationException` si elle est appelée lorsque `__state` est `0`. Cela permet de protéger l’utilisation de l’objet énumérable en tant qu’objet `GetEnumerator`énumérateur sans appeler au préalable.

L’exemple suivant montre une classe d’arborescence simple. La `Tree<T>` classe implémente sa `GetEnumerator` méthode à l’aide d’un itérateur. L’itérateur énumère les éléments de l’arborescence dans l’ordre des infixes.

```csharp
using System;
using System.Collections.Generic;

class Tree<T>: IEnumerable<T>
{
    T value;
    Tree<T> left;
    Tree<T> right;

    public Tree(T value, Tree<T> left, Tree<T> right) {
        this.value = value;
        this.left = left;
        this.right = right;
    }

    public IEnumerator<T> GetEnumerator() {
        if (left != null) foreach (T x in left) yield x;
        yield value;
        if (right != null) foreach (T x in right) yield x;
    }
}

class Program
{
    static Tree<T> MakeTree<T>(T[] items, int left, int right) {
        if (left > right) return null;
        int i = (left + right) / 2;
        return new Tree<T>(items[i], 
            MakeTree(items, left, i - 1),
            MakeTree(items, i + 1, right));
    }

    static Tree<T> MakeTree<T>(params T[] items) {
        return MakeTree(items, 0, items.Length - 1);
    }

    // The output of the program is:
    // 1 2 3 4 5 6 7 8 9
    // Mon Tue Wed Thu Fri Sat Sun

    static void Main() {
        Tree<int> ints = MakeTree(1, 2, 3, 4, 5, 6, 7, 8, 9);
        foreach (int i in ints) Console.Write("{0} ", i);
        Console.WriteLine();

        Tree<string> strings = MakeTree(
            "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun");
        foreach (string s in strings) Console.Write("{0} ", s);
        Console.WriteLine();
    }
}
```

La `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

```csharp
class Tree<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1 : IEnumerator<T>, IEnumerator
    {
        Node<T> __this;
        IEnumerator<T> __left, __right;
        int __state;
        T __current;

        public __Enumerator1(Node<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            try {
                switch (__state) {

                case 0:
                    __state = -1;
                    if (__this.left == null) goto __yield_value;
                    __left = __this.left.GetEnumerator();
                    goto case 1;

                case 1:
                    __state = -2;
                    if (!__left.MoveNext()) goto __left_dispose;
                    __current = __left.Current;
                    __state = 1;
                    return true;

                __left_dispose:
                    __state = -1;
                    __left.Dispose();

                __yield_value:
                    __current = __this.value;
                    __state = 2;
                    return true;

                case 2:
                    __state = -1;
                    if (__this.right == null) goto __end;
                    __right = __this.right.GetEnumerator();
                    goto case 3;

                case 3:
                    __state = -3;
                    if (!__right.MoveNext()) goto __right_dispose;
                    __current = __right.Current;
                    __state = 3;
                    return true;

                __right_dispose:
                    __state = -1;
                    __right.Dispose();

                __end:
                    __state = 4;
                    break;

                }
            }
            finally {
                if (__state < 0) Dispose();
            }
            return false;
        }

        public void Dispose() {
            try {
                switch (__state) {

                case 1:
                case -2:
                    __left.Dispose();
                    break;

                case 3:
                case -3:
                    __right.Dispose();
                    break;

                }
            }
            finally {
                __state = 4;
            }
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

Les compilateurs temporaires générés par le `foreach` compilateur utilisés dans les instructions sont `__left` levés dans les champs et `__right` de l’objet énumérateur. Le `__state` champ de l’objet énumérateur est mis à jour avec soin afin `Dispose()` que la méthode correcte soit appelée correctement si une exception est levée. Notez qu’il n’est pas possible d’écrire le code traduit avec `foreach` des instructions simples.

## <a name="async-functions"></a>Fonctions Async

Une méthode ([méthodes](classes.md#methods)) ou une fonction anonyme ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) avec le `async` modificateur est appelée ***fonction Async***. En général, le terme ***Async*** est utilisé pour décrire tout type de fonction qui a le `async` modificateur.

Il s’agit d’une erreur de compilation pour la liste de paramètres formels d’une fonction Async `ref` pour `out` spécifier des paramètres ou.

Le *type_retour* d’une méthode Async doit être `void` ou un ***type de tâche***. Les types de tâches `System.Threading.Tasks.Task` sont et les types `System.Threading.Tasks.Task<T>`construits à partir de. Par souci de concision, dans ce chapitre, ces types sont référencés en `Task` tant `Task<T>`que et, respectivement. Une méthode Async qui retourne un type de tâche est dite retournant des tâches.

La définition exacte des types de tâches est définie par l’implémentation, mais du point de vue du langage, un type de tâche se trouve dans l’un des États incomplet, SUCCEEDED ou Faulted. Une tâche ayant généré une erreur enregistre une exception pertinente. Une opération `Task<T>` réussie enregistre un résultat de `T`type. Les types de tâches sont await, et peuvent par conséquent être les opérandes des expressions await ([expression await](expressions.md#await-expressions)).

Un appel de fonction Async a la possibilité de suspendre l’évaluation à l’aide d’expressions await ([expression await](expressions.md#await-expressions)) dans son corps. L’évaluation peut être reprise ultérieurement au moment de l’interruption de l’expression await au moyen d’un ***délégué de reprise***. Le délégué de reprise est de `System.Action`type, et lorsqu’il est appelé, l’évaluation de l’appel de la fonction Async reprend à partir de l’expression await là où elle s’est arrêtée. L' ***appelant actuel*** d’un appel de fonction Async est l’appelant d’origine si l’appel de fonction n’a jamais été interrompu ou l’appelant le plus récent du délégué de reprise dans le cas contraire.

### <a name="evaluation-of-a-task-returning-async-function"></a>Évaluation d’une fonction asynchrone retournant des tâches

L’appel d’une fonction Async retournant des tâches provoque la génération d’une instance du type de tâche retourné. C’est ce que l’on appelle la ***tâche de retour*** de la fonction Async. L’état initial de la tâche est incomplet.

Le corps de la fonction Async est ensuite évalué jusqu’à ce qu’il soit suspendu (en atteignant une expression await) ou se termine, auquel point le contrôle est retourné à l’appelant, ainsi que la tâche de retour.

Lorsque le corps de la fonction Async se termine, la tâche de retour est déplacée de l’état incomplet :

*  Si le corps de la fonction se termine suite à l’atteinte d’une instruction return ou à la fin du corps, toute valeur de résultat est enregistrée dans la tâche de retour, qui est placée dans un état de réussite.
*  Si le corps de la fonction se termine à la suite d’une exception non interceptée ([instruction throw](statements.md#the-throw-statement)), l’exception est enregistrée dans la tâche de retour qui est placée dans un état d’erreur.

### <a name="evaluation-of-a-void-returning-async-function"></a>Évaluation d’une fonction Async qui retourne void

Si le type de retour de la fonction Async `void`est, l’évaluation diffère de celle ci-dessus de la manière suivante : Étant donné qu’aucune tâche n’est retournée, la fonction communique à la place l’achèvement et les exceptions au ***contexte de synchronisation***du thread actuel. La définition exacte du contexte de synchronisation dépend de l’implémentation, mais est une représentation de « où » le thread actuel est en cours d’exécution. Le contexte de synchronisation est notifié lorsque l’évaluation d’une fonction Async qui retourne une valeur void commence, se termine correctement ou provoque la levée d’une exception non interceptée.

Cela permet au contexte d’effectuer le suivi du nombre de fonctions Async à retour void qui s’exécutent sous celui-ci et de décider comment propager les exceptions en provenance de ces dernières.
