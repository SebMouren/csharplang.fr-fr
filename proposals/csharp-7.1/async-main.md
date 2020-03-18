---
ms.openlocfilehash: 405153448d0e3685d6f22725e00d75d9250b3e20
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484796"
---
# <a name="async-main"></a><span data-ttu-id="87825-101">Main asynchrone</span><span class="sxs-lookup"><span data-stu-id="87825-101">Async Main</span></span>

* <span data-ttu-id="87825-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="87825-102">[x] Proposed</span></span>
* <span data-ttu-id="87825-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="87825-103">[ ] Prototype</span></span>
* <span data-ttu-id="87825-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="87825-104">[ ] Implementation</span></span>
* <span data-ttu-id="87825-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="87825-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="87825-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="87825-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="87825-107">Autorisez l’utilisation de `await` dans la méthode main/EntryPoint d’une application en autorisant le point d’entrée à retourner `Task` / `Task<int>` et à être marqué `async`.</span><span class="sxs-lookup"><span data-stu-id="87825-107">Allow `await` to be used in an application's Main / entrypoint method by allowing the entrypoint to return `Task` / `Task<int>` and be marked `async`.</span></span>

## <a name="motivation"></a><span data-ttu-id="87825-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="87825-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="87825-109">Il est très courant, lors C#de l’apprentissage, lors de l’écriture d’utilitaires basés sur une console, et lors de l’écriture de petites applications de test pour appeler et `await` méthodes `async` à partir de main.</span><span class="sxs-lookup"><span data-stu-id="87825-109">It is very common when learning C#, when writing console-based utilities, and when writing small test apps to want to call and `await` `async` methods from Main.</span></span>  <span data-ttu-id="87825-110">Aujourd’hui, nous ajoutons un niveau de complexité ici en forçant ce `await`à être effectué dans une méthode Async distincte, ce qui amène les développeurs à avoir besoin d’écrire des éléments réutilisables comme suit pour commencer :</span><span class="sxs-lookup"><span data-stu-id="87825-110">Today we add a level of complexity here by forcing such `await`'ing to be done in a separate async method, which causes developers to need to write boilerplate like the following just to get started:</span></span>

```csharp
public static void Main()
{
    MainAsync().GetAwaiter().GetResult();
}

private static async Task MainAsync()
{
    ... // Main body here
}
```

<span data-ttu-id="87825-111">Nous pouvons supprimer la nécessité de ce type de réutilisation et faciliter la prise en main en permettant simplement à la principale d’être `async` de manière à ce que `await`s puisse être utilisé dans celle-ci.</span><span class="sxs-lookup"><span data-stu-id="87825-111">We can remove the need for this boilerplate and make it easier to get started simply by allowing Main itself to be `async` such that `await`s can be used in it.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="87825-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="87825-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="87825-113">Les signatures suivantes sont actuellement autorisées entryPoints :</span><span class="sxs-lookup"><span data-stu-id="87825-113">The following signatures are currently allowed entrypoints:</span></span>

```csharp
static void Main()
static void Main(string[])
static int Main()
static int Main(string[])
```

<span data-ttu-id="87825-114">Nous étendons la liste des entryPoints autorisés à inclure :</span><span class="sxs-lookup"><span data-stu-id="87825-114">We extend the list of allowed entrypoints to include:</span></span>

```csharp
static Task Main()
static Task<int> Main()
static Task Main(string[])
static Task<int> Main(string[])
```

<span data-ttu-id="87825-115">Pour éviter les risques de compatibilité, ces nouvelles signatures sont considérées comme valides entryPoints si aucune surcharge de l’ensemble précédent n’est présente.</span><span class="sxs-lookup"><span data-stu-id="87825-115">To avoid compatibility risks, these new signatures will only be considered as valid entrypoints if no overloads of the previous set are present.</span></span>
<span data-ttu-id="87825-116">Le langage/compilateur n’exige pas que le point d’entrée soit marqué comme `async`, bien que la plupart des utilisations soient marquées comme telles.</span><span class="sxs-lookup"><span data-stu-id="87825-116">The language / compiler will not require that the entrypoint be marked as `async`, though we expect the vast majority of uses will be marked as such.</span></span>

