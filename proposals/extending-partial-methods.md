---
ms.openlocfilehash: 04dca01bad04d5c53aa1c7c876343fb7ef33d2fa
ms.sourcegitcommit: 5c7cc619214ade6a8f3a0caddfb4862635f5241d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82021929"
---
<a name="extending-partial-methods"></a><span data-ttu-id="6f539-101">Extension des méthodes partielles</span><span class="sxs-lookup"><span data-stu-id="6f539-101">Extending Partial Methods</span></span>
=====

## <a name="summary"></a><span data-ttu-id="6f539-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="6f539-102">Summary</span></span>
<span data-ttu-id="6f539-103">Cette proposition vise à supprimer toutes `partial` les restrictions concernant les signatures de méthodes en C.</span><span class="sxs-lookup"><span data-stu-id="6f539-103">This proposal aims to remove all restrictions around the signatures of `partial` methods in C#.</span></span> <span data-ttu-id="6f539-104">L’objectif étant d’élargir l’ensemble de scénarios dans lesquels ces méthodes peuvent fonctionner avec les générateurs de sources ainsi que d’être un formulaire de déclaration plus générale pour les méthodes C.</span><span class="sxs-lookup"><span data-stu-id="6f539-104">The goal being to expand the set of scenarios in which these methods can work with source generators as well as being a more general declaration form for C# methods.</span></span>

## <a name="motivation"></a><span data-ttu-id="6f539-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="6f539-105">Motivation</span></span>
<span data-ttu-id="6f539-106">Le support limité des développeurs pour les méthodes de fractionnement en déclarations, définitions/implémentations.</span><span class="sxs-lookup"><span data-stu-id="6f539-106">C# has limited support for developers splitting methods into declarations and definitions / implementations.</span></span> 

```cs 
partial class C
{
    // The declaration of C.M
    partial void M(string message);
}

partial class C
{
    // The definition of C.M
    partial void M(string message) => Console.WriteLine(message);
}
```

<span data-ttu-id="6f539-107">Un comportement `partial` des méthodes est que lorsque la définition est absente, alors la langue effacera simplement tous les appels à la `partial` méthode.</span><span class="sxs-lookup"><span data-stu-id="6f539-107">One behavior of `partial` methods is that when the definition is absent then the language will simply erase any calls to the `partial` method.</span></span> <span data-ttu-id="6f539-108">Essentiellement, il se comporte `[Conditional]` comme un appel à une méthode où la condition a été évaluée à faux.</span><span class="sxs-lookup"><span data-stu-id="6f539-108">Essentially it behaves like a call to a `[Conditional]` method where the condition was evaluated to false.</span></span> 

```cs
partial class D
{
    partial void M(string message);

    void Example()
    {
        M(GetIt()); // Call to M and GetIt erased at compile time
    }

    string GetIt() => "Hello World";
}
```

<span data-ttu-id="6f539-109">La motivation originale pour cette fonctionnalité était la génération source sous la forme de code généré par le concepteur.</span><span class="sxs-lookup"><span data-stu-id="6f539-109">The original motivation for this feature was source generation in the form of designer generated code.</span></span> <span data-ttu-id="6f539-110">Les utilisateurs éditaient constamment le code généré parce qu’ils voulaient brancher un aspect du code généré.</span><span class="sxs-lookup"><span data-stu-id="6f539-110">Users were constantly editing the generated code because they wanted to hook some aspect of the generated code.</span></span> <span data-ttu-id="6f539-111">Plus particulièrement des parties du processus de démarrage de Windows Forms, après l’initialisation des composants.</span><span class="sxs-lookup"><span data-stu-id="6f539-111">Most notably parts of the Windows Forms startup process, after components were initialized.</span></span>

