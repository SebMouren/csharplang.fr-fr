---
ms.openlocfilehash: 54ae4ffabde6dca49b7e6bfb626d65837eabc8f5
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281942"
---
# <a name="simple-programs"></a>Programmes simples

* [x] proposé
* [x] prototype : démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Autoriser une séquence d' *instructions* à se produire juste avant la *namespace_member_declaration*s d’un *compilation_unit* (c.-à-d. fichier source).

La sémantique est que, si une telle séquence d' *instructions* est présente, la déclaration de type suivante, Modulo le nom de type réel et le nom de la méthode, serait émise :

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Voir aussi https://github.com/dotnet/csharplang/issues/3117.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il y a une certaine quantité de programme, même la plus simple des programmes, en raison de la nécessité d’une méthode de `Main` explicite. Cela semble être un moyen d’apprendre et de clarifier le programme. L’objectif principal de cette fonctionnalité est donc d’autoriser C# les programmes qui n’ont pas besoin d’être réutilisés, à des fins d’apprentissage et de clarté du code.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

### <a name="syntax"></a>Syntaxe

La seule syntaxe supplémentaire autorise une séquence d' *instructions*dans une unité de compilation, juste avant la *namespace_member_declaration*s :

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

Un seul *compilation_unit* est autorisé à avoir des *instructions*. 

Exemple :

``` c#
if (System.Environment.CommandLine.Length == 0
    || !int.TryParse(System.Environment.CommandLine, out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a>Sémantique

Si des instructions de niveau supérieur sont présentes dans une unité de compilation du programme, la signification est comme si elles étaient combinées dans le corps du bloc d’une méthode `Main` d’une classe `Program` dans l’espace de noms global, comme suit :

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Notez que les noms « Program » et « main » sont utilisés uniquement à des fins d’illustration. les noms réels utilisés par le compilateur sont dépendants de l’implémentation et ni du type, ni la méthode ne peut être référencée par nom à partir du code source.

La méthode est désignée comme point d’entrée du programme. Les méthodes déclarées explicitement qui, par Convention, peuvent être considérées comme des candidats à un point d’entrée sont ignorées. Un avertissement est signalé lorsque cela se produit. Il est erroné de spécifier `-main:<type>` commutateur du compilateur lorsqu’il existe des instructions de niveau supérieur.

Les opérations asynchrones sont autorisées dans les instructions de niveau supérieur jusqu’à ce qu’elles soient autorisées dans les instructions au sein d’une méthode de point d’entrée Async standard. Toutefois, elles ne sont pas requises, si `await` expressions et d’autres opérations asynchrones sont omises, aucun avertissement n’est généré. Au lieu de cela, la signature de la méthode de point d’entrée générée est équivalente à 
``` c#
    static void Main()
```

L’exemple ci-dessus produirait la déclaration de méthode `$Main` suivante :

``` c#
static class $Program
{
    static void $Main()
    {
        if (System.Environment.CommandLine.Length == 0
            || !int.TryParse(System.Environment.CommandLine, out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

En même temps, un exemple semblable à celui-ci :
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

donne :
``` c#
static class $Program
{
    static async Task $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>Portée des variables locales de niveau supérieur et des fonctions locales

Même si les variables locales de niveau supérieur et les fonctions sont « encapsulées » dans la méthode de point d’entrée générée, elles doivent toujours être dans la portée tout au long du programme dans chaque unité de compilation.
Dans le cadre de l’évaluation de nom simple, une fois l’espace de noms global atteint :
- Tout d’abord, une tentative est faite pour évaluer le nom dans la méthode de point d’entrée générée et uniquement si cette tentative échoue 
- L’évaluation « normale » dans la déclaration d’espace de noms globale est effectuée. 

Cela peut entraîner l’occultation de nom des espaces de noms et des types déclarés dans l’espace de noms global, ainsi que l’occultation de noms importés.

Si l’évaluation de nom simple se produit en dehors des instructions de niveau supérieur et que l’évaluation génère une variable locale ou une fonction de niveau supérieur, cela devrait entraîner une erreur.

De cette façon, nous protégeons notre future capacité à mieux répondre aux « fonctions de niveau supérieur » (scénario 2 dans https://github.com/dotnet/csharplang/issues/3117), et pouvons fournir des diagnostics utiles aux utilisateurs qui pensent par erreur qu’ils sont pris en charge.

