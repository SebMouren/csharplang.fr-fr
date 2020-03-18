---
ms.openlocfilehash: fa3326bf69c83b6042b1db7b5567fd5c28d6f81a
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484523"
---
# <a name="callerargumentexpression"></a><span data-ttu-id="37f40-101">CallerArgumentExpression</span><span class="sxs-lookup"><span data-stu-id="37f40-101">CallerArgumentExpression</span></span>

* <span data-ttu-id="37f40-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="37f40-102">[x] Proposed</span></span>
* <span data-ttu-id="37f40-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="37f40-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="37f40-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="37f40-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="37f40-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="37f40-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="37f40-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="37f40-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="37f40-107">Permet aux développeurs de capturer les expressions passées à une méthode, afin d’améliorer les messages d’erreur dans les API de diagnostic/test et de réduire les séquences de touches.</span><span class="sxs-lookup"><span data-stu-id="37f40-107">Allow developers to capture the expressions passed to a method, to enable better error messages in diagnostic/testing APIs and reduce keystrokes.</span></span>

## <a name="motivation"></a><span data-ttu-id="37f40-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="37f40-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="37f40-109">En cas d’échec de la validation d’une assertion ou d’un argument, le développeur souhaite savoir autant que possible l’emplacement et la raison de l’échec.</span><span class="sxs-lookup"><span data-stu-id="37f40-109">When an assertion or argument validation fails, the developer wants to know as much as possible about where and why it failed.</span></span> <span data-ttu-id="37f40-110">Toutefois, les API de diagnostic d’aujourd’hui ne facilitent pas cette opération.</span><span class="sxs-lookup"><span data-stu-id="37f40-110">However, today's diagnostic APIs do not fully facilitate this.</span></span> <span data-ttu-id="37f40-111">Considérez la méthode suivante :</span><span class="sxs-lookup"><span data-stu-id="37f40-111">Consider the following method:</span></span>

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null);
    Debug.Assert(array.Length == 1);

    return array[0];
}
```

<span data-ttu-id="37f40-112">Lorsque l’une des assertions échoue, seuls le nom de fichier, le numéro de ligne et le nom de la méthode seront fournis dans la trace de la pile.</span><span class="sxs-lookup"><span data-stu-id="37f40-112">When one of the asserts fail, only the filename, line number, and method name will be provided in the stack trace.</span></span> <span data-ttu-id="37f40-113">Le développeur ne peut pas déterminer quelle assertion a échoué à partir de ces informations (s) il doit ouvrir le fichier et accéder au numéro de ligne fourni pour voir ce qui s’est produit.</span><span class="sxs-lookup"><span data-stu-id="37f40-113">The developer will not be able to tell which assert failed from this information-- (s)he will have to open the file and navigate to the provided line number to see what went wrong.</span></span>

<span data-ttu-id="37f40-114">C’est également la raison pour laquelle les infrastructures de test doivent fournir une variété de méthodes d’assertion.</span><span class="sxs-lookup"><span data-stu-id="37f40-114">This is also the reason testing frameworks have to provide a variety of assert methods.</span></span> <span data-ttu-id="37f40-115">Avec xUnit, `Assert.True` et `Assert.False` ne sont pas fréquemment utilisés, car ils ne fournissent pas suffisamment de contexte sur ce qui a échoué.</span><span class="sxs-lookup"><span data-stu-id="37f40-115">With xUnit, `Assert.True` and `Assert.False` are not frequently used because they do not provide enough context about what failed.</span></span>

<span data-ttu-id="37f40-116">Bien que la situation soit un peu mieux pour la validation des arguments, car les noms des arguments non valides sont présentés au développeur, le développeur doit passer ces noms aux exceptions manuellement.</span><span class="sxs-lookup"><span data-stu-id="37f40-116">While the situation is a bit better for argument validation because the names of invalid arguments are shown to the developer, the developer must pass these names to exceptions manually.</span></span> <span data-ttu-id="37f40-117">Si l’exemple ci-dessus a été réécrit pour utiliser la validation d’argument traditionnelle au lieu de `Debug.Assert`, il ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="37f40-117">If the above example were rewritten to use traditional argument validation instead of `Debug.Assert`, it would look like</span></span>

```csharp
T Single<T>(this T[] array)
{
    if (array == null)
    {
        throw new ArgumentNullException(nameof(array));
    }

    if (array.Length != 1)
    {
        throw new ArgumentException("Array must contain a single element.", nameof(array));
    }

    return array[0];
}
```

<span data-ttu-id="37f40-118">Notez que `nameof(array)` doit être passé à chaque exception, bien qu’il soit déjà clair dans le contexte de l’argument non valide.</span><span class="sxs-lookup"><span data-stu-id="37f40-118">Notice that `nameof(array)` must be passed to each exception, although it's already clear from context which argument is invalid.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="37f40-119">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="37f40-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="37f40-120">Dans les exemples ci-dessus, l’inclusion de la chaîne `"array != null"` ou `"array.Length == 1"` dans le message d’assertion permettrait au développeur de déterminer ce qui a échoué.</span><span class="sxs-lookup"><span data-stu-id="37f40-120">In the above examples, including the string `"array != null"` or `"array.Length == 1"` in the assert message would help the developer determine what failed.</span></span> <span data-ttu-id="37f40-121">Entrez `CallerArgumentExpression`: il s’agit d’un attribut que l’infrastructure peut utiliser pour obtenir la chaîne associée à un argument de méthode particulier.</span><span class="sxs-lookup"><span data-stu-id="37f40-121">Enter `CallerArgumentExpression`: it's an attribute the framework can use to obtain the string associated with a particular method argument.</span></span> <span data-ttu-id="37f40-122">Nous l’ajouterons à `Debug.Assert` comme c’est le cas</span><span class="sxs-lookup"><span data-stu-id="37f40-122">We would add it to `Debug.Assert` like so</span></span>

```csharp
public static class Debug
{
    public static void Assert(bool condition, [CallerArgumentExpression("condition")] string message = null);
}
```

<span data-ttu-id="37f40-123">Le code source de l’exemple ci-dessus reste le même.</span><span class="sxs-lookup"><span data-stu-id="37f40-123">The source code in the above example would stay the same.</span></span> <span data-ttu-id="37f40-124">Toutefois, le code que le compilateur émet réellement correspond à</span><span class="sxs-lookup"><span data-stu-id="37f40-124">However, the code the compiler actually emits would correspond to</span></span>

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null, "array != null");
    Debug.Assert(array.Length == 1, "array.Length == 1");

    return array[0];
}
```

