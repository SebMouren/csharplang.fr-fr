---
ms.openlocfilehash: 4f66c0f60d05ed6509a1d0843318a71d1b36c351
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484705"
---
# <a name="pattern-matching-with-generics"></a><span data-ttu-id="977bc-101">mise en correspondance de modèle avec des génériques</span><span class="sxs-lookup"><span data-stu-id="977bc-101">pattern-matching with generics</span></span>

* <span data-ttu-id="977bc-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="977bc-102">[x] Proposed</span></span>
* <span data-ttu-id="977bc-103">[] Prototype :</span><span class="sxs-lookup"><span data-stu-id="977bc-103">[ ] Prototype:</span></span>
* <span data-ttu-id="977bc-104">[] Implémentation :</span><span class="sxs-lookup"><span data-stu-id="977bc-104">[ ] Implementation:</span></span>
* <span data-ttu-id="977bc-105">[] Spécification :</span><span class="sxs-lookup"><span data-stu-id="977bc-105">[ ] Specification:</span></span>

## <a name="summary"></a><span data-ttu-id="977bc-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="977bc-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="977bc-107">La spécification de l' [opérateur C# As existant](../../spec/expressions.md#the-as-operator) permet de ne pas effectuer de conversion entre le type de l’opérande et le type spécifié lorsqu’il s’agit d’un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="977bc-107">The specification for the [existing C# as operator](../../spec/expressions.md#the-as-operator) permits there to be no conversion between the type of the operand and the specified type when either is an open type.</span></span> <span data-ttu-id="977bc-108">Toutefois, dans C# 7, le modèle de `Type identifier` nécessite une conversion entre le type de l’entrée et le type donné.</span><span class="sxs-lookup"><span data-stu-id="977bc-108">However, in C# 7 the `Type identifier` pattern requires there be a conversion between the type of the input and the given type.</span></span>

<span data-ttu-id="977bc-109">Nous vous proposons de l’assouplir et de modifier `expression is Type identifier`, en plus d’être autorisé dans les conditions lorsqu’il C# est autorisé dans 7, d’être également autorisé lorsque `expression as Type` serait autorisé.</span><span class="sxs-lookup"><span data-stu-id="977bc-109">We propose to relax this and change `expression is Type identifier`, in addition to being permitted in the conditions when it is permitted in C# 7, to also be permitted when `expression as Type` would be allowed.</span></span> <span data-ttu-id="977bc-110">Plus précisément, les nouveaux cas sont des cas où le type de l’expression ou le type spécifié est un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="977bc-110">Specifically, the new cases are cases where the type of the expression or the specified type is an open type.</span></span> 

## <a name="motivation"></a><span data-ttu-id="977bc-111">Motivation</span><span class="sxs-lookup"><span data-stu-id="977bc-111">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="977bc-112">Les cas où la mise en correspondance des modèles doit être « évidemment » ne sont pas autorisés actuellement à compiler.</span><span class="sxs-lookup"><span data-stu-id="977bc-112">Cases where pattern-matching should "obviously" be permitted currently fail to compile.</span></span> <span data-ttu-id="977bc-113">Voir, par exemple, https://github.com/dotnet/roslyn/issues/16195.</span><span class="sxs-lookup"><span data-stu-id="977bc-113">See, for example, https://github.com/dotnet/roslyn/issues/16195.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="977bc-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="977bc-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="977bc-115">Nous modifions le paragraphe dans la spécification de critères spéciaux (l’ajout proposé est affiché en gras) :</span><span class="sxs-lookup"><span data-stu-id="977bc-115">We change the paragraph in the pattern-matching specification (the proposed addition is shown in bold):</span></span>

> <span data-ttu-id="977bc-116">Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="977bc-116">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="977bc-117">Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T` **, ou si `E` ou `T` est un type ouvert**.</span><span class="sxs-lookup"><span data-stu-id="977bc-117">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`**, or if either `E` or `T` is an open type**.</span></span> <span data-ttu-id="977bc-118">Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.</span><span class="sxs-lookup"><span data-stu-id="977bc-118">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="977bc-119">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="977bc-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="977bc-120">Aucun.</span><span class="sxs-lookup"><span data-stu-id="977bc-120">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="977bc-121">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="977bc-121">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="977bc-122">Aucun.</span><span class="sxs-lookup"><span data-stu-id="977bc-122">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="977bc-123">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="977bc-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="977bc-124">Aucun.</span><span class="sxs-lookup"><span data-stu-id="977bc-124">None.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="977bc-125">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="977bc-125">Design meetings</span></span>

<span data-ttu-id="977bc-126">LDM a considéré cette question et a pensé qu’il s’agissait d’une modification du niveau de correction des bogues.</span><span class="sxs-lookup"><span data-stu-id="977bc-126">LDM considered this question and felt it was a bug-fix level change.</span></span> <span data-ttu-id="977bc-127">Nous le traitons comme une fonctionnalité de langage distincte, car l’apport de modifications après la publication du langage introduirait une incompatibilité de transfert.</span><span class="sxs-lookup"><span data-stu-id="977bc-127">We are treating it as a separate language feature because just making the change after the language has been released would introduce a forward incompatibility.</span></span> <span data-ttu-id="977bc-128">L’utilisation de la modification proposée requiert que le programmeur spécifie la version de langue 7,1.</span><span class="sxs-lookup"><span data-stu-id="977bc-128">Using the proposed change requires that the programmer specify language version 7.1.</span></span>
