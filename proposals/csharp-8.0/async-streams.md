---
ms.openlocfilehash: 9ed1aa75d581cecbf754a84d1f523c6334b8c0ac
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485258"
---
# <a name="async-streams"></a>Flux asynchrones

* [x] proposé
* [x] prototype
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

C#prend en charge les méthodes d’itérateur et les méthodes Async, mais aucune prise en charge d’une méthode qui est à la fois un itérateur et une méthode Async.  Nous devons résoudre ce cas en autorisant l’utilisation de `await` dans une nouvelle forme de `async` itérateur, qui retourne un `IAsyncEnumerable<T>` ou `IAsyncEnumerator<T>` plutôt qu’un `IEnumerable<T>` ou `IEnumerator<T>`, avec `IAsyncEnumerable<T>` consommable dans une nouvelle `await foreach`.  Une interface `IAsyncDisposable` est également utilisée pour activer le nettoyage asynchrone.

## <a name="related-discussion"></a>Discussion connexe
- https://github.com/dotnet/roslyn/issues/261
- https://github.com/dotnet/roslyn/issues/114

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

## <a name="interfaces"></a>Interfaces

### <a name="iasyncdisposable"></a>IAsyncDisposable

Il y a eu une grande partie des `IAsyncDisposable` (par exemple, https://github.com/dotnet/roslyn/issues/114) et s’il s’agit d’une bonne idée.  Toutefois, il s’agit d’un concept obligatoire à ajouter pour la prise en charge des itérateurs Async.  Étant donné que les blocs `finally` peuvent contenir des `await`s et que les blocs `finally` doivent être exécutés dans le cadre de la suppression d’itérateurs, nous avons besoin d’une suppression asynchrone.  Il est également généralement utile que tout nettoyage des ressources peut prendre un certain temps, par exemple, pour fermer des fichiers (en exigeant des vidages), annuler l’inscription des rappels et fournir un moyen de savoir quand la désinscription est terminée, etc.

L’interface suivante est ajoutée aux bibliothèques .NET de base (par exemple, System. private. CoreLib/System. Runtime) :
```csharp
namespace System
{
    public interface IAsyncDisposable
    {
        ValueTask DisposeAsync();
    }
}
```
Comme avec `Dispose`, l’appel de `DisposeAsync` plusieurs fois est acceptable, et les appels suivants à la suite de la première doivent être traités comme des NOPS, ce qui a pour conséquence de retourner une tâche ayant abouti de manière synchrone (`DisposeAsync` n’avez pas besoin d’être thread-safe et ne nécessite pas de prise en charge de l’appel simultané).  En outre, les types peuvent implémenter à la fois des `IDisposable` et des `IAsyncDisposable`, et si c’est le cas, il est tout à fait acceptable d’appeler `Dispose`, puis `DisposeAsync` ou vice versa, mais seul le premier doit être significatif et les appels suivants doivent être un NOP.  Par conséquent, si un type implémente à la fois, les consommateurs sont encouragés à appeler une fois et une seule fois la méthode la plus pertinente en fonction du contexte, `Dispose` dans les contextes synchrones et les `DisposeAsync` dans des contextes asynchrones.

(Je laisse une discussion sur la façon dont `IAsyncDisposable` interagit avec `using` à une discussion distincte.  Et la manière dont elle interagit avec `foreach` est traitée plus loin dans cette proposition.)

Alternatives prises en compte :
- _`DisposeAsync` acceptant une `CancellationToken`_ : en théorie, il est logique que tout ce qui est asynchrone puisse être annulé, que la suppression se fasse à propos du nettoyage, de la fermeture des éléments, des ressources free’ing, etc., ce qui n’est généralement pas une action qui doit être annulée. le nettoyage est toujours important pour le travail annulé.  Le même `CancellationToken` qui a provoqué l’annulation du travail réel est généralement le même jeton passé à `DisposeAsync`, ce qui rend `DisposeAsync` inutile, car l’annulation du travail entraînerait l' `DisposeAsync` d’un NOP.  Si une personne souhaite éviter d’être bloquée en attente de suppression, elle peut éviter d’attendre la `ValueTask`résultante, ou l’attendre uniquement pendant une période donnée.
- _`DisposeAsync` retournant un `Task`_ : maintenant qu’il existe une `ValueTask` non générique et qu’elle peut être construite à partir d’une `IValueTaskSource`, le retour de `ValueTask` à partir d' `DisposeAsync` permet à un objet existant d’être réutilisé comme promesse représentant l’achèvement asynchrone éventuel de `DisposeAsync`, en enregistrant une allocation de `Task` dans le cas où `DisposeAsync` se termine de façon asynchrone.
- _Configuration de `DisposeAsync` avec un `bool continueOnCapturedContext` (`ConfigureAwait`)_ : bien qu’il puisse y avoir des problèmes liés à la façon dont ce concept est exposé à `using`, à la `foreach`et à d’autres constructions de langage qui consomment ce concept, du point de vue de l’interface, il ne fait rien de `await`et il n’y a rien à configurer... les consommateurs de l' `ValueTask` peuvent le consommer, mais ils le souhaitent.
- _`IAsyncDisposable` héritant `IDisposable`_ : étant donné qu’un seul ou l’autre doit être utilisé, il n’est pas judicieux de forcer les types à implémenter les deux.
- _`IDisposableAsync` au lieu de `IAsyncDisposable`_ : nous avons suivi l’appellation des éléments/types « asynchrone », alors que les opérations sont « asynchrones ». ainsi, les types ont « Async » comme préfixe et les méthodes ont « Async » comme suffixe.

### <a name="iasyncenumerable--iasyncenumerator"></a>IAsyncEnumerable/IAsyncEnumerator

Deux interfaces sont ajoutées aux bibliothèques .NET de base :

```csharp
namespace System.Collections.Generic
{
    public interface IAsyncEnumerable<out T>
    {
        IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken cancellationToken = default);
    }

    public interface IAsyncEnumerator<out T> : IAsyncDisposable
    {
        ValueTask<bool> MoveNextAsync();
        T Current { get; }
    }
}
```

La consommation classique (sans fonctionnalités de langage supplémentaires) ressemble à ceci :

```csharp
IAsyncEnumerator<T> enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.MoveNextAsync())
    {
        Use(enumerator.Current);
    }
}
finally { await enumerator.DisposeAsync(); }
```

Options ignorées prises en compte :
- _`Task<bool> MoveNextAsync(); T current { get; }`_ : l’utilisation de `Task<bool>` prend en charge l’utilisation d’un objet de tâche mis en cache pour représenter des appels de `MoveNextAsync` synchrones et réussis, mais une allocation est toujours nécessaire pour la saisie semi-automatique asynchrone.  En retournant `ValueTask<bool>`, nous autorisons l’objet Enumerator à lui-même à implémenter `IValueTaskSource<bool>` et à être utilisé comme stockage pour le `ValueTask<bool>` retourné à partir de `MoveNextAsync`, ce qui permet à son tour de réduire considérablement les surplus.
- _`ValueTask<(bool, T)> MoveNextAsync();`_ : ce n’est pas seulement plus difficile à consommer, mais cela signifie que les `T` ne peuvent plus être covariants.
- _`ValueTask<T?> TryMoveNextAsync();`_ : non covariant.
- _`Task<T?> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.
- _`ITask<T?> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.
- _`ITask<(bool,T)> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.
- _`Task<bool> TryMoveNextAsync(out T result);`_ : le résultat du `out` doit être défini lorsque l’opération retourne de manière synchrone, et non pas lorsqu’il termine de façon asynchrone la tâche potentiellement dans le futur. à ce stade, il n’y a aucun moyen de communiquer le résultat.
- _`IAsyncEnumerator<T>` n’implémentez pas `IAsyncDisposable`_ : nous pouvons choisir de les séparer.  Toutefois, cela complique certaines autres parties de la proposition, car le code doit être en mesure de faire face à la possibilité qu’un énumérateur ne fournisse pas de suppression, ce qui complique l’écriture des applications auxiliaires basées sur les modèles.  En outre, il est courant que les énumérateurs aient besoin d’être éliminés (par exemple C# , tout itérateur Async qui a un bloc finally, la plupart des éléments qui énumèrent les données d’une connexion réseau, etc.), et si ce n’est pas le cas, il est simple d’implémenter la méthode simplement comme `public ValueTask DisposeAsync() => default(ValueTask);` avec une surcharge supplémentaire minimale.
- _ `IAsyncEnumerator<T> GetAsyncEnumerator()`: aucun paramètre de jeton d’annulation.

#### <a name="viable-alternative"></a>Alternative viable :

```csharp
namespace System.Collections.Generic
{
    public interface IAsyncEnumerable<out T>
    {
        IAsyncEnumerator<T> GetAsyncEnumerator();
    }

    public interface IAsyncEnumerator<out T> : IAsyncDisposable
    {
        ValueTask<bool> WaitForNextAsync();
        T TryGetNext(out bool success);
    }
}
```

`TryGetNext` est utilisé dans une boucle interne pour consommer des éléments avec un seul appel d’interface, à condition qu’ils soient disponibles de façon synchrone.  Lorsque l’élément suivant ne peut pas être récupéré de façon synchrone, il retourne false, et chaque fois qu’il retourne false, un appelant doit appeler par la suite `WaitForNextAsync` pour attendre que l’élément suivant soit disponible ou pour déterminer qu’il ne sera jamais un autre élément. La consommation classique (sans fonctionnalités de langage supplémentaires) ressemble à ceci :

```csharp
IAsyncEnumerator<T> enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.WaitForNextAsync())
    {
        while (true)
        {
            int item = enumerator.TryGetNext(out bool success);
            if (!success) break;
            Use(item);
        }
    }
}
finally { await enumerator.DisposeAsync(); }
```

L’avantage est qu’il s’agit d’un double pli, d’un secondaire et d’un principal :
- _Minor : permet à un énumérateur de prendre en charge plusieurs consommateurs_. Il peut exister des scénarios dans lesquels il est utile qu’un énumérateur prenne en charge plusieurs consommateurs simultanés.  Cela ne peut pas être atteint quand `MoveNextAsync` et `Current` sont séparés de telle sorte qu’une implémentation ne peut pas rendre leur utilisation atomique.  En revanche, cette approche fournit une seule méthode `TryGetNext` qui prend en charge le push de l’énumérateur vers l’avant et l’obtention de l’élément suivant, de sorte que l’énumérateur peut activer l’atomicité si vous le souhaitez.  Toutefois, il est probable que ces scénarios peuvent également être activés en donnant à chaque consommateur son propre énumérateur à partir d’un énumérable partagé.  En outre, nous ne souhaitons pas imposer que chaque énumérateur prenne en charge l’utilisation simultanée, car cela ajouterait des surcharges non triviales au cas majoritaire qui n’en a pas besoin, ce qui signifie qu’un consommateur de l’interface ne pouvait généralement pas s’appuyer sur cette méthode.
- _Majeur : performances_. L’approche de l' `MoveNextAsync`/`Current` requiert deux appels d’interface par opération, tandis que le meilleur cas pour `WaitForNextAsync`/`TryGetNext` est que la plupart des itérations se terminent de manière synchrone, ce qui permet une boucle interne étroite avec `TryGetNext`, de sorte que nous n’ayons qu’un seul appel d’interface par opération.  Cela peut avoir un impact mesurable dans les situations où les appels d’interface dominent le calcul.

Toutefois, il existe des inconvénients non négligeables, y compris une complexité considérablement accrue lors de l’utilisation manuelle de ces derniers, ainsi qu’une meilleure chance d’introduire des bogues lors de leur utilisation.  Et bien que les avantages en matière de performances s’affichent dans les microtesteurs, nous ne pensons pas qu’ils seront impactés dans la grande majorité des utilisations réelles.  S’il s’avère que c’est le cas, nous pouvons introduire un deuxième ensemble d’interfaces de manière claire.

Options ignorées prises en compte :
- `ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: les paramètres `out` ne peuvent pas être covariants.  Il y a également un faible impact (un problème avec le modèle try en général) qui entraîne probablement une barrière d’écriture d’exécution pour les résultats de type référence.

