---
ms.openlocfilehash: 9647fff40a1e45bef917f140612ae4e91abea958
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484572"
---
# <a name="lambda-attributes"></a><span data-ttu-id="d06e4-101">Attributs lambda</span><span class="sxs-lookup"><span data-stu-id="d06e4-101">Lambda Attributes</span></span>

* <span data-ttu-id="d06e4-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="d06e4-102">[x] Proposed</span></span>
* <span data-ttu-id="d06e4-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="d06e4-103">[ ] Prototype</span></span>
* <span data-ttu-id="d06e4-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="d06e4-104">[ ] Implementation</span></span>
* <span data-ttu-id="d06e4-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="d06e4-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="d06e4-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="d06e4-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="d06e4-107">Autorisez l’application d’attributs aux expressions lambda (et aux méthodes anonymes) et aux paramètres de méthode lambda/anonyme, comme ils le peuvent sur les méthodes normales.</span><span class="sxs-lookup"><span data-stu-id="d06e4-107">Allow attributes to be applied to lambdas (and anonymous methods) and to lambda / anonymous method parameters, as they can be on regular methods.</span></span>

## <a name="motivation"></a><span data-ttu-id="d06e4-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="d06e4-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="d06e4-109">Deux motivations principales :</span><span class="sxs-lookup"><span data-stu-id="d06e4-109">Two primary motivations:</span></span>

1. <span data-ttu-id="d06e4-110">Pour fournir des métadonnées visibles aux analyseurs au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="d06e4-110">To provide metadata visible to analyzers at compile-time.</span></span>
2. <span data-ttu-id="d06e4-111">Pour fournir des métadonnées visibles pour la réflexion et les outils au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="d06e4-111">To provide metadata visible to reflection and tooling at run-time.</span></span>

<span data-ttu-id="d06e4-112">En guise d’exemple de (1) : pour le code qui respecte les performances, il est utile de pouvoir avoir un analyseur qui signale quand des fermetures et des délégués sont alloués pour les expressions lambda qui se ferment sur l’État.</span><span class="sxs-lookup"><span data-stu-id="d06e4-112">As an example of (1): For performance-sensitive code, it is helpful to be able to have an analyzer that flags when closures and delegates are being allocated for lambdas that close over state.</span></span>  <span data-ttu-id="d06e4-113">Souvent, un développeur de ce type de code sortira de sa méthode pour éviter la capture d’un État, de sorte que le compilateur puisse générer une méthode statique et un délégué pouvant être mis en cache pour la méthode, ou le développeur s’assurera que le seul état fermé est `this`, ce qui permet au compilateur d’éviter d’allouer un objet de fermeture.</span><span class="sxs-lookup"><span data-stu-id="d06e4-113">Often a developer of such code will go out of his or her way to avoid capturing any state, so that the compiler can generate a static method and a cacheable delegate for the method, or the developer will ensure that the only state being closed over is `this`, allowing the compiler at least to avoid allocating a closure object.</span></span>  <span data-ttu-id="d06e4-114">Toutefois, sans prise en charge linguistique pour limiter ce qui peut être capturé, il est très facile de fermer accidentellement l’État.</span><span class="sxs-lookup"><span data-stu-id="d06e4-114">But, without language support for limiting what may be captured, it is all too easy to accidentally close over state.</span></span>  <span data-ttu-id="d06e4-115">Il serait utile si un développeur pouvait annoter des expressions lambda avec des attributs pour indiquer l’État sur lequel ils sont autorisés à se fermer, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-115">It would be valuable if a developer could annotate lambdas with attributes to indicate what state they're allowed to close over, for example:</span></span>

```csharp
[CaptureNone] // can't close over any instance state
[CaptureThis] // can only capture `this` and no other instance state
[CaptureAny] // can close over any instance state
```

<span data-ttu-id="d06e4-116">Un analyseur peut ensuite être écrit pour signaler quand l’État est capturé de manière incorrecte, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-116">Then an analyzer can be written to flag when state is captured incorrectly, for example:</span></span>

```csharp
var results = collection.Select([CaptureNone](i) => Process(item)); // Analyzer error: [CaptureNone] lambdas captures `this`
...
private U Process(T item) { ... }
```

## <a name="detailed-design"></a><span data-ttu-id="d06e4-117">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="d06e4-117">Detailed design</span></span>
[design]: #detailed-design