<span data-ttu-id="6f539-112">L’édition du code généré était sujette aux erreurs parce que toute action qui a amené le concepteur à régénérer le code entraînerait l’effacement de l’éditeur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6f539-112">Editing the generated code was error prone because any action which caused the designer to regenerate the code would cause the user edit to be erased.</span></span> <span data-ttu-id="6f539-113">La `partial` fonction de méthode a atténué cette tension car elle `partial` a permis aux concepteurs d’émettre des crochets sous forme de méthodes.</span><span class="sxs-lookup"><span data-stu-id="6f539-113">The `partial` method feature eased this tension because it allowed designers to emit hooks in the form of `partial` methods.</span></span> 

<span data-ttu-id="6f539-114">Les concepteurs pourraient `partial void OnComponentInit()` émettre des crochets comme et les développeurs pourraient définir des déclarations pour eux ou ne pas les définir.</span><span class="sxs-lookup"><span data-stu-id="6f539-114">Designers could emit hooks like `partial void OnComponentInit()` and developers could define declarations for them or not define them.</span></span> <span data-ttu-id="6f539-115">Dans les deux cas, bien que le code généré serait compiler et les développeurs qui étaient intéressés par le processus pourrait s’accrocher au besoin.</span><span class="sxs-lookup"><span data-stu-id="6f539-115">In either case though the generated code would compile and developers who were interested in the process could hook in as needed.</span></span> 

<span data-ttu-id="6f539-116">Cela signifie que les méthodes partielles ont plusieurs restrictions:</span><span class="sxs-lookup"><span data-stu-id="6f539-116">This does mean that partial methods have several restrictions:</span></span>

1. <span data-ttu-id="6f539-117">Doit avoir `void` un type de retour.</span><span class="sxs-lookup"><span data-stu-id="6f539-117">Must have a `void` return type.</span></span>
1. <span data-ttu-id="6f539-118">Impossible `ref` d’avoir ou `out` de paramètres.</span><span class="sxs-lookup"><span data-stu-id="6f539-118">Cannot have `ref` or `out` parameters.</span></span> 
1. <span data-ttu-id="6f539-119">Impossible d’avoir d’accessibilité (implicitement `private`).</span><span class="sxs-lookup"><span data-stu-id="6f539-119">Cannot have any accessibility (implicitly `private`).</span></span>

<span data-ttu-id="6f539-120">Ces restrictions existent parce que la langue doit être en mesure d’émettre du code lorsque le site d’appel est effacé.</span><span class="sxs-lookup"><span data-stu-id="6f539-120">These restrictions exist because the language must be able to emit code when the call site is erased.</span></span> <span data-ttu-id="6f539-121">Étant donné qu’ils peuvent être effacés `private` est la seule accessibilité possible parce que le membre ne peut pas être exposé dans les métadonnées d’assemblage.</span><span class="sxs-lookup"><span data-stu-id="6f539-121">Given they can be erased `private` is the only possible accessibility because the member can't be exposed in assembly metadata.</span></span> <span data-ttu-id="6f539-122">Ces restrictions servent également à limiter l’ensemble de scénarios dans lesquels `partial` des méthodes peuvent être appliquées.</span><span class="sxs-lookup"><span data-stu-id="6f539-122">These restrictions also serve to limit the set of scenarios in which `partial` methods can be applied.</span></span>

<span data-ttu-id="6f539-123">La proposition ici est de supprimer `partial` toutes les restrictions existantes concernant les méthodes.</span><span class="sxs-lookup"><span data-stu-id="6f539-123">The proposal here is to remove all of the existing restrictions around `partial` methods.</span></span> <span data-ttu-id="6f539-124">Essentiellement, laissez-les avoir, `out`les types de retour non nuls ou tout autre type d’accessibilité.</span><span class="sxs-lookup"><span data-stu-id="6f539-124">Essentially let them have `out`, non-void return types or any type of accessibility.</span></span> <span data-ttu-id="6f539-125">De `partial` telles déclarations auraient alors l’exigence supplémentaire qu’une définition doit exister.</span><span class="sxs-lookup"><span data-stu-id="6f539-125">Such `partial` declarations would then have the added requirement that a definition must exist.</span></span> <span data-ttu-id="6f539-126">Cela signifie que la langue n’a pas à tenir compte de l’impact de l’effacement des sites d’appels.</span><span class="sxs-lookup"><span data-stu-id="6f539-126">That means the language does not have to consider the impact of erasing the call sites.</span></span> 