#### <a name="cancellation"></a>Annulation

Il existe plusieurs approches possibles pour la prise en charge de l’annulation :
1. `IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` sont des annulations indépendantes : `CancellationToken` n’apparaît nulle part.  L’annulation est obtenue en regroupant logiquement les `CancellationToken` dans l’énumérateur et/ou énumérable de quelque manière que ce soit, par exemple, lors de l’appel d’un itérateur, en passant le `CancellationToken` en tant qu’argument à la méthode d’itérateur et en l’utilisant dans le corps de l’itérateur, comme c’est le cas avec tout autre paramètre.
2. `IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: vous transmettez un `CancellationToken` à `GetAsyncEnumerator`, et les opérations de `MoveNextAsync` suivantes le respectent toutefois.
3. `IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: vous transmettez un `CancellationToken` à chaque appel de `MoveNextAsync` individuel.
4. 1 & & 2 : incorporez `CancellationToken`s dans votre énumérable/énumérateur et transmettez `CancellationToken`s dans `GetAsyncEnumerator`.
5. 1 & & 3 : incorporez `CancellationToken`s dans votre énumérable/énumérateur et transmettez `CancellationToken`s dans `MoveNextAsync`.

D’un point de vue purement théorique, (5) est le plus robuste, dans ce (a) `MoveNextAsync` l’acceptation d’un `CancellationToken` permet le contrôle le plus affiné sur ce qui est annulé, et (b) `CancellationToken` est simplement un autre type qui peut être passé comme argument à des itérateurs, incorporés dans des types arbitraires, etc.

