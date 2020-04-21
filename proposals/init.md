---
ms.openlocfilehash: ebbdab6d121f3001ac34a953b3de09768cda6344
ms.sourcegitcommit: 3f177e90b12e39d4d28f8bb1064df81a8e5912ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726067"
---
<a name="init-only-members"></a><span data-ttu-id="27223-101">Init Seuls les membres</span><span class="sxs-lookup"><span data-stu-id="27223-101">Init Only Members</span></span>
=====

## <a name="summary"></a><span data-ttu-id="27223-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="27223-102">Summary</span></span>
<span data-ttu-id="27223-103">Cette proposition ajoute le concept de propriétés init seulement à C.</span><span class="sxs-lookup"><span data-stu-id="27223-103">This proposal adds the concept of init only properties to C#.</span></span> <span data-ttu-id="27223-104">Ces propriétés peuvent être définies au `get` point de création d’objets, mais ne deviennent effectivement qu’une fois la création d’objet terminée.</span><span class="sxs-lookup"><span data-stu-id="27223-104">These properties can be set at the point of object creation but become effectively `get` only once object creation has completed.</span></span> <span data-ttu-id="27223-105">Cela permet un modèle immuable beaucoup plus flexible dans le C.</span><span class="sxs-lookup"><span data-stu-id="27223-105">This allows for a much more flexible immutable model in C#.</span></span> 

## <a name="motivation"></a><span data-ttu-id="27223-106">Motivation</span><span class="sxs-lookup"><span data-stu-id="27223-106">Motivation</span></span>
<span data-ttu-id="27223-107">Les mécanismes sous-jacents pour la construction de données immuables dans le C n’ont pas changé depuis 1,0.</span><span class="sxs-lookup"><span data-stu-id="27223-107">The underlying mechanisms for building immutable data in C# haven't changed since 1.0.</span></span> <span data-ttu-id="27223-108">Ils restent :</span><span class="sxs-lookup"><span data-stu-id="27223-108">They remain:</span></span>

1. <span data-ttu-id="27223-109">Déclarer les `readonly`champs comme .</span><span class="sxs-lookup"><span data-stu-id="27223-109">Declaring fields as `readonly`.</span></span>
1. <span data-ttu-id="27223-110">Déclarer les propriétés `get` qui ne contiennent qu’un accesseur.</span><span class="sxs-lookup"><span data-stu-id="27223-110">Declaring properties that contain only a `get` accessor.</span></span>

<span data-ttu-id="27223-111">Ces mécanismes sont efficaces pour permettre la construction de données immuables, mais ils le font en ajoutant des coûts au code de la plaque chauffante des types et en optant ces types hors des caractéristiques comme l’objet et les initialisateurs de collecte.</span><span class="sxs-lookup"><span data-stu-id="27223-111">These mechanisms are effective at allowing the construction of immutable data but they do so by adding cost to the boilerplate code of types and opting such types out of features like object and collection initializers.</span></span> <span data-ttu-id="27223-112">Cela signifie que les développeurs doivent choisir entre la facilité d’utilisation et l’immuabilité.</span><span class="sxs-lookup"><span data-stu-id="27223-112">This means developers must choose between ease of use and immutability.</span></span>

<span data-ttu-id="27223-113">Un simple objet immuable comme `Point` nécessite deux fois plus de code de plaque de chaudière pour soutenir la construction que de déclarer le type.</span><span class="sxs-lookup"><span data-stu-id="27223-113">A simple immutable object like `Point` requires twice as much boiler plate code to support construction as it does to declare the type.</span></span> <span data-ttu-id="27223-114">Plus le type est grand, plus le coût de cette plaque de chaudière est élevé :</span><span class="sxs-lookup"><span data-stu-id="27223-114">The bigger the type the bigger the cost of this boiler plate:</span></span>

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

<span data-ttu-id="27223-115">L’accesseur `init` rend les objets immuables plus flexibles en permettant à l’appelant de muter les membres pendant l’acte de construction.</span><span class="sxs-lookup"><span data-stu-id="27223-115">The `init` accessor makes immutable objects more flexible by allowing the caller to mutate the members during the act of construction.</span></span> <span data-ttu-id="27223-116">Cela signifie que les propriétés immuables de l’objet peuvent participer à des initialisateurs d’objets et élimine ainsi le besoin de toutes les plaques chauffantes constructor dans le type.</span><span class="sxs-lookup"><span data-stu-id="27223-116">That means the object's immutable properties can participate in object initializers and thus removes the need for all constructor boilerplate in the type.</span></span> <span data-ttu-id="27223-117">Le `Point` type est maintenant simplement:</span><span class="sxs-lookup"><span data-stu-id="27223-117">The `Point` type is now simply:</span></span>

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

<span data-ttu-id="27223-118">Le consommateur peut alors utiliser des initialisateurs d’objets pour créer l’objet</span><span class="sxs-lookup"><span data-stu-id="27223-118">The consumer can then use object initializers to create the object</span></span>

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a><span data-ttu-id="27223-119">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="27223-119">Detailed Design</span></span>

### <a name="init-members"></a><span data-ttu-id="27223-120">membres init</span><span class="sxs-lookup"><span data-stu-id="27223-120">init members</span></span>
<span data-ttu-id="27223-121">Une propriété init only est `init` déclarée en `set` utilisant l’accesseur à la place de l’accesseur :</span><span class="sxs-lookup"><span data-stu-id="27223-121">An init only property is declared by using the `init` accessor in place of the `set` accessor:</span></span>

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

<span data-ttu-id="27223-122">Une propriété d’instance contenant un `init` accesseur est considérée comme définie dans les circonstances suivantes :</span><span class="sxs-lookup"><span data-stu-id="27223-122">An instance property containing an `init` accessor is considered settable in the following circumstances:</span></span>

