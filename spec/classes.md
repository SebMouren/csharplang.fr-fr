---
ms.openlocfilehash: af7af574814dc04ee3ece0396b7ae5f86b3ec8eb
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229645"
---
# <a name="classes"></a>Classes

Une classe est une structure de données qui peut contenir des données membres (constantes et champs), des fonctions membres (méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et les types imbriqués. Types de classes prend en charge l’héritage, un mécanisme par lequel une classe dérivée peut étendre et spécialiser une classe de base.

## <a name="class-declarations"></a>Déclarations de classe

Un *class_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare une nouvelle classe.

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

Un *class_declaration* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *class_modifier*s ([modificateurs de classe](classes.md#class-modifiers)), suivie éventuellement d’un `partial` modificateur, suivi par le mot clé `class` et un *identificateur* qui nomme la classe, suivie d’un facultatif *type_parameter_list* ([paramètres de Type](classes.md#type-parameters)), suivie éventuellement d’un *class_base* spécification ([base de classe spécification](classes.md#class-base-specification)), suivi d’un ensemble facultatif de *type_parameter_constraints_clause*s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), suivi par un *class_body*  ([Classe corps](classes.md#class-body)), éventuellement suivi par un point-virgule.

Une déclaration de classe ne peut pas fournir *type_parameter_constraints_clause*s, sauf si elle fournit également un *type_parameter_list*.

Une déclaration de classe qui fournit un *type_parameter_list* est un ***déclaration de classe générique***. En outre, toute classe imbriquée dans une déclaration de classe générique ou une déclaration de struct générique est lui-même une déclaration de classe générique, dans la mesure où les paramètres de type pour le type de conteneur doivent être fournis pour créer un type construit.

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

Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration de classe.

Le `new` modificateur est autorisé sur les classes imbriquées. Spécifie que la classe masque un membre hérité par le même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier). Il s’agit d’une erreur lors de la compilation pour le `new` modificateur apparaît sur une déclaration de classe qui n’est pas une déclaration de classe imbriquée.

Le `public`, `protected`, `internal`, et `private` modificateurs contrôlent l’accessibilité de la classe. En fonction du contexte dans lequel la déclaration de classe se produit, certains de ces modificateurs ne sont pas autorisées ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Le `abstract`, `sealed` et `static` modificateurs sont décrits dans les sections suivantes.

#### <a name="abstract-classes"></a>Classes abstraites

Le `abstract` modificateur est utilisé pour indiquer qu’une classe est incomplète et qu’elle est destinée à être utilisée uniquement comme classe de base. Une classe abstraite est différent d’une classe non abstraite comme suit :

*  Une classe abstraite ne peut pas être instanciée directement, et il s’agit d’une erreur de compilation à utiliser le `new` opérateur sur une classe abstraite. Bien qu’il soit possible d’avoir des variables et valeurs dont les types de compilation sont abstraites, ces variables et valeurs nécessairement sera `null` ou contiennent des références à des instances de classes non abstraites dérivées des types abstraits.
*  Une classe abstraite peut (mais pas obligatoire) pour contenir des membres abstraits.
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
la classe abstraite `A` présente une méthode abstraite `F`. Classe `B` introduit une méthode supplémentaire `G`, mais dans la mesure où il ne fournit pas une implémentation de `F`, `B` doit également être déclarée abstraite. Classe `C` substitue `F` et fournit une implémentation réelle. Comme il n’existe aucun membre abstrait dans `C`, `C` est autorisé (mais pas obligatoire) pour être non abstraite.

#### <a name="sealed-classes"></a>Classes sealed

Le `sealed` modificateur est utilisé pour empêcher toute dérivation à partir d’une classe. Une erreur de compilation se produit si une classe sealed est spécifiée comme classe de base d’une autre classe.

Une classe sealed ne peut pas être également une classe abstraite.

Le `sealed` modificateur est principalement utilisé pour empêcher toute dérivation accidentelle, mais il permet également de certaines optimisations au moment de l’exécution. En particulier, comme une classe sealed est connue de ne jamais avoir toutes les classes dérivées, il est possible de transformer des appels de membre de fonction virtuelle sur des instances de la classe sealed en appels non virtuels.

#### <a name="static-classes"></a>Classes statiques

Le `static` modificateur est utilisé pour marquer la classe déclarée comme un ***classe statique***. Une classe statique ne peut pas être instanciée, il ne peut pas être utilisée en tant que type et peut contenir uniquement des membres statiques. Uniquement une classe statique peut contenir les déclarations de méthodes d’extension ([méthodes d’Extension](classes.md#extension-methods)).

Une déclaration de classe statique est soumis aux restrictions suivantes :

*  Une classe statique ne peut pas inclure un `sealed` ou `abstract` modificateur. Toutefois, notez que dans la mesure où une classe statique ne peut pas être instanciée ou dérivée, il se comporte comme si elle était à la fois abstract et sealed.
*  Une classe statique ne peut pas inclure un *class_base* spécification ([spécification de la base de la classe](classes.md#class-base-specification)) et vous ne pouvez pas spécifier explicitement une classe de base ou une liste des interfaces implémentées. Une classe statique hérite implicitement de type `object`.
*  Une classe statique peut contenir uniquement des membres statiques ([membres statiques et d’instance](classes.md#static-and-instance-members)). Notez que les constantes et les types imbriqués sont classés comme des membres statiques.
*  Une classe statique ne peut pas avoir de membres avec `protected` ou `protected internal` accessibilité déclarée.

Il s’agit d’une erreur de compilation à viole l’une de ces restrictions.

Une classe statique n’a aucun constructeur d’instance. Il n’est pas possible de déclarer un constructeur d’instance dans une classe statique et aucun constructeur d’instance par défaut ([constructeurs par défaut](classes.md#default-constructors)) est fourni pour une classe statique.

Les membres d’une classe statique ne sont pas automatiquement statiques et les déclarations de membre doivent inclure explicitement un `static` modificateur (à l’exception des constantes et types imbriqués). Lorsqu’une classe est imbriquée dans une classe statique externe, la classe imbriquée n’est pas une classe statique, sauf si elle n’inclut pas explicitement un `static` modificateur.

__Référencement de types de classe statique__

Un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) est autorisé à faire référence à une classe statique si

*  Le *namespace_or_type_name* est la `T` dans un *namespace_or_type_name* du formulaire `T.I`, ou
*  Le *namespace_or_type_name* est la `T` dans un *typeof_expression* ([listes d’arguments](expressions.md#argument-lists)1) sous la forme `typeof(T)`.

Un *primary_expression* ([membres de fonction](expressions.md#function-members)) est autorisé à faire référence à une classe statique si

*  Le *primary_expression* est la `E` dans un *member_access* ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) sous la forme `E.I`.

Dans un autre contexte, il est une erreur de compilation pour référencer une classe statique. Par exemple, il est une erreur pour une classe statique pour être utilisée comme classe de base, un type constitutif ([les types imbriqués](classes.md#nested-types)) d’un membre, un argument de type générique ou une contrainte de paramètre de type. De même, une classe statique ne peut pas être utilisée dans un type de tableau, un type pointeur, un `new` expression, une expression de cast, une `is` expression, une `as` expression, une `sizeof` expression ou une expression de valeur par défaut.

### <a name="partial-modifier"></a>Modificateur partiel

Le `partial` modificateur est utilisé pour indiquer que ce *class_declaration* est une déclaration de type partiel. Plusieurs déclarations de type partiel avec le même nom dans une déclaration d’espace de noms ou type englobant combinent à la déclaration d’un type de formulaire, conformément aux règles spécifiées dans [types partiels](classes.md#partial-types).

Peut être utile d’avoir la déclaration d’une classe distribuée sur des segments distincts du texte du programme si ces segments sont élaborés ou conservées dans des contextes différents. Par exemple, une partie d’une déclaration de classe peut être générée, par machine alors que l’autre est créée manuellement. Séparation textuelle des deux empêche les mises à jour par rapport à entrer en conflit avec les mises à jour par d’autres.

### <a name="type-parameters"></a>Paramètres de type

Un paramètre de type est un identificateur simple qui désigne un espace réservé pour un argument de type fourni pour créer un type construit. Un paramètre de type est un espace réservé formel pour un type qui sera fourni plus loin. En revanche, un argument de type ([arguments de Type](types.md#type-arguments)) est le type réel est remplacé par le paramètre de type lors de la création d’un type construit.

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

Chaque paramètre de type dans une déclaration de classe définit un nom de l’espace de déclaration ([déclarations](basic-concepts.md#declarations)) de cette classe. Par conséquent, il ne peut pas avoir le même nom qu’un autre paramètre de type ou un membre déclaré dans cette classe. Un paramètre de type ne peut pas avoir le même nom que le type lui-même.

### <a name="class-base-specification"></a>Spécification de classe de base

Une déclaration de classe peut inclure un *class_base* spécification qui définit la classe de base directe de la classe et les interfaces ([Interfaces](interfaces.md)) directement implémenté par la classe.

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

La classe de base spécifiée dans une déclaration de classe peut être un type de classe construite ([types construits](types.md#constructed-types)). Une classe de base ne peut pas être un paramètre de type sur son propre, même si elle peut impliquer les paramètres de type qui sont dans la portée.

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a>Classes de base

Quand un *class_type* est inclus dans le *class_base*, il spécifie la classe de base directe de la classe déclarée. Si une déclaration de classe n’a pas *class_base*, ou si le *class_base* répertorie uniquement les types interface, la classe de base directe est censée pour être `object`. Une classe hérite des membres de sa classe de base directe, comme décrit dans [héritage](classes.md#inheritance).

Dans l’exemple
```csharp
class A {}

class B: A {}
```
classe `A` on parle de la classe de base directe de `B`, et `B` est dit être dérivé `A`. Dans la mesure où `A` est pas explicitement spécifier une classe de base directe, sa classe de base directe est implicitement `object`.

Pour un type classe construite, si une classe de base est spécifiée dans la déclaration de classe générique, la classe de base du type construit est obtenue en remplaçant, pour chaque *type_parameter* dans la déclaration de classe de base correspondant *type_argument* du type construit. Compte tenu des déclarations de classe générique
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
la classe de base du type construit `G<int>` serait `B<string,int[]>`.

La classe de base directe d’un type de classe doit être au moins aussi accessible que le type de classe lui-même ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)). Par exemple, il est une erreur de compilation pour un `public` classe à dériver d’un `private` ou `internal` classe.

La classe de base directe d’un type de classe ne doit pas être un des types suivants : `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, ou `System.ValueType`. En outre, une déclaration de classe générique ne peut pas utiliser `System.Attribute` comme une classe de base directe ou indirecte.

Lors de la détermination de la signification de la spécification de la classe de base directe `A` d’une classe `B`, la classe de base directe de `B` est censé temporairement pour être `object`. Intuitivement, cela garantit que la signification d’une spécification de classe de base ne peut pas de manière récursive dépendre d’elle-même. L’exemple :
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
est une erreur depuis dans la spécification de la classe de base `A<C.B>` la classe de base directe de `C` est considéré comme étant `object`et donc (par les règles de [Namespace noms et type](basic-concepts.md#namespace-and-type-names)) `C` n’est pas censé avoir un membre `B`.

Les classes de base d’un type de classe sont la classe de base directe et ses classes de base. En d’autres termes, l’ensemble des classes de base est la fermeture transitive de la relation de la classe de base directe. Qui fait référence à l’exemple ci-dessus, les classes de base de `B` sont `A` et `object`. Dans l’exemple
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
les classes de base de `D<int>` sont `C<int[]>`, `B<IComparable<int[]>>`, `A`, et `object`.

À l’exception de classe `object`, chaque type de classe a exactement une classe de base directe. Le `object` classe n’a aucune classe de base directe et est la classe de base fondamentale de toutes les autres classes.

Lorsqu’une classe `B` dérive d’une classe `A`, c’est une erreur de compilation pour `A` dépendre `B`. Une classe ***dépend directement de*** sa classe de base directe (le cas échéant) et ***dépend directement de*** la classe dans laquelle elle est immédiatement imbriquée (le cas échéant). Étant donné cette définition, l’ensemble complet des classes dont dépend une classe est la fermeture réflexive et transitive de la ***dépend directement*** relation.

L’exemple
```csharp
class A: A {}
```
est erronée, car la classe dépend de lui-même. De même, l’exemple
```csharp
class A: B {}
class B: C {}
class C: A {}
```
est erroné, car les classes de façon circulaire dépendent d’elles-mêmes. Enfin, l’exemple
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
génère une erreur de compilation, car `A` dépend `B.C` (classe de base directe), qui dépend de `B` (sa classe immédiatement englobante), qui dépend de `A`.

Notez qu’une classe ne repose pas sur les classes qui sont imbriqués. Dans l’exemple
```csharp
class A
{
    class B: A {}
}
```
`B` dépend de `A` (étant donné que `A` est sa classe de base directe et sa classe immédiatement englobante), mais `A` ne dépend pas `B` (dans la mesure où `B` n’est ni une classe de base ni une classe englobante de `A` ). Par conséquent, l’exemple est valide.

Il n’est pas possible de dériver à partir d’un `sealed` classe. Dans l’exemple
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
classe `B` est erroné, car elle tente de dériver à partir de la `sealed` classe `A`.

#### <a name="interface-implementations"></a>Implémentations d'interfaces

Un *class_base* spécification peut inclure une liste des types d’interface, dans lequel cas la classe est dite implémenter directement les types d’interface donné. Implémentations d’interface sont abordées plus en détail dans [implémentations d’Interface](interfaces.md#interface-implementations).

### <a name="type-parameter-constraints"></a>Contraintes de paramètre de type

Déclarations de type et de méthode génériques peuvent éventuellement spécifier des contraintes de paramètre de type en incluant *type_parameter_constraints_clause*s.

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

Chaque *type_parameter_constraints_clause* se compose du jeton `where`par le nom d’un paramètre de type, suivi par un signe deux-points et la liste de contraintes pour ce paramètre de type. Il peut y avoir au plus une `where` clause pour chaque paramètre de type et le `where` clauses peuvent être répertoriées dans n’importe quel ordre. Comme le `get` et `set` jetons dans un accesseur de propriété, le `where` jeton n’est pas un mot clé.

La liste des contraintes donné dans un `where` clause peut inclure les composants suivants, dans cet ordre : une contrainte primaire unique, une ou plusieurs contraintes secondaire et la contrainte de constructeur, `new()`.

Une contrainte primaire peut être un type de classe ou la ***contrainte de type de référence*** `class` ou ***valeur de contrainte de type*** `struct`. Une contrainte secondaire peut être un *type_parameter* ou *interface_type*.

La contrainte de type référence Spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type référence. Tous les types de classe, types interface, types délégués, types tableau et les paramètres de type connus pour être un type référence (tel que défini ci-dessous) correspondent à la contrainte.

La contrainte de type valeur spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type valeur non nullable. Tous les types struct non nullable types enum et les paramètres de type ayant la contrainte de type valeur correspondent à la contrainte. Notez que bien que classées comme un type valeur, un type nullable ([types Nullable](types.md#nullable-types)) ne satisfait pas la contrainte de type valeur. Un paramètre de type ayant la contrainte de type valeur ne peut pas avoir également le *constructor_constraint*.

Types pointeur ne sont jamais autorisés à être des arguments de type et ne sont pas considérés comme pour satisfaire soit les type ou valeur contraintes de type référence.

Si une contrainte est un type de classe, un type d’interface ou un paramètre de type, ce type spécifie minimale « type de base « chaque argument de type utilisé pour ce paramètre de type doit prendre en charge. Chaque fois qu’un type construit ou une méthode générique est utilisée, l’argument de type est recherchée dans les contraintes sur le paramètre de type au moment de la compilation. L’argument de type fourni doit satisfaire les conditions décrites dans [qui satisfait aux contraintes](types.md#satisfying-constraints).

Un *class_type* contrainte doit respecter les règles suivantes :

*  Le type doit être un type de classe.
*  Le type ne doit pas être `sealed`.
*  Le type ne doit pas être un des types suivants : `System.Array`, `System.Delegate`, `System.Enum`, ou `System.ValueType`.
*  Le type ne doit pas être `object`. Étant donné que tous les types dérivent `object`, une telle contrainte n’a aucun effet si elle a été autorisée.
*  Au maximum une seule contrainte pour un paramètre de type donné peut être un type de classe.

Un type spécifié en tant qu’un *interface_type* contrainte doit respecter les règles suivantes :

*  Le type doit être un type d’interface.
*  Un type de ne doit pas être spécifié plusieurs fois dans une donnée `where` clause.

Dans les deux cas, la contrainte peut impliquer un des paramètres de type de la déclaration de méthode dans le cadre d’un type construit ou le type associé et peut impliquer le type déclaré.

N’importe quel type de classe ou interface spécifié comme une contrainte de paramètre de type doit être au moins aussi accessible ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)) en tant que le type générique ou une méthode déclarée.

Un type spécifié en tant qu’un *type_parameter* contrainte doit respecter les règles suivantes :

*  Le type doit être un paramètre de type.
*  Un type de ne doit pas être spécifié plusieurs fois dans une donnée `where` clause.

En outre aucun cycle ne doit être dans le graphique de dépendance de paramètres de type, où la dépendance est une relation transitive définie par :

*  Si un paramètre de type `T` est utilisé comme contrainte pour le paramètre de type `S` puis `S` ***dépend*** `T`.
*  Si un paramètre de type `S` dépend d’un paramètre de type `T` et `T` dépend d’un paramètre de type `U` puis `S` ***dépend*** `U`.

Cette relation, il est une erreur de compilation pour un paramètre de type à dépendre d’elle-même (directement ou indirectement).

Toutes les contraintes doivent être cohérentes entre les paramètres de type dépendant. Si le paramètre de type `S` dépend du paramètre de type `T` puis :

*  `T` ne doit pas avoir la contrainte de type valeur. Sinon, `T` est sealed, `S` devra obligatoirement être du même type que `T`, éliminant le besoin de deux paramètres de type.
*  Si `S` a la contrainte de type valeur puis `T` ne doit pas avoir un *class_type* contrainte.
*  Si `S` a un *class_type* contrainte `A` et `T` a un *class_type* contrainte `B` alors qu’il doit y avoir une conversion d’identité ou implicite conversion à partir de référence `A` à `B` ou une conversion de référence implicite à partir de `B` à `A`.
*  Si `S` dépend également de paramètre de type `U` et `U` a un *class_type* contrainte `A` et `T` a un *class_type* contrainte `B` puis il doit y avoir une conversion d’identité ou de conversion de référence implicite de `A` à `B` ou une conversion de référence implicite à partir de `B` à `A`.

Il est valide pour `S` pour que la contrainte de type valeur et `T` pour que la contrainte de type référence. En fait, cela limite `T` aux types `System.Object`, `System.ValueType`, `System.Enum`et n’importe quel type interface.

Si le `where` clause pour un paramètre de type inclut une contrainte de constructeur (qui a la forme `new()`), il est possible d’utiliser le `new` opérateur pour créer des instances du type ([lesexpressionsdecréationdel’objet](expressions.md#object-creation-expressions)). Tout type d’argument utilisé pour un paramètre de type avec une contrainte de constructeur doit avoir un constructeur sans paramètre public (ce constructeur existe implicitement pour n’importe quel type de valeur) ou être un paramètre de type ayant la contrainte de type valeur ou une contrainte de constructeur (voir [Les contraintes de paramètre de type](classes.md#type-parameter-constraints) pour plus d’informations).

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

L’exemple suivant est erroné, car il provoque une circularité dans le graphique de dépendance des paramètres de type :
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

Les exemples suivants illustrent d’autres situations non valides :
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

Le ***une classe de base efficace*** d’un paramètre de type `T` est défini comme suit :

*  Si `T` ne comporte aucune contrainte primaire ou de contraintes de paramètre de type, sa classe de base efficace est `object`.
*  Si `T` a la contrainte de type valeur, sa classe de base efficace est `System.ValueType`.
*  Si `T` a un *class_type* contrainte `C` mais aucune *type_parameter* contraintes, sa classe de base efficace est `C`.
*  Si `T` n’a aucun *class_type* contrainte a, mais un ou plusieurs *type_parameter* contraintes, sa classe de base effective est le type le plus englobé ([levé de conversion opérateurs](conversions.md#lifted-conversion-operators)) dans le jeu effectifs des classes de base de son *type_parameter* contraintes. Les règles de cohérence Vérifiez l’existence d’un tel type plus englobé.
*  Si `T` dispose à la fois un *class_type* contrainte et un ou plusieurs *type_parameter* contraintes, sa classe de base effective est le type le plus englobé ([levé de conversion opérateurs](conversions.md#lifted-conversion-operators)) dans l’ensemble comprenant le *class_type* contrainte de `T` et les classes de base effectifs de son *type_parameter* contraintes. Les règles de cohérence Vérifiez l’existence d’un tel type plus englobé.
*  Si `T` a la contrainte de type référence, mais aucune *class_type* contraintes, sa classe de base efficace est `object`.

Pour les besoins de ces règles, si T a une contrainte `V` autrement dit un *value_type*, utilisez à la place le type de base plus spécifique de `V` autrement dit un *class_type*. Cela ne peut jamais se produire dans une contrainte donnée explicitement, mais peut se produire lorsque les contraintes d’une méthode générique sont implicitement héritées par une déclaration de méthode de substitution ou une implémentation explicite d’une méthode d’interface.

Ces règles garantissent que la classe de base efficace est toujours un *class_type*.

Le ***ensemble efficace interface*** d’un paramètre de type `T` est défini comme suit :

*  Si `T` n’a aucun *secondary_constraints*, son jeu d’interface efficace est vide.
*  Si `T` a *interface_type* contraintes, mais aucune *type_parameter* contraintes, son jeu d’interface efficace est son ensemble de *interface_type* contraintes.
*  Si `T` n’a aucun *interface_type* contraintes, mais a *type_parameter* contraintes, son jeu d’interface efficace est l’union des jeux interface efficace de son *type_ paramètre* contraintes.
*  Si `T` dispose à la fois *interface_type* contraintes et *type_parameter* contraintes, son jeu d’interface efficace est l’union de son ensemble de *interface_type* contraintes et les ensembles d’interface effectif de son *type_parameter* contraintes.

Est un paramètre de type ***connu pour être un type référence*** si elle a la contrainte de type référence ou sa classe de base efficace n’est pas `object` ou `System.ValueType`.

Valeurs d’un type de paramètre de type contraint peuvent être utilisées pour accéder aux membres d’instance impliquées par les contraintes. Dans l’exemple
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
les méthodes de `IPrintable` peut être appelée directement sur `x` car `T` pour toujours implémenter `IPrintable`.

### <a name="class-body"></a>Corps de la classe

Le *class_body* d’une classe définit les membres de cette classe.

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a>Types partiels

Une déclaration de type peut être répartie entre plusieurs ***les déclarations de type partiel***. La déclaration de type est construite à partir de ses parties en suivant les règles dans cette section, après quoi il est traité comme une déclaration unique pendant le reste du traitement du moment de la compilation et exécution du programme.

Un *class_declaration*, *struct_declaration* ou *interface_declaration* représente une déclaration de type partiel si elle inclut un `partial` modificateur. `partial` n’est pas un mot clé et agit uniquement comme un modificateur si elle apparaît immédiatement avant un des mots-clés `class`, `struct` ou `interface` dans une déclaration de type, ou avant le type de `void` dans une déclaration de méthode. Dans d’autres contextes, il peut être utilisé comme identificateur normal.

Chaque partie d’une déclaration de type partiel doit inclure un `partial` modificateur. Il doit avoir le même nom et être déclaré dans le même espace de noms ou de la déclaration de type en tant que les autres parties. Le `partial` modificateur indique que des parties supplémentaires de la déclaration de type peuvent exister ailleurs, mais l’existence de ces parties supplémentaires n’est pas obligatoire ; il est valide pour un type avec une déclaration unique à inclure le `partial` modificateur.

Toutes les parties d’un type partiel doivent être compilés ensemble, telles que les parties peuvent être fusionnées au moment de la compilation en une seule déclaration de type. Plus précisément, types partiels n’autorisent pas les types déjà compilées à étendre.

Les types imbriqués peuvent être déclarés dans plusieurs parties à l’aide de la `partial` modificateur. En règle générale, le type conteneur est déclaré à l’aide de `partial` , ainsi chaque partie du type imbriqué déclaré dans une autre partie du type conteneur.

Le `partial` modificateur n’est pas autorisé dans les déclarations de délégué ou enum.

### <a name="attributes"></a>Attributs

Les attributs d’un type partiel sont déterminés en combinant, dans un ordre non spécifié, les attributs de chacune des parties. Si un attribut est placé sur plusieurs parties, il est équivalent à la spécification de l’attribut plusieurs fois sur le type. Par exemple, les deux parties :

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
sont équivalentes à une déclaration comme :
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

Combinent des attributs sur les paramètres de type de la même manière.

### <a name="modifiers"></a>Modificateurs

Lorsqu’une déclaration de type partiel inclut une spécification d’accessibilité (le `public`, `protected`, `internal`, et `private` modificateurs) il doit correspondre à toutes les autres parties qui incluent une spécification d’accessibilité. Si aucune partie d’un type partiel n’inclut une spécification d’accessibilité, le type est fourni à l’accessibilité par défaut approprié ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Si un ou plusieurs déclarations partielles d’un type imbriqué incluent un `new` modificateur, aucun avertissement n’est signalée si le type imbriqué masque un membre hérité ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)).

Si un ou plusieurs déclarations partielles d’une classe incluent un `abstract` modificateur, la classe est considérée abstraite ([classes abstraites](classes.md#abstract-classes)). Sinon, la classe est considérée comme non abstraite.

Si un ou plusieurs déclarations partielles d’une classe incluent un `sealed` modificateur, la classe est considéré comme sealed ([des classes Sealed](classes.md#sealed-classes)). Sinon, la classe est considérée comme non scellé.

Notez qu’une classe ne peut pas être abstract et sealed.

Lorsque le `unsafe` modificateur est utilisé dans une déclaration de type partiel, seule cette partie est considérée comme un contexte unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)).

### <a name="type-parameters-and-constraints"></a>Paramètres de type et contraintes

Si un type générique est déclaré dans plusieurs parties, chaque partie doit indiquer les paramètres de type. Chaque partie doit avoir le même nombre de paramètres de type et le même nom pour chaque paramètre de type dans l’ordre.

Lorsqu’une déclaration de type générique partiel inclut des contraintes (`where` clauses), les contraintes doivent correspondre à tous les autres parties qui incluent des contraintes. Plus précisément, chaque partie qui inclut des contraintes doit avoir des contraintes pour le même ensemble de paramètres de type, et pour chaque paramètre de type les ensembles de principal, secondaire et les contraintes de constructeur doivent être équivalente. Deux ensembles de contraintes sont équivalentes si elles contiennent les mêmes membres. Si aucune partie d’un type générique partiel ne spécifie les contraintes de paramètre de type, le type de paramètres sont considérés comme sans contrainte.

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
est correct, car les parties qui incluent des contraintes (les deux premiers) efficacement spécifient la même ensemble de principal, secondaire, les contraintes de constructeur pour le même ensemble de paramètres de type, respectivement.

### <a name="base-class"></a>Classe de base

Lorsqu’une déclaration de classe partielle inclut une spécification de classe de base, il doit correspondre à toutes les autres parties qui incluent une spécification de classe de base. Si aucune partie d’une classe partielle n’inclut une spécification de classe de base, la classe de base devient `System.Object` ([classes de Base](classes.md#base-classes)).

### <a name="base-interfaces"></a>Interfaces de base

Le jeu d’interfaces de base pour un type déclaré dans plusieurs parties est l’union des interfaces de base spécifiée sur chaque partie. Une interface de base donnée peut être nommée qu’une seule fois sur chaque partie, mais il est possible de plusieurs articles nommer les mêmes interfaces de base. Il ne doit exister qu’une seule implémentation des membres de n’importe quelle interface de base donnée.

Dans l’exemple
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
le jeu d’interfaces de base pour la classe `C` est `IA`, `IB`, et `IC`.

En règle générale, chaque partie fournit une implémentation des interfaces déclarées sur ce tiers ; Toutefois, cela n’est pas une exigence. Une partie peut fournir l’implémentation pour une interface déclarée sur une autre partie :
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

À l’exception des méthodes partielles ([méthodes partielles](classes.md#partial-methods)), le jeu de membres d’un type déclaré dans plusieurs parties est simplement l’union de l’ensemble des membres déclarés dans chaque partie. Les organismes de toutes les parties de la déclaration de type partagent le même espace de déclaration ([déclarations](basic-concepts.md#declarations)) et la portée de chaque membre ([étendues](basic-concepts.md#scopes)) s’étend aux organismes de toutes les parties. Le domaine d’accessibilité d’un membre inclut toujours toutes les parties du type englobant ; un `private` membre déclaré dans une partie est librement accessible à partir d’une autre partie. C’est une erreur de compilation pour déclarer le même membre dans plusieurs parties du type, sauf si ce membre est un type avec le `partial` modificateur.

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

L’ordre des membres au sein d’un type est rarement importante au code c#, mais peut être importante lors de l’interfaçage avec d’autres langages et environnements. Dans ce cas, l’ordre des membres au sein d’un type déclaré dans plusieurs parties n’est pas défini.

### <a name="partial-methods"></a>Méthodes partielles

Méthodes partielles peuvent être définies dans une partie d’une déclaration de type et implémentés dans un autre. L’implémentation est facultative. Si aucune partie n’implémente la méthode partielle, la déclaration de méthode partielle et tous les appels à celle-ci sont supprimés de la déclaration de type résultant de la combinaison des parties.

Les méthodes partielles ne peuvent pas définir de modificateurs d’accès, mais sont implicitement `private`. Leur type de retour doit être `void`, et leurs paramètres ne peut pas avoir le `out` modificateur. L’identificateur `partial` est reconnu comme un mot clé spécial dans une déclaration de méthode uniquement s’il apparaît juste avant le `void` type ; sinon il peut être utilisé comme un identificateur normal. Une méthode partielle ne peut pas implémenter explicitement les méthodes d’interface.

Il existe deux types de déclarations de méthode partielles : Si le corps de la déclaration de méthode est un point-virgule, la déclaration est dite un ***déclaration de méthode partielle de définition***. Si le corps est indiqué comme un *bloc*, la déclaration est dite un ***mise en œuvre de la déclaration de méthode partielle***. Entre les parties d’une déclaration de type il peut y avoir qu’une déclaration de méthode partielle avec une signature donnée de définition et il peut y avoir qu’une seule déclaration de méthode partielle avec une signature donnée d’implémentation. Si une déclaration de méthode partielle implémentation est donnée, une déclaration de méthode partielle de définition correspondant doit exister, et les déclarations doivent correspondre comme spécifié dans le code suivant :

* Les déclarations doivent avoir les mêmes modificateurs (bien que pas nécessairement dans le même ordre), nom de la méthode, le nombre de paramètres de type et nombre de paramètres.
* Paramètres correspondants dans les déclarations doivent avoir les mêmes modificateurs (bien que pas nécessairement dans le même ordre) et les mêmes types (modulo différences dans les noms de paramètre de type).
* Les paramètres de type correspondant dans les déclarations doivent avoir les mêmes contraintes (modulo différences dans les noms de paramètre de type).

Déclaration de méthode partielle d’implémentation peut apparaître dans la même partie de la déclaration de méthode partielle de définition correspondante.

Une seule définition partielle méthode participe à la résolution de surcharge. Par conséquent, si une déclaration d’implémentation est fournie, expressions d’appel peuvent être résolu en appels de la méthode partielle. Comme une méthode partielle retourne toujours `void`, ces expressions d’appel sera toujours les instructions d’expression. En outre, car une méthode partielle est implicitement `private`, ces instructions se produira toujours au sein d’une des parties de la déclaration de type dans lequel la méthode partielle est déclarée.

Si aucune partie d’une déclaration de type partiel ne contient une déclaration d’implémentation pour une méthode partielle, n’importe quelle instruction d’expression appeler est simplement supprimée de la déclaration de type combinée. Par conséquent, l’expression d’appel, y compris toutes les expressions qui le composent, n’a aucun effet au moment de l’exécution. La méthode partielle lui-même est également supprimée et ne sera pas membre de la déclaration de type combinée.

Si une déclaration d’implémentation existe pour une méthode partielle, les appels de méthodes partielles sont conservés. La méthode partielle donne lieu à une déclaration de méthode semblable à la déclaration de méthode partielle implémentation à l’exception de ce qui suit :

* Le `partial` modificateur n’est pas inclus
* Les attributs dans la déclaration de méthode qui en résulte sont les attributs combinées de la définition et de la déclaration de méthode partielle implémentation dans un ordre non spécifié. Les doublons ne sont pas supprimés.
* Les attributs des paramètres de la déclaration de méthode qui en résulte sont les attributs combinés de paramètres correspondants de la définition et la déclaration de méthode partielle implémentation dans un ordre non spécifié. Les doublons ne sont pas supprimés.

Si une déclaration de définition, mais pas une déclaration d’implémentation est indiquée pour une méthode partielle M, les restrictions suivantes s’appliquent :

* C’est une erreur de compilation pour créer un délégué à la méthode ([expressions de création de délégué](expressions.md#delegate-creation-expressions)).
* Il s’agit d’une erreur de compilation pour faire référence à `M` à l’intérieur d’une fonction anonyme qui est convertie en un type arborescence d’expression ([évaluation des conversions de fonction anonyme pour les types d’arborescence expression](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).
* Les expressions qui se produisent dans le cadre d’un appel de `M` n’affectent pas l’état d’assignation définie ([assignation définie](variables.md#definite-assignment)), ce qui peut potentiellement entraîner des erreurs de compilation.
* `M` ne peut pas être le point d’entrée pour une application ([démarrage de l’Application](basic-concepts.md#application-startup)).

Les méthodes partielles sont utiles pour autoriser une partie d’une déclaration de type pour personnaliser le comportement d’une autre partie, par exemple, une qui est généré par un outil. Considérons la déclaration de classe partielle suivante :
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

Si cette classe est compilée sans tous les autres composants, les déclarations de méthode partielle de définition et de leurs appels seront supprimés, et la déclaration de classe combiné résultant sera équivalente à la suivante :
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

Ensuite, la déclaration de classe combiné résultant sera équivalente à la suivante :
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

Bien que chaque partie d’un type extensible doit être déclaré dans le même espace de noms, les parties sont généralement écrits dans les déclarations d’espace de noms différent. Par conséquent, différents `using` directives ([à l’aide de directives](namespaces.md#using-directives)) peuvent être présents pour chaque partie. Lors de l’interprétation des noms simples ([l’inférence de Type](expressions.md#type-inference)) au sein d’une part, uniquement le `using` directives de l’espace de noms ou des déclarations englobant cette partie sont considérés comme. Cela peut entraîner le même identificateur avoir des significations différentes dans différentes parties :
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

Les membres d’une classe se composent des membres introduits par ses *class_member_declaration*s et les membres héritent de la classe de base directe.

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

Les membres d’un type de classe sont réparties dans les catégories suivantes :

*  Constantes, qui représentent des valeurs constantes associées à la classe ([constantes](classes.md#constants)).
*  Champs, qui sont les variables de la classe ([champs](classes.md#fields)).
*  Méthodes qui implémentent les calculs et les actions qui peuvent être effectuées par la classe ([méthodes](classes.md#methods)).
*  Propriétés qui définissent des caractéristiques nommées et les actions associées à la lecture et l’écriture de ces caractéristiques ([propriétés](classes.md#properties)).
*  Événements, qui définissent les notifications peuvent être générées par la classe ([événements](classes.md#events)).
*  Les indexeurs, qui permettent d’instances de la classe à indexer de la même manière (point de vue syntaxique) en tant que tableaux ([indexeurs](classes.md#indexers)).
*  Opérateurs, qui définissent les opérateurs d’expression qui peuvent être appliquées aux instances de la classe ([opérateurs](classes.md#operators)).
*  Constructeurs d’instance, qui implémentent les actions requises pour initialiser les instances de la classe ([constructeurs d’Instance](classes.md#instance-constructors))
*  Les destructeurs, qui implémentent les actions à effectuer avant que les instances de la classe sont ignorés définitivement ([destructeurs](classes.md#destructors)).
*  Les constructeurs statiques, qui implémentent les actions requises pour initialiser la classe elle-même ([constructeurs statiques](classes.md#static-constructors)).
*  Types qui représentent les types qui sont locales à la classe ([les types imbriqués](classes.md#nested-types)).

Les membres qui peuvent contenir du code exécutable sont collectivement regroupés sous le *membres de fonction* le type de classe. Les fonctions membres d’un type de classe sont les méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et les constructeurs statiques de ce type de classe.

Un *class_declaration* crée un nouvel espace de déclaration ([déclarations](basic-concepts.md#declarations)) et le *class_member_declaration*s immédiatement contenues par la *classe _declaration* introduire de nouveaux membres dans cet espace de déclaration. Les règles suivantes s’appliquent aux *class_member_declaration*%s :

*  Constructeurs d’instance, destructeurs et les constructeurs statiques doivent avoir le même nom que la classe immédiatement englobante. Tous les autres membres doivent avoir des noms qui diffèrent du nom de la classe immédiatement englobante.
*  Le nom de constante, champ, propriété, événement, type ou doit différer des noms de tous les autres membres déclarés dans la même classe.
*  Le nom d’une méthode doit différer des noms de tous les autres méthodes non déclarés dans la même classe. En outre, la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir signatures qui diffèrent uniquement par `ref` et `out`.
*  La signature d’un constructeur d’instance doit être différente des signatures de tous les autres constructeurs d’instance déclarés dans la même classe, et deux constructeurs déclarés dans la même classe ne peuvent pas avoir signatures qui diffèrent uniquement par `ref` et `out`.
*  La signature d’un indexeur doit différer des signatures de tous les autres indexeurs déclarés dans la même classe.
*  La signature d’un opérateur doit différer des signatures de tous les autres opérateurs déclarés dans la même classe.

Les membres hérités d’un type de classe ([héritage](classes.md#inheritance)) ne font pas partie de l’espace de déclaration d’une classe. Par conséquent, une classe dérivée est autorisée à déclarer un membre portant le même nom ou la même signature qu’un membre hérité (ce qui en vigueur masque le membre hérité).

### <a name="the-instance-type"></a>Le type d’instance

Chaque déclaration de classe a un type de liaison associé ([liés et indépendants des types](types.md#bound-and-unbound-types)), la ***type d’instance***. Pour une déclaration de classe générique, le type d’instance est formé en créant un type construit ([types construits](types.md#constructed-types)) à partir de la déclaration de type, avec le type fourni d’arguments en cours correspondant paramètre de type. Étant donné que le type d’instance utilise les paramètres de type, il peut uniquement être utilisé où les paramètres de type sont dans la portée ; Autrement dit, à l’intérieur de la déclaration de classe. Le type d’instance est le type de `this` pour le code écrit à l’intérieur de la déclaration de classe. Pour les classes non génériques, le type d’instance est simplement la classe déclarée. L’exemple suivant montre plusieurs déclarations de classe, ainsi que leurs types d’instance : 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a>Membres de types construits

Les membres non héritées d’un type construit sont obtenues en remplaçant, pour chaque *type_parameter* dans la déclaration de membre correspondant *type_argument* du type construit. Le processus de substitution est basé sur la signification sémantique de déclarations de type et n’est pas simplement textuelle substitution.

Par exemple, étant donné la déclaration de classe générique
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
le type construit `Gen<int[],IComparable<string>>` possède les membres suivants :
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

Le type du membre `a` dans la déclaration de classe générique `Gen` est « tableau à deux dimensions de `T`», de sorte que le type du membre `a` dans le type construit ci-dessus est « tableau à deux dimensions de tableau unidimensionnel de `int`», ou `int[,][]`.

Dans les fonctions membres d’instance, le type de `this` est le type d’instance ([le type d’instance](classes.md#the-instance-type)) de la déclaration du conteneur.

Tous les membres d’une classe générique peuvent utiliser les paramètres de type à partir de n’importe quelle classe englobante, directement ou en tant que partie d’un type construit. Fermeture d’un particulier de type construit ([ouvert et fermé types](types.md#open-and-closed-types)) est utilisé au moment de l’exécution, chaque utilisation d’un paramètre de type est remplacée par l’argument de type réel fourni pour le type construit. Exemple :
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

Une classe ***hérite*** les membres de son type de classe de base directe. L’héritage signifie qu’une classe contient implicitement tous les membres de son type de classe de base directe, sauf pour les constructeurs d’instance, les destructeurs et les constructeurs statiques de la classe de base. Voici quelques aspects importants d’héritage sont :

*  L’héritage est transitif. Si `C` est dérivée de `B`, et `B` est dérivée de `A`, puis `C` hérite des membres déclarés dans `B` , ainsi que les membres déclarés dans `A`.
*  Une classe dérivée étend sa classe de base directe. Une classe dérivée peut ajouter des membres hérités, mais ne peut pas supprimer la définition d’un membre hérité.
*  Constructeurs d’instance, destructeurs et les constructeurs statiques ne sont pas hérités, mais tous les autres membres sont, quel que soit leur accessibilité déclarée ([l’accès au membre](basic-concepts.md#member-access)). Toutefois, en fonction de leur accessibilité déclarée, les membres hérités est peut-être pas accessible dans une classe dérivée.
*  Une classe dérivée peut ***masquer*** ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)) les membres hérités en déclarant de nouveaux membres avec le même nom ou la même signature. Notez toutefois que masquer un membre hérité ne supprime pas ce membre, il est simplement ce membre inaccessible directement par le biais de la classe dérivée.
*  Une instance d’une classe contient un ensemble de tous les champs d’instance déclarés dans la classe et ses classes de base et une conversion implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) existe à partir d’un type de classe dérivée à un de ses types de classe de base. Par conséquent, une référence à une instance d’une classe dérivée peut être traitée comme une référence à une instance d’une de ses classes de base.
*  Une classe peut déclarer des indexeurs, propriétés et méthodes virtuelles et les classes dérivées peuvent substituer l’implémentation de ces fonctions membres. Cela permet aux classes un comportement polymorphe dans laquelle les actions effectuées par un appel de fonction membre varie selon le type au moment de l’exécution de l’instance par le biais duquel cette fonction membre est appelée.

Le membre hérité d’un type classe construite sont les membres du type de classe de base immédiate ([classes de Base](classes.md#base-classes)), qui se trouve en substituant les arguments de type du type construit pour chaque occurrence du type correspondant paramètres dans le *class_base* spécification. Ces membres, à son tour, sont transformés en substituant, pour chaque *type_parameter* dans la déclaration de membre correspondant *type_argument* de la *class_base* spécification.

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

Dans l’exemple ci-dessus, le type construit `D<int>` a un membre non héritées `public int G(string s)` obtenue en remplaçant l’argument de type `int` pour le paramètre de type `T`. `D<int>` possède également un membre hérité à partir de la déclaration de classe `B`. Ce membre hérité est déterminé en déterminant d’abord le type de classe de base `B<int[]>` de `D<int>` en substituant `int` pour `T` dans la spécification de la classe de base `B<T[]>`. Puis, sous la forme d’un argument de type `B`, `int[]` est remplacé par `U` dans `public U F(long index)`, on obtient le membre hérité `public int[] F(long index)`.

### <a name="the-new-modifier"></a>Le nouveau modificateur

Un *class_member_declaration* est autorisé à déclarer un membre portant le même nom ou la même signature qu’un membre hérité. Lorsque cela se produit, le membre de classe dérivée est dite ***masquer*** le membre de classe de base. Masquer un membre hérité n'est pas considéré comme une erreur, mais elle entraîne le compilateur à émettre un avertissement. Pour supprimer l’avertissement, la déclaration du membre de classe dérivée peut inclure un `new` modificateur pour indiquer que le membre dérivé est conçu pour masquer le membre de base. Ce sujet est abordé plus en détail dans [masquage par héritage](basic-concepts.md#hiding-through-inheritance).

Si un `new` modificateur est inclus dans une déclaration qui ne masque pas un membre hérité, un avertissement est émis à cet effet. Cet avertissement est supprimé en supprimant le `new` modificateur.

### <a name="access-modifiers"></a>Modificateurs d’accès

Un *class_member_declaration* peut avoir l’une des cinq niveaux d’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) : `public`, `protected internal`, `protected`, `internal` , ou `private`. À l’exception de la `protected internal` combinaison, il est une erreur de compilation pour spécifier plus d’un modificateur d’accès. Quand un *class_member_declaration* n’inclut pas les modificateurs d’accès, `private` est supposé.

### <a name="constituent-types"></a>Types constitutifs

Les types qui sont utilisés dans la déclaration d’un membre sont appelés types constitutifs de ce membre. Les types constitutifs possibles sont le type d’une constante, champ, propriété, événement ou l’indexeur, le type de retour d’une méthode ou un opérateur et les types de paramètres d’une méthode, un indexeur, un opérateur ou un constructeur d’instance. Les types constitutifs d’un membre doivent être au moins aussi accessibles que ce membre lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

### <a name="static-and-instance-members"></a>Membres statiques et d’instance

Membres d’une classe sont soit ***membres statiques*** ou ***membres d’instance***. En règle générale, il est utile de considérer les membres statiques comme appartenant à des types de classes et membres d’instance comme appartenant à des objets (instances de types de classe).

Lorsqu’une déclaration de champ, méthode, propriété, événement, opérateur ou le constructeur inclut un `static` modificateur, elle déclare un membre statique. En outre, une déclaration de constante ou de type déclare implicitement un membre statique. Membres statiques ont les caractéristiques suivantes :

*  Lorsqu’un membre statique `M` est référencé dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`, `E` doit indiquer un type contenant `M`. Il s’agit d’une erreur lors de la compilation pour `E` pour désigner une instance.
*  Un champ statique identifie exactement un emplacement de stockage devant être partagé par toutes les instances d’un type de classe fermé donnée. Quel que soit le nombre d’instances d’un type de classe fermé donnée est créé, il n'existe qu’une seule copie d’un champ statique.
*  Une fonction membre statique (méthode, propriété, événement, opérateur ou constructeur) ne fonctionne pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire référence à `this` dans une fonction membre.

Lorsqu’une déclaration de champ, méthode, propriété, événement, indexeur, constructeur ou un destructeur n’inclut pas un `static` modificateur, elle déclare un membre d’instance. (Un membre d’instance est parfois appelé un membre non statique). Membres d’instance ont les caractéristiques suivantes :

*  Lorsqu’un membre d’instance `M` est référencé dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`, `E` doit indiquer une instance d’un type contenant `M`. Il est une erreur au moment de la liaison pour `E` pour indiquer un type.
*  Chaque instance d’une classe contient un ensemble distinct de tous les champs d’instance de la classe.
*  Un fonction membre d’instance (méthode, propriété, indexeur, constructeur d’instance ou destructeur) fonctionne sur une instance donnée de la classe, et cette instance est accessible en tant que `this` ([cet accès](expressions.md#this-access)).

L’exemple suivant illustre les règles d’accès aux statiques et membres d’instance :
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

Le `F` méthode montre que dans une fonction membre d’instance, un *simple_name* ([noms simples](expressions.md#simple-names)) peut être utilisé pour accéder aux membres d’instance et membres statiques. Le `G` méthode montre que dans une fonction membre statique, une erreur de compilation pour accéder à un membre d’instance via un *simple_name*. Le `Main` méthode montre que dans un *member_access* ([l’accès au membre](expressions.md#member-access)), les membres d’instance doivent être accessibles via des instances et des membres statiques doivent être accessible via les types.

### <a name="nested-types"></a>Types imbriqués

Un type déclaré dans une déclaration de classe ou un struct est appelé un ***type imbriqué***. Un type qui est déclaré dans une unité de compilation ou d’un espace de noms est appelé un ***type non imbriqué***.

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
classe `B` est un type imbriqué, car il est déclaré au sein de la classe `A`et la classe `A` est un type non imbriqué, car il est déclaré au sein d’une unité de compilation.

#### <a name="fully-qualified-name"></a>Nom qualifié complet

Le nom qualifié complet ([noms qualifiés complets](basic-concepts.md#fully-qualified-names)) pour un type imbriqué est `S.N` où `S` est le nom qualifié complet du type dans le type `N` est déclaré.

#### <a name="declared-accessibility"></a>Accessibilité déclarée

Les types non imbriqués peuvent avoir `public` ou `internal` accessibilité déclarée et avez `internal` accessibilité déclarée par défaut. Les types imbriqués peuvent avoir ces formes d’accessibilité déclarée trop, ainsi que d’un ou plusieurs des formes supplémentaires d’accessibilité déclarée, selon que le type conteneur est une classe ou un struct :

*  Un type imbriqué qui est déclaré dans une classe peut avoir une des cinq formes possibles d’accessibilité déclarée (`public`, `protected internal`, `protected`, `internal`, ou `private`) et, comme d’autres membres de classe, valeur par défaut est `private` déclaré accessibilité.
*  Un type imbriqué qui est déclaré dans un struct peut avoir une des trois formes d’accessibilité déclarée (`public`, `internal`, ou `private`) et, comme d’autres membres de struct, valeur par défaut est `private` accessibilité déclarée.

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
déclare une classe privée imbriquée `Node`.

#### <a name="hiding"></a>Masquage

Un type imbriqué peut masquer ([masquage de nom](basic-concepts.md#name-hiding)) un membre de base. Le `new` modificateur est autorisé sur les déclarations de type imbriqué afin que le masquage peut être exprimé explicitement. L’exemple
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
montre une classe imbriquée `M` qui masque la méthode `M` défini dans `Base`.

#### <a name="this-access"></a>Cet accès

Un type imbriqué et son type conteneur n’ont pas une relation spéciale aux *this_access* ([cet accès](expressions.md#this-access)). Plus précisément, `this` au sein d’un type imbriqué ne peut pas être utilisé pour faire référence aux membres d’instance du type conteneur. Dans les cas où un type imbriqué a besoin d’accéder aux membres d’instance de son type conteneur, l’accès peut être fourni en fournissant le `this` pour l’instance du type conteneur comme un argument de constructeur pour le type imbriqué. L’exemple suivant
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
illustre cette technique. Une instance de `C` crée une instance de `Nested` et transmet son propre `this` à `Nested`du constructeur afin de fournir un accès ultérieur à `C`de membres d’instance.

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a>Accès aux membres privés et protégés du type conteneur

Un type imbriqué a accès à tous les membres qui sont accessibles à son type conteneur, y compris les membres du type conteneur qui ont `private` et `protected` accessibilité déclarée. L’exemple
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
montre une classe `C` qui contient une classe imbriquée `Nested`. Dans `Nested`, la méthode `G` appelle la méthode statique `F` définies dans `C`, et `F` a private accessibilité déclarée.

Un type imbriqué peut également accéder aux membres protégés définis dans un type de base de son type conteneur. Dans l’exemple
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
la classe imbriquée `Derived.Nested` accède à la méthode protégée `F` définies dans `Derived`de classe de base `Base`, par l’appel via une instance de `Derived`.

#### <a name="nested-types-in-generic-classes"></a>Types imbriqués dans les classes génériques

Une déclaration de classe générique peut contenir des déclarations de type imbriqué. Les paramètres de type de la classe englobante peuvent être utilisés dans les types imbriqués. Une déclaration de type imbriqué peut contenir des paramètres de type supplémentaires qui s’appliquent uniquement au type imbriqué.

Chaque déclaration de type contenue dans une déclaration de classe générique est implicitement une déclaration de type générique. Lors de l’écriture d’une référence à un type imbriqué dans un type générique, le type construit qui le contient, y compris ses arguments de type doit être nommé. Toutefois, à partir d’au sein de la classe externe, le type imbriqué utilisable sans qualification ; le type d’instance de la classe externe peut être utilisé implicitement lors de la construction de type imbriqué. L’exemple suivant montre trois façons correctes pour faire référence à un type construit, créé à partir de `Inner`; les deux premières sont équivalentes :
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

Bien qu’il soit incorrecte programmation de style, un paramètre de type dans un type imbriqué peut masquer un membre ou paramètre de type déclaré dans le type externe :
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a>Noms de membre réservés

Pour faciliter le c# exécution implémentation sous-jacente, pour chaque déclaration de membre source qui est une propriété, un événement ou un indexeur, l’implémentation doit réserver deux signatures de méthode en fonction du type de la déclaration de membre, son nom et son type. C’est une erreur de compilation d’un programme déclarer un membre dont la signature correspond à l’une de ces signatures réservées, même si l’implémentation d’exécution sous-jacente ne rend pas utiliser ces réservations.

Les noms réservés n’introduisent pas de déclarations, par conséquent, ils ne participent pas rechercher un membre. Toutefois, une déclaration associé du signatures participent à l’héritage de méthode réservée ([héritage](classes.md#inheritance)) et peuvent être masqués avec le `new` modificateur ([le nouveau modificateur](classes.md#the-new-modifier)).

La réservation de ces noms répond à trois objectifs :

*  Pour permettre l’implémentation sous-jacente utiliser un identificateur ordinaire en tant que nom de méthode pour obtenir ou définir l’accès à la fonctionnalité de langage c#.
*  Pour autoriser d’autres langages à interagir à l’aide d’un identificateur ordinaire en tant que nom de méthode pour obtenir ou définir l’accès à la fonctionnalité de langage c#.
*  Pour vous assurer que la source acceptée par un compilateur conforme est acceptée par un autre, en rendant les détails de membre réservé noms cohérente sur toutes les implémentations c#.

La déclaration d’un destructeur ([destructeurs](classes.md#destructors)) entraîne également une signature à réserver ([les noms de membre réservés pour les destructeurs](classes.md#member-names-reserved-for-destructors)).

#### <a name="member-names-reserved-for-properties"></a>Noms de membres réservés pour les propriétés

Pour une propriété `P` ([propriétés](classes.md#properties)) de type `T`, les signatures suivantes sont réservées :

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
une classe `A` définit une propriété en lecture seule `P`, réservant ainsi des signatures pour `get_P` et `set_P` méthodes. Une classe `B` dérive `A` et masque les deux de ces signatures réservées. L’exemple génère la sortie :
```
123
123
456
```

#### <a name="member-names-reserved-for-events"></a>Noms de membres réservés pour les événements

Pour un événement `E` ([événements](classes.md#events)) du type de délégué `T`, les signatures suivantes sont réservées :
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a>Noms de membres réservés pour les indexeurs

Pour un indexeur ([indexeurs](classes.md#indexers)) de type `T` avec liste de paramètres `L`, les signatures suivantes sont réservées :
```csharp
T get_Item(L);
void set_Item(L, T value);
```

Les deux signatures sont réservées, même si l’indexeur est en lecture seule ou en écriture seule.

En outre le nom du membre `Item` est réservé.

#### <a name="member-names-reserved-for-destructors"></a>Noms de membres réservés pour les destructeurs

Pour une classe contenant un destructeur ([destructeurs](classes.md#destructors)), la signature suivante est réservée :
```csharp
void Finalize();
```

## <a name="constants"></a>Constantes

Un ***constante*** est un membre de classe qui représente une valeur constante : une valeur qui peut être calculée au moment de la compilation. Un *constant_declaration* introduit une ou plusieurs constantes d’un type donné.

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

Un *constant_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)), un `new` modificateur ([le nouveau modificateur](classes.md#the-new-modifier)), et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)). Les attributs et les modificateurs s’appliquent à tous les membres déclarés par le *constant_declaration*. Même si les constantes sont considérés comme des membres statiques, une *constant_declaration* aucune ne nécessite ni n’autorise un `static` modificateur. C’est une erreur pour le même modificateur apparaît plusieurs fois dans une déclaration de constante.

Le *type* d’un *constant_declaration* Spécifie le type des membres introduits par la déclaration. Le type est suivi d’une liste de *constant_declarator*s, chacun d’eux présente un nouveau membre. A *constant_declarator* se compose d’un *identificateur* qui nomme le membre, suivi d’un «`=`» jeton, suivi par un *constant_expression* ([ Expressions constantes](expressions.md#constant-expressions)) qui fournit la valeur du membre.

Le *type* spécifié dans une constante de déclaration doit être `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, un *type_de_liste*, ou un *reference_type*. Chaque *constant_expression* doit produire une valeur du type cible ou d’un type qui peut être converti en type cible par une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).

Le *type* d’une constante doit être au moins aussi accessibles que la constante elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

La valeur d’une constante est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)) ou un *member_access* ([l’accès au membre](expressions.md#member-access)).

Une constante peut elle-même faire partie d’un *constant_expression*. Par conséquent, une constante peut être utilisée dans n’importe quelle construction nécessite un *constant_expression*. Exemples de ces constructions `case` étiquettes, `goto case` instructions, `enum` autres déclarations de constantes, des attributs et des déclarations de membre.

Comme décrit dans [expressions constantes](expressions.md#constant-expressions), un *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation. Depuis la seule façon de créer une valeur non null d’un *reference_type* autre que `string` consiste à appliquer le `new` opérateur et depuis le `new` opérateur n’est pas autorisé dans un *constant_ expression*, la seule valeur possible pour les constantes de *reference_type*autres que `string` est `null`.

Lorsqu’un nom symbolique d’une valeur constante est souhaité, mais lorsque le type de cette valeur n’est pas autorisé dans une déclaration de constante, ou que la valeur ne peut pas être calculée au moment de la compilation par un *constant_expression*, un `readonly` champ () [Champs en lecture seule](classes.md#readonly-fields)) peut être utilisé à la place.

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

Les constantes peuvent dépendre d’autres constantes au sein du même programme tant que les dépendances ne sont pas de nature circulaire. Le compilateur organise automatiquement les évaluer les déclarations de constante dans l’ordre approprié. Dans l’exemple
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
le compilateur évalue d’abord `A.Y`, puis évalue `B.Z`et enfin évalue `A.X`, produisant les valeurs `10`, `11`, et `12`. Déclarations de constante peuvent dépendre de constantes d’autres programmes, mais ces dépendances ne sont possibles dans une seule direction. Qui fait référence à l’exemple ci-dessus, si `A` et `B` ont été déclarées dans des programmes distincts, il serait possible pour `A.X` dépendre `B.Z`, mais `B.Z` puis pas simultanément dépend de `A.Y`.

## <a name="fields"></a>Champs

Un ***champ*** est un membre qui représente une variable associée à un objet ou une classe. Un *field_declaration* présente un ou plusieurs champs d’un type donné.

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

Un *field_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)), un `new` modificateur ([le nouveau modificateur](classes.md#the-new-modifier)), un une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) et un `static` modificateur ([champs statiques et d’instance](classes.md#static-and-instance-fields)). En outre, un *field_declaration* peut inclure un `readonly` modificateur ([champs en lecture seule](classes.md#readonly-fields)) ou un `volatile` modificateur ([des champs volatiles](classes.md#volatile-fields)) mais pas les deux. Les attributs et les modificateurs s’appliquent à tous les membres déclarés par le *field_declaration*. C’est une erreur pour le même modificateur apparaît plusieurs fois dans une déclaration de champ.

Le *type* d’un *field_declaration* Spécifie le type des membres introduits par la déclaration. Le type est suivi d’une liste de *variable_declarator*s, chacun d’eux présente un nouveau membre. Un *variable_declarator* se compose d’un *identificateur* qui nomme ce membre, éventuellement suivi d’une «`=`« jeton et un *variable_initializer* ([ Les initialiseurs de variable](classes.md#variable-initializers)) qui fournit la valeur initiale de ce membre.

Le *type* d’un champ doit être au moins aussi accessibles que le champ lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

La valeur d’un champ est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)) ou un *member_access* ([l’accès au membre](expressions.md#member-access)). La valeur d’un champ non readonly est modifiée à l’aide un *attribution* ([opérateurs d’assignation](expressions.md#assignment-operators)). La valeur d’un champ non readonly peut être obtenu et modifiée à l’aide d’incrémentation et opérateurs de décrémentation ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators)) et Incrément préfixé et opérateurs de décrémentation ([préfixe opérateurs d’incrémentation et décrémentation](expressions.md#prefix-increment-and-decrement-operators)).

Une déclaration de champ qui déclare plusieurs champs équivaut à plusieurs déclarations de champs uniques ayant les mêmes attributs, modificateurs et type. Exemple :
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

### <a name="static-and-instance-fields"></a>Champs statiques et d’instance

Lorsqu’une déclaration de champ inclut un `static` sont des champs introduites par la déclaration de modificateur, ***champs statiques***. En cas de non `static` modificateur est présent, les champs introduites par la déclaration sont ***champs d’instance***. Les champs statiques et les champs d’instance sont deux des types différents de variables ([Variables](variables.md)) pris en charge par c#, et dans certains cas elles sont appelées ***variables statiques*** et ***variables d’instances*** , respectivement.

Un champ statique ne fait pas partie d’une instance spécifique ; au lieu de cela, il est partagé entre toutes les instances d’un type fermé ([ouvert et fermé types](types.md#open-and-closed-types)). Quel que soit le nombre d’instances d’un type de classe fermées est créé, il n'existe qu’une seule copie d’un champ statique pour le domaine d’application associé.

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

Lorsqu’un champ est référencé dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`si `M` est un champ statique, `E` doit indiquer un type contenant `M` et si `M` est un champ d’instance, E doit indiquer une instance d’un type contenant `M`.

Les différences entre statiques et membres d’instance sont décrits plus en détail dans [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="readonly-fields"></a>Champs en lecture seule

Quand un *field_declaration* inclut un `readonly` sont des champs introduites par la déclaration de modificateur, ***champs en lecture seule***. Les assignations directes aux champs readonly peuvent se produire uniquement dans le cadre de cette déclaration ou dans un constructeur d’instance ou un constructeur statique dans la même classe. (Un champ readonly peut être assigné à plusieurs fois dans ces contextes.) Plus précisément, les assignations directes à un `readonly` champ sont autorisés uniquement dans les contextes suivants :

*  Dans le *variable_declarator* qui introduit le champ (en incluant un *variable_initializer* dans la déclaration).
*  Pour un champ d’instance, dans les constructeurs d’instance de la classe contenant la déclaration de champ ; pour un champ statique, dans le constructeur statique de la classe qui contient la déclaration de champ. Il s’agit également les seuls contextes dans lesquels il est valide de passer un `readonly` champ comme un `out` ou `ref` paramètre.

Tentative d’assigner à un `readonly` champ ou passer en tant qu’un `out` ou `ref` paramètre dans un autre contexte est une erreur de compilation.

#### <a name="using-static-readonly-fields-for-constants"></a>Utilisation des champs statiques en lecture seule pour des constantes

Un `static readonly` champ s’avère utile lorsqu’un nom symbolique d’une valeur constante est souhaité, mais lorsque le type de la valeur n’est pas autorisé dans un `const` déclaration, ou que la valeur ne peut pas être calculée au moment de la compilation. Dans l’exemple
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
le `Black`, `White`, `Red`, `Green`, et `Blue` membres ne peuvent pas être déclarés en tant que `const` membres, car leurs valeurs ne peut pas être calculées au moment de la compilation. Toutefois, en les déclarant `static readonly` elle dispose d’un effet similaire.

#### <a name="versioning-of-constants-and-static-readonly-fields"></a>Contrôle de version de constantes et de champs statiques en lecture seule

Constantes et des champs en lecture seule ont une sémantique de versioning binaire différente. Lorsqu’une expression fait référence à une constante, la valeur de la constante est obtenue au moment de la compilation, mais lorsqu’une expression fait référence à un champ en lecture seule, la valeur du champ n’est pas obtenue avant l’exécution. Considérez une application qui se compose de deux programmes distincts :
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

Le `Program1` et `Program2` espaces de noms représentent deux programmes compilés séparément. Étant donné que `Program1.Utils.X` est déclaré comme un champ readonly statique, la valeur générée par le `Console.WriteLine` instruction n’est pas connue au moment de la compilation, mais est obtenue au moment de l’exécution. Par conséquent, si la valeur de `X` est modifié et `Program1` est recompilé, les `Console.WriteLine` instruction génère la nouvelle valeur si `Program2` n’est pas recompilé. Cependant, dû `X` été une constante, la valeur de `X` aurait été obtenu au moment `Program2` a été compilé et resterait pas affectés par les modifications dans `Program1` jusqu'à ce que `Program2` est recompilé.

### <a name="volatile-fields"></a>Champs volatiles

Quand un *field_declaration* inclut un `volatile` sont des champs introduites par la déclaration de modificateur, ***des champs volatiles***.

Pour les champs non volatile, les techniques d’optimisation qui réorganisent les instructions peuvent produire des résultats inattendus et imprévisibles dans des programmes multithreads qui accèdent aux champs sans synchronisation telle que celle fournie par le *lock_statement*  ([L’instruction lock](statements.md#the-lock-statement)). Ces optimisations peuvent être effectuées en matériel par le compilateur ou par le système d’exécution. Pour des champs volatiles, ces optimisations de réorganisation sont limitées :

*  Lecture d’un champ volatile est appelée un ***lecture volatile***. Une lecture volatile a « sémantiques acquire » ; Autrement dit, il est assuré de se produire avant toutes les références à la mémoire qui se produisent après lui dans la séquence d’instructions.
*  Une écriture d’un champ volatile est appelée un ***écriture volatile***. Une écriture volatile a « release sémantique » ; Autrement dit, il est garanti se produise après toutes les références mémoire avant l’instruction d’écriture dans la séquence d’instructions.

Ces restrictions garantissent que tous les threads observent les écritures volatiles effectuées par un autre thread dans l’ordre dans lequel elles ont été effectuées. Une implémentation conforme n’est pas obligée de fournir un classement total unique des écritures volatiles, comme l’indiquent tous les threads d’exécution. Le type d’un champ volatil doit être une des opérations suivantes :

*  Un *reference_type*.
*  Le type `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, ou` System.UIntPtr`.
*  Un *type_de_liste* ayant un type de base enum de `byte`, `sbyte`, `short`, `ushort`, `int`, ou `uint`.

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
```
result = 143
```

Dans cet exemple, la méthode `Main` démarre un nouveau thread qui exécute la méthode `Thread2`. Cette méthode stocke une valeur dans un champ non volatile appelé `result`, puis le stocke `true` dans le champ volatil `finished`. Le thread principal attend que le champ `finished` à `true`, puis lit le champ `result`. Dans la mesure où `finished` a été déclaré `volatile`, le thread principal doit lire la valeur `143` à partir du champ `result`. Si le champ `finished` n’avait pas été déclaré `volatile`, puis il serait le stockage à `result` soit visible par le thread principal après le stockage dans `finished`et par conséquent, pour le thread principal de lire la valeur `0` à partir de la champ `result`. Déclaration `finished` comme un `volatile` champ empêche une telle incohérence.

### <a name="field-initialization"></a>Initialisation de champ

La valeur initiale d’un champ, qu’il s’agisse d’un champ statique ou un champ d’instance, est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) de type du champ. Il n’est pas possible d’observer la valeur d’un champ avant de l’initialisation par défaut s’est produite, et un champ n’est donc jamais « non initialisé ». L’exemple
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
```
b = False, i = 0
```
Étant donné que `b` et `i` sont tous deux automatiquement initialisés sur les valeurs par défaut.

### <a name="variable-initializers"></a>Initialiseurs de variable

Les déclarations de champ peuvent inclure *variable_initializer*s. Pour les champs statiques, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées pendant l’initialisation de classe. Par exemple des champs, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées lorsqu’une instance de la classe est créée.

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
```
x = 1.4142135623731, i = 100, s = Hello
```
car une assignation à `x` se produit lorsque les initialiseurs de champ statique exécutent et assignations à `i` et `s` se produire lors de l’exécution des initialiseurs de champ d’instance.

L’initialisation de valeur par défaut décrite dans [initialisation de champ](classes.md#field-initialization) se produit pour tous les champs, y compris les champs qui ont des initialiseurs de variable. Par conséquent, lorsqu’une classe est initialisée, tous les champs statiques de cette classe sont d’abord initialisés à leurs valeurs par défaut, et les initialiseurs de champ statique sont exécutées dans l’ordre textuel. De même, lorsqu’une instance d’une classe est créée, tous les champs d’instance dans cette instance sont d’abord initialisés à leurs valeurs par défaut, et les initialiseurs de champ d’instance sont exécutées dans l’ordre textuel.

Il est possible pour les champs statiques des initialiseurs de variable à observer dans leur état de la valeur par défaut. Toutefois, ceci est fortement déconseillé comme une question de style. L’exemple
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
a ce comportement. Malgré les définitions circulaires d’un et b, le programme est valide. Il en résulte dans la sortie
```
a = 1, b = 2
```
Étant donné que les champs statiques `a` et `b` sont initialisés à `0` (la valeur par défaut `int`) avant l’exécution de leurs initialiseurs. Lors de l’initialiseur pour `a` s’exécute, la valeur de `b` est égal à zéro et donc `a` est initialisée à `1`. Lors de l’initialiseur pour `b` s’exécute, la valeur de `a` est déjà `1`et donc `b` est initialisée à `2`.

#### <a name="static-field-initialization"></a>Initialisation de champ statique

Les initialiseurs de variable de champ statique d’une classe correspondent à une séquence d’assignations qui sont exécutées dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe. Si un constructeur statique ([constructeurs statiques](classes.md#static-constructors)) existe dans la classe, l’exécution des initialiseurs de champ statique se produit immédiatement avant l’exécution de ce constructeur statique. Sinon, les initialiseurs de champ statique sont exécutés à un moment qui dépend de l’implémentation avant la première utilisation d’un champ statique de cette classe. L’exemple
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
peut générer de sortie :
```
Init A
Init B
1 1
```
ou la sortie :
```
Init B
Init A
1 1
```
Étant donné que l’exécution de `X`d’initialiseur et `Y`d’initialiseur peut se produire dans les deux sens ; ils sont uniquement limités à se produire avant les références à ces champs. Toutefois, dans l’exemple :
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
```
Init B
Init A
1 1
```
Étant donné que les règles pour exécuter lorsque des constructeurs statiques (tel que défini dans [constructeurs statiques](classes.md#static-constructors)) qui fournissent `B`du constructeur statique (et par conséquent, `B`d’initialiseurs de champ statique) doit s’exécuter avant `A`du constructeur statique et les initialiseurs de champ.

#### <a name="instance-field-initialization"></a>Initialisation de champ d’instance

Les initialiseurs de variable de champ d’instance d’une classe correspondent à une séquence d’assignations qui sont exécutées immédiatement lors de l’entrée à l’un des constructeurs d’instance ([initialiseurs de constructeur](classes.md#constructor-initializers)) de cette classe. Les initialiseurs de variable sont exécutées dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe. Le processus de création et d’initialisation instance de classe est décrit plus loin dans [constructeurs d’Instance](classes.md#instance-constructors).

Un initialiseur de variable pour un champ d’instance ne peut pas référencer l’instance en cours de création. Par conséquent, c’est une erreur de compilation pour faire référence à `this` dans un initialiseur de variable, car il s’agit une erreur de compilation pour un initialiseur de variable faire référence à n’importe quel membre d’instance via un *simple_name*. Dans l’exemple
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
l’initialiseur de variable pour `y` provoque une erreur de compilation, car elle fait référence à un membre de l’instance en cours de création.

## <a name="methods"></a>Méthodes

Une ***méthode*** est un membre qui implémente un calcul ou une action qui peut être effectuée par un objet ou une classe. Les méthodes sont déclarées à l’aide de *method_declaration*%s :

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

Un *method_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès ](classes.md#access-modifiers)), la `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([méthodes statiques et d’instance](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), `override` ([Substituer les méthodes](classes.md#override-methods)), `sealed` ([Sealed méthodes](classes.md#sealed-methods)), `abstract` ([méthodes abstraites](classes.md#abstract-methods)), et `extern` ([Méthodes externes](classes.md#external-methods)) modificateurs.

Une déclaration a une combinaison valide de modificateurs si toutes les conditions suivantes sont remplies :

*  La déclaration inclut une combinaison valide de modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)).
*  La déclaration n’inclut pas le même modificateur plusieurs fois.
*  La déclaration contient au maximum un des modificateurs suivants : `static`, `virtual`, et `override`.
*  La déclaration contient au maximum un des modificateurs suivants : `new` et `override`.
*  Si la déclaration inclut le `abstract` modificateur, puis la déclaration n’inclut pas un des modificateurs suivants : `static`, `virtual`, `sealed` ou `extern`.
*  Si la déclaration inclut le `private` modificateur, puis la déclaration n’inclut pas un des modificateurs suivants : `virtual`, `override`, ou `abstract`.
*  Si la déclaration inclut le `sealed` modificateur, puis la déclaration inclut également le `override` modificateur.
*  Si la déclaration inclut le `partial` modificateur, alors il n’inclut pas un des modificateurs suivants : `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override` , `abstract`, ou `extern`.

Une méthode qui a le `async` modificateur est une fonction async et suit les règles décrites dans [fonctions Async](classes.md#async-functions).

Le *return_type* d’une méthode de déclaration spécifie le type de la valeur calculée et retournée par la méthode. Le *return_type* est `void` si la méthode ne retourne pas de valeur. Si la déclaration inclut le `partial` modificateur, le type de retour doit être `void`.

Le *member_name* Spécifie le nom de la méthode. À moins que la méthode est une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicite](interfaces.md#explicit-interface-member-implementations)), la *member_name* est simplement un *identificateur*. Pour une implémentation de membre d’interface explicite, le *member_name* se compose d’un *interface_type* suivie d’un «`.`» et un *identificateur*.

Le paramètre facultatif *type_parameter_list* spécifie les paramètres de type de la méthode ([paramètres de Type](classes.md#type-parameters)). Si un *type_parameter_list* est spécifié à la méthode est un ***méthode générique***. Si la méthode a un `extern` modificateur, une *type_parameter_list* ne peut pas être spécifié.

Le paramètre facultatif *formal_parameter_list* spécifie les paramètres de la méthode ([paramètres de méthode](classes.md#method-parameters)).

Le paramètre facultatif *type_parameter_constraints_clause*s spécifient des contraintes sur les paramètres de type individuel ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) et ne peut être spécifiée que si un *type_parameter_ liste* est également fourni, et la méthode n’est pas un `override` modificateur.

Le *return_type* et chacun des types référencés dans le *formal_parameter_list* d’une méthode doivent être au moins aussi accessibles que la méthode elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Le *method_body* est soit un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***. Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque la méthode est appelée. Un corps d’expression se compose de `=>` suivie d’une *expression* et un point-virgule et désigne une seule expression à exécuter lorsque la méthode est appelée. 

Pour `abstract` et `extern` méthodes, le *method_body* est simplement constitué d’un point-virgule. Pour `partial` méthodes le *method_body* peut se composer d’un point-virgule, un corps de bloc ou un corps d’expression. Pour toutes les autres méthodes, le *method_body* est un corps de bloc ou d’un corps d’expression.

Si le *method_body* se compose d’un point-virgule, puis la déclaration ne peut pas inclure le `async` modificateur.

Le nom, la liste de paramètres de type et la liste de paramètres formels d’une méthode définissent la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de la méthode. Plus précisément, la signature d’une méthode se compose de son nom, le nombre de paramètres de type et le nombre, les modificateurs et les types de ses paramètres formels. À ces fins, n’importe quel paramètre de type de la méthode qui se produit dans le type d’un paramètre formel est identifié pas par son nom, mais par sa position ordinale dans la liste d’arguments de type de la méthode. Le type de retour ne fait pas partie de la signature d’une méthode, ni est les noms des paramètres de type ou de paramètres formels.

Le nom d’une méthode doit différer des noms de tous les autres méthodes non déclarés dans la même classe. En outre, la signature d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir signatures qui diffèrent uniquement par `ref` et `out`.

La méthode *type_parameter*s sont dans la portée du *method_declaration*et peut être utilisé pour les types de formulaire dans l’ensemble de cette étendue dans *return_type*, *method_body*, et *type_parameter_constraints_clause*s, mais pas dans *attributs*.

Tous les paramètres formels et les paramètres de type doivent avoir des noms différents.

### <a name="method-parameters"></a>Paramètres de méthode

Les paramètres d’une méthode, le cas échéant, sont déclarées par la méthode *formal_parameter_list*.

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

La liste de paramètres formels se compose d’un ou plusieurs paramètres séparés par des virgules dont seule la dernière peut être un *parameter_array*.

Un *fixed_parameter* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), facultative `ref`, `out` ou `this` modificateur, une *type*, un *identificateur* et éventuellement un *default_argument*. Chaque *fixed_parameter* déclare un paramètre du type donné avec le nom donné. Le `this` désigne la méthode comme méthode d’extension de modificateur et n’est autorisé que sur le premier paramètre d’une méthode statique. Méthodes d’extension sont décrites en détail dans [méthodes d’Extension](classes.md#extension-methods).

A *fixed_parameter* avec un *default_argument* est appelé un ***paramètre facultatif***, tandis qu’un *fixed_parameter* sans un *default_argument* est un ***paramètre obligatoire***. Un paramètre obligatoire ne peut pas apparaître après un paramètre facultatif dans un *formal_parameter_list*.

Un `ref` ou `out` paramètre ne peut pas avoir un *default_argument*. Le *expression* dans un *default_argument* doit être une des opérations suivantes :

*  a *constant_expression*
*  une expression sous la forme `new S()` où `S` est un type valeur
*  une expression sous la forme `default(S)` où `S` est un type valeur

Le *expression* doit être implicitement convertible en une identité ou une conversion nullable vers le type du paramètre.

Si les paramètres facultatifs se produisent dans la déclaration de méthode partielle d’implémentation ([méthodes partielles](classes.md#partial-methods)), une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicite](interfaces.md#explicit-interface-member-implementations)) ou dans un déclaration d’indexeur de paramètre unique ([indexeurs](classes.md#indexers)) le compilateur doit générer un avertissement, étant donné que ces membres ne peuvent jamais être appelés d’une façon qui autorise des arguments à être omis.

Un *parameter_array* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), un `params` modificateur, une *array_type*, et un *identificateur*. Un tableau de paramètres déclare un paramètre unique du type tableau donné avec le nom donné. Le *array_type* d’un paramètre de tableau doit être un type de tableau unidimensionnel ([types tableau](arrays.md#array-types)). Dans un appel de méthode, un tableau de paramètres permet à un seul argument du type tableau donné à être spécifié ou il permet de zéro ou plusieurs arguments du type d’élément de tableau à être spécifié. Tableaux de paramètres sont décrits dans [tableaux de paramètres](classes.md#parameter-arrays).

Un *parameter_array* peut se produire après un paramètre facultatif, mais ne peut pas avoir une valeur par défaut, l’omission d’arguments pour un *parameter_array* au lieu de cela entraînerait la création d’un tableau vide.

L’exemple suivant illustre les différents types de paramètres :
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

Dans le *formal_parameter_list* pour `M`, `i` est un paramètre ref requis, `d` est un paramètre de valeur requise, `b`, `s`, `o` et `t` paramètres de la valeur facultative et `a` est un tableau de paramètres.

Une déclaration de méthode crée un espace de déclaration distincte pour les paramètres, les paramètres de type et les variables locales. Les noms sont introduits dans cet espace de déclaration par la liste de paramètres de type et la liste de paramètres formels de la méthode et par des déclarations de variables locales dans le *bloc* de la méthode. C’est une erreur par deux membres d’un espace de déclaration de méthode ont le même nom. C’est une erreur pour l’espace de déclaration de méthode et l’espace de déclaration de variable locale d’un espace de déclaration imbriquée pour contenir les éléments portant le même nom.

Un appel de méthode ([appels de méthode](expressions.md#method-invocations)) crée une copie, spécifique à cet appel, des paramètres formels et des variables locales de la méthode et la liste d’arguments de l’appel assigne des valeurs ou des références de variable à la qui vient d’être créé des paramètres formels. Dans le *bloc* d’une méthode, les paramètres formels peuvent être référencés par leurs identificateurs dans *simple_name* expressions ([noms simples](expressions.md#simple-names)).

Il existe quatre types de paramètres formels :

*  Paramètres de valeur, qui sont déclarés sans modificateur.
*  Paramètres de référence, qui sont déclarées avec le `ref` modificateur.
*  Paramètres de sortie, qui sont déclarées avec le `out` modificateur.
*  Les tableaux de paramètres, qui sont déclarées avec le `params` modificateur.

Comme décrit dans [Signatures et surcharge](basic-concepts.md#signatures-and-overloading), le `ref` et `out` modificateurs font partie de la signature d’une méthode, mais la `params` modificateur n’est pas.

#### <a name="value-parameters"></a>Paramètres de valeur

Un paramètre déclaré sans modificateur est un paramètre de valeur. Un paramètre de valeur correspond à une variable locale qui obtient sa valeur initiale de l’argument correspondant fourni dans l’appel de méthode.

Lorsqu’un paramètre formel est un paramètre de valeur, l’argument correspondant dans un appel de méthode doit être une expression qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) pour le type de paramètre formel.

Une méthode peut assigner de nouvelles valeurs à un paramètre de valeur. Ce type d’assignation affecte uniquement l’emplacement de stockage local représenté par le paramètre de valeur, elles n’ont aucun effet sur l’argument réel fourni dans l’appel de méthode.

#### <a name="reference-parameters"></a>Paramètres de référence

Un paramètre déclaré avec un `ref` modificateur est un paramètre de référence. Contrairement à un paramètre de valeur, un paramètre de référence ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de référence représente le même emplacement de stockage que la variable fournie comme argument dans l’appel de méthode.

Lorsqu’un paramètre formel est un paramètre de référence, l’argument correspondant dans un appel de méthode doit être du mot clé `ref` suivie d’un *variable_reference* ([règles précises de détermination assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que le paramètre formel. Une variable doit être assignée de manière définitive avant de pouvoir être passée comme paramètre de référence.

Dans une méthode, un paramètre de référence est toujours considéré comme définitivement assigné.

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
```
i = 2, j = 1
```

Pour l’appel de `Swap` dans `Main`, `x` représente `i` et `y` représente `j`. Par conséquent, l’appel a pour effet de permuter les valeurs de `i` et `j`.

Dans une méthode qui accepte des paramètres de référence il est possible que plusieurs noms représenter le même emplacement de stockage. Dans l’exemple
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
l’appel de `F` dans `G` passe une référence à `s` pour les deux `a` et `b`. Par conséquent, dans cet appel, les noms `s`, `a`, et `b` tous faire référence au même emplacement de stockage et les assignations modifient toutes le champ d’instance `s`.

#### <a name="output-parameters"></a>Paramètres de sortie

Un paramètre déclaré avec un `out` modificateur est un paramètre de sortie. Comme pour un paramètre de référence, un paramètre de sortie ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de sortie représente le même emplacement de stockage que la variable fournie comme argument dans l’appel de méthode.

Lorsqu’un paramètre formel est un paramètre de sortie, l’argument correspondant dans un appel de méthode doit être du mot clé `out` suivie d’un *variable_reference* ([règles précises de détermination assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que le paramètre formel. Une variable de ne doive pas définitivement assignée avant elle peut être passée comme paramètre de sortie, mais après un appel où une variable a été passée comme paramètre de sortie, la variable est considéré comme définitivement assignée.

Dans une méthode, une variable locale comme variable, un paramètre de sortie est initialement considéré comme non attribués et doit absolument être assigné avant que sa valeur est utilisée.

Chaque paramètre de sortie d’une méthode doit être assignée de manière définitive avant le retour de la méthode.

Une méthode déclarée comme une méthode partielle ([méthodes partielles](classes.md#partial-methods)) ou d’un itérateur ([itérateurs](classes.md#iterators)) ne peut pas posséder des paramètres de sortie.

Paramètres de sortie sont généralement utilisés dans les méthodes qui produisent plusieurs valeurs de retour. Exemple :
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

L’exemple génère la sortie :
```
c:\Windows\System\
hello.txt
```

Notez que le `dir` et `name` variables peuvent ne pas avoir avant d’être passés à `SplitPath`, et qu’ils sont considérés comme définitivement assignées après l’appel.

#### <a name="parameter-arrays"></a>Tableaux de paramètres

Un paramètre déclaré avec un `params` modificateur est un tableau de paramètres. Si une liste de paramètres formels comprend un tableau de paramètres, il doit être le dernier paramètre dans la liste, et il doit être d’un type tableau unidimensionnel. Par exemple, les types `string[]` et `string[][]` peut être utilisé comme le type d’un tableau de paramètres, mais le type `string[,]` ne peut pas. Il n’est pas possible de combiner le `params` modificateur avec les modificateurs `ref` et `out`.

Un tableau de paramètres permet de transmettre les arguments doit être spécifié dans une des deux façons dans un appel de méthode :

*  L’argument fourni pour un tableau de paramètres peut être une expression unique qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) pour le type de tableau de paramètres. Dans ce cas, le tableau de paramètres opère précisément comme un paramètre de valeur.
*  Vous pouvez également l’appel peut spécifier zéro ou plusieurs arguments pour le tableau de paramètres, où chaque argument est une expression qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) pour le type d’élément de tableau de paramètres. Dans ce cas, l’appel crée une instance de type tableau de paramètres d’une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument donné et utilise l’instance de tableau qui vient d’être créé en tant que le texte réel argument.

À l’exception autorise un nombre variable d’arguments dans un appel, un tableau de paramètres est équivalent à un paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)) du même type.

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
```
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

La première invocation de `F` transmet simplement le tableau `a` comme un paramètre de valeur. Le deuxième appel de `F` crée automatiquement un élément de quatre `int[]` avec les valeurs de l’élément donné et les transmet cette instance comme un paramètre de valeur de tableau. De même, le troisième appel de `F` crée un élément de zéro `int[]` et passe cette instance comme un paramètre de valeur. Les deuxième et troisième appels sont précisément équivalentes à l’écriture :
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

Lorsque vous effectuez la résolution de surcharge, une méthode avec un tableau de paramètres s’applique dans sa forme normale ou dans sa forme étendue ([membre de fonction Applicable](expressions.md#applicable-function-member)). La forme étendue d’une méthode est disponible uniquement si la forme normale de la méthode n’est pas applicable et uniquement si une méthode applicable avec la même signature que la forme étendue n’est pas déjà déclarée dans le même type.

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
```
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

Dans l’exemple, deux des formes développées possibles de la méthode avec un tableau de paramètres sont déjà incluses dans la classe en tant que méthodes régulières. Ces formes développées ne sont donc pas considérés lors de l’exécution de la résolution de surcharge, et les appels de méthode de première et troisième ainsi sélectionnent les méthodes classiques. Lorsqu’une classe déclare une méthode avec un tableau de paramètres, il n’est pas rare d’inclure également certaines des formes développées en tant que méthodes régulières. En procédant ainsi, il est possible d’éviter l’allocation d’un tableau instance qui se produit lorsqu’une forme développée d’une méthode avec un tableau de paramètres est appelée.

Lorsque le type d’un tableau de paramètres est `object[]`, une ambiguïté potentielle apparaît entre la forme normale de la méthode et la forme développée pour un seul `object` paramètre. La raison de l’ambiguïté est qu’un `object[]` lui-même est implicitement convertible en type `object`. L’ambiguïté ne présente aucun problème, toutefois, dans la mesure où elle peut être résolue en insérant un cast si nécessaire.

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
```
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

Dans les premier et dernier appels de `F`, la forme normale de `F` est applicable, car il existe une conversion implicite du type d’argument au type de paramètre (les deux sont de type `object[]`). Par conséquent, la résolution de surcharge sélectionne la forme normale de `F`, et l’argument est passé comme un paramètre de valeur régulier. Dans les deuxième et troisième appels, la forme normale de `F` n’est pas applicable, car aucune conversion implicite n’existe entre le type d’argument au type de paramètre (type `object` ne peut pas être converti implicitement en type `object[]`). Toutefois, la forme développée de `F` étant applicable, il est sélectionné par la résolution de surcharge. Par conséquent, un seul élément `object[]` est créé par l’appel, et l’élément unique du tableau est initialisé avec la valeur d’argument donné (qui est lui-même une référence à un `object[]`).

### <a name="static-and-instance-methods"></a>Méthodes statiques et d’instance

Lorsqu’une déclaration de méthode inclut un `static` modificateur, que la méthode est appelée à être une méthode statique. En cas de non `static` modificateur est présent, la méthode est appelée à être une méthode d’instance.

Une méthode statique n’opère pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire référence à `this` dans une méthode statique.

Une méthode d’instance opère sur une instance donnée d’une classe, et cette instance est accessible en tant que `this` ([cet accès](expressions.md#this-access)).

Lorsqu’une méthode est référencée dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`si `M` est une méthode statique, `E` doit indiquer un type contenant `M`et si `M` est une méthode d’instance, `E` doit indiquer une instance d’un type contenant `M`.

Les différences entre statiques et membres d’instance sont décrits plus en détail dans [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="virtual-methods"></a>Méthodes virtuelles

Lorsqu’une déclaration de méthode d’instance inclut un `virtual` modificateur, que la méthode est appelée pour une méthode virtuelle. En cas de non `virtual` modificateur est présent, la méthode est appelée pour une méthode non virtuelle.

L’implémentation d’une méthode non virtuelle est invariable : L’implémentation est la même que la méthode est appelée sur une instance de la classe dans laquelle elle est déclarée ou une instance d’une classe dérivée. En revanche, l’implémentation d’une méthode virtuelle peut être remplacée par des classes dérivées. Le processus de l’implémentation d’une méthode virtuelle héritée est appelé ***substitution*** cette méthode ([substituer les méthodes](classes.md#override-methods)).

Dans un appel de méthode virtuelle, le ***type au moment de l’exécution*** de l’instance pour laquelle cet appel prend place détermine l’implémentation de méthode à appeler. Dans un appel de méthode non virtuelle, le ***type au moment de la compilation*** de l’instance est le facteur déterminant. Plus précisément, lorsqu’une méthode nommée `N` est appelé avec une liste d’arguments `A` sur une instance avec un type de compilation `C` et un type au moment de l’exécution `R` (où `R` est soit `C` ou une classe dérivée à partir de `C`), l’appel est traité comme suit :

*  Tout d’abord, la résolution de surcharge est appliquée à `C`, `N`, et `A`pour sélectionner une méthode spécifique `M` à partir de l’ensemble de méthodes déclarées dans et héritée par `C`. Cette opération est décrite dans [appels de méthode](expressions.md#method-invocations).
*  Ensuite, si `M` est une méthode non virtuelle, `M` est appelé.
*  Sinon, `M` est une méthode virtuelle et l’implémentation la plus dérivée de `M` par rapport à `R` est appelé.

Pour chaque méthode virtuelle déclarée dans ou héritées par une classe, il existe un ***implémentation la plus dérivée*** de la méthode par rapport à cette classe. L’implémentation la plus dérivée d’une méthode virtuelle `M` par rapport à une classe `R` est déterminée comme suit :

*  Si `R` contient la présentation `virtual` déclaration de `M`, il s’agit de l’implémentation la plus dérivée de `M`.
*  Sinon, si `R` contient un `override` de `M`, il s’agit de l’implémentation la plus dérivée de `M`.
*  Sinon, le meilleur implémentation dérivée de `M` par rapport au `R` est identique à l’implémentation la plus dérivée de `M` en ce qui concerne la classe de base directe de `R`.

L’exemple suivant illustre les différences entre les méthodes non virtuelles et :
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

Dans l’exemple, `A` présente une méthode non virtuelle `F` et une méthode virtuelle `G`. La classe `B` introduit une nouvelle méthode non virtuelle `F`, masquant ainsi la hérité `F`et substitue la méthode héritée `G`. L’exemple génère la sortie :
```
A.F
B.F
B.G
B.G
```

Notez que l’instruction `a.G()` appelle `B.G`, et non `A.G`. Il s’agit, car le type de la durée d’exécution de l’instance (qui est `B`), pas le type au moment de la compilation de l’instance (c'est-à-dire `A`), détermine l’implémentation de méthode à appeler.

Étant donné que les méthodes sont autorisées pour masquer les méthodes héritées, il est possible pour une classe permettant de contenir plusieurs méthodes virtuelles avec la même signature. Cela ne présente pas de problème d’ambiguïté, étant donné que tout sauf la méthode la plus dérivée sont masqués. Dans l’exemple
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
le `C` et `D` classes contiennent deux méthodes virtuelles avec la même signature : Celle introduite par `A` et l’autre par `C`. La méthode introduite par `C` masque la méthode héritée `A`. Par conséquent, la déclaration de remplacement dans `D` substitue la méthode introduite par `C`, et il n’est pas possible pour `D` pour substituer la méthode introduite par `A`. L’exemple génère la sortie :
```
B.F
B.F
D.F
D.F
```

Notez qu’il est possible d’appeler la méthode virtuelle masquée en accédant à une instance de `D` via une moins dérivés type dans lequel la méthode n’est pas masquée.

### <a name="override-methods"></a>Substituer des méthodes

Lorsqu’une déclaration de méthode d’instance inclut un `override` modificateur, la méthode est dite un ***remplacer la méthode***. Une méthode override substitue à une méthode virtuelle héritée ayant la même signature. Là où une déclaration de méthode virtuelle présente une nouvelle méthode, une déclaration de méthode de substitution spécialise une méthode virtuelle héritée existante en fournissant une nouvelle implémentation de cette méthode.

La méthode substituée par une `override` déclaration est appelée le ***substitution de méthode de base***. Pour une méthode override `M` déclaré dans une classe `C`, la méthode de base substituée est déterminée en examinant chaque type de classe de base de `C`, en commençant par le type de classe de base directe de `C` et en continuant avec chaque successives type de classe de base directe, jusqu'à ce que dans un type de classe de base de donnée au moins une méthode accessible est situé qui a la même signature que `M` après la substitution des arguments de type. Dans le cadre de la localisation de la méthode de base substituée, une méthode est considérée comme accessible s’il s’agit `public`, s’il s’agit `protected`, s’il s’agit `protected internal`, ou si elle est `internal` et déclaré dans le même programme que `C`.

Une erreur de compilation se produit, sauf si toutes les conditions suivantes sont remplies pour une déclaration de remplacement :

*  Une méthode de base substituée peut se trouve comme décrit ci-dessus.
*  Il existe exactement une telle méthode de base substituée. Cette restriction a effet uniquement si le type de classe de base est un type construit où la substitution des arguments de type rend la signature des deux méthodes de la même.
*  La méthode de base substituée est un virtual, abstract ou substituer la méthode. En d’autres termes, la méthode de base substituée ne peut pas être statique ou non virtuelle.
*  La méthode de base substituée n’est pas une méthode sealed.
*  La méthode de substitution et la méthode de base substituée ont le même type de retour.
*  La déclaration de remplacement et de la méthode de base substituée ont la même accessibilité déclarée. En d’autres termes, une déclaration override ne peut pas modifier l’accessibilité de la méthode virtuelle. Toutefois, si la méthode de base substituée est protégée interne et il est déclaré dans un autre assembly que l’assembly qui contient la méthode de substitution ensuite la méthode de substitution déclaré accessibilité doit être protégée.
*  La déclaration de substitution ne spécifie pas de type de paramètre contraintes clauses. Au lieu de cela, les contraintes sont héritées de la méthode de base substituée. Notez que les contraintes des paramètres de type dans la méthode substituée peuvent être remplacées par des arguments de type dans la contrainte héritée. Cela peut entraîner des contraintes qui ne sont pas autorisées lors de la spécification explicite, tels que des types valeur ou les types sealed.

L’exemple suivant montre comment les règles de remplacement fonctionnent pour les classes génériques :
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

Une déclaration de remplacement peut accéder à la méthode de base substituée à l’aide un *base_access* ([accès de Base](expressions.md#base-access)). Dans l’exemple
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
le `base.PrintFields()` invocation dans `B` appelle le `PrintFields` méthode déclarée dans `A`. Un *base_access* désactive le mécanisme d’appel virtuel et traite simplement la méthode de base comme une méthode non virtuelle. A connu de l’appel de `B` été écrits `((A)this).PrintFields()`, s’il s’agissait de manière récursive appeler le `PrintFields` méthode déclarée dans `B`, pas celui déclaré dans `A`, étant donné que `PrintFields` est virtuel et le type au moment de l’exécution de `((A)this)` est `B`.

Uniquement en incluant un `override` peut modifier une méthode remplacent une autre méthode. Dans tous les autres cas, une méthode avec la même signature qu’une méthode héritée masque simplement la méthode héritée. Dans l’exemple
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
le `F` méthode dans `B` n’inclut pas un `override` modificateur et par conséquent ne remplace pas le `F` méthode dans `A`. Au lieu de cela, le `F` méthode dans `B` masque la méthode de `A`, et un avertissement est signalé, car la déclaration n’inclut pas un `new` modificateur.

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
le `F` méthode dans `B` masque virtuel `F` héritée de la méthode `A`. Depuis la nouvelle `F` dans `B` possède un accès privé, sa portée inclut uniquement le corps de la classe de `B` et ne s’étend pas à `C`. Par conséquent, la déclaration de `F` dans `C` est autorisée à se substituer le `F` héritée `A`.

### <a name="sealed-methods"></a>Méthodes sealed

Lorsqu’une déclaration de méthode d’instance inclut un `sealed` modificateur, que la méthode est appelée à être un ***sealed méthode***. Si une déclaration de méthode d’instance inclut le `sealed` modificateur, il doit également inclure le `override` modificateur. Utilisation de la `sealed` modificateur empêche une classe dérivée de substitution ultérieure de la méthode.

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
la classe `B` fournit deux méthodes override : une `F` méthode qui a le `sealed` modificateur et un `G` méthode pas. `B`l’utilisation de la sealed `modifier` empêche `C` à partir de la substitution ultérieure `F`.

### <a name="abstract-methods"></a>Méthodes abstraites

Lorsqu’une déclaration de méthode d’instance inclut un `abstract` modificateur, que la méthode est appelée à être un ***méthode abstraite***. Bien qu’une méthode abstraite est implicitement également une méthode virtuelle, il ne peut pas avoir le modificateur `virtual`.

Une déclaration de méthode abstraite introduit une nouvelle méthode virtuelle mais ne fournit pas une implémentation de cette méthode. Au lieu de cela, les classes non abstraites dérivées doivent fournir leur propre implémentation en substituant cette méthode. Car une méthode abstraite ne fournit aucune implémentation réelle, le *method_body* d’une méthode abstraite se compose simplement d’un point-virgule.

Déclarations de méthodes abstraites sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).

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
le `Shape` classe définit la notion abstraite d’un objet de forme géométrique qui peut se peindre lui-même. Le `Paint` méthode est abstraite, car il n’existe aucune implémentation par défaut significatives. Le `Ellipse` et `Box` classes sont concrètes `Shape` implémentations. Étant donné que ces classes sont abstraites, ils sont requis pour substituer le `Paint` méthode et fournir une implémentation réelle.

Il s’agit d’une erreur lors de la compilation pour un *base_access* ([accès de Base](expressions.md#base-access)) pour référencer une méthode abstraite. Dans l’exemple
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
une erreur de compilation est signalée pour le `base.F()` invocation, car elle référence une méthode abstraite.

Une déclaration de méthode abstraite est autorisée à se substituer une méthode virtuelle. Cela permet à une classe abstraite forcer la réimplémentation de la méthode dans les classes dérivées et ne permet pas l’implémentation de la méthode d’origine. Dans l’exemple
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
classe `A` déclare une méthode virtuelle, la classe `B` se substitue à cette méthode avec une méthode abstraite et une classe `C` substitue la méthode abstraite afin de fournir sa propre implémentation.

### <a name="external-methods"></a>Méthodes externes

Lorsqu’une déclaration de méthode inclut un `extern` modificateur, que la méthode est appelée à être un ***méthode externe***. Méthodes externes sont implémentées en externe, généralement à l’aide d’une langue autre que c#. Comme une déclaration de méthode externe ne fournit aucune implémentation réelle, le *method_body* d’une méthode externe se compose simplement d’un point-virgule. Une méthode externe ne peut-être pas être générique.

Le `extern` modificateur est généralement utilisé conjointement avec un `DllImport` attribut ([l’interopérabilité avec les composants COM et Win32](attributes.md#interoperation-with-com-and-win32-components)), autorisant des méthodes à implémenter par la DLL (Dynamic Link Libraries) externes. L’environnement d’exécution peut prendre en charge d’autres mécanismes par laquelle les implémentations de méthodes externes peuvent être fournies.

Lorsqu’une méthode externe inclut un `DllImport` attribut, la déclaration de méthode doit également inclure un `static` modificateur. Cet exemple illustre l’utilisation de la `extern` modificateur et `DllImport` attribut :
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

### <a name="partial-methods-recap"></a>Méthodes partielles (Récapitulatif)

Lorsqu’une déclaration de méthode inclut un `partial` modificateur, que la méthode est appelée à être un ***méthode partielle***. Les méthodes partielles peuvent uniquement être déclarés en tant que membres de types partiels ([types partiels](classes.md#partial-types)) et sont soumis à un nombre de restrictions. Les méthodes partielles sont décrites en détail dans [méthodes partielles](classes.md#partial-methods).

### <a name="extension-methods"></a>Méthodes d’extension

Lorsque le premier paramètre d’une méthode inclut la `this` modificateur, que la méthode est appelée à être un ***méthode d’extension***. Méthodes d’extension peuvent uniquement être déclarés dans les classes static non générique, non imbriqués. Le premier paramètre d’une méthode d’extension ne peut avoir aucun modificateur autre que `this`, et le type de paramètre ne peut pas être un type pointeur.

Voici un exemple d’une classe statique qui déclare deux méthodes d’extension :
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

Une méthode d’extension est une méthode statique normale. En outre, lorsque sa classe englobante statique est dans la portée, une méthode d’extension peut être appelée à l’aide de la syntaxe d’appel de méthode instance ([appels de méthode d’Extension](expressions.md#extension-method-invocations)), à l’aide de l’expression de récepteur comme premier argument.

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

Le `Slice` méthode est disponible sur le `string[]`et le `ToInt32` méthode est disponible sur `string`, car ils ont été déclarés comme méthodes d’extension. La signification du programme est le même que les appels de méthode statique ordinaire suivantes, à l’aide :
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

### <a name="method-body"></a>Corps de méthode

Le *method_body* d’une méthode de déclaration se compose d’un corps de bloc, d’un corps d’expression ou d’un point-virgule.

Le ***résultat de type*** d’une méthode est `void` si le type de retour est `void`, ou si la méthode est asynchrone et le type de retour est `System.Threading.Tasks.Task`. Sinon, le type de résultat d’une méthode non asynchrone est son type de retour et le type de résultat d’une méthode async avec type de retour `System.Threading.Tasks.Task<T>` est `T`.

Quand une méthode a un `void` de résultats type et un corps de bloc, `return` instructions ([l’instruction return](statements.md#the-return-statement)) dans le bloc ne sont pas autorisées à spécifier une expression. Si l’exécution du bloc d’une méthode void s’effectue normalement (autrement dit, le flux de contrôle après la fin du corps de méthode), cette méthode retourne simplement à son appelant actuel.
    
Quand une méthode a un `void` résultat et un corps d’expression, l’expression `E` doit être un *statement_expression*, et le corps équivaut exactement à un corps de bloc du formulaire `{ E; }`.
    
Lorsqu’une méthode a un type de résultat de non void et un bloc de corps, chacun `return` instruction dans le bloc doit spécifier une expression qui est implicitement convertible en type de résultat. Le point de terminaison d’un corps de bloc d’une méthode retournant une valeur ne doit pas être accessible. En d’autres termes, dans une méthode retournant une valeur avec un corps de bloc, contrôle n’est pas autorisé à déborder à la fin du corps de méthode.
    
Quand une méthode a un type de résultat de non void et un corps d’expression, l’expression doit être implicitement convertible au type de résultat, et le corps équivaut exactement à un corps de bloc du formulaire `{ return E; }`.
    
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
la valeur de retour `F` méthode génère une erreur de compilation, car le contrôle peut s’écouler la fin du corps de méthode. Le `G` et `H` méthodes sont corrects, car tous les chemins d’exécution possibles se terminent par une instruction return qui spécifie une valeur de retour. Le `I` méthode est correcte, car son corps est équivalent à un bloc d’instructions avec seulement une seule instruction return qu’il contient.

### <a name="method-overloading"></a>Surcharge de méthode

Les règles de résolution de surcharge de méthode sont décrites dans [l’inférence de Type](expressions.md#type-inference).

## <a name="properties"></a>Properties

Un ***propriété*** est un membre qui fournit l’accès à une caractéristique d’un objet ou une classe. Exemples de propriétés incluent la longueur d’une chaîne, la taille d’une police, la légende d’une fenêtre, le nom d’un client et ainsi de suite. Les propriétés sont une extension naturelle des champs, les deux sont membres nommés avec des types associés, et la syntaxe permettant d’accéder aux champs et propriétés est le même. Toutefois, contrairement aux champs, les propriétés ne désignent pas des emplacements de stockage. Au lieu de cela, les propriétés ont des ***accesseurs*** qui spécifient les instructions à exécuter lorsque les valeurs sont lues ou écrites. Les propriétés fournissent ainsi un mécanisme permettant d’associer des actions à la lecture et l’écriture des attributs d’un objet ; en outre, elles autorisent ces attributs à calculer.

Les propriétés sont déclarées à l’aide de *property_declaration*%s :

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

Un *property_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès ](classes.md#access-modifiers)), la `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([méthodes statiques et d’instance](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), `override` ([Substituer les méthodes](classes.md#override-methods)), `sealed` ([Sealed méthodes](classes.md#sealed-methods)), `abstract` ([méthodes abstraites](classes.md#abstract-methods)), et `extern` ([Méthodes externes](classes.md#external-methods)) modificateurs.

Déclarations de propriété sont soumis aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons de modificateurs valides.

Le *type* d’une propriété déclaration spécifie le type de la propriété introduite par la déclaration et le *member_name* Spécifie le nom de la propriété. À moins que la propriété est une implémentation de membre d’interface explicite, le *member_name* est simplement un *identificateur*. Pour obtenir une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicite](interfaces.md#explicit-interface-member-implementations)), la *member_name* se compose d’un *interface_type* suivie d’un » `.`» et un *identificateur*.

Le *type* d’une propriété doit être au moins aussi accessibles que la propriété proprement dite ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Un *property_body* peut soit être constituée d’un ***corps de l’accesseur*** ou un ***corps d’expression***. Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre «`{`« et »`}`« jetons, déclarez des accesseurs ([accesseurs](classes.md#accessors)) de la propriété. Les accesseurs spécifient les instructions exécutables associées à la lecture et l’écriture de la propriété.

Un corps d’expression consistant en `=>` suivie d’une *expression* `E` et un point-virgule est exactement équivalent au corps d’instruction `{ get { return E; } }`et par conséquent utilisable uniquement pour spécifier la méthode getter uniquement propriétés où le résultat de l’accesseur Get est fourni par une expression unique.

Un *property_initializer* peut uniquement être donné pour une propriété implémentée automatiquement ([implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties)) et provoque l’initialisation du champ sous-jacent de telles propriétés de la valeur indiquée par le *expression*.

Bien que la syntaxe pour l’accès à une propriété est identique à celui d’un champ, une propriété n’est pas classée en tant que variable. Par conséquent, il n’est pas possible de passer une propriété en tant qu’un `ref` ou `out` argument.

Lorsqu’une déclaration de propriété inclut un `extern` modificateur, la propriété est dite un ***propriété externe***. Car une déclaration de propriété externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.

### <a name="static-and-instance-properties"></a>Propriétés statiques et d’instance

Lorsqu’une déclaration de propriété inclut un `static` modificateur, la propriété est dite un ***propriété statique***. En cas de non `static` modificateur est présent, la propriété est dite un ***propriété d’instance***.

Une propriété statique n’est pas associée à une instance spécifique, et il s’agit d’une erreur de compilation pour faire référence à `this` dans les accesseurs d’une propriété statique.

Une propriété d’instance est associée à une instance donnée d’une classe, et cette instance est accessible en tant que `this` ([cet accès](expressions.md#this-access)) dans les accesseurs de cette propriété.

Lorsqu’une propriété est référencée dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`si `M` est une propriété statique, `E` doit indiquer un type contenant `M`et si `M` est une propriété d’instance, E doit indiquer une instance d’un type contenant `M`.

Les différences entre statiques et membres d’instance sont décrits plus en détail dans [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="accessors"></a>Accesseurs

Le *accessor_declarations* d’une propriété spécifient les instructions exécutables associées à la lecture et l’écriture de cette propriété.

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

Les déclarations d’accesseur se composent d’un *get_accessor_declaration*, un *set_accessor_declaration*, ou les deux. Chaque déclaration d’accesseur se compose du jeton `get` ou `set` suivie éventuellement d’un *accessor_modifier* et un *accessor_body*.

L’utilisation de *accessor_modifier*s est régie par les restrictions suivantes :

*  Un *accessor_modifier* ne peut pas être utilisé dans une interface ou dans une implémentation de membre d’interface explicite.
*  Pour une propriété ou un indexeur qui n’a aucun `override` modificateur, une *accessor_modifier* est autorisée uniquement si la propriété ou l’indexeur dispose à la fois un `get` et `set` accesseur et ensuite est autorisé uniquement sur l’un de ces accesseurs.
*  Pour une propriété ou un indexeur qui inclut un `override` modificateur, un accesseur doit correspondre à la *accessor_modifier*, le cas échéant, de l’accesseur substitué.
*  Le *accessor_modifier* doit déclarer une accessibilité est strictement plus restrictive que l’accessibilité déclarée de la propriété ou l’indexeur lui-même. Pour être précis :
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `public`, le *accessor_modifier* peut être `protected internal`, `internal`, `protected`, ou `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `protected internal`, le *accessor_modifier* peut être `internal`, `protected`, ou `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `internal` ou `protected`, le *accessor_modifier* doit être `private`.
   * Si la propriété ou l’indexeur a une accessibilité déclarée de `private`, aucun *accessor_modifier* peut être utilisé.

Pour `abstract` et `extern` propriétés, le *accessor_body* pour chaque accesseur spécifié est simplement un point-virgule. Une propriété non abstraite, non externe peut avoir chacune *accessor_body* être un point-virgule, auquel cas il est un ***propriété automatiquement implémentée*** ([implémenté automatiquement les propriétés ](classes.md#automatically-implemented-properties)). Une propriété implémentée automatiquement doit avoir au moins un accesseur get. Pour les accesseurs de toute autre propriété non abstraite, non externe, le *accessor_body* est un *bloc* qui spécifie les instructions à exécuter lorsque l’accesseur correspondant est appelé.

Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété. À l’exception comme cible d’une assignation, lorsqu’une propriété est référencée dans une expression, le `get` accesseur de la propriété est appelé pour calculer la valeur de la propriété ([valeurs des expressions](expressions.md#values-of-expressions)). Le corps d’un `get` accesseur doit être conforme aux règles de retourner de valeur méthodes décrites dans [corps de la méthode](classes.md#method-body). En particulier, tous les `return` instructions dans le corps d’un `get` accesseur doit spécifier une expression qui est implicitement convertible au type de propriété. En outre, le point de terminaison d’un `get` accesseur ne doit pas être accessible.

Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété et un `void` type de retour. Le paramètre implicite d’un `set` accesseur est toujours nommé `value`. Lorsqu’une propriété est référencée en tant que la cible d’une assignation ([opérateurs d’assignation](expressions.md#assignment-operators)), ou comme opérande de `++` ou `--` ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators), [ Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), la `set` accesseur est appelé avec un argument (dont la valeur est celle de la partie droite de l’assignation ou de l’opérande de le `++` ou `--` opérateur) qui Fournit la nouvelle valeur ([assignation Simple](expressions.md#simple-assignment)). Le corps d’un `set` accesseur doit être conforme aux règles `void` méthodes décrites dans [corps de la méthode](classes.md#method-body). En particulier, `return` instructions dans le `set` corps d’accesseur ne sont pas autorisées à spécifier une expression. Dans la mesure où un `set` accesseur possède implicitement un paramètre nommé `value`, c’est une erreur de compilation pour une déclaration de variable ou constante locale dans un `set` accesseur a ce nom.

En fonction de la présence ou l’absence de la `get` et `set` accesseurs, une propriété est classé comme suit :

*  Une propriété qui comprend à la fois un `get` accesseur et un `set` accesseur est dite un ***en lecture-écriture*** propriété.
*  Une propriété qui possède uniquement un `get` accesseur est dite un ***en lecture seule*** propriété. Il s’agit d’une erreur lors de la compilation pour une propriété en lecture seule à être la cible d’une assignation.
*  Une propriété qui possède uniquement un `set` accesseur est dite un ***en écriture seule*** propriété. À l’exception comme cible d’une assignation, il est une erreur de compilation pour référencer une propriété en écriture seule dans une expression.

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
le `Button` contrôle déclare un public `Caption` propriété. Le `get` l’accesseur de la `Caption` propriété retourne la chaîne stockée dans le privé `caption` champ. Le `set` accesseur vérifie si la nouvelle valeur est différente de la valeur actuelle et, dans ce cas, il stocke la nouvelle valeur et redessine le contrôle. Propriétés suivent souvent le modèle ci-dessus : Le `get` accesseur retourne simplement une valeur stockée dans un champ privé et le `set` accesseur modifie ce champ privé, puis effectue les actions supplémentaires requises pour mettre entièrement à jour l’état de l’objet.

Étant donné la `Button` classe ci-dessus, Voici un exemple d’utilisation de la `Caption` propriété :
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

Ici, le `set` accesseur est appelé en affectant une valeur à la propriété et le `get` accesseur est appelé en référençant la propriété dans une expression.

Le `get` et `set` accesseurs d’une propriété ne sont pas membres distincts, et il n’est pas possible de déclarer les accesseurs d’une propriété séparément. Par conséquent, il n’est pas possible pour les deux accesseurs d’une propriété en lecture-écriture avoir une accessibilité différente. L’exemple
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
ne déclare pas une seule propriété en lecture-écriture. Au lieu de cela, il déclare deux propriétés portant le même nom, une en lecture seule et l’autre en écriture seule. Dans la mesure où les deux membres déclarés dans la même classe ne peut pas avoir le même nom, l’exemple provoque une erreur de compilation se produit.

Lorsqu’une classe dérivée déclare une propriété portant le même nom qu’une propriété héritée, la propriété dérivée masque la propriété héritée en ce qui concerne la lecture et l’écriture. Dans l’exemple
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
le `P` propriété dans `B` masque la `P` propriété dans `A` en ce qui concerne la lecture et l’écriture. Par conséquent, dans les instructions
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
l’assignation à `b.P` provoque une erreur de compilation, depuis en lecture seule `P` propriété dans `B` masque écriture seule `P` propriété dans `A`. Notez, cependant, qu’un cast peut être utilisé pour accéder le masqué `P` propriété.

Contrairement aux champs publics, les propriétés fournissent une séparation entre l’état interne d’un objet et son interface publique. Prenons l’exemple :
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

Ici, le `Label` classe utilise deux `int` champs, `x` et `y`, pour stocker son emplacement. L’emplacement est publiquement exposés à la fois comme un `X` et un `Y` propriété et comme un `Location` propriété de type `Point`. If, dans une future version de `Label`, il devient plus pratique de stocker l’emplacement en tant qu’un `Point` en interne, la modification peut être apportée sans affecter l’interface publique de la classe :
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

Avait `x` et `y` au lieu de cela été `public readonly` champs, il aurait été impossible d’effectuer une telle modification à la `Label` classe.

Exposant état via les propriétés n’est pas nécessairement moins efficace que d’exposer directement les champs. En particulier, lorsqu’une propriété n’est pas virtuelle et contient uniquement une petite quantité de code, l’environnement d’exécution peut remplacer les appels aux accesseurs par le code réel des accesseurs. Ce processus est appelé ***incorporation (inlining)***, et il rend l’accès aux propriétés aussi efficace que l’accès à un champ, tout en préservant la souplesse accrue des propriétés.

Depuis l’appel d’un `get` accesseur est conceptuellement équivalent à la lecture de la valeur d’un champ, il est considéré comme style de programmation de mauvaise qualité `get` accesseurs pour avoir des effets secondaires observables. Dans l’exemple
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
La valeur de la `Next` propriété varie selon le nombre de fois où la propriété a déjà été accédée. Par conséquent, l’accès à la propriété produit un effet secondaire observable, et la propriété doit être implémentée comme une méthode à la place.

La convention « sans effets secondaires » pour `get` accesseurs ne signifie pas que `get` accesseurs doivent toujours être écrits pour retourner simplement des valeurs stockées dans les champs. En effet, `get` accesseurs calculent souvent la valeur d’une propriété en accédant à plusieurs champs ou en appelant des méthodes. Toutefois, bien conçu `get` accesseur n’effectue aucune action qui entraîne des modifications visibles dans l’état de l’objet.

Propriétés peuvent être utilisées pour différer l’initialisation d’une ressource jusqu’au moment où qu'il est tout d’abord référencé. Exemple :
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

Le `Console` classe contient trois propriétés, `In`, `Out`, et `Error`, qui représentent l’entrée standard, de sortie et erreur des appareils, respectivement. En exposant ces membres en tant que propriétés, la `Console` classe peut retarder leur initialisation jusqu'à ce qu’elles soient utilisées. Par exemple, dès la première référence à la `Out` propriété, comme dans
```csharp
Console.Out.WriteLine("hello, world");
```
sous-jacent `TextWriter` pour le périphérique de sortie est créé. Toutefois, si l’application ne fait pas référence à la `In` et `Error` propriété, alors qu’aucun objet est créés pour ces appareils.

### <a name="automatically-implemented-properties"></a>Propriétés implémentées automatiquement

Une propriété implémentée automatiquement (ou ***auto-propriété*** pour faire plus court), est une propriété de non extern non abstraite avec corps d’accesseur point-virgule uniquement. Les auto-properties doivent avoir un accesseur get et peuvent éventuellement avoir un accesseur set.

Lorsqu’une propriété est spécifiée comme une propriété implémentée automatiquement, un champ de stockage masqué est automatiquement disponible pour la propriété et les accesseurs sont implémentées pour lire et écrire dans ce champ de stockage. Si la propriété automatique n’a aucun accesseur set, le champ de stockage est considéré comme `readonly` ([champs en lecture seule](classes.md#readonly-fields)). Tout comme un `readonly` champ, une méthode getter seule automatique-propriété peut également être affectée à dans le corps d’un constructeur de la classe englobante. Ce type d’assignation affecte directement au champ de stockage en lecture seule de la propriété.

Une auto-property peut éventuellement inclure un *property_initializer*, qui est appliqué directement au champ de stockage comme un *variable_initializer* ([initialiseurs de Variable](classes.md#variable-initializers)) .

L’exemple suivant :
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
est équivalente à la déclaration suivante :
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
est équivalente à la déclaration suivante :
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

Notez que les attributions au champ en lecture seule sont autorisées, car elles se produisent dans le constructeur.


### <a name="accessibility"></a>Accessibilité

Si un accesseur a un *accessor_modifier*, le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) de l’accesseur est déterminé à l’aide de l’accessibilité déclarée de la *accessor_modifier* . Si un accesseur n’a pas un *accessor_modifier*, le domaine d’accessibilité de l’accesseur est déterminé à partir de l’accessibilité déclarée de la propriété ou l’indexeur.

La présence d’un *accessor_modifier* n’affecte jamais de recherche de membres ([opérateurs](expressions.md#operators)) ou la résolution de surcharge ([la résolution de surcharge](expressions.md#overload-resolution)). Les modificateurs sur la propriété ou l’indexeur toujours déterminent quelle propriété ou l’indexeur est lié, quel que soit le contexte de l’accès.

Une fois qu’une propriété particulière ou un indexeur a été sélectionné, les domaines d’accessibilité des accesseurs spécifiques impliqués sont utilisés pour déterminer si cette utilisation est valide :

*  Si l’utilisation en tant que valeur ([valeurs des expressions](expressions.md#values-of-expressions)), le `get` accesseur doit exister et être accessible.
*  Si l’utilisation est la cible d’une assignation simple ([assignation Simple](expressions.md#simple-assignment)), le `set` accesseur doit exister et être accessible.
*  Si l’utilisation est la cible d’assignation composée ([assignation composée](expressions.md#compound-assignment)), ou comme cible de la `++` ou `--` opérateurs ([membres de fonction](expressions.md#function-members).9, [ Expressions d’appel](expressions.md#invocation-expressions)), à la fois le `get` accesseurs et le `set` accesseur doit exister et être accessible.

Dans l’exemple suivant, la propriété `A.Text` est masquée par la propriété `B.Text`, même dans les contextes où seul le `set` accesseur est appelé. En revanche, la propriété `B.Count` n’est pas accessible à la classe `M`, de sorte que la propriété accessible `A.Count` est utilisé à la place.

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

Un accesseur qui est utilisé pour implémenter une interface ne peut pas avoir un *accessor_modifier*. Si un accesseur est utilisé uniquement pour implémenter une interface, l’autre accesseur peut être déclaré avec un *accessor_modifier*:
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a>Virtual, sealed, override et accesseurs de propriété abstraite

Un `virtual` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels. Le `virtual` modificateur s’applique aux deux accesseurs d’une propriété en lecture-écriture, il n’est pas possible pour qu’un seul accesseur d’une propriété en lecture-écriture d’être virtuel.

Un `abstract` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels, mais ne fournit pas une implémentation réelle des accesseurs. Au lieu de cela, les classes non abstraites dérivées doivent fournir leur propre implémentation pour les accesseurs en substituant la propriété. Étant donné qu’un accesseur d’une déclaration de propriété abstraite ne fournit aucune implémentation réelle, son *accessor_body* se compose simplement d’un point-virgule.

Une déclaration de propriété qui comprend à la fois le `abstract` et `override` modificateurs Spécifie que la propriété est abstraite et substitue une propriété de base. Les accesseurs d’une telle propriété sont également abstract.

Les déclarations de propriété abstraite sont uniquement autorisées dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)). Les accesseurs d’une propriété virtuelle héritée peuvent être substituées dans une classe dérivée en incluant une déclaration de propriété qui spécifie un `override` directive. Il s’agit une ***substitution de déclaration de propriété***. Une déclaration de propriété de substitution ne déclare pas une nouvelle propriété. Au lieu de cela, il ne fait que spécialiser les implémentations des accesseurs d’une propriété virtuelle existante.

Une déclaration de propriété de substitution doit spécifier l’exactement les mêmes modificateurs d’accessibilité, le type et le nom que la propriété héritée. Si la propriété héritée n’a qu’un seul accesseur (par exemple, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur. Si la propriété héritée comprend les deux accesseurs (par exemple, si la propriété héritée est en lecture-écriture), la substitution de propriété peut inclure un seul accesseur ou les deux accesseurs.

Une déclaration de propriété de substitution peut inclure le `sealed` modificateur. Utilisation de ce modificateur empêche la substitution ultérieure de la propriété d’une classe dérivée. Les accesseurs d’une propriété sealed sont également fermés.

À l’exception des différences dans la déclaration et d’appel, syntaxe, virtual, sealed, override et accesseurs abstraits se comportent exactement comme virtual, sealed, override et méthodes abstraites. Plus précisément, les règles décrites dans [les méthodes virtuelles](classes.md#virtual-methods), [substituer les méthodes](classes.md#override-methods), [Sealed méthodes](classes.md#sealed-methods), et [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si accesseurs étaient des méthodes d’un formulaire correspondant :

*  Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour de type de propriété et les modificateurs de mêmes que la propriété conteneur.
*  Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété, un `void` type de retour et les modificateurs de mêmes que la propriété conteneur.

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
`X` est une propriété en lecture seule virtuelle, `Y` est une propriété en lecture-écriture virtuelle, et `Z` est une propriété en lecture-écriture abstraite. Étant donné que `Z` est abstrait, la classe conteneur `A` doit également être déclarée abstraite.

Une classe qui dérive de `A` sont présentées ci-dessous :
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

Ici, les déclarations de `X`, `Y`, et `Z` remplacent les déclarations de propriété. Chaque déclaration de propriété correspond à exactement les modificateurs d’accessibilité, le type et le nom de la propriété héritée correspondante. Le `get` l’accesseur de `X` et `set` l’accesseur de `Y` utiliser le `base` mot clé pour accéder aux accesseurs hérités. La déclaration de `Z` remplace deux accesseurs : par conséquent, il n’existe aucun membre de fonction abstraite en suspens dans `B`, et `B` est autorisée à être une classe non abstraite.

Quand une propriété est déclarée comme un `override`, les accesseurs remplacés doivent être accessibles au code de substitution. En outre, l’accessibilité déclarée de la propriété ou l’indexeur lui-même et des accesseurs, doit correspondre à celui du membre substitué et les accesseurs. Exemple :
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

## <a name="events"></a>Événements

Un ***événement*** est un membre qui permet à un objet ou une classe de fournir des notifications. Les clients peuvent attacher un code exécutable pour les événements en fournissant ***gestionnaires d’événements***.

Les événements sont déclarés à l’aide de *event_declaration*%s :

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

Un *event_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès ](classes.md#access-modifiers)), la `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([méthodes statiques et d’instance](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), `override` ([Substituer les méthodes](classes.md#override-methods)), `sealed` ([Sealed méthodes](classes.md#sealed-methods)), `abstract` ([méthodes abstraites](classes.md#abstract-methods)), et `extern` ([Méthodes externes](classes.md#external-methods)) modificateurs.

Déclarations d’événements sont soumis aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons de modificateurs valides.

Le *type* d’un événement déclaration doit être un *delegate_type* ([types référence](types.md#reference-types)) et qui *delegate_type* doit être au moins en tant que accessible en tant que l’événement lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Une déclaration d’événement peut inclure *event_accessor_declarations*. Toutefois, le cas contraire, pour les non-extern, événements non abstraite, le compilateur les fournit automatiquement ([les événements de type champ](classes.md#field-like-events)) ; pour les événements externes, les accesseurs sont fournis en externe.

Une déclaration d’événement qui omet *event_accessor_declarations* définit un ou plusieurs événements, un pour chacune de la *variable_declarator*s. Les attributs et les modificateurs s’appliquent à tous les membres déclarés par ce type une *event_declaration*.

Il s’agit d’une erreur lors de la compilation pour un *event_declaration* à inclure à la fois le `abstract` modificateur et délimités par des accolades *event_accessor_declarations*.

Lorsqu’une déclaration d’événement inclut un `extern` modificateur, l’événement est dite un ***événement externe***. Car une déclaration event externe ne fournit aucune implémentation réelle, il est une erreur pour pouvoir inclure à la fois le `extern` modificateur et *event_accessor_declarations*.

Il s’agit d’une erreur lors de la compilation pour un *variable_declarator* d’une déclaration d’événement avec un `abstract` ou `external` modificateur à inclure un *variable_initializer*.

Un événement peut être utilisé comme opérande de gauche de la `+=` et `-=` opérateurs ([assignation d’événement](expressions.md#event-assignment)). Ces opérateurs sont utilisés, respectivement, pour attacher des gestionnaires d’événements à ou supprimer des gestionnaires d’événements à partir d’un événement, et les modificateurs d’accès de l’événement déterminent les contextes dans lesquels ces opérations sont autorisées.

Dans la mesure où `+=` et `-=` sont les seules opérations autorisées sur un événement en dehors du type qui déclare l’événement, le code externe peuvent ajouter et supprimer des gestionnaires pour un événement, mais ne peut pas de quelque autre manière obtenir ou modifier la liste sous-jacente d’événement gestionnaires.

Dans une opération de la forme `x += y` ou `x -= y`, lorsque `x` est un événement et la référence a lieu en dehors du type qui contient la déclaration de `x`, le résultat de l’opération a type `void` (au lieu d’avoir le type de `x`, avec la valeur de `x` après l’attribution). Cette règle empêche le code externe d’examiner indirectement le délégué sous-jacent d’un événement.

L’exemple suivant montre comment les gestionnaires d’événements sont attachés à des instances de la `Button` classe :
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

Ici, le `LoginDialog` constructeur d’instance crée deux `Button` instances et attache les gestionnaires d’événements pour le `Click` événements.

### <a name="field-like-events"></a>Événements de type champ

Dans le texte de programme de la classe ou structure qui contient la déclaration d’un événement, certains événements peuvent être utilisés comme champs. Pour être utilisé de cette façon, un événement ne doit pas être `abstract` ou `extern`et ne doit pas inclure explicitement *event_accessor_declarations*. Un tel événement peut être utilisé dans n’importe quel contexte qui permet à un champ. Le champ contient un délégué ([délégués](delegates.md)) qui fait référence à la liste des gestionnaires d’événements qui ont été ajoutés à l’événement. Si aucun gestionnaire d’événements n’ont été ajoutés, le champ contient `null`.

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
`Click` est utilisé en tant que champ dans le `Button` classe. Comme illustré dans l’exemple, le champ peut être examiné, modifié et utilisé dans les expressions d’appel de délégué. Le `OnClick` méthode dans le `Button` classe « déclenche » le `Click` événement. La notion de déclenchement d’un événement est équivalente à l’appel de délégué représenté par l’événement. Par conséquent, il n’existe aucune construction de langage particulière pour déclencher des événements. Notez que l’appel de délégué est précédé d’une vérification qui garantit que le délégué n’est pas null.

En dehors de la déclaration de la `Button` (classe), le `Click` membre peut uniquement être utilisé sur le côté gauche de la `+=` et `-=` opérateurs, comme dans
```csharp
b.Click += new EventHandler(...);
```
ce qui ajoute un délégué à la liste d’appel de la `Click` événement, et
```csharp
b.Click -= new EventHandler(...);
```
qui supprime un délégué de la liste d’appel de la `Click` événement.

Lors de la compilation d’un événement de type de champ, le compilateur crée automatiquement le stockage pour stocker le délégué et crée des accesseurs pour l’événement qui ajoutent ou suppriment des gestionnaires d’événements pour le champ de délégué. Les opérations d’addition et de suppression sont thread-safe et peut (mais n’êtes pas obligées) être effectuée tout en maintenant le verrou ([l’instruction lock](statements.md#the-lock-statement)) sur l’objet conteneur pour un événement d’instance, ou l’objet de type ([anonyme expressions de la création d’objet](expressions.md#anonymous-object-creation-expressions)) pour un événement statique.

Par conséquent, une instance déclaration d’événement sous la forme :
```csharp
class X
{
    public event D Ev;
}
```
est compilé en quelque chose d’équivalent à :
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
Dans la classe `X`, fait référence à `Ev` sur le côté gauche de la `+=` et `-=` opérateurs provoquent l’ajouter et supprimer les accesseurs à appeler. Toutes les autres références à `Ev` sont compilés pour référencer le champ masqué `__Ev` à la place ([l’accès au membre](expressions.md#member-access)). Le nom «`__Ev`» est arbitraire ; le champ masqué peut avoir n’importe quel nom ou aucun nom du tout.

### <a name="event-accessors"></a>Accesseurs d’événement

Déclarations d’événement généralement omettent *event_accessor_declarations*, comme dans le `Button` exemple ci-dessus. Cela est justifié implique par conséquent, le cas dans lequel le coût de stockage d’un champ par événement n’est pas acceptable. Dans ce cas, une classe peut comprendre *event_accessor_declarations* et utiliser un mécanisme privé pour stocker la liste des gestionnaires d’événements.

Le *event_accessor_declarations* d’un événement spécifient les instructions exécutables associées d’ajout et suppression de gestionnaires d’événements.

Les déclarations d’accesseur se composent d’un *add_accessor_declaration* et un *remove_accessor_declaration*. Chaque déclaration d’accesseur se compose du jeton `add` ou `remove` suivie d’un *bloc*. Le *bloc* associé à un *add_accessor_declaration* spécifie les instructions à exécuter lorsqu’un gestionnaire d’événements est ajouté et le *bloc* associé à un *remove_accessor_declaration* spécifie les instructions à exécuter lorsqu’un gestionnaire d’événements est supprimé.

Chaque *add_accessor_declaration* et *remove_accessor_declaration* correspond à une méthode avec un paramètre de valeur unique du type d’événement et un `void` type de retour. Le paramètre implicite d’un accesseur d’événement est nommé `value`. Lorsqu’un événement est utilisé dans une assignation d’événement, l’accesseur d’événement approprié est utilisé. Plus précisément, si l’opérateur d’assignation est `+=` l’accesseur add est utilisé, et si l’opérateur d’assignation est `-=` l’accesseur remove est utilisé. Dans les deux cas, l’opérande droit de l’opérateur d’assignation est utilisé comme argument de l’accesseur d’événement. Le bloc d’un *add_accessor_declaration* ou un *remove_accessor_declaration* doit se conformer aux règles `void` méthodes décrites dans [corps de la méthode](classes.md#method-body). En particulier, `return` instructions dans un tel bloc ne sont pas autorisées à spécifier une expression.

Dans la mesure où un accesseur d’événement possède implicitement un paramètre nommé `value`, c’est une erreur de compilation pour une variable locale ou constante déclarée dans un accesseur d’événement a ce nom.

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
le `Control` classe implémente un mécanisme de stockage interne pour les événements. Le `AddEventHandler` méthode associe une valeur de délégué avec une clé, le `GetEventHandler` méthode retourne le délégué associé à une clé et le `RemoveEventHandler` méthode supprime le délégué en tant que gestionnaire d’événements pour l’événement spécifié. Vraisemblablement, le mécanisme de stockage sous-jacent est conçu tel qu’il n’existe aucun coût permettant d’associer un `null` déléguer la valeur avec une clé, et donc non prise en charge des événements ne consomment aucun stockage.

### <a name="static-and-instance-events"></a>Événements statiques et d’instance

Lorsqu’une déclaration d’événement inclut un `static` modificateur, l’événement est dite un ***événement statique***. En cas de non `static` modificateur est présent, l’événement est dite un ***événement d’instance***.

Un événement statique n’est pas associé à une instance spécifique, et il s’agit d’une erreur de compilation pour faire référence à `this` dans les accesseurs d’un événement statique.

Un événement d’instance est associé à une instance donnée d’une classe, et cette instance est accessible en tant que `this` ([cet accès](expressions.md#this-access)) dans les accesseurs de cet événement.

Lorsqu’un événement est référencé dans un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `E.M`si `M` est un événement statique, `E` doit indiquer un type contenant `M`et si `M` est un événement d’instance, E doit indiquer une instance d’un type contenant `M`.

Les différences entre statiques et membres d’instance sont décrits plus en détail dans [membres statiques et d’instance](classes.md#static-and-instance-members).

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a>Virtual, sealed, override et accesseurs d’événements abstraits

Un `virtual` déclaration d’événement spécifie que les accesseurs de cet événement sont virtuels. Le `virtual` modificateur s’applique aux deux accesseurs d’événement.

Un `abstract` déclaration d’événement spécifie que les accesseurs de l’événement sont virtuels, mais ne fournit pas une implémentation réelle des accesseurs. Au lieu de cela, les classes non abstraites dérivées doivent fournir leur propre implémentation pour les accesseurs en substituant l’événement. Étant donné qu’une déclaration d’événement abstract ne fournit aucune implémentation réelle, il ne peut pas fournir de délimités par des accolades *event_accessor_declarations*.

Une déclaration d’événement qui comprend à la fois le `abstract` et `override` modificateurs Spécifie que l’événement est abstrait et substitue à un événement de base. Les accesseurs d’un tel événement sont également abstract.

Déclarations d’événements abstraits sont uniquement autorisées dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).

Les accesseurs d’un événement virtuel hérité peuvent être substituées dans une classe dérivée en incluant une déclaration d’événement qui spécifie un `override` modificateur. Il s’agit une ***substitution de déclaration d’événement***. Une déclaration d’événement substitution ne déclare pas un nouvel événement. Au lieu de cela, il ne fait que spécialiser les implémentations des accesseurs d’un événement virtuel existant.

Une déclaration d’événement substitution doit spécifier l’exactement les mêmes modificateurs d’accessibilité, le type et le nom que l’événement substitué.

Une déclaration d’événement substitution peut-être inclure la `sealed` modificateur. Utilisation de ce modificateur empêche la substitution ultérieure de l’événement d’une classe dérivée. Les accesseurs d’un événement sealed sont également fermés.

C’est une erreur de compilation pour une déclaration d’événement substitution inclure un `new` modificateur.

À l’exception des différences dans la déclaration et d’appel, syntaxe, virtual, sealed, override et accesseurs abstraits se comportent exactement comme virtual, sealed, override et méthodes abstraites. Plus précisément, les règles décrites dans [les méthodes virtuelles](classes.md#virtual-methods), [substituer les méthodes](classes.md#override-methods), [Sealed méthodes](classes.md#sealed-methods), et [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si accesseurs étaient des méthodes d’un formulaire correspondant. Chaque accesseur correspond à une méthode avec un paramètre de valeur unique du type d’événement, un `void` type de retour et les modificateurs de mêmes que l’événement conteneur.

## <a name="indexers"></a>Indexeurs

Un ***indexeur*** est un membre qui permet à un objet à indexer de la même façon sous forme de tableau. Les indexeurs sont déclarés à l’aide de *indexer_declaration*%s :

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

Un *indexer_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès ](classes.md#access-modifiers)), la `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `virtual` ([les méthodes virtuelles](classes.md#virtual-methods)), `override` ([substituer les méthodes](classes.md#override-methods) ), `sealed` ([Sealed méthodes](classes.md#sealed-methods)), `abstract` ([méthodes abstraites](classes.md#abstract-methods)), et `extern` ([méthodes externes](classes.md#external-methods)) modificateurs.

Les déclarations d’indexeur sont soumis aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons de modificateurs valides, la seule exception étant que le modificateur statique n’est pas autorisée dans une déclaration d’indexeur.

Les modificateurs `virtual`, `override`, et `abstract` s’excluent mutuellement, sauf dans un cas. Le `abstract` et `override` modificateurs peuvent être utilisés ensemble afin qu’un indexeur abstrait peut substituer virtuelle.

Le *type* d’un indexeur déclaration spécifie le type d’élément de l’indexeur introduite par la déclaration. À moins que l’indexeur est une implémentation de membre d’interface explicite, le *type* est suivi par le mot clé `this`. Pour une implémentation de membre d’interface explicite, le *type* est suivie d’une *interface_type*, un «`.`» et le mot clé `this`. Contrairement aux autres membres, les indexeurs n’ont pas de noms définis par l’utilisateur.

Le *formal_parameter_list* spécifie les paramètres de l’indexeur. La liste de paramètres formels d’un indexeur correspond à celui d’une méthode ([paramètres de méthode](classes.md#method-parameters)), sauf qu’au moins un paramètre doit être spécifié et que le `ref` et `out` modificateurs de paramètre ne sont pas autorisées .

Le *type* d’un indexeur et chacun des types référencés dans le *formal_parameter_list* doit être au moins aussi accessibles que l’indexeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Un *indexer_body* peut soit être constituée d’un ***corps de l’accesseur*** ou un ***corps d’expression***. Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre «`{`« et »`}`« jetons, déclarez des accesseurs ([accesseurs](classes.md#accessors)) de la propriété. Les accesseurs spécifient les instructions exécutables associées à la lecture et l’écriture de la propriété.

Un corps d’expression consistant en «`=>`» suivi d’une expression `E` et un point-virgule est exactement équivalent au corps d’instruction `{ get { return E; } }`et peut donc uniquement être utilisée pour spécifier des indexeurs getter uniquement où le résultat de l’accesseur Get est indiqué par une expression unique.

Bien que la syntaxe pour accéder à un élément d’indexeur est identique à celui d’un élément de tableau, un élément d’indexeur n’est pas classé en tant que variable. Par conséquent, il n’est pas possible de passer d’un élément d’indexeur en tant qu’un `ref` ou `out` argument.

La liste de paramètres formels d’un indexeur définit la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de l’indexeur. Plus précisément, la signature d’un indexeur se compose du nombre et types de ses paramètres formels. Le type d’élément et les noms des paramètres formels ne sont pas partie de la signature d’un indexeur.

La signature d’un indexeur doit différer des signatures de tous les autres indexeurs déclarés dans la même classe.

Les indexeurs et propriétés sont très similaires dans son concept, mais diffèrent comme suit :

*  Une propriété est identifiée par son nom, tandis qu’un indexeur est identifié par sa signature.
*  Une propriété est accessible via un *simple_name* ([noms simples](expressions.md#simple-names)) ou un *member_access* ([l’accès au membre](expressions.md#member-access)), alors qu’un indexeur élément est accessible via une *element_access* ([accès indexeur](expressions.md#indexer-access)).
*  Une propriété peut être un `static` membre, tandis qu’un indexeur est toujours un membre d’instance.
*  Un `get` accesseur d’une propriété correspond à une méthode sans paramètres, tandis qu’un `get` accesseur d’indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur.
*  Un `set` accesseur d’une propriété correspond à une méthode avec un paramètre unique nommé `value`, tandis qu’un `set` accesseur d’indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur, ainsi qu’un paramètre supplémentaire nommé `value`.
*  Il s’agit d’une erreur lors de la compilation pour un accesseur d’indexeur déclarer une variable locale avec le même nom comme paramètre d’indexeur.
*  Dans une déclaration de propriété de substitution, la propriété héritée est accessible à l’aide de la syntaxe `base.P`, où `P` est le nom de propriété. Dans une déclaration d’indexeur substitution, la propriété héritée est accessible à l’aide de la syntaxe `base[E]`, où `E` est une liste séparée par des virgules des expressions.
*  Il n’existe aucun concept de « indexeur implémenté automatiquement ». Il est une erreur d’avoir un indexeur non abstraite, non externe avec des accesseurs de points-virgules.

Outre ces différences, toutes les règles définies dans [accesseurs](classes.md#accessors) et [implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties) s’appliquent aux accesseurs des indexeurs, ainsi que pour les accesseurs de propriété.

Lorsqu’une déclaration d’indexeur inclut un `extern` modificateur, l’indexeur est dite un ***indexeur externe***. Car une déclaration d’indexeur externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.

L’exemple ci-dessous déclare une `BitArray` classe qui implémente un indexeur permettant d’accéder aux bits individuels dans le tableau de bits.
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

Une instance de la `BitArray` classe consomme beaucoup moins de mémoire que correspondante `bool[]` (étant donné que chaque valeur de la première occupe un seul bit à la place de ce dernier d’un octet), mais autorise les mêmes opérations qu’un `bool[]`.

Ce qui suit `CountPrimes` classe utilise un `BitArray` et l’algorithme « tamis » classique pour calculer le nombre de nombres premiers entre 1 et un maximum donné :
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

Notez que la syntaxe permettant d’accéder aux éléments de la `BitArray` est précisément les mêmes que pour un `bool[]`.

L’exemple suivant montre une classe de 26 * 10 grille qui a un indexeur avec deux paramètres. Le premier paramètre est requis pour être lettres majuscules ou minuscules dans la plage A-Z, et la seconde doit être un entier compris entre 0 et 9.

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

### <a name="indexer-overloading"></a>Surcharge d’indexeur

Les règles de résolution de surcharge indexeur sont décrites dans [l’inférence de Type](expressions.md#type-inference).

## <a name="operators"></a>Opérateurs

Un ***opérateur*** est un membre qui définit la signification d’un opérateur d’expression qui peut être appliqué aux instances de la classe. Les opérateurs sont déclarés à l’aide de *operator_declaration*%s :

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

Il existe trois catégories d’opérateurs surchargeables : Opérateurs unaires ([opérateurs unaires](classes.md#unary-operators)), opérateurs binaires ([opérateurs binaires](classes.md#binary-operators)) et les opérateurs de conversion ([les opérateurs de Conversion](classes.md#conversion-operators)).

Le *operator_body* est soit un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***. Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque l’opérateur est appelé. Le *bloc* doit être conforme aux règles de retourner de valeur méthodes décrites dans [corps de la méthode](classes.md#method-body). Un corps d’expression se compose de `=>` suivie d’une expression et un point-virgule et désigne une seule expression à exécuter lorsque l’opérateur est appelé.

Pour `extern` opérateurs, les *operator_body* est simplement constitué d’un point-virgule. Pour tous les autres opérateurs, les *operator_body* est un corps de bloc ou d’un corps d’expression.

Les règles suivantes s’appliquent à toutes les déclarations d’opérateur :

*  Une déclaration d’opérateur doit inclure à la fois un `public` et un `static` modificateur.
*  L’ou les paramètres d’un opérateur doit être un paramètre de valeur ([paramètres de valeur](variables.md#value-parameters)). Il s’agit d’une erreur lors de la compilation pour une déclaration d’opérateur spécifier `ref` ou `out` paramètres.
*  La signature d’un opérateur ([opérateurs unaires](classes.md#unary-operators), [opérateurs binaires](classes.md#binary-operators), [les opérateurs de Conversion](classes.md#conversion-operators)) doit être différente des signatures de tous les autres opérateurs déclarés dans le même classe.
*  Tous les types référencés dans une déclaration d’opérateur doivent être au moins aussi accessibles que l’opérateur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).
*  C’est une erreur pour le même modificateur apparaît plusieurs fois dans une déclaration d’opérateur.

Chaque catégorie d’opérateur impose des restrictions supplémentaires, comme décrit dans les sections suivantes.

Comme d’autres membres, les opérateurs déclarés dans une classe de base sont hérités par les classes dérivées. Étant donné que les déclarations d’opérateur toujours besoin de la classe ou structure dans laquelle l’opérateur est déclaré pour participer à la signature de l’opérateur, il n’est pas possible pour un opérateur déclaré dans une classe dérivée masquer un opérateur déclaré dans une classe de base. Par conséquent, le `new` modificateur n’est jamais requise et par conséquent jamais autorisé, dans une déclaration d’opérateur.

Vous trouverez plus d’informations sur les opérateurs unaires et binaires dans [opérateurs](expressions.md#operators).

Vous trouverez plus d’informations sur les opérateurs de conversion dans [conversions définies par l’utilisateur](conversions.md#user-defined-conversions).

### <a name="unary-operators"></a>Les opérateurs unaires.

Les règles suivantes s’appliquent aux déclarations d’opérateur unaire, où `T` indique le type de l’instance de la classe ou structure qui contient la déclaration d’opérateur :

*  Un opérateur unaire `+`, `-`, `!`, ou `~` opérateur doit accepter un seul paramètre de type `T` ou `T?` et peut retourner tout type.
*  Un opérateur unaire `++` ou `--` opérateur doit accepter un seul paramètre de type `T` ou `T?` et doit retourner que même type ou un type dérivé.
*  Un opérateur unaire `true` ou `false` opérateur doit accepter un seul paramètre de type `T` ou `T?` et retourner le type `bool`.

La signature d’un opérateur unaire se compose du jeton d’opérateur (`+`, `-`, `!`, `~`, `++`, `--`, `true`, ou `false`) et le type du paramètre formel unique. Le type de retour n’est pas partie de la signature d’un opérateur unaire, ni le nom du paramètre formel.

Le `true` et `false` opérateurs unaires nécessitent une déclaration par couple. Une erreur de compilation se produit si une classe déclare l’un de ces opérateurs sans également déclarer l’autre. Le `true` et `false` opérateurs sont décrites dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators) et [expressions booléennes](expressions.md#boolean-expressions).

L’exemple suivant montre une implémentation et l’utilisation ultérieure de `operator ++` pour une classe de vecteur entier :
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

Notez comment la méthode d’opérateur retourne la valeur produite en ajoutant 1 à l’opérande, comme l’incrémentation et de décrémentation des opérateurs ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators)) et l’incrémentation de préfixe et de décrémentation opérateurs ([Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)). Contrairement à dans C++, cette méthode ne devez pas modifier la valeur de son opérande directement. En fait, la modification de la valeur de l’opérande risque de violer la sémantique standard de l’opérateur d’incrémentation suffixée.

### <a name="binary-operators"></a>Opérateurs binaires

Les règles suivantes s’appliquent aux déclarations de l’opérateur binaire, où `T` indique le type de l’instance de la classe ou structure qui contient la déclaration d’opérateur :

*  Un opérateur de décalage non binaire doit prendre deux paramètres, au moins un d'entre eux doit avoir le type `T` ou `T?`et peut retourner tout type.
*  Un fichier binaire `<<` ou `>>` opérateur doit accepter deux paramètres, le premier d'entre eux doit avoir le type `T` ou `T?` et le second type `int` ou `int?`et peut retourner tout type.

La signature d’un opérateur binaire se compose du jeton d’opérateur (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, ou `<=`) et les types des deux paramètres formels. Le type de retour et les noms des paramètres formels ne sont pas partie de la signature de l’opérateur binaire.

Certains opérateurs binaires exigent une déclaration par couple. Pour chaque déclaration de des opérateurs d’une paire, il doit être une déclaration de l’autre opérateur de la paire correspondante. Deux déclarations d’opérateur correspondent lorsqu’elles ont le même type de retour et le même type pour chaque paramètre. Les opérateurs suivants nécessitent une déclaration par couple :

*  `operator ==` et `operator !=`
*  `operator >` et `operator <`
*  `operator >=` et `operator <=`

### <a name="conversion-operators"></a>Opérateurs de conversion

Une déclaration d’opérateur de conversion présente un ***conversion définie par l’utilisateur*** ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) qui augmente les conversions implicites et explicites prédéfinies.

Une déclaration d’opérateur de conversion qui inclut le `implicit` mot clé introduit une conversion implicite défini par l’utilisateur. Les conversions implicites peuvent se produire dans diverses situations, y compris les appels de fonctions membres, des expressions de cast et des assignations. Cela est décrite plus loin dans [conversions implicites](conversions.md#implicit-conversions).

Une déclaration d’opérateur de conversion qui inclut le `explicit` mot clé introduit une conversion explicite définie par l’utilisateur. Les conversions explicites peuvent se produire dans les expressions de cast et sont décrites dans [conversions explicites](conversions.md#explicit-conversions).

Un opérateur de conversion convertit un type de source, indiqué par le type de paramètre de l’opérateur de conversion, à un type de cible, indiqué par le type de retour de l’opérateur de conversion.

Pour un type de source donné `S` et type de cible `T`si `S` ou `T` sont des types nullables, permettent de `S0` et `T0` font référence à leurs types sous-jacents, sinon `S0` et `T0` sont égal à `S` et `T` respectivement. Une classe ou un struct est autorisé à déclarer une conversion à partir d’un type de source `S` à un type de cible `T` uniquement si toutes les conditions suivantes sont remplies :

*  `S0` et `T0` sont les différents types.
*  Soit `S0` ou `T0` est le type de classe ou struct dans lequel la déclaration d’opérateur a lieu.
*  Ni `S0` ni `T0` est un *interface_type*.
*  À l’exception des conversions définies par l’utilisateur, une conversion n’existe pas de `S` à `T` ou à partir de `T` à `S`.

Dans le cadre de ces règles, les paramètres associés de type `S` ou `T` sont considérés comme des types uniques qui ont aucune relation d’héritage avec d’autres types et des contraintes de type de ces paramètres sont ignorés.

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
les déclarations de deux opérateur première sont autorisées, car, dans le cadre de [indexeurs](classes.md#indexers).3, `T` et `int` et `string` respectivement sont considérés comme des types uniques avec aucune relation. Toutefois, le troisième opérateur est une erreur car `C<T>` est la classe de base de `D<T>`.

À partir de la deuxième règle, qu'il en résulte qu’un opérateur de conversion doit convertir vers ou depuis le type de classe ou struct dans lequel l’opérateur est déclaré. Par exemple, il est possible pour un type classe ou struct `C` à définir une conversion de `C` à `int` et à partir de `int` à `C`, mais pas de `int` à `bool`.

Il n’est pas possible de redéfinir directement une conversion prédéfinie. Par conséquent, les opérateurs de conversion ne sont pas autorisés à convertir depuis ou vers `object` , car les conversions implicites et explicites existent déjà entre `object` et tous les autres types. De même, ni la source ni les types de cible d’une conversion peuvent être un type de base de l’autre, dans la mesure où une conversion puis existent déjà.

Toutefois, il est possible de déclarer des opérateurs sur les types génériques qui, pour les arguments de type particulier, spécifient les conversions qui existent déjà en tant que de conversions prédéfinies. Dans l’exemple
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
Tapez lorsque `object` est spécifié comme un argument de type pour `T`, le deuxième opérateur déclare une conversion qui existe déjà (implicite et par conséquent également explicite, conversion existe à partir de tous types `object`).

Dans les cas où une conversion prédéfinie existe entre deux types, les conversions définies par l’utilisateur entre ces types sont ignorées. Plus précisément :

*  Si une conversion implicite prédéfinie ([conversions implicites](conversions.md#implicit-conversions)) existe entre le type `S` à taper `T`, tous définis par l’utilisateur conversions (implicites ou explicites) à partir de `S` à `T` sont ignorés.
*  Si une conversion explicite prédéfinie ([conversions explicites](conversions.md#explicit-conversions)) existe entre le type `S` à taper `T`, les conversions explicites définies par l’utilisateur à partir de `S` à `T` sont ignorés. En outre :

Si `T` est un type interface, les conversions implicites définies par l’utilisateur à partir de `S` à `T` sont ignorés.

Sinon, définie par l’utilisateur des conversions implicites de `S` à `T` sont toujours considérées comme.

Pour tous les types mais `object`, les opérateurs déclarés par le `Convertible<T>` type ci-dessus ne portent pas de conversions prédéfinies. Exemple :
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

Toutefois, pour le type `object`, conversions prédéfinies masquer les conversions définies par l’utilisateur dans tous les cas, mais une :

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

Conversions définies par l’utilisateur ne sont pas autorisées à convertir depuis ou vers *interface_type*s. En particulier, cette restriction permet de s’assurer qu’aucune transformation définie par l’utilisateur se produire lors de la conversion à un *interface_type*et qu’une conversion vers un *interface_type* réussit uniquement si l’objet en cours de conversion implémente réellement spécifié *interface_type*.

La signature d’un opérateur de conversion se compose du type source et le type de cible. (Notez qu’il s’agit de la seule forme de membre pour lequel le type de retour participe à la signature). Le `implicit` ou `explicit` classification d’un opérateur de conversion ne fait pas partie de la signature de l’opérateur. Par conséquent, une classe ou un struct ne peut pas déclarer un `implicit` et un `explicit` opérateur de conversion avec les mêmes types source et cible.

En règle générale, les conversions implicites définies par l’utilisateur doivent être conçues pour jamais lever d’exceptions et jamais perdre d’informations. Si une conversion définie par l’utilisateur peut donner lieu à des exceptions (par exemple, étant donné que l’argument de la source est hors limites) ou de perte d’informations (par exemple, en ignorant les bits de poids fort), alors que la conversion doit être définie comme une conversion explicite.

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
la conversion de `Digit` à `byte` est implicite, car il lève des exceptions ou perd des informations, mais la conversion à partir de jamais `byte` à `Digit` est explicite depuis `Digit` peut représenter uniquement un sous-ensemble de la possible valeurs d’un `byte`.

## <a name="instance-constructors"></a>Constructeurs d’instance

Un ***constructeur d’instance*** est un membre qui implémente les actions requises pour initialiser une instance d’une classe. Constructeurs d’instance sont déclarées à l’aide de *constructor_declaration*%s :

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

Un *constructor_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)), une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès ](classes.md#access-modifiers)) et un `extern` ([méthodes externes](classes.md#external-methods)) modificateur. Une déclaration de constructeur n’est pas autorisée à contenir le même modificateur plusieurs fois.

Le *identificateur* d’un *constructor_declarator* doit nommer la classe dans laquelle le constructeur d’instance est déclaré. Si aucun autre nom est spécifié, une erreur de compilation se produit.

Le paramètre facultatif *formal_parameter_list* d’une instance constructeur est soumis aux mêmes règles que le *formal_parameter_list* d’une méthode ([méthodes](classes.md#methods)). La liste de paramètres formels définit la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’un constructeur d’instance et détermine le processus par lequel la résolution de surcharge ([l’inférence de Type](expressions.md#type-inference)) sélectionne un particulier constructeur d’instance dans un appel.

Chacun des types référencés dans le *formal_parameter_list* d’une instance constructeur doit être au moins aussi accessible que le constructeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).

Le paramètre facultatif *constructor_initializer* spécifie un autre constructeur d’instance à appeler avant d’exécuter les instructions fournies dans le *constructor_body* de ce constructeur d’instance. Cela est décrite plus loin dans [initialiseurs de constructeur](classes.md#constructor-initializers).

Lorsqu’une déclaration de constructeur inclut un `extern` modificateur, le constructeur est dite un ***constructeur externe***. Étant donné qu’une déclaration de constructeur externe ne fournit aucune implémentation réelle, son *constructor_body* se compose d’un point-virgule. Pour tous les autres constructeurs, le *constructor_body* se compose d’un *bloc* qui spécifie les instructions pour initialiser une nouvelle instance de la classe. Cela correspond exactement à la *bloc* d’une méthode d’instance avec un `void` type de retour ([corps de la méthode](classes.md#method-body)).

Constructeurs d’instance ne sont pas hérités. Par conséquent, une classe n’a aucun constructeur d’instance autres que ceux réellement déclarés dans la classe. Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est automatiquement fourni ([constructeurs par défaut](classes.md#default-constructors)).

Constructeurs d’instance sont appelés par *object_creation_expression*s ([des expressions de la création d’objet](expressions.md#object-creation-expressions)) et via *constructor_initializer*s.

### <a name="constructor-initializers"></a>Initialiseurs de constructeur

Tous les constructeurs d’instances (à l’exception de celles de la catégorie `object`) incluent implicitement un appel à un autre constructeur d’instance immédiatement avant le *constructor_body*. Le constructeur à appeler implicitement est déterminé par le *constructor_initializer*:

*  Un initialiseur de constructeur d’instance sous la forme `base(argument_list)` ou `base()` provoque un constructeur d’instance à partir de la classe de base directe. Ce constructeur est sélectionné à l’aide de *argument_list* si présent et les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidats se compose de tous les constructeurs d’instance accessible contenues dans la classe de base directe ou le constructeur par défaut ([constructeurs par défaut](classes.md#default-constructors)), si aucun constructeur d’instance n’est déclarées dans le classe de base directe. Si cet ensemble est vide, ou si un seul constructeur d’instance optimal ne peut pas être identifié, une erreur de compilation se produit.
*  Un initialiseur de constructeur d’instance sous la forme `this(argument-list)` ou `this()` provoque un constructeur d’instance à partir de la classe elle-même à appeler. Le constructeur est sélectionné à l’aide de *argument_list* si présent et les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidats se compose de tous les constructeurs d’instance accessibles déclarés dans la classe elle-même. Si cet ensemble est vide, ou si un seul constructeur d’instance optimal ne peut pas être identifié, une erreur de compilation se produit. Si une déclaration de constructeur d’instance comprend un initialiseur de constructeur qui appelle le constructeur lui-même, une erreur de compilation se produit.

Si un constructeur d’instance ne possède aucun initialiseur de constructeur, un initialiseur de constructeur du formulaire `base()` est fourni implicitement. Par conséquent, une déclaration de constructeur d’instance du formulaire
```csharp
C(...) {...}
```
équivaut exactement à
```csharp
C(...): base() {...}
```

La portée des paramètres fournis par le *formal_parameter_list* d’un constructeur d’instance déclaration inclut l’initialiseur de constructeur de cette déclaration. Par conséquent, un initialiseur de constructeur est autorisé à accéder aux paramètres du constructeur. Exemple :
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

Un initialiseur de constructeur d’instance ne peut pas accéder à l’instance en cours de création. Par conséquent, il est une erreur de compilation pour faire référence à `this` dans une expression d’argument de l’initialiseur de constructeur, comme est-il une erreur de compilation pour un argument d’expression faire référence à n’importe quel membre d’instance via un *simple_name*.

### <a name="instance-variable-initializers"></a>Initialiseurs de variable d’instance

Quand un constructeur d’instance n’a aucun initialiseur de constructeur, ou il a un initialiseur de constructeur du formulaire `base(...)`, ce constructeur effectue implicitement les initialisations spécifiées par le *variable_initializer*s de les champs d’instance déclarés dans sa classe. Cela correspond à une séquence d’assignations qui sont exécutées immédiatement lors de l’entrée au constructeur et avant l’appel du constructeur de classe de base directe implicite. Les initialiseurs de variable sont exécutées dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe.

### <a name="constructor-execution"></a>Exécution du constructeur

Les initialiseurs de variable sont transformés en instructions d’assignation, et ces instructions d’assignation sont exécutées avant l’appel du constructeur d’instance de classe de base. Cet ordre garantit que tous les champs d’instance sont initialisés à leurs initialiseurs de variable avant l’exécution de toutes les instructions qui ont accès à cette instance.

Étant donné l’exemple
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
```
x = 1, y = 0
```

La valeur de `x` est 1, car l’initialiseur de variable est exécuté avant que le constructeur d’instance de classe de base est appelé. Toutefois, la valeur de `y` est 0 (la valeur par défaut une `int`), car l’assignation à `y` n’est pas exécutée avant une fois que le constructeur de classe de base retourne.

Il est utile d’envisager des initialiseurs de variable d’instance et les initialiseurs de constructeur comme des instructions qui sont automatiquement insérées devant le *constructor_body*. L’exemple
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
contient plusieurs initialiseurs de variable ; Il contient également des initialiseurs de constructeur des deux formulaires (`base` et `this`). L’exemple correspond au code indiqué ci-dessous, où chaque commentaire indique une instruction insérée automatiquement (la syntaxe utilisée pour les appels de constructeur inséré automatiquement n’est pas valide, mais sert uniquement à illustrer le mécanisme).

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

Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est fourni automatiquement. Ce constructeur par défaut appelle simplement le constructeur sans paramètre de la classe de base directe. Si la classe est abstraite, l’accessibilité déclarée pour le constructeur par défaut est protégée. Sinon, l’accessibilité déclarée pour le constructeur par défaut est publique. Par conséquent, le constructeur par défaut est toujours au format

```csharp
protected C(): base() {}
```
ou
```csharp
public C(): base() {}
```
où `C` est le nom de la classe. Une erreur de compilation se produit si la résolution de surcharge ne peut pas déterminer un meilleur candidat pour l’initialiseur de constructeur de classe de base unique.

Dans l’exemple
```csharp
class Message
{
    object sender;
    string text;
}
```
un constructeur par défaut est fourni, car la classe ne contient aucune déclaration de constructeur d’instance. Par conséquent, l’exemple est équivalente à
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a>Constructeurs privés

Lorsqu’une classe `T` déclare uniquement des constructeurs d’instance privé, il n’est pas possible pour les classes en dehors du texte de programme de `T` dériver `T` ou pour créer directement des instances de `T`. Par conséquent, si une classe contient uniquement des membres statiques et n’est pas destinée à être instanciée, l’ajout d’un constructeur d’instance privé empêche l’instanciation. Exemple :
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

Le `Trig` classe regroupe les méthodes associées et des constantes, mais n’est pas destinée à être instanciée. Par conséquent, elle déclare un constructeur d’instance privé unique. Au moins un constructeur d’instance doit être déclaré pour supprimer la génération automatique d’un constructeur par défaut.

### <a name="optional-instance-constructor-parameters"></a>Paramètres de constructeur d’instance facultatif

Le `this(...)` forme d’initialiseur de constructeur est utilisée conjointement avec la surcharge pour implémenter les paramètres de constructeur d’instance facultatif. Dans l’exemple
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
les constructeurs de deux instance première fournissent simplement les valeurs par défaut pour les arguments manquants. Les deux utilisent un `this(...)` initialiseur de constructeur à appeler le troisième constructeur d’instance, qui effectue réellement le travail d’initialisation de la nouvelle instance. L’effet est celui des paramètres de constructeur facultatif :
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a>Constructeurs statiques

Un ***constructeur statique*** est un membre qui implémente les actions requises pour initialiser un type de classe fermé. Les constructeurs statiques sont déclarées à l’aide de *static_constructor_declaration*%s :

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

Un *static_constructor_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)) et un `extern` modificateur ([méthodes externes](classes.md#external-methods)).

Le *identificateur* d’un *static_constructor_declaration* doit nommer la classe dans laquelle le constructeur statique est déclaré. Si aucun autre nom est spécifié, une erreur de compilation se produit.

Lorsqu’une déclaration de constructeur statique inclut un `extern` modificateur, le constructeur statique est dite un ***constructeur statique externe***. Étant donné qu’une déclaration de constructeur statique externe ne fournit aucune implémentation réelle, son *static_constructor_body* se compose d’un point-virgule. Pour toutes les autres déclarations de constructeur statique, le *static_constructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter afin d’initialiser la classe. Cela correspond exactement à la *method_body* d’une méthode statique avec un `void` type de retour ([corps de la méthode](classes.md#method-body)).

Les constructeurs statiques ne sont pas hérités et ne peut pas être appelées directement.

Le constructeur statique pour un type de classe fermé exécute au maximum une fois dans un domaine d’application donné. L’exécution d’un constructeur statique est déclenchée par le premier des événements suivants se produise au sein d’un domaine d’application :

*  Une instance du type de classe est créée.
*  Un des membres statiques du type classe sont référencé.

Si une classe contient le `Main` (méthode) ([démarrage de l’Application](basic-concepts.md#application-startup)) dans laquelle l’exécution commence, le constructeur statique pour cette classe s’exécute avant le `Main` méthode est appelée.

Pour initialiser un nouveau type de classe fermé, tout d’abord un nouvel ensemble de champs statiques ([champs statiques et d’instance](classes.md#static-and-instance-fields)) pour ce type fermé particulier est créé. Chacun des champs statiques est initialisé à sa valeur par défaut ([valeurs par défaut](variables.md#default-values)). Ensuite, les initialiseurs de champ statique ([l’initialisation de champ statique](classes.md#static-field-initialization)) sont exécutées pour ces champs statiques. Enfin, le constructeur statique est exécuté.

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
```
Init A
A.F
Init B
B.F
```
Étant donné que l’exécution de `A`du constructeur statique est déclenché par l’appel à `A.F`et l’exécution de `B`du constructeur statique est déclenché par l’appel à `B.F`.

Il est possible de construire des dépendances circulaires qui permettent des champs statiques avec initialiseurs de variable à observer dans leur état de la valeur par défaut.

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
```
X = 1, Y = 2
```

Pour exécuter le `Main` (méthode), le système de la première exécution de l’initialiseur pour `B.Y`, avant de la classe `B`du constructeur statique. `Y`d’initialiseur `A`du constructeur statique à exécuter, car la valeur de `A.X` est référencé. Le constructeur statique de `A` à son tour se poursuit pour calculer la valeur de `X`et cela extractions c’est le cas la valeur par défaut `Y`, qui est égal à zéro. `A.X` est donc initialisé à 1. Le processus en cours d’exécution `A`du constructeur statique et les initialiseurs de champ statique, puis se termine et renvoie le calcul de la valeur initiale de `Y`, dont le résultat devient 2.

Étant donné que le constructeur statique est exécuté une seule fois pour chaque type de classe construite de fermé, il est commode pour appliquer des contrôles d’exécution sur le paramètre de type ne peut pas être vérifiées au moment de la compilation par le biais de contraintes ([paramètre de Type contraintes](classes.md#type-parameter-constraints)). Par exemple, le type suivant utilise un constructeur statique pour assurer que l’argument de type est un enum :
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

Un ***destructeur*** est un membre qui implémente les actions requises pour détruire une instance d’une classe. Un destructeur est déclaré à l’aide un *destructor_declaration*:

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

Un *destructor_declaration* peut inclure un ensemble de *attributs* ([attributs](attributes.md)).

Le *identificateur* d’un *destructor_declaration* doit nommer la classe dans laquelle le destructeur est déclaré. Si aucun autre nom est spécifié, une erreur de compilation se produit.

Lorsqu’une déclaration de destructeur inclut un `extern` modificateur, le destructeur est dite un ***destructeur externe***. Étant donné qu’une déclaration de destructeur externe ne fournit aucune implémentation réelle, son *destructor_body* se compose d’un point-virgule. Pour tous les autres destructeurs, le *destructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter pour la destruction d’une instance de la classe. Un *destructor_body* correspond exactement à la *method_body* d’une méthode d’instance avec un `void` type de retour ([corps de la méthode](classes.md#method-body)).

Les destructeurs ne sont pas hérités. Par conséquent, une classe n’a aucun destructeur autre que celui qui peut être déclaré dans cette classe.

Dans la mesure où un destructeur est nécessaire pour n’avoir aucun paramètre, il ne peut pas être surchargé, donc une classe peut avoir, au maximum, un seul destructeur.

Les destructeurs sont appelés automatiquement et ne peut pas être appelés explicitement. Une instance devenue éligible pour la destruction lorsqu’il n’est plus possible pour n’importe quel code pour utiliser cette instance. L’exécution du destructeur de l’instance peut se produire à tout moment une fois que l’instance est éligible pour la destruction. Lorsqu’une instance est détruite, les destructeurs dans la chaîne d’héritage de cette instance sont appelés, dans l’ordre, du plus dérivé au moins dérivé. Un destructeur ne peut être exécuté sur n’importe quel thread. Pour plus d’informations sur les règles qui régissent quand et comment un destructeur est exécuté, consultez [gestion automatique de la mémoire](basic-concepts.md#automatic-memory-management).

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
Étant donné que les destructeurs dans une chaîne d’héritage sont appelés dans l’ordre, du plus dérivé au moins dérivé.

Les destructeurs sont implémentés en substituant la méthode virtuelle `Finalize` sur `System.Object`. Les programmes c# ne sont pas autorisés à substituer cette méthode ou appeler (ou les remplacements de celui-ci) directement. Par exemple, le programme
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

Le compilateur se comporte comme si cette méthode et des remplacements, n’existent pas du tout. Par conséquent, ce programme :
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
est valide, et la méthode affichée masque `System.Object`de `Finalize` (méthode).

Pour une description du comportement lorsqu’une exception est levée à partir d’un destructeur, consultez [gestion des exceptions](exceptions.md#how-exceptions-are-handled).

## <a name="iterators"></a>Iterators

Une fonction membre ([membres de fonction](expressions.md#function-members)) implémentée à l’aide d’un bloc itérateur ([blocs](statements.md#blocks)) est appelée une ***itérateur***.

Un bloc itérateur peut être utilisé comme le corps d’une fonction membre, que le type de retour de la fonction membre correspondant est une des interfaces d’énumérateur ([interfaces d’énumérateur](classes.md#enumerator-interfaces)) ou une des interfaces énumérables ([Interfaces énumérables](classes.md#enumerable-interfaces)). Il peut se produire comme un *method_body*, *operator_body* ou *accessor_body*, tandis que les événements, les constructeurs d’instance, les constructeurs statiques et les destructeurs ne peuvent pas être implémenté en tant qu’itérateurs.

Lorsqu’une fonction membre est implémentée à l’aide d’un bloc itérateur, il s’agit d’une erreur lors de la compilation pour obtenir la liste de paramètres formels de la fonction membre pour spécifier des `ref` ou `out` paramètres.

### <a name="enumerator-interfaces"></a>Interfaces d’énumérateur

Le ***interfaces d’énumérateur*** sont l’interface non générique `System.Collections.IEnumerator` et toutes les instanciations de l’interface générique `System.Collections.Generic.IEnumerator<T>`. Par souci de concision, dans ce chapitre ces interfaces sont référencés sous forme `IEnumerator` et `IEnumerator<T>`, respectivement.

### <a name="enumerable-interfaces"></a>Interfaces énumérables

Le ***interfaces énumérables*** sont l’interface non générique `System.Collections.IEnumerable` et toutes les instanciations de l’interface générique `System.Collections.Generic.IEnumerable<T>`. Par souci de concision, dans ce chapitre ces interfaces sont référencés sous forme `IEnumerable` et `IEnumerable<T>`, respectivement.

### <a name="yield-type"></a>Type yield

Un itérateur produit une séquence de valeurs, tous du même type. Ce type est appelé le ***yield type*** de l’itérateur.

*  Le type de rendement d’un itérateur qui retourne `IEnumerator` ou `IEnumerable` est `object`.
*  Le type de rendement d’un itérateur qui retourne `IEnumerator<T>` ou `IEnumerable<T>` est `T`.

### <a name="enumerator-objects"></a>Objets d’énumérateur

Lorsqu’une fonction membre retournant un énumérateur de type d’interface est implémentée à l’aide d’un bloc itérateur, appel de la fonction membre ne s’exécutera pas immédiatement le code dans le bloc itérateur. Au lieu de cela, une ***objet énumérateur*** est créé et retourné. Cet objet encapsule le code spécifié dans le bloc itérateur, et l’exécution du code dans le bloc itérateur se produit lorsque l’objet énumérateur `MoveNext` méthode est appelée. Un objet énumérateur a les caractéristiques suivantes :

*  Il implémente `IEnumerator` et `IEnumerator<T>`, où `T` est le type de rendement de l’itérateur.
*  Elle implémente `System.IDisposable`.
*  Il est initialisé avec une copie des valeurs d’argument (le cas échéant) et la valeur de l’instance passée à la fonction membre.
*  Il a quatre états potentiels, ***avant***, ***en cours d’exécution***, ***suspendu***, et ***après***et est initialement dans le ***avant***  état.

Un objet énumérateur est généralement une instance d’une classe d’énumérateur généré par le compilateur qui encapsule le code dans le bloc itérateur et implémente les interfaces d’énumérateur, mais les autres méthodes d’implémentation sont possibles. Si une classe d’énumérateur est générée par le compilateur, cette classe doit être imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, il aura accès privé, et il aura un nom réservé pour un usage du compilateur ([identificateurs ](lexical-structure.md#identifiers)).

Un objet énumérateur peut implémenter plusieurs interfaces que celles spécifiées ci-dessus.

Les sections suivantes décrivent le comportement exact de la `MoveNext`, `Current`, et `Dispose` membres de la `IEnumerable` et `IEnumerable<T>` fournies par un objet énumérateur des implémentations d’interface.

Notez que les objets d’énumérateur ne prennent pas en charge la `IEnumerator.Reset` (méthode). L’appel à cette méthode entraîne un `System.NotSupportedException` levée.

#### <a name="the-movenext-method"></a>La méthode MoveNext

Le `MoveNext` méthode d’un objet énumérateur encapsule le code d’un bloc itérateur. Appel de la `MoveNext` méthode exécute le code dans le bloc itérateur et définit le `Current` propriété de l’objet énumérateur comme il convient. L’action précise effectuée par `MoveNext` dépend de l’état de l’objet énumérateur lorsque `MoveNext` est appelé :

*  Si l’état de l’objet énumérateur est ***avant***, l’appel `MoveNext`:
   * Modifie l’état à ***en cours d’exécution***.
   * Initialise les paramètres (y compris `this`) du bloc itérateur aux valeurs d’argument et de la valeur d’instance enregistré lors de l’initialisation de l’objet énumérateur.
   * Exécute le bloc itérateur à partir du début jusqu'à ce que l’exécution est interrompue (comme décrit ci-dessous).
*  Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat de l’appel `MoveNext` n’est pas spécifié.
*  Si l’état de l’objet énumérateur est ***suspendu***, l’appel `MoveNext`:
   * Modifie l’état à ***en cours d’exécution***.
   * Restaure les valeurs de toutes les variables et paramètres locaux (y compris ce) pour les valeurs enregistrées lors de l’exécution du bloc itérateur a été suspendue en dernier. Notez que le contenu de tous les objets référencés par ces variables peut avoir changé depuis le précédent appel de MoveNext suivant.
   * Reprend l’exécution du bloc itérateur qui suit immédiatement la `yield return` instruction qui a provoqué la suspension de l’exécution et continue jusqu'à ce que l’exécution est interrompue (comme décrit ci-dessous).
*  Si l’état de l’objet énumérateur est ***après***, l’appel `MoveNext` retourne `false`.


Lorsque `MoveNext` exécute le bloc itérateur, l’exécution peut être interrompue de quatre manières : Par un `yield return` instruction, par un `yield break` instruction, en utilisant la fin du bloc itérateur et par une exception est levée et propagée hors du bloc itérateur.

*  Quand un `yield return` est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :
   * L’expression donnée dans l’instruction est évaluée, implicitement convertie vers le type yield et assignée à la `Current` propriété de l’objet énumérateur.
   * L’exécution du corps de l’itérateur est suspendue. Les valeurs de toutes les variables et paramètres locaux (y compris `this`) sont enregistrées, en l’état de l’emplacement de ce `yield return` instruction. Si le `yield return` instruction se trouve dans un ou plusieurs `try` bloque associé `finally` blocs ne sont pas exécutées pour l’instant.
   * L’état de l’objet énumérateur est passé à ***suspendu***.
   * Le `MoveNext` retourne de la méthode `true` à son appelant, indiquant que l’itération a réussi à avancer à la valeur suivante.
*  Quand un `yield break` est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :
   * Si le `yield break` instruction se trouve dans un ou plusieurs `try` bloque associé `finally` blocs sont exécutés.
   * L’état de l’objet énumérateur est passé à ***après***.
   * Le `MoveNext` retourne de la méthode `false` à son appelant, indiquant que l’itération est terminée.
*  Lorsque l’itérateur du corps de la fin :
   * L’état de l’objet énumérateur est passé à ***après***.
   * Le `MoveNext` retourne de la méthode `false` à son appelant, indiquant que l’itération est terminée.
*  Quand une exception est levée et propagée hors du bloc itérateur :
   * Appropriée `finally` blocs dans le corps de l’itérateur aura été exécutés par la propagation des exceptions.
   * L’état de l’objet énumérateur est passé à ***après***.
   * La propagation de l’exception continue à l’appelant de la `MoveNext` (méthode).

#### <a name="the-current-property"></a>La propriété actuelle

Objet énumérateur `Current` propriété est affectée par `yield return` instructions dans le bloc itérateur.

Quand un objet énumérateur est dans le ***suspendu*** d’état, la valeur de `Current` est la valeur définie par l’appel précédent à `MoveNext`. Quand un objet énumérateur est dans le ***avant***, ***en cours d’exécution***, ou ***après*** indique, le résultat de l’accès à `Current` n’est pas spécifié.

Pour un itérateur avec une instruction yield type autre que `object`, le résultat de l’accès à `Current` via l’objet énumérateur `IEnumerable` implémentation correspond à l’accès aux `Current` via l’objet énumérateur `IEnumerator<T>` implémentation et la conversion du résultat en `object`.

#### <a name="the-dispose-method"></a>La méthode Dispose

Le `Dispose` méthode est utilisée pour nettoyer l’itération en mettant l’objet énumérateur le ***après*** état.

*  Si l’état de l’objet énumérateur est ***avant***, l’appel `Dispose` modifie l’état à ***après***.
*  Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat de l’appel `Dispose` n’est pas spécifié.
*  Si l’état de l’objet énumérateur est ***suspendu***, l’appel `Dispose`:
   * Modifie l’état à ***en cours d’exécution***.
   * Exécute un enfin blocs comme si la dernière `yield return` instruction ont été une `yield break` instruction. Si cela provoque une exception levée et propagé hors du corps de l’itérateur, l’état de l’objet énumérateur est défini sur ***après*** et l’exception est propagée à l’appelant de la `Dispose` (méthode).
   * Modifie l’état à ***après***.
*  Si l’état de l’objet énumérateur est ***après***, l’appel `Dispose` n’a aucun effet.

### <a name="enumerable-objects"></a>Objets énumérables

Lorsqu’une fonction membre retournant un type énumérable interface est implémentée à l’aide d’un bloc itérateur, appel de la fonction membre ne s’exécutera pas immédiatement le code dans le bloc itérateur. Au lieu de cela, une ***objet énumérable*** est créé et retourné. L’objet énumérable `GetEnumerator` méthode retourne un objet énumérateur qui encapsule le code spécifié dans le bloc itérateur, et l’exécution du code dans le bloc itérateur se produit lorsque l’objet énumérateur `MoveNext` méthode est appelée. Un objet énumérable présente les caractéristiques suivantes :

*  Il implémente `IEnumerable` et `IEnumerable<T>`, où `T` est le type de rendement de l’itérateur.
*  Il est initialisé avec une copie des valeurs d’argument (le cas échéant) et la valeur de l’instance passée à la fonction membre.

Un objet énumérable est généralement une instance d’une classe générée par le compilateur énumérable qui encapsule le code dans le bloc itérateur et implémente les interfaces énumérables, mais les autres méthodes d’implémentation sont possibles. Si une classe enumerable est générée par le compilateur, cette classe doit être imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, il aura accès privé, et il aura un nom réservé pour un usage du compilateur ([identificateurs ](lexical-structure.md#identifiers)).

Un objet énumérable peut implémenter plusieurs interfaces que celles spécifiées ci-dessus. En particulier, un objet énumérable peut également implémenter `IEnumerator` et `IEnumerator<T>`, ce qui lui permet de servir un énumérable et un énumérateur. Dans ce type d’implémentation, la première fois d’un objet énumérable `GetEnumerator` méthode est appelée, l’objet énumérable lui-même est retourné. Les appels suivants de l’objet énumérable `GetEnumerator`, le cas échéant, renvoyer une copie de l’objet énumérable. Par conséquent, chaque retournée énumérateur a son propre état et les modifications dans un énumérateur n’affectent pas une autre.

#### <a name="the-getenumerator-method"></a>La méthode GetEnumerator

Un objet énumérable fournit une implémentation de la `GetEnumerator` méthodes de la `IEnumerable` et `IEnumerable<T>` interfaces. Les deux `GetEnumerator` méthodes partagent une implémentation commune qui acquiert et retourne un objet énumérateur disponibles. L’objet énumérateur est initialisé avec les valeurs d’argument et la valeur enregistrée lors de l’objet énumérable a été initialisé, mais sinon les fonctions d’objet énumérateur, comme décrit dans l’instance [objets d’énumérateur](classes.md#enumerator-objects).

### <a name="implementation-example"></a>Exemple d’implémentation

Cette section décrit une implémentation possible des itérateurs en termes de constructions c# standards. L’implémentation décrite ici est basée sur les mêmes principes que celui utilisés par le compilateur Microsoft c#, mais il n’est pas une implémentation autorisée ou le seul possible.

Ce qui suit `Stack<T>` la classe implémente son `GetEnumerator` méthode à l’aide d’un itérateur. L’itérateur énumère les éléments de la pile de haut en bas.

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

Le `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur généré par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

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

Dans la traduction précédente, le code dans le bloc itérateur est activé dans une machine à états et placé dans le `MoveNext` méthode de la classe d’énumérateur. En outre, la variable locale `i` se transforme en un champ dans l’objet énumérateur, donc il peut continuer à exister entre les appels de `MoveNext`.

L’exemple suivant imprime une simple table de multiplication des entiers entre 1 et 10. Le `FromTo` méthode dans l’exemple retourne un objet énumérable et est implémenté à l’aide d’un itérateur.

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

Le `FromTo` méthode peut être traduite en une instanciation d’une classe générée par le compilateur énumérable qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

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

La classe enumerable implémente les interfaces énumérables et les interfaces d’énumérateur, ce qui lui permet de servir un énumérable et un énumérateur. La première fois le `GetEnumerator` méthode est appelée, l’objet énumérable lui-même est retourné. Les appels suivants de l’objet énumérable `GetEnumerator`, le cas échéant, renvoyer une copie de l’objet énumérable. Par conséquent, chaque retournée énumérateur a son propre état et les modifications dans un énumérateur n’affectent pas une autre. Le `Interlocked.CompareExchange` méthode est utilisée pour garantir un fonctionnement thread-safe.

Le `from` et `to` paramètres sont transformées en champs dans la classe enumerable. Étant donné que `from` est modifié dans le bloc itérateur, une autre `__from` champ est introduit pour contenir la valeur initiale donnée `from` dans chaque énumérateur.

Le `MoveNext` méthode lève un `InvalidOperationException` si elle est appelée quand `__state` est `0`. Cela protège contre l’utilisation de l’objet énumérable en tant qu’un objet énumérateur sans préalablement appeler `GetEnumerator`.

L’exemple suivant montre une classe simple d’arborescence. Le `Tree<T>` la classe implémente son `GetEnumerator` méthode à l’aide d’un itérateur. L’itérateur énumère les éléments de l’arborescence par ordre infix.

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

Le `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur généré par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.

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

Les objets de temporaires générés par le compilateur utilisés dans le `foreach` instructions sont levées dans le `__left` et `__right` champs de l’objet énumérateur. Le `__state` de l’objet énumérateur est soigneusement mis à jour afin que le bon `Dispose()` méthode sera appelée correctement si une exception est levée. Notez qu’il n’est pas possible d’écrire le code traduit avec simple `foreach` instructions.

## <a name="async-functions"></a>Fonctions asynchrones

Une méthode ([méthodes](classes.md#methods)) ou une fonction anonyme ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)) avec le `async` modificateur est appelé un ***fonction async***. En règle générale, le terme ***async*** sert à décrire tout type de fonction qui a le `async` modificateur.

Il s’agit d’une erreur lors de la compilation pour obtenir la liste de paramètres formels d’une fonction async pour spécifier des `ref` ou `out` paramètres.

Le *return_type* d’un async méthode doit être `void` ou un ***type de tâche***. Les types de tâches sont `System.Threading.Tasks.Task` et types construits à partir de `System.Threading.Tasks.Task<T>`. Par souci de concision, dans ce chapitre ces types sont référencés en tant que `Task` et `Task<T>`, respectivement. Une méthode async retournant un type de tâche est dite retournant des tâches.

La définition exacte des types de tâche de l’implémentation est définie, mais du point de vue de la langue un type de tâche est dans un état incomplet, a réussi ou a généré une erreur. Une tâche ayant échoué enregistre une exception pertinente. Une réussite `Task<T>` enregistre un résultat de type `T`. Types de tâches sont await et peuvent donc être les opérandes d’expressions await ([expressions Await](expressions.md#await-expressions)).

Un appel de fonction async a la possibilité de suspendre l’évaluation au moyen d’expressions await ([expressions Await](expressions.md#await-expressions)) dans son corps. Version d’évaluation peut être reprise plus tard au moment de la suspension await expression au moyen d’un ***délégué de reprise***. Le délégué de reprise est de type `System.Action`, et lorsqu’il est appelé, l’évaluation de l’appel de fonction async reprend à partir de l’expression await où elle s’était arrêtée. Le ***appelant actuel*** d’une fonction async appel est l’appelant d’origine si l’appel de fonction n’a jamais été suspendu ou l’appelant plus récente du délégué de reprise dans le cas contraire.

### <a name="evaluation-of-a-task-returning-async-function"></a>Évaluation d’une fonction async retournant des tâches

Appel d’une fonction async retournant des tâches provoque une instance du type de tâche retournée doit être généré. Il s’agit du ***retourner task*** de la fonction async. La tâche est initialement dans un état incomplet.

Le corps de la fonction async est ensuite évalué jusqu'à ce qu’il est soit suspendu (par atteindre une expression await) ou se termine, auquel point de contrôle est retourné à l’appelant, ainsi que la tâche de retournée.

Quand le corps de la fonction asynchrone se termine, la tâche retournée est déplacée hors de l’état incomplète :

*  Si le corps de fonction se termine à la suite d’atteindre une instruction return ou à la fin du corps, n’importe quelle valeur de résultat est enregistrée dans la tâche de retournée, qui est placée dans un état de réussite.
*  Si le corps de fonction se termine à la suite d’une exception non interceptée ([l’instruction throw](statements.md#the-throw-statement)) l’exception est enregistrée dans la tâche de retournée qui est placée dans un état d’erreur.

### <a name="evaluation-of-a-void-returning-async-function"></a>Évaluation d’une fonction async qui retournent void

Si le type de retour de la fonction async est `void`, évaluation diffère de la méthode ci-dessus de la façon suivante : Parce qu’aucune tâche n’est renvoyée, la fonction communique au lieu de cela la saisie semi-automatique et des exceptions pour le thread actuel ***contexte de synchronisation***. La définition exacte de contexte de synchronisation dépend de l’implémentation, mais est une représentation sous forme de « where » le thread actuel est en cours d’exécution. Le contexte de synchronisation est averti lors de l’évaluation d’une fonction async qui retournent void commence, se termine correctement ou provoque la levée d’une exception non interceptée.

Ainsi, le contexte de savoir combien les fonctions asynchrones qui retournent void sont en cours d’exécution dans cette section et décider comment propager les exceptions provenant de leur.