Toutefois, il existe plusieurs problèmes avec cette approche :
- Comment un `CancellationToken` passé à `GetAsyncEnumerator` le faire dans le corps de l’itérateur ?  Nous pouvons exposer un nouveau mot clé de `iterator` que vous pourriez déconnecter pour accéder aux `CancellationToken` passés à `GetEnumerator`, mais un) qui est un grand nombre de machines supplémentaires, b), c’est un citoyen de premier ordre et c) le cas 99% semble être le même code appelant un itérateur et appeler `GetAsyncEnumerator` dessus, auquel cas il peut simplement passer le `CancellationToken` en tant qu’argument dans la méthode.
- Comment un `CancellationToken` passé à `MoveNextAsync` peut-il se trouver dans le corps de la méthode ?  C’est encore pire, comme s’il s’agissait d’un objet local `iterator`, sa valeur peut changer dans await, ce qui signifie que tout code inscrit avec le jeton doit annuler son inscription avant d’attendre, puis se réinscrire après. Il est également potentiellement coûteux de procéder à l’inscription et à la désinscription dans chaque appel `MoveNextAsync`, qu’il soit implémenté par le compilateur dans un itérateur ou par un développeur manuellement.
- Comment un développeur annule-t-il une boucle de `foreach` ?  Si c’est le cas en attribuant un `CancellationToken` à un énumérable/énumérateur, puis à un), nous devons prendre en charge `foreach`les énumérateurs, ce qui leur permet d’être des citoyens de première classe. vous devez maintenant commencer à réfléchir à un écosystème développé autour d’énumérateurs (par exemple, des méthodes LINQ) ou b). nous devons tout de même incorporer les `CancellationToken` dans l’énumérable, de la même façon en utilisant une méthode d’extension de `WithCancellation` de `IAsyncEnumerable<T>` qui stockerait le jeton fourni, puis le passera dans le `GetAsyncEnumerator` `GetAsyncEnumerator` est appelé (en ignorant ce jeton).  Ou bien, vous pouvez simplement utiliser le `CancellationToken` que vous avez dans le corps de l’instruction foreach.
- Si/lorsque les interprétations des requêtes sont prises en charge, comment les `CancellationToken` fournies pour `GetEnumerator` ou `MoveNextAsync` être passées à chaque clause ?  La méthode la plus simple consisterait simplement à la capturer dans la clause, ce qui fait que le jeton est passé à `GetAsyncEnumerator`/`MoveNextAsync` est ignorée.