<span data-ttu-id="87825-117">Quand l’un de ces éléments est identifié comme étant le point d’entrée, le compilateur synthétise une méthode EntryPoint réelle qui appelle l’une de ces méthodes codées :</span><span class="sxs-lookup"><span data-stu-id="87825-117">When one of these is identified as the entrypoint, the compiler will synthesize an actual entrypoint method that calls one of these coded methods:</span></span>
- <span data-ttu-id="87825-118">```static Task Main()``` entraîne l’émission par le compilateur de l’équivalent de ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="87825-118">```static Task Main()``` will result in the compiler emitting the equivalent of ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="87825-119">```static Task Main(string[])``` entraîne l’émission par le compilateur de l’équivalent de ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="87825-119">```static Task Main(string[])``` will result in the compiler emitting the equivalent of ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="87825-120">```static Task<int> Main()``` entraîne l’émission par le compilateur de l’équivalent de ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="87825-120">```static Task<int> Main()``` will result in the compiler emitting the equivalent of ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="87825-121">```static Task<int> Main(string[])``` entraîne l’émission par le compilateur de l’équivalent de ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="87825-121">```static Task<int> Main(string[])``` will result in the compiler emitting the equivalent of ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span></span>

<span data-ttu-id="87825-122">Exemple d’utilisation :</span><span class="sxs-lookup"><span data-stu-id="87825-122">Example usage:</span></span>

```csharp
using System;
using System.Net.Http;

class Test
{
    static async Task Main(string[] args) =>
        Console.WriteLine(await new HttpClient().GetStringAsync(args[0]));
}
```

## <a name="drawbacks"></a><span data-ttu-id="87825-123">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="87825-123">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="87825-124">Le principal inconvénient est simplement la complexité supplémentaire de la prise en charge de signatures EntryPoint supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="87825-124">The main drawback is simply the additional complexity of supporting additional entrypoint signatures.</span></span>

## <a name="alternatives"></a><span data-ttu-id="87825-125">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="87825-125">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="87825-126">Autres variantes prises en compte :</span><span class="sxs-lookup"><span data-stu-id="87825-126">Other variants considered:</span></span>

<span data-ttu-id="87825-127">Autorisation de `async void`.</span><span class="sxs-lookup"><span data-stu-id="87825-127">Allowing `async void`.</span></span>  <span data-ttu-id="87825-128">Nous devons conserver la sémantique de la même façon pour le code qui l’appelle directement, ce qui rendrait ensuite difficile pour un point d’entrée généré de l’appeler (aucune tâche n’est retournée).</span><span class="sxs-lookup"><span data-stu-id="87825-128">We need to keep the semantics the same for code calling it directly, which would then make it difficult for a generated entrypoint to call it (no Task returned).</span></span>  <span data-ttu-id="87825-129">Nous pourrions résoudre ce risque en générant deux autres méthodes, par exemple</span><span class="sxs-lookup"><span data-stu-id="87825-129">We could solve this by generating two other methods, e.g.</span></span>

```csharp
public static async void Main()
{
   ... // await code
}
```

<span data-ttu-id="87825-130">devient</span><span class="sxs-lookup"><span data-stu-id="87825-130">becomes</span></span>

```csharp
public static async void Main() => await $MainTask();

private static void $EntrypointMain() => Main().GetAwaiter().GetResult();

private static async Task $MainTask()
{
    ... // await code
}
```

<span data-ttu-id="87825-131">Il y a également des préoccupations concernant l’utilisation de `async void`.</span><span class="sxs-lookup"><span data-stu-id="87825-131">There are also concerns around encouraging usage of `async void`.</span></span>

<span data-ttu-id="87825-132">Utilisation de « MainAsync » au lieu de « main » comme nom.</span><span class="sxs-lookup"><span data-stu-id="87825-132">Using "MainAsync" instead of "Main" as the name.</span></span>  <span data-ttu-id="87825-133">Si le suffixe Async est recommandé pour les méthodes qui retournent des tâches, il s’agit principalement de la fonctionnalité de bibliothèque, qui n’est pas, et la prise en charge de noms de point d’entrée supplémentaires au-delà de « main » ne le justifie pas.</span><span class="sxs-lookup"><span data-stu-id="87825-133">While the async suffix is recommended for Task-returning methods, that's primarily about library functionality, which Main is not, and supporting additional entrypoint names beyond "Main" is not worth it.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="87825-134">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="87825-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="87825-135">n/a</span><span class="sxs-lookup"><span data-stu-id="87825-135">n/a</span></span>

## <a name="design-meetings"></a><span data-ttu-id="87825-136">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="87825-136">Design meetings</span></span>

<span data-ttu-id="87825-137">n/a</span><span class="sxs-lookup"><span data-stu-id="87825-137">n/a</span></span>