<span data-ttu-id="37f40-125">Le compilateur reconnaît spécifiquement l’attribut sur `Debug.Assert`.</span><span class="sxs-lookup"><span data-stu-id="37f40-125">The compiler specially recognizes the attribute on `Debug.Assert`.</span></span> <span data-ttu-id="37f40-126">Il passe la chaîne associée à l’argument référencé dans le constructeur de l’attribut (dans ce cas, `condition`) sur le site d’appel.</span><span class="sxs-lookup"><span data-stu-id="37f40-126">It passes the string associated with the argument referred to in the attribute's constructor (in this case, `condition`) at the call site.</span></span> <span data-ttu-id="37f40-127">En cas d’échec de la méthode Assert, le développeur verra la condition false et déterminera celle qui a échoué.</span><span class="sxs-lookup"><span data-stu-id="37f40-127">When either assert fails, the developer will be shown the condition that was false and will know which one failed.</span></span>

<span data-ttu-id="37f40-128">Pour la validation d’argument, l’attribut ne peut pas être utilisé directement, mais peut être utilisé par le biais d’une classe d’assistance :</span><span class="sxs-lookup"><span data-stu-id="37f40-128">For argument validation, the attribute cannot be used directly, but can be made use of through a helper class:</span></span>

```csharp
public static class Verify
{
    public static void Argument(bool condition, string message, [CallerArgumentExpression("condition")] string conditionExpression = null)
    {
        if (!condition) throw new ArgumentException(message: message, paramName: conditionExpression);
    }

    public static void InRange(int argument, int low, int high,
        [CallerArgumentExpression("argument")] string argumentExpression = null,
        [CallerArgumentExpression("low")] string lowExpression = null,
        [CallerArgumentExpression("high")] string highExpression = null)
    {
        if (argument < low)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be less than {lowExpression} ({low}).");
        }

        if (argument > high)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be greater than {highExpression} ({high}).");
        }
    }

    public static void NotNull<T>(T argument, [CallerArgumentExpression("argument")] string argumentExpression = null)
        where T : class
    {
        if (argument == null) throw new ArgumentNullException(paramName: argumentExpression);
    }
}

T Single<T>(this T[] array)
{
    Verify.NotNull(array); // paramName: "array"
    Verify.Argument(array.Length == 1, "Array must contain a single element."); // paramName: "array.Length == 1"

    return array[0];
}

T ElementAt(this T[] array, int index)
{
    Verify.NotNull(array); // paramName: "array"
    // paramName: "index"
    // message: "index (-1) cannot be less than 0 (0).", or
    //          "index (6) cannot be greater than array.Length - 1 (5)."
    Verify.InRange(index, 0, array.Length - 1);

    return array[index];
}
```