Une version antérieure de ce document est recommandée (1), mais nous avons passé à (4).

Les deux principaux problèmes avec (1) :
- les producteurs d’énumérables annulables doivent implémenter une certaine utilisation et peuvent uniquement tirer parti de la prise en charge par le compilateur des itérateurs Async pour implémenter une méthode `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)`.
- Il est probable que de nombreux producteurs soient tentés d’ajouter simplement un paramètre `CancellationToken` à leur signature Async-Enumerable à la place, ce qui empêchera les consommateurs de transmettre le jeton d’annulation souhaité lorsqu’ils reçoivent un type `IAsyncEnumerable`.

Il existe deux scénarios de consommation principaux :
1. `await foreach (var i in GetData(token)) ...` où le consommateur appelle la méthode Async-Iterator,
2. `await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` où le consommateur traite une instance `IAsyncEnumerable` donnée.

Nous constatons qu’une compromission raisonnable de la prise en charge des deux scénarios de manière pratique pour les producteurs et les consommateurs de Async-Streams consiste à utiliser un paramètre spécialement annoté dans la méthode Async-Iterator. L’attribut `[EnumeratorCancellation]` est utilisé à cet effet. Le fait de placer cet attribut sur un paramètre indique au compilateur que, si un jeton est passé à la méthode `GetAsyncEnumerator`, ce jeton doit être utilisé à la place de la valeur transmise à l’origine pour le paramètre.

