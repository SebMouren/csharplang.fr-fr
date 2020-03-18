---
ms.openlocfilehash: 405153448d0e3685d6f22725e00d75d9250b3e20
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484796"
---
# <a name="async-main"></a>Main asynchrone

* [x] proposé
* [] Prototype
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

Autorisez l’utilisation de `await` dans la méthode main/EntryPoint d’une application en autorisant le point d’entrée à retourner `Task` / `Task<int>` et à être marqué `async`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il est très courant, lors C#de l’apprentissage, lors de l’écriture d’utilitaires basés sur une console, et lors de l’écriture de petites applications de test pour appeler et `await` méthodes `async` à partir de main.  Aujourd’hui, nous ajoutons un niveau de complexité ici en forçant ce `await`à être effectué dans une méthode Async distincte, ce qui amène les développeurs à avoir besoin d’écrire des éléments réutilisables comme suit pour commencer :

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

Nous pouvons supprimer la nécessité de ce type de réutilisation et faciliter la prise en main en permettant simplement à la principale d’être `async` de manière à ce que `await`s puisse être utilisé dans celle-ci.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Les signatures suivantes sont actuellement autorisées entryPoints :

```csharp
static void Main()
static void Main(string[])
static int Main()
static int Main(string[])
```

Nous étendons la liste des entryPoints autorisés à inclure :

```csharp
static Task Main()
static Task<int> Main()
static Task Main(string[])
static Task<int> Main(string[])
```

Pour éviter les risques de compatibilité, ces nouvelles signatures sont considérées comme valides entryPoints si aucune surcharge de l’ensemble précédent n’est présente.
Le langage/compilateur n’exige pas que le point d’entrée soit marqué comme `async`, bien que la plupart des utilisations soient marquées comme telles.

Quand l’un de ces éléments est identifié comme étant le point d’entrée, le compilateur synthétise une méthode EntryPoint réelle qui appelle l’une de ces méthodes codées :
- ```static Task Main()``` entraîne l’émission par le compilateur de l’équivalent de ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```
- ```static Task Main(string[])``` entraîne l’émission par le compilateur de l’équivalent de ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```
- ```static Task<int> Main()``` entraîne l’émission par le compilateur de l’équivalent de ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```
- ```static Task<int> Main(string[])``` entraîne l’émission par le compilateur de l’équivalent de ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```

Exemple d’utilisation :

```csharp
using System;
using System.Net.Http;

class Test
{
    static async Task Main(string[] args) =>
        Console.WriteLine(await new HttpClient().GetStringAsync(args[0]));
}
```

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Le principal inconvénient est simplement la complexité supplémentaire de la prise en charge de signatures EntryPoint supplémentaires.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Autres variantes prises en compte :

Autorisation de `async void`.  Nous devons conserver la sémantique de la même façon pour le code qui l’appelle directement, ce qui rendrait ensuite difficile pour un point d’entrée généré de l’appeler (aucune tâche n’est retournée).  Nous pourrions résoudre ce risque en générant deux autres méthodes, par exemple

```csharp
public static async void Main()
{
   ... // await code
}
```

devient

```csharp
public static async void Main() => await $MainTask();

private static void $EntrypointMain() => Main().GetAwaiter().GetResult();

private static async Task $MainTask()
{
    ... // await code
}
```

Il y a également des préoccupations concernant l’utilisation de `async void`.

Utilisation de « MainAsync » au lieu de « main » comme nom.  Si le suffixe Async est recommandé pour les méthodes qui retournent des tâches, il s’agit principalement de la fonctionnalité de bibliothèque, qui n’est pas, et la prise en charge de noms de point d’entrée supplémentaires au-delà de « main » ne le justifie pas.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

n/a

## <a name="design-meetings"></a>Réunions de conception

n/a
