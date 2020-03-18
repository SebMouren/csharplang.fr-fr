---
ms.openlocfilehash: fa3326bf69c83b6042b1db7b5567fd5c28d6f81a
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484523"
---
# <a name="callerargumentexpression"></a>CallerArgumentExpression

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Permet aux développeurs de capturer les expressions passées à une méthode, afin d’améliorer les messages d’erreur dans les API de diagnostic/test et de réduire les séquences de touches.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

En cas d’échec de la validation d’une assertion ou d’un argument, le développeur souhaite savoir autant que possible l’emplacement et la raison de l’échec. Toutefois, les API de diagnostic d’aujourd’hui ne facilitent pas cette opération. Considérez la méthode suivante :

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null);
    Debug.Assert(array.Length == 1);

    return array[0];
}
```

Lorsque l’une des assertions échoue, seuls le nom de fichier, le numéro de ligne et le nom de la méthode seront fournis dans la trace de la pile. Le développeur ne peut pas déterminer quelle assertion a échoué à partir de ces informations (s) il doit ouvrir le fichier et accéder au numéro de ligne fourni pour voir ce qui s’est produit.

C’est également la raison pour laquelle les infrastructures de test doivent fournir une variété de méthodes d’assertion. Avec xUnit, `Assert.True` et `Assert.False` ne sont pas fréquemment utilisés, car ils ne fournissent pas suffisamment de contexte sur ce qui a échoué.

Bien que la situation soit un peu mieux pour la validation des arguments, car les noms des arguments non valides sont présentés au développeur, le développeur doit passer ces noms aux exceptions manuellement. Si l’exemple ci-dessus a été réécrit pour utiliser la validation d’argument traditionnelle au lieu de `Debug.Assert`, il ressemble à ceci :

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

Notez que `nameof(array)` doit être passé à chaque exception, bien qu’il soit déjà clair dans le contexte de l’argument non valide.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Dans les exemples ci-dessus, l’inclusion de la chaîne `"array != null"` ou `"array.Length == 1"` dans le message d’assertion permettrait au développeur de déterminer ce qui a échoué. Entrez `CallerArgumentExpression`: il s’agit d’un attribut que l’infrastructure peut utiliser pour obtenir la chaîne associée à un argument de méthode particulier. Nous l’ajouterons à `Debug.Assert` comme c’est le cas

```csharp
public static class Debug
{
    public static void Assert(bool condition, [CallerArgumentExpression("condition")] string message = null);
}
```

Le code source de l’exemple ci-dessus reste le même. Toutefois, le code que le compilateur émet réellement correspond à

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null, "array != null");
    Debug.Assert(array.Length == 1, "array.Length == 1");

    return array[0];
}
```

Le compilateur reconnaît spécifiquement l’attribut sur `Debug.Assert`. Il passe la chaîne associée à l’argument référencé dans le constructeur de l’attribut (dans ce cas, `condition`) sur le site d’appel. En cas d’échec de la méthode Assert, le développeur verra la condition false et déterminera celle qui a échoué.

Pour la validation d’argument, l’attribut ne peut pas être utilisé directement, mais peut être utilisé par le biais d’une classe d’assistance :

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

Une proposition d’ajout d’une telle classe d’assistance à l’infrastructure est en cours d' https://github.com/dotnet/corefx/issues/17068. Si cette fonctionnalité de langage a été implémentée, la proposition peut être mise à jour pour tirer parti de cette fonctionnalité.

### <a name="extension-methods"></a>Méthodes d’extension

Le paramètre `this` dans une méthode d’extension peut être référencé par `CallerArgumentExpression`. Par exemple :

```csharp
public static void ShouldBe<T>(this T @this, T expected, [CallerArgumentExpression("this")] string thisExpression = null) {}

contestant.Points.ShouldBe(1337); // thisExpression: "contestant.Points"
```