Prenez le cas de `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`. L’implémenteur de cette méthode peut simplement utiliser le paramètre dans le corps de la méthode. Le consommateur peut utiliser les modèles de consommation ci-dessus :
1. Si vous utilisez `GetData(token)`, le jeton est enregistré dans Async-Enumerable et sera utilisé dans l’itération.
2. Si vous utilisez `givenIAsyncEnumerable.WithCancellation(token)`, le jeton passé à `GetAsyncEnumerator` remplacera tout jeton enregistré dans Async-Enumerable.

## <a name="foreach"></a>foreach

les `foreach` seront augmentées pour prendre en charge les `IAsyncEnumerable<T>` en plus de la prise en charge existante de `IEnumerable<T>`.  Et il prend en charge l’équivalent de `IAsyncEnumerable<T>` en tant que modèle si les membres pertinents sont exposés publiquement, en revenant à l’utilisation directe de l’interface si ce n’est pas le cas, afin d’activer les extensions basées sur un struct qui évitent d’allouer et d’utiliser d’autres awaitables comme type de retour de `MoveNextAsync` et `DisposeAsync`.

### <a name="syntax"></a>Syntaxe

À l’aide de la syntaxe :

```csharp
foreach (var i in enumerable)
```

C#continuera à traiter `enumerable` comme un énumérable synchrone, de sorte que même s’il expose les API pertinentes pour les énumérables Async (exposant le modèle ou implémentant l’interface), il prend uniquement en compte les API synchrones.

Pour forcer `foreach` à la place uniquement les API asynchrones, `await` est insérée comme suit :

```csharp
await foreach (var i in enumerable)
```

Aucune syntaxe n’est fournie pour prendre en charge l’utilisation des API Async ou Sync ; le développeur doit choisir en fonction de la syntaxe utilisée.

