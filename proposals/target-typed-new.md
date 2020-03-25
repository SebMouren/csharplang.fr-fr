---
ms.openlocfilehash: 07b4afe4a3fcbf10c978f05e642dfd8a47d53ea5
ms.sourcegitcommit: 194a043db72b9244f8db45db326cc82de6cec965
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80217201"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="bf89d-101">Expressions de `new` typées cibles</span><span class="sxs-lookup"><span data-stu-id="bf89d-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="bf89d-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="bf89d-102">[x] Proposed</span></span>
* <span data-ttu-id="bf89d-103">[x] [prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span><span class="sxs-lookup"><span data-stu-id="bf89d-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="bf89d-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="bf89d-104">[ ] Implementation</span></span>
* <span data-ttu-id="bf89d-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="bf89d-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="bf89d-106">Récapitulatif</span><span class="sxs-lookup"><span data-stu-id="bf89d-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="bf89d-107">Ne requièrent pas de spécification de type pour les constructeurs lorsque le type est connu.</span><span class="sxs-lookup"><span data-stu-id="bf89d-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="bf89d-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="bf89d-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="bf89d-109">Autorisez l’initialisation des champs sans dupliquer le type.</span><span class="sxs-lookup"><span data-stu-id="bf89d-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

<span data-ttu-id="bf89d-110">Autoriser l’omission du type lorsqu’il peut être déduit de l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="bf89d-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

<span data-ttu-id="bf89d-111">Instanciez un objet sans épeler le type.</span><span class="sxs-lookup"><span data-stu-id="bf89d-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```

## <a name="detailed-design"></a><span data-ttu-id="bf89d-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="bf89d-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="bf89d-113">La syntaxe de *object_creation_expression* est modifiée pour rendre le *type* facultatif quand les parenthèses sont présentes.</span><span class="sxs-lookup"><span data-stu-id="bf89d-113">The *object_creation_expression* syntax would be modified to make the *type* optional when parentheses are present.</span></span> <span data-ttu-id="bf89d-114">Cela est nécessaire pour résoudre l’ambiguïté avec *anonymous_object_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="bf89d-114">This is required to address the ambiguity with *anonymous_object_creation_expression*.</span></span>
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```

<span data-ttu-id="bf89d-115">Un `new` typé cible peut être converti en n’importe quel type.</span><span class="sxs-lookup"><span data-stu-id="bf89d-115">A target-typed `new` is convertible to any type.</span></span> <span data-ttu-id="bf89d-116">Par conséquent, il ne contribue pas à la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="bf89d-116">As a result, it does not contribute to overload resolution.</span></span> <span data-ttu-id="bf89d-117">Cela permet essentiellement d’éviter les modifications importantes non prévisibles.</span><span class="sxs-lookup"><span data-stu-id="bf89d-117">This is mainly to avoid unpredictable breaking changes.</span></span>

<span data-ttu-id="bf89d-118">La liste d’arguments et les expressions de l’initialiseur sont liées une fois que le type est déterminé.</span><span class="sxs-lookup"><span data-stu-id="bf89d-118">The argument list and the initializer expressions will be bound after the type is determined.</span></span>

<span data-ttu-id="bf89d-119">Le type de l’expression est déduit à partir du type cible qui doit être l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="bf89d-119">The type of the expression would be inferred from the target-type which would be required to be one of the following:</span></span>

- <span data-ttu-id="bf89d-120">**Tout type struct** (y compris les types Tuple)</span><span class="sxs-lookup"><span data-stu-id="bf89d-120">**Any struct type** (including tuple types)</span></span>
- <span data-ttu-id="bf89d-121">**Tout type référence** (y compris les types délégués)</span><span class="sxs-lookup"><span data-stu-id="bf89d-121">**Any reference type** (including delegate types)</span></span>
- <span data-ttu-id="bf89d-122">**Tout paramètre de type** avec un constructeur ou une contrainte `struct`</span><span class="sxs-lookup"><span data-stu-id="bf89d-122">**Any type parameter** with a constructor or a `struct` constraint</span></span>

<span data-ttu-id="bf89d-123">avec les exceptions suivantes :</span><span class="sxs-lookup"><span data-stu-id="bf89d-123">with the following exceptions:</span></span>

- <span data-ttu-id="bf89d-124">**Types enum :** tous les types ENUM ne contiennent pas la constante zéro. il est donc préférable d’utiliser le membre enum explicite.</span><span class="sxs-lookup"><span data-stu-id="bf89d-124">**Enum types:** not all enum types contain the constant zero, so it should be desirable to use the explicit enum member.</span></span>
- <span data-ttu-id="bf89d-125">**Types d’interface :** il s’agit d’une fonctionnalité de niche qui doit être préférable pour mentionner explicitement le type.</span><span class="sxs-lookup"><span data-stu-id="bf89d-125">**Interface types:** this is a niche feature and it should be preferable to explicitly mention the type.</span></span>
- <span data-ttu-id="bf89d-126">**Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.</span><span class="sxs-lookup"><span data-stu-id="bf89d-126">**Array types:** arrays need a special syntax to provide the length.</span></span>
- <span data-ttu-id="bf89d-127">**dynamique :** nous n’autorisons pas `new dynamic()`, donc nous n’autorisons pas `new()` avec `dynamic` comme type de cible.</span><span class="sxs-lookup"><span data-stu-id="bf89d-127">**dynamic:** we don't allow `new dynamic()`, so we don't allow `new()` with `dynamic` as a target type.</span></span>

<span data-ttu-id="bf89d-128">Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.</span><span class="sxs-lookup"><span data-stu-id="bf89d-128">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>

<span data-ttu-id="bf89d-129">Lorsque le type cible est un type valeur Nullable, le `new` de type cible est converti en type sous-jacent au lieu du type Nullable.</span><span class="sxs-lookup"><span data-stu-id="bf89d-129">When the target type is a nullable value type, the target-typed `new` will be converted to the underlying type instead of the nullable type.</span></span>

> <span data-ttu-id="bf89d-130">**Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?</span><span class="sxs-lookup"><span data-stu-id="bf89d-130">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="bf89d-131">Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct).</span><span class="sxs-lookup"><span data-stu-id="bf89d-131">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="bf89d-132">Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.</span><span class="sxs-lookup"><span data-stu-id="bf89d-132">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a><span data-ttu-id="bf89d-133">Divers</span><span class="sxs-lookup"><span data-stu-id="bf89d-133">Miscellaneous</span></span>

<span data-ttu-id="bf89d-134">`throw new()` n’est pas autorisé.</span><span class="sxs-lookup"><span data-stu-id="bf89d-134">`throw new()` is disallowed.</span></span>

<span data-ttu-id="bf89d-135">Le `new` de type cible n’est pas autorisé avec les opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="bf89d-135">Target-typed `new` is not allowed with binary operators.</span></span>

<span data-ttu-id="bf89d-136">Elle n’est pas autorisée lorsqu’il n’y a aucun type à cibler : les opérateurs unaires, la collection d’un `foreach`dans un `using`, dans une déconstruction, dans une expression `await`, en tant que propriété de type anonyme (`new { Prop = new() }`), dans une instruction `lock`, dans une `sizeof`, dans une instruction `fixed`, dans un accès aux membres (`new().field`), dans une opération distribuée dynamiquement (`someDynamic.Method(new())`), dans une requête LINQ, comme opérande de l’opérateur `is`, comme opérande gauche de l’opérateur `??` ,  ...</span><span class="sxs-lookup"><span data-stu-id="bf89d-136">It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...</span></span>

<span data-ttu-id="bf89d-137">Il est également interdit en tant que `ref`.</span><span class="sxs-lookup"><span data-stu-id="bf89d-137">It is also disallowed as a `ref`.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="bf89d-138">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="bf89d-138">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="bf89d-139">Il y avait des problèmes avec les `new` de type cible qui créent des catégories de modifications avec rupture, mais nous avons déjà cela avec `null` et `default`, et cela n’a pas été un problème significatif.</span><span class="sxs-lookup"><span data-stu-id="bf89d-139">There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.</span></span>

## <a name="alternatives"></a><span data-ttu-id="bf89d-140">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="bf89d-140">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="bf89d-141">La plupart des plaintes sur les types trop longs pour être dupliqués dans l’initialisation de champ concerne les *arguments de type* qui ne sont pas le type lui-même, mais nous pourrions déduire uniquement les arguments de type comme `new Dictionary(...)` (ou similaire) et déduire les arguments de type localement à partir des arguments ou de l’initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="bf89d-141">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="bf89d-142">Questions</span><span class="sxs-lookup"><span data-stu-id="bf89d-142">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="bf89d-143">Devons-nous interdire les utilisations dans les arborescences d’expressions ?</span><span class="sxs-lookup"><span data-stu-id="bf89d-143">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="bf89d-144">º</span><span class="sxs-lookup"><span data-stu-id="bf89d-144">(no)</span></span>
- <span data-ttu-id="bf89d-145">Comment la fonctionnalité interagit avec `dynamic` arguments ?</span><span class="sxs-lookup"><span data-stu-id="bf89d-145">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="bf89d-146">(aucun traitement spécial)</span><span class="sxs-lookup"><span data-stu-id="bf89d-146">(no special treatment)</span></span>
- <span data-ttu-id="bf89d-147">Comment IntelliSense doit fonctionner avec `new()`?</span><span class="sxs-lookup"><span data-stu-id="bf89d-147">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="bf89d-148">(uniquement s’il existe un seul type cible)</span><span class="sxs-lookup"><span data-stu-id="bf89d-148">(only when there is a single target-type)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="bf89d-149">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="bf89d-149">Design meetings</span></span>

- [<span data-ttu-id="bf89d-150">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="bf89d-150">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [<span data-ttu-id="bf89d-151">LDM-2018-05-21</span><span class="sxs-lookup"><span data-stu-id="bf89d-151">LDM-2018-05-21</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [<span data-ttu-id="bf89d-152">LDM-2018-06-25</span><span class="sxs-lookup"><span data-stu-id="bf89d-152">LDM-2018-06-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [<span data-ttu-id="bf89d-153">LDM-2018-08-22</span><span class="sxs-lookup"><span data-stu-id="bf89d-153">LDM-2018-08-22</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [<span data-ttu-id="bf89d-154">LDM-2018-10-17</span><span class="sxs-lookup"><span data-stu-id="bf89d-154">LDM-2018-10-17</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
