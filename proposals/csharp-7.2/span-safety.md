---
ms.openlocfilehash: 6088a5cd41926d828013f1b8e5736fd2b7939e44
ms.sourcegitcommit: da452002c3f472165a0e1fa7759f494cc703ae31
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "79485041"
---
# <a name="compile-time-enforcement-of-safety-for-ref-like-types"></a><span data-ttu-id="6e328-101">Mise en œuvre de la sécurité au moment de la compilation pour les types de type Ref</span><span class="sxs-lookup"><span data-stu-id="6e328-101">Compile time enforcement of safety for ref-like types</span></span>

## <a name="introduction"></a><span data-ttu-id="6e328-102">Introduction</span><span class="sxs-lookup"><span data-stu-id="6e328-102">Introduction</span></span>

<span data-ttu-id="6e328-103">La principale raison des règles de sécurité supplémentaires en ce qui concerne les types tels que `Span<T>` et `ReadOnlySpan<T>` est que ces types doivent être confinés à la pile d’exécution.</span><span class="sxs-lookup"><span data-stu-id="6e328-103">The main reason for the additional safety rules when dealing with types like `Span<T>` and `ReadOnlySpan<T>` is that such types must be confined to the execution stack.</span></span>
 
<span data-ttu-id="6e328-104">Il existe deux raisons pour lesquelles les `Span<T>` et les types similaires doivent être des types de pile uniquement.</span><span class="sxs-lookup"><span data-stu-id="6e328-104">There are two reasons why `Span<T>` and similar types must be a stack-only types.</span></span>

1. <span data-ttu-id="6e328-105">`Span<T>` est sémantiquement un struct contenant une référence et un `(ref T data, int length)`de plage.</span><span class="sxs-lookup"><span data-stu-id="6e328-105">`Span<T>` is semantically a struct containing a reference and a range - `(ref T data, int length)`.</span></span> <span data-ttu-id="6e328-106">Quelle que soit l’implémentation réelle, les écritures dans ce type de structure ne seraient pas atomiques.</span><span class="sxs-lookup"><span data-stu-id="6e328-106">Regardless of actual implementation, writes to such struct would not be atomic.</span></span> <span data-ttu-id="6e328-107">Une « déchirure » simultanée de ce type de struct entraînerait la possibilité de `length` ne correspond pas à la `data`, provoquant des accès hors limites et des violations de sécurité de type, ce qui finit par provoquer l’endommagement du tas GC dans du code apparemment « sûr ».</span><span class="sxs-lookup"><span data-stu-id="6e328-107">Concurrent "tearing" of such struct would lead to the possibility of `length` not matching the `data`, causing out-of-range accesses and type-safety violations, which ultimately could result in GC heap corruption in seemingly "safe" code.</span></span>
2. <span data-ttu-id="6e328-108">Certaines implémentations de `Span<T>` contiennent littéralement un pointeur managé dans l’un de ses champs.</span><span class="sxs-lookup"><span data-stu-id="6e328-108">Some implementations of `Span<T>` literally contain a managed pointer in one of its fields.</span></span> <span data-ttu-id="6e328-109">Les pointeurs managés ne sont pas pris en charge en tant que champs d’objets de tas et le code qui gère pour placer un pointeur managé sur le tas GC se bloque généralement au moment du JIT.</span><span class="sxs-lookup"><span data-stu-id="6e328-109">Managed pointers are not supported as fields of heap objects and code that manages to put a managed pointer on the GC heap typically crashes at JIT time.</span></span>

<span data-ttu-id="6e328-110">Tous les problèmes ci-dessus sont atténués si des instances de `Span<T>` sont contractions pour exister uniquement sur la pile d’exécution.</span><span class="sxs-lookup"><span data-stu-id="6e328-110">All the above problems would be alleviated if instances of `Span<T>` are constrained to exist only on the execution stack.</span></span> 

<span data-ttu-id="6e328-111">Un autre problème survient en raison de la composition.</span><span class="sxs-lookup"><span data-stu-id="6e328-111">An additional problem arises due to composition.</span></span> <span data-ttu-id="6e328-112">Il est généralement préférable de créer des types de données plus complexes qui incorporaient des instances `Span<T>` et `ReadOnlySpan<T>`.</span><span class="sxs-lookup"><span data-stu-id="6e328-112">It would be generally desirable to build more complex data types that would embed `Span<T>` and `ReadOnlySpan<T>` instances.</span></span> <span data-ttu-id="6e328-113">Ces types composites devraient être des structs et partager tous les dangers et exigences de `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="6e328-113">Such composite types would have to be structs and would share all the hazards and requirements of `Span<T>`.</span></span> <span data-ttu-id="6e328-114">Par conséquent, les règles de sécurité décrites ici doivent être affichées en fonction de l’ensemble des **_types de type REF_** .</span><span class="sxs-lookup"><span data-stu-id="6e328-114">As a result the safety rules described here should be viewed as applicable to the whole range of **_ref-like types_**.</span></span>

<span data-ttu-id="6e328-115">La [spécification de langage brouillon](#draft-language-specification) est destinée à garantir que les valeurs d’un type de type REF se produisent uniquement sur la pile.</span><span class="sxs-lookup"><span data-stu-id="6e328-115">The [draft language specification](#draft-language-specification) is intended to ensure that values of a ref-like type occurs only on the stack.</span></span>

## <a name="generalized-ref-like-types-in-source-code"></a><span data-ttu-id="6e328-116">Types de `ref-like` généralisés dans le code source</span><span class="sxs-lookup"><span data-stu-id="6e328-116">Generalized `ref-like` types in source code</span></span>

<span data-ttu-id="6e328-117">`ref-like` structs sont explicitement marqués dans le code source à l’aide du modificateur `ref` :</span><span class="sxs-lookup"><span data-stu-id="6e328-117">`ref-like` structs are explicitly marked in the source code using `ref` modifier:</span></span>

```csharp
ref struct TwoSpans<T>
{
    // can have ref-like instance fields
    public Span<T> first;
    public Span<T> second;
} 

// error: arrays of ref-like types are not allowed. 
TwoSpans<T>[] arr = null;

```

<span data-ttu-id="6e328-118">La désignation d’un struct comme REF permet à la structure d’avoir des champs d’instance de type REF et rend également toutes les exigences des types de type REF applicables à la structure.</span><span class="sxs-lookup"><span data-stu-id="6e328-118">Designating a struct as ref-like will allow the struct to have ref-like instance fields and will also make all the requirements of ref-like types applicable to the struct.</span></span> 

## <a name="metadata-representation-or-ref-like-structs"></a><span data-ttu-id="6e328-119">Structures de représentation de métadonnées ou de type Ref</span><span class="sxs-lookup"><span data-stu-id="6e328-119">Metadata representation or ref-like structs</span></span>

<span data-ttu-id="6e328-120">Les structs de type REF sont marqués avec l’attribut **System. Runtime. CompilerServices. IsRefLikeAttribute** .</span><span class="sxs-lookup"><span data-stu-id="6e328-120">Ref-like structs will be marked with **System.Runtime.CompilerServices.IsRefLikeAttribute** attribute.</span></span>

<span data-ttu-id="6e328-121">L’attribut sera ajouté aux bibliothèques de base courantes, telles que `mscorlib`.</span><span class="sxs-lookup"><span data-stu-id="6e328-121">The attribute will be added to common base libraries such as `mscorlib`.</span></span> <span data-ttu-id="6e328-122">Dans un cas, si l’attribut n’est pas disponible, le compilateur génère un en interne similaire à d’autres attributs incorporés à la demande, tels que `IsReadOnlyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="6e328-122">In a case if the attribute is not available, compiler will generate an internal one similarly to other embedded-on-demand attributes such as `IsReadOnlyAttribute`.</span></span>

