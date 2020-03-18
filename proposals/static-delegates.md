---
ms.openlocfilehash: a8822137c85f449444ed675c6f2912315c041691
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484474"
---
# <a name="static-delegates"></a><span data-ttu-id="238ad-101">Délégués statiques</span><span class="sxs-lookup"><span data-stu-id="238ad-101">Static Delegates</span></span>

* <span data-ttu-id="238ad-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="238ad-102">[x] Proposed</span></span>
* <span data-ttu-id="238ad-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="238ad-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="238ad-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="238ad-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="238ad-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="238ad-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="238ad-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="238ad-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="238ad-107">Fournissez une fonction de rappel à usage général et légère C# au langage.</span><span class="sxs-lookup"><span data-stu-id="238ad-107">Provide a general-purpose, lightweight callback capability to the C# language.</span></span>

## <a name="motivation"></a><span data-ttu-id="238ad-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="238ad-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="238ad-109">Aujourd’hui, les utilisateurs ont la possibilité de créer des rappels à l’aide du type de `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="238ad-109">Today, users have the ability to create callbacks using the `System.Delegate` type.</span></span> <span data-ttu-id="238ad-110">Toutefois, celles-ci sont assez lourdes (par exemple, une allocation de tas et une gestion systématique pour le chaînage des rappels).</span><span class="sxs-lookup"><span data-stu-id="238ad-110">However, these are fairly heavyweight (such as requiring a heap allocation and always having handling for chaining callbacks together).</span></span>

<span data-ttu-id="238ad-111">En outre, `System.Delegate` ne fournit pas la meilleure interopérabilité avec les pointeurs de fonction non managés, à savoir la non-blittable et la nécessité d’un marshaling à chaque fois qu’il franchit les limites managées/non managées.</span><span class="sxs-lookup"><span data-stu-id="238ad-111">Additionally, `System.Delegate` does not provide the best interop with unmanaged function pointers, namely due being non-blittable and requiring marshalling anytime it crosses the managed/unmanaged boundary.</span></span>

<span data-ttu-id="238ad-112">Avec quelques ajustements mineurs, nous pourrions fournir un nouveau type de délégué léger, à usage général et des interopérabilités bien avec le code natif.</span><span class="sxs-lookup"><span data-stu-id="238ad-112">With a few minor tweaks, we could provide a new type of delegate that is lightweight, general-purpose, and interops well with native code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="238ad-113">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="238ad-113">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="238ad-114">Vous pouvez déclarer un délégué statique à l’aide de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="238ad-114">One would declare a static delegate via the following:</span></span>

```C#
static delegate int Func()
```

<span data-ttu-id="238ad-115">Il est également possible d’attribuer à la déclaration une valeur similaire à `System.Runtime.InteropServices.UnmanagedFunctionPointer` afin que la Convention d’appel, le marshaling de chaînes et le comportement de la définition de la dernière erreur puissent être contrôlés.</span><span class="sxs-lookup"><span data-stu-id="238ad-115">One could additionally attribute the declaration with something similar to `System.Runtime.InteropServices.UnmanagedFunctionPointer` so that the calling convention, string marshalling, and set last error behavior can be controlled.</span></span> <span data-ttu-id="238ad-116">Remarque : l’utilisation de `System.Runtime.InteropServices.UnmanagedFunctionPointer` elle-même ne fonctionne pas, car elle est utilisable uniquement sur les délégués.</span><span class="sxs-lookup"><span data-stu-id="238ad-116">NOTE: Using `System.Runtime.InteropServices.UnmanagedFunctionPointer` itself will not work, as it is only usable on Delegates.</span></span>

<span data-ttu-id="238ad-117">La déclaration est traduite dans une représentation interne par le compilateur qui est semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="238ad-117">The declaration would get translated into an internal representation by the compiler that is similar to the following</span></span>

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

