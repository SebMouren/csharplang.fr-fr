---
ms.openlocfilehash: ebbdab6d121f3001ac34a953b3de09768cda6344
ms.sourcegitcommit: 3f177e90b12e39d4d28f8bb1064df81a8e5912ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726067"
---
<a name="init-only-members"></a>Init Seuls les membres
=====

## <a name="summary"></a>Résumé
Cette proposition ajoute le concept de propriétés init seulement à C. Ces propriétés peuvent être définies au `get` point de création d’objets, mais ne deviennent effectivement qu’une fois la création d’objet terminée. Cela permet un modèle immuable beaucoup plus flexible dans le C. 

## <a name="motivation"></a>Motivation
Les mécanismes sous-jacents pour la construction de données immuables dans le C n’ont pas changé depuis 1,0. Ils restent :

1. Déclarer les `readonly`champs comme .
1. Déclarer les propriétés `get` qui ne contiennent qu’un accesseur.

Ces mécanismes sont efficaces pour permettre la construction de données immuables, mais ils le font en ajoutant des coûts au code de la plaque chauffante des types et en optant ces types hors des caractéristiques comme l’objet et les initialisateurs de collecte. Cela signifie que les développeurs doivent choisir entre la facilité d’utilisation et l’immuabilité.

Un simple objet immuable comme `Point` nécessite deux fois plus de code de plaque de chaudière pour soutenir la construction que de déclarer le type. Plus le type est grand, plus le coût de cette plaque de chaudière est élevé :

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

L’accesseur `init` rend les objets immuables plus flexibles en permettant à l’appelant de muter les membres pendant l’acte de construction. Cela signifie que les propriétés immuables de l’objet peuvent participer à des initialisateurs d’objets et élimine ainsi le besoin de toutes les plaques chauffantes constructor dans le type. Le `Point` type est maintenant simplement:

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

Le consommateur peut alors utiliser des initialisateurs d’objets pour créer l’objet

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a>Conception détaillée

### <a name="init-members"></a>membres init
Une propriété init only est `init` déclarée en `set` utilisant l’accesseur à la place de l’accesseur :

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

Une propriété d’instance contenant un `init` accesseur est considérée comme définie dans les circonstances suivantes :

- Lors d’un initialisateur d’objets
- À l’intérieur d’un constructeur d’instance du type contenant ou dérivé, sur `this` ou`base`
- À `init` l’intérieur de l’accesseur de toute propriété, sur `this` ou`base`

Les heures ci-dessus dans lesquelles les `init` accesseurs sont définis sont collectivement désignés dans ce document comme la phase de construction de l’objet.

Cela signifie `Student` que la classe peut être utilisée de la manière suivante :

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

Les règles `init` autour du moment où les accesseurs sont définis s’étendent sur les hiérarchies de type. Si le membre est accessible et que l’objet est connu pour être dans la phase de construction, le membre est défini. Cela permet spécifiquement ce qui suit:

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

Au moment `init` où un accesseur est invoqué, l’instance est connue pour être dans la phase de construction ouverte. Par `init` conséquent, un accesseur est autorisé à prendre `set` les mesures suivantes en plus de ce qu’un accesseur normal peut faire:

1. Appelez `init` d’autres `this` accesseurs disponibles par l’intermédiaire ou`base`
1. Attribuer `readonly` les champs déclarés sur le même type

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

La possibilité `readonly` d’attribuer `init` des champs à partir d’un accesseur est limitée aux champs déclarés sur le même type que l’accesseur. Il ne peut `readonly` pas être utilisé pour attribuer des champs dans un type de base. Cette règle garantit que les auteurs de type restent en contrôle sur le comportement de mutabilité de leur type. Les développeurs qui ne `init` souhaitent pas utiliser ne peuvent pas être touchés par d’autres types qui choisissent de le faire :

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

Quand `init` est utilisé dans une propriété virtuelle, puis tous `init`les remplacements doivent également être marqués comme . De même, il n’est `set` `init`pas possible de passer outre à un simple avec .

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

Une `interface` déclaration peut `init` également participer à l’initialisation de style par le biais du modèle suivant :

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

Restrictions de cette fonctionnalité:
- L’accesseur `init` ne peut être utilisé que sur les propriétés d’instance
- Une propriété ne `init` peut `set` pas contenir à la fois un et un accesseur
- Toutes les dérogations `init` d’une `init`propriété doivent avoir si la base avait . Cette règle s’applique également à la mise en œuvre de l’interface.