<span data-ttu-id="37f40-129">Une proposition d’ajout d’une telle classe d’assistance à l’infrastructure est en cours d' https://github.com/dotnet/corefx/issues/17068.</span><span class="sxs-lookup"><span data-stu-id="37f40-129">A proposal to add such a helper class to the framework is underway at https://github.com/dotnet/corefx/issues/17068.</span></span> <span data-ttu-id="37f40-130">Si cette fonctionnalité de langage a été implémentée, la proposition peut être mise à jour pour tirer parti de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="37f40-130">If this language feature was implemented, the proposal could be updated to take advantage of this feature.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="37f40-131">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="37f40-131">Extension methods</span></span>

<span data-ttu-id="37f40-132">Le paramètre `this` dans une méthode d’extension peut être référencé par `CallerArgumentExpression`.</span><span class="sxs-lookup"><span data-stu-id="37f40-132">The `this` parameter in an extension method may be referenced by `CallerArgumentExpression`.</span></span> <span data-ttu-id="37f40-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="37f40-133">For example:</span></span>

```csharp
public static void ShouldBe<T>(this T @this, T expected, [CallerArgumentExpression("this")] string thisExpression = null) {}

contestant.Points.ShouldBe(1337); // thisExpression: "contestant.Points"
```

<span data-ttu-id="37f40-134">`thisExpression` recevra l’expression qui correspond à l’objet avant le point.</span><span class="sxs-lookup"><span data-stu-id="37f40-134">`thisExpression` will receive the expression corresponding to the object before the dot.</span></span> <span data-ttu-id="37f40-135">S’il est appelé avec la syntaxe de méthode statique, par exemple `Ext.ShouldBe(contestant.Points, 1337)`, il se comporte comme si le premier paramètre n’était pas marqué comme `this`.</span><span class="sxs-lookup"><span data-stu-id="37f40-135">If it's called with static method syntax, e.g. `Ext.ShouldBe(contestant.Points, 1337)`, it will behave as if first parameter wasn't marked `this`.</span></span>

<span data-ttu-id="37f40-136">Il doit toujours y avoir une expression correspondant au paramètre `this`.</span><span class="sxs-lookup"><span data-stu-id="37f40-136">There should always be an expression corresponding to the `this` parameter.</span></span> <span data-ttu-id="37f40-137">Même si une instance d’une classe appelle une méthode d’extension sur elle-même, par exemple `this.Single()` à l’intérieur d’un type de collection, le `this` est mandaté par le compilateur afin que `"this"` soit passé.</span><span class="sxs-lookup"><span data-stu-id="37f40-137">Even if an instance of a class calls an extension method on itself, e.g. `this.Single()` from inside a collection type, the `this` is mandated by the compiler so `"this"` will get passed.</span></span> <span data-ttu-id="37f40-138">Si cette règle est modifiée à l’avenir, nous pouvons envisager de passer `null` ou la chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="37f40-138">If this rule is changed in the future, we can consider passing `null` or the empty string.</span></span>

### <a name="extra-details"></a><span data-ttu-id="37f40-139">Détails supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37f40-139">Extra details</span></span>

