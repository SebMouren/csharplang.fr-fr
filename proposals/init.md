---
ms.openlocfilehash: f50299739321818a4877f593ee715f35540132b0
ms.sourcegitcommit: e006b4808d8c107dad2935348b57d51edbfaf9a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82820162"
---
<a name="init-only-setters"></a>Les Setters init uniquement
=====

## <a name="summary"></a>Résumé
Cette proposition ajoute le concept de propriétés et d’indexeurs init uniquement à C#. Ces indexeurs peuvent être définis au moment de la création de l’objet, mais ne `get` deviennent effectifs qu’une fois la création de l’objet terminée.
Cela permet un modèle immuable bien plus flexible en C#. 

## <a name="motivation"></a>Motivation
Les mécanismes sous-jacents pour la génération de données immuables en C# n’ont pas changé depuis 1,0. Elles sont conservées :

1. Déclaration de champs comme `readonly`.
1. Déclarer des propriétés qui contiennent uniquement un `get` accesseur.

Ces mécanismes sont efficaces pour permettre la construction de données immuables, mais ils le font en ajoutant des coûts au code réutilisable de types et en choisissant ces types en dehors des fonctionnalités telles que les initialiseurs d’objets et de collections. Cela signifie que les développeurs doivent choisir entre facilité d’utilisation et immuabilité.

Un objet immuable simple comme `Point` nécessite deux fois plus de code de plaque de chaudière pour prendre en charge la construction, comme c’est le cas pour déclarer le type. Plus le type est grand, plus le coût de cette plaque de chaudière est important :

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int X, int Y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

L' `init` accesseur rend les objets immuables plus flexibles en permettant à l’appelant de muter les membres pendant l’acte de construction. Cela signifie que les propriétés immuables de l’objet peuvent participer à des initialiseurs d’objets et, par conséquent, supprimer la nécessité d’utiliser tout le constructeur dans le type. Le `Point` type est désormais simplement :

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

Le consommateur peut ensuite utiliser des initialiseurs d’objet pour créer l’objet

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a>Conception détaillée

### <a name="init-accessors"></a>accesseurs init
Une propriété init only (ou indexeur) est déclarée à l' `init` aide de l’accesseur `set` à la place de l’accesseur :

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

Une propriété d’instance contenant `init` un accesseur est considérée comme définissable dans les circonstances suivantes :

- Au cours d’un initialiseur d’objet
- Au cours `with` d’un initialiseur d’expression
- À l’intérieur d’un constructeur d’instance du type conteneur ou `this` dérivé, sur ou`base`
- À l' `init` intérieur de l’accesseur d' `this` une propriété, sur ou`base`
- À l’intérieur des utilisations d’attributs avec des paramètres nommés

Les heures ci-dessus dans `init` lesquelles les accesseurs sont définissables sont collectivement appelées dans ce document en tant que phase de construction de l’objet.

Cela signifie que `Student` la classe peut être utilisée des manières suivantes :

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

Les règles autour desquelles `init` les accesseurs sont définissables s’étendent sur les hiérarchies de types. Si le membre est accessible et que l’objet est connu pour être dans la phase de construction, le membre est définissable. Cela autorise spécifiquement les éléments suivants :

```cs
class Base
{
    public bool Value { get; init; }
}

class Derived : Base
{
    Derived()
    {
        // Not allowed with get only properties but allowed with init
        Value = true;
    }
}

class Consumption
{
    void Example()
    {
        var d = new Derived() { Value = true; };
    }
}

```

Au point où un `init` accesseur est appelé, l’instance est connue pour être dans la phase de construction ouverte. Par conséquent `init` , un accesseur est autorisé à effectuer les actions suivantes en plus de `set` ce qu’un accesseur normal peut faire :

1. Appeler d' `init` autres accesseurs disponibles `this` via ou`base`
1. Assigner `readonly` des champs déclarés sur le même type via`this`

```cs
class Complex
{
    readonly int Field1;
    int Field2;
    int Prop1 { get; init ; }
    int Prop2
    {
        get => 42;
        init
        {
            Field1 = 13; // okay
            Field2 = 13; // okay
            Prop1 = 13; // okay
        }
    }
}
```

