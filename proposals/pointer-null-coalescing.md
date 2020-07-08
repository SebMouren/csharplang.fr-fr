---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074296"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a><span data-ttu-id="f2674-101">Étendre la fusion Null ( ??) et l’affectation de fusion Null ( ?? =) opérateurs aux pointeurs</span><span class="sxs-lookup"><span data-stu-id="f2674-101">Extend null-coalescing (??) and null coalescing assignment (??=) operators to pointers</span></span>

* <span data-ttu-id="f2674-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="f2674-102">[x] Proposed</span></span>
* <span data-ttu-id="f2674-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="f2674-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="f2674-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="f2674-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="f2674-105">[] Spécification : en cours</span><span class="sxs-lookup"><span data-stu-id="f2674-105">[ ] Specification: In Progress</span></span>

## <a name="summary"></a><span data-ttu-id="f2674-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="f2674-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="f2674-107">Cette proposition étend la prise en charge d’une fonctionnalité couramment utilisée en C# ( ??</span><span class="sxs-lookup"><span data-stu-id="f2674-107">This proposal extends support of a commonly used feature in C# (??</span></span> <span data-ttu-id="f2674-108">et ?? =) en types de code et de pointeur non sécurisés specifcally.</span><span class="sxs-lookup"><span data-stu-id="f2674-108">and ??=) to unsafe code and pointer types specifcally.</span></span> 

## <a name="motivation"></a><span data-ttu-id="f2674-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="f2674-109">Motivation</span></span>
[motivation]: #motivation


<span data-ttu-id="f2674-110">Il n’y a aucune raison pour que les types pointeur soient exclus de cette fonctionnalité, car ils sont sémantiquement adaptés au cas d’usage de la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f2674-110">There is no reason for pointer types to be excluded from this feature as they semantically fit the feature's use case.</span></span> <span data-ttu-id="f2674-111">La prise en charge de cette fonctionnalité étend l’étendue de la fonctionnalité à ce qu’elle attendrait qu’elle prenne en charge logiquement.</span><span class="sxs-lookup"><span data-stu-id="f2674-111">Supporting this extends the feature's scope to what one would expect it to logically support.</span></span> <span data-ttu-id="f2674-112">Ce concept est déjà pris en charge dans les expressions ternaires, par exemple :</span><span class="sxs-lookup"><span data-stu-id="f2674-112">This concept is already supported in ternary expressions EG:</span></span>

 `T *foo = bar != null ? bar : baz;`

<span data-ttu-id="f2674-113">Les mêmes options syntaxiques proposées aux types non-pointeur doivent donc être étendues aux types pointeur.</span><span class="sxs-lookup"><span data-stu-id="f2674-113">So the same syntactic options that are offered to non-pointer types should be extended to pointer types.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f2674-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="f2674-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="f2674-115">Pour cet ajout au langage, aucune modification grammaticale n’est requise.</span><span class="sxs-lookup"><span data-stu-id="f2674-115">For this addition to the language, no grammar changes are required.</span></span> <span data-ttu-id="f2674-116">Nous ajoutons simplement des types pris en charge à un opérateur existant.</span><span class="sxs-lookup"><span data-stu-id="f2674-116">We are merely adding supported types to an existing operator.</span></span> <span data-ttu-id="f2674-117">Les règles de conversion actuelles détermineront alors le type résultant de l’opération ??</span><span class="sxs-lookup"><span data-stu-id="f2674-117">The current conversion rules will then determine the resulting type of the ??</span></span> <span data-ttu-id="f2674-118">expression.</span><span class="sxs-lookup"><span data-stu-id="f2674-118">expression.</span></span>
<span data-ttu-id="f2674-119">Les autres règles restent les mêmes, à l’exception des règles de type souple qui devront être modifiées.</span><span class="sxs-lookup"><span data-stu-id="f2674-119">Other rules will remain the same with the exception of the relaxed type rules which will need to be modified.</span></span>

