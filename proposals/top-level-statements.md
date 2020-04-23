---
ms.openlocfilehash: 252b7d601ce2f1649b090dcfd97a3aea15d8e132
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82038234"
---
# <a name="top-level-statements"></a><span data-ttu-id="831fc-101">Déclarations de haut niveau</span><span class="sxs-lookup"><span data-stu-id="831fc-101">Top-level statements</span></span>

* <span data-ttu-id="831fc-102">[x] Proposé</span><span class="sxs-lookup"><span data-stu-id="831fc-102">[x] Proposed</span></span>
* <span data-ttu-id="831fc-103">[x] Prototype: Commencé</span><span class="sxs-lookup"><span data-stu-id="831fc-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="831fc-104">[x] Mise en œuvre : Démarrage</span><span class="sxs-lookup"><span data-stu-id="831fc-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="831fc-105">[ ] Spécification: Pas commencé</span><span class="sxs-lookup"><span data-stu-id="831fc-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="831fc-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="831fc-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="831fc-107">Permettre qu’une séquence *d’instructions* se produise juste avant le *namespace_member_declaration*d’un *compilation_unit* (c.-à-d. le fichier source).</span><span class="sxs-lookup"><span data-stu-id="831fc-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="831fc-108">La sémantique est que si une telle séquence de *déclarations* est présente, la déclaration de type suivante, modulo le nom de type réel et le nom de la méthode, seraient émis:</span><span class="sxs-lookup"><span data-stu-id="831fc-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="831fc-109">Voir aussi https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="831fc-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="831fc-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="831fc-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="831fc-111">Il ya une certaine quantité de plaque chauffante entourant même le plus `Main` simple des programmes, en raison de la nécessité d’une méthode explicite.</span><span class="sxs-lookup"><span data-stu-id="831fc-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="831fc-112">Cela semble mettre en travers de la voie de l’apprentissage des langues et de la clarté du programme.</span><span class="sxs-lookup"><span data-stu-id="831fc-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="831fc-113">L’objectif principal de la fonctionnalité est donc d’autoriser les programmes C 'sans plaque chauffante inutile autour d’eux, pour le bien des apprenants et la clarté du code.</span><span class="sxs-lookup"><span data-stu-id="831fc-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="831fc-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="831fc-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="831fc-115">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="831fc-115">Syntax</span></span>

<span data-ttu-id="831fc-116">La seule syntaxe supplémentaire est d’autoriser une séquence de *déclaration*s dans une unité de compilation, juste avant le *namespace_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="831fc-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="831fc-117">Un seul *compilation_unit* est autorisé à avoir *la déclaration*s.</span><span class="sxs-lookup"><span data-stu-id="831fc-117">Only one *compilation_unit* is allowed to have *statement*s.</span></span> 

<span data-ttu-id="831fc-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="831fc-118">Example:</span></span>

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

### <a name="semantics"></a><span data-ttu-id="831fc-119">Sémantique</span><span class="sxs-lookup"><span data-stu-id="831fc-119">Semantics</span></span>

<span data-ttu-id="831fc-120">Si des déclarations de haut niveau sont présentes dans n’importe quelle unité de `Main` compilation du `Program` programme, le sens est comme s’ils étaient combinés dans le corps de bloc d’une méthode d’une classe dans l’espace de nom global, comme suit :</span><span class="sxs-lookup"><span data-stu-id="831fc-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="831fc-121">Notez que les noms «Programme» et «Main» ne sont utilisés qu’à des fins d’illustrations, les noms réels utilisés par le compilateur sont dépendants de la mise en œuvre et ni le type, ni la méthode ne peut être référencé par le nom à partir du code source.</span><span class="sxs-lookup"><span data-stu-id="831fc-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="831fc-122">La méthode est désignée comme point d’entrée du programme.</span><span class="sxs-lookup"><span data-stu-id="831fc-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="831fc-123">Les méthodes explicitement déclarées selon lesquelles, selon la convention, les candidats pourraient être considérés comme des candidats aux points d’entrée sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="831fc-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="831fc-124">Un avertissement est signalé lorsque cela se produit.</span><span class="sxs-lookup"><span data-stu-id="831fc-124">A warning is reported when that happens.</span></span> <span data-ttu-id="831fc-125">C’est une `-main:<type>` erreur de spécifier l’interrupteur compilateur lorsqu’il y a des relevés de haut niveau.</span><span class="sxs-lookup"><span data-stu-id="831fc-125">It is an error to specify `-main:<type>` compiler switch when there are top-level statements.</span></span>

