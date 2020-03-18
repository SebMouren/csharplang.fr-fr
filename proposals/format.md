---
ms.openlocfilehash: 1457c1eb018e12af30ce5b38be704bf8851d4b25
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "79485013"
---
# <a name="efficient-params-and-string-formatting"></a>Paramètres et mise en forme de chaîne efficaces

## <a name="summary"></a>Résumé
Cette combinaison de fonctionnalités augmente l’efficacité de la mise en forme `string` valeurs et le passage d’arguments de style `params`.

## <a name="motivation"></a>Motivation
La surcharge d’allocation de la mise en forme des valeurs `string` peut dominer les performances de nombreuses applications basées sur du texte : à partir de la pénalité boxing des types de `struct`, l’allocation de `object[]` pour `params` et les allocations de `string` intermédiaires pendant les appels de `string.Format`. Pour maintenir l’efficacité, ces applications doivent souvent abandonner des fonctionnalités de productivité, telles que `params` et `string` l’interpolation, et passer à des solutions codées non standard. 

Considérez MSBuild comme un exemple. Cela est écrit à l’aide de nombreuses C# fonctionnalités modernes par les développeurs qui sont conscients des performances. Pourtant, dans un exemple de build représentative, MSBuild générera 262MB d’allocation de `string` à l’aide de commentaires minimes. De ces 1/2 de l’allocation sont des allocations éphémères à l’intérieur de `string.Format`. Ces fonctionnalités suppriment la majeure partie de ce dernier sur le bureau .NET et l’ont réduit à presque zéro sur .NET Core en raison de la disponibilité des `Span<T>`

L’ensemble des fonctionnalités de langage décrites ici permettra aux applications de continuer à utiliser ces fonctionnalités, avec très peu ou pas d’évolution vers leur base de code d’application, tout en supprimant la surcharge d’allocation imprévue dans la majorité des cas.

## <a name="detailed-design"></a>Conception détaillée 
Il existe un ensemble de fonctionnalités qui seront utilisées ici pour obtenir ces résultats :

- Le développement `params` pour prendre en charge un ensemble plus large de types de collections.
- Permettre aux développeurs de personnaliser la façon dont `string` interpolation est effectuée. 
- Autoriser les `string` interpolées à établir une liaison avec des surcharges de `string.Format` plus efficaces.

### <a name="extending-params"></a>Extension des paramètres
Le langage permettra aux `params` dans une signature de méthode d’avoir les types `Span<T>`, `ReadOnlySpan<T>` et `IEnumerable<T>`. Les mêmes règles pour l’appel s’appliqueront à ces nouveaux types qui s’appliquent à `params T[]`:

- Impossible de surcharger lorsque la seule différence est un mot clé `params`.
- Peut appeler en passant une série d’arguments qui sont implicitement convertibles en `T` ou une seule `Span<T>` / 
`ReadOnlySpan<T>` / `IEnumerable<T>`.
- Doit être le dernier paramètre dans une signature de méthode.
- Etc... 

Les variantes `Span<T>` et `ReadOnlySpan<T>` sont désignées comme `Span<T>` ci-dessous pour plus de simplicité. Dans les cas où le comportement de `ReadOnlySpan<T>` diffère, il sera explicitement appelé. 

L’avantage du `Span<T>` variantes de `params` est qu’il offre au compilateur une grande flexibilité dans la façon dont il alloue le stockage de sauvegarde pour la valeur de `Span<T>`. Avec un `params T[]` le compilateur doit allouer une nouvelle `T[]` pour chaque appel d’une méthode `params`. La réutilisation n’est pas possible, car elle doit supposer que l’appelé est stocké et réutilisé le paramètre. Cela peut entraîner une forte inefficacité des méthodes avec un grand nombre d’appels de `params`.

`Span<T>` variantes spécifiées sont `ref struct` l’appelé ne peut pas stocker l’argument. Par conséquent, le compilateur peut optimiser les sites d’appel en effectuant des actions telles que la réutilisation de l’argument. Cela peut rendre les appels répétés très efficaces par rapport à `T[]`. Toutefois, le langage n’apporte aucune garantie spécifique quant à l’optimisation de ces sites DLR. Notez uniquement que le compilateur est libre d’utiliser des valeurs autres que `T[]` lors de l’appel d’une méthode `params Span<T>`. 

Une telle implémentation potentielle est la suivante. Examinez tous les `params` appel dans un corps de méthode. Le compilateur peut allouer un tableau dont la taille est égale à la plus grande `params` appel et l’utilise pour tous les appels en créant des instances de `Span<T>` de taille appropriée sur le tableau. Par exemple :

``` csharp
static class OneAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("jaredpar");
        Use("hello", "world");
        Use("a", "longer", "set");
    }
}
```

