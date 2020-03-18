---
ms.openlocfilehash: 91afbc3e3412049cd183c36c8035f1862c520413
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485055"
---
# <a name="pattern-based-using-and-using-declarations"></a><span data-ttu-id="143e3-101">« utilisation basée sur des modèles » et « utilisation des déclarations »</span><span class="sxs-lookup"><span data-stu-id="143e3-101">"pattern-based using" and "using declarations"</span></span>

## <a name="summary"></a><span data-ttu-id="143e3-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="143e3-102">Summary</span></span>

<span data-ttu-id="143e3-103">Le langage ajoutera deux nouvelles fonctionnalités autour de l’instruction `using` afin de simplifier la gestion des ressources : `using` doit reconnaître un modèle jetable en plus de `IDisposable` et ajouter une déclaration de `using` à la langue.</span><span class="sxs-lookup"><span data-stu-id="143e3-103">The language will add two new capabilities around the `using` statement in order to make resource management simpler: `using` should recognize a disposable pattern in addition to `IDisposable` and add a `using` declaration to the language.</span></span>

## <a name="motivation"></a><span data-ttu-id="143e3-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="143e3-104">Motivation</span></span>

<span data-ttu-id="143e3-105">L’instruction `using` est un outil efficace pour la gestion des ressources, mais elle nécessite un peu de cérémonie.</span><span class="sxs-lookup"><span data-stu-id="143e3-105">The `using` statement is an effective tool for resource management today but it requires quite a bit of ceremony.</span></span> <span data-ttu-id="143e3-106">Les méthodes qui ont un certain nombre de ressources à gérer peuvent être facilement coincées dans une série d’instructions `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-106">Methods that have a number of resources to manage can get syntactically bogged down with a series of `using` statements.</span></span> <span data-ttu-id="143e3-107">Cette charge de syntaxe est suffisante pour que la plupart des règles de style de codage aient explicitement une exception autour des accolades pour ce scénario.</span><span class="sxs-lookup"><span data-stu-id="143e3-107">This syntax burden is enough that most coding style guidelines explicitly have an exception around braces for this scenario.</span></span> 

<span data-ttu-id="143e3-108">La déclaration de `using` supprime la plus grande partie de la C# cérémonie ici et s’obtient en même temps que d’autres langages qui incluent des blocs de gestion des ressources.</span><span class="sxs-lookup"><span data-stu-id="143e3-108">The `using` declaration removes much of the ceremony here and gets C# on par with other languages that include resource management blocks.</span></span> <span data-ttu-id="143e3-109">En outre, le `using` basé sur des modèles permet aux développeurs de développer l’ensemble des types qui peuvent participer ici.</span><span class="sxs-lookup"><span data-stu-id="143e3-109">Additionally the pattern-based `using` lets developers expand the set of types that can participate here.</span></span> <span data-ttu-id="143e3-110">Dans de nombreux cas, il est inutile de créer des types de wrappers qui n’existent que pour permettre l’utilisation d’une valeur dans une instruction `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-110">In many cases removing the need to create wrapper types that only exist to allow for a values use in a `using` statement.</span></span> 

<span data-ttu-id="143e3-111">Ensemble, ces fonctionnalités permettent aux développeurs de simplifier et d’étendre les scénarios dans lesquels les `using` peuvent être appliquées.</span><span class="sxs-lookup"><span data-stu-id="143e3-111">Together these features allow developers to simplify and expand the scenarios where `using` can be applied.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="143e3-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="143e3-112">Detailed Design</span></span> 

### <a name="using-declaration"></a><span data-ttu-id="143e3-113">déclaration using</span><span class="sxs-lookup"><span data-stu-id="143e3-113">using declaration</span></span>

<span data-ttu-id="143e3-114">Le langage permet d’ajouter des `using` à une déclaration de variable locale.</span><span class="sxs-lookup"><span data-stu-id="143e3-114">The language will allow for `using` to be added to a local variable declaration.</span></span> <span data-ttu-id="143e3-115">Une telle déclaration aura le même effet que la déclaration de la variable dans une instruction `using` au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="143e3-115">Such a declaration will have the same effect as declaring the variable in a `using` statement at the same location.</span></span>

```csharp
if (...) 
{ 
   using FileStream f = new FileStream(@"C:\users\jaredpar\using.md");
   // statements
}

// Equivalent to 
if (...) 
{ 
   using (FileStream f = new FileStream(@"C:\users\jaredpar\using.md")) 
   {
    // statements
   }
}
```

<span data-ttu-id="143e3-116">La durée de vie d’un `using` local s’étend jusqu’à la fin de l’étendue dans laquelle il est déclaré.</span><span class="sxs-lookup"><span data-stu-id="143e3-116">The lifetime of a `using` local will extend to the end of the scope in which it is declared.</span></span> <span data-ttu-id="143e3-117">Les variables locales `using` sont ensuite supprimées dans l’ordre inverse dans lequel elles sont déclarées.</span><span class="sxs-lookup"><span data-stu-id="143e3-117">The `using` locals will then be disposed in the reverse order in which they are declared.</span></span> 