- <span data-ttu-id="37f40-140">Comme les autres attributs de `Caller*`, tels que `CallerMemberName`, cet attribut ne peut être utilisé que sur des paramètres avec des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="37f40-140">Like the other `Caller*` attributes, such as `CallerMemberName`, this attribute may only be used on parameters with default values.</span></span>
- <span data-ttu-id="37f40-141">Plusieurs paramètres marqués avec `CallerArgumentExpression` sont autorisés, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="37f40-141">Multiple parameters marked with `CallerArgumentExpression` are permitted, as shown above.</span></span>
- <span data-ttu-id="37f40-142">L’espace de noms de l’attribut sera `System.Runtime.CompilerServices`.</span><span class="sxs-lookup"><span data-stu-id="37f40-142">The attribute's namespace will be `System.Runtime.CompilerServices`.</span></span>
- <span data-ttu-id="37f40-143">Si `null` ou une chaîne qui n’est pas un nom de paramètre (par exemple, `"notAParameterName"`) est fourni, le compilateur passera une chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="37f40-143">If `null` or a string that is not a parameter name (e.g. `"notAParameterName"`) is provided, the compiler will pass in an empty string.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="37f40-144">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="37f40-144">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="37f40-145">Les personnes qui savent comment utiliser les décompilateurs pourront voir une partie du code source sur les sites d’appel pour les méthodes marquées avec cet attribut.</span><span class="sxs-lookup"><span data-stu-id="37f40-145">People who know how to use decompilers will be able to see some of the source code at call sites for methods marked with this attribute.</span></span> <span data-ttu-id="37f40-146">Cela peut être indésirable/inattendu pour les logiciels de source fermée.</span><span class="sxs-lookup"><span data-stu-id="37f40-146">This may be undesirable/unexpected for closed-source software.</span></span>

- <span data-ttu-id="37f40-147">Bien qu’il ne s’agisse pas d’un défaut dans la fonctionnalité elle-même, une source de préoccupation peut être qu’il existe une API `Debug.Assert` aujourd’hui qui ne prend qu’une `bool`.</span><span class="sxs-lookup"><span data-stu-id="37f40-147">Although this is not a flaw in the feature itself, a source of concern may be that there exists a `Debug.Assert` API today that only takes a `bool`.</span></span> <span data-ttu-id="37f40-148">Même si la surcharge acceptant un message avait son deuxième paramètre marqué avec cet attribut et rendu facultatif, le compilateur sélectionne toujours le non-message dans la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="37f40-148">Even if the overload taking a message had its second parameter marked with this attribute and made optional, the compiler would still pick the no-message one in overload resolution.</span></span> <span data-ttu-id="37f40-149">Par conséquent, la surcharge sans message doit être supprimée pour tirer parti de cette fonctionnalité, qui serait une modification avec rupture binaire (bien que non source).</span><span class="sxs-lookup"><span data-stu-id="37f40-149">Therefore, the no-message overload would have to be removed to take advantage of this feature, which would be a binary (although not source) breaking change.</span></span>

## <a name="alternatives"></a><span data-ttu-id="37f40-150">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="37f40-150">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="37f40-151">Si la possibilité de voir le code source sur les sites d’appel pour les méthodes qui utilisent cet attribut est un problème, nous pouvons faire en sorte que les effets de l’attribut soient choisis.</span><span class="sxs-lookup"><span data-stu-id="37f40-151">If being able to see source code at call sites for methods that use this attribute proves to be a problem, we can make the attribute's effects opt-in.</span></span> <span data-ttu-id="37f40-152">Les développeurs l’activent via un attribut `[assembly: EnableCallerArgumentExpression]` à l’ensemble de l’assembly qu’ils placent dans `AssemblyInfo.cs`.</span><span class="sxs-lookup"><span data-stu-id="37f40-152">Developers will enable it through an assembly-wide `[assembly: EnableCallerArgumentExpression]` attribute they put in `AssemblyInfo.cs`.</span></span>
  - <span data-ttu-id="37f40-153">Si les effets de l’attribut ne sont pas activés, l’appel de méthodes marquées avec l’attribut n’est pas une erreur, pour permettre aux méthodes existantes d’utiliser l’attribut et de conserver la compatibilité source.</span><span class="sxs-lookup"><span data-stu-id="37f40-153">In the case the attribute's effects are not enabled, calling methods marked with the attribute would not be an error, to allow existing methods to use the attribute and maintain source compatibility.</span></span> <span data-ttu-id="37f40-154">Toutefois, l’attribut serait ignoré et la méthode serait appelée avec n’importe quelle valeur par défaut fournie.</span><span class="sxs-lookup"><span data-stu-id="37f40-154">However, the attribute would be ignored and the method would be called with whatever default value was provided.</span></span>

```csharp
// Assembly1

void Foo(string bar); // V1
void Foo(string bar, string barExpression = "not provided"); // V2
void Foo(string bar, [CallerArgumentExpression("bar")] string barExpression = "not provided"); // V3

// Assembly2

Foo(a); // V1: Compiles to Foo(a), V2, V3: Compiles to Foo(a, "not provided")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")

// Assembly3

[assembly: EnableCallerArgumentExpression]

Foo(a); // V1: Compiles to Foo(a), V2: Compiles to Foo(a, "not provided"), V3: Compiles to Foo(a, "a")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")
```

