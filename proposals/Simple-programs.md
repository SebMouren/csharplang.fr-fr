---
ms.openlocfilehash: 0e2b4b70e0555145f54be2cc57da9f50f5a43548
ms.sourcegitcommit: 1ecebc412f073f3959395f33b70666c8ede88f3c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81612099"
---
# <a name="simple-programs"></a>Programmes simples

* [x] Proposé
* [x] Prototype: Commencé
* [x] Mise en œuvre : Démarrage
* [ ] Spécification: Pas commencé

## <a name="summary"></a>Résumé
[summary]: #summary

Permettre qu’une séquence *d’instructions* se produise juste avant le *namespace_member_declaration*d’un *compilation_unit* (c.-à-d. le fichier source).

La sémantique est que si une telle séquence de *déclarations* est présente, la déclaration de type suivante, modulo le nom de type réel et le nom de la méthode, seraient émis:

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

Il ya une certaine quantité de plaque chauffante entourant même le plus `Main` simple des programmes, en raison de la nécessité d’une méthode explicite. Cela semble mettre en travers de la voie de l’apprentissage des langues et de la clarté du programme. L’objectif principal de la fonctionnalité est donc d’autoriser les programmes C 'sans plaque chauffante inutile autour d’eux, pour le bien des apprenants et la clarté du code.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

### <a name="syntax"></a>Syntaxe

La seule syntaxe supplémentaire est d’autoriser une séquence de *déclaration*s dans une unité de compilation, juste avant le *namespace_member_declaration*s:

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

Un seul *compilation_unit* est autorisé à avoir *la déclaration*s. 

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

Si des déclarations de haut niveau sont présentes dans n’importe quelle unité de `Main` compilation du `Program` programme, le sens est comme s’ils étaient combinés dans le corps de bloc d’une méthode d’une classe dans l’espace de nom global, comme suit :

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Notez que les noms «Programme» et «Main» ne sont utilisés qu’à des fins d’illustrations, les noms réels utilisés par le compilateur sont dépendants de la mise en œuvre et ni le type, ni la méthode ne peut être référencé par le nom à partir du code source.

La méthode est désignée comme point d’entrée du programme. Les méthodes explicitement déclarées selon lesquelles, selon la convention, les candidats pourraient être considérés comme des candidats aux points d’entrée sont ignorées. Un avertissement est signalé lorsque cela se produit. C’est une `-main:<type>` erreur de spécifier l’interrupteur compilateur lorsqu’il y a des relevés de haut niveau.

Les opérations Async sont autorisées dans les relevés de haut niveau dans la mesure où elles sont autorisées dans les déclarations dans le cadre d’une méthode régulière de point d’entrée async. Cependant, ils ne sont `await` pas nécessaires, si des expressions et d’autres opérations async sont omises, aucun avertissement n’est produit.

La signature de la méthode générée du point d’entrée est déterminée en fonction des opérations utilisées par les énoncés de niveau supérieur comme suit :
**Async-operations-Retour-avec-expression** | **Présent** | **Absent**
----------------------------------------| -------------|-------------
**Présent** | ```static Task<int> Main()```| ```static Task Main()```
**Absent**  | ```static int Main()``` | ```static void Main()```

L’exemple ci-dessus `$Main` donnerait la déclaration de méthode suivante :

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

En même temps un exemple comme celui-ci:
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

donnerait :
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

Un exemple comme celui-ci:
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

donnerait :
``` c#
static class $Program
{
    static async Task<int> $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

Et un exemple comme celui-ci:
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

donnerait :
``` c#
static class $Program
{
    static int $Main()
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>Portée des variables locales de haut niveau et des fonctions locales

Même si les variables et les fonctions locales de haut niveau sont « enveloppées » dans la méthode générée des points d’entrée, elles devraient toujours être de portée tout au long du programme dans chaque unité de compilation.
Aux fins de l’évaluation simple de nom, une fois que l’espace de nom global est atteint :
- Tout d’abord, une tentative est faite d’évaluer le nom dans la méthode générée point d’entrée et seulement si cette tentative échoue 
- L’évaluation « régulière » dans la déclaration globale de namespace est effectuée. 

Cela pourrait conduire à l’ombre de noms des espaces de nom et des types déclarés dans l’espace de nom global ainsi que l’ombre des noms importés.

Si l’évaluation simple du nom se produit en dehors des énoncés de haut niveau et que l’évaluation donne une variable ou une fonction locale de haut niveau, cela devrait conduire à une erreur.

De cette façon, nous protégeons notre capacité future à mieux répondre https://github.com/dotnet/csharplang/issues/3117)aux « fonctions de haut niveau » (scénario 2 po , et sommes en mesure de donner des diagnostics utiles aux utilisateurs qui croient à tort qu’ils sont pris en charge.