La possibilité d’assigner `readonly` des `init` champs à partir d’un accesseur est limitée aux champs déclarés sur le même type que l’accesseur. Elle ne peut pas être utilisée `readonly` pour assigner des champs dans un type de base. Cette règle garantit que les auteurs de type restent en contrôle sur le comportement de mutabilité de leur type. Les développeurs qui ne souhaitent pas utiliser `init` ne peuvent pas être affectés par d’autres types :

```cs
class Base
{
    internal readonly int Field;
    internal int Property
    {
        get => Field;
        init => Field = value; // Okay
    }

    internal int OtherProperty { get; init; }
}

class Derived : Base
{
    internal readonly int DerivedField;
    internal int DerivedProperty
    {
        get => DerivedField;
        init
        {
            DerivedField = 42;  // Okay
            Property = 0;       // Okay
            Field = 13;         // Error Field is readonly
        }
    }

    public Derived()
    {
        Property = 42;  // Okay 
        Field = 13;     // Error Field is readonly
    }
}
```

Lorsque `init` est utilisé dans une propriété virtuelle, toutes les substitutions doivent également être marquées comme `init`. De même, il n’est pas possible de remplacer `set` un `init`simple par.

```cs
class Base
{
    public virtual int Property { get; init; }
}

class C1 : Base
{
    public override int Property { get; init; }
}

class C2 : Base
{
    // Error: Property must have init to override Base.Property
    public override int Property { get; set; }
}
```

Une `interface` déclaration peut également participer à `init` l’initialisation de style à l’aide du modèle suivant :

```cs
interface IPerson
{
    string Name { get; init; }
}

class Init
{
    void M<T>() where T : IPerson, new()
    {
        var local = new T()
        {
            Name = "Jared"
        };
        local.Name = "Jraed"; // Error
    }
}
```

Restrictions de cette fonctionnalité :
- L' `init` accesseur ne peut être utilisé que sur les propriétés d’instance
- Une propriété ne peut pas contenir `init` à `set` la fois un accesseur and
- Toutes les substitutions d’une propriété doivent avoir `init` si la base avait `init`. Cette règle s’applique également à l’implémentation de l’interface.

### <a name="metadata-encoding"></a>Encodage des métadonnées 
Les `init` accesseurs de propriété sont émis en tant qu' `set` accesseur standard avec le type de retour marqué avec `IsExternalInit`un modreq de. Il s’agit d’un nouveau type qui aura la définition suivante :

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

Le compilateur mettra en correspondance le type par nom complet. Il n’est pas nécessaire qu’il s’affiche dans la bibliothèque principale. S’il existe plusieurs types portant ce nom, le compilateur lie l’arrêt dans l’ordre suivant :

1. Celui défini dans le projet en cours de compilation
1. Celui défini dans corelib

Si aucune de ces options n’existe, une erreur d’ambiguïté de type sera émise.