<span data-ttu-id="6e328-123">Une mesure supplémentaire sera prise pour empêcher l’utilisation de structs de type REF dans les compilateurs qui ne sont pas familiarisés avec les règles de C# sécurité (y compris les compilateurs antérieurs à celui dans lequel cette fonctionnalité est implémentée).</span><span class="sxs-lookup"><span data-stu-id="6e328-123">An additional measure will be taken to prevent the use of ref-like structs in compilers not familiar with the safety rules (this includes C# compilers prior to the one in which this feature is implemented).</span></span> 

<span data-ttu-id="6e328-124">Si vous n’avez pas d’autres alternatives qui fonctionnent dans les compilateurs anciens sans maintenance, un attribut `Obsolete` avec une chaîne connue est ajouté à tous les structs de type Ref.</span><span class="sxs-lookup"><span data-stu-id="6e328-124">Having no other good alternatives that work in old compilers without servicing, an `Obsolete` attribute with a known string will be added to all ref-like structs.</span></span> <span data-ttu-id="6e328-125">Les compilateurs qui savent utiliser les types de type REF ignorent cette forme particulière de `Obsolete`.</span><span class="sxs-lookup"><span data-stu-id="6e328-125">Compilers that know how to use ref-like types will ignore this particular form of `Obsolete`.</span></span>

<span data-ttu-id="6e328-126">Représentation classique des métadonnées :</span><span class="sxs-lookup"><span data-stu-id="6e328-126">A typical metadata representation:</span></span>

```csharp
    [IsRefLike]
    [Obsolete("Types with embedded references are not supported in this version of your compiler.")]
    public struct TwoSpans<T>
    {
       // . . . .
    }
```

<span data-ttu-id="6e328-127">Remarque : ce n’est pas l’objectif de faire en sorte que toute utilisation de types de type REF sur les compilateurs anciens échoue 100%.</span><span class="sxs-lookup"><span data-stu-id="6e328-127">NOTE: it is not the goal to make it so that any use of ref-like types on old compilers fails 100%.</span></span> <span data-ttu-id="6e328-128">Ce qui est difficile à atteindre et n’est pas strictement nécessaire.</span><span class="sxs-lookup"><span data-stu-id="6e328-128">That is hard to achieve and is not strictly necessary.</span></span> <span data-ttu-id="6e328-129">Par exemple, il serait toujours possible de contourner le `Obsolete` à l’aide de code dynamique ou, par exemple, en créant un tableau de types de type REF à travers la réflexion.</span><span class="sxs-lookup"><span data-stu-id="6e328-129">For example there would always be a way to get around the `Obsolete` using dynamic code or, for example, creating an array of ref-like types through reflection.</span></span>

<span data-ttu-id="6e328-130">En particulier, si l’utilisateur souhaite réellement placer un `Obsolete` ou un attribut `Deprecated` sur un type de type REF, nous n’aurons pas d’autre choix que d’émettre un attribut prédéfini, car `Obsolete` attribut ne peut pas être appliqué plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="6e328-130">In particular, if user wants to actually put an `Obsolete` or `Deprecated` attribute on a ref-like type, we will have no choice other than not emitting the predefined one since `Obsolete` attribute cannot be applied more than once..</span></span>  

## <a name="examples"></a><span data-ttu-id="6e328-131">Exemples :</span><span class="sxs-lookup"><span data-stu-id="6e328-131">Examples:</span></span>

```csharp
SpanLikeType M1(ref SpanLikeType x, Span<byte> y)
{
    // this is all valid, unconcerned with stack-referring stuff
    var local = new SpanLikeType(y);
    x = local;
    return x;
}

void Test1(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    // this is allowed
    stackReferring2 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    stackReferring2 = M1(ref param1, stackReferring1);

    // this is NOT allowed
    param1 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    param2 = stackReferring1.Slice(10);

    // this is allowed
    param1 = new SpanLikeType(param2);

    // this is allowed
    stackReferring2 = param1;
}

ref SpanLikeType M2(ref SpanLikeType x)
{
    return ref x;
}

ref SpanLikeType Test2(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    ref var stackReferring3 = M2(ref stackReferring2);

    // this is allowed
    stackReferring3 = M1(ref stackReferring2, stackReferring1);

    // this is allowed
    M2(ref stackReferring3) = stackReferring2;

    // this is NOT allowed
    M1(ref param1) = stackReferring2;

    // this is NOT allowed
    param1 = stackReferring3;

    // this is NOT allowed
    return ref stackReferring3;

    // this is allowed
    return ref param1;
}

```

----------------

## <a name="draft-language-specification"></a><span data-ttu-id="6e328-132">Spécification du langage brouillon</span><span class="sxs-lookup"><span data-stu-id="6e328-132">Draft language specification</span></span>

<span data-ttu-id="6e328-133">Ci-dessous, nous décrivons un ensemble de règles de sécurité pour les types de type REF (`ref struct`s) pour garantir que les valeurs de ces types se produisent uniquement sur la pile.</span><span class="sxs-lookup"><span data-stu-id="6e328-133">Below we describe a set of safety rules for ref-like types (`ref struct`s) to ensure that values of these types occur only on the stack.</span></span> <span data-ttu-id="6e328-134">Un ensemble de règles de sécurité différent, plus simple, serait possible si les variables locales ne peuvent pas être passées par référence.</span><span class="sxs-lookup"><span data-stu-id="6e328-134">A different, simpler set of safety rules would be possible if locals cannot be passed by reference.</span></span> <span data-ttu-id="6e328-135">Cette spécification permet également la réaffectation sécurisée des variables locales Ref.</span><span class="sxs-lookup"><span data-stu-id="6e328-135">This specification would also permit the safe reassignment of ref locals.</span></span>

### <a name="overview"></a><span data-ttu-id="6e328-136">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="6e328-136">Overview</span></span>

<span data-ttu-id="6e328-137">Nous associons à chaque expression au moment de la compilation le concept de l’étendue à laquelle l’expression est autorisée à échapper, « sans risque ».</span><span class="sxs-lookup"><span data-stu-id="6e328-137">We associate with each expression at compile-time the concept of what scope that expression is permitted to escape to, "safe-to-escape".</span></span> <span data-ttu-id="6e328-138">De même, pour chaque lvalue, nous gérons un concept de l’étendue dans laquelle une référence est autorisée à échapper, « Ref-Safe-to-Escape ».</span><span class="sxs-lookup"><span data-stu-id="6e328-138">Similarly, for each lvalue we maintain a concept of what scope a reference to it is permitted to escape to, "ref-safe-to-escape".</span></span> <span data-ttu-id="6e328-139">Pour une expression lvalue donnée, elles peuvent être différentes.</span><span class="sxs-lookup"><span data-stu-id="6e328-139">For a given lvalue expression, these may be different.</span></span>

