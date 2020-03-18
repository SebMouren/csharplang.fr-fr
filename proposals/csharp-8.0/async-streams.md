---
ms.openlocfilehash: 9ed1aa75d581cecbf754a84d1f523c6334b8c0ac
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485258"
---
# <a name="async-streams"></a><span data-ttu-id="01b1c-101">Flux asynchrones</span><span class="sxs-lookup"><span data-stu-id="01b1c-101">Async Streams</span></span>

* <span data-ttu-id="01b1c-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="01b1c-102">[x] Proposed</span></span>
* <span data-ttu-id="01b1c-103">[x] prototype</span><span class="sxs-lookup"><span data-stu-id="01b1c-103">[x] Prototype</span></span>
* <span data-ttu-id="01b1c-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="01b1c-104">[ ] Implementation</span></span>
* <span data-ttu-id="01b1c-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="01b1c-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="01b1c-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="01b1c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="01b1c-107">C#prend en charge les méthodes d’itérateur et les méthodes Async, mais aucune prise en charge d’une méthode qui est à la fois un itérateur et une méthode Async.</span><span class="sxs-lookup"><span data-stu-id="01b1c-107">C# has support for iterator methods and async methods, but no support for a method that is both an iterator and an async method.</span></span>  <span data-ttu-id="01b1c-108">Nous devons résoudre ce cas en autorisant l’utilisation de `await` dans une nouvelle forme de `async` itérateur, qui retourne un `IAsyncEnumerable<T>` ou `IAsyncEnumerator<T>` plutôt qu’un `IEnumerable<T>` ou `IEnumerator<T>`, avec `IAsyncEnumerable<T>` consommable dans une nouvelle `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-108">We should rectify this by allowing for `await` to be used in a new form of `async` iterator, one that returns an `IAsyncEnumerable<T>` or `IAsyncEnumerator<T>` rather than an `IEnumerable<T>` or `IEnumerator<T>`, with `IAsyncEnumerable<T>` consumable in a new `await foreach`.</span></span>  <span data-ttu-id="01b1c-109">Une interface `IAsyncDisposable` est également utilisée pour activer le nettoyage asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-109">An `IAsyncDisposable` interface is also used to enable asynchronous cleanup.</span></span>

## <a name="related-discussion"></a><span data-ttu-id="01b1c-110">Discussion connexe</span><span class="sxs-lookup"><span data-stu-id="01b1c-110">Related discussion</span></span>
- https://github.com/dotnet/roslyn/issues/261
- https://github.com/dotnet/roslyn/issues/114

## <a name="detailed-design"></a><span data-ttu-id="01b1c-111">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="01b1c-111">Detailed design</span></span>
[design]: #detailed-design

## <a name="interfaces"></a><span data-ttu-id="01b1c-112">Interfaces</span><span class="sxs-lookup"><span data-stu-id="01b1c-112">Interfaces</span></span>

### <a name="iasyncdisposable"></a><span data-ttu-id="01b1c-113">IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="01b1c-113">IAsyncDisposable</span></span>

