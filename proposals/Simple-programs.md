---
ms.openlocfilehash: b9697fc1d772ba59ed3b1de339a5a3d4eb24b1bd
ms.sourcegitcommit: 36b028f4d6e88bd7d4a843c6d384d1b63cc73334
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "79485223"
---
# <a name="simple-programs"></a><span data-ttu-id="fda7c-101">Programmes simples</span><span class="sxs-lookup"><span data-stu-id="fda7c-101">Simple programs</span></span>

* <span data-ttu-id="fda7c-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="fda7c-102">[x] Proposed</span></span>
* <span data-ttu-id="fda7c-103">[x] prototype : démarré</span><span class="sxs-lookup"><span data-stu-id="fda7c-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="fda7c-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="fda7c-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="fda7c-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="fda7c-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="fda7c-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="fda7c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="fda7c-107">Autoriser une séquence d' *instructions* à se produire juste avant la *namespace_member_declaration*s d’un *compilation_unit* (c.-à-d. fichier source).</span><span class="sxs-lookup"><span data-stu-id="fda7c-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="fda7c-108">La sémantique est que, si une telle séquence d' *instructions* est présente, la déclaration de type suivante, Modulo le nom de type réel et le nom de la méthode, serait émise :</span><span class="sxs-lookup"><span data-stu-id="fda7c-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="fda7c-109">Voir aussi https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="fda7c-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="fda7c-110">Motivation</span><span class="sxs-lookup"><span data-stu-id="fda7c-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="fda7c-111">Il y a une certaine quantité de programme, même la plus simple des programmes, en raison de la nécessité d’une méthode de `Main` explicite.</span><span class="sxs-lookup"><span data-stu-id="fda7c-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="fda7c-112">Cela semble être un moyen d’apprendre et de clarifier le programme.</span><span class="sxs-lookup"><span data-stu-id="fda7c-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="fda7c-113">L’objectif principal de cette fonctionnalité est donc d’autoriser C# les programmes qui n’ont pas besoin d’être réutilisés, à des fins d’apprentissage et de clarté du code.</span><span class="sxs-lookup"><span data-stu-id="fda7c-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="fda7c-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="fda7c-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="fda7c-115">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="fda7c-115">Syntax</span></span>

<span data-ttu-id="fda7c-116">La seule syntaxe supplémentaire autorise une séquence d' *instructions*dans une unité de compilation, juste avant la *namespace_member_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="fda7c-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="fda7c-117">Dans toutes les *compilation_unit* , sauf une, les *instructions*doivent toutes être des déclarations de fonction locale.</span><span class="sxs-lookup"><span data-stu-id="fda7c-117">In all but one *compilation_unit* the *statement*s must all be local function declarations.</span></span> 

<span data-ttu-id="fda7c-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fda7c-118">Example:</span></span>

``` c#
// File 1 - any statements
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

// File 2 - only local functions
(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="fda7c-119">Sémantique</span><span class="sxs-lookup"><span data-stu-id="fda7c-119">Semantics</span></span>

<span data-ttu-id="fda7c-120">Si des instructions de niveau supérieur sont présentes dans une unité de compilation du programme, la signification est comme si elles étaient combinées dans le corps du bloc d’une méthode `Main` d’une classe `Program` dans l’espace de noms global, comme suit :</span><span class="sxs-lookup"><span data-stu-id="fda7c-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // File 1 statements
        // File 2 local functions
        // ...
    }
}
```

<span data-ttu-id="fda7c-121">Notez que les noms « Program » et « main » sont utilisés uniquement à des fins d’illustration. les noms réels utilisés par le compilateur sont dépendants de l’implémentation et ni du type, ni la méthode ne peut être référencée par nom à partir du code source.</span><span class="sxs-lookup"><span data-stu-id="fda7c-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="fda7c-122">La méthode est désignée comme point d’entrée du programme.</span><span class="sxs-lookup"><span data-stu-id="fda7c-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="fda7c-123">Les méthodes déclarées explicitement qui, par Convention, peuvent être considérées comme des candidats à un point d’entrée sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="fda7c-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="fda7c-124">Un avertissement est signalé lorsque cela se produit.</span><span class="sxs-lookup"><span data-stu-id="fda7c-124">A warning is reported when that happens.</span></span> <span data-ttu-id="fda7c-125">Spécifier `-main:<type>` commutateur du compilateur est une erreur.</span><span class="sxs-lookup"><span data-stu-id="fda7c-125">It is an error to specify `-main:<type>` compiler switch.</span></span>

