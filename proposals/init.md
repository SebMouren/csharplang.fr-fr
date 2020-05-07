---
ms.openlocfilehash: f50299739321818a4877f593ee715f35540132b0
ms.sourcegitcommit: e006b4808d8c107dad2935348b57d51edbfaf9a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82820162"
---
<a name="init-only-setters"></a><span data-ttu-id="8c6a3-101">Les Setters init uniquement</span><span class="sxs-lookup"><span data-stu-id="8c6a3-101">Init Only Setters</span></span>
=====

## <a name="summary"></a><span data-ttu-id="8c6a3-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="8c6a3-102">Summary</span></span>
<span data-ttu-id="8c6a3-103">Cette proposition ajoute le concept de propriétés et d’indexeurs init uniquement à C#.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-103">This proposal adds the concept of init only properties and indexers to C#.</span></span> <span data-ttu-id="8c6a3-104">Ces indexeurs peuvent être définis au moment de la création de l’objet, mais ne `get` deviennent effectifs qu’une fois la création de l’objet terminée.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-104">These properties and indexers can be set at the point of object creation but become effectively `get` only once object creation has completed.</span></span>
<span data-ttu-id="8c6a3-105">Cela permet un modèle immuable bien plus flexible en C#.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-105">This allows for a much more flexible immutable model in C#.</span></span> 

## <a name="motivation"></a><span data-ttu-id="8c6a3-106">Motivation</span><span class="sxs-lookup"><span data-stu-id="8c6a3-106">Motivation</span></span>
<span data-ttu-id="8c6a3-107">Les mécanismes sous-jacents pour la génération de données immuables en C# n’ont pas changé depuis 1,0.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-107">The underlying mechanisms for building immutable data in C# haven't changed since 1.0.</span></span> <span data-ttu-id="8c6a3-108">Elles sont conservées :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-108">They remain:</span></span>

1. <span data-ttu-id="8c6a3-109">Déclaration de champs comme `readonly`.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-109">Declaring fields as `readonly`.</span></span>
1. <span data-ttu-id="8c6a3-110">Déclarer des propriétés qui contiennent uniquement un `get` accesseur.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-110">Declaring properties that contain only a `get` accessor.</span></span>

<span data-ttu-id="8c6a3-111">Ces mécanismes sont efficaces pour permettre la construction de données immuables, mais ils le font en ajoutant des coûts au code réutilisable de types et en choisissant ces types en dehors des fonctionnalités telles que les initialiseurs d’objets et de collections.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-111">These mechanisms are effective at allowing the construction of immutable data but they do so by adding cost to the boilerplate code of types and opting such types out of features like object and collection initializers.</span></span> <span data-ttu-id="8c6a3-112">Cela signifie que les développeurs doivent choisir entre facilité d’utilisation et immuabilité.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-112">This means developers must choose between ease of use and immutability.</span></span>

<span data-ttu-id="8c6a3-113">Un objet immuable simple comme `Point` nécessite deux fois plus de code de plaque de chaudière pour prendre en charge la construction, comme c’est le cas pour déclarer le type.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-113">A simple immutable object like `Point` requires twice as much boiler plate code to support construction as it does to declare the type.</span></span> <span data-ttu-id="8c6a3-114">Plus le type est grand, plus le coût de cette plaque de chaudière est important :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-114">The bigger the type the bigger the cost of this boiler plate:</span></span>

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

<span data-ttu-id="8c6a3-115">L' `init` accesseur rend les objets immuables plus flexibles en permettant à l’appelant de muter les membres pendant l’acte de construction.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-115">The `init` accessor makes immutable objects more flexible by allowing the caller to mutate the members during the act of construction.</span></span> <span data-ttu-id="8c6a3-116">Cela signifie que les propriétés immuables de l’objet peuvent participer à des initialiseurs d’objets et, par conséquent, supprimer la nécessité d’utiliser tout le constructeur dans le type.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-116">That means the object's immutable properties can participate in object initializers and thus removes the need for all constructor boilerplate in the type.</span></span> <span data-ttu-id="8c6a3-117">Le `Point` type est désormais simplement :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-117">The `Point` type is now simply:</span></span>

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

<span data-ttu-id="8c6a3-118">Le consommateur peut ensuite utiliser des initialiseurs d’objet pour créer l’objet</span><span class="sxs-lookup"><span data-stu-id="8c6a3-118">The consumer can then use object initializers to create the object</span></span>

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a><span data-ttu-id="8c6a3-119">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="8c6a3-119">Detailed Design</span></span>

### <a name="init-accessors"></a><span data-ttu-id="8c6a3-120">accesseurs init</span><span class="sxs-lookup"><span data-stu-id="8c6a3-120">init accessors</span></span>
<span data-ttu-id="8c6a3-121">Une propriété init only (ou indexeur) est déclarée à l' `init` aide de l’accesseur `set` à la place de l’accesseur :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-121">An init only property (or indexer) is declared by using the `init` accessor in place of the `set` accessor:</span></span>

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

<span data-ttu-id="8c6a3-122">Une propriété d’instance contenant `init` un accesseur est considérée comme définissable dans les circonstances suivantes :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-122">An instance property containing an `init` accessor is considered settable in the following circumstances:</span></span>