### <a name="metadata-encoding"></a>Codage de métadonnées 
Les `init` accesseurs de propriété seront `set` émis en tant qu’accesseur standard `IsInitOnly`avec le type de retour marqué d’un modreq de . Il s’agit d’un nouveau type qui aura la définition suivante:

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsInitOnly
    {
    }
}
```

Le compilateur correspondra au type par nom complet. Il n’est pas nécessaire qu’il apparaisse dans la bibliothèque centrale. S’il existe plusieurs types par ce nom, puis le compilateur va attacher break dans l’ordre suivant:

1. Celui défini dans le projet en cours de compilation
1. Celui défini dans corelib

Si aucun de ces éléments n’existe, une erreur d’ambiguïté type sera émise.

La conception `IsInitOnly` pour est futher couvert dans [ce numéro](https://github.com/dotnet/runtime/issues/34978)

## <a name="questions"></a>Questions

### <a name="breaking-changes"></a>Changements cassants
L’un des principaux points de pivot dans la façon dont cette fonctionnalité est codée se résumera à la question suivante: 

> Est-ce un changement `init` de `set`rupture binaire à remplacer par ?

Remplacer `init` par `set` et ainsi rendre une propriété entièrement facilitable n’est jamais une source de rupture de changement sur une propriété non virtuelle. Il élargit simplement l’ensemble des scénarios où la propriété peut être écrite. Le seul comportement en question est de savoir si oui ou non cela reste un changement binaire de rupture.

Si nous voulons faire `init` le `set` changement de à une source et binaire changement compatible alors il va forcer notre main sur le modreq vs attributs décision ci-dessous parce qu’il exclura modreqs comme une âmeution. Si d’autre part cela est considéré comme un non-intéressant, alors cela rendra le modreq vs décision d’attribut moins percutant.

**Résolution** Ce scénario n’est pas considéré comme convaincant par LDM.

### <a name="modreqs-vs-attributes"></a>Modreqs vs attributs
La stratégie `init` d’émission pour les accesseurs de propriété doit choisir entre l’utilisation d’attributs ou de modréqs lors de l’émission pendant les métadonnées. Ceux-ci ont des compromis différents qui doivent être pris en considération.

Annoter un accesseur de l’ensemble de propriété avec une déclaration modreq signifie compilateurs conformes CLI ignorera l’accesseur à moins qu’il ne comprend le modreq. Cela signifie que seuls `init` les compilateurs au courant liront le membre. Les compilateurs `init` qui `set` ne connaissent pas l’ignorance du membre et ne traiteront donc pas accidentellement la propriété comme l’a lu ou écrit. 

L’inconvénient du modreq est `init` devient une `set` partie de la signature binaire de l’accesseur. L’ajout `init` ou la suppression brisera la compatibilité binaire de l’application.

L’utilisation d’attributs `set` pour annoter l’accesseur signifie que seuls les compilateurs qui comprennent l’attribut sauront limiter l’accès à celui-ci. Un compilateur `init` qui ne connaît pas le verra comme une simple propriété de lecture/ d’écriture et permettra l’accès.

Cela signifierait apparemment que cette décision est un choix entre une sécurité supplémentaire au détriment de la compatibilité binaire. Creuser dans un peu la sécurité supplémentaire n’est pas exactement ce qu’il semble. Il ne protégera pas contre les circonstances suivantes :

1. Réflexion `public` sur les membres
1. L’utilisation de`dynamic` 
1. Compilateurs qui ne reconnaissent pas les modréqs

Il convient également de considérer que, lorsque nous aurons terminé `init` les règles de vérification de l’IL pour .NET 5, sera l’une de ces règles. Cela signifie que l’application supplémentaire sera obtenue en vérifiant simplement les compilateurs émettant l’IL vérifiable.

Les langues primaires pour .NET (C, F et VB) `init` seront toutes mises à jour pour reconnaître ces accesseurs. D’où le seul scénario réaliste ici est quand `init` un compilateur C 9 émet des propriétés et ils sont vus par un ensemble d’outils plus anciens tels que C 8, VB 15, etc ... C 8. C’est le compromis d’examiner et de peser contre la compatibilité binaire.

**Note** Cette discussion s’applique principalement uniquement aux membres, et non aux domaines. Bien `init` que les champs aient été rejetés par LDM, ils sont toujours intéressants à considérer pour la discussion modreq vs attribut. La `init` caractéristique pour les champs est `readonly`un assouplissement de la restriction existante de . Cela signifie que si `readonly` nous émettons les champs comme un attribut, il n’y `readonly`a aucun risque de compilateurs plus âgés mal utiliser le champ parce qu’ils reconnaîtraient déjà . Par conséquent, l’utilisation d’un modreq ici n’ajoute pas de protection supplémentaire.

**Résolution** La fonction utilisera un modreq `init` pour encoder le setter de propriété. Les facteurs impérieux étaient (sans ordre particulier) :

* Désir de décourager les compilateurs plus âgés de violer `init` la sémantique
* Désir de faire l’ajout `virtual` ou `interface` la suppression `init` dans une déclaration ou à la fois une source et binaire changement de rupture.

Étant donné qu’il n’y avait pas non plus de soutien significatif pour supprimer `init` pour être un changement compatible binaire, il a fait le choix d’utiliser modreq directement en avant.

### <a name="init-vs-initonly"></a>init vs initonly
Il y avait trois formes de syntaxe qui ont obtenu une considération significative au cours de notre réunion LDM:

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

**Résolution** Il n’y avait pas de syntaxe qui a été largement favorisée dans LDM.

Un point qui a attiré l’attention a été la `init` façon dont le choix de la syntaxe aurait un impact sur notre capacité à faire des membres comme une caractéristique générale à l’avenir.
Choisir l’option 1 signifierait qu’il serait `init` difficile `get` de définir une propriété qui avait une méthode de style à l’avenir. Finalement, il a été décidé que `init` si nous décidions `init` d’aller de l’avant avec les membres `init set`généraux à l’avenir, nous pourrions permettre d’être un modificateur dans la liste des accessoirs de propriété ainsi qu’une main courte pour . Essentiellement, les deux déclarations suivantes seraient identiques.

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

La décision a été `init` prise d’aller de l’avant en tant qu’accesseur autonome dans la liste des accessoirs de propriété.

### <a name="warn-on-failed-init"></a>Avertir sur init échoué
Considérons le scénario suivant. Un type déclare `init` un seul membre qui n’est pas placé dans le constructeur. Le code qui construit l’objet devrait-il obtenir un avertissement s’il n’a pas réussi à initialiser la valeur ?

À ce stade, il est clair que le champ ne sera jamais défini et `private` a donc beaucoup de similitudes avec l’avertissement de ne pas initialiser les données. D’où un avertissement aurait apparemment une certaine valeur ici?

Il ya des inconvénients importants à cet avertissement si:
1. Il complique l’histoire de `readonly` compatibilité de changer à `init`. 
1. Il faut transporter des métadonnées supplémentaires pour désigner les membres qui doivent être parasés par l’appelant.

En outre, si nous croyons qu’il ya de la valeur ici dans le scénario global de forcer les créateurs d’objets à être avertis / erreur sur des domaines spécifiques, alors cela est probablement logique comme une caractéristique générale. Il n’y a aucune `init` raison qu’il soit limité aux membres.

**Résolution** Il n’y aura `init` pas d’avertissement sur la consommation de champs et de propriétés.

LDM veut avoir une discussion plus large sur l’idée des champs et des propriétés nécessaires. Cela peut nous amener à revenir `init` et à reconsidérer notre position sur les membres et la validation.

## <a name="allow-init-as-a-field-modifier"></a>Autoriser l’init en tant que modificateur de champ
De la `init` même manière peut servir d’accesseur de propriété, il pourrait également `init` servir de désignation sur les champs pour leur donner des comportements similaires que les propriétés.
Cela permettrait d’attribuer le champ avant que la construction ne soit terminée par le type, les types dérivés ou les initialisateurs d’objets.

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

Dans les métadonnées, ces champs `readonly` seraient marqués de la même manière que `init` les champs, mais avec un attribut ou un modreq supplémentaire pour indiquer qu’il s’agit de champs de style. 

**Résolution** LDM convient que cette proposition est saine, mais dans l’ensemble, le scénario s’est senti disjoint des propriétés. La décision était de `init` procéder uniquement avec les propriétés pour l’instant. Cela a un niveau approprié `init` de flexibilité `readonly` car une propriété peut muter un champ sur le type déclarant de la propriété. Cela sera réexaminé s’il y a une rétroaction importante des clients qui justifie le scénario.

### <a name="allow-init-as-a-type-modifier"></a>Autoriser l’init comme modificateur de type
De la même `readonly` manière, le `struct` modificateur peut être `readonly`appliqué `init` à un pour `struct` déclarer `class` automatiquement tous les `init`champs comme , le seul modificateur peut être déclaré sur un ou pour marquer automatiquement tous les champs comme .
Cela signifie que les deux déclarations de type suivantes sont équivalentes :

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

**Résolution** Cette fonctionnalité est trop *mignonne* ici et entre en conflit avec la `readonly struct` fonctionnalité sur laquelle il est basé. La `readonly struct` fonctionnalité est simple `readonly` en ce qu’elle s’applique à tous les membres : champs, méthodes, etc... La `init struct` fonction ne s’appliquerait qu’aux propriétés. Cela finit par le rendre déroutant pour les utilisateurs. 

Étant `init` donné que ce n’est valable que sur certains aspects d’un type, nous avons rejeté l’idée de l’avoir comme modificateur de type.

## <a name="considerations"></a>Considérations

### <a name="compatibility"></a>Compatibilité
La `init` fonctionnalité est conçue pour `get` être compatible avec les seules propriétés existantes. Plus précisément, il est censé être un `get` changement complètement additif pour une propriété qui est seulement aujourd’hui, mais désire plus flexbile création d’objets sémantiques.

Prenons, par exemple, le type suivant :

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

L’ajout `init` à ces propriétés est un changement non-rupture:

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

### <a name="il-verification"></a>Vérification DE l’IL
Lorsque .NET Core décide de ré-implémenter la vérification `init` de l’IL, les règles devront être ajustées pour tenir compte des membres. Cela devra être inclus dans les modifications des règles `readonly` pour les as non mutants aux données.

Les règles de vérification de l’IL devront être divisées en deux parties : 

1. Permettre `init` aux membres `readonly` de mettre un champ.
1. Déterminer quand `init` un membre peut être légalement appelé.

Le premier est un simple ajustement aux règles existantes. Le vérificateur de l’IL `init` peut apprendre à reconnaître les `readonly` membres et à `this` partir de là, il a juste besoin d’envisager un champ à définir sur un tel membre.

La deuxième règle est plus compliquée. Dans le cas simple des initialisateurs d’objets, la règle est simple. Il devrait être `init` légal d’appeler `new` les membres lorsque le résultat d’une expression est toujours sur la pile. C’est jusqu’à ce que la valeur a été stockée dans un local, élément `init` de tableau ou un champ ou passé comme un argument à une autre méthode, il sera toujours légal d’appeler les membres. Cela garantit qu’une fois `new` que le résultat de l’expression est publié sur un identifiant nommé (autre que) `this`alors il ne sera plus légal d’appeler les `init` membres. 

Le cas le plus compliqué `init` est cependant quand `await`nous mélangeons les membres, les initialisateurs d’objets et . Cela peut faire en sorte que l’objet nouvellement créé soit temporairement hissé dans une machine d’état et donc mis dans un champ.

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

Ici, le `new Student()` résultat sera hoised dans une machine d’état comme un champ avant l’ensemble de `Name` se produit. Le compilateur devra marquer ces champs hissés d’une manière que le vérificateur DE l’IL comprend qu’ils `init`ne sont pas accessibles à l’utilisateur et ne viole donc pas la sémantique prévue de .

### <a name="init-members"></a>membres init
Le `init` modificateur pourrait être étendu pour s’appliquer à tous les membres de l’instance. Cela généraliserait le `init` concept de construction d’objets pendant la construction d’objets et `init` permettrait aux types de déclarer les méthodes d’aide qui pourraient se faire dans le processus de construction pour initialiser les champs et les propriétés.

Ces membres auraient toutes les restricions qu’un `init` accesseur fait dans cette conception. Le besoin est discutable cependant et cela peut être ajouté en toute sécurité dans une version future de la langue d’une manière compatible.

### <a name="generate-three-accessors"></a>Générer trois accesseurs
Une mise `init` en œuvre potentielle des propriétés est de rendre `init` complètement séparé de `set`. Cela signifie qu’une propriété peut potentiellement `get` `set` avoir `init`trois accessoiris différents: , et .

Cela a l’avantage potentiel de permettre l’utilisation de modreq pour faire respecter la justesse tout en maintenant la compatibilité binaire. La mise en œuvre serait à peu près la suivante:

1. Un `init` accesseur est toujours émis `set`s’il y a un . Lorsqu’il n’est pas défini `set`par le développeur, il s’agit simplement d’une référence à . 
1. L’ensemble d’une propriété dans un `init` initialisateur d’objet sera toujours utilisé si présent, mais retomber à `set` si elle est manquante.

Cela signifie qu’un développeur `init` peut toujours supprimer en toute sécurité d’une propriété. 

L’inconvénient de cette conception est `init` qui n’est utile `set`que si elle est **toujours** émise quand il ya un . La langue ne peut `init` pas savoir si a été supprimé dans le `init` passé, il doit supposer qu’il était et donc le doit toujours être émis. Cela entraînerait une expansion importante des métadonnées et ne vaut tout simplement pas le coût de la compatibilité ici.