- <span data-ttu-id="37f40-155">Pour éviter que le [problème] [ drawbacks] de compatibilité binaire se produise chaque fois que nous souhaitons ajouter de nouvelles informations de l’appelant à `Debug.Assert`, une autre solution consiste à ajouter un struct `CallerInfo` à l’infrastructure qui contient toutes les informations nécessaires sur l’appelant.</span><span class="sxs-lookup"><span data-stu-id="37f40-155">To prevent the [binary compatibility problem][drawbacks] from occurring every time we want to add new caller info to `Debug.Assert`, an alternative solution would be to add a `CallerInfo` struct to the framework that contains all the necessary information about the caller.</span></span>

```csharp
struct CallerInfo
{
    public string MemberName { get; set; }
    public string TypeName { get; set; }
    public string Namespace { get; set; }
    public string FullTypeName { get; set; }
    public string FilePath { get; set; }
    public int LineNumber { get; set; }
    public int ColumnNumber { get; set; }
    public Type Type { get; set; }
    public MethodBase Method { get; set; }
    public string[] ArgumentExpressions { get; set; }
}

[Flags]
enum CallerInfoOptions
{
    MemberName = 1, TypeName = 2, ...
}

public static class Debug
{
    public static void Assert(bool condition,
        // If a flag is not set here, the corresponding CallerInfo member is not populated by the caller, so it's
        // pay-for-play friendly.
        [CallerInfo(CallerInfoOptions.FilePath | CallerInfoOptions.Method | CallerInfoOptions.ArgumentExpressions)] CallerInfo callerInfo = default(CallerInfo))
    {
        string filePath = callerInfo.FilePath;
        MethodBase method = callerInfo.Method;
        string conditionExpression = callerInfo.ArgumentExpressions[0];
        ...
    }
}

class Bar
{
    void Foo()
    {
        Debug.Assert(false);

        // Translates to:

        var callerInfo = new CallerInfo();
        callerInfo.FilePath = @"C:\Bar.cs";
        callerInfo.Method = MethodBase.GetCurrentMethod();
        callerInfo.ArgumentExpressions = new string[] { "false" };
        Debug.Assert(false, callerInfo);
    }
}
```

<span data-ttu-id="37f40-156">Il a été proposé à l’origine sur https://github.com/dotnet/csharplang/issues/87.</span><span class="sxs-lookup"><span data-stu-id="37f40-156">This was originally proposed at https://github.com/dotnet/csharplang/issues/87.</span></span>

<span data-ttu-id="37f40-157">Cette approche présente quelques inconvénients :</span><span class="sxs-lookup"><span data-stu-id="37f40-157">There are a few disadvantages of this approach:</span></span>

- <span data-ttu-id="37f40-158">Bien qu’il soit facile de payer pour la lecture en vous permettant de spécifier les propriétés dont vous avez besoin, il peut malgré tout nuire aux performances en allouant un tableau pour les expressions/`MethodBase.GetCurrentMethod` appelant même lorsque l’assertion réussit.</span><span class="sxs-lookup"><span data-stu-id="37f40-158">Despite being pay-for-play friendly by allowing you to specify which properties you need, it could still hurt perf significantly by allocating an array for the expressions/calling `MethodBase.GetCurrentMethod` even when the assert passes.</span></span>

- <span data-ttu-id="37f40-159">En outre, si le passage d’un nouvel indicateur à l’attribut `CallerInfo` ne constitue pas une modification avec rupture, `Debug.Assert` n’est pas assuré de recevoir réellement ce nouveau paramètre à partir des sites d’appel qui ont été compilés sur une ancienne version de la méthode.</span><span class="sxs-lookup"><span data-stu-id="37f40-159">Additionally, while passing a new flag to the `CallerInfo` attribute won't be a breaking change, `Debug.Assert` won't be guaranteed to actually receive that new parameter from call sites that compiled against an old version of the method.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="37f40-160">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="37f40-160">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="37f40-161">TBD</span><span class="sxs-lookup"><span data-stu-id="37f40-161">TBD</span></span>

## <a name="design-meetings"></a><span data-ttu-id="37f40-162">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="37f40-162">Design meetings</span></span>

<span data-ttu-id="37f40-163">N/A</span><span class="sxs-lookup"><span data-stu-id="37f40-163">N/A</span></span>