- <span data-ttu-id="8c6a3-123">Au cours d’un initialiseur d’objet</span><span class="sxs-lookup"><span data-stu-id="8c6a3-123">During an object initializer</span></span>
- <span data-ttu-id="8c6a3-124">Au cours `with` d’un initialiseur d’expression</span><span class="sxs-lookup"><span data-stu-id="8c6a3-124">During a `with` expression initializer</span></span>
- <span data-ttu-id="8c6a3-125">À l’intérieur d’un constructeur d’instance du type conteneur ou `this` dérivé, sur ou`base`</span><span class="sxs-lookup"><span data-stu-id="8c6a3-125">Inside an instance constructor of the containing or derived type, on `this` or `base`</span></span>
- <span data-ttu-id="8c6a3-126">À l' `init` intérieur de l’accesseur d' `this` une propriété, sur ou`base`</span><span class="sxs-lookup"><span data-stu-id="8c6a3-126">Inside the `init` accessor of any property, on `this` or `base`</span></span>
- <span data-ttu-id="8c6a3-127">À l’intérieur des utilisations d’attributs avec des paramètres nommés</span><span class="sxs-lookup"><span data-stu-id="8c6a3-127">Inside attribute usages with named parameters</span></span>

<span data-ttu-id="8c6a3-128">Les heures ci-dessus dans `init` lesquelles les accesseurs sont définissables sont collectivement appelées dans ce document en tant que phase de construction de l’objet.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-128">The times above in which the `init` accessors are settable are collectively referred to in this document as the construction phase of the object.</span></span>

<span data-ttu-id="8c6a3-129">Cela signifie que `Student` la classe peut être utilisée des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-129">This means the `Student` class can be used in the following ways:</span></span>

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

<span data-ttu-id="8c6a3-130">Les règles autour desquelles `init` les accesseurs sont définissables s’étendent sur les hiérarchies de types.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-130">The rules around when `init` accessors are settable extend across type hierarchies.</span></span> <span data-ttu-id="8c6a3-131">Si le membre est accessible et que l’objet est connu pour être dans la phase de construction, le membre est définissable.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-131">If the member is accessible and the object is known to be in the construction phase then the member is settable.</span></span> <span data-ttu-id="8c6a3-132">Cela autorise spécifiquement les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-132">That specifically allows for the following:</span></span>

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

<span data-ttu-id="8c6a3-133">Au point où un `init` accesseur est appelé, l’instance est connue pour être dans la phase de construction ouverte.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-133">At the point a `init` accessor is invoked the instance is known to be in the open construction phase.</span></span> <span data-ttu-id="8c6a3-134">Par conséquent `init` , un accesseur est autorisé à effectuer les actions suivantes en plus de `set` ce qu’un accesseur normal peut faire :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-134">Hence an `init` accessor is allowed to take the following actions in addition to what a normal `set` accessor can do:</span></span>

1. <span data-ttu-id="8c6a3-135">Appeler d' `init` autres accesseurs disponibles `this` via ou`base`</span><span class="sxs-lookup"><span data-stu-id="8c6a3-135">Call other `init` accessors available through `this` or `base`</span></span>
1. <span data-ttu-id="8c6a3-136">Assigner `readonly` des champs déclarés sur le même type via`this`</span><span class="sxs-lookup"><span data-stu-id="8c6a3-136">Assign `readonly` fields declared on the same type through `this`</span></span>

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

<span data-ttu-id="8c6a3-137">La possibilité d’assigner `readonly` des `init` champs à partir d’un accesseur est limitée aux champs déclarés sur le même type que l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-137">The ability to assign `readonly` fields from an `init` accessor is limited to those fields declared on the same type as the accessor.</span></span> <span data-ttu-id="8c6a3-138">Elle ne peut pas être utilisée `readonly` pour assigner des champs dans un type de base.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-138">It cannot be used to assign `readonly` fields in a base type.</span></span> <span data-ttu-id="8c6a3-139">Cette règle garantit que les auteurs de type restent en contrôle sur le comportement de mutabilité de leur type.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-139">This rule ensures that type authors remain in control over the mutability behavior of their type.</span></span> <span data-ttu-id="8c6a3-140">Les développeurs qui ne souhaitent pas utiliser `init` ne peuvent pas être affectés par d’autres types :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-140">Developers who do not wish to utilize `init` cannot be impacted from other types choosing to do so:</span></span>

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

<span data-ttu-id="8c6a3-141">Lorsque `init` est utilisé dans une propriété virtuelle, toutes les substitutions doivent également être marquées comme `init`.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-141">When `init` is used in a virtual property then all the overrides must also be marked as `init`.</span></span> <span data-ttu-id="8c6a3-142">De même, il n’est pas possible de remplacer `set` un `init`simple par.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-142">Likewise it is not possible to override a simple `set` with `init`.</span></span>

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

<span data-ttu-id="8c6a3-143">Une `interface` déclaration peut également participer à `init` l’initialisation de style à l’aide du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-143">An `interface` declaration can also participate in `init` style initialization via the following pattern:</span></span>

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

<span data-ttu-id="8c6a3-144">Restrictions de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-144">Restrictions of this feature:</span></span>
- <span data-ttu-id="8c6a3-145">L' `init` accesseur ne peut être utilisé que sur les propriétés d’instance</span><span class="sxs-lookup"><span data-stu-id="8c6a3-145">The `init` accessor can only be used on instance properties</span></span>
- <span data-ttu-id="8c6a3-146">Une propriété ne peut pas contenir `init` à `set` la fois un accesseur and</span><span class="sxs-lookup"><span data-stu-id="8c6a3-146">A property cannot contain both an `init` and `set` accessor</span></span>
- <span data-ttu-id="8c6a3-147">Toutes les substitutions d’une propriété doivent avoir `init` si la base avait `init`.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-147">All overrides of a property must have `init` if the base had `init`.</span></span> <span data-ttu-id="8c6a3-148">Cette règle s’applique également à l’implémentation de l’interface.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-148">This rule also applies to interface implementation.</span></span>

