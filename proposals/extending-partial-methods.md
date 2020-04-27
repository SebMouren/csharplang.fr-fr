---
ms.openlocfilehash: 6fbc866af9971d86a287b026013e235e5b25fc21
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162072"
---
<a name="extending-partial-methods"></a><span data-ttu-id="e4219-101">Étendre des méthodes partielles</span><span class="sxs-lookup"><span data-stu-id="e4219-101">Extending Partial Methods</span></span>
=====

## <a name="summary"></a><span data-ttu-id="e4219-102">Récapitulatif</span><span class="sxs-lookup"><span data-stu-id="e4219-102">Summary</span></span>
<span data-ttu-id="e4219-103">Cette proposition vise à supprimer toutes les restrictions concernant les signatures `partial` des méthodes en C#.</span><span class="sxs-lookup"><span data-stu-id="e4219-103">This proposal aims to remove all restrictions around the signatures of `partial` methods in C#.</span></span> <span data-ttu-id="e4219-104">L’objectif est de développer l’ensemble des scénarios dans lesquels ces méthodes peuvent fonctionner avec les générateurs de code source, ainsi qu’une forme de déclaration plus générale pour les méthodes C#.</span><span class="sxs-lookup"><span data-stu-id="e4219-104">The goal being to expand the set of scenarios in which these methods can work with source generators as well as being a more general declaration form for C# methods.</span></span>

