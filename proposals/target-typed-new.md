---
ms.openlocfilehash: 4e2a536bab00859b003e8d967cb1927a99a9fa21
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484537"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="16813-101">Expressions de `new` typées cibles</span><span class="sxs-lookup"><span data-stu-id="16813-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="16813-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="16813-102">[x] Proposed</span></span>
* <span data-ttu-id="16813-103">[x] [prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span><span class="sxs-lookup"><span data-stu-id="16813-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="16813-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="16813-104">[ ] Implementation</span></span>
* <span data-ttu-id="16813-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="16813-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="16813-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="16813-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="16813-107">Ne requièrent pas de spécification de type pour les constructeurs lorsque le type est connu.</span><span class="sxs-lookup"><span data-stu-id="16813-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="16813-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="16813-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="16813-109">Autorisez l’initialisation des champs sans dupliquer le type.</span><span class="sxs-lookup"><span data-stu-id="16813-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```
<span data-ttu-id="16813-110">Autoriser l’omission du type lorsqu’il peut être déduit de l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="16813-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```
<span data-ttu-id="16813-111">Instanciez un objet sans épeler le type.</span><span class="sxs-lookup"><span data-stu-id="16813-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```
## <a name="detailed-design"></a><span data-ttu-id="16813-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="16813-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="16813-113">La syntaxe de *object_creation_expression* est modifiée pour rendre le *type* facultatif quand les parenthèses sont présentes.</span><span class="sxs-lookup"><span data-stu-id="16813-113">The *object_creation_expression* syntax would be modified to make the *type* optional when parentheses are present.</span></span> <span data-ttu-id="16813-114">Cela est nécessaire pour résoudre l’ambiguïté avec *anonymous_object_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="16813-114">This is required to address the ambiguity with *anonymous_object_creation_expression*.</span></span>
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```
<span data-ttu-id="16813-115">Un `new` typé cible peut être converti en n’importe quel type.</span><span class="sxs-lookup"><span data-stu-id="16813-115">A target-typed `new` is convertible to any type.</span></span> <span data-ttu-id="16813-116">Par conséquent, il ne contribue pas à la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="16813-116">As a result, it does not contribute to overload resolution.</span></span> <span data-ttu-id="16813-117">Cela permet essentiellement d’éviter les modifications importantes non prévisibles.</span><span class="sxs-lookup"><span data-stu-id="16813-117">This is mainly to avoid unpredictable breaking changes.</span></span>

<span data-ttu-id="16813-118">La liste d’arguments et les expressions de l’initialiseur sont liées une fois que le type est déterminé.</span><span class="sxs-lookup"><span data-stu-id="16813-118">The argument list and the initializer expressions will be bound after the type is determined.</span></span>

<span data-ttu-id="16813-119">Le type de l’expression est déduit à partir du type cible qui doit être l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="16813-119">The type of the expression would be inferred from the target-type which would be required to be one of the following:</span></span>

- <span data-ttu-id="16813-120">**Tout type struct**</span><span class="sxs-lookup"><span data-stu-id="16813-120">**Any struct type**</span></span>
- <span data-ttu-id="16813-121">**Tout type référence**</span><span class="sxs-lookup"><span data-stu-id="16813-121">**Any reference type**</span></span>
- <span data-ttu-id="16813-122">**Tout paramètre de type** avec un constructeur ou une contrainte `struct`</span><span class="sxs-lookup"><span data-stu-id="16813-122">**Any type parameter** with a constructor or a `struct` constraint</span></span>

<span data-ttu-id="16813-123">avec les exceptions suivantes :</span><span class="sxs-lookup"><span data-stu-id="16813-123">with the following exceptions:</span></span>

- <span data-ttu-id="16813-124">**Types enum :** tous les types ENUM ne contiennent pas la constante zéro. il est donc préférable d’utiliser le membre enum explicite.</span><span class="sxs-lookup"><span data-stu-id="16813-124">**Enum types:** not all enum types contain the constant zero, so it should be desirable to use the explicit enum member.</span></span>
- <span data-ttu-id="16813-125">**Types d’interface :** il s’agit d’une fonctionnalité de niche qui doit être préférable pour mentionner explicitement le type.</span><span class="sxs-lookup"><span data-stu-id="16813-125">**Interface types:** this is a niche feature and it should be preferable to explicitly mention the type.</span></span>
- <span data-ttu-id="16813-126">**Types tableau :** les tableaux ont besoin d’une syntaxe spéciale pour fournir la longueur.</span><span class="sxs-lookup"><span data-stu-id="16813-126">**Array types:** arrays need a special syntax to provide the length.</span></span>
- <span data-ttu-id="16813-127">**Constructeur par défaut de struct**: cette règle élimine tous les types primitifs et la plupart des types valeur.</span><span class="sxs-lookup"><span data-stu-id="16813-127">**Struct default constructor**: this rules out all primitive types and most value types.</span></span> <span data-ttu-id="16813-128">Si vous souhaitez utiliser la valeur par défaut de ces types, vous pouvez écrire `default` à la place.</span><span class="sxs-lookup"><span data-stu-id="16813-128">If you wanted to use the default value of such types you could write `default` instead.</span></span>

<span data-ttu-id="16813-129">Tous les autres types qui ne sont pas autorisés dans le *object_creation_expression* sont également exclus, par exemple les types pointeur.</span><span class="sxs-lookup"><span data-stu-id="16813-129">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>

> <span data-ttu-id="16813-130">**Problème d’ouverture :** devons-nous autoriser les délégués et les tuples en tant que type cible ?</span><span class="sxs-lookup"><span data-stu-id="16813-130">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="16813-131">Les règles ci-dessus incluent les délégués (type référence) et les tuples (type struct).</span><span class="sxs-lookup"><span data-stu-id="16813-131">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="16813-132">Bien que les deux types soient constructible, si le type est déduire, une fonction anonyme ou un littéral de tuple peut déjà être utilisé.</span><span class="sxs-lookup"><span data-stu-id="16813-132">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found

var x = new() == (1, 2); // ruled out by "use of struct default constructor"
var x = new(1, 2) == (1, 2) // "new" is redundant
```


> <span data-ttu-id="16813-133">**Problème d’ouverture :** devons-nous autoriser `throw new()` avec `Exception` comme type cible ?</span><span class="sxs-lookup"><span data-stu-id="16813-133">**Open Issue:** should we allow `throw new()` with `Exception` as the target-type?</span></span>

<span data-ttu-id="16813-134">Nous avons `throw null` aujourd’hui, mais pas `throw default` (même si cela aurait le même effet).</span><span class="sxs-lookup"><span data-stu-id="16813-134">We have `throw null` today, but not `throw default` (though it would have the same effect).</span></span> <span data-ttu-id="16813-135">En revanche, `throw new()` peut être utile en tant que raccourci pour `throw new Exception(...)`.</span><span class="sxs-lookup"><span data-stu-id="16813-135">On the other hand, `throw new()` could be actually useful as a shorthand for `throw new Exception(...)`.</span></span> <span data-ttu-id="16813-136">Notez qu’il est déjà autorisé par la spécification actuelle.</span><span class="sxs-lookup"><span data-stu-id="16813-136">Note that it is already allowed by the current specification.</span></span> <span data-ttu-id="16813-137">`Exception` est un type référence, et la spécification de l’instruction throw indique que l’expression est convertie en `Exception`.</span><span class="sxs-lookup"><span data-stu-id="16813-137">`Exception` is a reference type, and the specification for the throw statement says that the expression is converted to `Exception`.</span></span>

> <span data-ttu-id="16813-138">**Problème d’ouverture :** devons-nous autoriser les utilisations d’un `new` de type cible avec des opérateurs arithmétiques et de comparaison définis par l’utilisateur ?</span><span class="sxs-lookup"><span data-stu-id="16813-138">**Open Issue:** should we allow usages of a target-typed `new` with user-defined comparison and arithmetic operators?</span></span>

<span data-ttu-id="16813-139">À des fins de comparaison, `default` prend en charge uniquement les opérateurs d’égalité (définis par l’utilisateur et intégrés).</span><span class="sxs-lookup"><span data-stu-id="16813-139">For comparison, `default` only supports equality (user-defined and built-in) operators.</span></span> <span data-ttu-id="16813-140">Serait-il judicieux de prendre en charge d’autres opérateurs aussi bien pour `new()` ?</span><span class="sxs-lookup"><span data-stu-id="16813-140">Would it make sense to support other operators for `new()` as well?</span></span>

## <a name="drawbacks"></a><span data-ttu-id="16813-141">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="16813-141">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="16813-142">Aucun.</span><span class="sxs-lookup"><span data-stu-id="16813-142">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="16813-143">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="16813-143">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="16813-144">La plupart des plaintes sur les types trop longs pour être dupliqués dans l’initialisation de champ concerne les *arguments de type* qui ne sont pas le type lui-même, mais nous pourrions déduire uniquement les arguments de type comme `new Dictionary(...)` (ou similaire) et déduire les arguments de type localement à partir des arguments ou de l’initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="16813-144">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="16813-145">Questions</span><span class="sxs-lookup"><span data-stu-id="16813-145">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="16813-146">Devons-nous interdire les utilisations dans les arborescences d’expressions ?</span><span class="sxs-lookup"><span data-stu-id="16813-146">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="16813-147">º</span><span class="sxs-lookup"><span data-stu-id="16813-147">(no)</span></span>
- <span data-ttu-id="16813-148">Comment la fonctionnalité interagit avec `dynamic` arguments ?</span><span class="sxs-lookup"><span data-stu-id="16813-148">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="16813-149">(aucun traitement spécial)</span><span class="sxs-lookup"><span data-stu-id="16813-149">(no special treatment)</span></span>
- <span data-ttu-id="16813-150">Comment IntelliSense doit fonctionner avec `new()`?</span><span class="sxs-lookup"><span data-stu-id="16813-150">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="16813-151">(uniquement s’il existe un seul type cible)</span><span class="sxs-lookup"><span data-stu-id="16813-151">(only when there is a single target-type)</span></span>
## <a name="design-meetings"></a><span data-ttu-id="16813-152">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="16813-152">Design meetings</span></span>

- [<span data-ttu-id="16813-153">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="16813-153">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