<span data-ttu-id="238ad-118">Autrement dit, il est représenté en interne par un struct qui a un seul membre de type `IntPtr` (ce type de struct est blittable et n’entraîne pas d’allocation de tas) :</span><span class="sxs-lookup"><span data-stu-id="238ad-118">That is to say, it is internally represented by a struct that has a single member of type `IntPtr` (such a struct is blittable and does not incur any heap allocations):</span></span>
* <span data-ttu-id="238ad-119">Le membre contient l’adresse de la fonction qui doit être le rappel.</span><span class="sxs-lookup"><span data-stu-id="238ad-119">The member contains the address of the function that is to be the callback.</span></span>
* <span data-ttu-id="238ad-120">Le type déclare une méthode qui correspond à la signature de méthode du rappel.</span><span class="sxs-lookup"><span data-stu-id="238ad-120">The type declares a method matching the method signature of the callback.</span></span>
* <span data-ttu-id="238ad-121">Le nom du struct ne doit pas être constructible par l’utilisateur (comme nous le faisons avec d’autres structures de stockage générées en interne).</span><span class="sxs-lookup"><span data-stu-id="238ad-121">The name of the struct should not be user-constructible (as we do with other internally generated backing structures).</span></span>
 * <span data-ttu-id="238ad-122">Par exemple : les mémoires tampons de taille fixe génèrent un struct avec un nom au format `<name>e__FixedBuffer` (`<` et `>` font partie de l’identificateur et rendent l’identificateur non constructible C#, mais toujours utilisable dans il).</span><span class="sxs-lookup"><span data-stu-id="238ad-122">For example: fixed size buffers generate a struct with a name in the format of `<name>e__FixedBuffer` (`<` and `>` are part of the identifier and make the identifier not constructible in C#, but still useable in IL).</span></span>
* <span data-ttu-id="238ad-123">Le nom de la déclaration de méthode doit être un nom connu utilisé dans tous les types délégués statiques (cela permet au compilateur de connaître le nom à rechercher lors de la détermination de la signature).</span><span class="sxs-lookup"><span data-stu-id="238ad-123">The name of the method declaration should be a well known name used across all static delegate types (this allows the compiler to know the name to look for when determining the signature).</span></span>

<span data-ttu-id="238ad-124">La valeur du délégué statique ne peut être liée qu’à une méthode statique qui correspond à la signature du rappel.</span><span class="sxs-lookup"><span data-stu-id="238ad-124">The value of the static delegate can only be bound to a static method that matches the signature of the callback.</span></span>

<span data-ttu-id="238ad-125">Le chaînage des rappels n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="238ad-125">Chaining callbacks together is not supported.</span></span>

<span data-ttu-id="238ad-126">L’appel du rappel est implémenté par l’instruction `calli`.</span><span class="sxs-lookup"><span data-stu-id="238ad-126">Invocation of the callback would be implemented by the `calli` instruction.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="238ad-127">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="238ad-127">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="238ad-128">Les délégués statiques ne fonctionneront pas avec les API existantes qui utilisent des délégués réguliers (vous devez encapsuler ce délégué statique dans un délégué normal de la même signature).</span><span class="sxs-lookup"><span data-stu-id="238ad-128">Static Delegates would not work with existing APIs that use regular delegates (one would need to wrap said static delegate in a regular delegate of the same signature).</span></span>
* <span data-ttu-id="238ad-129">Étant donné que `System.Delegate` est représenté en interne sous la forme d’un ensemble de champs `object` et `IntPtr` (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), il est possible d’autoriser la conversion implicite d’un délégué statique en `System.Delegate` qui a une signature de méthode correspondante.</span><span class="sxs-lookup"><span data-stu-id="238ad-129">Given that `System.Delegate` is represented internally as a set of `object` and `IntPtr` fields (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), it would be possible to allow implicit conversion of a static delegate to a `System.Delegate` that has a matching method signature.</span></span> <span data-ttu-id="238ad-130">Il serait également possible de fournir une conversion explicite dans le sens inverse, à condition que la `System.Delegate` conforme à toutes les exigences d’un délégué statique.</span><span class="sxs-lookup"><span data-stu-id="238ad-130">It would also be possible to provide an explicit conversion in the opposite direction, provided the `System.Delegate` conformed to all the requirements of being a static delegate.</span></span>

<span data-ttu-id="238ad-131">Un travail supplémentaire est nécessaire pour rendre le délégué statique facilement utilisable dans l’infrastructure de base.</span><span class="sxs-lookup"><span data-stu-id="238ad-131">Additional work would be needed to make Static Delegate readily usable in the core framework.</span></span>

## <a name="alternatives"></a><span data-ttu-id="238ad-132">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="238ad-132">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="238ad-133">TBD</span><span class="sxs-lookup"><span data-stu-id="238ad-133">TBD</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="238ad-134">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="238ad-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="238ad-135">Quelles sont les parties de la conception toujours TBD ?</span><span class="sxs-lookup"><span data-stu-id="238ad-135">What parts of the design are still TBD?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="238ad-136">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="238ad-136">Design meetings</span></span>

<span data-ttu-id="238ad-137">Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.</span><span class="sxs-lookup"><span data-stu-id="238ad-137">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>


