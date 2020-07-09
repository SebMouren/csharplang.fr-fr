---
ms.openlocfilehash: a26af810ced3fe5a7d7108f38a22d40aa64543f5
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157195"
---
# <a name="repeated-attributes-in-partial-members"></a><span data-ttu-id="1b0ff-101">Attributs répétés dans les membres partiels</span><span class="sxs-lookup"><span data-stu-id="1b0ff-101">Repeated Attributes in Partial Members</span></span>

## <a name="summary"></a><span data-ttu-id="1b0ff-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="1b0ff-102">Summary</span></span>

<span data-ttu-id="1b0ff-103">Autorisez chaque déclaration d’un membre partiel à appliquer indépendamment un attribut non marqué avec `[AttributeUsage(AllowMultiple = true)]` , à condition que les arguments d’attribut soient identiques dans toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-103">Allow each declaration of a partial member to independently apply an attribute not marked with `[AttributeUsage(AllowMultiple = true)]`, as long as the attribute arguments are identical in all applications.</span></span>

## <a name="motivation"></a><span data-ttu-id="1b0ff-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="1b0ff-104">Motivation</span></span>

<span data-ttu-id="1b0ff-105">Lors de la prise en compte des attributs présents sur une méthode « partial », le langage Union tous les attributs de toutes les positions correspondantes sur les deux déclarations.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-105">When considering what attributes are present on a 'partial' method, the language unions together all the attributes in all corresponding positions on both declarations.</span></span> <span data-ttu-id="1b0ff-106">Par exemple, la méthode `M` ci-dessous a des attributs `A` et `B` .</span><span class="sxs-lookup"><span data-stu-id="1b0ff-106">For example, the method `M` below has attributes `A` and `B`.</span></span>

```cs
[A]
partial void M();

[B]
partial void M() { }
```

<span data-ttu-id="1b0ff-107">Cela signifie que les attributs qui ne sont pas marqués `[AttributeUsage(AllowMultiple = true)]` ne peuvent pas être présents dans les deux parties :</span><span class="sxs-lookup"><span data-stu-id="1b0ff-107">This means that attributes which are not marked `[AttributeUsage(AllowMultiple = true)]` cannot be present across both parts:</span></span>

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

<span data-ttu-id="1b0ff-108">Cela présente un problème d’utilisation/de lisibilité, car certains attributs sont conçus pour informer l’utilisateur et/ou le chargé de maintenance de la méthode des pré/post-conditions ou des invariantes requis par la méthode.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-108">This presents a usability/readability issue, because some attributes are designed to inform the user and/or maintainer of the method of what pre/postconditions or invariants the method requires.</span></span> <span data-ttu-id="1b0ff-109">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1b0ff-109">For example:</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

<span data-ttu-id="1b0ff-110">Un membre partiel facilite généralement la relation entre un générateur de code et un utilisateur final--chaque tiers fournit l’une des déclarations du membre partiel afin qu’un générateur de code fournisse des fonctionnalités à l’utilisateur ou que l’utilisateur puisse accéder à un point d’extension dans le code généré.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-110">A partial member typically facilitates the relationship between a code generator and an end user--each party provides one of the declarations of the partial member in order for a code generator to provide functionality to the user, or for the user to access an extension point in generated code.</span></span> <span data-ttu-id="1b0ff-111">Dans le cas où une seule déclaration est autorisée à avoir ces attributs d’application unique, le générateur et l’utilisateur ne peuvent pas communiquer efficacement leurs exigences.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-111">In the situation where only one declaration is allowed to have these single-application attributes, the generator and the user can't effectively communicate their requirements to each other.</span></span> <span data-ttu-id="1b0ff-112">Si un générateur produit une déclaration de définition avec un `NotNullWhen` attribut, par exemple, l’utilisateur ne peut pas écrire de déclaration d’implémentation avec le même attribut, même si la post-condition est applicable à l’implémentation et vérifiée par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-112">If a generator produces a defining declaration with a `NotNullWhen` attribute, for instance, the user cannot write an implementing declaration with that same attribute, even though the postcondition is applicable to the implementation, and checked by the compiler.</span></span> <span data-ttu-id="1b0ff-113">Cela crée une confusion pour les utilisateurs lorsqu’ils effectuent le suivi des causes racines des avertissements ou lorsque vous essayez de comprendre les comportements d’une méthode.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-113">This creates confusion for users when tracking down the root causes of warnings or when trying to understand the behaviors of a method.</span></span>

