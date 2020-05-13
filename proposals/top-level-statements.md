---
ms.openlocfilehash: a42c55a8ebeb848cd0df906363c2feb327331ef6
ms.sourcegitcommit: c2fe8f1d150ac6ac171072d1c6f9df883adcbb40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83203241"
---
# <a name="top-level-statements"></a><span data-ttu-id="0ddab-101">Instructions de niveau supérieur</span><span class="sxs-lookup"><span data-stu-id="0ddab-101">Top-level statements</span></span>

* <span data-ttu-id="0ddab-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="0ddab-102">[x] Proposed</span></span>
* <span data-ttu-id="0ddab-103">[x] prototype : démarré</span><span class="sxs-lookup"><span data-stu-id="0ddab-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="0ddab-104">[x] implémentation : démarré</span><span class="sxs-lookup"><span data-stu-id="0ddab-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="0ddab-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="0ddab-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="0ddab-106">Récapitulatif</span><span class="sxs-lookup"><span data-stu-id="0ddab-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="0ddab-107">Autoriser une séquence d' *instructions* à se produire juste avant la *namespace_member_declaration*s d’un *compilation_unit* (c.-à-d. fichier source).</span><span class="sxs-lookup"><span data-stu-id="0ddab-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="0ddab-108">La sémantique est que, si une telle séquence d' *instructions* est présente, la déclaration de type suivante, Modulo le nom de type réel et le nom de la méthode, serait émise :</span><span class="sxs-lookup"><span data-stu-id="0ddab-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="0ddab-109">Voir aussi https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="0ddab-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="0ddab-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="0ddab-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="0ddab-111">Il y a une certaine quantité de programme, même la plus simple des programmes, en raison de la nécessité d’une `Main` méthode explicite.</span><span class="sxs-lookup"><span data-stu-id="0ddab-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="0ddab-112">Cela semble être un moyen d’apprendre et de clarifier le programme.</span><span class="sxs-lookup"><span data-stu-id="0ddab-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="0ddab-113">L’objectif principal de cette fonctionnalité est donc d’autoriser les programmes C# sans aucune réutilisabilité inutile, à des fins d’apprentissage et de clarté du code.</span><span class="sxs-lookup"><span data-stu-id="0ddab-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="0ddab-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="0ddab-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="0ddab-115">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="0ddab-115">Syntax</span></span>

<span data-ttu-id="0ddab-116">La seule syntaxe supplémentaire autorise une séquence d' *instructions*dans une unité de compilation, juste avant la *namespace_member_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0ddab-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="0ddab-117">Un seul *compilation_unit* est autorisé à avoir des *instructions*.</span><span class="sxs-lookup"><span data-stu-id="0ddab-117">Only one *compilation_unit* is allowed to have *statement*s.</span></span> 

<span data-ttu-id="0ddab-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0ddab-118">Example:</span></span>