- <span data-ttu-id="27223-123">Lors d’un initialisateur d’objets</span><span class="sxs-lookup"><span data-stu-id="27223-123">During an object initializer</span></span>
- <span data-ttu-id="27223-124">À l’intérieur d’un constructeur d’instance du type contenant ou dérivé, sur `this` ou`base`</span><span class="sxs-lookup"><span data-stu-id="27223-124">Inside an instance constructor of the containing or derived type, on `this` or `base`</span></span>
- <span data-ttu-id="27223-125">À `init` l’intérieur de l’accesseur de toute propriété, sur `this` ou`base`</span><span class="sxs-lookup"><span data-stu-id="27223-125">Inside the `init` accessor of any property, on `this` or `base`</span></span>

<span data-ttu-id="27223-126">Les heures ci-dessus dans lesquelles les `init` accesseurs sont définis sont collectivement désignés dans ce document comme la phase de construction de l’objet.</span><span class="sxs-lookup"><span data-stu-id="27223-126">The times above in which the `init` accessors are settable are collectively referred to in this document as the construction phase of the object.</span></span>

<span data-ttu-id="27223-127">Cela signifie `Student` que la classe peut être utilisée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="27223-127">This means the `Student` class can be used in the following ways:</span></span>

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

<span data-ttu-id="27223-128">Les règles `init` autour du moment où les accesseurs sont définis s’étendent sur les hiérarchies de type.</span><span class="sxs-lookup"><span data-stu-id="27223-128">The rules around when `init` accessors are settable extend across type hierarchies.</span></span> <span data-ttu-id="27223-129">Si le membre est accessible et que l’objet est connu pour être dans la phase de construction, le membre est défini.</span><span class="sxs-lookup"><span data-stu-id="27223-129">If the member is accessible and the object is known to be in the construction phase then the member is settable.</span></span> <span data-ttu-id="27223-130">Cela permet spécifiquement ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="27223-130">That specifically allows for the following:</span></span>

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

<span data-ttu-id="27223-131">Au moment `init` où un accesseur est invoqué, l’instance est connue pour être dans la phase de construction ouverte.</span><span class="sxs-lookup"><span data-stu-id="27223-131">At the point a `init` accessor is invoked the instance is known to be in the open construction phase.</span></span> <span data-ttu-id="27223-132">Par `init` conséquent, un accesseur est autorisé à prendre `set` les mesures suivantes en plus de ce qu’un accesseur normal peut faire:</span><span class="sxs-lookup"><span data-stu-id="27223-132">Hence an `init` accessor is allowed to take the following actions in addition to what a normal `set` accessor can do:</span></span>

1. <span data-ttu-id="27223-133">Appelez `init` d’autres `this` accesseurs disponibles par l’intermédiaire ou`base`</span><span class="sxs-lookup"><span data-stu-id="27223-133">Call other `init` accessors available through `this` or `base`</span></span>
1. <span data-ttu-id="27223-134">Attribuer `readonly` les champs déclarés sur le même type</span><span class="sxs-lookup"><span data-stu-id="27223-134">Assign `readonly` fields declared on the same type</span></span>

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

<span data-ttu-id="27223-135">La possibilité `readonly` d’attribuer `init` des champs à partir d’un accesseur est limitée aux champs déclarés sur le même type que l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="27223-135">The ability to assign `readonly` fields from an `init` accessor is limited to those fields declared on the same type as the accessor.</span></span> <span data-ttu-id="27223-136">Il ne peut `readonly` pas être utilisé pour attribuer des champs dans un type de base.</span><span class="sxs-lookup"><span data-stu-id="27223-136">It cannot be used to assign `readonly` fields in a base type.</span></span> <span data-ttu-id="27223-137">Cette règle garantit que les auteurs de type restent en contrôle sur le comportement de mutabilité de leur type.</span><span class="sxs-lookup"><span data-stu-id="27223-137">This rule ensures that type authors remain in control over the mutability behavior of their type.</span></span> <span data-ttu-id="27223-138">Les développeurs qui ne `init` souhaitent pas utiliser ne peuvent pas être touchés par d’autres types qui choisissent de le faire :</span><span class="sxs-lookup"><span data-stu-id="27223-138">Developers who do not wish to utilize `init` cannot be impacted from other types choosing to do so:</span></span>

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

<span data-ttu-id="27223-139">Quand `init` est utilisé dans une propriété virtuelle, puis tous `init`les remplacements doivent également être marqués comme .</span><span class="sxs-lookup"><span data-stu-id="27223-139">When `init` is used in a virtual property then all the overrides must also be marked as `init`.</span></span> <span data-ttu-id="27223-140">De même, il n’est `set` `init`pas possible de passer outre à un simple avec .</span><span class="sxs-lookup"><span data-stu-id="27223-140">Likewise it is not possible to override a simple `set` with `init`.</span></span>

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

<span data-ttu-id="27223-141">Une `interface` déclaration peut `init` également participer à l’initialisation de style par le biais du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="27223-141">An `interface` declaration can also participate in `init` style initialization via the following pattern:</span></span>

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

<span data-ttu-id="27223-142">Restrictions de cette fonctionnalité:</span><span class="sxs-lookup"><span data-stu-id="27223-142">Restrictions of this feature:</span></span>
- <span data-ttu-id="27223-143">L’accesseur `init` ne peut être utilisé que sur les propriétés d’instance</span><span class="sxs-lookup"><span data-stu-id="27223-143">The `init` accessor can only be used on instance properties</span></span>
- <span data-ttu-id="27223-144">Une propriété ne `init` peut `set` pas contenir à la fois un et un accesseur</span><span class="sxs-lookup"><span data-stu-id="27223-144">A property cannot contain both an `init` and `set` accessor</span></span>
- <span data-ttu-id="27223-145">Toutes les dérogations `init` d’une `init`propriété doivent avoir si la base avait .</span><span class="sxs-lookup"><span data-stu-id="27223-145">All overrides of a property must have `init` if the base had `init`.</span></span> <span data-ttu-id="27223-146">Cette règle s’applique également à la mise en œuvre de l’interface.</span><span class="sxs-lookup"><span data-stu-id="27223-146">This rule also applies to interface implementation.</span></span>