### <a name="metadata-encoding"></a><span data-ttu-id="8c6a3-149">Encodage des métadonnées</span><span class="sxs-lookup"><span data-stu-id="8c6a3-149">Metadata encoding</span></span> 
<span data-ttu-id="8c6a3-150">Les `init` accesseurs de propriété sont émis en tant qu' `set` accesseur standard avec le type de retour marqué avec `IsExternalInit`un modreq de.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-150">Property `init` accessors will be emitted as a standard `set` accessor with the return type marked with a modreq of `IsExternalInit`.</span></span> <span data-ttu-id="8c6a3-151">Il s’agit d’un nouveau type qui aura la définition suivante :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-151">This is a new type which will have the following definition:</span></span>

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

<span data-ttu-id="8c6a3-152">Le compilateur mettra en correspondance le type par nom complet.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-152">The compiler will match the type by full name.</span></span> <span data-ttu-id="8c6a3-153">Il n’est pas nécessaire qu’il s’affiche dans la bibliothèque principale.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-153">There is no requirement that it appear in the core library.</span></span> <span data-ttu-id="8c6a3-154">S’il existe plusieurs types portant ce nom, le compilateur lie l’arrêt dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-154">If there are multiple types by this name then the compiler will tie break in the following order:</span></span>

1. <span data-ttu-id="8c6a3-155">Celui défini dans le projet en cours de compilation</span><span class="sxs-lookup"><span data-stu-id="8c6a3-155">The one defined in the project being compiled</span></span>
1. <span data-ttu-id="8c6a3-156">Celui défini dans corelib</span><span class="sxs-lookup"><span data-stu-id="8c6a3-156">The one defined in corelib</span></span>

<span data-ttu-id="8c6a3-157">Si aucune de ces options n’existe, une erreur d’ambiguïté de type sera émise.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-157">If neither of these exist then a type ambiguity error will be issued.</span></span>