``` c#
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="0ddab-119">Sémantique</span><span class="sxs-lookup"><span data-stu-id="0ddab-119">Semantics</span></span>

<span data-ttu-id="0ddab-120">Si des instructions de niveau supérieur sont présentes dans une unité de compilation du programme, la signification est comme si elles étaient combinées dans le corps de bloc d’une `Main` méthode d’une `Program` classe de l’espace de noms global, comme suit :</span><span class="sxs-lookup"><span data-stu-id="0ddab-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="0ddab-121">Notez que les noms « Program » et « main » sont utilisés uniquement à des fins d’illustration. les noms réels utilisés par le compilateur sont dépendants de l’implémentation et ni du type, ni la méthode ne peut être référencée par nom à partir du code source.</span><span class="sxs-lookup"><span data-stu-id="0ddab-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="0ddab-122">La méthode est désignée comme point d’entrée du programme.</span><span class="sxs-lookup"><span data-stu-id="0ddab-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="0ddab-123">Les méthodes déclarées explicitement qui, par Convention, peuvent être considérées comme des candidats à un point d’entrée sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0ddab-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="0ddab-124">Un avertissement est signalé lorsque cela se produit.</span><span class="sxs-lookup"><span data-stu-id="0ddab-124">A warning is reported when that happens.</span></span> <span data-ttu-id="0ddab-125">Il est erroné de spécifier le `-main:<type>` commutateur du compilateur lorsqu’il existe des instructions de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="0ddab-125">It is an error to specify `-main:<type>` compiler switch when there are top-level statements.</span></span>

<span data-ttu-id="0ddab-126">La méthode de point d’entrée a toujours un paramètre formel, ```string[] args``` .</span><span class="sxs-lookup"><span data-stu-id="0ddab-126">The entry point method always has one formal parameter, ```string[] args```.</span></span> <span data-ttu-id="0ddab-127">L’environnement d’exécution crée et passe un ```string[]``` argument contenant les arguments de ligne de commande spécifiés au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0ddab-127">The execution environment creates and passes a ```string[]``` argument containing the command-line arguments that were specified when the application was started.</span></span> <span data-ttu-id="0ddab-128">L' ```string[]``` argument n’est jamais null, mais peut avoir une longueur de zéro si aucun argument de ligne de commande n’a été spécifié.</span><span class="sxs-lookup"><span data-stu-id="0ddab-128">The ```string[]``` argument is never null, but it may have a length of zero if no command-line arguments were specified.</span></span> <span data-ttu-id="0ddab-129">Le paramètre « args » se trouve dans la portée dans les instructions de niveau supérieur et n’est pas dans la portée en dehors de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="0ddab-129">The ‘args’ parameter is in scope  within top-level statements and is not in scope outside of them.</span></span> <span data-ttu-id="0ddab-130">Des règles de conflit ou de masquage de noms standard s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="0ddab-130">Regular name conflict/shadowing rules apply.</span></span>

<span data-ttu-id="0ddab-131">Les opérations asynchrones sont autorisées dans les instructions de niveau supérieur jusqu’à ce qu’elles soient autorisées dans les instructions au sein d’une méthode de point d’entrée Async standard.</span><span class="sxs-lookup"><span data-stu-id="0ddab-131">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="0ddab-132">Toutefois, elles ne sont pas requises, si des `await` expressions et d’autres opérations asynchrones sont omises, aucun avertissement n’est généré.</span><span class="sxs-lookup"><span data-stu-id="0ddab-132">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span>

<span data-ttu-id="0ddab-133">La signature de la méthode de point d’entrée générée est déterminée en fonction des opérations utilisées par les instructions de niveau supérieur comme suit :</span><span class="sxs-lookup"><span data-stu-id="0ddab-133">The signature of the generated entry point method is determined based on operations used by the top level statements as follows:</span></span>
<span data-ttu-id="0ddab-134">**Async-operations\Return-with-expression**</span><span class="sxs-lookup"><span data-stu-id="0ddab-134">**Async-operations\Return-with-expression**</span></span> | <span data-ttu-id="0ddab-135">**Trouver**</span><span class="sxs-lookup"><span data-stu-id="0ddab-135">**Present**</span></span> | <span data-ttu-id="0ddab-136">**Absent**</span><span class="sxs-lookup"><span data-stu-id="0ddab-136">**Absent**</span></span>
----------------------------------------| -------------|-------------
<span data-ttu-id="0ddab-137">**Trouver**</span><span class="sxs-lookup"><span data-stu-id="0ddab-137">**Present**</span></span> | ```static Task<int> Main(string[] args)```| ```static Task Main(string[] args)```
<span data-ttu-id="0ddab-138">**Absent**</span><span class="sxs-lookup"><span data-stu-id="0ddab-138">**Absent**</span></span>  | ```static int Main(string[] args)``` | ```static void Main(string[] args)```

<span data-ttu-id="0ddab-139">L’exemple ci-dessus produirait la `$Main` déclaration de méthode suivante :</span><span class="sxs-lookup"><span data-stu-id="0ddab-139">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main(string[] args)
    {
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
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

<span data-ttu-id="0ddab-140">En même temps, un exemple semblable à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="0ddab-140">At the same time an example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="0ddab-141">donne :</span><span class="sxs-lookup"><span data-stu-id="0ddab-141">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

<span data-ttu-id="0ddab-142">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="0ddab-142">An example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

<span data-ttu-id="0ddab-143">donne :</span><span class="sxs-lookup"><span data-stu-id="0ddab-143">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task<int> $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

<span data-ttu-id="0ddab-144">Et un exemple similaire à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="0ddab-144">And an example like this:</span></span>
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

<span data-ttu-id="0ddab-145">donne :</span><span class="sxs-lookup"><span data-stu-id="0ddab-145">would  yield:</span></span>
``` c#
static class $Program
{
    static int $Main(string[] args)
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="0ddab-146">Portée des variables locales de niveau supérieur et des fonctions locales</span><span class="sxs-lookup"><span data-stu-id="0ddab-146">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="0ddab-147">Même si les variables locales de niveau supérieur et les fonctions sont « encapsulées » dans la méthode de point d’entrée générée, elles doivent toujours être dans la portée tout au long du programme dans chaque unité de compilation.</span><span class="sxs-lookup"><span data-stu-id="0ddab-147">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program in every compilation unit.</span></span>
<span data-ttu-id="0ddab-148">Dans le cadre de l’évaluation de nom simple, une fois l’espace de noms global atteint :</span><span class="sxs-lookup"><span data-stu-id="0ddab-148">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="0ddab-149">Tout d’abord, une tentative est faite pour évaluer le nom dans la méthode de point d’entrée générée et uniquement si cette tentative échoue</span><span class="sxs-lookup"><span data-stu-id="0ddab-149">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="0ddab-150">L’évaluation « normale » dans la déclaration d’espace de noms globale est effectuée.</span><span class="sxs-lookup"><span data-stu-id="0ddab-150">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="0ddab-151">Cela peut entraîner l’occultation de nom des espaces de noms et des types déclarés dans l’espace de noms global, ainsi que l’occultation de noms importés.</span><span class="sxs-lookup"><span data-stu-id="0ddab-151">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="0ddab-152">Si l’évaluation de nom simple se produit en dehors des instructions de niveau supérieur et que l’évaluation génère une variable locale ou une fonction de niveau supérieur, cela devrait entraîner une erreur.</span><span class="sxs-lookup"><span data-stu-id="0ddab-152">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="0ddab-153">De cette façon, nous protégeons notre future capacité à mieux répondre aux « fonctions de niveau supérieur » (scénario 2 dans https://github.com/dotnet/csharplang/issues/3117) , et pouvons fournir des diagnostics utiles aux utilisateurs qui pensent par erreur qu’ils sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0ddab-153">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