<span data-ttu-id="e4219-105">Consultez également la [spécification des méthodes partielles d’origine](/spec/classes.md#partial-methods).</span><span class="sxs-lookup"><span data-stu-id="e4219-105">See also the [original partial methods specification](/spec/classes.md#partial-methods).</span></span>

## <a name="motivation"></a><span data-ttu-id="e4219-106">Motivation</span><span class="sxs-lookup"><span data-stu-id="e4219-106">Motivation</span></span>
<span data-ttu-id="e4219-107">C# offre une prise en charge limitée pour les développeurs qui fractionnent les méthodes en déclarations et définitions/implémentations.</span><span class="sxs-lookup"><span data-stu-id="e4219-107">C# has limited support for developers splitting methods into declarations and definitions / implementations.</span></span> 

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

<span data-ttu-id="e4219-108">L’un des `partial` comportements des méthodes est que lorsque la définition est absente, le langage efface simplement `partial` tous les appels à la méthode.</span><span class="sxs-lookup"><span data-stu-id="e4219-108">One behavior of `partial` methods is that when the definition is absent then the language will simply erase any calls to the `partial` method.</span></span> <span data-ttu-id="e4219-109">Il se comporte essentiellement comme un appel à une `[Conditional]` méthode dans laquelle la condition a été évaluée à false.</span><span class="sxs-lookup"><span data-stu-id="e4219-109">Essentially it behaves like a call to a `[Conditional]` method where the condition was evaluated to false.</span></span> 

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

<span data-ttu-id="e4219-110">La motivation initiale de cette fonctionnalité était la génération de la source sous la forme d’un code généré par le concepteur.</span><span class="sxs-lookup"><span data-stu-id="e4219-110">The original motivation for this feature was source generation in the form of designer generated code.</span></span> <span data-ttu-id="e4219-111">Les utilisateurs modifient en permanence le code généré parce qu’ils souhaitaient raccorder certains aspects du code généré.</span><span class="sxs-lookup"><span data-stu-id="e4219-111">Users were constantly editing the generated code because they wanted to hook some aspect of the generated code.</span></span> <span data-ttu-id="e4219-112">Plus particulièrement les parties du processus de démarrage Windows Forms, après l’initialisation des composants.</span><span class="sxs-lookup"><span data-stu-id="e4219-112">Most notably parts of the Windows Forms startup process, after components were initialized.</span></span>

<span data-ttu-id="e4219-113">La modification du code généré était sujette aux erreurs, car toute action ayant entraîné la régénération du code par le concepteur entraînerait l’effacement de la modification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e4219-113">Editing the generated code was error prone because any action which caused the designer to regenerate the code would cause the user edit to be erased.</span></span> <span data-ttu-id="e4219-114">La `partial` fonctionnalité de méthode a facilité la traction, car elle a permis aux concepteurs d' `partial` émettre des raccordements sous la forme de méthodes.</span><span class="sxs-lookup"><span data-stu-id="e4219-114">The `partial` method feature eased this tension because it allowed designers to emit hooks in the form of `partial` methods.</span></span> 

<span data-ttu-id="e4219-115">Les concepteurs peuvent émettre `partial void OnComponentInit()` des points de raccordement comme et les développeurs peuvent définir des déclarations pour eux ou ne pas les définir.</span><span class="sxs-lookup"><span data-stu-id="e4219-115">Designers could emit hooks like `partial void OnComponentInit()` and developers could define declarations for them or not define them.</span></span> <span data-ttu-id="e4219-116">Dans les deux cas, même si le code généré est compilé et que les développeurs intéressés par le processus pouvaient se raccorder en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="e4219-116">In either case though the generated code would compile and developers who were interested in the process could hook in as needed.</span></span> 

<span data-ttu-id="e4219-117">Cela signifie que les méthodes partielles ont plusieurs restrictions :</span><span class="sxs-lookup"><span data-stu-id="e4219-117">This does mean that partial methods have several restrictions:</span></span>

1. <span data-ttu-id="e4219-118">Doit avoir un `void` type de retour.</span><span class="sxs-lookup"><span data-stu-id="e4219-118">Must have a `void` return type.</span></span>
1. <span data-ttu-id="e4219-119">Impossible d' `out` avoir des paramètres.</span><span class="sxs-lookup"><span data-stu-id="e4219-119">Cannot have `out` parameters.</span></span> 
1. <span data-ttu-id="e4219-120">Impossible d’avoir une accessibilité (implicitement `private`).</span><span class="sxs-lookup"><span data-stu-id="e4219-120">Cannot have any accessibility (implicitly `private`).</span></span>

<span data-ttu-id="e4219-121">Ces restrictions existent, car la langue doit être en mesure d’émettre du code lorsque le site d’appel est effacé.</span><span class="sxs-lookup"><span data-stu-id="e4219-121">These restrictions exist because the language must be able to emit code when the call site is erased.</span></span> <span data-ttu-id="e4219-122">Étant donné qu’ils peuvent être `private` effacés, la seule accessibilité possible est que le membre ne peut pas être exposé dans les métadonnées de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="e4219-122">Given they can be erased `private` is the only possible accessibility because the member can't be exposed in assembly metadata.</span></span> <span data-ttu-id="e4219-123">Ces restrictions servent également à limiter l’ensemble des scénarios dans lesquels `partial` les méthodes peuvent être appliquées.</span><span class="sxs-lookup"><span data-stu-id="e4219-123">These restrictions also serve to limit the set of scenarios in which `partial` methods can be applied.</span></span>

<span data-ttu-id="e4219-124">La proposition ici consiste à supprimer toutes les restrictions existantes autour `partial` des méthodes.</span><span class="sxs-lookup"><span data-stu-id="e4219-124">The proposal here is to remove all of the existing restrictions around `partial` methods.</span></span> <span data-ttu-id="e4219-125">Ils les laissent `out`, les types de retour non void ou n’importe quel type d’accessibilité.</span><span class="sxs-lookup"><span data-stu-id="e4219-125">Essentially let them have `out`, non-void return types or any type of accessibility.</span></span> <span data-ttu-id="e4219-126">Ces `partial` déclarations auront alors la nécessité ajoutée qu’une définition doit exister.</span><span class="sxs-lookup"><span data-stu-id="e4219-126">Such `partial` declarations would then have the added requirement that a definition must exist.</span></span> <span data-ttu-id="e4219-127">Cela signifie que le langage n’a pas à prendre en compte l’impact de l’effacement des sites d’appel.</span><span class="sxs-lookup"><span data-stu-id="e4219-127">That means the language does not have to consider the impact of erasing the call sites.</span></span> 

<span data-ttu-id="e4219-128">Cela développerait l’ensemble des scénarios de générateur `partial` auxquels les méthodes pourraient participer et, par conséquent, nous permettons de créer une liaison avec notre fonctionnalité de générateurs de source.</span><span class="sxs-lookup"><span data-stu-id="e4219-128">This would expand the set of generator scenarios that `partial` methods could participate in and hence link in nicely with our source generators feature.</span></span> <span data-ttu-id="e4219-129">Par exemple, une expression régulière peut être définie à l’aide du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="e4219-129">For example a regex could be defined using the following pattern:</span></span>

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

<span data-ttu-id="e4219-130">Cela donne aux développeurs un moyen déclaratif simple d’opter pour les générateurs et de donner aux générateurs un ensemble très facile de déclarations à examiner dans le code source pour piloter leur sortie générée.</span><span class="sxs-lookup"><span data-stu-id="e4219-130">This gives both the developer a simple declarative way of opting into generators as well as giving generators a very easy set of declarations to look through in the source code to drive their generated output.</span></span> 

<span data-ttu-id="e4219-131">Comparez cela à la difficulté qu’un générateur aurait raccordé à l’extrait de code suivant.</span><span class="sxs-lookup"><span data-stu-id="e4219-131">Compare that with the difficulty that a generator would have hooking up the following snippet of code.</span></span> 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

<span data-ttu-id="e4219-132">Étant donné que le compilateur n’autorise pas les générateurs à modifier le raccordement de code, ce modèle serait beaucoup impossible pour les générateurs.</span><span class="sxs-lookup"><span data-stu-id="e4219-132">Given that the compiler doesn't allow generators to modify code hooking up this pattern would be pretty much impossible for generators.</span></span> <span data-ttu-id="e4219-133">Ils doivent recourir à la réflexion dans l' `IsMatch` implémentation ou demander aux utilisateurs de remplacer leurs sites d’appel par une nouvelle méthode + refactoriser l’expression régulière pour passer le littéral de chaîne en tant qu’argument.</span><span class="sxs-lookup"><span data-stu-id="e4219-133">They would need to resort to reflection in the `IsMatch` implementation, or asking users to change their call sites to a new method + refactor the regex to pass the string literal as an argument.</span></span> <span data-ttu-id="e4219-134">C’est assez compliqué.</span><span class="sxs-lookup"><span data-stu-id="e4219-134">It's pretty messy.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="e4219-135">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="e4219-135">Detailed Design</span></span>
<span data-ttu-id="e4219-136">Le langage change pour permettre `partial` l’annotation des méthodes avec un modificateur d’accessibilité explicite.</span><span class="sxs-lookup"><span data-stu-id="e4219-136">The language will change to allow `partial` methods to be annotated with an explicit accessibility modifier.</span></span> <span data-ttu-id="e4219-137">Cela signifie qu’ils peuvent être `private`étiquetés `public`comme,, etc.</span><span class="sxs-lookup"><span data-stu-id="e4219-137">This means they can be labeled as `private`, `public`, etc ...</span></span> 

<span data-ttu-id="e4219-138">Quand une `partial` méthode a un modificateur d’accessibilité explicite, bien que le langage exige que la déclaration ait une définition correspondante même lorsque l' `private`accessibilité est :</span><span class="sxs-lookup"><span data-stu-id="e4219-138">When a `partial` method has an explicit accessibility modifier though the language will require that the declaration has a matching definition even when the accessibility is `private`:</span></span>

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

<span data-ttu-id="e4219-139">En outre, le langage supprimera toutes les restrictions sur ce qui `partial` peut apparaître sur une méthode qui a une accessibilité explicite.</span><span class="sxs-lookup"><span data-stu-id="e4219-139">Further the language will remove all restrictions on what can appear on a `partial` method which has an explicit accessibility.</span></span> <span data-ttu-id="e4219-140">Ces déclarations peuvent contenir des types de retour non void `out` , des `extern` paramètres, des modificateurs, etc. Ces signatures auront l’intégralité de l’expression du langage C#.</span><span class="sxs-lookup"><span data-stu-id="e4219-140">Such declarations can contain non-void return types, `out` parameters, `extern` modifier, etc ... These signatures will have the full expressivity of the C# language.</span></span>

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

<span data-ttu-id="e4219-141">Cela permet explicitement aux `partial` méthodes de participer aux `overrides` implémentations et `interface` :</span><span class="sxs-lookup"><span data-stu-id="e4219-141">This explicitly allows for `partial` methods to participate in `overrides` and `interface` implementations:</span></span>

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

<span data-ttu-id="e4219-142">Le compilateur modifie l’erreur qu’il émet lorsqu’une `partial` méthode contient un élément illégal pour dire :</span><span class="sxs-lookup"><span data-stu-id="e4219-142">The compiler will change the error it emits when a `partial` method contains an illegal element to essentially say:</span></span>

> <span data-ttu-id="e4219-143">Impossible d' `ref` utiliser sur `partial` une méthode qui n’a pas d’accessibilité explicite</span><span class="sxs-lookup"><span data-stu-id="e4219-143">Cannot use `ref` on a `partial` method that lacks explicit accessibility</span></span> 

<span data-ttu-id="e4219-144">Cela aidera les développeurs dans le sens adapté à l’utilisation de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="e4219-144">This will help point developers in the right direction when using this feature.</span></span>

<span data-ttu-id="e4219-145">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="e4219-145">Restrictions:</span></span>
- <span data-ttu-id="e4219-146">`partial`les déclarations avec accessibilité explicite doivent avoir une définition</span><span class="sxs-lookup"><span data-stu-id="e4219-146">`partial` declarations with explicit accessibility must have a definition</span></span>
- <span data-ttu-id="e4219-147">`partial`les déclarations et les signatures de définition doivent correspondre sur tous les modificateurs de méthode et de paramètre.</span><span class="sxs-lookup"><span data-stu-id="e4219-147">`partial` declarations and definition signatures must match on all method and parameter modifiers.</span></span> <span data-ttu-id="e4219-148">Les seuls aspects qui peuvent différer sont les noms de paramètres et les listes d’attributs (cela n’est pas nouveau `partial` , mais plutôt une exigence existante des méthodes).</span><span class="sxs-lookup"><span data-stu-id="e4219-148">The only aspects which can differ are parameter names and attribute lists (this is not new but rather an existing requirement of `partial` methods).</span></span>

## <a name="questions"></a><span data-ttu-id="e4219-149">Questions</span><span class="sxs-lookup"><span data-stu-id="e4219-149">Questions</span></span>

### <a name="partial-on-all-members"></a><span data-ttu-id="e4219-150">partielle sur tous les membres</span><span class="sxs-lookup"><span data-stu-id="e4219-150">partial on all members</span></span>
<span data-ttu-id="e4219-151">Étant donné que nous développons `partial` pour être plus conviviaux pour les générateurs de code source, devons-nous également le développer pour travailler sur tous les membres de classe ?</span><span class="sxs-lookup"><span data-stu-id="e4219-151">Given that we're expanding `partial` to be more friendly to source generators should we also expand it to work on all class members?</span></span> <span data-ttu-id="e4219-152">Par exemple, vous devriez pouvoir déclarer `partial` des constructeurs, des opérateurs, etc.</span><span class="sxs-lookup"><span data-stu-id="e4219-152">For example should we be able to declare `partial` constructors, operators, etc ...</span></span>

<span data-ttu-id="e4219-153">**Résolution** L’idée est un son, mais à ce stade de la planification C# 9, nous essayons d’éviter la compensation des fonctionnalités inutiles.</span><span class="sxs-lookup"><span data-stu-id="e4219-153">**Resolution** The idea is sound but at this point in the C# 9 schedule we're trying to avoid unnecessary feature creep.</span></span> <span data-ttu-id="e4219-154">Vous souhaitez résoudre le problème immédiat lié au développement de la fonctionnalité afin qu’elle fonctionne avec les générateurs de source modernes.</span><span class="sxs-lookup"><span data-stu-id="e4219-154">Want to solve the immediate problem of expanding the feature to work with modern source generators.</span></span> 

<span data-ttu-id="e4219-155">L' `partial` extension de pour prendre en charge d’autres membres sera prise en compte pour la version C# 10.</span><span class="sxs-lookup"><span data-stu-id="e4219-155">Extending `partial` to support other members will be considered for the C# 10 release.</span></span> <span data-ttu-id="e4219-156">Il semble que nous envisageons cette extension.</span><span class="sxs-lookup"><span data-stu-id="e4219-156">Seems likely that we will consider this extension.</span></span>

### <a name="use-abstract-instead-of-partial"></a><span data-ttu-id="e4219-157">Utiliser abstract au lieu de partial</span><span class="sxs-lookup"><span data-stu-id="e4219-157">Use abstract instead of partial</span></span>
<span data-ttu-id="e4219-158">Le essentiel de cette proposition consiste essentiellement à s’assurer qu’une déclaration a une définition/implémentation correspondante.</span><span class="sxs-lookup"><span data-stu-id="e4219-158">The crux of this proposal is essentially ensuring that a declaration has a corresponding definition / implementation.</span></span> <span data-ttu-id="e4219-159">Étant donné que nous devons `abstract` utiliser, car il s’agit déjà d’un mot clé de langage qui oblige le développeur à réfléchir à la mise en œuvre d’une implémentation ?</span><span class="sxs-lookup"><span data-stu-id="e4219-159">Given that should we use `abstract` since it's already a language keyword that forces the developer to think about having an implementation?</span></span>

<span data-ttu-id="e4219-160">**Résolution** Il y a eu une discussion saine sur ce sujet, mais il a finalement été décidé.</span><span class="sxs-lookup"><span data-stu-id="e4219-160">**Resolution** There was a healthy discussion about this but eventually it was decided against.</span></span>
<span data-ttu-id="e4219-161">Oui, les spécifications sont familières, mais les concepts sont très différents.</span><span class="sxs-lookup"><span data-stu-id="e4219-161">Yes the requirements are familiar but the concepts are significantly different.</span></span>
<span data-ttu-id="e4219-162">Peut permettre au développeur de croire qu’il a créé des emplacements virtuels alors qu’il ne l’a pas fait.</span><span class="sxs-lookup"><span data-stu-id="e4219-162">Could easily lead the developer to believe they were creating virtual slots when they were not doing so.</span></span>
