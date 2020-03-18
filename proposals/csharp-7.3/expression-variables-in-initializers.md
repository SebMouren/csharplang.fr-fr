---
ms.openlocfilehash: a78567594d39fc4e204e12c4f2f247b8d6995c38
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484922"
---
# <a name="expression-variables-in-initializers"></a><span data-ttu-id="c9838-101">Variables d’expression dans les initialiseurs</span><span class="sxs-lookup"><span data-stu-id="c9838-101">Expression variables in initializers</span></span>

## <a name="summary"></a><span data-ttu-id="c9838-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="c9838-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="c9838-103">Nous étendons les fonctionnalités introduites dans C# 7 pour autoriser des expressions contenant des variables d’expression (des déclarations de variables et des modèles de déclaration) dans les initialiseurs de champ, les initialiseurs de propriété, les initialiseurs de constructeurs et les clauses de requête.</span><span class="sxs-lookup"><span data-stu-id="c9838-103">We extend the features introduced in C# 7 to permit expressions containing expression variables (out variable declarations and declaration patterns) in field initializers, property initializers, ctor-initializers, and query clauses.</span></span>

## <a name="motivation"></a><span data-ttu-id="c9838-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="c9838-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="c9838-105">Cela termine quelques bords à gauche du C# langage en raison d’un manque de temps.</span><span class="sxs-lookup"><span data-stu-id="c9838-105">This completes a couple of the rough edges left in the C# language due to lack of time.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="c9838-106">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="c9838-106">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="c9838-107">Nous supprimons la restriction empêchant la déclaration des variables d’expression (les déclarations de variables et les modèles de déclaration) dans un constructeur ctor-initializer.</span><span class="sxs-lookup"><span data-stu-id="c9838-107">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a ctor-initializer.</span></span> <span data-ttu-id="c9838-108">Une telle variable déclarée est dans la portée dans le corps du constructeur.</span><span class="sxs-lookup"><span data-stu-id="c9838-108">Such a declared variable is in scope throughout the body of the constructor.</span></span>

<span data-ttu-id="c9838-109">Nous supprimons la restriction empêchant la déclaration de variables d’expression (les déclarations de variable et les modèles de déclaration) dans un initialiseur de champ ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="c9838-109">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a field or property initializer.</span></span> <span data-ttu-id="c9838-110">Une telle variable déclarée est dans la portée tout au long de l’expression d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="c9838-110">Such a declared variable is in scope throughout the initializing expression.</span></span>

<span data-ttu-id="c9838-111">Nous supprimons la restriction empêchant la déclaration des variables d’expression (les déclarations de variables et les modèles de déclaration) dans une clause d’expression de requête qui est traduite dans le corps d’une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="c9838-111">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a query expression clause that is translated into the body of a lambda.</span></span> <span data-ttu-id="c9838-112">Une telle variable déclarée est dans la portée tout au long de cette expression de la clause de requête.</span><span class="sxs-lookup"><span data-stu-id="c9838-112">Such a declared variable is in scope throughout that expression of the query clause.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="c9838-113">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="c9838-113">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="c9838-114">Aucun.</span><span class="sxs-lookup"><span data-stu-id="c9838-114">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="c9838-115">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="c9838-115">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="c9838-116">La portée appropriée pour les variables d’expression déclarées dans ces contextes n’est pas évidente et mérite une discussion de LDM.</span><span class="sxs-lookup"><span data-stu-id="c9838-116">The appropriate scope for expression variables declared in these contexts is not obvious, and deserves further LDM discussion.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="c9838-117">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="c9838-117">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="c9838-118">[] Quelle est la portée appropriée pour ces variables ?</span><span class="sxs-lookup"><span data-stu-id="c9838-118">[ ] What is the appropriate scope for these variables?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="c9838-119">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="c9838-119">Design meetings</span></span>

<span data-ttu-id="c9838-120">Aucun.</span><span class="sxs-lookup"><span data-stu-id="c9838-120">None.</span></span>