Options ignorées prises en compte :
- _`foreach (var i in await enumerable)`_ : il s’agit déjà d’une syntaxe valide, et la modification de sa signification serait une modification avec rupture.  Cela signifie que pour `await` le `enumerable`, récupérez un résultat pouvant être iteré de façon synchrone à partir de celui-ci, puis itérez de manière synchrone.
- _`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_ : ils suggèrent que nous attendons l’élément suivant, mais il existe d’autres await impliqués dans foreach, en particulier si l’énumérable est une `IAsyncDisposable`, nous allons `await`de sa suppression asynchrone.  Cette expression await correspond à la portée de foreach plutôt qu’à chaque élément individuel. par conséquent, le mot clé `await` mérite d’être au niveau `foreach`.  En outre, le fait de l’associer au `foreach` nous donne un moyen de décrire le `foreach` avec un terme différent, par exemple « await foreach ».  Mais plus important encore, il est important de tenir compte de `foreach` syntaxe en même temps que `using` syntaxe, afin qu’ils restent cohérents les uns avec les autres, et `using (await ...)` une syntaxe déjà valide.
- _`foreach await (var i in enumerable)`_

Toutefois, tenez compte des éléments suivants :
- `foreach` Today ne prend pas en charge l’itération au sein d’un énumérateur.  Nous pensons qu’il est plus courant d’avoir des `IAsyncEnumerator<T>`s, et il est donc tentant de prendre en charge les `await foreach` avec `IAsyncEnumerable<T>` et `IAsyncEnumerator<T>`.  Toutefois, une fois que nous ajouterons une telle prise en charge, il est question de savoir si `IAsyncEnumerator<T>` est un citoyen de première classe, et si nous devons avoir des surcharges de combinateurs qui fonctionnent sur les énumérateurs en plus des énumérables.    Souhaitez-vous encourager les méthodes pour retourner des énumérateurs plutôt que des énumérables ? Nous devons continuer à aborder ce sujet.  Si nous décidons de ne pas la prendre en charge, nous pourrions souhaiter introduire une méthode d’extension `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` qui permettrait à un énumérateur d’être toujours `foreach`.  Si nous décidons de le prendre en charge, nous devrons également décider si la `await foreach` est responsable de l’appel de `DisposeAsync` sur l’énumérateur et que la réponse est probablement « non, le contrôle de la suppression doit être géré par la personne appelée `GetEnumerator`».

### <a name="pattern-based-compilation"></a>Compilation basée sur des modèles

Le compilateur crée une liaison avec les API basées sur un modèle s’ils existent, en préférant ceux qui utilisent l’interface (le modèle peut être satisfait avec des méthodes d’instance ou des méthodes d’extension).  La configuration requise pour le modèle est la suivante :
- L’énumérable doit exposer une méthode `GetAsyncEnumerator` qui peut être appelée sans argument et qui retourne un énumérateur qui répond au modèle approprié.
- L’énumérateur doit exposer une méthode `MoveNextAsync` qui peut être appelée sans argument et qui retourne une valeur qui peut être `await`Ed et dont `GetResult()` retourne un `bool`.
- L’énumérateur doit également exposer `Current` propriété dont l’accesseur Get retourne un `T` représentant le type de données énumérées.
- L’énumérateur peut éventuellement exposer une méthode `DisposeAsync` qui peut être appelée sans argument et qui retourne une valeur qui peut être `await`Ed et dont `GetResult()` retourne `void`.

Ce code :

```csharp
var enumerable = ...;
await foreach (T item in enumerable)
{
   ...
}
```

est traduit en l’équivalent de :

```csharp
var enumerable = ...;
var enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.MoveNextAsync())
    {
       T item = enumerator.Current;
       ...
    }
}
finally
{
    await enumerator.DisposeAsync(); // omitted, along with the try/finally, if the enumerator doesn't expose DisposeAsync
}
```

Si le type itéré n’expose pas le modèle approprié, les interfaces sont utilisées.

### <a name="configureawait"></a>ConfigureAwait

Cette compilation basée sur des modèles permettra l’utilisation de `ConfigureAwait` sur toutes les await, par le biais d’une méthode d’extension `ConfigureAwait` :

```csharp
await foreach (T item in enumerable.ConfigureAwait(false))
{
   ...
}
```

Cela sera basé sur les types que nous ajouterons également à .NET, susceptibles de System. Threading. Tasks. extensions. dll :

```csharp
// Approximate implementation, omitting arg validation and the like
namespace System.Threading.Tasks
{
    public static class AsyncEnumerableExtensions
    {
        public static ConfiguredAsyncEnumerable<T> ConfigureAwait<T>(this IAsyncEnumerable<T> enumerable, bool continueOnCapturedContext) =>
            new ConfiguredAsyncEnumerable<T>(enumerable, continueOnCapturedContext);

        public struct ConfiguredAsyncEnumerable<T>
        {
            private readonly IAsyncEnumerable<T> _enumerable;
            private readonly bool _continueOnCapturedContext;

            internal ConfiguredAsyncEnumerable(IAsyncEnumerable<T> enumerable, bool continueOnCapturedContext)
            {
                _enumerable = enumerable;
                _continueOnCapturedContext = continueOnCapturedContext;
            }

            public ConfiguredAsyncEnumerator<T> GetAsyncEnumerator() =>
                new ConfiguredAsyncEnumerator<T>(_enumerable.GetAsyncEnumerator(), _continueOnCapturedContext);

            public struct Enumerator
            {
                private readonly IAsyncEnumerator<T> _enumerator;
                private readonly bool _continueOnCapturedContext;

                internal Enumerator(IAsyncEnumerator<T> enumerator, bool continueOnCapturedContext)
                {
                    _enumerator = enumerator;
                    _continueOnCapturedContext = continueOnCapturedContext;
                }

                public ConfiguredValueTaskAwaitable<bool> MoveNextAsync() =>
                    _enumerator.MoveNextAsync().ConfigureAwait(_continueOnCapturedContext);

                public T Current => _enumerator.Current;