## <a name="solution"></a><span data-ttu-id="1b0ff-114">Solution</span><span class="sxs-lookup"><span data-stu-id="1b0ff-114">Solution</span></span>

<span data-ttu-id="1b0ff-115">Autorisez l’utilisation d’un attribut non-AllowMultiple une seule fois dans chaque déclaration partielle, à condition que les arguments d’attribut soient identiques.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-115">Allow a non-AllowMultiple attribute to be used once in each partial declaration, as long as the attribute arguments are identical.</span></span> <span data-ttu-id="1b0ff-116">Étant donné que les arguments d’attribut sont toutes des constantes, cela ne doit pas être très difficile à vérifier au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-116">Since attribute arguments are all constants, this should not be very difficult to verify at compile time.</span></span> <span data-ttu-id="1b0ff-117">Lorsque les attributs sont unions entre les déclarations, chaque attribut non-AllowMultiple est dédupliqué et une seule instance de l’attribut est émise.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-117">When attributes are unioned across declarations, each non-AllowMultiple attribute will be de-duplicated and only one instance of the attribute will be emitted.</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a><span data-ttu-id="1b0ff-118">Questions ouvertes</span><span class="sxs-lookup"><span data-stu-id="1b0ff-118">Open questions</span></span>

1. <span data-ttu-id="1b0ff-119">Une telle répétition des attributs doit-elle être autorisée sur les déclarations de type « partiels » ou uniquement sur les membres qui ne sont pas de type (par exemple, méthodes) ?</span><span class="sxs-lookup"><span data-stu-id="1b0ff-119">Should such repetition of attributes be permitted on 'partial' type declarations or only on non-type members (e.g. methods)?</span></span>
2. <span data-ttu-id="1b0ff-120">Les attributs qui autorisent l’utilisation de plusieurs utilisations sur un symbole doivent *-ils être* autorisés à déduplication des utilisations équivalentes d’un attribut ?</span><span class="sxs-lookup"><span data-stu-id="1b0ff-120">Should attributes which *do* allow multiple usages on a symbol be permitted to "opt in" to de-duplication of equivalent usages of an attribute?</span></span>

### <a name="design-meetings"></a><span data-ttu-id="1b0ff-121">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="1b0ff-121">Design meetings</span></span>
#### <a name="6th-july-2020"></a>[<span data-ttu-id="1b0ff-122">6 juillet 2020</span><span class="sxs-lookup"><span data-stu-id="1b0ff-122">6th July 2020</span></span>](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
<span data-ttu-id="1b0ff-123">La proposition est acceptée.</span><span class="sxs-lookup"><span data-stu-id="1b0ff-123">The proposal is accepted.</span></span>
  - <span data-ttu-id="1b0ff-124">La répétition des attributs non-AllowMultiple est autorisée dans les déclarations de type partiel (Open question 1).</span><span class="sxs-lookup"><span data-stu-id="1b0ff-124">Repetition of non-AllowMultiple attributes will be permitted across partial type declarations (open question 1).</span></span>
  - <span data-ttu-id="1b0ff-125">L’application répétée d’attributs AllowMultiple ne changera pas dans le comportement, et un mécanisme d’abonnement pour la déduplication peut être envisagé dans une prochaine proposition (Open question 2).</span><span class="sxs-lookup"><span data-stu-id="1b0ff-125">Repeated application of AllowMultiple attributes will not change in behavior, and an "opt in" mechanism for de-duplication may be considered in a future proposal (open question 2).</span></span>