`thisExpression` recevra l’expression qui correspond à l’objet avant le point. S’il est appelé avec la syntaxe de méthode statique, par exemple `Ext.ShouldBe(contestant.Points, 1337)`, il se comporte comme si le premier paramètre n’était pas marqué comme `this`.

Il doit toujours y avoir une expression correspondant au paramètre `this`. Même si une instance d’une classe appelle une méthode d’extension sur elle-même, par exemple `this.Single()` à l’intérieur d’un type de collection, le `this` est mandaté par le compilateur afin que `"this"` soit passé. Si cette règle est modifiée à l’avenir, nous pouvons envisager de passer `null` ou la chaîne vide.

### <a name="extra-details"></a>Détails supplémentaires

- Comme les autres attributs de `Caller*`, tels que `CallerMemberName`, cet attribut ne peut être utilisé que sur des paramètres avec des valeurs par défaut.
- Plusieurs paramètres marqués avec `CallerArgumentExpression` sont autorisés, comme indiqué ci-dessus.
- L’espace de noms de l’attribut sera `System.Runtime.CompilerServices`.
- Si `null` ou une chaîne qui n’est pas un nom de paramètre (par exemple, `"notAParameterName"`) est fourni, le compilateur passera une chaîne vide.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

- Les personnes qui savent comment utiliser les décompilateurs pourront voir une partie du code source sur les sites d’appel pour les méthodes marquées avec cet attribut. Cela peut être indésirable/inattendu pour les logiciels de source fermée.

- Bien qu’il ne s’agisse pas d’un défaut dans la fonctionnalité elle-même, une source de préoccupation peut être qu’il existe une API `Debug.Assert` aujourd’hui qui ne prend qu’une `bool`. Même si la surcharge acceptant un message avait son deuxième paramètre marqué avec cet attribut et rendu facultatif, le compilateur sélectionne toujours le non-message dans la résolution de surcharge. Par conséquent, la surcharge sans message doit être supprimée pour tirer parti de cette fonctionnalité, qui serait une modification avec rupture binaire (bien que non source).

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

- Si la possibilité de voir le code source sur les sites d’appel pour les méthodes qui utilisent cet attribut est un problème, nous pouvons faire en sorte que les effets de l’attribut soient choisis. Les développeurs l’activent via un attribut `[assembly: EnableCallerArgumentExpression]` à l’ensemble de l’assembly qu’ils placent dans `AssemblyInfo.cs`.
  - Si les effets de l’attribut ne sont pas activés, l’appel de méthodes marquées avec l’attribut n’est pas une erreur, pour permettre aux méthodes existantes d’utiliser l’attribut et de conserver la compatibilité source. Toutefois, l’attribut serait ignoré et la méthode serait appelée avec n’importe quelle valeur par défaut fournie.

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

- Pour éviter que le [problème] [ drawbacks] de compatibilité binaire se produise chaque fois que nous souhaitons ajouter de nouvelles informations de l’appelant à `Debug.Assert`, une autre solution consiste à ajouter un struct `CallerInfo` à l’infrastructure qui contient toutes les informations nécessaires sur l’appelant.

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

Il a été proposé à l’origine sur https://github.com/dotnet/csharplang/issues/87.

Cette approche présente quelques inconvénients :

- Bien qu’il soit facile de payer pour la lecture en vous permettant de spécifier les propriétés dont vous avez besoin, il peut malgré tout nuire aux performances en allouant un tableau pour les expressions/`MethodBase.GetCurrentMethod` appelant même lorsque l’assertion réussit.

- En outre, si le passage d’un nouvel indicateur à l’attribut `CallerInfo` ne constitue pas une modification avec rupture, `Debug.Assert` n’est pas assuré de recevoir réellement ce nouveau paramètre à partir des sites d’appel qui ont été compilés sur une ancienne version de la méthode.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

TBD

## <a name="design-meetings"></a>Réunions de conception

N/A
