---
ms.openlocfilehash: 5636157ba54e93587847d6f2f7aac2dc675f3112
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84228210"
---

# <a name="records-v2"></a><span data-ttu-id="84242-101">Enregistrements v2</span><span class="sxs-lookup"><span data-stu-id="84242-101">Records v2</span></span>

<span data-ttu-id="84242-102">Dans le passé, nous avons pensé aux enregistrements comme une fonctionnalité pour permettre l’utilisation de données.</span><span class="sxs-lookup"><span data-stu-id="84242-102">In the past we've thought about records as a feature to enable working with data.</span></span>

<span data-ttu-id="84242-103">« L’utilisation des données » est un grand groupe avec un certain nombre de facettes, il peut donc être intéressant d’examiner chacun d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="84242-103">"Working with data" is a big group with a number of facets, so it may be interesting to look at each in isolation.</span></span> <span data-ttu-id="84242-104">Commençons par examiner un exemple d’enregistrement aujourd’hui et certains de ses inconvénients.</span><span class="sxs-lookup"><span data-stu-id="84242-104">Let's start by looking at an example of records today and some of its drawbacks.</span></span>

<span data-ttu-id="84242-105">Par exemple, un enregistrement simple serait défini aujourd’hui comme suit</span><span class="sxs-lookup"><span data-stu-id="84242-105">For instance, a simple record would be defined today as follows</span></span>

```C#
public class UserInfo
{
    public string Username { get; set; }
    public string Email { get; set; }
    public bool IsAdmin  { get; set; } = false;
}
```

<span data-ttu-id="84242-106">et l’utilisation est lue</span><span class="sxs-lookup"><span data-stu-id="84242-106">and the usage would read</span></span>

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

<span data-ttu-id="84242-107">Ce code présente des avantages significatifs :</span><span class="sxs-lookup"><span data-stu-id="84242-107">There are significant advantages in this code:</span></span>

1. <span data-ttu-id="84242-108">La définition est résiliente à la version, les propriétés peuvent être facilement ajoutées ou déplacées</span><span class="sxs-lookup"><span data-stu-id="84242-108">The definition is version resilient, properties can easily be added or moved</span></span>
2. <span data-ttu-id="84242-109">Les propriétés peuvent être définies dans n’importe quel ordre, et les noms dans l’initialisation correspondent toujours aux accesseurs</span><span class="sxs-lookup"><span data-stu-id="84242-109">Properties can be set in any order, and the names in the initialization always match the accessors</span></span>
3. <span data-ttu-id="84242-110">Les propriétés avec des valeurs par défaut peuvent simplement être ignorées</span><span class="sxs-lookup"><span data-stu-id="84242-110">Properties with default values can simply be skipped</span></span>

<span data-ttu-id="84242-111">Le premier défaut est que les propriétés doivent maintenant être mutables.</span><span class="sxs-lookup"><span data-stu-id="84242-111">The first flaw is that the properties must now be mutable.</span></span>

# <a name="mutability"></a><span data-ttu-id="84242-112">Mutabilité</span><span class="sxs-lookup"><span data-stu-id="84242-112">Mutability</span></span>

<span data-ttu-id="84242-113">Ce que nous aimerions, c’est pour C# de fournir un moyen de définir un `readonly` membre dans des initialiseurs d’objets.</span><span class="sxs-lookup"><span data-stu-id="84242-113">What we'd like is for C# to provide a way to set a `readonly` member in object initializers.</span></span>
<span data-ttu-id="84242-114">Étant donné que certains types n’ont peut-être pas été conçus avec cette initialisation à l’esprit, nous aimerions également l’accepter.</span><span class="sxs-lookup"><span data-stu-id="84242-114">Since some types may not have been designed with this initialization in mind, we'd also like it to be opt-in.</span></span>

<span data-ttu-id="84242-115">La solution proposée est un nouveau modificateur, `initonly` , qui peut être appliqué à des propriétés et des champs :</span><span class="sxs-lookup"><span data-stu-id="84242-115">The proposed solution is a new modifier, `initonly`, that can be applied to properties and fields:</span></span>