<span data-ttu-id="6f539-127">Cela élargirait l’ensemble des `partial` scénarios de générateurs que les méthodes pourraient participer et donc relier bien avec notre fonction de générateurs sources.</span><span class="sxs-lookup"><span data-stu-id="6f539-127">This would expand the set of generator scenarios that `partial` methods could participate in and hence link in nicely with our source generators feature.</span></span> <span data-ttu-id="6f539-128">Par exemple, un regex peut être défini à l’aide du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="6f539-128">For example a regex could be defined using the following pattern:</span></span>

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

<span data-ttu-id="6f539-129">Cela donne à la fois au développeur une façon simple déclarative d’opter pour les générateurs ainsi que de donner aux générateurs un ensemble très facile de déclarations à regarder à travers dans le code source pour conduire leur sortie générée.</span><span class="sxs-lookup"><span data-stu-id="6f539-129">This gives both the developer a simple declarative way of opting into generators as well as giving generators a very easy set of declarations to look through in the source code to drive their generated output.</span></span> 

<span data-ttu-id="6f539-130">Comparez cela avec la difficulté qu’un générateur aurait brancher l’extrait de code suivant.</span><span class="sxs-lookup"><span data-stu-id="6f539-130">Compare that with the difficulty that a generator would have hooking up the following snippet of code.</span></span> 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

<span data-ttu-id="6f539-131">Étant donné que le compilateur ne permet pas aux générateurs de modifier le code branchant ce modèle serait à peu près impossible pour les générateurs.</span><span class="sxs-lookup"><span data-stu-id="6f539-131">Given that the compiler doesn't allow generators to modify code hooking up this pattern would be pretty much impossible for generators.</span></span> <span data-ttu-id="6f539-132">Ils auraient besoin de recourir `IsMatch` à la réflexion dans la mise en œuvre, ou de demander aux utilisateurs de changer leurs sites d’appel à une nouvelle méthode - refactor le regex pour passer la chaîne littérale comme un argument.</span><span class="sxs-lookup"><span data-stu-id="6f539-132">They would need to resort to reflection in the `IsMatch` implementation, or asking users to change their call sites to a new method + refactor the regex to pass the string literal as an argument.</span></span> <span data-ttu-id="6f539-133">C’est assez désordonné.</span><span class="sxs-lookup"><span data-stu-id="6f539-133">It's pretty messy.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="6f539-134">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="6f539-134">Detailed Design</span></span>
<span data-ttu-id="6f539-135">Le langage changera `partial` pour permettre aux méthodes d’être annotées avec un modificateur explicite d’accessibilité.</span><span class="sxs-lookup"><span data-stu-id="6f539-135">The language will change to allow `partial` methods to be annotated with an explicit accessibility modifier.</span></span> <span data-ttu-id="6f539-136">Cela signifie qu’ils `private`peuvent `public`être étiquetés comme , , etc ...</span><span class="sxs-lookup"><span data-stu-id="6f539-136">This means they can be labeled as `private`, `public`, etc ...</span></span> 

<span data-ttu-id="6f539-137">Lorsqu’une `partial` méthode a un modificateur explicite de l’accessibilité, bien que `private`le libellé exige que la déclaration ait une définition correspondante même lorsque l’accessibilité est :</span><span class="sxs-lookup"><span data-stu-id="6f539-137">When a `partial` method has an explicit accessibility modifier though the language will require that the declaration has a matching definition even when the accessibility is `private`:</span></span>