                public ConfiguredValueTaskAwaitable DisposeAsync() =>
                    _enumerator.DisposeAsync().ConfigureAwait(_continueOnCapturedContext);
            }
        }
    }
}
```

Notez que cette approche n’autorise pas l’utilisation de `ConfigureAwait` avec les énumérables basés sur des modèles. mais là encore, il est déjà possible que le `ConfigureAwait` ne soit exposé qu’en tant qu’extension sur `Task`/`Task<T>`/`ValueTask`/`ValueTask<T>` et ne puisse pas être appliquée à des choses arbitraires, car il n’est utile que pour les tâches (il contrôle un comportement implémenté dans la prise en charge de continuation de la tâche).  Toute personne qui retourne des éléments await peut fournir son propre comportement personnalisé dans de tels scénarios avancés.

(Si nous pouvons trouver un moyen de prendre en charge une solution de `ConfigureAwait` au niveau de l’étendue ou de l’assembly, cela n’est pas nécessaire.)

## <a name="async-iterators"></a>Itérateurs Async

Le langage/compilateur prendra en charge la production `IAsyncEnumerable<T>`s et `IAsyncEnumerator<T>`s en plus de les consommer. Aujourd’hui, le langage prend en charge l’écriture d’un itérateur comme :

```csharp
static IEnumerable<int> MyIterator()
{
    try
    {
        for (int i = 0; i < 100; i++)
        {
            Thread.Sleep(1000);
            yield return i;
        }
    }
    finally
    {
        Thread.Sleep(200);
        Console.WriteLine("finally");
    }
}
```

mais `await` ne peuvent pas être utilisés dans le corps de ces itérateurs.  Nous allons ajouter cette prise en charge.

### <a name="syntax"></a>Syntaxe

La prise en charge du langage existant pour les itérateurs déduit la nature de l’itérateur de la méthode selon qu’elle contient des `yield`s.  Il en va de même pour les itérateurs Async.  Ces itérateurs Async seront dédélimités et différenciés des itérateurs synchrones via l’ajout d' `async` à la signature, et doivent alors également avoir `IAsyncEnumerable<T>` ou `IAsyncEnumerator<T>` comme type de retour.  Par exemple, l’exemple ci-dessus peut être écrit comme un itérateur Async comme suit :

```csharp
static async IAsyncEnumerable<int> MyIterator()
{
    try
    {
        for (int i = 0; i < 100; i++)
        {
            await Task.Delay(1000);
            yield return i;
        }
    }
    finally
    {
        await Task.Delay(200);
        Console.WriteLine("finally");
    }
}
```

Alternatives prises en compte :
- _N’utilisez pas `async` dans la signature_: l’utilisation de `async` est probablement techniquement requise par le compilateur, car il l’utilise pour déterminer si `await` est valide dans ce contexte.  Mais même si ce n’est pas obligatoire, nous avons établi que `await` ne peut être utilisé que dans les méthodes marquées comme `async`, et il semble important de conserver la cohérence.
- L' _activation de générateurs personnalisés pour `IAsyncEnumerable<T>`_ : ce que nous pourrions examiner pour le futur, mais la machine est compliquée et nous ne prenons pas en charge cette opération pour les homologues synchrones.
- _Présence d’un mot clé `iterator` dans la signature : les_itérateurs Async utilisent `async iterator` dans la signature, et `yield` ne peuvent être utilisés que dans les méthodes `async` incluses `iterator`; `iterator` est ensuite rendu facultatif sur les itérateurs synchrones.  En fonction de votre perspective, cela a l’avantage d’être très clair en ce qui concerne la signature de la méthode, qu' `yield` soit autorisé et que la méthode soit en fait supposée retourner des instances de type `IAsyncEnumerable<T>` plutôt que le compilateur en fabrication, selon que le code utilise `yield` ou non.  Mais il est différent des itérateurs synchrones, ce qui n’est pas et ne peut pas être fait pour en exiger un.  En outre, certains développeurs n’aiment pas la syntaxe supplémentaire.  Si nous étions en train de le concevoir à partir de zéro, nous aurions probablement effectué cette opération, mais à ce stade, il y a bien plus de valeur pour maintenir les itérateurs Async près des itérateurs de synchronisation.

## <a name="linq"></a>LINQ

Il y a plus de ~ 200 surcharges de méthodes sur la classe `System.Linq.Enumerable`, qui fonctionnent toutes en termes de `IEnumerable<T>`; certaines d’entre elles acceptent des `IEnumerable<T>`, certaines d’entre elles produisent des `IEnumerable<T>`, et beaucoup font les deux.  L’ajout de la prise en charge de LINQ pour `IAsyncEnumerable<T>` entraînerait probablement la duplication de toutes ces surcharges pour celle-ci, pour une autre ~ 200.  Et étant donné que `IAsyncEnumerator<T>` est susceptible d’être plus courant en tant qu’entité autonome dans le monde asynchrone que `IEnumerator<T>` se trouve dans le monde synchrone, nous pourrions potentiellement avoir besoin d’une autre surcharges ~ 200 qui fonctionnent avec `IAsyncEnumerator<T>`.  En outre, un grand nombre de surcharges traitent les prédicats (par exemple, `Where` qui prend un `Func<T, bool>`) et il peut être souhaitable d’avoir des surcharges de `IAsyncEnumerable<T>`qui traitent à la fois des prédicats synchrones et asynchrones (par exemple, `Func<T, ValueTask<bool>>` en plus de `Func<T, bool>`).  Bien que cela ne s’applique pas à toutes les nouvelles surcharges de plus de 400, un calcul approximatif est qu’elle s’applique à la moitié, ce qui signifie une autre ~ 200 surcharges, pour un total de ~ 600 nouvelles méthodes.

Il s’agit d’un nombre d’API en quinconce, avec un potentiel encore plus possible lorsque des bibliothèques d’extensions telles que les extensions interactives (IX) sont prises en compte.  Mais IX a déjà une implémentation de beaucoup d’entre elles, et il n’existe pas de bonne raison de dupliquer ce travail. Nous devons à la place aider la communauté à améliorer IX et la recommander quand les développeurs souhaitent utiliser LINQ avec `IAsyncEnumerable<T>`.

Il y a également le problème de syntaxe d’interprétation des requêtes.  La nature basée sur les modèles des interprétations des requêtes leur permettrait de « travailler » avec certains opérateurs, par exemple, si IX fournit les méthodes suivantes :

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, TResult> func);
public static IAsyncEnumerable<T> Where(this IAsyncEnumerable<T> source, Func<T, bool> func);
```