<span data-ttu-id="831fc-126">Les opérations Async sont autorisées dans les relevés de haut niveau dans la mesure où elles sont autorisées dans les déclarations dans le cadre d’une méthode régulière de point d’entrée async.</span><span class="sxs-lookup"><span data-stu-id="831fc-126">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="831fc-127">Cependant, ils ne sont `await` pas nécessaires, si des expressions et d’autres opérations async sont omises, aucun avertissement n’est produit.</span><span class="sxs-lookup"><span data-stu-id="831fc-127">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span>

<span data-ttu-id="831fc-128">La signature de la méthode générée du point d’entrée est déterminée en fonction des opérations utilisées par les énoncés de niveau supérieur comme suit :</span><span class="sxs-lookup"><span data-stu-id="831fc-128">The signature of the generated entry point method is determined based on operations used by the top level statements as follows:</span></span>
<span data-ttu-id="831fc-129">**Async-operations-Retour-avec-expression**</span><span class="sxs-lookup"><span data-stu-id="831fc-129">**Async-operations\Return-with-expression**</span></span> | <span data-ttu-id="831fc-130">**Présent**</span><span class="sxs-lookup"><span data-stu-id="831fc-130">**Present**</span></span> | <span data-ttu-id="831fc-131">**Absent**</span><span class="sxs-lookup"><span data-stu-id="831fc-131">**Absent**</span></span>
----------------------------------------| -------------|-------------
<span data-ttu-id="831fc-132">**Présent**</span><span class="sxs-lookup"><span data-stu-id="831fc-132">**Present**</span></span> | ```static Task<int> Main()```| ```static Task Main()```
<span data-ttu-id="831fc-133">**Absent**</span><span class="sxs-lookup"><span data-stu-id="831fc-133">**Absent**</span></span>  | ```static int Main()``` | ```static void Main()```

<span data-ttu-id="831fc-134">L’exemple ci-dessus `$Main` donnerait la déclaration de méthode suivante :</span><span class="sxs-lookup"><span data-stu-id="831fc-134">The example above would yield the following `$Main` method declaration:</span></span>

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

<span data-ttu-id="831fc-135">En même temps un exemple comme celui-ci:</span><span class="sxs-lookup"><span data-stu-id="831fc-135">At the same time an example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="831fc-136">donnerait :</span><span class="sxs-lookup"><span data-stu-id="831fc-136">would  yield:</span></span>
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

<span data-ttu-id="831fc-137">Un exemple comme celui-ci:</span><span class="sxs-lookup"><span data-stu-id="831fc-137">An example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

<span data-ttu-id="831fc-138">donnerait :</span><span class="sxs-lookup"><span data-stu-id="831fc-138">would  yield:</span></span>
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

<span data-ttu-id="831fc-139">Et un exemple comme celui-ci:</span><span class="sxs-lookup"><span data-stu-id="831fc-139">And an example like this:</span></span>
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

<span data-ttu-id="831fc-140">donnerait :</span><span class="sxs-lookup"><span data-stu-id="831fc-140">would  yield:</span></span>
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

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="831fc-141">Portée des variables locales de haut niveau et des fonctions locales</span><span class="sxs-lookup"><span data-stu-id="831fc-141">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="831fc-142">Même si les variables et les fonctions locales de haut niveau sont « enveloppées » dans la méthode générée des points d’entrée, elles devraient toujours être de portée tout au long du programme dans chaque unité de compilation.</span><span class="sxs-lookup"><span data-stu-id="831fc-142">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program in every compilation unit.</span></span>
<span data-ttu-id="831fc-143">Aux fins de l’évaluation simple de nom, une fois que l’espace de nom global est atteint :</span><span class="sxs-lookup"><span data-stu-id="831fc-143">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="831fc-144">Tout d’abord, une tentative est faite d’évaluer le nom dans la méthode générée point d’entrée et seulement si cette tentative échoue</span><span class="sxs-lookup"><span data-stu-id="831fc-144">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="831fc-145">L’évaluation « régulière » dans la déclaration globale de namespace est effectuée.</span><span class="sxs-lookup"><span data-stu-id="831fc-145">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="831fc-146">Cela pourrait conduire à l’ombre de noms des espaces de nom et des types déclarés dans l’espace de nom global ainsi que l’ombre des noms importés.</span><span class="sxs-lookup"><span data-stu-id="831fc-146">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="831fc-147">Si l’évaluation simple du nom se produit en dehors des énoncés de haut niveau et que l’évaluation donne une variable ou une fonction locale de haut niveau, cela devrait conduire à une erreur.</span><span class="sxs-lookup"><span data-stu-id="831fc-147">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="831fc-148">De cette façon, nous protégeons notre capacité future à mieux répondre https://github.com/dotnet/csharplang/issues/3117)aux « fonctions de haut niveau » (scénario 2 po , et sommes en mesure de donner des diagnostics utiles aux utilisateurs qui croient à tort qu’ils sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="831fc-148">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