```cs
partial class C
{
    // Okay because no definition is required here
    partial void M1();

    // Okay because M2 has a definition
    private partial void M2();

    // Error: partial method M3 must have a definition
    private partial void M3();
}

partial class C
{
    private partial void M2() { }
}
```

<span data-ttu-id="6f539-138">En outre, le langage supprimera toutes `partial` les restrictions sur ce qui peut apparaître sur une méthode qui a une accessibilité explicite.</span><span class="sxs-lookup"><span data-stu-id="6f539-138">Further the language will remove all restrictions on what can appear on a `partial` method which has an explicit accessibility.</span></span> <span data-ttu-id="6f539-139">Ces déclarations peuvent contenir des `ref` types `out` de `extern` retour non nuls, ou paramètres, modificateur, etc ... Ces signatures auront toute l’expressivité de la langue C.</span><span class="sxs-lookup"><span data-stu-id="6f539-139">Such declarations can contain non-void return types, `ref` or `out` parameters, `extern` modifier, etc ... These signatures will have the full expressivity of the C# language.</span></span>

```cs
partial class D
{
    // Okay
    internal partial bool TryParse(string s, out int i); 
}

partial class D
{
    internal partial bool TryParse(string s, out int i) { }
}
```

<span data-ttu-id="6f539-140">Cela permet `partial` explicitement des `overrides` méthodes `interface` de participation et de mise en œuvre :</span><span class="sxs-lookup"><span data-stu-id="6f539-140">This explicitly allows for `partial` methods to participate in `overrides` and `interface` implementations:</span></span>

```cs
interface IStudent
{
    string GetName();
}

partial class C : IStudent
{
    public virtual partial string GetName(); 
}

partial class C
{
    public virtual partial string GetName() => "Jarde";
}
```

<span data-ttu-id="6f539-141">Le compilateur va changer l’erreur `partial` qu’il émet quand une méthode contient un élément illégal pour dire essentiellement:</span><span class="sxs-lookup"><span data-stu-id="6f539-141">The compiler will change the error it emits when a `partial` method contains an illegal element to essentially say:</span></span>

> <span data-ttu-id="6f539-142">Impossible `ref` d’utiliser sur une `partial` méthode qui manque d’accessibilité explicite</span><span class="sxs-lookup"><span data-stu-id="6f539-142">Cannot use `ref` on a `partial` method that lacks explicit accessibility</span></span> 

<span data-ttu-id="6f539-143">Cela aidera les développeurs à pointer dans la bonne direction lors de l’utilisation de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="6f539-143">This will help point developers in the right direction when using this feature.</span></span>

<span data-ttu-id="6f539-144">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="6f539-144">Restrictions:</span></span>
- <span data-ttu-id="6f539-145">`partial`déclarations avec accessibilité explicite doivent avoir une définition</span><span class="sxs-lookup"><span data-stu-id="6f539-145">`partial` declarations with explicit accessibility must have a definition</span></span>
- <span data-ttu-id="6f539-146">`partial`les déclarations et les signatures de définition doivent correspondre à toutes les méthodes et les modificateurs de paramètres.</span><span class="sxs-lookup"><span data-stu-id="6f539-146">`partial` declarations and definition signatures must match on all method and parameter modifiers.</span></span> <span data-ttu-id="6f539-147">Les seuls aspects qui peuvent différer sont les noms de paramètres `partial` et les listes d’attributs (ce n’est pas nouveau, mais plutôt une exigence existante de méthodes).</span><span class="sxs-lookup"><span data-stu-id="6f539-147">The only aspects which can differ are parameter names and attribute lists (this is not new but rather an existing requirement of `partial` methods).</span></span>

## <a name="questions"></a><span data-ttu-id="6f539-148">Questions</span><span class="sxs-lookup"><span data-stu-id="6f539-148">Questions</span></span>