```csharp
{ 
    using var f1 = new FileStream("...");
    using var f2 = new FileStream("..."), f3 = new FileStream("...");
    ...
    // Dispose f3
    // Dispose f2 
    // Dispose f1
}
```

<span data-ttu-id="143e3-118">Il n’existe aucune restriction concernant `goto`, ou toute autre construction de workflow de contrôle en face d’une déclaration `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-118">There are no restrictions around `goto`, or any other control flow construct in the face of a `using` declaration.</span></span> <span data-ttu-id="143e3-119">Au lieu de cela, le code agit comme pour l’instruction `using` équivalente :</span><span class="sxs-lookup"><span data-stu-id="143e3-119">Instead the code acts just as it would for the equivalent `using` statement:</span></span>

```csharp
{
    using var f1 = new FileStream("...");
  target:
    using var f2 = new FileStream("...");
    if (someCondition) 
    {
        // Causes f2 to be disposed but has no effect on f1
        goto target;
    }
}
```

<span data-ttu-id="143e3-120">Un local déclaré dans une déclaration locale `using` est implicitement en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="143e3-120">A local declared in a `using` local declaration will be implicitly read-only.</span></span> <span data-ttu-id="143e3-121">Cela correspond au comportement des variables locales déclarées dans une instruction `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-121">This matches the behavior of locals declared in a `using` statement.</span></span> 

<span data-ttu-id="143e3-122">La grammaire du langage pour les déclarations de `using` est la suivante :</span><span class="sxs-lookup"><span data-stu-id="143e3-122">The language grammar for `using` declarations will be the following:</span></span>

```antlr
local-using-declaration:
  using type using-declarators

using-declarators:
  using-declarator
  using-declarators , using-declarator
  
using-declarator:
  identifier = expression
```

<span data-ttu-id="143e3-123">Restrictions relatives à la déclaration de `using` :</span><span class="sxs-lookup"><span data-stu-id="143e3-123">Restrictions around `using` declaration:</span></span>

- <span data-ttu-id="143e3-124">Peut ne pas apparaître directement à l’intérieur d’une étiquette de `case`, mais au lieu d’être dans un bloc à l’intérieur de l’étiquette `case`.</span><span class="sxs-lookup"><span data-stu-id="143e3-124">May not appear directly inside a `case` label but instead must be within a block inside the `case` label.</span></span>
- <span data-ttu-id="143e3-125">Peut ne pas apparaître dans le cadre d’une déclaration de variable `out`.</span><span class="sxs-lookup"><span data-stu-id="143e3-125">May not appear as part of an `out` variable declaration.</span></span> 
- <span data-ttu-id="143e3-126">Doit avoir un initialiseur pour chaque déclarateur.</span><span class="sxs-lookup"><span data-stu-id="143e3-126">Must have an initializer for each declarator.</span></span>
- <span data-ttu-id="143e3-127">Le type local doit être implicitement convertible en `IDisposable` ou respecter le modèle de `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-127">The local type must be implicitly convertible to `IDisposable` or fulfill the `using` pattern.</span></span>

### <a name="pattern-based-using"></a><span data-ttu-id="143e3-128">utilisation basée sur des modèles</span><span class="sxs-lookup"><span data-stu-id="143e3-128">pattern-based using</span></span>

<span data-ttu-id="143e3-129">Le langage ajoutera la notion d’un modèle jetable : il s’agit d’un type qui a une méthode d’instance accessible `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="143e3-129">The language will add the notion of a disposable pattern: that is a type which has an accessible `Dispose` instance method.</span></span> <span data-ttu-id="143e3-130">Les types qui correspondent au modèle jetable peuvent participer à une instruction `using` ou à une déclaration sans qu’il soit nécessaire d’implémenter `IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="143e3-130">Types which fit the disposable pattern can participate in a `using` statement or declaration without being required to implement `IDisposable`.</span></span> 

```csharp
class Resource
{ 
    public void Dispose() { ... }
}

using (var r = new Resource())
{
    // statements
}
```

<span data-ttu-id="143e3-131">Cela permet aux développeurs de tirer parti de `using` dans un certain nombre de nouveaux scénarios :</span><span class="sxs-lookup"><span data-stu-id="143e3-131">This will allow developers to leverage `using` in a number of new scenarios:</span></span>

- <span data-ttu-id="143e3-132">`ref struct`: ces types ne peuvent pas implémenter d’interfaces aujourd’hui et ne peuvent donc pas participer aux instructions `using`.</span><span class="sxs-lookup"><span data-stu-id="143e3-132">`ref struct`: These types can't implement interfaces today and hence can't participate in `using` statements.</span></span>
- <span data-ttu-id="143e3-133">Les méthodes d’extension permettent aux développeurs d’ajouter des types dans d’autres assemblys pour participer à `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="143e3-133">Extension methods will allow developers to augment types in other assemblies to participate in `using` statements.</span></span>

