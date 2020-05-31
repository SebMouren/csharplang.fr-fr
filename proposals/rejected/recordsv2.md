---
ms.openlocfilehash: 5636157ba54e93587847d6f2f7aac2dc675f3112
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84228210"
---

# <a name="records-v2"></a>Enregistrements v2

Dans le passé, nous avons pensé aux enregistrements comme une fonctionnalité pour permettre l’utilisation de données.

« L’utilisation des données » est un grand groupe avec un certain nombre de facettes, il peut donc être intéressant d’examiner chacun d’entre eux. Commençons par examiner un exemple d’enregistrement aujourd’hui et certains de ses inconvénients.

Par exemple, un enregistrement simple serait défini aujourd’hui comme suit

```C#
public class UserInfo
{
    public string Username { get; set; }
    public string Email { get; set; }
    public bool IsAdmin  { get; set; } = false;
}
```

et l’utilisation est lue

```C#
void M()
{
    var userInfo = new UserInfo() 
    {
        Username = "andy",
        Email = "angocke@microsoft.com",
        IsAdmin = true
    };
}
```

Ce code présente des avantages significatifs :

1. La définition est résiliente à la version, les propriétés peuvent être facilement ajoutées ou déplacées
2. Les propriétés peuvent être définies dans n’importe quel ordre, et les noms dans l’initialisation correspondent toujours aux accesseurs
3. Les propriétés avec des valeurs par défaut peuvent simplement être ignorées

Le premier défaut est que les propriétés doivent maintenant être mutables.

# <a name="mutability"></a>Mutabilité

Ce que nous aimerions, c’est pour C# de fournir un moyen de définir un `readonly` membre dans des initialiseurs d’objets.
Étant donné que certains types n’ont peut-être pas été conçus avec cette initialisation à l’esprit, nous aimerions également l’accepter.

La solution proposée est un nouveau modificateur, `initonly` , qui peut être appliqué à des propriétés et des champs :

```C#
public class UserInfo
{
    public initonly string Username { get; }
    public initonly string Email { get; }
    public initonly bool IsAdmin { get; } = false;
}
```

Le CodeGen est étonnamment simple : nous définissons simplement le champ ReadOnly.
Plus précisément, les propriétés inférieures ressemblent à ceci :

```C#
public class UserInfo
{
    private readonly string <Backing>_username;
    public string get_Username() => <Backing>_username;
    [return: modreq(initonly)]
    public void set_Username(string value) { <Backing>_username = value; }
    ...
}
```

Le CLR considère que les champs ReadOnly ne sont pas vérifiables, mais pas non sécurisés. Pour prendre en charge un vérificateur plus avancé, la règle suivante est proposée : un champ ReadOnly ne peut être modifié qu’à l’intérieur de `initonly` méthodes, ou sur un nouvel objet qui se trouve sur la pile CLR et n’a pas été publié via un appel de méthode ou de magasin.

Cela devrait résoudre de nombreux problèmes liés à la mutabilité dans l' `UserInfo` exemple, tout en ne nécessitant pas de stratégies d’émission compliquées ou fragiles. Toutefois, l’immuabilité présente un nouveau problème : en créant facilement un objet avec des modifications.

# <a name="with-ing"></a>Avec-ING

Lors de la programmation avec l’immuabilité, l’apport de modifications à un objet s’effectue en créant une copie avec des modifications au lieu d’apporter les modifications directement à l’objet. Malheureusement, il n’existe aucun moyen pratique de le faire en C#, même avec des enregistrements de style actuel. Il a été proposé précédemment de fournir un type de méthode « with » générée automatiquement pour les enregistrements qui implémentent cette fonctionnalité. Si nous disposons d’un tel mécanisme, il est important qu’il fonctionne avec `initonly` les membres. Pour ce faire, nous avons proposé d’ajouter une `with` expression, similaire à un initialiseur d’objet. Voici un exemple d’utilisation :