- <span data-ttu-id="d06e4-118">À l’aide de la même syntaxe d’attribut que sur les méthodes normales, les attributs peuvent être appliqués au début d’une méthode lambda ou anonyme, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-118">Using the same attribute syntax as on normal methods, attributes may be applied at the beginning of a lambda or anonymous method, for example:</span></span>

```csharp
[SomeAttribute(...)] () => { ... }
[SomeAttribute(...)] delegate (int i) { ... }
```

- <span data-ttu-id="d06e4-119">Pour éviter toute ambiguïté quant au fait qu’un attribut s’applique à la méthode lambda ou à l’un des arguments, les attributs ne peuvent être utilisés que lorsque parenthèses sont utilisés autour d’un argument, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-119">To avoid ambiguity as to whether an attribute applies to the lambda method or to one of the arguments, attributes may only be used when parens are used around any arguments, for example:</span></span>

```csharp
[SomeAttribute] i => { ... } // ERROR
[SomeAttribute] (i) => { ... } // Ok
[SomeAttribute] (int i) => { ... } // Ok
```

- <span data-ttu-id="d06e4-120">Avec les méthodes anonymes, les parenthèses ne sont pas nécessaires pour appliquer un attribut à la méthode avant le mot clé `delegate`, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-120">With anonymous methods, parens are not needed in order to apply an attribute to the method before the `delegate` keyword, for example:</span></span>

```csharp
[SomeAttribute] delegate { ... } // Ok
[SomeAttribute] delegate (int i) => { ... } // Ok
```

- <span data-ttu-id="d06e4-121">Plusieurs attributs peuvent être appliqués, par le biais d’une syntaxe standard délimitée par des virgules ou par le biais d’une syntaxe d’attribut complet, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-121">Multiple attributes may be applied, either via standard comma-delimited syntax or via full-attribute syntax, for example:</span></span>

```csharp
[FirstAttribute, SecondAttribute] (i) => { ... } // Ok
[FirstAttribute] [SecondAttribute] (i) => { .... } // Ok
```

- <span data-ttu-id="d06e4-122">Les attributs peuvent être appliqués aux paramètres d’une méthode anonyme ou d’une expression lambda, mais uniquement quand les parenthèses sont utilisés autour des arguments, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-122">Attributes may be applied to the parameters to an anonymous method or lambda, but only when parens are used around any arguments, for example:</span></span>

```csharp
[SomeAttribute] i => { ... } // ERROR
([SomeAttribute] i) => { .... } // Ok
([SomeAttribute] int i) => { ... } // Ok
([SomeAttribute] i, [SomeOtherAttribute] j) => { ... } // Ok
```

- <span data-ttu-id="d06e4-123">Plusieurs attributs peuvent être appliqués aux paramètres d’une méthode anonyme ou d’une expression lambda, à l’aide de la syntaxe à attributs ou délimités par des virgules, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-123">Multiple attributes may be applied to the parameters of an anonymous method or lambda, using either the comma-delimited or full-attribute syntax, for example:</span></span>

```csharp
([FirstAttribute, SecondAttribute] i) => { ... } // Ok
([FirstAttribute] [SecondAttribute] i) => { ... } // Ok
```

- <span data-ttu-id="d06e4-124">les attributs ciblés `return`peuvent également être utilisés sur les expressions lambda, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d06e4-124">`return`-targeted attributes may also be used on lambdas, for example:</span></span>

```csharp
([return: SomeAttribute] (i) => { ... }) // Ok
```

- <span data-ttu-id="d06e4-125">Le compilateur génère les attributs sur la méthode générée et les arguments de ces méthodes comme c’est le cas pour toute autre méthode.</span><span class="sxs-lookup"><span data-stu-id="d06e4-125">The compiler outputs the attributes onto the generated method and arguments to those methods as it would for any other method.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="d06e4-126">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="d06e4-126">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="d06e4-127">n/a</span><span class="sxs-lookup"><span data-stu-id="d06e4-127">n/a</span></span>

## <a name="alternatives"></a><span data-ttu-id="d06e4-128">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="d06e4-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="d06e4-129">n/a</span><span class="sxs-lookup"><span data-stu-id="d06e4-129">n/a</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="d06e4-130">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="d06e4-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="d06e4-131">n/a</span><span class="sxs-lookup"><span data-stu-id="d06e4-131">n/a</span></span>

## <a name="design-meetings"></a><span data-ttu-id="d06e4-132">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="d06e4-132">Design meetings</span></span>

<span data-ttu-id="d06e4-133">n/a</span><span class="sxs-lookup"><span data-stu-id="d06e4-133">n/a</span></span>