<span data-ttu-id="6e328-140">Celles-ci sont analogues au « Safe to Return » de la fonctionnalité de variables locales REF, mais elles sont plus précises.</span><span class="sxs-lookup"><span data-stu-id="6e328-140">These are analogous to the "safe to return" of the ref locals feature, but it is more fine-grained.</span></span> <span data-ttu-id="6e328-141">Où le « sans risque » d’une expression enregistre uniquement si (ou non) il peut échapper à la méthode englobante dans son ensemble, les enregistrements sécurisés de l’échappement qui peuvent être échappés (ce qui peut ne pas dépasser le cadre d’une séquence d’échappement).</span><span class="sxs-lookup"><span data-stu-id="6e328-141">Where the "safe-to-return" of an expression records only whether (or not) it may escape the enclosing method as a whole, the safe-to-escape records which scope it may escape to (which scope it may not escape beyond).</span></span> <span data-ttu-id="6e328-142">Le mécanisme de sécurité de base est appliqué comme suit.</span><span class="sxs-lookup"><span data-stu-id="6e328-142">The basic safety mechanism is enforced as follows.</span></span> <span data-ttu-id="6e328-143">Dans le cas d’une assignation d’une expression E1 avec une étendue sécurisée à l’échappement S1, à une expression (lvalue) E2 avec une portée sécurisée à l’échappement S2, il s’agit d’une erreur si S2 est une portée plus étendue que S1.</span><span class="sxs-lookup"><span data-stu-id="6e328-143">Given an assignment from an expression E1 with a safe-to-escape scope S1, to an (lvalue) expression E2 with safe-to-escape scope S2, it is an error if S2 is a wider scope than S1.</span></span> <span data-ttu-id="6e328-144">Par construction, les deux étendues S1 et S2 se trouvent dans une relation d’imbrication, car une expression légale est toujours sûre à retourner à partir d’une portée englobant l’expression.</span><span class="sxs-lookup"><span data-stu-id="6e328-144">By construction, the two scopes S1 and S2 are in a nesting relationship, because a legal expression is always safe-to-return from some scope enclosing the expression.</span></span>

<span data-ttu-id="6e328-145">Pour le moment, c’est suffisant, à des fins d’analyse, pour prendre en charge uniquement deux étendues, externes à la méthode, et la portée de niveau supérieur de la méthode.</span><span class="sxs-lookup"><span data-stu-id="6e328-145">For the time being it is sufficient, for the purpose of the analysis, to support just two scopes - external to the method, and top-level scope of the method.</span></span> <span data-ttu-id="6e328-146">Cela est dû au fait que les valeurs de type REF avec étendues internes ne peuvent pas être créées et que les variables locales Ref ne prennent pas en charge la réaffectation.</span><span class="sxs-lookup"><span data-stu-id="6e328-146">That is because ref-like values with inner scopes cannot be created and ref locals do not support re-assignment.</span></span> <span data-ttu-id="6e328-147">Toutefois, les règles peuvent prendre en charge plus de deux niveaux de portée.</span><span class="sxs-lookup"><span data-stu-id="6e328-147">The rules, however, can support more than two scope levels.</span></span>

<span data-ttu-id="6e328-148">Les règles précises pour calculer l’état de *retour sécurisé* d’une expression et les règles régissant la légalité des expressions, suivez.</span><span class="sxs-lookup"><span data-stu-id="6e328-148">The precise rules for computing the *safe-to-return* status of an expression, and the rules governing the legality of expressions, follow.</span></span>

### <a name="ref-safe-to-escape"></a><span data-ttu-id="6e328-149">Ref-sécurisé-à-Escape</span><span class="sxs-lookup"><span data-stu-id="6e328-149">ref-safe-to-escape</span></span>

<span data-ttu-id="6e328-150">La *référence de référence* est une portée, englobant une expression lvalue, à laquelle il est possible de sécuriser une référence à la lvalue pour échapper à.</span><span class="sxs-lookup"><span data-stu-id="6e328-150">The *ref-safe-to-escape* is a scope, enclosing an lvalue expression, to which it is safe for a ref to the lvalue to escape to.</span></span> <span data-ttu-id="6e328-151">Si cette étendue est la méthode entière, nous disons qu’une référence à la lvalue peut être *retournée en toute sécurité* à partir de la méthode.</span><span class="sxs-lookup"><span data-stu-id="6e328-151">If that scope is the entire method, we say that a ref to the lvalue is *safe to return* from the method.</span></span>

### <a name="safe-to-escape"></a><span data-ttu-id="6e328-152">sans risque</span><span class="sxs-lookup"><span data-stu-id="6e328-152">safe-to-escape</span></span>

<span data-ttu-id="6e328-153">La *sécurité de l’échappement* est une étendue, englobant une expression, à laquelle la valeur doit être échappée en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="6e328-153">The *safe-to-escape* is a scope, enclosing an expression, to which it is safe for the value to escape to.</span></span> <span data-ttu-id="6e328-154">Si cette portée est la méthode entière, nous disons qu’une valeur peut être *retournée en toute sécurité* à partir de la méthode.</span><span class="sxs-lookup"><span data-stu-id="6e328-154">If that scope is the entire method, we say that a the value is *safe to return* from the method.</span></span>

<span data-ttu-id="6e328-155">Une expression dont le type n’est pas un type `ref struct` est *sécurisée pour retourner* la totalité de la méthode englobante.</span><span class="sxs-lookup"><span data-stu-id="6e328-155">An expression whose type is not a `ref struct` type is *safe-to-return* from the entire enclosing method.</span></span> <span data-ttu-id="6e328-156">Dans le cas contraire, nous faisons référence aux règles ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="6e328-156">Otherwise we refer to the rules below.</span></span>

#### <a name="parameters"></a><span data-ttu-id="6e328-157">Paramètres</span><span class="sxs-lookup"><span data-stu-id="6e328-157">Parameters</span></span>