### <a name="metadata-encoding"></a><span data-ttu-id="27223-147">Codage de métadonnées</span><span class="sxs-lookup"><span data-stu-id="27223-147">Metadata encoding</span></span> 
<span data-ttu-id="27223-148">Les `init` accesseurs de propriété seront `set` émis en tant qu’accesseur standard `IsInitOnly`avec le type de retour marqué d’un modreq de .</span><span class="sxs-lookup"><span data-stu-id="27223-148">Property `init` accessors will be emitted as a standard `set` accessor with the return type marked with a modreq of `IsInitOnly`.</span></span> <span data-ttu-id="27223-149">Il s’agit d’un nouveau type qui aura la définition suivante:</span><span class="sxs-lookup"><span data-stu-id="27223-149">This is a new type which will have the following definition:</span></span>

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsInitOnly
    {
    }
}
```

<span data-ttu-id="27223-150">Le compilateur correspondra au type par nom complet.</span><span class="sxs-lookup"><span data-stu-id="27223-150">The compiler will match the type by full name.</span></span> <span data-ttu-id="27223-151">Il n’est pas nécessaire qu’il apparaisse dans la bibliothèque centrale.</span><span class="sxs-lookup"><span data-stu-id="27223-151">There is no requirement that it appear in the core library.</span></span> <span data-ttu-id="27223-152">S’il existe plusieurs types par ce nom, puis le compilateur va attacher break dans l’ordre suivant:</span><span class="sxs-lookup"><span data-stu-id="27223-152">If there are multiple types by this name then the compiler will tie break in the following order:</span></span>

1. <span data-ttu-id="27223-153">Celui défini dans le projet en cours de compilation</span><span class="sxs-lookup"><span data-stu-id="27223-153">The one defined in the project being compiled</span></span>
1. <span data-ttu-id="27223-154">Celui défini dans corelib</span><span class="sxs-lookup"><span data-stu-id="27223-154">The one defined in corelib</span></span>

<span data-ttu-id="27223-155">Si aucun de ces éléments n’existe, une erreur d’ambiguïté type sera émise.</span><span class="sxs-lookup"><span data-stu-id="27223-155">If neither of these exist then a type ambiguity error will be issued.</span></span>

<span data-ttu-id="27223-156">La conception `IsInitOnly` pour est futher couvert dans [ce numéro](https://github.com/dotnet/runtime/issues/34978)</span><span class="sxs-lookup"><span data-stu-id="27223-156">The design for `IsInitOnly` is futher covered in [this issue](https://github.com/dotnet/runtime/issues/34978)</span></span>

## <a name="questions"></a><span data-ttu-id="27223-157">Questions</span><span class="sxs-lookup"><span data-stu-id="27223-157">Questions</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="27223-158">Changements cassants</span><span class="sxs-lookup"><span data-stu-id="27223-158">Breaking changes</span></span>
<span data-ttu-id="27223-159">L’un des principaux points de pivot dans la façon dont cette fonctionnalité est codée se résumera à la question suivante:</span><span class="sxs-lookup"><span data-stu-id="27223-159">One of the main pivot points in how this feature is encoded will come down to the following question:</span></span> 

> <span data-ttu-id="27223-160">Est-ce un changement `init` de `set`rupture binaire à remplacer par ?</span><span class="sxs-lookup"><span data-stu-id="27223-160">Is it a binary breaking change to replace `init` with `set`?</span></span>

<span data-ttu-id="27223-161">Remplacer `init` par `set` et ainsi rendre une propriété entièrement facilitable n’est jamais une source de rupture de changement sur une propriété non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="27223-161">Replacing `init` with `set` and thus making a property fully writable is never a source breaking change on a non-virtual property.</span></span> <span data-ttu-id="27223-162">Il élargit simplement l’ensemble des scénarios où la propriété peut être écrite.</span><span class="sxs-lookup"><span data-stu-id="27223-162">It simply expands the set of scenarios where the property can be written.</span></span> <span data-ttu-id="27223-163">Le seul comportement en question est de savoir si oui ou non cela reste un changement binaire de rupture.</span><span class="sxs-lookup"><span data-stu-id="27223-163">The only behavior in question is whether or not this remains a binary breaking change.</span></span>

<span data-ttu-id="27223-164">Si nous voulons faire `init` le `set` changement de à une source et binaire changement compatible alors il va forcer notre main sur le modreq vs attributs décision ci-dessous parce qu’il exclura modreqs comme une âmeution.</span><span class="sxs-lookup"><span data-stu-id="27223-164">If we want to make the change of `init` to `set` a source and binary compatible change then it will force our hand on the modreq vs. attributes decision below because it will rule out modreqs as a soulution.</span></span> <span data-ttu-id="27223-165">Si d’autre part cela est considéré comme un non-intéressant, alors cela rendra le modreq vs décision d’attribut moins percutant.</span><span class="sxs-lookup"><span data-stu-id="27223-165">If on the other hand this is seen as a non-interesting then this will make the modreq vs. attribute decision less impactful.</span></span>

<span data-ttu-id="27223-166">**Résolution** Ce scénario n’est pas considéré comme convaincant par LDM.</span><span class="sxs-lookup"><span data-stu-id="27223-166">**Resolution** This scenario is not seen as compelling by LDM.</span></span>

### <a name="modreqs-vs-attributes"></a><span data-ttu-id="27223-167">Modreqs vs attributs</span><span class="sxs-lookup"><span data-stu-id="27223-167">Modreqs vs. attributes</span></span>
<span data-ttu-id="27223-168">La stratégie `init` d’émission pour les accesseurs de propriété doit choisir entre l’utilisation d’attributs ou de modréqs lors de l’émission pendant les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="27223-168">The emit strategy for `init` property accessors must choose between using attributes or modreqs when emitting during metadata.</span></span> <span data-ttu-id="27223-169">Ceux-ci ont des compromis différents qui doivent être pris en considération.</span><span class="sxs-lookup"><span data-stu-id="27223-169">These have different trade offs that need to be considered.</span></span>

<span data-ttu-id="27223-170">Annoter un accesseur de l’ensemble de propriété avec une déclaration modreq signifie compilateurs conformes CLI ignorera l’accesseur à moins qu’il ne comprend le modreq.</span><span class="sxs-lookup"><span data-stu-id="27223-170">Annotating a property set accessor with a modreq declaration means CLI compliant compilers will ignore the accessor unless it understands the modreq.</span></span> <span data-ttu-id="27223-171">Cela signifie que seuls `init` les compilateurs au courant liront le membre.</span><span class="sxs-lookup"><span data-stu-id="27223-171">That means only compilers aware of `init` will read the member.</span></span> <span data-ttu-id="27223-172">Les compilateurs `init` qui `set` ne connaissent pas l’ignorance du membre et ne traiteront donc pas accidentellement la propriété comme l’a lu ou écrit.</span><span class="sxs-lookup"><span data-stu-id="27223-172">Compilers unaware of `init` will ignore the `set` member and hence will not accidentally treat the property as read / write.</span></span> 

<span data-ttu-id="27223-173">L’inconvénient du modreq est `init` devient une `set` partie de la signature binaire de l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="27223-173">The downside of modreq is `init` becomes a part of the binary signature of the `set` accessor.</span></span> <span data-ttu-id="27223-174">L’ajout `init` ou la suppression brisera la compatibilité binaire de l’application.</span><span class="sxs-lookup"><span data-stu-id="27223-174">Adding or removing `init` will break binary compatbility of the application.</span></span>

<span data-ttu-id="27223-175">L’utilisation d’attributs `set` pour annoter l’accesseur signifie que seuls les compilateurs qui comprennent l’attribut sauront limiter l’accès à celui-ci.</span><span class="sxs-lookup"><span data-stu-id="27223-175">Using attributes to annotate the `set` accessor means that only compilers which understand the attribute will know to limit access to it.</span></span> <span data-ttu-id="27223-176">Un compilateur `init` qui ne connaît pas le verra comme une simple propriété de lecture/ d’écriture et permettra l’accès.</span><span class="sxs-lookup"><span data-stu-id="27223-176">A compiler unaware of `init` will see it as a simple read / write property and allow access.</span></span>

<span data-ttu-id="27223-177">Cela signifierait apparemment que cette décision est un choix entre une sécurité supplémentaire au détriment de la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="27223-177">This would seemingly mean this decision is a choice between extra safety at the expense of binary compatibility.</span></span> <span data-ttu-id="27223-178">Creuser dans un peu la sécurité supplémentaire n’est pas exactement ce qu’il semble.</span><span class="sxs-lookup"><span data-stu-id="27223-178">Digging in a bit the extra safety is not exactly what it seems.</span></span> <span data-ttu-id="27223-179">Il ne protégera pas contre les circonstances suivantes :</span><span class="sxs-lookup"><span data-stu-id="27223-179">It will not protect against the following circumstances:</span></span>

1. <span data-ttu-id="27223-180">Réflexion `public` sur les membres</span><span class="sxs-lookup"><span data-stu-id="27223-180">Reflection over `public` members</span></span>
1. <span data-ttu-id="27223-181">L’utilisation de`dynamic`</span><span class="sxs-lookup"><span data-stu-id="27223-181">The use of `dynamic`</span></span> 
1. <span data-ttu-id="27223-182">Compilateurs qui ne reconnaissent pas les modréqs</span><span class="sxs-lookup"><span data-stu-id="27223-182">Compilers that don't recognize modreqs</span></span>

<span data-ttu-id="27223-183">Il convient également de considérer que, lorsque nous aurons terminé `init` les règles de vérification de l’IL pour .NET 5, sera l’une de ces règles.</span><span class="sxs-lookup"><span data-stu-id="27223-183">It should also be considered that, when we complete the IL verification rules for .NET 5, `init` will be one of those rules.</span></span> <span data-ttu-id="27223-184">Cela signifie que l’application supplémentaire sera obtenue en vérifiant simplement les compilateurs émettant l’IL vérifiable.</span><span class="sxs-lookup"><span data-stu-id="27223-184">That means extra enforcement will be gained from simply verifying compilers emitting verifiable IL.</span></span>

<span data-ttu-id="27223-185">Les langues primaires pour .NET (C, F et VB) `init` seront toutes mises à jour pour reconnaître ces accesseurs.</span><span class="sxs-lookup"><span data-stu-id="27223-185">The primary languages for .NET (C#, F# and VB) will all be updated to recognize these `init` accessors.</span></span> <span data-ttu-id="27223-186">D’où le seul scénario réaliste ici est quand `init` un compilateur C 9 émet des propriétés et ils sont vus par un ensemble d’outils plus anciens tels que C 8, VB 15, etc ... C 8.</span><span class="sxs-lookup"><span data-stu-id="27223-186">Hence the only realistic scenario here is when a C# 9 compiler emits `init` properties and they are seen by an older toolset such as C# 8, VB 15, etc ... C# 8.</span></span> <span data-ttu-id="27223-187">C’est le compromis d’examiner et de peser contre la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="27223-187">That is the trade off to consider and weigh against binary compatibility.</span></span>

<span data-ttu-id="27223-188">**Note** Cette discussion s’applique principalement uniquement aux membres, et non aux domaines.</span><span class="sxs-lookup"><span data-stu-id="27223-188">**Note** This discussion primarily applies to members only, not to fields.</span></span> <span data-ttu-id="27223-189">Bien `init` que les champs aient été rejetés par LDM, ils sont toujours intéressants à considérer pour la discussion modreq vs attribut.</span><span class="sxs-lookup"><span data-stu-id="27223-189">While `init` fields were rejected by LDM they are still interesting to consider for the modreq vs. attribute discussion.</span></span> <span data-ttu-id="27223-190">La `init` caractéristique pour les champs est `readonly`un assouplissement de la restriction existante de .</span><span class="sxs-lookup"><span data-stu-id="27223-190">The `init` feature for fields is a relaxation of the existing restriction of `readonly`.</span></span> <span data-ttu-id="27223-191">Cela signifie que si `readonly` nous émettons les champs comme un attribut, il n’y `readonly`a aucun risque de compilateurs plus âgés mal utiliser le champ parce qu’ils reconnaîtraient déjà .</span><span class="sxs-lookup"><span data-stu-id="27223-191">That means if we emit the fields as `readonly` + an attribute there is no risk of older compilers mis-using the field because they would already recognize `readonly`.</span></span> <span data-ttu-id="27223-192">Par conséquent, l’utilisation d’un modreq ici n’ajoute pas de protection supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="27223-192">Hence using a modreq here doesn't add any extra protection.</span></span>

<span data-ttu-id="27223-193">**Résolution** La fonction utilisera un modreq `init` pour encoder le setter de propriété.</span><span class="sxs-lookup"><span data-stu-id="27223-193">**Resolution** The feature will use a modreq to encode the property `init` setter.</span></span> <span data-ttu-id="27223-194">Les facteurs impérieux étaient (sans ordre particulier) :</span><span class="sxs-lookup"><span data-stu-id="27223-194">The compelling factors were (in no particular order):</span></span>

* <span data-ttu-id="27223-195">Désir de décourager les compilateurs plus âgés de violer `init` la sémantique</span><span class="sxs-lookup"><span data-stu-id="27223-195">Desire to discourage older compilers from violating `init` semantics</span></span>
* <span data-ttu-id="27223-196">Désir de faire l’ajout `virtual` ou `interface` la suppression `init` dans une déclaration ou à la fois une source et binaire changement de rupture.</span><span class="sxs-lookup"><span data-stu-id="27223-196">Desire to make adding or removing `init` in a `virtual` declaration or `interface` both a source and binary breaking change.</span></span>

<span data-ttu-id="27223-197">Étant donné qu’il n’y avait pas non plus de soutien significatif pour supprimer `init` pour être un changement compatible binaire, il a fait le choix d’utiliser modreq directement en avant.</span><span class="sxs-lookup"><span data-stu-id="27223-197">Given there was also no significant support for removing `init` to be a binary compatible change it made the choice of using modreq straight forward.</span></span>

### <a name="init-vs-initonly"></a><span data-ttu-id="27223-198">init vs initonly</span><span class="sxs-lookup"><span data-stu-id="27223-198">init vs. initonly</span></span>
<span data-ttu-id="27223-199">Il y avait trois formes de syntaxe qui ont obtenu une considération significative au cours de notre réunion LDM:</span><span class="sxs-lookup"><span data-stu-id="27223-199">There were three syntax forms which got significant consideration during our LDM meeting:</span></span>

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

<span data-ttu-id="27223-200">**Résolution** Il n’y avait pas de syntaxe qui a été largement favorisée dans LDM.</span><span class="sxs-lookup"><span data-stu-id="27223-200">**Resolution** There was no syntax which was overwhelmingly favored in LDM.</span></span>

<span data-ttu-id="27223-201">Un point qui a attiré l’attention a été la `init` façon dont le choix de la syntaxe aurait un impact sur notre capacité à faire des membres comme une caractéristique générale à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="27223-201">One point which got significant attention was how the choice of syntax would impact our ability to do `init` members as a general feature in the future.</span></span>
<span data-ttu-id="27223-202">Choisir l’option 1 signifierait qu’il serait `init` difficile `get` de définir une propriété qui avait une méthode de style à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="27223-202">Choosing option 1 would mean that it would be difficult to define a property which had a `init` style `get` method in the future.</span></span> <span data-ttu-id="27223-203">Finalement, il a été décidé que `init` si nous décidions `init` d’aller de l’avant avec les membres `init set`généraux à l’avenir, nous pourrions permettre d’être un modificateur dans la liste des accessoirs de propriété ainsi qu’une main courte pour .</span><span class="sxs-lookup"><span data-stu-id="27223-203">Eventually it was decided that if we decided to go forward with general `init` members in future we could allow `init` to be a modifier in the property accessor list as well as a short hand for `init set`.</span></span> <span data-ttu-id="27223-204">Essentiellement, les deux déclarations suivantes seraient identiques.</span><span class="sxs-lookup"><span data-stu-id="27223-204">Essentially the following two declarations would be identical.</span></span>

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

<span data-ttu-id="27223-205">La décision a été `init` prise d’aller de l’avant en tant qu’accesseur autonome dans la liste des accessoirs de propriété.</span><span class="sxs-lookup"><span data-stu-id="27223-205">The decision was made to move forward with `init` as a standalone accessor in the property accessor list.</span></span>

### <a name="warn-on-failed-init"></a><span data-ttu-id="27223-206">Avertir sur init échoué</span><span class="sxs-lookup"><span data-stu-id="27223-206">Warn on failed init</span></span>
<span data-ttu-id="27223-207">Considérons le scénario suivant.</span><span class="sxs-lookup"><span data-stu-id="27223-207">Consider the following scenario.</span></span> <span data-ttu-id="27223-208">Un type déclare `init` un seul membre qui n’est pas placé dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="27223-208">A type declares an `init` only member which is not set in the constructor.</span></span> <span data-ttu-id="27223-209">Le code qui construit l’objet devrait-il obtenir un avertissement s’il n’a pas réussi à initialiser la valeur ?</span><span class="sxs-lookup"><span data-stu-id="27223-209">Should the code which constructs the object get a warning if they failed to initialize the value?</span></span>

<span data-ttu-id="27223-210">À ce stade, il est clair que le champ ne sera jamais défini et `private` a donc beaucoup de similitudes avec l’avertissement de ne pas initialiser les données.</span><span class="sxs-lookup"><span data-stu-id="27223-210">At that point it is clear the field will never be set and hence has a lot of similarities with the warning around failing to initialize `private` data.</span></span> <span data-ttu-id="27223-211">D’où un avertissement aurait apparemment une certaine valeur ici?</span><span class="sxs-lookup"><span data-stu-id="27223-211">Hence a warning would seemingly have some value here?</span></span>

<span data-ttu-id="27223-212">Il ya des inconvénients importants à cet avertissement si:</span><span class="sxs-lookup"><span data-stu-id="27223-212">There are significant downsides to this warning though:</span></span>
1. <span data-ttu-id="27223-213">Il complique l’histoire de `readonly` compatibilité de changer à `init`.</span><span class="sxs-lookup"><span data-stu-id="27223-213">It complicates the compatibility story of changing `readonly` to `init`.</span></span> 
1. <span data-ttu-id="27223-214">Il faut transporter des métadonnées supplémentaires pour désigner les membres qui doivent être parasés par l’appelant.</span><span class="sxs-lookup"><span data-stu-id="27223-214">It requires carrying additional metadata around to denote the members which are required to be initialized by the caller.</span></span>

<span data-ttu-id="27223-215">En outre, si nous croyons qu’il ya de la valeur ici dans le scénario global de forcer les créateurs d’objets à être avertis / erreur sur des domaines spécifiques, alors cela est probablement logique comme une caractéristique générale.</span><span class="sxs-lookup"><span data-stu-id="27223-215">Further if we believe there is value here in the overall scenario of forcing object creators to be warned / error'd about specific fields then this likely makes sense as a general feature.</span></span> <span data-ttu-id="27223-216">Il n’y a aucune `init` raison qu’il soit limité aux membres.</span><span class="sxs-lookup"><span data-stu-id="27223-216">There is no reason it should be limited to just `init` members.</span></span>

<span data-ttu-id="27223-217">**Résolution** Il n’y aura `init` pas d’avertissement sur la consommation de champs et de propriétés.</span><span class="sxs-lookup"><span data-stu-id="27223-217">**Resolution** There will be no warning on consumption of `init` fields and properties.</span></span>

<span data-ttu-id="27223-218">LDM veut avoir une discussion plus large sur l’idée des champs et des propriétés nécessaires.</span><span class="sxs-lookup"><span data-stu-id="27223-218">LDM wants to have a broader discussion on the idea of required fields and properties.</span></span> <span data-ttu-id="27223-219">Cela peut nous amener à revenir `init` et à reconsidérer notre position sur les membres et la validation.</span><span class="sxs-lookup"><span data-stu-id="27223-219">That may cause us to come back and reconsider our position on `init` members and validation.</span></span>

## <a name="allow-init-as-a-field-modifier"></a><span data-ttu-id="27223-220">Autoriser l’init en tant que modificateur de champ</span><span class="sxs-lookup"><span data-stu-id="27223-220">Allow init as a field modifier</span></span>
<span data-ttu-id="27223-221">De la `init` même manière peut servir d’accesseur de propriété, il pourrait également `init` servir de désignation sur les champs pour leur donner des comportements similaires que les propriétés.</span><span class="sxs-lookup"><span data-stu-id="27223-221">In the same way `init` can serve as a property accessor it could also serve as a designation on fields to give them similar behaviors as `init` properties.</span></span>
<span data-ttu-id="27223-222">Cela permettrait d’attribuer le champ avant que la construction ne soit terminée par le type, les types dérivés ou les initialisateurs d’objets.</span><span class="sxs-lookup"><span data-stu-id="27223-222">That would allow for the field to be assigned before construction was complete by the type, derived types, or object initializers.</span></span>

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

<span data-ttu-id="27223-223">Dans les métadonnées, ces champs `readonly` seraient marqués de la même manière que `init` les champs, mais avec un attribut ou un modreq supplémentaire pour indiquer qu’il s’agit de champs de style.</span><span class="sxs-lookup"><span data-stu-id="27223-223">In metadata these fields would be marked in the same way as `readonly` fields but with an additional attribute or modreq to indicate they are `init` style fields.</span></span> 

<span data-ttu-id="27223-224">**Résolution** LDM convient que cette proposition est saine, mais dans l’ensemble, le scénario s’est senti disjoint des propriétés.</span><span class="sxs-lookup"><span data-stu-id="27223-224">**Resolution** LDM agrees this proposal is sound but overall the scenario felt disjoint from properties.</span></span> <span data-ttu-id="27223-225">La décision était de `init` procéder uniquement avec les propriétés pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="27223-225">The decision was to proceed only with `init` properties for now.</span></span> <span data-ttu-id="27223-226">Cela a un niveau approprié `init` de flexibilité `readonly` car une propriété peut muter un champ sur le type déclarant de la propriété.</span><span class="sxs-lookup"><span data-stu-id="27223-226">This has a suitable level of flexibility as an `init` property can mutate a `readonly` field on the declaring type of the property.</span></span> <span data-ttu-id="27223-227">Cela sera réexaminé s’il y a une rétroaction importante des clients qui justifie le scénario.</span><span class="sxs-lookup"><span data-stu-id="27223-227">This will be reconsidered if there is significant customer feedback that justifies the scenario.</span></span>

### <a name="allow-init-as-a-type-modifier"></a><span data-ttu-id="27223-228">Autoriser l’init comme modificateur de type</span><span class="sxs-lookup"><span data-stu-id="27223-228">Allow init as a type modifier</span></span>
<span data-ttu-id="27223-229">De la même `readonly` manière, le `struct` modificateur peut être `readonly`appliqué `init` à un pour `struct` déclarer `class` automatiquement tous les `init`champs comme , le seul modificateur peut être déclaré sur un ou pour marquer automatiquement tous les champs comme .</span><span class="sxs-lookup"><span data-stu-id="27223-229">In the same way the `readonly` modifier can be applied to a `struct` to automatically declare all fields as `readonly`, the `init` only modifier can be declared on a `struct` or `class` to automatically mark all fields as `init`.</span></span>
<span data-ttu-id="27223-230">Cela signifie que les deux déclarations de type suivantes sont équivalentes :</span><span class="sxs-lookup"><span data-stu-id="27223-230">This means the following two type declarations are equivalent:</span></span>

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

<span data-ttu-id="27223-231">**Résolution** Cette fonctionnalité est trop *mignonne* ici et entre en conflit avec la `readonly struct` fonctionnalité sur laquelle il est basé.</span><span class="sxs-lookup"><span data-stu-id="27223-231">**Resolution** This feature is too *cute* here and conflicts with the `readonly struct` feature on which it is based.</span></span> <span data-ttu-id="27223-232">La `readonly struct` fonctionnalité est simple `readonly` en ce qu’elle s’applique à tous les membres : champs, méthodes, etc... La `init struct` fonction ne s’appliquerait qu’aux propriétés.</span><span class="sxs-lookup"><span data-stu-id="27223-232">The `readonly struct` feature is simple in that it applies `readonly` to all members: fields, methods, etc ... The `init struct` feature would only apply to properties.</span></span> <span data-ttu-id="27223-233">Cela finit par le rendre déroutant pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="27223-233">This actually ends up making it confusing for users.</span></span> 

<span data-ttu-id="27223-234">Étant `init` donné que ce n’est valable que sur certains aspects d’un type, nous avons rejeté l’idée de l’avoir comme modificateur de type.</span><span class="sxs-lookup"><span data-stu-id="27223-234">Given that `init` is only valid on certain aspects of a type we rejected the idea of having it as a type modifier.</span></span>

## <a name="considerations"></a><span data-ttu-id="27223-235">Considérations</span><span class="sxs-lookup"><span data-stu-id="27223-235">Considerations</span></span>

### <a name="compatibility"></a><span data-ttu-id="27223-236">Compatibilité</span><span class="sxs-lookup"><span data-stu-id="27223-236">Compatibility</span></span>
<span data-ttu-id="27223-237">La `init` fonctionnalité est conçue pour `get` être compatible avec les seules propriétés existantes.</span><span class="sxs-lookup"><span data-stu-id="27223-237">The `init` feature is designed to be compatible with existing `get` only properties.</span></span> <span data-ttu-id="27223-238">Plus précisément, il est censé être un `get` changement complètement additif pour une propriété qui est seulement aujourd’hui, mais désire plus flexbile création d’objets sémantiques.</span><span class="sxs-lookup"><span data-stu-id="27223-238">Specifically it is meant to be a completely additive change for a property which is `get` only today but desires more flexbile object creation semantics.</span></span>

<span data-ttu-id="27223-239">Prenons, par exemple, le type suivant :</span><span class="sxs-lookup"><span data-stu-id="27223-239">For example consider the following type:</span></span>

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

<span data-ttu-id="27223-240">L’ajout `init` à ces propriétés est un changement non-rupture:</span><span class="sxs-lookup"><span data-stu-id="27223-240">Adding `init` to these properties is a non-breaking change:</span></span>

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

### <a name="il-verification"></a><span data-ttu-id="27223-241">Vérification DE l’IL</span><span class="sxs-lookup"><span data-stu-id="27223-241">IL verification</span></span>
<span data-ttu-id="27223-242">Lorsque .NET Core décide de ré-implémenter la vérification `init` de l’IL, les règles devront être ajustées pour tenir compte des membres.</span><span class="sxs-lookup"><span data-stu-id="27223-242">When .NET Core decides to re-implement IL verification the rules will need to be adjusted to account for `init` members.</span></span> <span data-ttu-id="27223-243">Cela devra être inclus dans les modifications des règles `readonly` pour les as non mutants aux données.</span><span class="sxs-lookup"><span data-stu-id="27223-243">This will need to be included in the rule changes for non-mutating acess to `readonly` data.</span></span>

<span data-ttu-id="27223-244">Les règles de vérification de l’IL devront être divisées en deux parties :</span><span class="sxs-lookup"><span data-stu-id="27223-244">The IL verification rules will need to be broken into two parts:</span></span> 

1. <span data-ttu-id="27223-245">Permettre `init` aux membres `readonly` de mettre un champ.</span><span class="sxs-lookup"><span data-stu-id="27223-245">Allowing `init` members to set a `readonly` field.</span></span>
1. <span data-ttu-id="27223-246">Déterminer quand `init` un membre peut être légalement appelé.</span><span class="sxs-lookup"><span data-stu-id="27223-246">Determining when an `init` member can be legally called.</span></span>

<span data-ttu-id="27223-247">Le premier est un simple ajustement aux règles existantes.</span><span class="sxs-lookup"><span data-stu-id="27223-247">The first is a simple adjustment to the existing rules.</span></span> <span data-ttu-id="27223-248">Le vérificateur de l’IL `init` peut apprendre à reconnaître les `readonly` membres et à `this` partir de là, il a juste besoin d’envisager un champ à définir sur un tel membre.</span><span class="sxs-lookup"><span data-stu-id="27223-248">The IL verifier can be taught to recognize `init` members and from there it just needs to consider a `readonly` field to be settable on `this` in such a member.</span></span>

<span data-ttu-id="27223-249">La deuxième règle est plus compliquée.</span><span class="sxs-lookup"><span data-stu-id="27223-249">The second rule is more complicated.</span></span> <span data-ttu-id="27223-250">Dans le cas simple des initialisateurs d’objets, la règle est simple.</span><span class="sxs-lookup"><span data-stu-id="27223-250">In the simple case of object initializers the rule is straight forward.</span></span> <span data-ttu-id="27223-251">Il devrait être `init` légal d’appeler `new` les membres lorsque le résultat d’une expression est toujours sur la pile.</span><span class="sxs-lookup"><span data-stu-id="27223-251">It should be legal to call `init` members when the result of a `new` expression is still on the stack.</span></span> <span data-ttu-id="27223-252">C’est jusqu’à ce que la valeur a été stockée dans un local, élément `init` de tableau ou un champ ou passé comme un argument à une autre méthode, il sera toujours légal d’appeler les membres.</span><span class="sxs-lookup"><span data-stu-id="27223-252">That is until the value has been stored in a local, array element or field or passed as an argument to another method it will still be legal to call `init` members.</span></span> <span data-ttu-id="27223-253">Cela garantit qu’une fois `new` que le résultat de l’expression est publié sur un identifiant nommé (autre que) `this`alors il ne sera plus légal d’appeler les `init` membres.</span><span class="sxs-lookup"><span data-stu-id="27223-253">This ensures that once the result of the `new` expression is published to a named identifier (other than `this`) then it will no longer be legal to call `init` members.</span></span> 

<span data-ttu-id="27223-254">Le cas le plus compliqué `init` est cependant quand `await`nous mélangeons les membres, les initialisateurs d’objets et .</span><span class="sxs-lookup"><span data-stu-id="27223-254">The more complicated case though is when we mix `init` members, object initializers and `await`.</span></span> <span data-ttu-id="27223-255">Cela peut faire en sorte que l’objet nouvellement créé soit temporairement hissé dans une machine d’état et donc mis dans un champ.</span><span class="sxs-lookup"><span data-stu-id="27223-255">That can cause the newly created object to be temporarily hoisted into a state machine and hence put into a field.</span></span>

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

<span data-ttu-id="27223-256">Ici, le `new Student()` résultat sera hoised dans une machine d’état comme un champ avant l’ensemble de `Name` se produit.</span><span class="sxs-lookup"><span data-stu-id="27223-256">Here the result of `new Student()` will be hoised into a state machine as a field before the set of `Name` occurs.</span></span> <span data-ttu-id="27223-257">Le compilateur devra marquer ces champs hissés d’une manière que le vérificateur DE l’IL comprend qu’ils `init`ne sont pas accessibles à l’utilisateur et ne viole donc pas la sémantique prévue de .</span><span class="sxs-lookup"><span data-stu-id="27223-257">The compiler will need to mark such hoisted fields in a way that the IL verifier understands they're not user accessible and hence doesn't violate the intended semantics of `init`.</span></span>

### <a name="init-members"></a><span data-ttu-id="27223-258">membres init</span><span class="sxs-lookup"><span data-stu-id="27223-258">init members</span></span>
<span data-ttu-id="27223-259">Le `init` modificateur pourrait être étendu pour s’appliquer à tous les membres de l’instance.</span><span class="sxs-lookup"><span data-stu-id="27223-259">The `init` modifier could be extended to apply to all instance members.</span></span> <span data-ttu-id="27223-260">Cela généraliserait le `init` concept de construction d’objets pendant la construction d’objets et `init` permettrait aux types de déclarer les méthodes d’aide qui pourraient se faire dans le processus de construction pour initialiser les champs et les propriétés.</span><span class="sxs-lookup"><span data-stu-id="27223-260">This would generalize the concept of `init` during object construction and allow types to declare helper methods that could partipate in the construction process to initialize `init` fields and properties.</span></span>

<span data-ttu-id="27223-261">Ces membres auraient toutes les restricions qu’un `init` accesseur fait dans cette conception.</span><span class="sxs-lookup"><span data-stu-id="27223-261">Such members would have all the restricions that an `init` accessor does in this design.</span></span> <span data-ttu-id="27223-262">Le besoin est discutable cependant et cela peut être ajouté en toute sécurité dans une version future de la langue d’une manière compatible.</span><span class="sxs-lookup"><span data-stu-id="27223-262">The need is questionable though and this can be safely added in a future version of the language in a compatible manner.</span></span>

### <a name="generate-three-accessors"></a><span data-ttu-id="27223-263">Générer trois accesseurs</span><span class="sxs-lookup"><span data-stu-id="27223-263">Generate three accessors</span></span>
<span data-ttu-id="27223-264">Une mise `init` en œuvre potentielle des propriétés est de rendre `init` complètement séparé de `set`.</span><span class="sxs-lookup"><span data-stu-id="27223-264">One potential implementation of `init` properties is to make `init` completely separate from `set`.</span></span> <span data-ttu-id="27223-265">Cela signifie qu’une propriété peut potentiellement `get` `set` avoir `init`trois accessoiris différents: , et .</span><span class="sxs-lookup"><span data-stu-id="27223-265">That means that a property can potentially have three different accessors: `get`, `set` and `init`.</span></span>

<span data-ttu-id="27223-266">Cela a l’avantage potentiel de permettre l’utilisation de modreq pour faire respecter la justesse tout en maintenant la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="27223-266">This has the potential advantage of allowing the use of modreq to enforce correctness while maintaining binary compatibility.</span></span> <span data-ttu-id="27223-267">La mise en œuvre serait à peu près la suivante:</span><span class="sxs-lookup"><span data-stu-id="27223-267">The implementation would roughly be the following:</span></span>

1. <span data-ttu-id="27223-268">Un `init` accesseur est toujours émis `set`s’il y a un .</span><span class="sxs-lookup"><span data-stu-id="27223-268">An `init` accessor is always emitted if there is a `set`.</span></span> <span data-ttu-id="27223-269">Lorsqu’il n’est pas défini `set`par le développeur, il s’agit simplement d’une référence à .</span><span class="sxs-lookup"><span data-stu-id="27223-269">When not defined by the developer it is simply a reference to `set`.</span></span> 
1. <span data-ttu-id="27223-270">L’ensemble d’une propriété dans un `init` initialisateur d’objet sera toujours utilisé si présent, mais retomber à `set` si elle est manquante.</span><span class="sxs-lookup"><span data-stu-id="27223-270">The set of a property in an object initializer will always use `init` if present but fall back to `set` if it's missing.</span></span>

<span data-ttu-id="27223-271">Cela signifie qu’un développeur `init` peut toujours supprimer en toute sécurité d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="27223-271">This means that a developer can always safely delete `init` from a property.</span></span> 

<span data-ttu-id="27223-272">L’inconvénient de cette conception est `init` qui n’est utile `set`que si elle est **toujours** émise quand il ya un .</span><span class="sxs-lookup"><span data-stu-id="27223-272">The downside of this design is that is only useful if `init` is **always** emitted when there is a `set`.</span></span> <span data-ttu-id="27223-273">La langue ne peut `init` pas savoir si a été supprimé dans le `init` passé, il doit supposer qu’il était et donc le doit toujours être émis.</span><span class="sxs-lookup"><span data-stu-id="27223-273">The language can't know if `init` was deleted in the past, it has to assume it was and hence the `init` must always be emitted.</span></span> <span data-ttu-id="27223-274">Cela entraînerait une expansion importante des métadonnées et ne vaut tout simplement pas le coût de la compatibilité ici.</span><span class="sxs-lookup"><span data-stu-id="27223-274">That would cause a significant metadata expansion and is simply not worth the cost of the compatibility here.</span></span>