### <a name="partial-on-all-members"></a><span data-ttu-id="6f539-149">partielle sur tous les membres</span><span class="sxs-lookup"><span data-stu-id="6f539-149">partial on all members</span></span>
<span data-ttu-id="6f539-150">Étant donné que `partial` nous sommes en expansion pour être plus amical pour les générateurs source devrions-nous également l’étendre pour travailler sur tous les membres de la classe?</span><span class="sxs-lookup"><span data-stu-id="6f539-150">Given that we're expanding `partial` to be more friendly to source generators should we also expand it to work on all class members?</span></span> <span data-ttu-id="6f539-151">Par exemple, devrions-nous `partial` être en mesure de déclarer les constructeurs, les opérateurs, etc ...</span><span class="sxs-lookup"><span data-stu-id="6f539-151">For example should we be able to declare `partial` constructors, operators, etc ...</span></span>

<span data-ttu-id="6f539-152">**Résolution** L’idée est saine, mais à ce stade de l’horaire C 9, nous essayons d’éviter le fluage des fonctionnalités inutiles.</span><span class="sxs-lookup"><span data-stu-id="6f539-152">**Resolution** The idea is sound but at this point in the C# 9 schedule we're trying to avoid unnecessary feature creep.</span></span> <span data-ttu-id="6f539-153">Vous voulez résoudre le problème immédiat de l’expansion de la fonctionnalité pour travailler avec les générateurs sources modernes.</span><span class="sxs-lookup"><span data-stu-id="6f539-153">Want to solve the immediate problem of expanding the feature to work with modern source generators.</span></span> 

<span data-ttu-id="6f539-154">L’extension `partial` à l’appui d’autres membres sera envisagée pour la version C 10.</span><span class="sxs-lookup"><span data-stu-id="6f539-154">Extending `partial` to support other members will be considered for the C# 10 release.</span></span> <span data-ttu-id="6f539-155">Il semble probable que nous envisagerons cette prolongation.</span><span class="sxs-lookup"><span data-stu-id="6f539-155">Seems likely that we will consider this extension.</span></span>

### <a name="use-abstract-instead-of-partial"></a><span data-ttu-id="6f539-156">Utiliser abstrait au lieu de partielle</span><span class="sxs-lookup"><span data-stu-id="6f539-156">Use abstract instead of partial</span></span>
<span data-ttu-id="6f539-157">Le nœud de cette proposition est essentiellement de s’assurer qu’une déclaration a une définition correspondante / mise en œuvre.</span><span class="sxs-lookup"><span data-stu-id="6f539-157">The crux of this proposal is essentially ensuring that a declaration has a corresponding definition / implementation.</span></span> <span data-ttu-id="6f539-158">Étant donné que `abstract` devrions-nous utiliser car c’est déjà un mot clé de langue qui oblige le développeur à penser à avoir une implémentation?</span><span class="sxs-lookup"><span data-stu-id="6f539-158">Given that should we use `abstract` since it's already a language keyword that forces the developer to think about having an implementation?</span></span>

<span data-ttu-id="6f539-159">**Résolution** Il y a eu une discussion saine à ce sujet, mais finalement il a été décidé contre.</span><span class="sxs-lookup"><span data-stu-id="6f539-159">**Resolution** There was a healthy discussion about this but eventually it was decided against.</span></span>
<span data-ttu-id="6f539-160">Oui, les exigences sont familières, mais les concepts sont très différents.</span><span class="sxs-lookup"><span data-stu-id="6f539-160">Yes the requirements are familiar but the concepts are significantly different.</span></span>
<span data-ttu-id="6f539-161">Pourrait facilement conduire le développeur à croire qu’ils étaient la création de fentes virtuelles quand ils ne le faisaient pas.</span><span class="sxs-lookup"><span data-stu-id="6f539-161">Could easily lead the developer to believe they were creating virtual slots when they were not doing so.</span></span>