ce C# code fonctionnera alors « tout simplement » :

```csharp
IAsyncEnumerable<int> enumerable = ...;
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select item * 2;
```

Toutefois, il n’existe aucune syntaxe d’interprétation des requêtes qui prend en charge l’utilisation de `await` dans les clauses, donc si IX a été ajouté, par exemple :

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, ValueTask<TResult>> func);
```

Cela ne fonctionnerait alors que :

```csharp
IAsyncEnumerable<string> result = from url in urls
                                  where item % 2 == 0
                                  select SomeAsyncMethod(item);

async ValueTask<int> SomeAsyncMethod(int item)
{
    await Task.Yield();
    return item * 2;
}
```

mais il n’y avait aucun moyen de l’écrire avec le `await` inline dans la clause `select`.  Dans le cadre d’un travail distinct, nous pourrions envisager d’ajouter des expressions `async { ... }`es dans le langage. à ce stade, nous pourrions les autoriser à être utilisés dans les interprétations des requêtes, et il serait préférable d’écrire le contenu ci-dessus comme suit :

```csharp
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select async
                               {
                                   await Task.Yield();
                                   return item * 2;
                               };
```

ou pour permettre l’utilisation directe de `await` dans des expressions, par exemple en prenant en charge `async from`.  Toutefois, il est peu probable qu’une conception ici ait un impact sur le reste de l’ensemble de fonctionnalités, ou sur l’autre, et ce n’est pas une chose particulièrement importante pour investir à l’heure actuelle. par conséquent, la proposition consiste à ne rien faire d’autre ici pour le moment.

## <a name="integration-with-other-asynchronous-frameworks"></a>Intégration à d’autres frameworks asynchrones

L’intégration avec `IObservable<T>` et d’autres frameworks asynchrones (par exemple, les flux réactifs) est effectuée au niveau de la bibliothèque plutôt qu’au niveau de la langue.  Par exemple, toutes les données d’un `IAsyncEnumerator<T>` peuvent être publiées sur un `IObserver<T>` simplement en `await foreach`ant sur l’énumérateur et `OnNext`de données à l’observateur. une méthode d’extension `AsObservable<T>` est donc possible.  L’utilisation d’un `IObservable<T>` dans un `await foreach` nécessite la mise en mémoire tampon des données (au cas où un autre élément est envoyé alors que l’élément précédent est toujours en cours de traitement), mais un tel adaptateur push-pull peut facilement être implémenté pour permettre l’extraction d’un `IObservable<T>` à partir d’un `IAsyncEnumerator<T>`.  Suite.  RX/IX fournit déjà des prototypes de telles implémentations, et les bibliothèques comme https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels fournissent différents types de structures de données de mise en mémoire tampon.  La langue n’est pas nécessaire à ce niveau.