Le compilateur peut choisir d’émettre le corps de `Go` comme suit :

``` csharp
    static void Go() {
        var args = new string[3];
        args[0] = "jaredpar";
        Use(new Span<string>(args, start: 0, length: 1));

        args[0] = "hello";
        args[1] = "world";
        Use(new Span<string>(args, start: 0, length: 2));

        args[0] = "a";
        args[1] = "longer";
        args[2] = "set";
        Use(new Span<string>(args, start: 0, length: 3));
   }
```

Cela peut réduire considérablement le nombre de tableaux alloués dans une application. Les allocations peuvent être encore plus réduites si le Runtime fournit des utilitaires pour l’allocation de piles plus intelligente des tableaux.

Cette optimisation ne peut cependant pas toujours être appliquée. Même si l’appelé ne peut pas capturer l’argument `params` il peut toujours être capturé dans l’appelant lorsqu’il existe un paramètre `ref` ou `out / ref` qui est lui-même un type `ref struct`. 

``` csharp
static class SneakyCapture {
    static ref int M(params Span<T> span) => ref span[0];

    static void Oops() {
        // This now holds onto the memory backing the Span<T> 
        ref int r = ref M(42);
    }
}
```

Toutefois, ces cas sont détectables de manière statique. Il se produit potentiellement lorsqu’il existe un retour `ref` ou un paramètre `ref struct` passé par `out` ou `ref`. Dans ce cas, le compilateur doit allouer une nouvelle `T[]` pour chaque appel. 

Plusieurs autres stratégies d’optimisation potentielles sont décrites à la fin de ce document.

Le `IEnumerable<T>` variant est une simple surcharge pratique. Elle est utile dans les scénarios qui utilisent fréquemment des `IEnumerable<T>`, mais qui ont également un grand nombre d’utilisations `params`. Lorsqu’elle est appelée dans `T` argument, le stockage de sauvegarde est alloué en tant que `T[]` de la même façon que `params T[]` est effectué aujourd’hui.

### <a name="params-overload-resolution-changes"></a>modifications de résolution de surcharge des paramètres
Cette proposition signifie que la langue présente désormais quatre variantes de `params`, le cas échéant. Il est raisonnable pour les méthodes de définir des surcharges de méthodes qui diffèrent uniquement par le type d’une `params` déclarations. 

Tenez compte du fait que `StringBuilder.AppendFormat` ajouterait certainement une surcharge de `params ReadOnlySpan<object>` en plus de la `params object[]`. Cela lui permettrait d’améliorer considérablement les performances en réduisant les allocations de collection sans avoir à modifier le code appelant. 

Pour faciliter cette opération, le langage introduira la règle suivante de rupture de la résolution de surcharge. Lorsque les méthodes candidates diffèrent uniquement par le paramètre `params`, les candidats sont préférés dans l’ordre suivant :

1. `ReadOnlySpan<T>`
1. `Span<T>`
1. `T[]`
1. `IEnumerable<T>`

Cet ordre est le plus efficace pour le cas général.