<span data-ttu-id="6e328-158">Une lvalue désignant un paramètre formel est *ref-Safe-to-Escape* (par référence) comme suit :</span><span class="sxs-lookup"><span data-stu-id="6e328-158">An lvalue designating a formal parameter is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="6e328-159">Si le paramètre est un paramètre `ref`, `out`ou `in`, il est *ref-Safe-to-Escape* de la méthode entière (par exemple, par une instruction `return ref`). dispose</span><span class="sxs-lookup"><span data-stu-id="6e328-159">If the parameter is a `ref`, `out`, or `in` parameter, it is *ref-safe-to-escape* from the entire method (e.g. by a `return ref` statement); otherwise</span></span>
- <span data-ttu-id="6e328-160">Si le paramètre est le paramètre `this` d’un type struct, il est de type *ref-Safe* pour la portée de niveau supérieur de la méthode (mais pas de la totalité de la méthode elle-même); [Exemple](#struct-this-escape)</span><span class="sxs-lookup"><span data-stu-id="6e328-160">If the parameter is the `this` parameter of a struct type, it is *ref-safe-to-escape* to the top-level scope of the method (but not from the entire method itself); [Sample](#struct-this-escape)</span></span>
- <span data-ttu-id="6e328-161">Dans le cas contraire, le paramètre est un paramètre de valeur, et il est conforme à la méthode de *référence* pour la portée de niveau supérieur de la méthode (mais pas à partir de la méthode elle-même).</span><span class="sxs-lookup"><span data-stu-id="6e328-161">Otherwise the parameter is a value parameter, and it is *ref-safe-to-escape* to the top-level scope of the method (but not from the method itself).</span></span>

<span data-ttu-id="6e328-162">Une expression qui est une valeur Rvalue désignant l’utilisation d’un paramètre formel est la sécurité de l' *échappement* (par valeur) de la méthode entière (par exemple, par une instruction `return`).</span><span class="sxs-lookup"><span data-stu-id="6e328-162">An expression that is an rvalue designating the use of a formal parameter is *safe-to-escape* (by value) from the entire method (e.g. by a `return` statement).</span></span> <span data-ttu-id="6e328-163">Cela s’applique également au paramètre `this`.</span><span class="sxs-lookup"><span data-stu-id="6e328-163">This applies to the `this` parameter as well.</span></span>

#### <a name="locals"></a><span data-ttu-id="6e328-164">Variables locales</span><span class="sxs-lookup"><span data-stu-id="6e328-164">Locals</span></span>

<span data-ttu-id="6e328-165">Une lvalue désignant une variable locale est *ref-Safe-to-Escape* (par référence) comme suit :</span><span class="sxs-lookup"><span data-stu-id="6e328-165">An lvalue designating a local variable is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="6e328-166">Si la variable est une variable `ref`, son « *ref-Safe-to-Escape* » est extrait de l’expression de *référence sécurisée à l’échappement* de son expression d’initialisation ; dispose</span><span class="sxs-lookup"><span data-stu-id="6e328-166">If the variable is a `ref` variable, then its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of its initializing expression; otherwise</span></span>
- <span data-ttu-id="6e328-167">La variable est de *référence (Ref-safe) pour* la portée dans laquelle elle a été déclarée.</span><span class="sxs-lookup"><span data-stu-id="6e328-167">The variable is *ref-safe-to-escape* the scope in which it was declared.</span></span>

<span data-ttu-id="6e328-168">Une expression qui est une rvalue désignant l’utilisation d’une variable locale est une valeur *d’échappement sécurisée* (par valeur) comme suit :</span><span class="sxs-lookup"><span data-stu-id="6e328-168">An expression that is an rvalue designating the use of a local variable is *safe-to-escape* (by value) as follows:</span></span>
- <span data-ttu-id="6e328-169">Toutefois, la règle générale ci-dessus, une variable locale dont le type n’est pas un type `ref struct` est *sécurisée pour retourner* la totalité de la méthode englobante.</span><span class="sxs-lookup"><span data-stu-id="6e328-169">But the general rule above, a local whose type is not a `ref struct` type is *safe-to-return* from the entire enclosing method.</span></span>
- <span data-ttu-id="6e328-170">Si la variable est une variable d’itération d’une boucle `foreach`, la portée *sécurisée* de la variable est la même que *celle de l’expression de la* boucle `foreach`.</span><span class="sxs-lookup"><span data-stu-id="6e328-170">If the variable is an iteration variable of a `foreach` loop, then the variable's *safe-to-escape* scope is the same as the *safe-to-escape* of the `foreach` loop's expression.</span></span>
- <span data-ttu-id="6e328-171">Une valeur locale de `ref struct` type et non initialisée au point de déclaration est *sécurisée à retourner* à partir de l’intégralité de la méthode englobante.</span><span class="sxs-lookup"><span data-stu-id="6e328-171">A local of `ref struct` type and uninitialized at the point of declaration is *safe-to-return* from the entire enclosing method.</span></span>
- <span data-ttu-id="6e328-172">Dans le cas contraire, le type de la variable est un type `ref struct` et la déclaration de la variable requiert un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="6e328-172">Otherwise the variable's type is a `ref struct` type, and the variable's declaration requires an initializer.</span></span> <span data-ttu-id="6e328-173">La portée de l' *échappement sécurisé* de la variable est la même que celle *de son* initialiseur.</span><span class="sxs-lookup"><span data-stu-id="6e328-173">The variable's *safe-to-escape* scope is the same as the *safe-to-escape* of its initializer.</span></span>

#### <a name="field-reference"></a><span data-ttu-id="6e328-174">Référence du champ</span><span class="sxs-lookup"><span data-stu-id="6e328-174">Field reference</span></span>

<span data-ttu-id="6e328-175">Une lvalue désignant une référence à un champ, `e.F`, est de référence conforme à la *séquence d’échappement* (par référence) comme suit :</span><span class="sxs-lookup"><span data-stu-id="6e328-175">An lvalue designating a reference to a field, `e.F`, is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="6e328-176">Si `e` est d’un type référence, il s’agit d’un type de référence ( *ref-Safe-to-Escape* ) de la méthode entière ; dispose</span><span class="sxs-lookup"><span data-stu-id="6e328-176">If `e` is of a reference type, it is *ref-safe-to-escape* from the entire method; otherwise</span></span>
- <span data-ttu-id="6e328-177">Si `e` est d’un type valeur, son « *ref-Safe-to-Escape* » est extrait de l’espace de `e`de type *Safe-to-Escape* .</span><span class="sxs-lookup"><span data-stu-id="6e328-177">If `e` is of a value type, its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of `e`.</span></span>

<span data-ttu-id="6e328-178">Rvalue désignant une référence à un champ, `e.F`, a une portée *sécurisée* qui est la même que la *valeur d’échappement sécurisée* de `e`.</span><span class="sxs-lookup"><span data-stu-id="6e328-178">An rvalue designating a reference to a field, `e.F`, has a *safe-to-escape* scope that is the same as the *safe-to-escape* of `e`.</span></span>

#### <a name="operators-including-"></a><span data-ttu-id="6e328-179">Opérateurs incluant `?:`</span><span class="sxs-lookup"><span data-stu-id="6e328-179">Operators including `?:`</span></span>

<span data-ttu-id="6e328-180">L’application d’un opérateur défini par l’utilisateur est traitée comme un appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="6e328-180">The application of a user-defined operator is treated as a method invocation.</span></span>

<span data-ttu-id="6e328-181">Pour un opérateur qui produit une rvalue, telle que `e1 + e2` ou `c ? e1 : e2`, le *niveau de sécurité d’échappement* du résultat est la portée la plus étroite parmi les opérandes d' *échappement* de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="6e328-181">For an operator that yields an rvalue, such as `e1 + e2` or `c ? e1 : e2`, the *safe-to-escape* of the result is the narrowest scope among the *safe-to-escape* of the operands of the operator.</span></span>  <span data-ttu-id="6e328-182">Par conséquent, dans le cas d’un opérateur unaire qui produit une rvalue, telle que `+e`, la sécurité de l' *échappement* du résultat est la *sécurité* de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="6e328-182">As a consequence, for a unary operator that yields an rvalue, such as `+e`, the *safe-to-escape* of the result is the *safe-to-escape* of the operand.</span></span>

<span data-ttu-id="6e328-183">Pour un opérateur qui produit une lvalue, telle que `c ? ref e1 : ref e2`</span><span class="sxs-lookup"><span data-stu-id="6e328-183">For an operator that yields an lvalue, such as `c ? ref e1 : ref e2`</span></span>
- <span data-ttu-id="6e328-184">le *ref-Safe-to-Escape* du résultat est l’étendue la plus étroite parmi les opérandes de l’opérateur de *référence sécurisée* .</span><span class="sxs-lookup"><span data-stu-id="6e328-184">the *ref-safe-to-escape* of the result is the narrowest scope among the *ref-safe-to-escape* of the operands of the operator.</span></span>
- <span data-ttu-id="6e328-185">l' *échappement sécurisé* des opérandes doit s’accorder et c’est la sécurité de l' *échappement* de la lvalue résultante.</span><span class="sxs-lookup"><span data-stu-id="6e328-185">the *safe-to-escape* of the operands must agree, and that is the *safe-to-escape* of the resulting lvalue.</span></span>

#### <a name="method-invocation"></a><span data-ttu-id="6e328-186">Appel de méthode</span><span class="sxs-lookup"><span data-stu-id="6e328-186">Method invocation</span></span>

<span data-ttu-id="6e328-187">Une lvalue résultant d’un appel de méthode de retour de référence `e1.M(e2, ...)` est *ref-Safe pour échapper* la plus petite des étendues suivantes :</span><span class="sxs-lookup"><span data-stu-id="6e328-187">An lvalue resulting from a ref-returning method invocation `e1.M(e2, ...)` is *ref-safe-to-escape* the smallest of the following scopes:</span></span>
- <span data-ttu-id="6e328-188">La totalité de la méthode englobante</span><span class="sxs-lookup"><span data-stu-id="6e328-188">The entire enclosing method</span></span>
- <span data-ttu-id="6e328-189">les expressions de référence de tous les arguments `ref` et `out` ( *à* l’exclusion du récepteur)</span><span class="sxs-lookup"><span data-stu-id="6e328-189">the *ref-safe-to-escape* of all `ref` and `out` argument expressions (excluding the receiver)</span></span>
- <span data-ttu-id="6e328-190">Pour chaque paramètre `in` de la méthode, s’il existe une expression correspondante qui est une lvalue, son *ref-Safe-to-Escape*, sinon la portée englobante la plus proche</span><span class="sxs-lookup"><span data-stu-id="6e328-190">For each `in` parameter of the method, if there is a corresponding expression that is an lvalue, its *ref-safe-to-escape*, otherwise the nearest enclosing scope</span></span>
- <span data-ttu-id="6e328-191">la *sécurité* de l’échappement de toutes les expressions d’arguments (y compris le récepteur)</span><span class="sxs-lookup"><span data-stu-id="6e328-191">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

> <span data-ttu-id="6e328-192">Remarque : la dernière puce est nécessaire pour gérer le code, par exemple</span><span class="sxs-lookup"><span data-stu-id="6e328-192">Note: the last bullet is necessary to handle code such as</span></span>
> ```csharp
> var sp = new Span(...)
> return ref sp[0];
> ```
> <span data-ttu-id="6e328-193">or</span><span class="sxs-lookup"><span data-stu-id="6e328-193">or</span></span>
> ```csharp
> return ref M(sp, 0);
> ```

<span data-ttu-id="6e328-194">Une rvalue qui résulte d’un appel de méthode `e1.M(e2, ...)` est *sécurisée pour échapper* de la plus petite des étendues suivantes :</span><span class="sxs-lookup"><span data-stu-id="6e328-194">An rvalue resulting from a method invocation `e1.M(e2, ...)` is *safe-to-escape* from the smallest of the following scopes:</span></span>
- <span data-ttu-id="6e328-195">La totalité de la méthode englobante</span><span class="sxs-lookup"><span data-stu-id="6e328-195">The entire enclosing method</span></span>
- <span data-ttu-id="6e328-196">la *sécurité* de l’échappement de toutes les expressions d’arguments (y compris le récepteur)</span><span class="sxs-lookup"><span data-stu-id="6e328-196">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

#### <a name="an-rvalue"></a><span data-ttu-id="6e328-197">Rvalue</span><span class="sxs-lookup"><span data-stu-id="6e328-197">An Rvalue</span></span>
<span data-ttu-id="6e328-198">Une rvalue est de *référence pour la sécurité* de la portée la plus proche.</span><span class="sxs-lookup"><span data-stu-id="6e328-198">An rvalue is *ref-safe-to-escape* from the nearest enclosing scope.</span></span> <span data-ttu-id="6e328-199">Cela se produit par exemple dans un appel tel que `M(ref d.Length)` où `d` est de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="6e328-199">This occurs for example in an invocation such as `M(ref d.Length)` where `d` is of type `dynamic`.</span></span> <span data-ttu-id="6e328-200">Elle est également cohérente avec (et peut-être subsumes) notre gestion des arguments correspondant aux paramètres de `in`. \*</span><span class="sxs-lookup"><span data-stu-id="6e328-200">It is also consistent with (and perhaps subsumes) our handling of arguments corresponding to `in` parameters.\*</span></span>

#### <a name="property-invocations"></a><span data-ttu-id="6e328-201">Appels de propriété</span><span class="sxs-lookup"><span data-stu-id="6e328-201">Property invocations</span></span>

<span data-ttu-id="6e328-202">Un appel de propriété (`get` ou `set`) traité comme un appel de méthode de la méthode sous-jacente par les règles ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6e328-202">A property invocation (either `get` or `set`) it treated as a method invocation of the underlying method by the above rules.</span></span>

#### `stackalloc`

<span data-ttu-id="6e328-203">Une expression stackalloc est une rvalue qui est *sécurisée pour échapper* à la portée de niveau supérieur de la méthode (mais pas de la totalité de la méthode elle-même).</span><span class="sxs-lookup"><span data-stu-id="6e328-203">A stackalloc expression is an rvalue that is *safe-to-escape* to the top-level scope of the method (but not from the entire method itself).</span></span>

#### <a name="constructor-invocations"></a><span data-ttu-id="6e328-204">Appels de constructeur</span><span class="sxs-lookup"><span data-stu-id="6e328-204">Constructor invocations</span></span>

<span data-ttu-id="6e328-205">Une expression `new` qui appelle un constructeur obéit aux mêmes règles qu’un appel de méthode qui est considéré pour retourner le type en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="6e328-205">A `new` expression that invokes a constructor obeys the same rules as a method invocation that is considered to return the type being constructed.</span></span>

<span data-ttu-id="6e328-206">En outre, la *sécurité* de l’échappement n’est pas plus étendue que la plus petite *de l’ensemble* des arguments/opérandes des expressions d’initialiseur d’objet, de manière récursive, si l’initialiseur est présent.</span><span class="sxs-lookup"><span data-stu-id="6e328-206">In addition *safe-to-escape* is no wider than the smallest of the *safe-to-escape* of all arguments/operands of the object initializer expressions, recursively, if initializer is present.</span></span> 

#### <a name="span-constructor"></a><span data-ttu-id="6e328-207">Span, constructeur</span><span class="sxs-lookup"><span data-stu-id="6e328-207">Span constructor</span></span>
<span data-ttu-id="6e328-208">Le langage s’appuie sur `Span<T>` ne possédant pas de constructeur de la forme suivante :</span><span class="sxs-lookup"><span data-stu-id="6e328-208">The language relies on `Span<T>` not having a constructor of the following form:</span></span>

```csharp
void Example(ref int x)
{
    // Create a span of length one
    var span = new Span<int>(ref x); 
}
```

<span data-ttu-id="6e328-209">Un constructeur de ce type rend `Span<T>` qui sont utilisés comme champs et ne peuvent pas être différenciés d’un champ `ref`.</span><span class="sxs-lookup"><span data-stu-id="6e328-209">Such a constructor makes `Span<T>` which are used as fields indistinguishable from a `ref` field.</span></span> <span data-ttu-id="6e328-210">Les règles de sécurité décrites dans ce document dépendent de `ref` champs qui ne sont pas une C#construction valide dans, ou .net.</span><span class="sxs-lookup"><span data-stu-id="6e328-210">The safety rules described in this document depend on `ref` fields not being a valid construct in C#, or .NET.</span></span>

#### <a name="default-expressions"></a><span data-ttu-id="6e328-211">Expressions `default`</span><span class="sxs-lookup"><span data-stu-id="6e328-211">`default` expressions</span></span>

<span data-ttu-id="6e328-212">Une expression `default` est *sécurisée pour échapper* de l’intégralité de la méthode englobante.</span><span class="sxs-lookup"><span data-stu-id="6e328-212">A `default` expression is *safe-to-escape* from the entire enclosing method.</span></span>

## <a name="language-constraints"></a><span data-ttu-id="6e328-213">Contraintes de langue</span><span class="sxs-lookup"><span data-stu-id="6e328-213">Language Constraints</span></span>

<span data-ttu-id="6e328-214">Nous souhaitons garantir qu’aucune variable locale `ref`, et aucune variable de type `ref struct`, ne fasse référence à la mémoire de Stack ou aux variables qui ne sont plus actives.</span><span class="sxs-lookup"><span data-stu-id="6e328-214">We wish to ensure that no `ref` local variable, and no variable of `ref struct` type, refers to stack memory or variables that are no longer alive.</span></span> <span data-ttu-id="6e328-215">Nous avons donc les contraintes de langue suivantes :</span><span class="sxs-lookup"><span data-stu-id="6e328-215">We therefore have the following language constraints:</span></span>

- <span data-ttu-id="6e328-216">Ni un paramètre ref, ni un local de référence, ni un paramètre ou un local d’un type `ref struct` peuvent être levés dans une fonction lambda ou locale.</span><span class="sxs-lookup"><span data-stu-id="6e328-216">Neither a ref parameter, nor a ref local, nor a parameter or local of a `ref struct` type can be lifted into a lambda or local function.</span></span>

- <span data-ttu-id="6e328-217">Aucun paramètre ref ou paramètre d’un type `ref struct` ne peut être un argument sur une méthode Iterator ou une méthode `async`.</span><span class="sxs-lookup"><span data-stu-id="6e328-217">Neither a ref parameter nor a parameter of a `ref struct` type may be an argument on an iterator method or an `async` method.</span></span>

- <span data-ttu-id="6e328-218">Ni une variable locale REF, ni une variable locale d’un type `ref struct` peuvent se trouver dans la portée au point d’une instruction `yield return` ou d’une expression `await`.</span><span class="sxs-lookup"><span data-stu-id="6e328-218">Neither a ref local, nor a local of a `ref struct` type may be in scope at the point of a `yield return` statement or an `await` expression.</span></span>

- <span data-ttu-id="6e328-219">Un type de `ref struct` ne peut pas être utilisé comme argument de type ou comme type d’élément dans un type de Tuple.</span><span class="sxs-lookup"><span data-stu-id="6e328-219">A `ref struct` type may not be used as a type argument, or as an element type in a tuple type.</span></span>

- <span data-ttu-id="6e328-220">Un type de `ref struct` ne peut pas être le type déclaré d’un champ, à ceci près qu’il peut s’agir du type déclaré d’un champ d’instance d’un autre `ref struct`.</span><span class="sxs-lookup"><span data-stu-id="6e328-220">A `ref struct` type may not be the declared type of a field, except that it may be the declared type of an instance field of another `ref struct`.</span></span>

- <span data-ttu-id="6e328-221">Un type de `ref struct` ne peut pas être le type d’élément d’un tableau.</span><span class="sxs-lookup"><span data-stu-id="6e328-221">A `ref struct` type may not be the element type of an array.</span></span>

- <span data-ttu-id="6e328-222">Une valeur d’un type de `ref struct` ne peut pas être convertie :</span><span class="sxs-lookup"><span data-stu-id="6e328-222">A value of a `ref struct` type may not be boxed:</span></span>
  - <span data-ttu-id="6e328-223">Il n’y a aucune conversion d’un type de `ref struct` au type `object` ou au type `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="6e328-223">There is no conversion from a `ref struct` type to the type `object` or the type `System.ValueType`.</span></span>
  - <span data-ttu-id="6e328-224">Un type de `ref struct` ne peut pas être déclaré pour implémenter une interface</span><span class="sxs-lookup"><span data-stu-id="6e328-224">A `ref struct` type may not be declared to implement any interface</span></span>
  - <span data-ttu-id="6e328-225">Aucune méthode d’instance déclarée dans `object` ou dans `System.ValueType` mais non substituée dans un type de `ref struct` peut être appelée avec un récepteur de ce type de `ref struct`.</span><span class="sxs-lookup"><span data-stu-id="6e328-225">No instance method declared in `object` or in `System.ValueType` but not overridden in a `ref struct` type may be called with a receiver of that `ref struct` type.</span></span>
  - <span data-ttu-id="6e328-226">Aucune méthode d’instance d’un type de `ref struct` ne peut être capturée par la conversion de méthode en un type délégué.</span><span class="sxs-lookup"><span data-stu-id="6e328-226">No instance method of a `ref struct` type may be captured by method conversion to a delegate type.</span></span>

- <span data-ttu-id="6e328-227">Pour une réassignation de référence `ref e1 = ref e2`, le niveau de sécurité de la *référence à l’échappement* de l' `e2` doit être au moins aussi vaste que la portée de la *référence* de `e1`.</span><span class="sxs-lookup"><span data-stu-id="6e328-227">For a ref reassignment `ref e1 = ref e2`, the *ref-safe-to-escape* of `e2` must be at least as wide a scope as the *ref-safe-to-escape* of `e1`.</span></span>

- <span data-ttu-id="6e328-228">Dans le cas d’une instruction de retour de référence `return ref e1`, la référence de la méthode Ref- *Safe à Escape* de `e1` *doit être de* la même façon que la méthode entière.</span><span class="sxs-lookup"><span data-stu-id="6e328-228">For a ref return statement `return ref e1`, the *ref-safe-to-escape* of `e1` must be *ref-safe-to-escape* from the entire method.</span></span> <span data-ttu-id="6e328-229">(TODO : nous avons également besoin d’une règle qui `e1` doit être *sécurisée pour l’échappement* de la méthode entière, ou est-ce redondant ?)</span><span class="sxs-lookup"><span data-stu-id="6e328-229">(TODO: Do we also need a rule that `e1` must be *safe-to-escape* from the entire method, or is that redundant?)</span></span>

- <span data-ttu-id="6e328-230">Pour une instruction return `return e1`, la sécurité de l' *échappement* de `e1` doit être *sécurisée pour* la totalité de la méthode.</span><span class="sxs-lookup"><span data-stu-id="6e328-230">For a return statement `return e1`, the *safe-to-escape* of `e1` must be *safe-to-escape* from the entire method.</span></span>

- <span data-ttu-id="6e328-231">Pour une assignation `e1 = e2`, si le type de `e1` est un type de `ref struct`, l' *échappement* de `e2` doit être au moins aussi vaste que l’étendue comme étant la sécurité d' *échappement* de `e1`.</span><span class="sxs-lookup"><span data-stu-id="6e328-231">For an assignment `e1 = e2`, if the type of `e1` is a `ref struct` type, then the *safe-to-escape* of `e2` must be at least as wide a scope as the *safe-to-escape* of `e1`.</span></span>

- <span data-ttu-id="6e328-232">Dans le cas d’un appel de méthode s’il existe un argument `ref` ou `out` d’un type `ref struct` (y compris le récepteur), avec une *valeur d’échappement sans risque* , aucun argument (y compris le récepteur) peut avoir une *valeur d’échappement* plus restreinte que E1.</span><span class="sxs-lookup"><span data-stu-id="6e328-232">For a method invocation if there is a `ref` or `out` argument of a `ref struct` type (including the receiver), with *safe-to-escape* E1, then no argument (including the receiver) may have a narrower *safe-to-escape* than E1.</span></span> [<span data-ttu-id="6e328-233">Exemple</span><span class="sxs-lookup"><span data-stu-id="6e328-233">Sample</span></span>](#method-arguments-must-match)

- <span data-ttu-id="6e328-234">Une fonction locale ou une fonction anonyme ne peut pas faire référence à un paramètre local ou de `ref struct` de type déclaré dans une portée englobante.</span><span class="sxs-lookup"><span data-stu-id="6e328-234">A local function or anonymous function may not refer to a local or parameter of `ref struct` type declared in an enclosing scope.</span></span>

> <span data-ttu-id="6e328-235">***Problème d’ouverture :*** Nous avons besoin d’une règle qui nous permette de générer une erreur en cas de dépassement d’une valeur de pile d’un type de `ref struct` au niveau d’une expression await, par exemple dans le code</span><span class="sxs-lookup"><span data-stu-id="6e328-235">***Open Issue:*** We need some rule that permits us to produce an error when needing to spill a stack value of a `ref struct` type at an await expression, for example in the code</span></span>
> ```csharp
> Foo(new Span<int>(...), await e2);
> ```

## <a name="explanations"></a><span data-ttu-id="6e328-236">Descriptions</span><span class="sxs-lookup"><span data-stu-id="6e328-236">Explanations</span></span>
<span data-ttu-id="6e328-237">Ces explications et exemples aident à expliquer la raison pour laquelle de nombreuses règles de sécurité ci-dessus existent</span><span class="sxs-lookup"><span data-stu-id="6e328-237">These explanations and samples help explain why many of the safety rules above exist</span></span>

### <a name="method-arguments-must-match"></a><span data-ttu-id="6e328-238">Les arguments de méthode doivent correspondre</span><span class="sxs-lookup"><span data-stu-id="6e328-238">Method Arguments Must Match</span></span>
<span data-ttu-id="6e328-239">Lors de l’appel d’une méthode contenant un `out`, `ref` paramètre qui est un `ref struct`, y compris le récepteur, alors tous les `ref struct` doivent avoir la même durée de vie.</span><span class="sxs-lookup"><span data-stu-id="6e328-239">When invoking a method where there is an `out`, `ref` parameter that is a `ref struct` including the receiver then all of the `ref struct` need to have the same lifetime.</span></span> <span data-ttu-id="6e328-240">Cela est nécessaire, C# car doit prendre toutes les décisions concernant la sécurité de la durée de vie en fonction des informations disponibles dans la signature de la méthode et de la durée de vie des valeurs sur le site d’appel.</span><span class="sxs-lookup"><span data-stu-id="6e328-240">This is necessary because C# must make all of it's decisions around lifetime safety based on the information available in the signature of the method and the lifetime of the values at the call site.</span></span> 

<span data-ttu-id="6e328-241">Lorsqu’il y a `ref` paramètres qui sont `ref struct`, il y a éventualité qu’ils peuvent échanger autour de leur contenu.</span><span class="sxs-lookup"><span data-stu-id="6e328-241">When there are `ref` parameters that are `ref struct` then there is the possiblity they could swap around their contents.</span></span> <span data-ttu-id="6e328-242">Par conséquent, sur le site d’appel, nous devons nous assurer que tous ces échanges **potentiels** sont compatibles.</span><span class="sxs-lookup"><span data-stu-id="6e328-242">Hence at the call site we must ensure all of these **potential** swaps are compatible.</span></span> <span data-ttu-id="6e328-243">Si la langue ne s’est pas mise en application, elle autorisera un code incorrect comme celui-ci.</span><span class="sxs-lookup"><span data-stu-id="6e328-243">If the language didn't enforce that then it will allow for bad code like the following.</span></span>

```csharp
void M1(ref Span<int> s1)
{
    Span<int> s2 = stackalloc int[1];
    Swap(ref s1, ref s2);
}

void Swap(ref Span<int> x, ref int Span<int> y)
{
    // This will effectively assign the stackalloc to the s1 parameter and allow it
    // to escape to the caller of M1
    ref x = ref y; 
}
```

<span data-ttu-id="6e328-244">La restriction sur le récepteur est nécessaire car, si aucun de ses contenus n’est de niveau « Ref-Safe », il peut stocker les valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="6e328-244">The restriction on the receiver is necessary because while none of its contents are ref-safe-to-escape it can store the provided values.</span></span> <span data-ttu-id="6e328-245">Cela signifie que les durées de vie ne correspondent pas, vous pouvez créer un trou de sécurité de type de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="6e328-245">This means with mismatched lifetimes you could create a type safety hole in the following way:</span></span>

```csharp
ref struct S
{
    public Span<int> Span;

    public void Set(Span<int> span)
    {
        Span = span;
    }
}

void Broken(ref S s)
{
    Span<int> span = stackalloc int[1];

    // The result of a stackalloc is now stored in s.Span and escaped to the caller
    // of Broken
    s.Set(span); 
}
```

### <a name="struct-this-escape"></a><span data-ttu-id="6e328-246">Structurer ce caractère d’échappement</span><span class="sxs-lookup"><span data-stu-id="6e328-246">Struct This Escape</span></span>
<span data-ttu-id="6e328-247">Lorsqu’il s’agit d’étendre les règles de sécurité, la valeur `this` dans un membre d’instance est modélisée en tant que paramètre pour le membre.</span><span class="sxs-lookup"><span data-stu-id="6e328-247">When it comes to span safety rules the `this` value in an instance member is modeled as a parameter to the member.</span></span> <span data-ttu-id="6e328-248">À présent, pour un `struct` le type de `this` est `ref S` où dans un `class` il est simplement `S` (pour les membres d’un `class / struct` nommé S).</span><span class="sxs-lookup"><span data-stu-id="6e328-248">Now for a `struct` the type of `this` is actually `ref S` where in a `class` it's simply `S` (for members of a `class / struct` named S).</span></span> 

<span data-ttu-id="6e328-249">Pourtant `this` a des règles d’échappement différentes des autres paramètres de `ref`.</span><span class="sxs-lookup"><span data-stu-id="6e328-249">Yet `this` has different escaping rules than other `ref` parameters.</span></span> <span data-ttu-id="6e328-250">Plus précisément, il n’est pas de référence pour la sécurité de l’échappement, alors que d’autres paramètres sont :</span><span class="sxs-lookup"><span data-stu-id="6e328-250">Specifically it is not ref-safe-to-escape while other parameters are:</span></span>

```csharp
ref struct S
{ 
    int Field;

    // Illegal because this isn't safe to escape as ref
    ref int Get() => ref Field;

    // Legal
    ref int GetParam(ref int p) => ref p;
}
```

<span data-ttu-id="6e328-251">La raison de cette restriction a en fait peu de choses à faire avec `struct` appel de membre.</span><span class="sxs-lookup"><span data-stu-id="6e328-251">The reason for this restriction actually has little to do with `struct` member invocation.</span></span> <span data-ttu-id="6e328-252">Il existe des règles qui doivent être traitées en ce qui concerne l’appel de membre sur `struct` membres où le récepteur est un rvalue.</span><span class="sxs-lookup"><span data-stu-id="6e328-252">There are some rules that need to be worked out with respect to member invocation on `struct` members where the receiver is an rvalue.</span></span> <span data-ttu-id="6e328-253">Mais cela est très proche.</span><span class="sxs-lookup"><span data-stu-id="6e328-253">But that is very approachable.</span></span> 

<span data-ttu-id="6e328-254">La raison de cette restriction est en fait l’appel de l’interface.</span><span class="sxs-lookup"><span data-stu-id="6e328-254">The reason for this restriction is actually about interface invocation.</span></span> <span data-ttu-id="6e328-255">En particulier, il s’agit de savoir si l’exemple suivant doit ou non être compilé ;</span><span class="sxs-lookup"><span data-stu-id="6e328-255">Specifically it comes down to whether or not the following sample should or should not compile;</span></span>

```csharp
interface I1
{
    ref int Get();
}

ref int Use<T>(T p)
    where T : I1
{
    return ref p.Get();
}
```

<span data-ttu-id="6e328-256">Considérez le cas où `T` est instancié en tant que `struct`.</span><span class="sxs-lookup"><span data-stu-id="6e328-256">Consider the case where `T` is instantiated as a `struct`.</span></span> <span data-ttu-id="6e328-257">Si le paramètre `this` est de type REF-Safe-to-Escape, le retour de `p.Get` peut pointer vers la pile (en particulier, il peut s’agir d’un champ à l’intérieur du type instancié de `T`).</span><span class="sxs-lookup"><span data-stu-id="6e328-257">If the `this` parameter is ref-safe-to-escape then the return of `p.Get` could point to the stack (specifically it could be a field inside of the instantiated type of `T`).</span></span> <span data-ttu-id="6e328-258">Cela signifie que le langage n’a pas pu permettre à cet exemple de se compiler, car il pourrait retourner un `ref` à un emplacement de pile.</span><span class="sxs-lookup"><span data-stu-id="6e328-258">That means the language could not allow this sample to compile as it could be returning a `ref` to a stack location.</span></span> <span data-ttu-id="6e328-259">En revanche, si `this` n’est pas de référence pour la sécurité de l’échappement, `p.Get` ne peut pas faire référence à la pile et, par conséquent, il est possible de retourner en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="6e328-259">On the other hand if `this` is not ref-safe-to-escape then `p.Get` cannot refer to the stack and hence it's safe to return.</span></span> 

<span data-ttu-id="6e328-260">C’est la raison pour laquelle l’échappement des `this` dans un `struct` est tout à fait sur les interfaces.</span><span class="sxs-lookup"><span data-stu-id="6e328-260">This is why the escapability of `this` in a `struct` is really all about interfaces.</span></span> <span data-ttu-id="6e328-261">Il est tout à fait possible de travailler, mais il a un compromis.</span><span class="sxs-lookup"><span data-stu-id="6e328-261">It can absolutely be made to work but it has a trade off.</span></span> <span data-ttu-id="6e328-262">La conception a finalement été mise en place pour rendre les interfaces plus flexibles.</span><span class="sxs-lookup"><span data-stu-id="6e328-262">The design eventually came down in favor of making interfaces more flexible.</span></span> 

<span data-ttu-id="6e328-263">Toutefois, nous pouvons l’assouplir à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="6e328-263">There is potential for us to relax this in the future though.</span></span> 

## <a name="future-considerations"></a><span data-ttu-id="6e328-264">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="6e328-264">Future Considerations</span></span>

### <a name="length-one-spant-over-ref-values"></a><span data-ttu-id="6e328-265">Longueur une étendue\<T > sur les valeurs Ref</span><span class="sxs-lookup"><span data-stu-id="6e328-265">Length one Span\<T> over ref values</span></span>
<span data-ttu-id="6e328-266">Bien qu’il ne soit pas légal aujourd’hui, il y a des cas où la création d’une instance de longueur un `Span<T>` sur une valeur serait avantageuse :</span><span class="sxs-lookup"><span data-stu-id="6e328-266">Though not legal today there are cases where creating a length one `Span<T>` instance over a value would be beneficial:</span></span>

```csharp
void RefExample()
{
    int x = ...;

    // Today creating a length one Span<int> requires a stackalloc and a new 
    // local
    Span<int> span1 = stackalloc [] { x };
    Use(span1);
    x = span1[0]; 

    // Simpler to just allow length one span
    var span2 = new Span<int>(ref x);
    Use(span2);
}
```

<span data-ttu-id="6e328-267">Cette fonctionnalité est plus intéressante si nous remontons les restrictions sur les [mémoires tampons de taille fixe](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) , car cela permettrait à `Span<T>` instances d’une longueur encore plus longue.</span><span class="sxs-lookup"><span data-stu-id="6e328-267">This feature gets more compelling if we lift the restrictions on [fixed sized buffers](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) as it would allow for `Span<T>` instances of even greater length.</span></span> 

<span data-ttu-id="6e328-268">Si vous avez toujours besoin de descendre dans ce chemin, le langage peut le prendre en compte en veillant à ce que les instances de `Span<T>` soient uniquement dirigées vers le bas.</span><span class="sxs-lookup"><span data-stu-id="6e328-268">If there is ever a need to go down this path then the language could accommodate this by ensuring such `Span<T>` instances were downward facing only.</span></span> <span data-ttu-id="6e328-269">Il n’était jamais possible d' *échapper* à l’étendue dans laquelle ils ont été créés.</span><span class="sxs-lookup"><span data-stu-id="6e328-269">That is they were only ever *safe-to-escape* to the scope in which they were created.</span></span> <span data-ttu-id="6e328-270">Cela permet de s’assurer que le langage n’a jamais eu à prendre en compte une `ref` valeur d’échappement dans une méthode par le biais d’une `ref struct` retour ou d’un champ de `ref struct`.</span><span class="sxs-lookup"><span data-stu-id="6e328-270">This ensure the language never had to consider a `ref` value escaping a method via a `ref struct` return or field of `ref struct`.</span></span> <span data-ttu-id="6e328-271">Cela nécessitera probablement également des modifications supplémentaires pour reconnaître de tels constructeurs comme la capture d’un paramètre `ref` de cette manière.</span><span class="sxs-lookup"><span data-stu-id="6e328-271">This would likely also require further changes to recognize such constructors as capturing a `ref` parameter in this way though.</span></span>