```C#
var userInfo = new UserInfo() 
{
    Username = "andy",
    Email = "angocke@microsoft.com",
    IsAdmin = true
};
var newUserName = userInfo with { Username = "angocke" };
```

L' `newUserName` objet résultant serait une copie de `userInfo` , avec la `Username` valeur "angocke".
Le CodeGen sur l' `with` expression est également similaire à l’initialiseur d’objet : un nouvel objet est construit, puis l' `initonly` `Username` accesseur Set est appelé dans le corps de la méthode.

Bien entendu, la différence ici est que le nouvel objet en cours de construction n’est pas une simple création d’objet, il s’agit d’un doublon de l’objet d’origine. Pour fournir cette fonctionnalité, nous exigeons que l’objet fournisse un « avec constructeur » qui fournit un objet dupliqué. Voici un exemple de `With` constructeur :

```C#
class UserInfo
{
    ...
    [WithConstructor] // placeholder syntax, up for debate
    public UserInfo With()
    {
        return new UserInfo() { Username = this.Username, Email = this.Email, IsAdmin = this.IsAdmin };
    }
}
```

En particulier, l' `with` expression définit des `initonly` membres, tout comme l’initialiseur d’objet. ainsi, pour prendre en charge la vérification, nous devons nous assurer que l’objet ne peut pas avoir été publié avant la `initonly` définition des membres. Pour appliquer cela, l' `WithConstructor` attribut (ou la syntaxe équivalente) applique une nouvelle règle pour la méthode : toutes les instructions Return doivent contenir directement une expression de création d’objet, éventuellement avec un initialiseur d’objet.

Si le `With` constructeur requiert une validation, l’utilisateur peut introduire un constructeur pour effectuer cette validation, par exemple

```C#
class UserInfo
{
    ...
    private UserInfo(UserInfo original)
    {
        // validation code
    }
    [WithConstructor]
    public UserInfo With() => new UserInfo(this);
}
```

Le dernier élément de complexité associé à `With` est l’héritage. Si votre enregistrement est extensible, vous devrez fournir un nouveau `With` pour la sous-classe. Pour ce faire, procédez comme suit :

```C#
class Base
{
    ...
    protected Base(Base original)
    {
        // validation
    }
    [WithConstructor]
    public virtual Base With() => new Base(this);
}
class Derived : Base
{
    ...
    protected Derived(Derived original)
    : base(original)
    {
        // validation
    }
    [WithConstructor]
    public override Derived With() => new Derived(this);
}
```

Notez un élément de complexité supplémentaire ici : pour substituer le `With` constructeur avec le type dérivé, le langage devra également prendre en charge les types de retour covariants dans les substitutions.
Il existe déjà une proposition distincte pour [cette fonctionnalité.](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md)

**Inconvénients**

- Le fait que toutes les instructions return dans `WithConstructor` s contiennent des expressions d’objet est restrictif.
  Cela peut être atténué par l’analyse de Flow qui garantit que le nouvel objet n’échappe pas à la méthode
- La prise en charge de la variance dans les substitutions via les astuces du compilateur nécessite des méthodes stub, qui vont croître augmentera avec la profondeur d’héritage. La nécessité d’une méthode stub est due à une condition d’exécution qui remplace exactement les signatures. Si la spécification du runtime a été relâchée, les méthodes stub ne sont pas nécessaires du tout.
- L’utilisation de constructeurs chaînés de la forme `Type(Type original)` réserve effectivement ce constructeur pour l’utilisation du modèle. Étant donné que les constructeurs ont une sémantique unique et ne peuvent pas être renommés, cela peut être limité.


## <a name="wrapping-it-all-up-records"></a>Encapsuler tout : enregistrements

Les fonctionnalités ci-dessus permettent un style de programmation très difficile. Mais même avec les nouvelles fonctionnalités, elles peuvent être très détaillées et sujettes aux erreurs pour annoter tout le reste. Il y a également quelques éléments, comme Equals et GetHashCode, qui peuvent déjà être écrits aujourd’hui, c’est tout simplement laborieux.
En outre, une faille importante en matière d’implémentation de l’égalité sur ces nouvelles primitives est que l’égalité structurelle est quelque chose qui doit changer avec votre type de données à mesure que de nouvelles données sont ajoutées, mais lorsque vous la gérez manuellement, il est probable que ces éléments peuvent être désynchronisés.