<span data-ttu-id="01b1c-114">Il y a eu une grande partie des `IAsyncDisposable` (par exemple, https://github.com/dotnet/roslyn/issues/114) et s’il s’agit d’une bonne idée.</span><span class="sxs-lookup"><span data-stu-id="01b1c-114">There has been much discussion of `IAsyncDisposable` (e.g. https://github.com/dotnet/roslyn/issues/114) and whether it's a good idea.</span></span>  <span data-ttu-id="01b1c-115">Toutefois, il s’agit d’un concept obligatoire à ajouter pour la prise en charge des itérateurs Async.</span><span class="sxs-lookup"><span data-stu-id="01b1c-115">However, it's a required concept to add in support of async iterators.</span></span>  <span data-ttu-id="01b1c-116">Étant donné que les blocs `finally` peuvent contenir des `await`s et que les blocs `finally` doivent être exécutés dans le cadre de la suppression d’itérateurs, nous avons besoin d’une suppression asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-116">Since `finally` blocks may contain `await`s, and since `finally` blocks need to be run as part of disposing of iterators, we need async disposal.</span></span>  <span data-ttu-id="01b1c-117">Il est également généralement utile que tout nettoyage des ressources peut prendre un certain temps, par exemple, pour fermer des fichiers (en exigeant des vidages), annuler l’inscription des rappels et fournir un moyen de savoir quand la désinscription est terminée, etc.</span><span class="sxs-lookup"><span data-stu-id="01b1c-117">It's also just generally useful any time cleaning up of resources might take any period of time, e.g. closing files (requiring flushes), deregistering callbacks and providing a way to know when deregistration has completed, etc.</span></span>

<span data-ttu-id="01b1c-118">L’interface suivante est ajoutée aux bibliothèques .NET de base (par exemple, System. private. CoreLib/System. Runtime) :</span><span class="sxs-lookup"><span data-stu-id="01b1c-118">The following interface is added to the core .NET libraries (e.g. System.Private.CoreLib / System.Runtime):</span></span>
```csharp
namespace System
{
    public interface IAsyncDisposable
    {
        ValueTask DisposeAsync();
    }
}
```
<span data-ttu-id="01b1c-119">Comme avec `Dispose`, l’appel de `DisposeAsync` plusieurs fois est acceptable, et les appels suivants à la suite de la première doivent être traités comme des NOPS, ce qui a pour conséquence de retourner une tâche ayant abouti de manière synchrone (`DisposeAsync` n’avez pas besoin d’être thread-safe et ne nécessite pas de prise en charge de l’appel simultané).</span><span class="sxs-lookup"><span data-stu-id="01b1c-119">As with `Dispose`, invoking `DisposeAsync` multiple times is acceptable, and subsequent invocations after the first should be treated as nops, returning a synchronously completed successful task (`DisposeAsync` need not be thread-safe, though, and need not support concurrent invocation).</span></span>  <span data-ttu-id="01b1c-120">En outre, les types peuvent implémenter à la fois des `IDisposable` et des `IAsyncDisposable`, et si c’est le cas, il est tout à fait acceptable d’appeler `Dispose`, puis `DisposeAsync` ou vice versa, mais seul le premier doit être significatif et les appels suivants doivent être un NOP.</span><span class="sxs-lookup"><span data-stu-id="01b1c-120">Further, types may implement both `IDisposable` and `IAsyncDisposable`, and if they do, it's similarly acceptable to invoke `Dispose` and then `DisposeAsync` or vice versa, but only the first should be meaningful and subsequent invocations of either should be a nop.</span></span>  <span data-ttu-id="01b1c-121">Par conséquent, si un type implémente à la fois, les consommateurs sont encouragés à appeler une fois et une seule fois la méthode la plus pertinente en fonction du contexte, `Dispose` dans les contextes synchrones et les `DisposeAsync` dans des contextes asynchrones.</span><span class="sxs-lookup"><span data-stu-id="01b1c-121">As such, if a type does implement both, consumers are encouraged to call once and only once the more relevant method based on the context, `Dispose` in synchronous contexts and `DisposeAsync` in asynchronous ones.</span></span>

<span data-ttu-id="01b1c-122">(Je laisse une discussion sur la façon dont `IAsyncDisposable` interagit avec `using` à une discussion distincte.</span><span class="sxs-lookup"><span data-stu-id="01b1c-122">(I'm leaving discussion of how `IAsyncDisposable` interacts with `using` to a separate discussion.</span></span>  <span data-ttu-id="01b1c-123">Et la manière dont elle interagit avec `foreach` est traitée plus loin dans cette proposition.)</span><span class="sxs-lookup"><span data-stu-id="01b1c-123">And coverage of how it interacts with `foreach` is handled later in this proposal.)</span></span>

<span data-ttu-id="01b1c-124">Alternatives prises en compte :</span><span class="sxs-lookup"><span data-stu-id="01b1c-124">Alternatives considered:</span></span>
- <span data-ttu-id="01b1c-125">_`DisposeAsync` acceptant une `CancellationToken`_ : en théorie, il est logique que tout ce qui est asynchrone puisse être annulé, que la suppression se fasse à propos du nettoyage, de la fermeture des éléments, des ressources free’ing, etc., ce qui n’est généralement pas une action qui doit être annulée. le nettoyage est toujours important pour le travail annulé.</span><span class="sxs-lookup"><span data-stu-id="01b1c-125">_`DisposeAsync` accepting a `CancellationToken`_: while in theory it makes sense that anything async can be canceled, disposal is about cleanup, closing things out, free'ing resources, etc., which is generally not something that should be canceled; cleanup is still important for work that's canceled.</span></span>  <span data-ttu-id="01b1c-126">Le même `CancellationToken` qui a provoqué l’annulation du travail réel est généralement le même jeton passé à `DisposeAsync`, ce qui rend `DisposeAsync` inutile, car l’annulation du travail entraînerait l' `DisposeAsync` d’un NOP.</span><span class="sxs-lookup"><span data-stu-id="01b1c-126">The same `CancellationToken` that caused the actual work to be canceled would typically be the same token passed to `DisposeAsync`, making `DisposeAsync` worthless because cancellation of the work would cause `DisposeAsync` to be a nop.</span></span>  <span data-ttu-id="01b1c-127">Si une personne souhaite éviter d’être bloquée en attente de suppression, elle peut éviter d’attendre la `ValueTask`résultante, ou l’attendre uniquement pendant une période donnée.</span><span class="sxs-lookup"><span data-stu-id="01b1c-127">If someone wants to avoid being blocked waiting for disposal, they can avoid waiting on the resulting `ValueTask`, or wait on it only for some period of time.</span></span>
- <span data-ttu-id="01b1c-128">_`DisposeAsync` retournant un `Task`_ : maintenant qu’il existe une `ValueTask` non générique et qu’elle peut être construite à partir d’une `IValueTaskSource`, le retour de `ValueTask` à partir d' `DisposeAsync` permet à un objet existant d’être réutilisé comme promesse représentant l’achèvement asynchrone éventuel de `DisposeAsync`, en enregistrant une allocation de `Task` dans le cas où `DisposeAsync` se termine de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-128">_`DisposeAsync` returning a `Task`_: Now that a non-generic `ValueTask` exists and can be constructed from an `IValueTaskSource`, returning `ValueTask` from `DisposeAsync` allows an existing object to be reused as the promise representing the eventual async completion of `DisposeAsync`, saving a `Task` allocation in the case where `DisposeAsync` completes asynchronously.</span></span>
- <span data-ttu-id="01b1c-129">_Configuration de `DisposeAsync` avec un `bool continueOnCapturedContext` (`ConfigureAwait`)_ : bien qu’il puisse y avoir des problèmes liés à la façon dont ce concept est exposé à `using`, à la `foreach`et à d’autres constructions de langage qui consomment ce concept, du point de vue de l’interface, il ne fait rien de `await`et il n’y a rien à configurer... les consommateurs de l' `ValueTask` peuvent le consommer, mais ils le souhaitent.</span><span class="sxs-lookup"><span data-stu-id="01b1c-129">_Configuring `DisposeAsync` with a `bool continueOnCapturedContext` (`ConfigureAwait`)_: While there may be issues related to how such a concept is exposed to `using`, `foreach`, and other language constructs that consume this, from an interface perspective it's not actually doing any `await`'ing and there's nothing to configure... consumers of the `ValueTask` can consume it however they wish.</span></span>
- <span data-ttu-id="01b1c-130">_`IAsyncDisposable` héritant `IDisposable`_ : étant donné qu’un seul ou l’autre doit être utilisé, il n’est pas judicieux de forcer les types à implémenter les deux.</span><span class="sxs-lookup"><span data-stu-id="01b1c-130">_`IAsyncDisposable` inheriting `IDisposable`_:  Since only one or the other should be used, it doesn't make sense to force types to implement both.</span></span>
- <span data-ttu-id="01b1c-131">_`IDisposableAsync` au lieu de `IAsyncDisposable`_ : nous avons suivi l’appellation des éléments/types « asynchrone », alors que les opérations sont « asynchrones ». ainsi, les types ont « Async » comme préfixe et les méthodes ont « Async » comme suffixe.</span><span class="sxs-lookup"><span data-stu-id="01b1c-131">_`IDisposableAsync` instead of `IAsyncDisposable`_: We've been following the naming that things/types are an "async something" whereas operations are "done async", so types have "Async" as a prefix and methods have "Async" as a suffix.</span></span>

### <a name="iasyncenumerable--iasyncenumerator"></a><span data-ttu-id="01b1c-132">IAsyncEnumerable/IAsyncEnumerator</span><span class="sxs-lookup"><span data-stu-id="01b1c-132">IAsyncEnumerable / IAsyncEnumerator</span></span>

<span data-ttu-id="01b1c-133">Deux interfaces sont ajoutées aux bibliothèques .NET de base :</span><span class="sxs-lookup"><span data-stu-id="01b1c-133">Two interfaces are added to the core .NET libraries:</span></span>

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

<span data-ttu-id="01b1c-134">La consommation classique (sans fonctionnalités de langage supplémentaires) ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="01b1c-134">Typical consumption (without additional language features) would look like:</span></span>

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

<span data-ttu-id="01b1c-135">Options ignorées prises en compte :</span><span class="sxs-lookup"><span data-stu-id="01b1c-135">Discarded options considered:</span></span>
- <span data-ttu-id="01b1c-136">_`Task<bool> MoveNextAsync(); T current { get; }`_ : l’utilisation de `Task<bool>` prend en charge l’utilisation d’un objet de tâche mis en cache pour représenter des appels de `MoveNextAsync` synchrones et réussis, mais une allocation est toujours nécessaire pour la saisie semi-automatique asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-136">_`Task<bool> MoveNextAsync(); T current { get; }`_: Using `Task<bool>` would support using a cached task object to represent synchronous, successful `MoveNextAsync` calls, but an allocation would still be required for asynchronous completion.</span></span>  <span data-ttu-id="01b1c-137">En retournant `ValueTask<bool>`, nous autorisons l’objet Enumerator à lui-même à implémenter `IValueTaskSource<bool>` et à être utilisé comme stockage pour le `ValueTask<bool>` retourné à partir de `MoveNextAsync`, ce qui permet à son tour de réduire considérablement les surplus.</span><span class="sxs-lookup"><span data-stu-id="01b1c-137">By returning `ValueTask<bool>`, we enable the enumerator object to itself implement `IValueTaskSource<bool>` and be used as the backing for the `ValueTask<bool>` returned from `MoveNextAsync`, which in turn allows for significantly reduced overheads.</span></span>
- <span data-ttu-id="01b1c-138">_`ValueTask<(bool, T)> MoveNextAsync();`_ : ce n’est pas seulement plus difficile à consommer, mais cela signifie que les `T` ne peuvent plus être covariants.</span><span class="sxs-lookup"><span data-stu-id="01b1c-138">_`ValueTask<(bool, T)> MoveNextAsync();`_: It's not only harder to consume, but it means that `T` can no longer be covariant.</span></span>
- <span data-ttu-id="01b1c-139">_`ValueTask<T?> TryMoveNextAsync();`_ : non covariant.</span><span class="sxs-lookup"><span data-stu-id="01b1c-139">_`ValueTask<T?> TryMoveNextAsync();`_: Not covariant.</span></span>
- <span data-ttu-id="01b1c-140">_`Task<T?> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.</span><span class="sxs-lookup"><span data-stu-id="01b1c-140">_`Task<T?> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="01b1c-141">_`ITask<T?> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.</span><span class="sxs-lookup"><span data-stu-id="01b1c-141">_`ITask<T?> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="01b1c-142">_`ITask<(bool,T)> TryMoveNextAsync();`_ : n’est pas covariant, prolocations à chaque appel, etc.</span><span class="sxs-lookup"><span data-stu-id="01b1c-142">_`ITask<(bool,T)> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="01b1c-143">_`Task<bool> TryMoveNextAsync(out T result);`_ : le résultat du `out` doit être défini lorsque l’opération retourne de manière synchrone, et non pas lorsqu’il termine de façon asynchrone la tâche potentiellement dans le futur. à ce stade, il n’y a aucun moyen de communiquer le résultat.</span><span class="sxs-lookup"><span data-stu-id="01b1c-143">_`Task<bool> TryMoveNextAsync(out T result);`_: The `out` result would need to be set when the operation returns synchronously, not when it asynchronously completes the task potentially sometime long in the future, at which point there'd be no way to communicate the result.</span></span>
- <span data-ttu-id="01b1c-144">_`IAsyncEnumerator<T>` n’implémentez pas `IAsyncDisposable`_ : nous pouvons choisir de les séparer.</span><span class="sxs-lookup"><span data-stu-id="01b1c-144">_`IAsyncEnumerator<T>` not implementing `IAsyncDisposable`_: We could choose to separate these.</span></span>  <span data-ttu-id="01b1c-145">Toutefois, cela complique certaines autres parties de la proposition, car le code doit être en mesure de faire face à la possibilité qu’un énumérateur ne fournisse pas de suppression, ce qui complique l’écriture des applications auxiliaires basées sur les modèles.</span><span class="sxs-lookup"><span data-stu-id="01b1c-145">However, doing so complicates certain other areas of the proposal, as code must then be able to deal with the possibility that an enumerator doesn't provide disposal, which makes it difficult to write pattern-based helpers.</span></span>  <span data-ttu-id="01b1c-146">En outre, il est courant que les énumérateurs aient besoin d’être éliminés (par exemple C# , tout itérateur Async qui a un bloc finally, la plupart des éléments qui énumèrent les données d’une connexion réseau, etc.), et si ce n’est pas le cas, il est simple d’implémenter la méthode simplement comme `public ValueTask DisposeAsync() => default(ValueTask);` avec une surcharge supplémentaire minimale.</span><span class="sxs-lookup"><span data-stu-id="01b1c-146">Further, it will be common for enumerators to have a need for disposal (e.g. any C# async iterator that has a finally block, most things enumerating data from a network connection, etc.), and if one doesn't, it is simple to implement the method purely as `public ValueTask DisposeAsync() => default(ValueTask);` with minimal additional overhead.</span></span>
- <span data-ttu-id="01b1c-147">_ `IAsyncEnumerator<T> GetAsyncEnumerator()`: aucun paramètre de jeton d’annulation.</span><span class="sxs-lookup"><span data-stu-id="01b1c-147">_ `IAsyncEnumerator<T> GetAsyncEnumerator()`: No cancellation token parameter.</span></span>

#### <a name="viable-alternative"></a><span data-ttu-id="01b1c-148">Alternative viable :</span><span class="sxs-lookup"><span data-stu-id="01b1c-148">Viable alternative:</span></span>

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

<span data-ttu-id="01b1c-149">`TryGetNext` est utilisé dans une boucle interne pour consommer des éléments avec un seul appel d’interface, à condition qu’ils soient disponibles de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-149">`TryGetNext` is used in an inner loop to consume items with a single interface call as long as they're available synchronously.</span></span>  <span data-ttu-id="01b1c-150">Lorsque l’élément suivant ne peut pas être récupéré de façon synchrone, il retourne false, et chaque fois qu’il retourne false, un appelant doit appeler par la suite `WaitForNextAsync` pour attendre que l’élément suivant soit disponible ou pour déterminer qu’il ne sera jamais un autre élément.</span><span class="sxs-lookup"><span data-stu-id="01b1c-150">When the next item can't be retrieved synchronously, it returns false, and any time it returns false, a caller must subsequently invoke `WaitForNextAsync` to either wait for the next item to be available or to determine that there will never be another item.</span></span> <span data-ttu-id="01b1c-151">La consommation classique (sans fonctionnalités de langage supplémentaires) ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="01b1c-151">Typical consumption (without additional language features) would look like:</span></span>

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

<span data-ttu-id="01b1c-152">L’avantage est qu’il s’agit d’un double pli, d’un secondaire et d’un principal :</span><span class="sxs-lookup"><span data-stu-id="01b1c-152">The advantage of this is two-fold, one minor and one major:</span></span>
- <span data-ttu-id="01b1c-153">_Minor : permet à un énumérateur de prendre en charge plusieurs consommateurs_.</span><span class="sxs-lookup"><span data-stu-id="01b1c-153">_Minor: Allows for an enumerator to support multiple consumers_.</span></span> <span data-ttu-id="01b1c-154">Il peut exister des scénarios dans lesquels il est utile qu’un énumérateur prenne en charge plusieurs consommateurs simultanés.</span><span class="sxs-lookup"><span data-stu-id="01b1c-154">There may be scenarios where it's valuable for an enumerator to support multiple concurrent consumers.</span></span>  <span data-ttu-id="01b1c-155">Cela ne peut pas être atteint quand `MoveNextAsync` et `Current` sont séparés de telle sorte qu’une implémentation ne peut pas rendre leur utilisation atomique.</span><span class="sxs-lookup"><span data-stu-id="01b1c-155">That can't be achieved when `MoveNextAsync` and `Current` are separate such that an implementation can't make their usage atomic.</span></span>  <span data-ttu-id="01b1c-156">En revanche, cette approche fournit une seule méthode `TryGetNext` qui prend en charge le push de l’énumérateur vers l’avant et l’obtention de l’élément suivant, de sorte que l’énumérateur peut activer l’atomicité si vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="01b1c-156">In contrast, this approach provides a single method `TryGetNext` that supports pushing the enumerator forward and getting the next item, so the enumerator can enable atomicity if desired.</span></span>  <span data-ttu-id="01b1c-157">Toutefois, il est probable que ces scénarios peuvent également être activés en donnant à chaque consommateur son propre énumérateur à partir d’un énumérable partagé.</span><span class="sxs-lookup"><span data-stu-id="01b1c-157">However, it's likely that such scenarios could also be enabled by giving each consumer its own enumerator from a shared enumerable.</span></span>  <span data-ttu-id="01b1c-158">En outre, nous ne souhaitons pas imposer que chaque énumérateur prenne en charge l’utilisation simultanée, car cela ajouterait des surcharges non triviales au cas majoritaire qui n’en a pas besoin, ce qui signifie qu’un consommateur de l’interface ne pouvait généralement pas s’appuyer sur cette méthode.</span><span class="sxs-lookup"><span data-stu-id="01b1c-158">Further, we don't want to enforce that every enumerator support concurrent usage, as that would add non-trivial overheads to the majority case that doesn't require it, which means a consumer of the interface generally couldn't rely on this any way.</span></span>
- <span data-ttu-id="01b1c-159">_Majeur : performances_.</span><span class="sxs-lookup"><span data-stu-id="01b1c-159">_Major: Performance_.</span></span> <span data-ttu-id="01b1c-160">L’approche de l' `MoveNextAsync`/`Current` requiert deux appels d’interface par opération, tandis que le meilleur cas pour `WaitForNextAsync`/`TryGetNext` est que la plupart des itérations se terminent de manière synchrone, ce qui permet une boucle interne étroite avec `TryGetNext`, de sorte que nous n’ayons qu’un seul appel d’interface par opération.</span><span class="sxs-lookup"><span data-stu-id="01b1c-160">The `MoveNextAsync`/`Current` approach requires two interface calls per operation, whereas the best case for `WaitForNextAsync`/`TryGetNext` is that most iterations complete synchronously, enabling a tight inner loop with `TryGetNext`, such that we only have one interface call per operation.</span></span>  <span data-ttu-id="01b1c-161">Cela peut avoir un impact mesurable dans les situations où les appels d’interface dominent le calcul.</span><span class="sxs-lookup"><span data-stu-id="01b1c-161">This can have a measurable impact in situations where the interface calls dominate the computation.</span></span>

<span data-ttu-id="01b1c-162">Toutefois, il existe des inconvénients non négligeables, y compris une complexité considérablement accrue lors de l’utilisation manuelle de ces derniers, ainsi qu’une meilleure chance d’introduire des bogues lors de leur utilisation.</span><span class="sxs-lookup"><span data-stu-id="01b1c-162">However, there are non-trivial downsides, including significantly increased complexity when consuming these manually, and an increased chance of introducing bugs when using them.</span></span>  <span data-ttu-id="01b1c-163">Et bien que les avantages en matière de performances s’affichent dans les microtesteurs, nous ne pensons pas qu’ils seront impactés dans la grande majorité des utilisations réelles.</span><span class="sxs-lookup"><span data-stu-id="01b1c-163">And while the performance benefits show up in microbenchmarks, we don't believe they'll be impactful in the vast majority of real usage.</span></span>  <span data-ttu-id="01b1c-164">S’il s’avère que c’est le cas, nous pouvons introduire un deuxième ensemble d’interfaces de manière claire.</span><span class="sxs-lookup"><span data-stu-id="01b1c-164">If it turns out they are, we can introduce a second set of interfaces in a light-up fashion.</span></span>

<span data-ttu-id="01b1c-165">Options ignorées prises en compte :</span><span class="sxs-lookup"><span data-stu-id="01b1c-165">Discarded options considered:</span></span>
- <span data-ttu-id="01b1c-166">`ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: les paramètres `out` ne peuvent pas être covariants.</span><span class="sxs-lookup"><span data-stu-id="01b1c-166">`ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: `out` parameters can't be covariant.</span></span>  <span data-ttu-id="01b1c-167">Il y a également un faible impact (un problème avec le modèle try en général) qui entraîne probablement une barrière d’écriture d’exécution pour les résultats de type référence.</span><span class="sxs-lookup"><span data-stu-id="01b1c-167">There's also a small impact here (an issue with the try pattern in general) that this likely incurs a runtime write barrier for reference type results.</span></span>

#### <a name="cancellation"></a><span data-ttu-id="01b1c-168">Annulation</span><span class="sxs-lookup"><span data-stu-id="01b1c-168">Cancellation</span></span>

<span data-ttu-id="01b1c-169">Il existe plusieurs approches possibles pour la prise en charge de l’annulation :</span><span class="sxs-lookup"><span data-stu-id="01b1c-169">There are several possible approaches to supporting cancellation:</span></span>
1. <span data-ttu-id="01b1c-170">`IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` sont des annulations indépendantes : `CancellationToken` n’apparaît nulle part.</span><span class="sxs-lookup"><span data-stu-id="01b1c-170">`IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` are cancellation-agnostic: `CancellationToken` doesn't appear anywhere.</span></span>  <span data-ttu-id="01b1c-171">L’annulation est obtenue en regroupant logiquement les `CancellationToken` dans l’énumérateur et/ou énumérable de quelque manière que ce soit, par exemple, lors de l’appel d’un itérateur, en passant le `CancellationToken` en tant qu’argument à la méthode d’itérateur et en l’utilisant dans le corps de l’itérateur, comme c’est le cas avec tout autre paramètre.</span><span class="sxs-lookup"><span data-stu-id="01b1c-171">Cancellation is achieved by logically baking the `CancellationToken` into the enumerable and/or enumerator in whatever manner is appropriate, e.g. when calling an iterator, passing the `CancellationToken` as an argument to the iterator method and using it in the body of the iterator, as is done with any other parameter.</span></span>
2. <span data-ttu-id="01b1c-172">`IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: vous transmettez un `CancellationToken` à `GetAsyncEnumerator`, et les opérations de `MoveNextAsync` suivantes le respectent toutefois.</span><span class="sxs-lookup"><span data-stu-id="01b1c-172">`IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: You pass a `CancellationToken` to `GetAsyncEnumerator`, and subsequent `MoveNextAsync` operations respect it however it can.</span></span>
3. <span data-ttu-id="01b1c-173">`IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: vous transmettez un `CancellationToken` à chaque appel de `MoveNextAsync` individuel.</span><span class="sxs-lookup"><span data-stu-id="01b1c-173">`IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: You pass a `CancellationToken` to each individual `MoveNextAsync` call.</span></span>
4. <span data-ttu-id="01b1c-174">1 & & 2 : incorporez `CancellationToken`s dans votre énumérable/énumérateur et transmettez `CancellationToken`s dans `GetAsyncEnumerator`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-174">1 && 2: You both embed `CancellationToken`s into your enumerable/enumerator and pass `CancellationToken`s into `GetAsyncEnumerator`.</span></span>
5. <span data-ttu-id="01b1c-175">1 & & 3 : incorporez `CancellationToken`s dans votre énumérable/énumérateur et transmettez `CancellationToken`s dans `MoveNextAsync`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-175">1 && 3: You both embed `CancellationToken`s into your enumerable/enumerator and pass `CancellationToken`s into `MoveNextAsync`.</span></span>

<span data-ttu-id="01b1c-176">D’un point de vue purement théorique, (5) est le plus robuste, dans ce (a) `MoveNextAsync` l’acceptation d’un `CancellationToken` permet le contrôle le plus affiné sur ce qui est annulé, et (b) `CancellationToken` est simplement un autre type qui peut être passé comme argument à des itérateurs, incorporés dans des types arbitraires, etc.</span><span class="sxs-lookup"><span data-stu-id="01b1c-176">From a purely theoretical perspective, (5) is the most robust, in that (a) `MoveNextAsync` accepting a `CancellationToken` enables the most fine-grained control over what's canceled, and (b) `CancellationToken` is just any other type that can passed as an argument into iterators, embedded in arbitrary types, etc.</span></span>

<span data-ttu-id="01b1c-177">Toutefois, il existe plusieurs problèmes avec cette approche :</span><span class="sxs-lookup"><span data-stu-id="01b1c-177">However, there are multiple problems with that approach:</span></span>
- <span data-ttu-id="01b1c-178">Comment un `CancellationToken` passé à `GetAsyncEnumerator` le faire dans le corps de l’itérateur ?</span><span class="sxs-lookup"><span data-stu-id="01b1c-178">How does a `CancellationToken` passed to `GetAsyncEnumerator` make it into the body of the iterator?</span></span>  <span data-ttu-id="01b1c-179">Nous pouvons exposer un nouveau mot clé de `iterator` que vous pourriez déconnecter pour accéder aux `CancellationToken` passés à `GetEnumerator`, mais un) qui est un grand nombre de machines supplémentaires, b), c’est un citoyen de premier ordre et c) le cas 99% semble être le même code appelant un itérateur et appeler `GetAsyncEnumerator` dessus, auquel cas il peut simplement passer le `CancellationToken` en tant qu’argument dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="01b1c-179">We could expose a new `iterator` keyword that you could dot off of to get access to the `CancellationToken` passed to `GetEnumerator`, but a) that's a lot of additional machinery, b) we're making it a very first-class citizen, and c) the 99% case would seem to be the same code both calling an iterator and calling `GetAsyncEnumerator` on it, in which case it can just pass the `CancellationToken` as an argument into the method.</span></span>
- <span data-ttu-id="01b1c-180">Comment un `CancellationToken` passé à `MoveNextAsync` peut-il se trouver dans le corps de la méthode ?</span><span class="sxs-lookup"><span data-stu-id="01b1c-180">How does a `CancellationToken` passed to `MoveNextAsync` get into the body of the method?</span></span>  <span data-ttu-id="01b1c-181">C’est encore pire, comme s’il s’agissait d’un objet local `iterator`, sa valeur peut changer dans await, ce qui signifie que tout code inscrit avec le jeton doit annuler son inscription avant d’attendre, puis se réinscrire après. Il est également potentiellement coûteux de procéder à l’inscription et à la désinscription dans chaque appel `MoveNextAsync`, qu’il soit implémenté par le compilateur dans un itérateur ou par un développeur manuellement.</span><span class="sxs-lookup"><span data-stu-id="01b1c-181">This is even worse, as if it's exposed off of an `iterator` local object, its value could change across awaits, which means any code that registered with the token would need to unregister from it prior to awaits and then re-register after; it's also potentially quite expensive to need to do such registering and unregistering in every `MoveNextAsync` call, regardless of whether implemented by the compiler in an iterator or by a developer manually.</span></span>
- <span data-ttu-id="01b1c-182">Comment un développeur annule-t-il une boucle de `foreach` ?</span><span class="sxs-lookup"><span data-stu-id="01b1c-182">How does a developer cancel a `foreach` loop?</span></span>  <span data-ttu-id="01b1c-183">Si c’est le cas en attribuant un `CancellationToken` à un énumérable/énumérateur, puis à un), nous devons prendre en charge `foreach`les énumérateurs, ce qui leur permet d’être des citoyens de première classe. vous devez maintenant commencer à réfléchir à un écosystème développé autour d’énumérateurs (par exemple, des méthodes LINQ) ou b). nous devons tout de même incorporer les `CancellationToken` dans l’énumérable, de la même façon en utilisant une méthode d’extension de `WithCancellation` de `IAsyncEnumerable<T>` qui stockerait le jeton fourni, puis le passera dans le `GetAsyncEnumerator` `GetAsyncEnumerator` est appelé (en ignorant ce jeton).</span><span class="sxs-lookup"><span data-stu-id="01b1c-183">If it's done by giving a `CancellationToken` to an enumerable/enumerator, then either a) we need to support `foreach`'ing over enumerators, which raises them to being first-class citizens, and now you need to start thinking about an ecosystem built up around enumerators (e.g. LINQ methods) or b) we need to embed the `CancellationToken` in the enumerable anyway by having some `WithCancellation` extension method off of `IAsyncEnumerable<T>` that would store the provided token and then pass it into  the wrapped enumerable's `GetAsyncEnumerator` when the `GetAsyncEnumerator` on the returned struct is invoked (ignoring that token).</span></span>  <span data-ttu-id="01b1c-184">Ou bien, vous pouvez simplement utiliser le `CancellationToken` que vous avez dans le corps de l’instruction foreach.</span><span class="sxs-lookup"><span data-stu-id="01b1c-184">Or, you can just use the `CancellationToken` you have in the body of the foreach.</span></span>
- <span data-ttu-id="01b1c-185">Si/lorsque les interprétations des requêtes sont prises en charge, comment les `CancellationToken` fournies pour `GetEnumerator` ou `MoveNextAsync` être passées à chaque clause ?</span><span class="sxs-lookup"><span data-stu-id="01b1c-185">If/when query comprehensions are supported, how would the `CancellationToken` supplied to `GetEnumerator` or `MoveNextAsync` be passed into each clause?</span></span>  <span data-ttu-id="01b1c-186">La méthode la plus simple consisterait simplement à la capturer dans la clause, ce qui fait que le jeton est passé à `GetAsyncEnumerator`/`MoveNextAsync` est ignorée.</span><span class="sxs-lookup"><span data-stu-id="01b1c-186">The easiest way would simply be for the clause to capture it, at which point whatever token is passed to `GetAsyncEnumerator`/`MoveNextAsync` is ignored.</span></span>

<span data-ttu-id="01b1c-187">Une version antérieure de ce document est recommandée (1), mais nous avons passé à (4).</span><span class="sxs-lookup"><span data-stu-id="01b1c-187">An earlier version of this document recommended (1), but we since switched to (4).</span></span>

<span data-ttu-id="01b1c-188">Les deux principaux problèmes avec (1) :</span><span class="sxs-lookup"><span data-stu-id="01b1c-188">The two main problems with (1):</span></span>
- <span data-ttu-id="01b1c-189">les producteurs d’énumérables annulables doivent implémenter une certaine utilisation et peuvent uniquement tirer parti de la prise en charge par le compilateur des itérateurs Async pour implémenter une méthode `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-189">producers of cancellable enumerables have to implement some boilerplate, and can only leverage the compiler's support for async-iterators to implement a `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)` method.</span></span>
- <span data-ttu-id="01b1c-190">Il est probable que de nombreux producteurs soient tentés d’ajouter simplement un paramètre `CancellationToken` à leur signature Async-Enumerable à la place, ce qui empêchera les consommateurs de transmettre le jeton d’annulation souhaité lorsqu’ils reçoivent un type `IAsyncEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-190">it is likely that many producers would be tempted to just add a `CancellationToken` parameter to their async-enumerable signature instead, which will prevent consumers from passing the cancellation token they want when they are given an `IAsyncEnumerable` type.</span></span>

<span data-ttu-id="01b1c-191">Il existe deux scénarios de consommation principaux :</span><span class="sxs-lookup"><span data-stu-id="01b1c-191">There are two main consumption scenarios:</span></span>
1. <span data-ttu-id="01b1c-192">`await foreach (var i in GetData(token)) ...` où le consommateur appelle la méthode Async-Iterator,</span><span class="sxs-lookup"><span data-stu-id="01b1c-192">`await foreach (var i in GetData(token)) ...` where the consumer calls the async-iterator method,</span></span>
2. <span data-ttu-id="01b1c-193">`await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` où le consommateur traite une instance `IAsyncEnumerable` donnée.</span><span class="sxs-lookup"><span data-stu-id="01b1c-193">`await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` where the consumer deals with a given `IAsyncEnumerable` instance.</span></span>

<span data-ttu-id="01b1c-194">Nous constatons qu’une compromission raisonnable de la prise en charge des deux scénarios de manière pratique pour les producteurs et les consommateurs de Async-Streams consiste à utiliser un paramètre spécialement annoté dans la méthode Async-Iterator.</span><span class="sxs-lookup"><span data-stu-id="01b1c-194">We find that a reasonable compromise to support both scenarios in a way that is convenient for both producers and consumers of async-streams is to use a specially annotated parameter in the async-iterator method.</span></span> <span data-ttu-id="01b1c-195">L’attribut `[EnumeratorCancellation]` est utilisé à cet effet.</span><span class="sxs-lookup"><span data-stu-id="01b1c-195">The `[EnumeratorCancellation]` attribute is used for this purpose.</span></span> <span data-ttu-id="01b1c-196">Le fait de placer cet attribut sur un paramètre indique au compilateur que, si un jeton est passé à la méthode `GetAsyncEnumerator`, ce jeton doit être utilisé à la place de la valeur transmise à l’origine pour le paramètre.</span><span class="sxs-lookup"><span data-stu-id="01b1c-196">Placing this attribute on a parameter tells the compiler that if a token is passed to the `GetAsyncEnumerator` method, that token should be used instead of the value originally passed for the parameter.</span></span>

<span data-ttu-id="01b1c-197">Prenez le cas de `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-197">Consider `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`.</span></span> <span data-ttu-id="01b1c-198">L’implémenteur de cette méthode peut simplement utiliser le paramètre dans le corps de la méthode.</span><span class="sxs-lookup"><span data-stu-id="01b1c-198">The implementer of this method can simply use the parameter in the method body.</span></span> <span data-ttu-id="01b1c-199">Le consommateur peut utiliser les modèles de consommation ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="01b1c-199">The consumer can use either consumption patterns above:</span></span>
1. <span data-ttu-id="01b1c-200">Si vous utilisez `GetData(token)`, le jeton est enregistré dans Async-Enumerable et sera utilisé dans l’itération.</span><span class="sxs-lookup"><span data-stu-id="01b1c-200">if you use `GetData(token)`, then the token is saved into the async-enumerable and will be used in iteration,</span></span>
2. <span data-ttu-id="01b1c-201">Si vous utilisez `givenIAsyncEnumerable.WithCancellation(token)`, le jeton passé à `GetAsyncEnumerator` remplacera tout jeton enregistré dans Async-Enumerable.</span><span class="sxs-lookup"><span data-stu-id="01b1c-201">if you use `givenIAsyncEnumerable.WithCancellation(token)`, then the token passed to `GetAsyncEnumerator` will supersede any token saved in the async-enumerable.</span></span>

## <a name="foreach"></a><span data-ttu-id="01b1c-202">foreach</span><span class="sxs-lookup"><span data-stu-id="01b1c-202">foreach</span></span>

<span data-ttu-id="01b1c-203">les `foreach` seront augmentées pour prendre en charge les `IAsyncEnumerable<T>` en plus de la prise en charge existante de `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-203">`foreach` will be augmented to support `IAsyncEnumerable<T>` in addition to its existing support for `IEnumerable<T>`.</span></span>  <span data-ttu-id="01b1c-204">Et il prend en charge l’équivalent de `IAsyncEnumerable<T>` en tant que modèle si les membres pertinents sont exposés publiquement, en revenant à l’utilisation directe de l’interface si ce n’est pas le cas, afin d’activer les extensions basées sur un struct qui évitent d’allouer et d’utiliser d’autres awaitables comme type de retour de `MoveNextAsync` et `DisposeAsync`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-204">And it will support the equivalent of `IAsyncEnumerable<T>` as a pattern if the relevant members are exposed publicly, falling back to using the interface directly if not, in order to enable struct-based extensions that avoid allocating as well as using alternative awaitables as the return type of `MoveNextAsync` and `DisposeAsync`.</span></span>

### <a name="syntax"></a><span data-ttu-id="01b1c-205">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="01b1c-205">Syntax</span></span>

<span data-ttu-id="01b1c-206">À l’aide de la syntaxe :</span><span class="sxs-lookup"><span data-stu-id="01b1c-206">Using the syntax:</span></span>

```csharp
foreach (var i in enumerable)
```

<span data-ttu-id="01b1c-207">C#continuera à traiter `enumerable` comme un énumérable synchrone, de sorte que même s’il expose les API pertinentes pour les énumérables Async (exposant le modèle ou implémentant l’interface), il prend uniquement en compte les API synchrones.</span><span class="sxs-lookup"><span data-stu-id="01b1c-207">C# will continue to treat `enumerable` as a synchronous enumerable, such that even if it exposes the relevant APIs for async enumerables (exposing the pattern or implementing the interface), it will only consider the synchronous APIs.</span></span>

<span data-ttu-id="01b1c-208">Pour forcer `foreach` à la place uniquement les API asynchrones, `await` est insérée comme suit :</span><span class="sxs-lookup"><span data-stu-id="01b1c-208">To force `foreach` to instead only consider the asynchronous APIs, `await` is inserted as follows:</span></span>

```csharp
await foreach (var i in enumerable)
```

<span data-ttu-id="01b1c-209">Aucune syntaxe n’est fournie pour prendre en charge l’utilisation des API Async ou Sync ; le développeur doit choisir en fonction de la syntaxe utilisée.</span><span class="sxs-lookup"><span data-stu-id="01b1c-209">No syntax would be provided that would support using either the async or the sync APIs; the developer must choose based on the syntax used.</span></span>

<span data-ttu-id="01b1c-210">Options ignorées prises en compte :</span><span class="sxs-lookup"><span data-stu-id="01b1c-210">Discarded options considered:</span></span>
- <span data-ttu-id="01b1c-211">_`foreach (var i in await enumerable)`_ : il s’agit déjà d’une syntaxe valide, et la modification de sa signification serait une modification avec rupture.</span><span class="sxs-lookup"><span data-stu-id="01b1c-211">_`foreach (var i in await enumerable)`_: This is already valid syntax, and changing its meaning would be a breaking change.</span></span>  <span data-ttu-id="01b1c-212">Cela signifie que pour `await` le `enumerable`, récupérez un résultat pouvant être iteré de façon synchrone à partir de celui-ci, puis itérez de manière synchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-212">This means to `await` the `enumerable`, get back something synchronously iterable from it, and then synchronously iterate through that.</span></span>
- <span data-ttu-id="01b1c-213">_`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_ : ils suggèrent que nous attendons l’élément suivant, mais il existe d’autres await impliqués dans foreach, en particulier si l’énumérable est une `IAsyncDisposable`, nous allons `await`de sa suppression asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01b1c-213">_`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_: These all suggest that we're awaiting the next item, but there are other awaits involved in foreach, in particular if the enumerable is an `IAsyncDisposable`, we will be `await`'ing its async disposal.</span></span>  <span data-ttu-id="01b1c-214">Cette expression await correspond à la portée de foreach plutôt qu’à chaque élément individuel. par conséquent, le mot clé `await` mérite d’être au niveau `foreach`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-214">That await is as the scope of the foreach rather than for each individual element, and thus the `await` keyword deserves to be at the `foreach` level.</span></span>  <span data-ttu-id="01b1c-215">En outre, le fait de l’associer au `foreach` nous donne un moyen de décrire le `foreach` avec un terme différent, par exemple « await foreach ».</span><span class="sxs-lookup"><span data-stu-id="01b1c-215">Further, having it associated with the `foreach` gives us a way to describe the `foreach` with a different term, e.g. a "await foreach".</span></span>  <span data-ttu-id="01b1c-216">Mais plus important encore, il est important de tenir compte de `foreach` syntaxe en même temps que `using` syntaxe, afin qu’ils restent cohérents les uns avec les autres, et `using (await ...)` une syntaxe déjà valide.</span><span class="sxs-lookup"><span data-stu-id="01b1c-216">But more importantly, there's value in considering `foreach` syntax at the same time as `using` syntax, so that they remain consistent with each other, and `using (await ...)` is already valid syntax.</span></span>
- _`foreach await (var i in enumerable)`_

<span data-ttu-id="01b1c-217">Toutefois, tenez compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="01b1c-217">Still to consider:</span></span>
- <span data-ttu-id="01b1c-218">`foreach` Today ne prend pas en charge l’itération au sein d’un énumérateur.</span><span class="sxs-lookup"><span data-stu-id="01b1c-218">`foreach` today does not support iterating through an enumerator.</span></span>  <span data-ttu-id="01b1c-219">Nous pensons qu’il est plus courant d’avoir des `IAsyncEnumerator<T>`s, et il est donc tentant de prendre en charge les `await foreach` avec `IAsyncEnumerable<T>` et `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-219">We expect it will be more common to have `IAsyncEnumerator<T>`s handed around, and thus it's tempting to support `await foreach` with both `IAsyncEnumerable<T>` and `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="01b1c-220">Toutefois, une fois que nous ajouterons une telle prise en charge, il est question de savoir si `IAsyncEnumerator<T>` est un citoyen de première classe, et si nous devons avoir des surcharges de combinateurs qui fonctionnent sur les énumérateurs en plus des énumérables.</span><span class="sxs-lookup"><span data-stu-id="01b1c-220">But once we add such support, it introduces the question of whether `IAsyncEnumerator<T>` is a first-class citizen, and whether we need to have overloads of combinators that operate on enumerators in addition to enumerables?</span></span>    <span data-ttu-id="01b1c-221">Souhaitez-vous encourager les méthodes pour retourner des énumérateurs plutôt que des énumérables ?</span><span class="sxs-lookup"><span data-stu-id="01b1c-221">Do we want to encourage methods to return enumerators rather than enumerables?</span></span> <span data-ttu-id="01b1c-222">Nous devons continuer à aborder ce sujet.</span><span class="sxs-lookup"><span data-stu-id="01b1c-222">We should continue to discuss this.</span></span>  <span data-ttu-id="01b1c-223">Si nous décidons de ne pas la prendre en charge, nous pourrions souhaiter introduire une méthode d’extension `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` qui permettrait à un énumérateur d’être toujours `foreach`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-223">If we decide we don't want to support it, we might want to introduce an extension method `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` that would allow an enumerator to still be `foreach`'d.</span></span>  <span data-ttu-id="01b1c-224">Si nous décidons de le prendre en charge, nous devrons également décider si la `await foreach` est responsable de l’appel de `DisposeAsync` sur l’énumérateur et que la réponse est probablement « non, le contrôle de la suppression doit être géré par la personne appelée `GetEnumerator`».</span><span class="sxs-lookup"><span data-stu-id="01b1c-224">If we decide we do want to support it, we'll need to also decide on whether the `await foreach` would be responsible for calling `DisposeAsync` on the enumerator, and the answer is likely "no, control over disposal should be handled by whoever called `GetEnumerator`."</span></span>

### <a name="pattern-based-compilation"></a><span data-ttu-id="01b1c-225">Compilation basée sur des modèles</span><span class="sxs-lookup"><span data-stu-id="01b1c-225">Pattern-based Compilation</span></span>

<span data-ttu-id="01b1c-226">Le compilateur crée une liaison avec les API basées sur un modèle s’ils existent, en préférant ceux qui utilisent l’interface (le modèle peut être satisfait avec des méthodes d’instance ou des méthodes d’extension).</span><span class="sxs-lookup"><span data-stu-id="01b1c-226">The compiler will bind to the pattern-based APIs if they exist, preferring those over using the interface (the pattern may be satisfied with instance methods or extension methods).</span></span>  <span data-ttu-id="01b1c-227">La configuration requise pour le modèle est la suivante :</span><span class="sxs-lookup"><span data-stu-id="01b1c-227">The requirements for the pattern are:</span></span>
- <span data-ttu-id="01b1c-228">L’énumérable doit exposer une méthode `GetAsyncEnumerator` qui peut être appelée sans argument et qui retourne un énumérateur qui répond au modèle approprié.</span><span class="sxs-lookup"><span data-stu-id="01b1c-228">The enumerable must expose a `GetAsyncEnumerator` method that may be called with no arguments and that returns an enumerator that meets the relevant pattern.</span></span>
- <span data-ttu-id="01b1c-229">L’énumérateur doit exposer une méthode `MoveNextAsync` qui peut être appelée sans argument et qui retourne une valeur qui peut être `await`Ed et dont `GetResult()` retourne un `bool`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-229">The enumerator must expose a `MoveNextAsync` method that may be called with no arguments and that returns something which may be `await`ed and whose `GetResult()` returns a `bool`.</span></span>
- <span data-ttu-id="01b1c-230">L’énumérateur doit également exposer `Current` propriété dont l’accesseur Get retourne un `T` représentant le type de données énumérées.</span><span class="sxs-lookup"><span data-stu-id="01b1c-230">The enumerator must also expose `Current` property whose getter returns a `T` representing the kind of data being enumerated.</span></span>
- <span data-ttu-id="01b1c-231">L’énumérateur peut éventuellement exposer une méthode `DisposeAsync` qui peut être appelée sans argument et qui retourne une valeur qui peut être `await`Ed et dont `GetResult()` retourne `void`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-231">The enumerator may optionally expose a `DisposeAsync` method that may be invoked with no arguments and that returns something that can be `await`ed and whose `GetResult()` returns `void`.</span></span>

<span data-ttu-id="01b1c-232">Ce code :</span><span class="sxs-lookup"><span data-stu-id="01b1c-232">This code:</span></span>

```csharp
var enumerable = ...;
await foreach (T item in enumerable)
{
   ...
}
```

<span data-ttu-id="01b1c-233">est traduit en l’équivalent de :</span><span class="sxs-lookup"><span data-stu-id="01b1c-233">is translated to the equivalent of:</span></span>

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

<span data-ttu-id="01b1c-234">Si le type itéré n’expose pas le modèle approprié, les interfaces sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="01b1c-234">If the iterated type doesn't expose the right pattern, the interfaces will be used.</span></span>

### <a name="configureawait"></a><span data-ttu-id="01b1c-235">ConfigureAwait</span><span class="sxs-lookup"><span data-stu-id="01b1c-235">ConfigureAwait</span></span>

<span data-ttu-id="01b1c-236">Cette compilation basée sur des modèles permettra l’utilisation de `ConfigureAwait` sur toutes les await, par le biais d’une méthode d’extension `ConfigureAwait` :</span><span class="sxs-lookup"><span data-stu-id="01b1c-236">This pattern-based compilation will allow `ConfigureAwait` to be used on all of the awaits, via a `ConfigureAwait` extension method:</span></span>

```csharp
await foreach (T item in enumerable.ConfigureAwait(false))
{
   ...
}
```

<span data-ttu-id="01b1c-237">Cela sera basé sur les types que nous ajouterons également à .NET, susceptibles de System. Threading. Tasks. extensions. dll :</span><span class="sxs-lookup"><span data-stu-id="01b1c-237">This will be based on types we'll add to .NET as well, likely to System.Threading.Tasks.Extensions.dll:</span></span>

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

<span data-ttu-id="01b1c-238">Notez que cette approche n’autorise pas l’utilisation de `ConfigureAwait` avec les énumérables basés sur des modèles. mais là encore, il est déjà possible que le `ConfigureAwait` ne soit exposé qu’en tant qu’extension sur `Task`/`Task<T>`/`ValueTask`/`ValueTask<T>` et ne puisse pas être appliquée à des choses arbitraires, car il n’est utile que pour les tâches (il contrôle un comportement implémenté dans la prise en charge de continuation de la tâche).</span><span class="sxs-lookup"><span data-stu-id="01b1c-238">Note that this approach will not enable `ConfigureAwait` to be used with pattern-based enumerables, but then again it's already the case that the `ConfigureAwait` is only exposed as an extension on `Task`/`Task<T>`/`ValueTask`/`ValueTask<T>` and can't be applied to arbitrary awaitable things, as it only makes sense when applied to Tasks (it controls a behavior implemented in Task's continuation support), and thus doesn't make sense when using a pattern where the awaitable things may not be tasks.</span></span>  <span data-ttu-id="01b1c-239">Toute personne qui retourne des éléments await peut fournir son propre comportement personnalisé dans de tels scénarios avancés.</span><span class="sxs-lookup"><span data-stu-id="01b1c-239">Anyone returning awaitable things can provide their own custom behavior in such advanced scenarios.</span></span>

<span data-ttu-id="01b1c-240">(Si nous pouvons trouver un moyen de prendre en charge une solution de `ConfigureAwait` au niveau de l’étendue ou de l’assembly, cela n’est pas nécessaire.)</span><span class="sxs-lookup"><span data-stu-id="01b1c-240">(If we can come up with some way to support a scope- or assembly-level `ConfigureAwait` solution, then this won't be necessary.)</span></span>

## <a name="async-iterators"></a><span data-ttu-id="01b1c-241">Itérateurs Async</span><span class="sxs-lookup"><span data-stu-id="01b1c-241">Async Iterators</span></span>

<span data-ttu-id="01b1c-242">Le langage/compilateur prendra en charge la production `IAsyncEnumerable<T>`s et `IAsyncEnumerator<T>`s en plus de les consommer.</span><span class="sxs-lookup"><span data-stu-id="01b1c-242">The language / compiler will support producing `IAsyncEnumerable<T>`s and `IAsyncEnumerator<T>`s in addition to consuming them.</span></span> <span data-ttu-id="01b1c-243">Aujourd’hui, le langage prend en charge l’écriture d’un itérateur comme :</span><span class="sxs-lookup"><span data-stu-id="01b1c-243">Today the language supports writing an iterator like:</span></span>

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

<span data-ttu-id="01b1c-244">mais `await` ne peuvent pas être utilisés dans le corps de ces itérateurs.</span><span class="sxs-lookup"><span data-stu-id="01b1c-244">but `await` can't be used in the body of these iterators.</span></span>  <span data-ttu-id="01b1c-245">Nous allons ajouter cette prise en charge.</span><span class="sxs-lookup"><span data-stu-id="01b1c-245">We will add that support.</span></span>

### <a name="syntax"></a><span data-ttu-id="01b1c-246">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="01b1c-246">Syntax</span></span>

<span data-ttu-id="01b1c-247">La prise en charge du langage existant pour les itérateurs déduit la nature de l’itérateur de la méthode selon qu’elle contient des `yield`s.</span><span class="sxs-lookup"><span data-stu-id="01b1c-247">The existing language support for iterators infers the iterator nature of the method based on whether it contains any `yield`s.</span></span>  <span data-ttu-id="01b1c-248">Il en va de même pour les itérateurs Async.</span><span class="sxs-lookup"><span data-stu-id="01b1c-248">The same will be true for async iterators.</span></span>  <span data-ttu-id="01b1c-249">Ces itérateurs Async seront dédélimités et différenciés des itérateurs synchrones via l’ajout d' `async` à la signature, et doivent alors également avoir `IAsyncEnumerable<T>` ou `IAsyncEnumerator<T>` comme type de retour.</span><span class="sxs-lookup"><span data-stu-id="01b1c-249">Such async iterators will be demarcated and differentiated from synchronous iterators via adding `async` to the signature, and must then also have either `IAsyncEnumerable<T>` or `IAsyncEnumerator<T>` as its return type.</span></span>  <span data-ttu-id="01b1c-250">Par exemple, l’exemple ci-dessus peut être écrit comme un itérateur Async comme suit :</span><span class="sxs-lookup"><span data-stu-id="01b1c-250">For example, the above example could be written as an async iterator as follows:</span></span>

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

<span data-ttu-id="01b1c-251">Alternatives prises en compte :</span><span class="sxs-lookup"><span data-stu-id="01b1c-251">Alternatives considered:</span></span>
- <span data-ttu-id="01b1c-252">_N’utilisez pas `async` dans la signature_: l’utilisation de `async` est probablement techniquement requise par le compilateur, car il l’utilise pour déterminer si `await` est valide dans ce contexte.</span><span class="sxs-lookup"><span data-stu-id="01b1c-252">_Not using `async` in the signature_: Using `async` is likely technically required by the compiler, as it uses it to determine whether `await` is valid in that context.</span></span>  <span data-ttu-id="01b1c-253">Mais même si ce n’est pas obligatoire, nous avons établi que `await` ne peut être utilisé que dans les méthodes marquées comme `async`, et il semble important de conserver la cohérence.</span><span class="sxs-lookup"><span data-stu-id="01b1c-253">But even if it's not required, we've established that `await` may only be used in methods marked as `async`, and it seems important to keep the consistency.</span></span>
- <span data-ttu-id="01b1c-254">L' _activation de générateurs personnalisés pour `IAsyncEnumerable<T>`_ : ce que nous pourrions examiner pour le futur, mais la machine est compliquée et nous ne prenons pas en charge cette opération pour les homologues synchrones.</span><span class="sxs-lookup"><span data-stu-id="01b1c-254">_Enabling custom builders for `IAsyncEnumerable<T>`_:  That's something we could look at for the future, but the machinery is complicated and we don't support that for the synchronous counterparts.</span></span>
- <span data-ttu-id="01b1c-255">_Présence d’un mot clé `iterator` dans la signature : les_itérateurs Async utilisent `async iterator` dans la signature, et `yield` ne peuvent être utilisés que dans les méthodes `async` incluses `iterator`; `iterator` est ensuite rendu facultatif sur les itérateurs synchrones.</span><span class="sxs-lookup"><span data-stu-id="01b1c-255">_Having an `iterator` keyword in the signature_: Async iterators would use `async iterator` in the signature, and `yield` could only be used in `async` methods that included `iterator`; `iterator` would then be made optional on synchronous iterators.</span></span>  <span data-ttu-id="01b1c-256">En fonction de votre perspective, cela a l’avantage d’être très clair en ce qui concerne la signature de la méthode, qu' `yield` soit autorisé et que la méthode soit en fait supposée retourner des instances de type `IAsyncEnumerable<T>` plutôt que le compilateur en fabrication, selon que le code utilise `yield` ou non.</span><span class="sxs-lookup"><span data-stu-id="01b1c-256">Depending on your perspective, this has the benefit of making it very clear by the signature of the method whether `yield` is allowed and whether the method is actually meant to return instances of type `IAsyncEnumerable<T>` rather than the compiler manufacturing one based on whether the code uses `yield` or not.</span></span>  <span data-ttu-id="01b1c-257">Mais il est différent des itérateurs synchrones, ce qui n’est pas et ne peut pas être fait pour en exiger un.</span><span class="sxs-lookup"><span data-stu-id="01b1c-257">But it is different from synchronous iterators, which don't and can't be made to require one.</span></span>  <span data-ttu-id="01b1c-258">En outre, certains développeurs n’aiment pas la syntaxe supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="01b1c-258">Plus some developers don't like the extra syntax.</span></span>  <span data-ttu-id="01b1c-259">Si nous étions en train de le concevoir à partir de zéro, nous aurions probablement effectué cette opération, mais à ce stade, il y a bien plus de valeur pour maintenir les itérateurs Async près des itérateurs de synchronisation.</span><span class="sxs-lookup"><span data-stu-id="01b1c-259">If we were designing it from scratch, we'd probably make this required, but at this point there's much more value in keeping async iterators close to sync iterators.</span></span>

## <a name="linq"></a><span data-ttu-id="01b1c-260">LINQ</span><span class="sxs-lookup"><span data-stu-id="01b1c-260">LINQ</span></span>

<span data-ttu-id="01b1c-261">Il y a plus de ~ 200 surcharges de méthodes sur la classe `System.Linq.Enumerable`, qui fonctionnent toutes en termes de `IEnumerable<T>`; certaines d’entre elles acceptent des `IEnumerable<T>`, certaines d’entre elles produisent des `IEnumerable<T>`, et beaucoup font les deux.</span><span class="sxs-lookup"><span data-stu-id="01b1c-261">There are over ~200 overloads of methods on the `System.Linq.Enumerable` class, all of which work in terms of `IEnumerable<T>`; some of these accept `IEnumerable<T>`, some of them produce `IEnumerable<T>`, and many do both.</span></span>  <span data-ttu-id="01b1c-262">L’ajout de la prise en charge de LINQ pour `IAsyncEnumerable<T>` entraînerait probablement la duplication de toutes ces surcharges pour celle-ci, pour une autre ~ 200.</span><span class="sxs-lookup"><span data-stu-id="01b1c-262">Adding LINQ support for `IAsyncEnumerable<T>` would likely entail duplicating all of these overloads for it, for another ~200.</span></span>  <span data-ttu-id="01b1c-263">Et étant donné que `IAsyncEnumerator<T>` est susceptible d’être plus courant en tant qu’entité autonome dans le monde asynchrone que `IEnumerator<T>` se trouve dans le monde synchrone, nous pourrions potentiellement avoir besoin d’une autre surcharges ~ 200 qui fonctionnent avec `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-263">And since `IAsyncEnumerator<T>` is likely to be more common as a standalone entity in the asynchronous world than `IEnumerator<T>` is in the synchronous world, we could potentially need another ~200 overloads that work with `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="01b1c-264">En outre, un grand nombre de surcharges traitent les prédicats (par exemple, `Where` qui prend un `Func<T, bool>`) et il peut être souhaitable d’avoir des surcharges de `IAsyncEnumerable<T>`qui traitent à la fois des prédicats synchrones et asynchrones (par exemple, `Func<T, ValueTask<bool>>` en plus de `Func<T, bool>`).</span><span class="sxs-lookup"><span data-stu-id="01b1c-264">Plus, a large number of the overloads deal with predicates (e.g. `Where` that takes a `Func<T, bool>`), and it may be desirable to have `IAsyncEnumerable<T>`-based overloads that deal with both synchronous and asynchronous predicates (e.g. `Func<T, ValueTask<bool>>` in addition to `Func<T, bool>`).</span></span>  <span data-ttu-id="01b1c-265">Bien que cela ne s’applique pas à toutes les nouvelles surcharges de plus de 400, un calcul approximatif est qu’elle s’applique à la moitié, ce qui signifie une autre ~ 200 surcharges, pour un total de ~ 600 nouvelles méthodes.</span><span class="sxs-lookup"><span data-stu-id="01b1c-265">While this isn't applicable to all of the now ~400 new overloads, a rough calculation is that it'd be applicable to half, which means another ~200 overloads, for a total of ~600 new methods.</span></span>

<span data-ttu-id="01b1c-266">Il s’agit d’un nombre d’API en quinconce, avec un potentiel encore plus possible lorsque des bibliothèques d’extensions telles que les extensions interactives (IX) sont prises en compte.</span><span class="sxs-lookup"><span data-stu-id="01b1c-266">That is a staggering number of APIs, with the potential for even more when extension libraries like Interactive Extensions (Ix) are considered.</span></span>  <span data-ttu-id="01b1c-267">Mais IX a déjà une implémentation de beaucoup d’entre elles, et il n’existe pas de bonne raison de dupliquer ce travail. Nous devons à la place aider la communauté à améliorer IX et la recommander quand les développeurs souhaitent utiliser LINQ avec `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-267">But Ix already has an implementation of many of these, and there doesn't seem to be a great reason to duplicate that work; we should instead help the community improve Ix and recommend it for when developers want to use LINQ with `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="01b1c-268">Il y a également le problème de syntaxe d’interprétation des requêtes.</span><span class="sxs-lookup"><span data-stu-id="01b1c-268">There is also the issue of query comprehension syntax.</span></span>  <span data-ttu-id="01b1c-269">La nature basée sur les modèles des interprétations des requêtes leur permettrait de « travailler » avec certains opérateurs, par exemple, si IX fournit les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="01b1c-269">The pattern-based nature of query comprehensions would allow them to "just work" with some operators, e.g. if Ix provides the following methods:</span></span>

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, TResult> func);
public static IAsyncEnumerable<T> Where(this IAsyncEnumerable<T> source, Func<T, bool> func);
```

<span data-ttu-id="01b1c-270">ce C# code fonctionnera alors « tout simplement » :</span><span class="sxs-lookup"><span data-stu-id="01b1c-270">then this C# code will "just work":</span></span>

```csharp
IAsyncEnumerable<int> enumerable = ...;
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select item * 2;
```

<span data-ttu-id="01b1c-271">Toutefois, il n’existe aucune syntaxe d’interprétation des requêtes qui prend en charge l’utilisation de `await` dans les clauses, donc si IX a été ajouté, par exemple :</span><span class="sxs-lookup"><span data-stu-id="01b1c-271">However, there is no query comprehension syntax that supports using `await` in the clauses, so if Ix added, for example:</span></span>

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, ValueTask<TResult>> func);
```

<span data-ttu-id="01b1c-272">Cela ne fonctionnerait alors que :</span><span class="sxs-lookup"><span data-stu-id="01b1c-272">then this would "just work":</span></span>

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

<span data-ttu-id="01b1c-273">mais il n’y avait aucun moyen de l’écrire avec le `await` inline dans la clause `select`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-273">but there'd be no way to write it with the `await` inline in the `select` clause.</span></span>  <span data-ttu-id="01b1c-274">Dans le cadre d’un travail distinct, nous pourrions envisager d’ajouter des expressions `async { ... }`es dans le langage. à ce stade, nous pourrions les autoriser à être utilisés dans les interprétations des requêtes, et il serait préférable d’écrire le contenu ci-dessus comme suit :</span><span class="sxs-lookup"><span data-stu-id="01b1c-274">As a separate effort, we could look into adding `async { ... }` expressions to the language, at which point we could allow them to be used in query comprehensions and the above could instead be written as:</span></span>

```csharp
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select async
                               {
                                   await Task.Yield();
                                   return item * 2;
                               };
```

<span data-ttu-id="01b1c-275">ou pour permettre l’utilisation directe de `await` dans des expressions, par exemple en prenant en charge `async from`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-275">or to enabling `await` to be used directly in expressions, such as by supporting `async from`.</span></span>  <span data-ttu-id="01b1c-276">Toutefois, il est peu probable qu’une conception ici ait un impact sur le reste de l’ensemble de fonctionnalités, ou sur l’autre, et ce n’est pas une chose particulièrement importante pour investir à l’heure actuelle. par conséquent, la proposition consiste à ne rien faire d’autre ici pour le moment.</span><span class="sxs-lookup"><span data-stu-id="01b1c-276">However, it's unlikely a design here would impact the rest of the feature set one way or the other, and this isn't a particularly high-value thing to invest in right now, so the proposal is to do nothing additional here right now.</span></span>

## <a name="integration-with-other-asynchronous-frameworks"></a><span data-ttu-id="01b1c-277">Intégration à d’autres frameworks asynchrones</span><span class="sxs-lookup"><span data-stu-id="01b1c-277">Integration with other asynchronous frameworks</span></span>

<span data-ttu-id="01b1c-278">L’intégration avec `IObservable<T>` et d’autres frameworks asynchrones (par exemple, les flux réactifs) est effectuée au niveau de la bibliothèque plutôt qu’au niveau de la langue.</span><span class="sxs-lookup"><span data-stu-id="01b1c-278">Integration with `IObservable<T>` and other asynchronous frameworks (e.g. reactive streams) would be done at the library level rather than at the language level.</span></span>  <span data-ttu-id="01b1c-279">Par exemple, toutes les données d’un `IAsyncEnumerator<T>` peuvent être publiées sur un `IObserver<T>` simplement en `await foreach`ant sur l’énumérateur et `OnNext`de données à l’observateur. une méthode d’extension `AsObservable<T>` est donc possible.</span><span class="sxs-lookup"><span data-stu-id="01b1c-279">For example, all of the data from an `IAsyncEnumerator<T>` can be published to an `IObserver<T>` simply by `await foreach`'ing over the enumerator and `OnNext`'ing the data to the observer, so an `AsObservable<T>` extension method is possible.</span></span>  <span data-ttu-id="01b1c-280">L’utilisation d’un `IObservable<T>` dans un `await foreach` nécessite la mise en mémoire tampon des données (au cas où un autre élément est envoyé alors que l’élément précédent est toujours en cours de traitement), mais un tel adaptateur push-pull peut facilement être implémenté pour permettre l’extraction d’un `IObservable<T>` à partir d’un `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="01b1c-280">Consuming an `IObservable<T>` in a `await foreach` requires buffering the data (in case another item is pushed while the previous item is still being processing), but such a push-pull adapter can easily be implemented to enable an `IObservable<T>` to be pulled from with an `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="01b1c-281">Suite.  RX/IX fournit déjà des prototypes de telles implémentations, et les bibliothèques comme https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels fournissent différents types de structures de données de mise en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="01b1c-281">Etc.  Rx/Ix already provide prototypes of such implementations, and libraries like https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels provide various kinds of buffering data structures.</span></span>  <span data-ttu-id="01b1c-282">La langue n’est pas nécessaire à ce niveau.</span><span class="sxs-lookup"><span data-stu-id="01b1c-282">The language need not be involved at this stage.</span></span>