```C#
public class UserInfo
{
    public initonly string Username { get; }
    public initonly string Email { get; }
    public initonly bool IsAdmin { get; } = false;
}
```

<span data-ttu-id="84242-116">Le CodeGen est étonnamment simple : nous définissons simplement le champ ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="84242-116">The codegen for this is surprisingly straight forward: we just set the readonly field.</span></span>
<span data-ttu-id="84242-117">Plus précisément, les propriétés inférieures ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="84242-117">Specifically, the lowered properties would look like:</span></span>

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

<span data-ttu-id="84242-118">Le CLR considère que les champs ReadOnly ne sont pas vérifiables, mais pas non sécurisés.</span><span class="sxs-lookup"><span data-stu-id="84242-118">The CLR considers setting readonly fields to be unverifiable, but not unsafe.</span></span> <span data-ttu-id="84242-119">Pour prendre en charge un vérificateur plus avancé, la règle suivante est proposée : un champ ReadOnly ne peut être modifié qu’à l’intérieur de `initonly` méthodes, ou sur un nouvel objet qui se trouve sur la pile CLR et n’a pas été publié via un appel de méthode ou de magasin.</span><span class="sxs-lookup"><span data-stu-id="84242-119">To support a more advanced verifier, the following rule is proposed: a readonly field can be modified only inside `initonly` methods, or on a new object that is on the CLR stack and has not been published via a store or method call.</span></span>

<span data-ttu-id="84242-120">Cela devrait résoudre de nombreux problèmes liés à la mutabilité dans l' `UserInfo` exemple, tout en ne nécessitant pas de stratégies d’émission compliquées ou fragiles.</span><span class="sxs-lookup"><span data-stu-id="84242-120">This should solve many of the problems with mutability in the `UserInfo` example, while not requiring complicated or brittle emit strategies.</span></span> <span data-ttu-id="84242-121">Toutefois, l’immuabilité présente un nouveau problème : en créant facilement un objet avec des modifications.</span><span class="sxs-lookup"><span data-stu-id="84242-121">However, immutability does present a new problem: easily constructing an object with changes.</span></span>

# <a name="with-ing"></a><span data-ttu-id="84242-122">Avec-ING</span><span class="sxs-lookup"><span data-stu-id="84242-122">With-ing</span></span>

<span data-ttu-id="84242-123">Lors de la programmation avec l’immuabilité, l’apport de modifications à un objet s’effectue en créant une copie avec des modifications au lieu d’apporter les modifications directement à l’objet.</span><span class="sxs-lookup"><span data-stu-id="84242-123">When programming with immutability, making changes to an object is done by constructing a copy with changes instead of making the changes directly on the object.</span></span> <span data-ttu-id="84242-124">Malheureusement, il n’existe aucun moyen pratique de le faire en C#, même avec des enregistrements de style actuel.</span><span class="sxs-lookup"><span data-stu-id="84242-124">Unfortunately, there's no convenient way to do this in C#, even with current-style records.</span></span> <span data-ttu-id="84242-125">Il a été proposé précédemment de fournir un type de méthode « with » générée automatiquement pour les enregistrements qui implémentent cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="84242-125">It's been previously proposed that some kind of autogenerated "With" method be provided for records that implements that functionality.</span></span> <span data-ttu-id="84242-126">Si nous disposons d’un tel mécanisme, il est important qu’il fonctionne avec `initonly` les membres.</span><span class="sxs-lookup"><span data-stu-id="84242-126">If we have such a mechanism, it's important that it work with `initonly` members.</span></span> <span data-ttu-id="84242-127">Pour ce faire, nous avons proposé d’ajouter une `with` expression, similaire à un initialiseur d’objet.</span><span class="sxs-lookup"><span data-stu-id="84242-127">To achieve this, it's proposed that we add a `with` expression, analogous to an object initializer.</span></span> <span data-ttu-id="84242-128">Voici un exemple d’utilisation :</span><span class="sxs-lookup"><span data-stu-id="84242-128">Sample usage would be as follows:</span></span>