<span data-ttu-id="fda7c-126">Si une unité de compilation a des instructions autres que des déclarations de fonctions locales, les instructions de cette unité de compilation se produisent en premier.</span><span class="sxs-lookup"><span data-stu-id="fda7c-126">If any one compilation unit has statements other than local function declarations, statements from that compilation unit occur first.</span></span> <span data-ttu-id="fda7c-127">Elle est alors légale pour les fonctions locales dans un fichier afin de référencer les variables locales dans une autre.</span><span class="sxs-lookup"><span data-stu-id="fda7c-127">This causes it to be legal for local functions in one file to reference local variables in another.</span></span> <span data-ttu-id="fda7c-128">L’ordre des contributions d’instruction (qui seraient toutes des fonctions locales) à partir d’autres unités de compilation n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="fda7c-128">The order of statement contributions (which would all be local functions) from other compilation units is undefined.</span></span>

<span data-ttu-id="fda7c-129">Les opérations asynchrones sont autorisées dans les instructions de niveau supérieur jusqu’à ce qu’elles soient autorisées dans les instructions au sein d’une méthode de point d’entrée Async standard.</span><span class="sxs-lookup"><span data-stu-id="fda7c-129">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="fda7c-130">Toutefois, elles ne sont pas requises, si `await` expressions et d’autres opérations asynchrones sont omises, aucun avertissement n’est généré.</span><span class="sxs-lookup"><span data-stu-id="fda7c-130">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span> <span data-ttu-id="fda7c-131">Au lieu de cela, la signature de la méthode de point d’entrée générée est équivalente à</span><span class="sxs-lookup"><span data-stu-id="fda7c-131">Instead the signature of the generated entry point method is equivalent to</span></span> 
``` c#
    static void Main()
```

<span data-ttu-id="fda7c-132">L’exemple ci-dessus produirait la déclaration de méthode `$Main` suivante :</span><span class="sxs-lookup"><span data-stu-id="fda7c-132">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main()
    {
        // Statements from File 1
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        // Local functions from File 2
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

<span data-ttu-id="fda7c-133">En même temps, un exemple semblable à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="fda7c-133">At the same time an example like this:</span></span>
``` c#
// File 1
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="fda7c-134">donne :</span><span class="sxs-lookup"><span data-stu-id="fda7c-134">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main()
    {
        // Statements from File 1
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="fda7c-135">Portée des variables locales de niveau supérieur et des fonctions locales</span><span class="sxs-lookup"><span data-stu-id="fda7c-135">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="fda7c-136">Même si les variables locales de niveau supérieur et les fonctions sont « encapsulées » dans la méthode de point d’entrée générée, elles doivent toujours être dans la portée tout au long du programme.</span><span class="sxs-lookup"><span data-stu-id="fda7c-136">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program.</span></span>
<span data-ttu-id="fda7c-137">Dans le cadre de l’évaluation de nom simple, une fois l’espace de noms global atteint :</span><span class="sxs-lookup"><span data-stu-id="fda7c-137">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="fda7c-138">Tout d’abord, une tentative est faite pour évaluer le nom dans la méthode de point d’entrée générée et uniquement si cette tentative échoue</span><span class="sxs-lookup"><span data-stu-id="fda7c-138">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="fda7c-139">L’évaluation « normale » dans la déclaration d’espace de noms globale est effectuée.</span><span class="sxs-lookup"><span data-stu-id="fda7c-139">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="fda7c-140">Cela peut entraîner l’occultation de nom des espaces de noms et des types déclarés dans l’espace de noms global, ainsi que l’occultation de noms importés.</span><span class="sxs-lookup"><span data-stu-id="fda7c-140">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="fda7c-141">Si l’évaluation de nom simple se produit en dehors des instructions de niveau supérieur et que l’évaluation génère une variable locale ou une fonction de niveau supérieur, cela devrait entraîner une erreur.</span><span class="sxs-lookup"><span data-stu-id="fda7c-141">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="fda7c-142">De cette façon, nous protégeons notre future capacité à mieux répondre aux « fonctions de niveau supérieur » (scénario 2 dans https://github.com/dotnet/csharplang/issues/3117), et pouvons fournir des diagnostics utiles aux utilisateurs qui pensent par erreur qu’ils sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fda7c-142">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

