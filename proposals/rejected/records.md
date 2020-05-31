---
ms.openlocfilehash: 49720d62c496ff904eacacb31ae29ef97a5aefaa
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84228217"
---
# <a name="records"></a>enregistrements

* [x] proposé
* [] Prototype : [terminé](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)
* [] Implémentation : [en cours](https://github.com/dotnet/roslyn/BRANCH_NAME)
* [] Spécification : spécification de brouillons encadrée

## <a name="summary"></a>Résumé
[summary]: #summary

Les enregistrements sont une nouvelle forme de déclaration simplifiée pour les types de classe et de struct C# qui combinent les avantages d’un certain nombre de fonctionnalités plus simples. Nous décrivons les nouvelles fonctionnalités (paramètres du récepteur de l’appelant et *with-expression*s), indiquons la syntaxe et la sémantique des déclarations d’enregistrement, puis fournissons des exemples.


## <a name="motivation"></a>Motivation
[motivation]: #motivation

Un nombre significatif de déclarations de type en C# n’est guère plus que les collections d’agrégats de données typées. Malheureusement, la déclaration de tels types nécessite une grande quantité de code réutilisable. Les *enregistrements* fournissent un mécanisme permettant de déclarer un type de données en décrivant les membres de l’agrégat ainsi que le code supplémentaire ou les écarts par rapport à l’usage habituel, le cas échéant.

Consultez les [exemples](#examples)ci-dessous.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

### <a name="caller-receiver-parameters"></a>paramètres Caller-Receiver

Actuellement *, l’argument par défaut* d’un paramètre de méthode doit être 
- *expression constante*; ni
- expression de la forme `new S()` où `S` est un type valeur ; ou
- expression de la forme `default(S)` où `S` est un type valeur

Nous étendons ceci pour ajouter les éléments suivants :
- une expression de la forme`this.Identifier`

Ce nouveau formulaire est appelé *argument par défaut du récepteur*de l’appelant et n’est autorisé que si toutes les conditions suivantes sont satisfaites
- La méthode dans laquelle il apparaît est une méthode d’instance ; les
- L’expression `this.Identifier` est liée à un membre d’instance du type englobant, qui doit être un champ ou une propriété ; et
- Le membre auquel il est lié (et l' `get` accesseur, s’il s’agit d’une propriété) est au moins aussi accessible que la méthode ; et
- Le type de `this.Identifier` est implicitement convertible à l’aide d’une conversion d’identité ou de valeurs NULL dans le type du paramètre (il s’agit d’une contrainte existante sur un *argument par défaut*).

Quand un argument est omis d’un appel d’un membre de fonction pour un paramètre facultatif correspondant avec un *argument par défaut de récepteur appelant*, la valeur du membre du destinataire est transmise de manière implicite. 

> **Design Notes**: la raison principale du paramètre Caller-Receiver est de prendre en charge l' *expression with*. L’idée est que vous pouvez déclarer une méthode comme celle-ci
> ```cs
> class Point
> {
>     public readonly int X;
>     public readonly int Y;
>     public Point With(int x = this.X, int y = this.Y) => new Point(x, y);
>     // etc
> }
> ```
> puis l’utiliser comme suit
> ```cs
>     Point p = new Point(3, 4);
>     p = p.With(x: 1);
> ```
> Pour créer un nouveau `Point` comme un existant `Point` , mais avec la valeur `X` modifiée.
> 
> Il s’agit d’une question ouverte que la forme syntaxique de l' *expression with-expression* mérite d’être ajoutée une fois que nous aurons pris en charge les paramètres du récepteur de l’appelant. il est donc possible de le faire *au lieu de* plutôt qu' *en plus* de l' *expression with*.

- [] **Problème d’ouverture**: quel est l’ordre dans lequel un *argument par défaut du récepteur appelant* est évalué par rapport à d’autres arguments ? Dois-je indiquer qu’il n’est pas spécifié ?

### <a name="with-expressions"></a>expressions with

Un nouveau formulaire d’expression est proposé :

```antlr
primary_expression
    : with_expression
    ;

with_expression
    : primary_expression 'with' '{' with_initializer_list '}'
    ;

with_initializer_list
    : with_initializer
    | with_initializer ',' with_initializer_list
    ;

with_initializer
    : identifier '=' expression
    ;
```

Le jeton `with` est un nouveau mot clé contextuel.

Chaque *identificateur* à gauche d’un *with_initializer* doit être lié à un champ ou une propriété d’instance accessible du type de la *primary_expression* du *with_expression*. Il se peut qu’il n’y ait pas de nom dupliqué parmi ces identificateurs d’un *with_expression*donné.

*With_expression* de la forme

> *E1* `with` `{` *identificateur*  =  *E2*,...`}`

est traité comme un appel de la forme

> *E1* `.With(` *identificateur2* `:` *E2*,...`)`

Où, pour chaque méthode nommée `With` qui est un membre d’instance accessible de *E1*, nous sélectionnons *identificateur2* comme nom du premier paramètre de cette méthode qui a un paramètre de récepteur appelant qui est le même membre que le champ d’instance ou la propriété liée à l' *identificateur*. Si aucun paramètre de ce type ne peut être identifié, la méthode n’est pas prise en compte. La méthode à appeler est sélectionnée parmi les candidats restants par la résolution de surcharge.

> **Remarques**sur la conception : les paramètres de récepteur de l’appelant sont fournis, un grand nombre des avantages de l' *expression with-expression* sont disponibles sans cette forme de syntaxe spéciale. Nous envisageons donc si cela est nécessaire. Son principal avantage est de permettre à de programmer en termes de noms de champs et de propriétés, plutôt qu’en termes de noms de paramètres. De cette façon, nous améliorons la lisibilité et la qualité des outils (par exemple, Go-to-Definition sur l’identificateur d’un *with_expression* naviguerait vers la propriété plutôt que vers un paramètre de méthode).

- [] **Problème d’ouverture**: cette description doit être modifiée pour prendre en charge les méthodes d’extension.

### <a name="pattern-matching"></a>correspondance au modèle

Consultez la [spécification de critères spéciaux](csharp-8.0/patterns.md#positional-pattern) pour une spécification de `Deconstruct` et sa relation à la mise en correspondance de modèle.

> **Remarques**sur la conception : en vertu du compilateur, `Deconstruct` comme indiqué dans le présent document, et de la spécification de la mise en correspondance des modèles, une déclaration d’enregistrement
> ```cs
> public class Point(int X, int Y);
> ```
> prend en charge la mise en correspondance des modèles positionnels comme suit
> ```cs
> Point p = new Point(3, 4);
> if (p is Point(3, var y)) { // if X is 3
>     Console.WriteLine(y);   // print Y
> }
> ```

### <a name="record-type-declarations"></a>déclarations de type d’enregistrement

La syntaxe d’une `class` `struct` déclaration ou est étendue pour prendre en charge les paramètres de valeur ; les paramètres deviennent des propriétés du type :

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      record_parameters? record_class_base? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      record_parameters? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

record_class_base
    : class_type record_base_arguments?
    | interface_type_list
    | class_type record_base_arguments? ',' interface_type_list
    ;

record_base_arguments
    : '(' argument_list? ')'
    ;

record_parameters
    : '(' record_parameter_list? ')'
    ;

record_parameter_list
    : record_parameter
    | record_parameter ',' record_parameter_list
    ;

record_parameter
    : attributes? type identifier record_property_name? default_argument?
    ;

record_property_name
    : ':' identifier
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

> **Design Notes**: étant donné que les types d’enregistrements sont souvent utiles sans qu’aucun membre n’ait besoin d’être explicitement déclaré dans un corps de classe, nous modifions la syntaxe de la déclaration pour permettre à un corps d’être simplement un point-virgule.

Une classe (struct) déclarée avec *Record-Parameters* est appelée *classe d’enregistrement* (struct d'*enregistrement*), soit un *type d’enregistrement*.

- [] **Problème d’ouverture**: nous devons inclure *primary_constructor_body* dans la grammaire pour qu’elle puisse apparaître dans une déclaration de type d’enregistrement.
- [] **Problème d’ouverture**: Quelles sont les règles de conflit de noms pour les noms de paramètres ? Vraisemblablement, il n’est pas possible d’entrer en conflit avec un paramètre de type ou un autre *paramètre d’enregistrement*.
- [] **Problème d’ouverture**: nous devons spécifier la portée des paramètres d’enregistrement. Où peut-il être utilisé ? Vraisemblablement dans les initialiseurs de champ d’instance et *primary_constructor_body* au moins.
- [] **Problème d’ouverture**: une déclaration de type d’enregistrement peut-elle être partielle ? Dans ce cas, les paramètres doivent-ils être répétés sur chaque composant ?

#### <a name="members-of-a-record-type"></a>Membres d’un type d’enregistrement

En plus des membres déclarés dans le *corps*de la classe, un type d’enregistrement a les membres supplémentaires suivants :

##### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un `public` constructeur dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce que l’on appelle le *constructeur principal* pour le type et entraîne la suppression du *constructeur par défaut* déclaré implicitement.

Au moment de l’exécution, le constructeur principal

* Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur ; [voir 1.1.2](#1.1.2)); Cliquez
* exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la *classe*; Et puis
* appelle un constructeur de classe de base :
    * Si le *record_base_arguments*contient des arguments, un constructeur de base sélectionné par la résolution de surcharge avec ces arguments est appelé ;
    * Dans le cas contraire, un constructeur de base est appelé sans argument.
* exécute le corps de chaque *primary_constructor_body*, le cas échéant, dans l’ordre de la source.

- [] **Problème d’ouverture**: nous devons spécifier cet ordre, particulièrement entre les unités de compilation pour les éléments partiels.
- [] **Problème d’ouverture**: nous devons spécifier que chaque constructeur explicitement déclaré doit être chaîné au constructeur principal.
- [] **Problème d’ouverture**: doit-il être autorisé à modifier le modificateur d’accès sur le constructeur principal ?
- [] **Problème d’ouverture**: dans un struct d’enregistrement, il s’agit d’une erreur pour qu’il n’y ait pas de paramètres d’enregistrement ?

##### <a name="primary-constructor-body"></a>Corps du constructeur principal

```antlr
primary_constructor_body
    : attributes? constructor_modifiers? identifier block
    ;
```

Un *primary_constructor_body* peut uniquement être utilisé dans une déclaration de type d’enregistrement. L' *identificateur* d’un *primary_constructor_body* doit nommer le type d’enregistrement dans lequel il est déclaré.

Le *primary_constructor_body* ne déclare pas un membre seul, mais est un moyen pour le programmeur de fournir des attributs pour et de spécifier l’accès de, le constructeur principal d’un type d’enregistrement. Elle permet également au programmeur de fournir du code supplémentaire qui est exécuté lorsqu’une instance du type d’enregistrement est construite.

- [] **Problème d’ouverture**: nous devrions noter qu’un constructeur par défaut de struct ignore cela.
- [] **Problème d’ouverture**: nous devons spécifier l’ordre d’exécution de l’initialisation.
- [] **Problème d’ouverture**: devons-nous autoriser un *primary_constructor_body* (vraisemblablement sans attributs et modificateurs) dans une déclaration de type non-enregistrement et le considérer comme le code d’un initialiseur de champ d’instance ?

##### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un `public` membre de propriété correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur. Son nom est l' *identificateur* de l' *record_property_name*, s’il est présent, ou l' *identificateur* du *record_parameter* dans le cas contraire. Si aucune propriété publique concrète (c’est-à-dire non abstraite) avec un `get` accesseur et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :

* Pour un *struct d’enregistrement* ou une `sealed` *classe d’enregistrement*:
 * Un `private` `readonly` champ est produit sous la forme d’un champ de stockage pour une `readonly` propriété. Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.
 * L’accesseur de la propriété `get` est implémenté pour retourner la valeur du champ de stockage.
 * Chaque accesseur de propriété virtuelle héritée « correspondant » `get` est substitué.

> **Design Notes**: en d’autres termes, si vous étendez une classe de base ou implémentez une interface qui déclare une propriété abstraite publique avec le même nom et le même type qu’un paramètre d’enregistrement, cette propriété est substituée ou implémentée.

- [] **Problème d’ouverture**: est-il possible de modifier le modificateur d’accès d’une propriété lorsqu’elle est déclarée explicitement ?
- [] **Problème d’ouverture**: est-il possible de remplacer un champ pour une propriété ?

##### <a name="object-methods"></a>Méthodes d'objets

Pour un *struct d’enregistrement* ou une `sealed` *classe d’enregistrement*, les implémentations des méthodes `object.GetHashCode()` et `object.Equals(object)` sont produits par le compilateur, sauf s’ils sont fournis par l’utilisateur.

- [] **Problème d’ouverture**: nous devons spécifier précisément leur implémentation.
- [] **Problème d’ouverture**: nous devons également ajouter l’interface `IEquatable<T>` pour le type d’enregistrement et spécifier que les implémentations sont fournies.
- [] **Problème d’ouverture**: nous devons également spécifier que nous implémentons tous les `IEquatable<T>.Equals` .
- [] **Problème d’ouverture**: nous devons spécifier précisément comment nous résolvons le problème des valeurs égales en termes d’héritage d’enregistrement : plus précisément comment nous générons des méthodes d’égalité de manière à ce qu’elles soient symétriques, transitives, réflexes, etc.
- [] **Problème d’ouverture**: il a été proposé de mettre en œuvre `operator ==` et `operator !=` pour les types d’enregistrements.
- [] **Problème d’ouverture**: dois-je générer automatiquement une implémentation de `object.ToString` ?

##### `Deconstruct`

Un type d’enregistrement a une méthode générée `public` `void Deconstruct` par le compilateur, sauf si une signature est fournie par l’utilisateur. Chaque paramètre est un `out` paramètre du même nom et du même type que le paramètre correspondant du type d’enregistrement. L’implémentation fournie par le compilateur de cette méthode doit assigner `out` à chaque paramètre la valeur de la propriété correspondante.

Consultez [la spécification de critères spéciaux](csharp-8.0/patterns.md#positional-pattern) pour la sémantique de `Deconstruct` .

##### <a name="with-method"></a>Méthode `With`

À moins qu’un membre déclaré par l’utilisateur ne soit nommé `With` déclaré, un type d’enregistrement a une méthode fournie par le compilateur nommée `With` dont le type de retour est le type d’enregistrement lui-même et qui contient un paramètre de valeur correspondant à chaque *paramètre d’enregistrement* dans le même ordre que ces paramètres apparaissent dans la déclaration de type d’enregistrement. Chaque paramètre doit avoir un *argument par défaut du récepteur* de l’appelant de la propriété correspondante.

Dans une `abstract` classe d’enregistrement, la méthode fournie par le compilateur `With` est abstraite. Dans un struct d’enregistrement ou une classe d’enregistrement sealed, la méthode fournie par le compilateur `With` est `sealed` . Sinon, la méthode fournie `With` par le compilateur est’Virtual et son implémentation retourne une nouvelle instance produite en appelant le constructeur principal avec les paramètres comme arguments pour créer une nouvelle instance à partir des paramètres et retourner cette nouvelle instance.

- [] **Problème d’ouverture**: nous devons également spécifier dans quelles conditions nous remplaçons ou implémentons `With` des méthodes ou des méthodes virtuelles héritées `With` à partir d’interfaces implémentées.
- [] **Problème d’ouverture**: nous devrions indiquer ce qui se passe lorsque nous héritons d’une méthode non virtuelle `With` .

> **Design Notes**: étant donné que les types d’enregistrements sont par défaut immuables, la `With` méthode permet de créer une nouvelle instance qui est la même qu’une instance existante, mais avec les propriétés sélectionnées en fonction de nouvelles valeurs. Par exemple, donné
> ```cs
> public class Point(int X, int Y);
> ```
> Il existe un membre fourni par le compilateur
> ```cs
>     public virtual Point With(int X = this.X, int Y = this.Y) => new Point(X, Y);
> ```
> Qui active une variable du type d’enregistrement
> ```cs
> var p = new Point(3, 4);
> ```
> à remplacer par une instance qui a une ou plusieurs propriétés différentes
> ```cs
>     p = p.With(X: 5);
> ```
> Cela peut également être exprimé à l’aide de la *with_expression*:
> ```cs
>     p = p with { X = 5 };
> ```

### <a name="examples"></a>Exemples

#### <a name="compatibility-of-record-types"></a>Compatibilité des types d’enregistrements

Étant donné que le programmeur peut ajouter des membres à une déclaration de type d’enregistrement, il est souvent possible de modifier le jeu d’éléments d’enregistrement sans affecter les clients existants. Par exemple, en fonction d’une version initiale d’un type d’enregistrement

```cs
// v1
public class Person(string Name, DateTime DateOfBirth);
```

Un nouvel élément du type d’enregistrement peut être compatible avec la prochaine révision du type sans affecter la compatibilité binaire ou source :

```cs
// v2
public class Person(string Name, DateTime DateOfBirth, string HomeTown)
{
    // Note: below operations added to retain binary compatibility with v1
    public Person(string Name, DateTime DateOfBirth) : this(Name, DateOfBirth, string.Empty) {}
    public static void operator is(Person self, out string Name, out DateTime DateOfBirth)
        { Name = self.Name; DateOfBirth = self.DateOfBirth; }
    public Person With(string Name, DateTime DateOfBirth) => new Person(Name, DateOfBirth);
}
```

#### <a name="record-struct-example"></a>exemple d’enregistrement d’un struct

Ce struct d’enregistrement

```cs
public struct Pair(object First, object Second);
```

est traduit dans ce code

```cs
public struct Pair : IEquatable<Pair>
{
    public object First { get; }
    public object Second { get; }
    public Pair(object First, object Second)
    {
        this.First = First;
        this.Second = Second;
    }
    public bool Equals(Pair other) // for IEquatable<Pair>
    {
        return Equals(First, other.First) && Equals(Second, other.Second);
    }
    public override bool Equals(object other)
    {
        return (other as Pair)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (First?.GetHashCode()*17 + Second?.GetHashCode()).GetValueOrDefault();
    }
    public Pair With(object First = this.First, object Second = this.Second) => new Pair(First, Second);
    public void Deconstruct(out object First, out object Second)
    {
        First = self.First;
        Second = self.Second;
    }
}
```

- [] **Problème d’ouverture**: l’implémentation de Equals (paire autre) doit-elle être un membre public de pair ?
- [] **Problème d’ouverture**: cette implémentation de `Equals` n’est pas symétrique dans le cas de l’héritage.
- [] **Problème d’ouverture**: un enregistrement doit-il être déclaré `operator ==` et `operator !=` ?

> **Design Notes**: étant donné qu’un seul type d’enregistrement peut hériter d’un autre, et que cette implémentation de n’est `Equals` pas symétrique dans ce cas, il n’est pas correct. Nous proposons de mettre en œuvre l’égalité de la manière suivante :
> ```cs
>     public bool Equals(Pair other) // for IEquatable<Pair>
>     {
>         return other != null && EqualityContract == other.EqualityContract &&
>             Equals(First, other.First) && Equals(Second, other.Second);
>     }
>     protected virtual Type EqualityContract => typeof(Pair);
> ```
> Les enregistrements dérivés le seraient `override EqualityContract` . L’alternative la moins attrayante est de restreindre l’héritage.

#### <a name="sealed-record-example"></a>exemple d’enregistrement sealed

Cette classe d’enregistrement sealed

```cs
public sealed class Student(string Name, decimal Gpa);
```

est traduit dans ce code

```cs
public sealed class Student : IEquatable<Student>
{
    public string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public bool Equals(Student other) // for IEquatable<Student>
    {
        return other != null && Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public override bool Equals(object other)
    {
        return this.Equals(other as Student);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa?.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public void Deconstruct(out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

#### <a name="abstract-record-class-example"></a>exemple de classe d’enregistrement abstraite

Cette classe d’enregistrement abstraite

```cs
public abstract class Person(string Name);
```

est traduit dans ce code

```cs
public abstract class Person : IEquatable<Person>
{
    public string Name { get; }
    public Person(string Name)
    {
        this.Name = Name;
    }
    public bool Equals(Person other)
    {
        return other != null && Equals(Name, other.Name);
    }
    public override bool Equals(object other)
    {
        return Equals(other as Person);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()).GetValueOrDefault();
    }
    public abstract Person With(string Name = this.Name);
    public void Deconstruct(Person self, out string Name)
    {
        Name = self.Name;
    }
}
```

#### <a name="combining-abstract-and-sealed-records"></a>combinaison d’enregistrements abstraits et sealed

À partir de la classe d’enregistrement abstraite `Person` ci-dessus, cette classe d’enregistrement sealed

```cs
public sealed class Student(string Name, decimal Gpa) : Person(Name);
```

est traduit dans ce code

```cs
public sealed class Student : Person, IEquatable<Student>
{
    public override string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa) : base(Name)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public override bool Equals(Student other) // for IEquatable<Student>
    {
        return Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public bool Equals(Person other) // for IEquatable<Person>
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override bool Equals(object other)
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public override Person With(string Name = this.Name) => new Student(Name, Gpa);
    public void Deconstruct(Student self, out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le corps des programmes C# qui tireraient parti de la fonctionnalité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Nous avons envisagé d’ajouter des *constructeurs principaux* en C# 6. Bien qu’ils occupent la même surface syntaxique que cette proposition, nous avons constaté qu’ils ne relevaient pas des avantages offerts par les enregistrements.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Les questions ouvertes s’affichent dans le corps de la proposition.

## <a name="design-meetings"></a>Réunions de conception

TBD