### <a name="variant"></a>Variant
CoreFX prototype un nouveau type géré nommé [Variant](https://github.com/dotnet/corefxlab/pull/2595). Ce type est destiné à être utilisé dans les API qui attendent des valeurs hétérogènes, mais ne souhaitent pas que la surcharge soit mise en oeuvre en utilisant `object` comme paramètre. Le type de `Variant` fournit le stockage universel, mais évite l’allocation Boxing pour les types les plus couramment utilisés. L’utilisation de ce type dans des API comme `string.Format` peut éliminer la surcharge de boxing dans la majorité des cas.

Ce type lui-même n’est pas nécessairement spécial pour le langage. Il est présenté dans ce document séparément, même s’il devient un détail d’implémentation d’autres parties de la proposition. 

### <a name="efficient-interpolated-strings"></a>Chaînes interpolées efficaces
Les chaînes interpolées sont une fonctionnalité courante mais inefficace C#dans. La syntaxe la plus courante, utilisant une `string` interpolée en tant que `string`, se traduit par un appel de `string.Format(string, params object[])`. Cela entraînera des allocations Boxing pour tous les types de valeur, les allocations de `string` intermédiaires, car l’implémentation utilise principalement `object.ToString` pour la mise en forme, ainsi que les allocations de tableau une fois que le nombre d’arguments dépasse la quantité de paramètres sur les surcharges « rapides » de `string.Format`. 

La langue modifie sa diminution de l’interpolation pour tenir compte des surcharges alternatives de `string.Format`. Elle prend en compte toutes les formes de `string.Format(string, params)` et sélectionne la « meilleure » surcharge qui satisfait les types d’arguments.
La surcharge de `params` optimale sera déterminée par les règles décrites ci-dessus. Cela signifie que les `string` interpolées peuvent désormais être liées à des surcharges très efficaces comme `string.Format(string format, params ReadOnlySpan<Variant> args)`. Dans de nombreux cas, cela entraîne la suppression de toutes les allocations intermédiaires.

### <a name="customizable-interpolated-strings"></a>Chaînes interpolées personnalisables
Les développeurs peuvent personnaliser le comportement des chaînes interpolées avec `FormattableString`. Contient les données qui sont placées dans une chaîne interpolée : le format `string` et les arguments en tant que tableau. Cela conserve toujours l’allocation de tableau boxing et d’arguments, ainsi que l’allocation pour `FormattableString` (il s’agit d’un `abstract class`). Il s’agit donc d’une utilisation minime des applications qui sont lourdes dans la mise en forme `string`.

Pour améliorer l’efficacité de la mise en forme des chaînes interpolées, le langage reconnaîtra un nouveau type : `System.ValueFormattableString`. Toutes les chaînes interpolées auront une conversion de type cible en ce type. Cette opération sera implémentée en traduisant la chaîne interpolée dans l’appel `ValueFormattableString.Create` exactement comme pour `FormattableString.Create` aujourd’hui. Le langage prend en charge toutes les options de `params` décrites dans ce document lors de la recherche de la méthode de `ValueFormattableString.Create` la plus appropriée. 

``` csharp
readonly struct ValueFormattableString {
    public static ValueFormattableString Create(Variant v) { ... } 
    public static ValueFormattableString Create(string s) { ... } 
    public static ValueFormattableString Create(string s, params ReadOnlySpan<Variant> collection) { ... } 
}

class ConsoleEx { 
    static void Write(ValueFormattableString f) { ... }
}

class Program { 
    static void Main() { 
        ConsoleEx.Write(42);
        ConsoleEx.Write($"hello {DateTime.UtcNow}");

        // Translates into 
        ConsoleEx.Write(ValueFormattableString.Create((Variant)42));
        ConsoleEx.Write(ValueFormattableString.Create(
            "hello {0}", 
            new Variant(DateTime.UtcNow));
    }
}
```

Les règles de résolution de surcharge seront modifiées pour préférer `ValueFormattableString` sur `string` lorsque l’argument est une chaîne interpolée. Cela signifie qu’il est utile d’avoir des surcharges qui diffèrent uniquement sur `string` et `ValueFormattableString`. Une telle surcharge aujourd’hui avec `FormattableString` n’est pas utile, car le compilateur préfèrera toujours la version `string` (sauf si le développeur utilise un cast explicite). 

## <a name="open-issues"></a>Problèmes ouverts

### <a name="valueformattablestring-breaking-change"></a>Modification avec rupture ValueFormattableString
La modification à préférer `ValueFormattableString` lors de la résolution de surcharge sur `string` est une modification avec rupture. Il est possible pour un développeur d’avoir défini un type appelé `ValueFormattableString` aujourd’hui et de l’utiliser dans des surcharges de méthode avec `string`. Cette modification proposée ferait en sorte que le compilateur sélectionne une surcharge différente une fois que cet ensemble de fonctionnalités a été implémenté. 

La possibilité de cela semble relativement faible. Le type a besoin du nom complet `System.ValueFormattableString` et il doit avoir `static` méthodes nommées `Create`. Étant donné que les développeurs sont fortement déconseillés de définir n’importe quel type dans l’espace de noms `System`, cet arrêt semble être un compromis raisonnable.

### <a name="expanding-to-more-types"></a>Développement d’un plus grand nombre de types
Étant donné que nous sommes dans le domaine, nous devons envisager d’ajouter `IList<T>`, `ICollection<T>` et `IReadOnlyList<T>` à l’ensemble des collections pour lesquelles `params` est pris en charge. En termes d’implémentation, le coût est réduit sur l’autre travail ici.

LDM doit décider si la complication du langage en est la plus intéressante. L’ajout de `IEnumerable<T>` supprime un point de frottement très spécifique. L’absence de cette `params` solution de nombreux clients étaient imposés à allouer des `T[]` à partir d’un `IEnumerable<T>` lors de l’appel d’une méthode `params`. Toutefois, l’ajout de `IEnumerable<T>` le corrige. Il n’existe pas de point de friction spécifique résolu ici par les autres interfaces. 

## <a name="considerations"></a>Considérations

### <a name="variant2-and-variant3"></a>Variant2 et Variant3
L’équipe CoreFX dispose également d’un ensemble de types de stockage qui ne permet pas d’allouer jusqu’à trois arguments `Variant`. Il s’agit d’un `Variant`unique, `Variant2` et `Variant3`. Tous ont une paire de méthodes permettant d’obtenir une allocation libre `Span<Variant>` de l’être : `CreateSpan` et `KeepAlive`. Cela signifie que pour un `params Span<Variant>` de trois arguments au maximum, le site d’appel peut être entièrement alloué gratuitement.

``` csharp
static class ZeroAllocation {
    static void Use(params Span<Variant> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

La méthode `Go` peut être abaissée aux éléments suivants :

``` csharp
static class ZeroAllocation {
    static void Go() {
        Variant2 _v;
        _v.Variant1 = new Variant("hello");
        _v.Variant2 = new Variant("word");
        Use(_v.CreateSpan());
        _v.KeepAlive();
    }
}
```

Cela nécessite très peu de travail au-dessus de la proposition pour réutiliser `T[]` entre les appels `params Span<T>`. Le compilateur a déjà besoin de gérer un temporaire par appel et de nettoyer le travail après (même si, dans un cas, il s’agit simplement de marquer un temp interne comme libre). 

Remarque : la fonction `KeepAlive` est uniquement nécessaire sur le bureau. Sur .NET Core, la méthode n’est pas disponible et, par conséquent, le compilateur n’émet pas d’appel à celui-ci.

### <a name="clr-stack-allocation-helpers"></a>Applications d’assistance de l’allocation de pile CLR
Le CLR fournit uniquement [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) pour l’allocation de pile de mémoire contiguë. Cette instruction est limitée en ce qu’elle fonctionne uniquement pour les types de `unmanaged`. Cela signifie qu’il ne peut pas être utilisé comme solution universelle pour allouer efficacement le stockage de sauvegarde pour `params 
 Span<T>`. 

Toutefois, cette limitation n’est pas une restriction fondamentale, mais plus un artefact de l’historique. Le CLR peut choisir d’ajouter de nouveaux codes d’opération/intrinsèques qui fournissent une allocation de pile universelle. Elles peuvent ensuite être utilisées pour allouer le stockage de sauvegarde pour la plupart des appels de `params Span<T>`.

``` csharp
static class BetterAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

La méthode `Go` peut être abaissée aux éléments suivants :

``` csharp
static class ZeroAllocation {
    static void Go() {
        Span<T> span = RuntimeIntrinsic.StackAlloc<string>(length: 2);
        span[0] = "hello";
        span[1] = "world";
        Use(span);
    }
}
```

Bien que cette approche soit très efficace dans le segment de mémoire, elle entraîne une utilisation supplémentaire de la pile. Dans un algorithme qui a une pile profonde et un grand nombre d’utilisations `params`, cela peut provoquer la génération d’une `StackOverflowException` où une allocation de `T[]` simple aboutirait. 

Malheureusement C# , il n’est pas possible de configurer le type d’analyse inter-méthode, où il pourrait déterminer si l’appel doit ou non utiliser l’allocation de pile ou de tas de `params`. Il peut uniquement considérer chaque appel seul.

Le CLR est la meilleure configuration pour effectuer ce type de détermination au moment de l’exécution. Par conséquent, il est probable que le runtime fournisse deux méthodes pour l’allocation de la pile universelle :

1. `Span<T> StackAlloc<T>(int length)`: cela a les mêmes comportements et limitations que `localloc`, sauf qu’elle peut fonctionner sur n’importe quel type `T`. 
1. `Span<T> MaybeStackAlloc<T>(int length)`: ce Runtime peut choisir d’implémenter cela en procédant à une allocation de pile ou de tas. Le runtime peut ensuite utiliser le contexte d’exécution dans lequel il est appelé pour déterminer la façon dont le `Span<T>` est alloué. Toutefois, l’appelant le traite toujours comme s’il était alloué à la pile.

Pour les cas très simples, comme un à deux arguments, C# le compilateur peut toujours utiliser `StackAlloc<T>` variant. Il est peu probable que cela contribue à réduire l’épuisement des piles dans la plupart des cas. Dans d’autres cas, le compilateur peut choisir d’utiliser `MaybeStackAlloc<T>` à la place et laisser le runtime effectuer l’appel.

La façon dont nous choisissons probablement une investigation plus approfondie et l’examen des applications réelles. Toutefois, si ces nouvelles fonctions intrinsèques sont disponibles, elles nous offrent ce type de flexibilité.

### <a name="why-not-varargs"></a>Pourquoi ne pas varargs ? 
La fonctionnalité [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) existante a été considérée comme étant une solution possible. Toutefois, cette fonctionnalité est principalement destinée C++aux scénarios/CLI et a des trous connus pour d’autres scénarios. En outre, le portage vers UNIX présente un coût considérable. Par conséquent, il n’a pas été considéré comme une solution viable.

## <a name="related-issues"></a>Problèmes connexes
Cette spécification est liée aux problèmes suivants : 

- https://github.com/dotnet/csharplang/issues/1757
- https://github.com/dotnet/csharplang/issues/179
- https://github.com/dotnet/corefxlab/pull/2595