```C#
var userInfo = new UserInfo() 
{
    Username = "andy",
    Email = "angocke@microsoft.com",
    IsAdmin = true
};
var newUserName = userInfo with { Username = "angocke" };
```

<span data-ttu-id="84242-129">L' `newUserName` objet résultant serait une copie de `userInfo` , avec la `Username` valeur "angocke".</span><span class="sxs-lookup"><span data-stu-id="84242-129">The resulting `newUserName` object would be a copy of `userInfo`, with `Username` set to "angocke".</span></span>
<span data-ttu-id="84242-130">Le CodeGen sur l' `with` expression est également similaire à l’initialiseur d’objet : un nouvel objet est construit, puis l' `initonly` `Username` accesseur Set est appelé dans le corps de la méthode.</span><span class="sxs-lookup"><span data-stu-id="84242-130">The codegen on the `with` expression would also be similar to the object initializer: a new object is constructed, and then the `initonly` `Username` setter would be called in the method body.</span></span>

<span data-ttu-id="84242-131">Bien entendu, la différence ici est que le nouvel objet en cours de construction n’est pas une simple création d’objet, il s’agit d’un doublon de l’objet d’origine.</span><span class="sxs-lookup"><span data-stu-id="84242-131">Of course, the difference here is that the new object being constructed is not a simple new object creation, it is a duplicate of the original object.</span></span> <span data-ttu-id="84242-132">Pour fournir cette fonctionnalité, nous exigeons que l’objet fournisse un « avec constructeur » qui fournit un objet dupliqué.</span><span class="sxs-lookup"><span data-stu-id="84242-132">To provide this functionality, we require that the object provide a "With constructor" that provides a duplicate object.</span></span> <span data-ttu-id="84242-133">Voici un exemple de `With` constructeur :</span><span class="sxs-lookup"><span data-stu-id="84242-133">A sample `With` constructor would look like:</span></span>

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

<span data-ttu-id="84242-134">En particulier, l' `with` expression définit des `initonly` membres, tout comme l’initialiseur d’objet. ainsi, pour prendre en charge la vérification, nous devons nous assurer que l’objet ne peut pas avoir été publié avant la `initonly` définition des membres.</span><span class="sxs-lookup"><span data-stu-id="84242-134">Notably, the `with` expression will set `initonly` members, just like the object initializer, so to support verification we must ensure that the object cannot have been published before the `initonly` members are set.</span></span> <span data-ttu-id="84242-135">Pour appliquer cela, l' `WithConstructor` attribut (ou la syntaxe équivalente) applique une nouvelle règle pour la méthode : toutes les instructions Return doivent contenir directement une expression de création d’objet, éventuellement avec un initialiseur d’objet.</span><span class="sxs-lookup"><span data-stu-id="84242-135">To enforce this, the `WithConstructor` attribute (or equivalent syntax) will enforce a new rule for the method: all return statements must directly contain an object creation expression, possibly with an object initializer.</span></span>

<span data-ttu-id="84242-136">Si le `With` constructeur requiert une validation, l’utilisateur peut introduire un constructeur pour effectuer cette validation, par exemple</span><span class="sxs-lookup"><span data-stu-id="84242-136">If the `With` constructor requires validation, the user may introduce a constructor to do that validation, e.g.</span></span>

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

<span data-ttu-id="84242-137">Le dernier élément de complexité associé à `With` est l’héritage.</span><span class="sxs-lookup"><span data-stu-id="84242-137">The last piece of complexity associated with `With` is inheritance.</span></span> <span data-ttu-id="84242-138">Si votre enregistrement est extensible, vous devrez fournir un nouveau `With` pour la sous-classe.</span><span class="sxs-lookup"><span data-stu-id="84242-138">If your record is extensible, you will need to provide a new `With` for the subclass.</span></span> <span data-ttu-id="84242-139">Pour ce faire, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="84242-139">This can be achieved as follows:</span></span>

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