Par conséquent, il est proposé que C# prenne en charge la nouvelle syntaxe pour les enregistrements, et non pour fournir de nouvelles fonctionnalités, mais pour définir des valeurs par défaut et générer du code conçu pour être utilisé dans les enregistrements. L’exemple de syntaxe ressemble à

```C#
data class UserInfo
{
    public string Username { get; }
    public string Email { get; }
    public bool IsAdmin { get; } = false;
}
```

Le code généré pour cette classe considérerait tous les champs publics et les propriétés automatiques comme des membres structurels de l’enregistrement. Les membres d’enregistrement peuvent être personnalisés à l’aide d’un nouvel `RecordMember(bool)` attribut qui peut être utilisé pour inclure ou exclure des membres. Les membres d’enregistrement sont `initonly` par défaut et l’égalité est générée automatiquement pour la classe en fonction des membres d’enregistrement. À tout moment, le comportement de ces membres peut être personnalisé simplement en les déclarant dans la source. L’implémentation écrite par l’utilisateur remplacerait l’implémentation par défaut dans toutes les utilisations de modèle.

Notez que l’égalité dans le visage de l’héritage est complexe, mais semble avoir été correctement résolue dans la [proposition autres enregistrements](records.md).

## <a name="primary-constructors"></a>Constructeurs principaux

La proposition d’enregistrement précédente a également inclus une nouvelle syntaxe pour une liste de paramètres sur le type lui-même, par exemple

```C#
class Point(int X, int Y);
```

Dans la nouvelle conception, la liste de paramètres serait une fonctionnalité C# orthogonale, qui pourrait être intégrée correctement aux enregistrements. Si un constructeur principal est inclus dans un enregistrement, il aurait de nouvelles valeurs par défaut, tout comme les champs publics et les propriétés automatiques : les paramètres dans le constructeur principal seraient utilisés pour générer des propriétés de membre d’enregistrement publiques portant le même nom. En outre, le constructeur principal peut maintenant être utilisé pour générer automatiquement un destructor.

Par exemple, l’enregistrement suivant avec un constructeur principal

```C#
data class Point(int X, int Y);
```

serait équivalent à

```C#
data class Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }
}
```

et la génération finale de la version ci-dessus serait

```C#
class Point
{
    public initonly int X { get; }
    public initonly int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    protected Point(Point other)
    : this(other.X, other.Y)
    { }

    [WithConstructor]
    public virtual Point With() => new Point(this);

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }

    // Generated equality
}
```

Notez que nous avons pris en compte une autre information pour une classe de données avec un constructeur principal : au lieu de définir les champs primaires dans le constructeur protégé généré, nous déléguons au constructeur principal. Si la classe point avait un autre membre d’enregistrement non primaire, par exemple

```C#
data class Point(int X, int Y)
{
    public int Z { get; }
}
```

cela modifierait ensuite le constructeur protégé généré comme suit :

```C#
class Point
{
    // ...
    protected Point(Point other)
    : this(other.X, other.Y)
    {
        Z = other.Z;
    }
    // ...
}
```

En particulier, cela ne répond pas à l’héritage des enregistrements avec les constructeurs principaux. Par exemple,

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A;
```

Plutôt que de résoudre de manière arbitraire, une approche plus explicite peut exiger qu’une liste de paramètres soit fournie avec la liste de base, par exemple

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A(X, Y);
```

La liste de paramètres dans la liste de base est ensuite appliquée à un `base` appel dans le constructeur principal généré :

```C#
class B
{
    // ..
    public B(int x, int y, int z)
    : base(x, y)
    // ..
}
```

Quant à ce qu’un constructeur principal peut signifier en dehors d’un enregistrement, il est toujours ouvert pour une proposition supplémentaire.