La conception de `IsExternalInit` est plus loin dans [ce numéro](https://github.com/dotnet/runtime/issues/34978)

## <a name="questions"></a>Questions

### <a name="breaking-changes"></a>Modifications avec rupture
L’un des principaux points du pivot dans la manière dont cette fonctionnalité est encodée va à la question suivante : 

> S’agit-il d’une modification avec `init` rupture `set`binaire à remplacer par ?

Le `init` remplacement `set` de par, ce qui rend une propriété entièrement accessible en écriture, n’est jamais une modification avec rupture source sur une propriété non virtuelle. Il développe simplement l’ensemble des scénarios dans lesquels la propriété peut être écrite. Le seul comportement en question est le fait qu’il reste ou non une modification avec rupture binaire.

Si vous souhaitez apporter la modification `init` à `set` une source et une modification compatible binaire, elle forcera notre main sur la décision modreq et sur les attributs ci-dessous, car elle va éliminer modreqs en tant que soulution. Si, en revanche, cela est considéré comme une opération non intéressante, cela rendra la décision modreq et d’attribut moins affectée.

**Résolution** Ce scénario n’est pas considéré comme intéressant par LDM.

### <a name="modreqs-vs-attributes"></a>Modreqs et attributs
La stratégie d’émission `init` pour les accesseurs de propriété doit choisir entre l’utilisation d’attributs ou de modreqs lors de l’émission pendant les métadonnées. Ils ont des compromis différents qui doivent être pris en compte.

L’annotation d’un accesseur Set de propriété à l’aide d’une déclaration modreq signifie que les compilateurs compatibles CLI ignoreront l’accesseur, sauf s’il comprend le modreq. Cela signifie que seuls les compilateurs reconnaissants de `init` lisent le membre. Les compilateurs qui ne sont `init` pas conscients de `set` ignorent l’accesseur et, par conséquent, ne traitent pas accidentellement la propriété comme étant en lecture/écriture. 

L’inconvénient de modreq est `init` de devenir une partie de la signature binaire de `set` l’accesseur. L’ajout ou `init` la suppression entraînera l’arrêt du compatbility binaire de l’application.

L’utilisation d’attributs pour annoter l' `set` accesseur signifie que seuls les compilateurs qui comprennent l’attribut sauront pouvoir limiter l’accès à celui-ci. Un compilateur qui n’est `init` pas conscient de le verra comme une simple propriété en lecture/écriture et autorisera l’accès.

Cela signifie apparemment que cette décision est un choix entre la sécurité supplémentaire au détriment de la compatibilité binaire. Le fait d’examiner un peu la sécurité supplémentaire n’est pas exactement ce qu’il semble. Elle ne protège pas contre les circonstances suivantes :

1. Réflexion sur `public` les membres
1. L’utilisation de`dynamic` 
1. Compilateurs qui ne reconnaissent pas modreqs

Il convient également de considérer que, lorsque nous effectuons les règles de vérification IL pour .NET `init` 5, est l’une de ces règles. Cela signifie qu’une mise en œuvre supplémentaire sera obtenue en vérifiant simplement les compilateurs émettant un IL vérifiable.

Les principaux langages pour .NET (C#, F # et VB) seront tous mis à jour pour `init` reconnaître ces accesseurs. Par conséquent, le seul scénario réaliste ici est lorsqu’un compilateur C# `init` 9 émet des propriétés et qu’ils sont visibles par un ensemble d’outils plus ancien, tel que C# 8, VB 15, etc. C# 8. C’est le compromis à prendre en compte et à peser sur la compatibilité binaire.

**Remarque** Cette discussion s’applique principalement aux membres uniquement, pas aux champs. Alors `init` que les champs ont été rejetés par LDM, ils sont toujours intéressants à prendre en compte pour la discussion modreq et d’attribut. La `init` fonctionnalité pour les champs est un assouplissement de la restriction existante `readonly`de. Cela signifie que si nous émettez les `readonly` champs sous la forme de + un attribut, il n’y a aucun risque que des compilateurs plus anciens ne `readonly`soient en utilisant le champ parce qu’ils se reconnaissent déjà. Par conséquent, l’utilisation d’un modreq ici n’ajoute pas de protection supplémentaire.

**Résolution** La fonctionnalité utilise un modreq pour encoder la `init` méthode setter de propriété. Les facteurs intéressants étaient (dans aucun ordre particulier) :

* Souhaitant dissuader les compilateurs plus anciens de `init` violer la sémantique
* Vous souhaitez ajouter ou supprimer `init` dans une `virtual` déclaration, ou `interface` à la fois une modification avec rupture source et binaire.

Étant donné qu’il n’y avait pas non `init` plus de prise en charge significative pour la suppression de en tant que modification compatible binaire, il a été possible de choisir d’utiliser modreq.

### <a name="init-vs-initonly"></a>init et initonly
Il existe trois formes de syntaxe qui ont été particulièrement étudiées pendant notre réunion LDM :

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

**Résolution** Il n’existait pas de syntaxe très difficilement favorisée dans LDM.

Une attention particulière était de savoir comment le choix de la syntaxe aurait un impact sur notre capacité `init` à faire des membres en tant que fonctionnalité générale dans le futur.
Le choix de l’option 1 signifie qu’il serait difficile de définir une propriété qui avait `init` une `get` méthode de style à l’avenir. Finalement, il a été décidé que, si nous avons décidé de `init` continuer avec les membres généraux à `init` l’avenir, nous pourrions autoriser à être un modificateur dans la liste des accesseurs `init set`de propriété, ainsi qu’une petite main pour. Fondamentalement, les deux déclarations suivantes sont identiques.

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

La décision a été prise de passer `init` en tant qu’accesseur autonome dans la liste des accesseurs de propriété.

### <a name="warn-on-failed-init"></a>Avertir en cas d’échec de l’initialisation
Considérons le scénario suivant. Un type déclare un `init` membre uniquement qui n’est pas défini dans le constructeur. Le code qui construit l’objet doit-il recevoir un avertissement s’il n’a pas pu initialiser la valeur ?

À ce stade, il est clair que le champ ne sera jamais défini et a donc beaucoup de similitudes avec l’avertissement autour de l’échec `private` de l’initialisation des données. Par conséquent, un avertissement aurait apparemment une valeur ici ?

Toutefois, cet avertissement présente des inconvénients importants :
1. Cela complique la modification `readonly` de la compatibilité avec `init`. 
1. Elle nécessite de fournir des métadonnées supplémentaires pour indiquer les membres qui doivent être initialisés par l’appelant.

En outre, si nous pensons qu’il y a de la valeur ici dans le scénario global de forcer les créateurs d’objets à être avertis/Errors sur des champs spécifiques, il est probable qu’il s’agit d’une fonctionnalité générale. Il n’y a aucune raison d’être limité à `init` des membres uniquement.

**Résolution** Il n’y aura aucun avertissement sur la `init` consommation des champs et des propriétés.

LDM souhaite avoir une discussion plus large sur l’idée des champs et propriétés requis. Cela peut nous amener à revenir en arrière et à reconsidérer notre `init` position sur les membres et la validation.

## <a name="allow-init-as-a-field-modifier"></a>Autoriser init en tant que modificateur de champ
De la même façon `init` peut servir d’accesseur de propriété, il peut également servir de désignation sur les champs pour leur attribuer des comportements `init` similaires en tant que propriétés.
Cela permet d’assigner le champ avant que la construction soit terminée par le type, les types dérivés ou les initialiseurs d’objets.

```cs
class Student
{
    public init string FirstName;
    public init string LastName;
}

var s = new Student()
{
    FirstName = "Jarde",
    LastName = "Parsons",
}

s.FirstName = "Jared"; // Error FirstName is readonly
```

Dans les métadonnées, ces champs sont marqués de la même `readonly` façon que les champs, mais avec un attribut ou modreq supplémentaire `init` pour indiquer qu’il s’agit de champs de style. 

**Résolution** Le LDM accepte que cette proposition soit un son, mais le scénario est globalement disjoint des propriétés. La décision consistait à continuer uniquement `init` avec les propriétés pour le moment. Cela offre un niveau de flexibilité approprié, car `init` une propriété peut muter un `readonly` champ sur le type déclarant de la propriété. Ce sera le cas si des commentaires client importants justifient le scénario.

### <a name="allow-init-as-a-type-modifier"></a>Autoriser init en tant que modificateur de type
De la même façon, `readonly` le modificateur peut être appliqué à `struct` un pour déclarer automatiquement tous les `readonly`champs comme `init` , le seul modificateur peut être déclaré `struct` sur `class` ou pour marquer automatiquement tous les `init`champs comme.
Cela signifie que les deux déclarations de type suivantes sont équivalentes :

```cs
struct Point
{
    public init int X;
    public init int Y;
}

// vs. 

init struct Point
{
    public int X;
    public int Y;
}
```

**Résolution** Cette fonctionnalité est trop *jolie* ici et est en conflit `readonly struct` avec la fonctionnalité sur laquelle elle est basée. La `readonly struct` fonctionnalité est simple en ce qu’elle `readonly` s’applique à tous les membres : champs, méthodes, etc... La `init struct` fonctionnalité s’applique uniquement aux propriétés. Cela finit par compliquer les utilisateurs. 

Étant donné `init` que n’est valide que sur certains aspects d’un type, nous avons rejeté l’idée de le faire en tant que modificateur de type.

## <a name="considerations"></a>Considérations

### <a name="compatibility"></a>Compatibilité
Cette `init` fonctionnalité est conçue pour être compatible avec les `get` propriétés existantes uniquement. Plus précisément, il s’agit d’une modification entièrement additive pour une propriété qui `get` est uniquement aujourd’hui, mais qui souhaite une plus grande sémantique de création d’objets Flexbile.

Prenons l’exemple du type suivant :

```cs
class Name
{
    public string First { get; }
    public string Last { get; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

L' `init` ajout à ces propriétés est une modification sans rupture :

```cs
class Name
{
    public string First { get; init; }
    public string Last { get; init; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

### <a name="il-verification"></a>Vérification IL
Lorsque .NET Core décide de réimplémenter la vérification IL, les règles doivent être ajustées pour tenir `init` compte des membres. Cela doit être inclus dans les modifications de règle pour l’accession non transmutante `readonly` aux données.

Les règles de vérification IL devront être divisées en deux parties : 

1. Autorisation `init` des membres à définir `readonly` un champ.
1. Détermination du moment `init` où un membre peut être appelé légalement.

La première est un ajustement simple aux règles existantes. Le vérificateur IL peut être enseigné pour reconnaître `init` les membres et, à partir de là, il suffit `readonly` de considérer un champ comme `this` pouvant être définissable dans ce membre.

La deuxième règle est plus compliquée. Dans le cas simple des initialiseurs d’objets, la règle est directe. Il doit être légal d’appeler `init` des membres lorsque le résultat d' `new` une expression est toujours sur la pile. Ainsi, tant que la valeur n’a pas été stockée dans un élément ou un élément de tableau local ou passé comme argument à une autre méthode, il `init` sera toujours légal d’appeler les membres. Cela garantit qu’une fois que le résultat `new` de l’expression est publié dans un identificateur nommé (autre `this`que), il n’est plus autorisé à appeler `init` les membres. 

Le cas le plus compliqué cependant est quand nous `init` mélangeons les membres, les `await`initialiseurs d’objets et. Cela peut entraîner le placement temporaire de l’objet nouvellement créé dans une machine à États et le placer dans un champ.

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

Ici, le résultat `new Student()` de sera hoised dans une machine à États en tant que champ avant l' `Name` ensemble de. Le compilateur doit marquer ces champs levés de telle sorte que le vérificateur IL comprenne qu’ils ne sont pas accessibles par l’utilisateur et ne viole donc pas la sémantique prévue `init`de.

### <a name="init-members"></a>membres init
Le `init` modificateur peut être étendu pour s’appliquer à tous les membres d’instance. Cela généraliserait le concept de la `init` construction de l’objet et autorisera les types à déclarer des méthodes d’assistance qui pouvaient partipate dans le processus `init` de construction pour initialiser les champs et les propriétés.

De tels membres auraient tous les restricions qu’un `init` accesseur effectue dans cette conception. La nécessité est cependant douteuse, mais elle peut être ajoutée en toute sécurité dans une future version du langage de manière compatible.

### <a name="generate-three-accessors"></a>Générer trois accesseurs
L’une des implémentations potentielles `init` des `init` propriétés consiste à `set`effectuer une séparation complète de. Cela signifie qu’une propriété peut potentiellement avoir trois accesseurs différents : `get`, `set` et `init`.

Cela présente l’avantage potentiel de permettre l’utilisation de modreq pour garantir l’exactitude tout en conservant la compatibilité binaire. L’implémentation serait à peu près ce qui suit :

1. Un `init` accesseur est toujours émis s’il y a `set`un. Lorsqu’il n’est pas défini par le développeur, il s' `set`agit simplement d’une référence à. 
1. Le jeu d’une propriété dans un initialiseur d’objet utilise `init` toujours s’il est présent, mais `set` revient à s’il est manquant.

Cela signifie qu’un développeur peut toujours supprimer `init` en toute sécurité une propriété. 

L’inconvénient de cette conception est que n’est utile que `init` si est **toujours** émis lorsqu’il y a `set`un. La langue ne peut pas `init` savoir si a été supprimé par le passé, elle doit supposer qu’elle était `init` et, par conséquent, doit toujours être émis. Cela entraînerait une expansion significative des métadonnées et ne justifie pas ici le coût de la compatibilité.