<span data-ttu-id="84242-140">Notez un élément de complexité supplémentaire ici : pour substituer le `With` constructeur avec le type dérivé, le langage devra également prendre en charge les types de retour covariants dans les substitutions.</span><span class="sxs-lookup"><span data-stu-id="84242-140">Note one additional piece of complexity here: in order to override the `With` constructor with the derived type the language will also need to support covariant return types in overrides.</span></span>
<span data-ttu-id="84242-141">Il existe déjà une proposition distincte pour [cette fonctionnalité.](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md)</span><span class="sxs-lookup"><span data-stu-id="84242-141">There is already a separate proposal for this feature [here](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md).</span></span>

<span data-ttu-id="84242-142">**Inconvénients**</span><span class="sxs-lookup"><span data-stu-id="84242-142">**Drawbacks**</span></span>

- <span data-ttu-id="84242-143">Le fait que toutes les instructions return dans `WithConstructor` s contiennent des expressions d’objet est restrictif.</span><span class="sxs-lookup"><span data-stu-id="84242-143">Making all return statements in `WithConstructor`s contain new object expressions is restrictive.</span></span>
  <span data-ttu-id="84242-144">Cela peut être atténué par l’analyse de Flow qui garantit que le nouvel objet n’échappe pas à la méthode</span><span class="sxs-lookup"><span data-stu-id="84242-144">This could be possibly be mitigated by flow analysis that ensures the new object doesn't escape the method</span></span>
- <span data-ttu-id="84242-145">La prise en charge de la variance dans les substitutions via les astuces du compilateur nécessite des méthodes stub, qui vont croître augmentera avec la profondeur d’héritage.</span><span class="sxs-lookup"><span data-stu-id="84242-145">Supporting variance in overrides through compiler tricks will require stub methods, which will grow quadratically with the inheritance depth.</span></span> <span data-ttu-id="84242-146">La nécessité d’une méthode stub est due à une condition d’exécution qui remplace exactement les signatures.</span><span class="sxs-lookup"><span data-stu-id="84242-146">The need for a stub method is due to a runtime requirement that override signatures match exactly.</span></span> <span data-ttu-id="84242-147">Si la spécification du runtime a été relâchée, les méthodes stub ne sont pas nécessaires du tout.</span><span class="sxs-lookup"><span data-stu-id="84242-147">If the runtime requirement were loosened, the stub methods would not be required at all.</span></span>
- <span data-ttu-id="84242-148">L’utilisation de constructeurs chaînés de la forme `Type(Type original)` réserve effectivement ce constructeur pour l’utilisation du modèle.</span><span class="sxs-lookup"><span data-stu-id="84242-148">Using chained constructors of the form `Type(Type original)` effectively reserves that constructor for the use of the pattern.</span></span> <span data-ttu-id="84242-149">Étant donné que les constructeurs ont une sémantique unique et ne peuvent pas être renommés, cela peut être limité.</span><span class="sxs-lookup"><span data-stu-id="84242-149">Since constructors have unique semantics and cannot be re-named this could be limiting.</span></span>


## <a name="wrapping-it-all-up-records"></a><span data-ttu-id="84242-150">Encapsuler tout : enregistrements</span><span class="sxs-lookup"><span data-stu-id="84242-150">Wrapping it all up: Records</span></span>

<span data-ttu-id="84242-151">Les fonctionnalités ci-dessus permettent un style de programmation très difficile.</span><span class="sxs-lookup"><span data-stu-id="84242-151">The above features enable a style of programming that was very difficult before.</span></span> <span data-ttu-id="84242-152">Mais même avec les nouvelles fonctionnalités, elles peuvent être très détaillées et sujettes aux erreurs pour annoter tout le reste.</span><span class="sxs-lookup"><span data-stu-id="84242-152">But even with the new features it could be quite verbose and error prone to annotate everything yourself.</span></span> <span data-ttu-id="84242-153">Il y a également quelques éléments, comme Equals et GetHashCode, qui peuvent déjà être écrits aujourd’hui, c’est tout simplement laborieux.</span><span class="sxs-lookup"><span data-stu-id="84242-153">There are also a few items, like Equals and GetHashCode, which can already be written today, it's just laborious.</span></span>
<span data-ttu-id="84242-154">En outre, une faille importante en matière d’implémentation de l’égalité sur ces nouvelles primitives est que l’égalité structurelle est quelque chose qui doit changer avec votre type de données à mesure que de nouvelles données sont ajoutées, mais lorsque vous la gérez manuellement, il est probable que ces éléments peuvent être désynchronisés.</span><span class="sxs-lookup"><span data-stu-id="84242-154">Moreover, a significant flaw in implementing equality on top of these new primitives is that structural equality is something that should change with your data type as new data is added, but when handling it manually it is likely that these things can get out of sync.</span></span>

