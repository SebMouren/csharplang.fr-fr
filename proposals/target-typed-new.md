---
ms.openlocfilehash: f000dda7eeb1c4f17c26f94c326a12a9d0014288
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281968"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="02c34-101">Expressions de `new` typées cibles</span><span class="sxs-lookup"><span data-stu-id="02c34-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="02c34-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="02c34-102">[x] Proposed</span></span>
* <span data-ttu-id="02c34-103">[x] [prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span><span class="sxs-lookup"><span data-stu-id="02c34-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="02c34-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="02c34-104">[ ] Implementation</span></span>
* <span data-ttu-id="02c34-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="02c34-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="02c34-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="02c34-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="02c34-107">Ne requièrent pas de spécification de type pour les constructeurs lorsque le type est connu.</span><span class="sxs-lookup"><span data-stu-id="02c34-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="02c34-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="02c34-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="02c34-109">Autorisez l’initialisation des champs sans dupliquer le type.</span><span class="sxs-lookup"><span data-stu-id="02c34-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

<span data-ttu-id="02c34-110">Autoriser l’omission du type lorsqu’il peut être déduit de l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="02c34-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

<span data-ttu-id="02c34-111">Instanciez un objet sans épeler le type.</span><span class="sxs-lookup"><span data-stu-id="02c34-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```

## <a name="specification"></a><span data-ttu-id="02c34-112">Caractéristique</span><span class="sxs-lookup"><span data-stu-id="02c34-112">Specification</span></span>
[design]: #detailed-design

<span data-ttu-id="02c34-113">Une nouvelle forme syntaxique, *target_typed_new* de la *object_creation_expression* est acceptée dans laquelle le *type* est facultatif.</span><span class="sxs-lookup"><span data-stu-id="02c34-113">A new syntactic form, *target_typed_new* of the *object_creation_expression* is accepted in which the *type* is optional.</span></span>

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    | target_typed_new
    ;
target_typed_new
    : 'new' '(' argument_list? ')' object_or_collection_initializer?
    ;
```

<span data-ttu-id="02c34-114">Une expression *target_typed_new* n’a pas de type.</span><span class="sxs-lookup"><span data-stu-id="02c34-114">A *target_typed_new* expression does not have a type.</span></span> <span data-ttu-id="02c34-115">Toutefois, il existe une nouvelle *conversion de création d’objet* qui est une conversion implicite à partir d’une expression, qui existe d’un *target_typed_new* à chaque type.</span><span class="sxs-lookup"><span data-stu-id="02c34-115">However, there is a new *object creation conversion* that is an implicit conversion from expression, that exists from a *target_typed_new* to every type.</span></span>

<span data-ttu-id="02c34-116">Étant donné un type cible `T`, le type `T0` est le type sous-jacent de `T`si `T` est une instance de `System.Nullable`.</span><span class="sxs-lookup"><span data-stu-id="02c34-116">Given a target type `T`, the type `T0` is `T`'s underlying type if `T` is an instance of `System.Nullable`.</span></span> <span data-ttu-id="02c34-117">Sinon `T0` est `T`.</span><span class="sxs-lookup"><span data-stu-id="02c34-117">Otherwise `T0` is `T`.</span></span> <span data-ttu-id="02c34-118">La signification d’une expression *target_typed_new* qui est convertie en type `T` est identique à la signification d’un *object_creation_expression* correspondant qui spécifie `T0` comme type.</span><span class="sxs-lookup"><span data-stu-id="02c34-118">The meaning of a *target_typed_new* expression that is converted to the type `T` is the same as the meaning of a corresponding *object_creation_expression* that specifies `T0` as the type.</span></span>

<span data-ttu-id="02c34-119">Il s’agit d’une erreur au moment de la compilation si un *target_typed_new* est utilisé comme opérande d’un opérateur unaire ou binaire, ou s’il est utilisé là où il n’est pas soumis à une *conversion de création d’objet*.</span><span class="sxs-lookup"><span data-stu-id="02c34-119">It is a compile-time error if a *target_typed_new* is used as an operand of a unary or binary operator, or if it is used where it is not subject to an *object creation conversion*.</span></span>

> <span data-ttu-id="02c34-120">**Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?</span><span class="sxs-lookup"><span data-stu-id="02c34-120">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="02c34-121">Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct).</span><span class="sxs-lookup"><span data-stu-id="02c34-121">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="02c34-122">Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.</span><span class="sxs-lookup"><span data-stu-id="02c34-122">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // OK; same as (0, 0)
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a><span data-ttu-id="02c34-123">Divers</span><span class="sxs-lookup"><span data-stu-id="02c34-123">Miscellaneous</span></span>

<span data-ttu-id="02c34-124">Les conséquences de la spécification sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="02c34-124">The following are consequences of the specification:</span></span>

- <span data-ttu-id="02c34-125">`throw new()` est autorisé (le type de cible est `System.Exception`)</span><span class="sxs-lookup"><span data-stu-id="02c34-125">`throw new()` is allowed (the target type is `System.Exception`)</span></span>
- <span data-ttu-id="02c34-126">Le `new` de type cible n’est pas autorisé avec les opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="02c34-126">Target-typed `new` is not allowed with binary operators.</span></span>
- <span data-ttu-id="02c34-127">Elle n’est pas autorisée lorsqu’il n’y a aucun type à cibler : les opérateurs unaires, la collection d’un `foreach`dans un `using`, dans une déconstruction, dans une expression `await`, en tant que propriété de type anonyme (`new { Prop = new() }`), dans une instruction `lock`, dans une `sizeof`, dans une instruction `fixed`, dans un accès aux membres (`new().field`), dans une opération distribuée dynamiquement (`someDynamic.Method(new())`), dans une requête LINQ, comme opérande de l’opérateur `is`, comme opérande gauche de l’opérateur `??` ,  ...</span><span class="sxs-lookup"><span data-stu-id="02c34-127">It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...</span></span>
- <span data-ttu-id="02c34-128">Il est également interdit en tant que `ref`.</span><span class="sxs-lookup"><span data-stu-id="02c34-128">It is also disallowed as a `ref`.</span></span>
- <span data-ttu-id="02c34-129">Les genres de types suivants ne sont pas autorisés en tant que cibles de la conversion</span><span class="sxs-lookup"><span data-stu-id="02c34-129">The following kinds of types are not permitted as targets of the conversion</span></span>
  - <span data-ttu-id="02c34-130">**Types enum :** `new()` fonctionnera (comme `new Enum()` fonctionne pour attribuer la valeur par défaut), mais `new(1)` ne fonctionnera pas car les types enum n’ont pas de constructeur.</span><span class="sxs-lookup"><span data-stu-id="02c34-130">**Enum types:** `new()` will work (as `new Enum()` works to give the default value), but `new(1)` will not work as enum types do not have a constructor.</span></span>
  - <span data-ttu-id="02c34-131">**Types d’interface :** Cela fonctionnerait de la même façon que l’expression de création correspondante pour les types COM.</span><span class="sxs-lookup"><span data-stu-id="02c34-131">**Interface types:** This would work the same as the corresponding creation expression for COM types.</span></span>
  - <span data-ttu-id="02c34-132">**Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.</span><span class="sxs-lookup"><span data-stu-id="02c34-132">**Array types:** arrays need a special syntax to provide the length.</span></span>    
  - <span data-ttu-id="02c34-133">**dynamique :** nous n’autorisons pas `new dynamic()`, donc nous n’autorisons pas `new()` avec `dynamic` comme type de cible.</span><span class="sxs-lookup"><span data-stu-id="02c34-133">**dynamic:** we don't allow `new dynamic()`, so we don't allow `new()` with `dynamic` as a target type.</span></span>
  - <span data-ttu-id="02c34-134">**tuples :** Celles-ci ont la même signification que la création d’un objet à l’aide du type sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="02c34-134">**tuples:** These have the same meaning as an object creation using the underlying type.</span></span>
  - <span data-ttu-id="02c34-135">Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.</span><span class="sxs-lookup"><span data-stu-id="02c34-135">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>   

## <a name="drawbacks"></a><span data-ttu-id="02c34-136">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="02c34-136">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="02c34-137">Il y avait des problèmes avec les `new` de type cible qui créent des catégories de modifications avec rupture, mais nous avons déjà cela avec `null` et `default`, et cela n’a pas été un problème significatif.</span><span class="sxs-lookup"><span data-stu-id="02c34-137">There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.</span></span>

## <a name="alternatives"></a><span data-ttu-id="02c34-138">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="02c34-138">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="02c34-139">La plupart des plaintes sur les types trop longs pour être dupliqués dans l’initialisation de champ concerne les *arguments de type* qui ne sont pas le type lui-même, mais nous pourrions déduire uniquement les arguments de type comme `new Dictionary(...)` (ou similaire) et déduire les arguments de type localement à partir des arguments ou de l’initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="02c34-139">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="02c34-140">Questions</span><span class="sxs-lookup"><span data-stu-id="02c34-140">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="02c34-141">Devons-nous interdire les utilisations dans les arborescences d’expressions ?</span><span class="sxs-lookup"><span data-stu-id="02c34-141">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="02c34-142">º</span><span class="sxs-lookup"><span data-stu-id="02c34-142">(no)</span></span>
- <span data-ttu-id="02c34-143">Comment la fonctionnalité interagit avec `dynamic` arguments ?</span><span class="sxs-lookup"><span data-stu-id="02c34-143">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="02c34-144">(aucun traitement spécial)</span><span class="sxs-lookup"><span data-stu-id="02c34-144">(no special treatment)</span></span>
- <span data-ttu-id="02c34-145">Comment IntelliSense doit fonctionner avec `new()`?</span><span class="sxs-lookup"><span data-stu-id="02c34-145">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="02c34-146">(uniquement s’il existe un seul type cible)</span><span class="sxs-lookup"><span data-stu-id="02c34-146">(only when there is a single target-type)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="02c34-147">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="02c34-147">Design meetings</span></span>

- [<span data-ttu-id="02c34-148">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="02c34-148">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [<span data-ttu-id="02c34-149">LDM-2018-05-21</span><span class="sxs-lookup"><span data-stu-id="02c34-149">LDM-2018-05-21</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [<span data-ttu-id="02c34-150">LDM-2018-06-25</span><span class="sxs-lookup"><span data-stu-id="02c34-150">LDM-2018-06-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [<span data-ttu-id="02c34-151">LDM-2018-08-22</span><span class="sxs-lookup"><span data-stu-id="02c34-151">LDM-2018-08-22</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [<span data-ttu-id="02c34-152">LDM-2018-10-17</span><span class="sxs-lookup"><span data-stu-id="02c34-152">LDM-2018-10-17</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
- [<span data-ttu-id="02c34-153">LDM-2020-03-25</span><span class="sxs-lookup"><span data-stu-id="02c34-153">LDM-2020-03-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md)