<span data-ttu-id="8c6a3-158">La conception de `IsExternalInit` est plus loin dans [ce numéro](https://github.com/dotnet/runtime/issues/34978)</span><span class="sxs-lookup"><span data-stu-id="8c6a3-158">The design for `IsExternalInit` is futher covered in [this issue](https://github.com/dotnet/runtime/issues/34978)</span></span>

## <a name="questions"></a><span data-ttu-id="8c6a3-159">Questions</span><span class="sxs-lookup"><span data-stu-id="8c6a3-159">Questions</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="8c6a3-160">Modifications avec rupture</span><span class="sxs-lookup"><span data-stu-id="8c6a3-160">Breaking changes</span></span>
<span data-ttu-id="8c6a3-161">L’un des principaux points du pivot dans la manière dont cette fonctionnalité est encodée va à la question suivante :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-161">One of the main pivot points in how this feature is encoded will come down to the following question:</span></span> 

> <span data-ttu-id="8c6a3-162">S’agit-il d’une modification avec `init` rupture `set`binaire à remplacer par ?</span><span class="sxs-lookup"><span data-stu-id="8c6a3-162">Is it a binary breaking change to replace `init` with `set`?</span></span>

<span data-ttu-id="8c6a3-163">Le `init` remplacement `set` de par, ce qui rend une propriété entièrement accessible en écriture, n’est jamais une modification avec rupture source sur une propriété non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-163">Replacing `init` with `set` and thus making a property fully writable is never a source breaking change on a non-virtual property.</span></span> <span data-ttu-id="8c6a3-164">Il développe simplement l’ensemble des scénarios dans lesquels la propriété peut être écrite.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-164">It simply expands the set of scenarios where the property can be written.</span></span> <span data-ttu-id="8c6a3-165">Le seul comportement en question est le fait qu’il reste ou non une modification avec rupture binaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-165">The only behavior in question is whether or not this remains a binary breaking change.</span></span>

<span data-ttu-id="8c6a3-166">Si vous souhaitez apporter la modification `init` à `set` une source et une modification compatible binaire, elle forcera notre main sur la décision modreq et sur les attributs ci-dessous, car elle va éliminer modreqs en tant que soulution.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-166">If we want to make the change of `init` to `set` a source and binary compatible change then it will force our hand on the modreq vs. attributes decision below because it will rule out modreqs as a soulution.</span></span> <span data-ttu-id="8c6a3-167">Si, en revanche, cela est considéré comme une opération non intéressante, cela rendra la décision modreq et d’attribut moins affectée.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-167">If on the other hand this is seen as a non-interesting then this will make the modreq vs. attribute decision less impactful.</span></span>

<span data-ttu-id="8c6a3-168">**Résolution** Ce scénario n’est pas considéré comme intéressant par LDM.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-168">**Resolution** This scenario is not seen as compelling by LDM.</span></span>

### <a name="modreqs-vs-attributes"></a><span data-ttu-id="8c6a3-169">Modreqs et attributs</span><span class="sxs-lookup"><span data-stu-id="8c6a3-169">Modreqs vs. attributes</span></span>
<span data-ttu-id="8c6a3-170">La stratégie d’émission `init` pour les accesseurs de propriété doit choisir entre l’utilisation d’attributs ou de modreqs lors de l’émission pendant les métadonnées.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-170">The emit strategy for `init` property accessors must choose between using attributes or modreqs when emitting during metadata.</span></span> <span data-ttu-id="8c6a3-171">Ils ont des compromis différents qui doivent être pris en compte.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-171">These have different trade offs that need to be considered.</span></span>

<span data-ttu-id="8c6a3-172">L’annotation d’un accesseur Set de propriété à l’aide d’une déclaration modreq signifie que les compilateurs compatibles CLI ignoreront l’accesseur, sauf s’il comprend le modreq.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-172">Annotating a property set accessor with a modreq declaration means CLI compliant compilers will ignore the accessor unless it understands the modreq.</span></span> <span data-ttu-id="8c6a3-173">Cela signifie que seuls les compilateurs reconnaissants de `init` lisent le membre.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-173">That means only compilers aware of `init` will read the member.</span></span> <span data-ttu-id="8c6a3-174">Les compilateurs qui ne sont `init` pas conscients de `set` ignorent l’accesseur et, par conséquent, ne traitent pas accidentellement la propriété comme étant en lecture/écriture.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-174">Compilers unaware of `init` will ignore the `set` accessor and hence will not accidentally treat the property as read / write.</span></span> 

<span data-ttu-id="8c6a3-175">L’inconvénient de modreq est `init` de devenir une partie de la signature binaire de `set` l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-175">The downside of modreq is `init` becomes a part of the binary signature of the `set` accessor.</span></span> <span data-ttu-id="8c6a3-176">L’ajout ou `init` la suppression entraînera l’arrêt du compatbility binaire de l’application.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-176">Adding or removing `init` will break binary compatbility of the application.</span></span>

<span data-ttu-id="8c6a3-177">L’utilisation d’attributs pour annoter l' `set` accesseur signifie que seuls les compilateurs qui comprennent l’attribut sauront pouvoir limiter l’accès à celui-ci.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-177">Using attributes to annotate the `set` accessor means that only compilers which understand the attribute will know to limit access to it.</span></span> <span data-ttu-id="8c6a3-178">Un compilateur qui n’est `init` pas conscient de le verra comme une simple propriété en lecture/écriture et autorisera l’accès.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-178">A compiler unaware of `init` will see it as a simple read / write property and allow access.</span></span>

<span data-ttu-id="8c6a3-179">Cela signifie apparemment que cette décision est un choix entre la sécurité supplémentaire au détriment de la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-179">This would seemingly mean this decision is a choice between extra safety at the expense of binary compatibility.</span></span> <span data-ttu-id="8c6a3-180">Le fait d’examiner un peu la sécurité supplémentaire n’est pas exactement ce qu’il semble.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-180">Digging in a bit the extra safety is not exactly what it seems.</span></span> <span data-ttu-id="8c6a3-181">Elle ne protège pas contre les circonstances suivantes :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-181">It will not protect against the following circumstances:</span></span>

1. <span data-ttu-id="8c6a3-182">Réflexion sur `public` les membres</span><span class="sxs-lookup"><span data-stu-id="8c6a3-182">Reflection over `public` members</span></span>
1. <span data-ttu-id="8c6a3-183">L’utilisation de`dynamic`</span><span class="sxs-lookup"><span data-stu-id="8c6a3-183">The use of `dynamic`</span></span> 
1. <span data-ttu-id="8c6a3-184">Compilateurs qui ne reconnaissent pas modreqs</span><span class="sxs-lookup"><span data-stu-id="8c6a3-184">Compilers that don't recognize modreqs</span></span>

<span data-ttu-id="8c6a3-185">Il convient également de considérer que, lorsque nous effectuons les règles de vérification IL pour .NET `init` 5, est l’une de ces règles.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-185">It should also be considered that, when we complete the IL verification rules for .NET 5, `init` will be one of those rules.</span></span> <span data-ttu-id="8c6a3-186">Cela signifie qu’une mise en œuvre supplémentaire sera obtenue en vérifiant simplement les compilateurs émettant un IL vérifiable.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-186">That means extra enforcement will be gained from simply verifying compilers emitting verifiable IL.</span></span>

<span data-ttu-id="8c6a3-187">Les principaux langages pour .NET (C#, F # et VB) seront tous mis à jour pour `init` reconnaître ces accesseurs.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-187">The primary languages for .NET (C#, F# and VB) will all be updated to recognize these `init` accessors.</span></span> <span data-ttu-id="8c6a3-188">Par conséquent, le seul scénario réaliste ici est lorsqu’un compilateur C# `init` 9 émet des propriétés et qu’ils sont visibles par un ensemble d’outils plus ancien, tel que C# 8, VB 15, etc. C# 8.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-188">Hence the only realistic scenario here is when a C# 9 compiler emits `init` properties and they are seen by an older toolset such as C# 8, VB 15, etc ... C# 8.</span></span> <span data-ttu-id="8c6a3-189">C’est le compromis à prendre en compte et à peser sur la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-189">That is the trade off to consider and weigh against binary compatibility.</span></span>

<span data-ttu-id="8c6a3-190">**Remarque** Cette discussion s’applique principalement aux membres uniquement, pas aux champs.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-190">**Note** This discussion primarily applies to members only, not to fields.</span></span> <span data-ttu-id="8c6a3-191">Alors `init` que les champs ont été rejetés par LDM, ils sont toujours intéressants à prendre en compte pour la discussion modreq et d’attribut.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-191">While `init` fields were rejected by LDM they are still interesting to consider for the modreq vs. attribute discussion.</span></span> <span data-ttu-id="8c6a3-192">La `init` fonctionnalité pour les champs est un assouplissement de la restriction existante `readonly`de.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-192">The `init` feature for fields is a relaxation of the existing restriction of `readonly`.</span></span> <span data-ttu-id="8c6a3-193">Cela signifie que si nous émettez les `readonly` champs sous la forme de + un attribut, il n’y a aucun risque que des compilateurs plus anciens ne `readonly`soient en utilisant le champ parce qu’ils se reconnaissent déjà.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-193">That means if we emit the fields as `readonly` + an attribute there is no risk of older compilers mis-using the field because they would already recognize `readonly`.</span></span> <span data-ttu-id="8c6a3-194">Par conséquent, l’utilisation d’un modreq ici n’ajoute pas de protection supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-194">Hence using a modreq here doesn't add any extra protection.</span></span>

<span data-ttu-id="8c6a3-195">**Résolution** La fonctionnalité utilise un modreq pour encoder la `init` méthode setter de propriété.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-195">**Resolution** The feature will use a modreq to encode the property `init` setter.</span></span> <span data-ttu-id="8c6a3-196">Les facteurs intéressants étaient (dans aucun ordre particulier) :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-196">The compelling factors were (in no particular order):</span></span>

* <span data-ttu-id="8c6a3-197">Souhaitant dissuader les compilateurs plus anciens de `init` violer la sémantique</span><span class="sxs-lookup"><span data-stu-id="8c6a3-197">Desire to discourage older compilers from violating `init` semantics</span></span>
* <span data-ttu-id="8c6a3-198">Vous souhaitez ajouter ou supprimer `init` dans une `virtual` déclaration, ou `interface` à la fois une modification avec rupture source et binaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-198">Desire to make adding or removing `init` in a `virtual` declaration or `interface` both a source and binary breaking change.</span></span>

<span data-ttu-id="8c6a3-199">Étant donné qu’il n’y avait pas non `init` plus de prise en charge significative pour la suppression de en tant que modification compatible binaire, il a été possible de choisir d’utiliser modreq.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-199">Given there was also no significant support for removing `init` to be a binary compatible change it made the choice of using modreq straight forward.</span></span>

### <a name="init-vs-initonly"></a><span data-ttu-id="8c6a3-200">init et initonly</span><span class="sxs-lookup"><span data-stu-id="8c6a3-200">init vs. initonly</span></span>
<span data-ttu-id="8c6a3-201">Il existe trois formes de syntaxe qui ont été particulièrement étudiées pendant notre réunion LDM :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-201">There were three syntax forms which got significant consideration during our LDM meeting:</span></span>

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

<span data-ttu-id="8c6a3-202">**Résolution** Il n’existait pas de syntaxe très difficilement favorisée dans LDM.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-202">**Resolution** There was no syntax which was overwhelmingly favored in LDM.</span></span>

<span data-ttu-id="8c6a3-203">Une attention particulière était de savoir comment le choix de la syntaxe aurait un impact sur notre capacité `init` à faire des membres en tant que fonctionnalité générale dans le futur.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-203">One point which got significant attention was how the choice of syntax would impact our ability to do `init` members as a general feature in the future.</span></span>
<span data-ttu-id="8c6a3-204">Le choix de l’option 1 signifie qu’il serait difficile de définir une propriété qui avait `init` une `get` méthode de style à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-204">Choosing option 1 would mean that it would be difficult to define a property which had a `init` style `get` method in the future.</span></span> <span data-ttu-id="8c6a3-205">Finalement, il a été décidé que, si nous avons décidé de `init` continuer avec les membres généraux à `init` l’avenir, nous pourrions autoriser à être un modificateur dans la liste des accesseurs `init set`de propriété, ainsi qu’une petite main pour.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-205">Eventually it was decided that if we decided to go forward with general `init` members in future we could allow `init` to be a modifier in the property accessor list as well as a short hand for `init set`.</span></span> <span data-ttu-id="8c6a3-206">Fondamentalement, les deux déclarations suivantes sont identiques.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-206">Essentially the following two declarations would be identical.</span></span>

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

<span data-ttu-id="8c6a3-207">La décision a été prise de passer `init` en tant qu’accesseur autonome dans la liste des accesseurs de propriété.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-207">The decision was made to move forward with `init` as a standalone accessor in the property accessor list.</span></span>

### <a name="warn-on-failed-init"></a><span data-ttu-id="8c6a3-208">Avertir en cas d’échec de l’initialisation</span><span class="sxs-lookup"><span data-stu-id="8c6a3-208">Warn on failed init</span></span>
<span data-ttu-id="8c6a3-209">Considérons le scénario suivant.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-209">Consider the following scenario.</span></span> <span data-ttu-id="8c6a3-210">Un type déclare un `init` membre uniquement qui n’est pas défini dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-210">A type declares an `init` only member which is not set in the constructor.</span></span> <span data-ttu-id="8c6a3-211">Le code qui construit l’objet doit-il recevoir un avertissement s’il n’a pas pu initialiser la valeur ?</span><span class="sxs-lookup"><span data-stu-id="8c6a3-211">Should the code which constructs the object get a warning if they failed to initialize the value?</span></span>

<span data-ttu-id="8c6a3-212">À ce stade, il est clair que le champ ne sera jamais défini et a donc beaucoup de similitudes avec l’avertissement autour de l’échec `private` de l’initialisation des données.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-212">At that point it is clear the field will never be set and hence has a lot of similarities with the warning around failing to initialize `private` data.</span></span> <span data-ttu-id="8c6a3-213">Par conséquent, un avertissement aurait apparemment une valeur ici ?</span><span class="sxs-lookup"><span data-stu-id="8c6a3-213">Hence a warning would seemingly have some value here?</span></span>

<span data-ttu-id="8c6a3-214">Toutefois, cet avertissement présente des inconvénients importants :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-214">There are significant downsides to this warning though:</span></span>
1. <span data-ttu-id="8c6a3-215">Cela complique la modification `readonly` de la compatibilité avec `init`.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-215">It complicates the compatibility story of changing `readonly` to `init`.</span></span> 
1. <span data-ttu-id="8c6a3-216">Elle nécessite de fournir des métadonnées supplémentaires pour indiquer les membres qui doivent être initialisés par l’appelant.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-216">It requires carrying additional metadata around to denote the members which are required to be initialized by the caller.</span></span>

<span data-ttu-id="8c6a3-217">En outre, si nous pensons qu’il y a de la valeur ici dans le scénario global de forcer les créateurs d’objets à être avertis/Errors sur des champs spécifiques, il est probable qu’il s’agit d’une fonctionnalité générale.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-217">Further if we believe there is value here in the overall scenario of forcing object creators to be warned / error'd about specific fields then this likely makes sense as a general feature.</span></span> <span data-ttu-id="8c6a3-218">Il n’y a aucune raison d’être limité à `init` des membres uniquement.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-218">There is no reason it should be limited to just `init` members.</span></span>

<span data-ttu-id="8c6a3-219">**Résolution** Il n’y aura aucun avertissement sur la `init` consommation des champs et des propriétés.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-219">**Resolution** There will be no warning on consumption of `init` fields and properties.</span></span>

<span data-ttu-id="8c6a3-220">LDM souhaite avoir une discussion plus large sur l’idée des champs et propriétés requis.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-220">LDM wants to have a broader discussion on the idea of required fields and properties.</span></span> <span data-ttu-id="8c6a3-221">Cela peut nous amener à revenir en arrière et à reconsidérer notre `init` position sur les membres et la validation.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-221">That may cause us to come back and reconsider our position on `init` members and validation.</span></span>

## <a name="allow-init-as-a-field-modifier"></a><span data-ttu-id="8c6a3-222">Autoriser init en tant que modificateur de champ</span><span class="sxs-lookup"><span data-stu-id="8c6a3-222">Allow init as a field modifier</span></span>
<span data-ttu-id="8c6a3-223">De la même façon `init` peut servir d’accesseur de propriété, il peut également servir de désignation sur les champs pour leur attribuer des comportements `init` similaires en tant que propriétés.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-223">In the same way `init` can serve as a property accessor it could also serve as a designation on fields to give them similar behaviors as `init` properties.</span></span>
<span data-ttu-id="8c6a3-224">Cela permet d’assigner le champ avant que la construction soit terminée par le type, les types dérivés ou les initialiseurs d’objets.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-224">That would allow for the field to be assigned before construction was complete by the type, derived types, or object initializers.</span></span>

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

<span data-ttu-id="8c6a3-225">Dans les métadonnées, ces champs sont marqués de la même `readonly` façon que les champs, mais avec un attribut ou modreq supplémentaire `init` pour indiquer qu’il s’agit de champs de style.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-225">In metadata these fields would be marked in the same way as `readonly` fields but with an additional attribute or modreq to indicate they are `init` style fields.</span></span> 

<span data-ttu-id="8c6a3-226">**Résolution** Le LDM accepte que cette proposition soit un son, mais le scénario est globalement disjoint des propriétés.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-226">**Resolution** LDM agrees this proposal is sound but overall the scenario felt disjoint from properties.</span></span> <span data-ttu-id="8c6a3-227">La décision consistait à continuer uniquement `init` avec les propriétés pour le moment.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-227">The decision was to proceed only with `init` properties for now.</span></span> <span data-ttu-id="8c6a3-228">Cela offre un niveau de flexibilité approprié, car `init` une propriété peut muter un `readonly` champ sur le type déclarant de la propriété.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-228">This has a suitable level of flexibility as an `init` property can mutate a `readonly` field on the declaring type of the property.</span></span> <span data-ttu-id="8c6a3-229">Ce sera le cas si des commentaires client importants justifient le scénario.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-229">This will be reconsidered if there is significant customer feedback that justifies the scenario.</span></span>

### <a name="allow-init-as-a-type-modifier"></a><span data-ttu-id="8c6a3-230">Autoriser init en tant que modificateur de type</span><span class="sxs-lookup"><span data-stu-id="8c6a3-230">Allow init as a type modifier</span></span>
<span data-ttu-id="8c6a3-231">De la même façon, `readonly` le modificateur peut être appliqué à `struct` un pour déclarer automatiquement tous les `readonly`champs comme `init` , le seul modificateur peut être déclaré `struct` sur `class` ou pour marquer automatiquement tous les `init`champs comme.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-231">In the same way the `readonly` modifier can be applied to a `struct` to automatically declare all fields as `readonly`, the `init` only modifier can be declared on a `struct` or `class` to automatically mark all fields as `init`.</span></span>
<span data-ttu-id="8c6a3-232">Cela signifie que les deux déclarations de type suivantes sont équivalentes :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-232">This means the following two type declarations are equivalent:</span></span>

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

<span data-ttu-id="8c6a3-233">**Résolution** Cette fonctionnalité est trop *jolie* ici et est en conflit `readonly struct` avec la fonctionnalité sur laquelle elle est basée.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-233">**Resolution** This feature is too *cute* here and conflicts with the `readonly struct` feature on which it is based.</span></span> <span data-ttu-id="8c6a3-234">La `readonly struct` fonctionnalité est simple en ce qu’elle `readonly` s’applique à tous les membres : champs, méthodes, etc... La `init struct` fonctionnalité s’applique uniquement aux propriétés.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-234">The `readonly struct` feature is simple in that it applies `readonly` to all members: fields, methods, etc ... The `init struct` feature would only apply to properties.</span></span> <span data-ttu-id="8c6a3-235">Cela finit par compliquer les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-235">This actually ends up making it confusing for users.</span></span> 

<span data-ttu-id="8c6a3-236">Étant donné `init` que n’est valide que sur certains aspects d’un type, nous avons rejeté l’idée de le faire en tant que modificateur de type.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-236">Given that `init` is only valid on certain aspects of a type we rejected the idea of having it as a type modifier.</span></span>

## <a name="considerations"></a><span data-ttu-id="8c6a3-237">Considérations</span><span class="sxs-lookup"><span data-stu-id="8c6a3-237">Considerations</span></span>

### <a name="compatibility"></a><span data-ttu-id="8c6a3-238">Compatibilité</span><span class="sxs-lookup"><span data-stu-id="8c6a3-238">Compatibility</span></span>
<span data-ttu-id="8c6a3-239">Cette `init` fonctionnalité est conçue pour être compatible avec les `get` propriétés existantes uniquement.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-239">The `init` feature is designed to be compatible with existing `get` only properties.</span></span> <span data-ttu-id="8c6a3-240">Plus précisément, il s’agit d’une modification entièrement additive pour une propriété qui `get` est uniquement aujourd’hui, mais qui souhaite une plus grande sémantique de création d’objets Flexbile.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-240">Specifically it is meant to be a completely additive change for a property which is `get` only today but desires more flexbile object creation semantics.</span></span>

<span data-ttu-id="8c6a3-241">Prenons l’exemple du type suivant :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-241">For example consider the following type:</span></span>

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

<span data-ttu-id="8c6a3-242">L' `init` ajout à ces propriétés est une modification sans rupture :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-242">Adding `init` to these properties is a non-breaking change:</span></span>

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

### <a name="il-verification"></a><span data-ttu-id="8c6a3-243">Vérification IL</span><span class="sxs-lookup"><span data-stu-id="8c6a3-243">IL verification</span></span>
<span data-ttu-id="8c6a3-244">Lorsque .NET Core décide de réimplémenter la vérification IL, les règles doivent être ajustées pour tenir `init` compte des membres.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-244">When .NET Core decides to re-implement IL verification the rules will need to be adjusted to account for `init` members.</span></span> <span data-ttu-id="8c6a3-245">Cela doit être inclus dans les modifications de règle pour l’accession non transmutante `readonly` aux données.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-245">This will need to be included in the rule changes for non-mutating acess to `readonly` data.</span></span>

<span data-ttu-id="8c6a3-246">Les règles de vérification IL devront être divisées en deux parties :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-246">The IL verification rules will need to be broken into two parts:</span></span> 

1. <span data-ttu-id="8c6a3-247">Autorisation `init` des membres à définir `readonly` un champ.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-247">Allowing `init` members to set a `readonly` field.</span></span>
1. <span data-ttu-id="8c6a3-248">Détermination du moment `init` où un membre peut être appelé légalement.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-248">Determining when an `init` member can be legally called.</span></span>

<span data-ttu-id="8c6a3-249">La première est un ajustement simple aux règles existantes.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-249">The first is a simple adjustment to the existing rules.</span></span> <span data-ttu-id="8c6a3-250">Le vérificateur IL peut être enseigné pour reconnaître `init` les membres et, à partir de là, il suffit `readonly` de considérer un champ comme `this` pouvant être définissable dans ce membre.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-250">The IL verifier can be taught to recognize `init` members and from there it just needs to consider a `readonly` field to be settable on `this` in such a member.</span></span>

<span data-ttu-id="8c6a3-251">La deuxième règle est plus compliquée.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-251">The second rule is more complicated.</span></span> <span data-ttu-id="8c6a3-252">Dans le cas simple des initialiseurs d’objets, la règle est directe.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-252">In the simple case of object initializers the rule is straight forward.</span></span> <span data-ttu-id="8c6a3-253">Il doit être légal d’appeler `init` des membres lorsque le résultat d' `new` une expression est toujours sur la pile.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-253">It should be legal to call `init` members when the result of a `new` expression is still on the stack.</span></span> <span data-ttu-id="8c6a3-254">Ainsi, tant que la valeur n’a pas été stockée dans un élément ou un élément de tableau local ou passé comme argument à une autre méthode, il `init` sera toujours légal d’appeler les membres.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-254">That is until the value has been stored in a local, array element or field or passed as an argument to another method it will still be legal to call `init` members.</span></span> <span data-ttu-id="8c6a3-255">Cela garantit qu’une fois que le résultat `new` de l’expression est publié dans un identificateur nommé (autre `this`que), il n’est plus autorisé à appeler `init` les membres.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-255">This ensures that once the result of the `new` expression is published to a named identifier (other than `this`) then it will no longer be legal to call `init` members.</span></span> 

<span data-ttu-id="8c6a3-256">Le cas le plus compliqué cependant est quand nous `init` mélangeons les membres, les `await`initialiseurs d’objets et.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-256">The more complicated case though is when we mix `init` members, object initializers and `await`.</span></span> <span data-ttu-id="8c6a3-257">Cela peut entraîner le placement temporaire de l’objet nouvellement créé dans une machine à États et le placer dans un champ.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-257">That can cause the newly created object to be temporarily hoisted into a state machine and hence put into a field.</span></span>

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

<span data-ttu-id="8c6a3-258">Ici, le résultat `new Student()` de sera hoised dans une machine à États en tant que champ avant l' `Name` ensemble de.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-258">Here the result of `new Student()` will be hoised into a state machine as a field before the set of `Name` occurs.</span></span> <span data-ttu-id="8c6a3-259">Le compilateur doit marquer ces champs levés de telle sorte que le vérificateur IL comprenne qu’ils ne sont pas accessibles par l’utilisateur et ne viole donc pas la sémantique prévue `init`de.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-259">The compiler will need to mark such hoisted fields in a way that the IL verifier understands they're not user accessible and hence doesn't violate the intended semantics of `init`.</span></span>

### <a name="init-members"></a><span data-ttu-id="8c6a3-260">membres init</span><span class="sxs-lookup"><span data-stu-id="8c6a3-260">init members</span></span>
<span data-ttu-id="8c6a3-261">Le `init` modificateur peut être étendu pour s’appliquer à tous les membres d’instance.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-261">The `init` modifier could be extended to apply to all instance members.</span></span> <span data-ttu-id="8c6a3-262">Cela généraliserait le concept de la `init` construction de l’objet et autorisera les types à déclarer des méthodes d’assistance qui pouvaient partipate dans le processus `init` de construction pour initialiser les champs et les propriétés.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-262">This would generalize the concept of `init` during object construction and allow types to declare helper methods that could partipate in the construction process to initialize `init` fields and properties.</span></span>

<span data-ttu-id="8c6a3-263">De tels membres auraient tous les restricions qu’un `init` accesseur effectue dans cette conception.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-263">Such members would have all the restricions that an `init` accessor does in this design.</span></span> <span data-ttu-id="8c6a3-264">La nécessité est cependant douteuse, mais elle peut être ajoutée en toute sécurité dans une future version du langage de manière compatible.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-264">The need is questionable though and this can be safely added in a future version of the language in a compatible manner.</span></span>

### <a name="generate-three-accessors"></a><span data-ttu-id="8c6a3-265">Générer trois accesseurs</span><span class="sxs-lookup"><span data-stu-id="8c6a3-265">Generate three accessors</span></span>
<span data-ttu-id="8c6a3-266">L’une des implémentations potentielles `init` des `init` propriétés consiste à `set`effectuer une séparation complète de.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-266">One potential implementation of `init` properties is to make `init` completely separate from `set`.</span></span> <span data-ttu-id="8c6a3-267">Cela signifie qu’une propriété peut potentiellement avoir trois accesseurs différents : `get`, `set` et `init`.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-267">That means that a property can potentially have three different accessors: `get`, `set` and `init`.</span></span>

<span data-ttu-id="8c6a3-268">Cela présente l’avantage potentiel de permettre l’utilisation de modreq pour garantir l’exactitude tout en conservant la compatibilité binaire.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-268">This has the potential advantage of allowing the use of modreq to enforce correctness while maintaining binary compatibility.</span></span> <span data-ttu-id="8c6a3-269">L’implémentation serait à peu près ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="8c6a3-269">The implementation would roughly be the following:</span></span>

1. <span data-ttu-id="8c6a3-270">Un `init` accesseur est toujours émis s’il y a `set`un.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-270">An `init` accessor is always emitted if there is a `set`.</span></span> <span data-ttu-id="8c6a3-271">Lorsqu’il n’est pas défini par le développeur, il s' `set`agit simplement d’une référence à.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-271">When not defined by the developer it is simply a reference to `set`.</span></span> 
1. <span data-ttu-id="8c6a3-272">Le jeu d’une propriété dans un initialiseur d’objet utilise `init` toujours s’il est présent, mais `set` revient à s’il est manquant.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-272">The set of a property in an object initializer will always use `init` if present but fall back to `set` if it's missing.</span></span>

<span data-ttu-id="8c6a3-273">Cela signifie qu’un développeur peut toujours supprimer `init` en toute sécurité une propriété.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-273">This means that a developer can always safely delete `init` from a property.</span></span> 

<span data-ttu-id="8c6a3-274">L’inconvénient de cette conception est que n’est utile que `init` si est **toujours** émis lorsqu’il y a `set`un.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-274">The downside of this design is that is only useful if `init` is **always** emitted when there is a `set`.</span></span> <span data-ttu-id="8c6a3-275">La langue ne peut pas `init` savoir si a été supprimé par le passé, elle doit supposer qu’elle était `init` et, par conséquent, doit toujours être émis.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-275">The language can't know if `init` was deleted in the past, it has to assume it was and hence the `init` must always be emitted.</span></span> <span data-ttu-id="8c6a3-276">Cela entraînerait une expansion significative des métadonnées et ne justifie pas ici le coût de la compatibilité.</span><span class="sxs-lookup"><span data-stu-id="8c6a3-276">That would cause a significant metadata expansion and is simply not worth the cost of the compatibility here.</span></span>