<span data-ttu-id="84242-155">Par conséquent, il est proposé que C# prenne en charge la nouvelle syntaxe pour les enregistrements, et non pour fournir de nouvelles fonctionnalités, mais pour définir des valeurs par défaut et générer du code conçu pour être utilisé dans les enregistrements.</span><span class="sxs-lookup"><span data-stu-id="84242-155">Therefore, it is proposed that C# support new syntax for records, not for providing new features, but for setting defaults and generating code designed for use in records.</span></span> <span data-ttu-id="84242-156">L’exemple de syntaxe ressemble à</span><span class="sxs-lookup"><span data-stu-id="84242-156">Example syntax would look like</span></span>

```C#
data class UserInfo
{
    public string Username { get; }
    public string Email { get; }
    public bool IsAdmin { get; } = false;
}
```

<span data-ttu-id="84242-157">Le code généré pour cette classe considérerait tous les champs publics et les propriétés automatiques comme des membres structurels de l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="84242-157">The generated code for this class would regard all public fields and auto-properties as structural members of the record.</span></span> <span data-ttu-id="84242-158">Les membres d’enregistrement peuvent être personnalisés à l’aide d’un nouvel `RecordMember(bool)` attribut qui peut être utilisé pour inclure ou exclure des membres.</span><span class="sxs-lookup"><span data-stu-id="84242-158">Record members could be customized using a new `RecordMember(bool)` attribute that could be used to either include or exclude members.</span></span> <span data-ttu-id="84242-159">Les membres d’enregistrement sont `initonly` par défaut et l’égalité est générée automatiquement pour la classe en fonction des membres d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="84242-159">Record members would be `initonly` by default and equality would be autogenerated for the class based on the record members.</span></span> <span data-ttu-id="84242-160">À tout moment, le comportement de ces membres peut être personnalisé simplement en les déclarant dans la source.</span><span class="sxs-lookup"><span data-stu-id="84242-160">At any point the behavior of these members could be customized simply by declaring them in source.</span></span> <span data-ttu-id="84242-161">L’implémentation écrite par l’utilisateur remplacerait l’implémentation par défaut dans toutes les utilisations de modèle.</span><span class="sxs-lookup"><span data-stu-id="84242-161">The user-written implementation would replace the default implementation in all pattern usage.</span></span>

<span data-ttu-id="84242-162">Notez que l’égalité dans le visage de l’héritage est complexe, mais semble avoir été correctement résolue dans la [proposition autres enregistrements](records.md).</span><span class="sxs-lookup"><span data-stu-id="84242-162">Note that equality in the face of inheritance is complex, but seems to have been adequately solved in the [other records proposal](records.md).</span></span>

## <a name="primary-constructors"></a><span data-ttu-id="84242-163">Constructeurs principaux</span><span class="sxs-lookup"><span data-stu-id="84242-163">Primary constructors</span></span>

<span data-ttu-id="84242-164">La proposition d’enregistrement précédente a également inclus une nouvelle syntaxe pour une liste de paramètres sur le type lui-même, par exemple</span><span class="sxs-lookup"><span data-stu-id="84242-164">Previous record proposal have also included a new syntax for a parameter list on the type itself, e.g.</span></span>

```C#
class Point(int X, int Y);
```