<span data-ttu-id="143e3-134">Dans le cas où un type peut être implicitement converti en `IDisposable` et s’adapte également au modèle jetable, `IDisposable` sera préféré.</span><span class="sxs-lookup"><span data-stu-id="143e3-134">In the situation where a type can be implicitly converted to `IDisposable` and also fits the disposable pattern, then `IDisposable` will be preferred.</span></span> <span data-ttu-id="143e3-135">Bien que cela prenne l’approche opposée de `foreach` (modèle préféré par rapport à l’interface), elle est nécessaire à des fins de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="143e3-135">While this takes the opposite approach of `foreach` (pattern preferred over interface) it is necessary for backwards compatibility.</span></span>

<span data-ttu-id="143e3-136">Les mêmes restrictions qu’une instruction de `using` traditionnelle s’appliquent également ici : les variables locales déclarées dans le `using` sont en lecture seule, une valeur de `null` n’entraîne pas la levée d’une exception, etc. La génération de code sera différente uniquement en ce qu’il n’y aura pas de cast en `IDisposable` avant d’appeler dispose :</span><span class="sxs-lookup"><span data-stu-id="143e3-136">The same restrictions from a traditional `using` statement apply here as well: local variables declared in the `using` are read-only, a `null` value will not cause an exception to be thrown, etc ... The code generation will be different only in that there will not be a cast to `IDisposable` before calling Dispose:</span></span>

```csharp
{
      Resource r = new Resource();
      try {
            // statements
      }
      finally {
            if (resource != null) resource.Dispose();
      }
}
```

<span data-ttu-id="143e3-137">Pour s’adapter au modèle jetable, la méthode `Dispose` doit être accessible, sans paramètre et avoir un type de retour `void`.</span><span class="sxs-lookup"><span data-stu-id="143e3-137">In order to fit the disposable pattern the `Dispose` method must be accessible, parameterless and have a `void` return type.</span></span> <span data-ttu-id="143e3-138">Il n’existe aucune autre restriction.</span><span class="sxs-lookup"><span data-stu-id="143e3-138">There are no other restrictions.</span></span> <span data-ttu-id="143e3-139">Cela signifie explicitement que les méthodes d’extension peuvent être utilisées ici.</span><span class="sxs-lookup"><span data-stu-id="143e3-139">This explicitly means that extension methods can be used here.</span></span>

## <a name="considerations"></a><span data-ttu-id="143e3-140">Considérations</span><span class="sxs-lookup"><span data-stu-id="143e3-140">Considerations</span></span>

### <a name="case-labels-without-blocks"></a><span data-ttu-id="143e3-141">étiquettes case sans blocs</span><span class="sxs-lookup"><span data-stu-id="143e3-141">case labels without blocks</span></span>

<span data-ttu-id="143e3-142">Une `using declaration` est directement illégale à l’intérieur d’une étiquette `case` en raison de complications autour de sa durée de vie réelle.</span><span class="sxs-lookup"><span data-stu-id="143e3-142">A `using declaration` is illegal directly inside a `case` label due to complications around its actual lifetime.</span></span> <span data-ttu-id="143e3-143">Une solution possible consiste simplement à lui attribuer la même durée de vie qu’une `out var` au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="143e3-143">One potential solution is to simply give it the same lifetime as an `out var` in the same location.</span></span> <span data-ttu-id="143e3-144">Il a été considéré comme la complexité supplémentaire de l’implémentation des fonctionnalités et de la facilité de travail (juste ajouter un bloc à l’étiquette `case`) n’a pas justifié la réalisation de cet itinéraire.</span><span class="sxs-lookup"><span data-stu-id="143e3-144">It was deemed the extra complexity to the feature implementation and the ease of the work around (just add a block to the `case` label) didn't justify taking this route.</span></span>

## <a name="future-expansions"></a><span data-ttu-id="143e3-145">Expansions futures</span><span class="sxs-lookup"><span data-stu-id="143e3-145">Future Expansions</span></span>

### <a name="fixed-locals"></a><span data-ttu-id="143e3-146">variables locales fixes</span><span class="sxs-lookup"><span data-stu-id="143e3-146">fixed locals</span></span>

<span data-ttu-id="143e3-147">Une instruction `fixed` possède toutes les propriétés des instructions `using` qui ont motivé la capacité à avoir `using` variables locales.</span><span class="sxs-lookup"><span data-stu-id="143e3-147">A `fixed` statement has all of the properties of `using` statements that motivated the ability to have `using` locals.</span></span> <span data-ttu-id="143e3-148">Vous devez tenir compte de l’extension de cette fonctionnalité à `fixed` les variables locales.</span><span class="sxs-lookup"><span data-stu-id="143e3-148">Consideration should be given to extending this feature to `fixed` locals as well.</span></span> <span data-ttu-id="143e3-149">Les règles de durée de vie et de classement doivent s’appliquer aussi bien pour `using` et `fixed` ici.</span><span class="sxs-lookup"><span data-stu-id="143e3-149">The lifetime and ordering rules should apply equally well for `using` and `fixed` here.</span></span>