<span data-ttu-id="f2674-120">La spécification C# devra être mise à jour pour refléter cet ajout avec la modification de la ligne.</span><span class="sxs-lookup"><span data-stu-id="f2674-120">The  C# spec will need to be updated to reflect this addition with the change of the line.</span></span>
> <span data-ttu-id="f2674-121">Une expression de fusion Null de la forme `a` ??</span><span class="sxs-lookup"><span data-stu-id="f2674-121">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="f2674-122">`b`requiert `a` un type Nullable ou un type référence.</span><span class="sxs-lookup"><span data-stu-id="f2674-122">`b` requires `a` to be of a nullable type or reference type.</span></span>

<span data-ttu-id="f2674-123">to</span><span class="sxs-lookup"><span data-stu-id="f2674-123">to</span></span>

> <span data-ttu-id="f2674-124">Une expression de fusion Null de la forme `a` ??</span><span class="sxs-lookup"><span data-stu-id="f2674-124">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="f2674-125">`b`requiert `a` un type Nullable, un type référence ou un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="f2674-125">`b` requires `a` to be of a nullable type, reference type or pointer type.</span></span>

<span data-ttu-id="f2674-126">ainsi que</span><span class="sxs-lookup"><span data-stu-id="f2674-126">as well as</span></span> 
> <span data-ttu-id="f2674-127">Si `A` Exists et n’est pas un type Nullable ou un type référence, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f2674-127">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>

<span data-ttu-id="f2674-128">to</span><span class="sxs-lookup"><span data-stu-id="f2674-128">to</span></span>

> <span data-ttu-id="f2674-129">Si `A` Exists et n’est pas un type Nullable, un type référence ou un type pointeur, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f2674-129">If `A` exists and is not a nullable type, reference type or pointer type, a compile-time error occurs.</span></span>

<span data-ttu-id="f2674-130">Actuellement, les règles pour `??=` n’interdisent pas les types de pointeur ; Toutefois, l’opérateur n’a pas été implémenté comme celui-ci.</span><span class="sxs-lookup"><span data-stu-id="f2674-130">Currently the rules for `??=` do not prohibit pointer types; however, the operator was not implemented like that.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="f2674-131">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="f2674-131">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="f2674-132">Le code unsafe peut être considéré comme confus et un vecteur pour les bogues qui n’auraient pas lieu autrement.</span><span class="sxs-lookup"><span data-stu-id="f2674-132">Unsafe code can be considered confusing and a vector for bugs that would not happen otherwise.</span></span> <span data-ttu-id="f2674-133">L’amélioration des options syntaxiques disponibles pour les types pointeur peut entraîner des bogues dans lesquels le développeur n’a pas eu connaissance de la signification sémantique de ce qui a été écrit.</span><span class="sxs-lookup"><span data-stu-id="f2674-133">Increasing the available syntactic options for pointer types could possibly lead to bugs where the developer was unaware of the semantic meaning of what was written.</span></span> 

## <a name="alternatives"></a><span data-ttu-id="f2674-134">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="f2674-134">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="f2674-135">Aucune autre conception n’a été prise en compte, car il ne s’agit pas d’une nouvelle fonctionnalité, mais il s’agit plutôt d’une extension d’une fonctionnalité déjà implémentée.</span><span class="sxs-lookup"><span data-stu-id="f2674-135">No other designs have been considered as this is not a new feature, but it is rather an extension of an already implemented feature.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="f2674-136">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="f2674-136">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="f2674-137">Une question possible est si la prise en charge doit être étendue au déréférencement implicite dans la même veine que les conversions implicites pour `Nullable<T>` sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="f2674-137">A possible question is if support should be extended to implicit dereferencing in the same vein as implicit conversions for `Nullable<T>` are supported.</span></span>

<span data-ttu-id="f2674-138">PAR exemple</span><span class="sxs-lookup"><span data-stu-id="f2674-138">EG:</span></span>

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a><span data-ttu-id="f2674-139">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="f2674-139">Design meetings</span></span>

https://github.com/dotnet/csharplang/issues/418