<span data-ttu-id="84242-165">Dans la nouvelle conception, la liste de paramètres serait une fonctionnalité C# orthogonale, qui pourrait être intégrée correctement aux enregistrements.</span><span class="sxs-lookup"><span data-stu-id="84242-165">In the new design, the parameter list would be an orthogonal C# feature, which could be cleanly integrated with records.</span></span> <span data-ttu-id="84242-166">Si un constructeur principal est inclus dans un enregistrement, il aurait de nouvelles valeurs par défaut, tout comme les champs publics et les propriétés automatiques : les paramètres dans le constructeur principal seraient utilisés pour générer des propriétés de membre d’enregistrement publiques portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="84242-166">If a primary constructor is included in a record, it would have new defaults, just like public fields and auto-properties: the parameters in the primary constructor would be used to generate public record-member properties with the same name.</span></span> <span data-ttu-id="84242-167">En outre, le constructeur principal peut maintenant être utilisé pour générer automatiquement un destructor.</span><span class="sxs-lookup"><span data-stu-id="84242-167">In addition, the primary constructor could now be used to auto-generate a deconstructor.</span></span>

<span data-ttu-id="84242-168">Par exemple, l’enregistrement suivant avec un constructeur principal</span><span class="sxs-lookup"><span data-stu-id="84242-168">For example, the following record with a primary constructor</span></span>

```C#
data class Point(int X, int Y);
```

<span data-ttu-id="84242-169">serait équivalent à</span><span class="sxs-lookup"><span data-stu-id="84242-169">would be equivalent to</span></span>

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

<span data-ttu-id="84242-170">et la génération finale de la version ci-dessus serait</span><span class="sxs-lookup"><span data-stu-id="84242-170">and the final generation of the above would be</span></span>

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

<span data-ttu-id="84242-171">Notez que nous avons pris en compte une autre information pour une classe de données avec un constructeur principal : au lieu de définir les champs primaires dans le constructeur protégé généré, nous déléguons au constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="84242-171">Note that we've taken one other piece of information into account for a data class with a primary constructor: instead of setting the primary fields inside the generated protected constructor, we delegate to the primary constructor.</span></span> <span data-ttu-id="84242-172">Si la classe point avait un autre membre d’enregistrement non primaire, par exemple</span><span class="sxs-lookup"><span data-stu-id="84242-172">If the Point class had another non-primary record member, e.g.</span></span>

```C#
data class Point(int X, int Y)
{
    public int Z { get; }
}
```

<span data-ttu-id="84242-173">cela modifierait ensuite le constructeur protégé généré comme suit :</span><span class="sxs-lookup"><span data-stu-id="84242-173">then that would change the generated protected constructor as follows:</span></span>

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

<span data-ttu-id="84242-174">En particulier, cela ne répond pas à l’héritage des enregistrements avec les constructeurs principaux.</span><span class="sxs-lookup"><span data-stu-id="84242-174">Notably, this doesn't answer what to do about inheritance of records with primary constructors.</span></span> <span data-ttu-id="84242-175">Par exemple,</span><span class="sxs-lookup"><span data-stu-id="84242-175">For instance,</span></span>

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A;
```

<span data-ttu-id="84242-176">Plutôt que de résoudre de manière arbitraire, une approche plus explicite peut exiger qu’une liste de paramètres soit fournie avec la liste de base, par exemple</span><span class="sxs-lookup"><span data-stu-id="84242-176">Rather than resolving in an arbitrary manner, a more explicit approach could require that a parameter list be provided with the base list, e.g.</span></span>

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A(X, Y);
```

<span data-ttu-id="84242-177">La liste de paramètres dans la liste de base est ensuite appliquée à un `base` appel dans le constructeur principal généré :</span><span class="sxs-lookup"><span data-stu-id="84242-177">The parameter list in the base list would then be applied to a `base` call in the generated primary constructor:</span></span>

```C#
class B
{
    // ..
    public B(int x, int y, int z)
    : base(x, y)
    // ..
}
```

<span data-ttu-id="84242-178">Quant à ce qu’un constructeur principal peut signifier en dehors d’un enregistrement, il est toujours ouvert pour une proposition supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="84242-178">As for what a primary constructor could mean outside of a record, that is still open to further proposal.</span></span>
