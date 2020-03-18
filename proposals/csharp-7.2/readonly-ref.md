---
ms.openlocfilehash: 39fb0aab5e8bb0d422f25fd2e92ab3d8256d3f59
ms.sourcegitcommit: b8f1103eb686c5d82e294837c9386d9b667da292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "79485111"
---
# <a name="readonly-references"></a><span data-ttu-id="88095-101">Références en lecture seule</span><span class="sxs-lookup"><span data-stu-id="88095-101">Readonly references</span></span>

* <span data-ttu-id="88095-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="88095-102">[x] Proposed</span></span>
* <span data-ttu-id="88095-103">[x] prototype</span><span class="sxs-lookup"><span data-stu-id="88095-103">[x] Prototype</span></span>
* <span data-ttu-id="88095-104">[x] implémentation : démarré</span><span class="sxs-lookup"><span data-stu-id="88095-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="88095-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="88095-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="88095-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="88095-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="88095-107">La fonctionnalité « références ReadOnly » est en fait un groupe de fonctionnalités qui tirent parti de l’efficacité du passage des variables par référence, mais sans exposer les données aux modifications :</span><span class="sxs-lookup"><span data-stu-id="88095-107">The "readonly references" feature is actually a group of features that leverage the efficiency of passing variables by reference, but without exposing the data to modifications:</span></span>  
- <span data-ttu-id="88095-108">paramètres `in`</span><span class="sxs-lookup"><span data-stu-id="88095-108">`in` parameters</span></span>
- <span data-ttu-id="88095-109">Retours `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="88095-109">`ref readonly` returns</span></span>
- <span data-ttu-id="88095-110">structs de `readonly`</span><span class="sxs-lookup"><span data-stu-id="88095-110">`readonly` structs</span></span>
- <span data-ttu-id="88095-111">`ref`/méthodes d’extension `in`</span><span class="sxs-lookup"><span data-stu-id="88095-111">`ref`/`in` extension methods</span></span>
- <span data-ttu-id="88095-112">`ref readonly` variables locales</span><span class="sxs-lookup"><span data-stu-id="88095-112">`ref readonly` locals</span></span>
- <span data-ttu-id="88095-113">`ref` des expressions conditionnelles</span><span class="sxs-lookup"><span data-stu-id="88095-113">`ref` conditional expressions</span></span>

## <a name="passing-arguments-as-readonly-references"></a><span data-ttu-id="88095-114">Passage des arguments en tant que références ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="88095-114">Passing arguments as readonly references.</span></span>

<span data-ttu-id="88095-115">Il existe déjà une proposition qui touche cette rubrique https://github.com/dotnet/roslyn/issues/115 comme un cas spécial de paramètres ReadOnly sans passer par de nombreux détails.</span><span class="sxs-lookup"><span data-stu-id="88095-115">There is an existing proposal that touches this topic https://github.com/dotnet/roslyn/issues/115 as a special case of readonly parameters without going into many details.</span></span>
<span data-ttu-id="88095-116">Ici, je veux simplement confirmer que l’idée n’est pas très nouvelle.</span><span class="sxs-lookup"><span data-stu-id="88095-116">Here I just want to acknowledge that the idea by itself is not very new.</span></span>

### <a name="motivation"></a><span data-ttu-id="88095-117">Motivation</span><span class="sxs-lookup"><span data-stu-id="88095-117">Motivation</span></span>

<span data-ttu-id="88095-118">Avant cette fonctionnalité C# , n’avait pas de moyen efficace d’exprimer un souhait de passer des variables de struct dans des appels de méthode à des fins ReadOnly sans intention de modifier.</span><span class="sxs-lookup"><span data-stu-id="88095-118">Prior to this feature C# did not have an efficient way of expressing a desire to pass struct variables into method calls for readonly purposes with no intention of modifying.</span></span> <span data-ttu-id="88095-119">Le passage d’un argument par valeur normal implique une copie, ce qui ajoute des coûts inutiles.</span><span class="sxs-lookup"><span data-stu-id="88095-119">Regular by-value argument passing implies copying, which adds unnecessary costs.</span></span>  <span data-ttu-id="88095-120">Cela permet aux utilisateurs d’utiliser un argument par référence en passant et de s’appuyer sur des commentaires/une documentation pour indiquer que les données ne sont pas censées être mutées par l’appelé.</span><span class="sxs-lookup"><span data-stu-id="88095-120">That drives users to use by-ref argument passing and rely on comments/documentation to indicate that the data is not supposed to be mutated by the callee.</span></span> <span data-ttu-id="88095-121">Ce n’est pas une bonne solution pour de nombreuses raisons.</span><span class="sxs-lookup"><span data-stu-id="88095-121">It is not a good solution for many reasons.</span></span>  
<span data-ttu-id="88095-122">Les exemples sont des opérateurs mathématiques à plusieurs vecteurs/matrices dans les bibliothèques de graphiques, comme [XNA](https://msdn.microsoft.com/library/bb194944.aspx) , dont les opérandes de référence sont purement en raison des considérations relatives aux performances.</span><span class="sxs-lookup"><span data-stu-id="88095-122">The examples are numerous - vector/matrix math operators in graphics libraries like [XNA](https://msdn.microsoft.com/library/bb194944.aspx) are known to have ref operands purely because of performance considerations.</span></span> <span data-ttu-id="88095-123">Il existe du code dans le compilateur Roslyn lui-même, qui utilise des structs pour éviter les allocations, puis les passe par référence afin d’éviter de copier les coûts.</span><span class="sxs-lookup"><span data-stu-id="88095-123">There is code in Roslyn compiler itself that uses structs to avoid allocations and then passes them by reference to avoid copying costs.</span></span>

### <a name="solution-in-parameters"></a><span data-ttu-id="88095-124">Solution (paramètres de`in`)</span><span class="sxs-lookup"><span data-stu-id="88095-124">Solution (`in` parameters)</span></span>

<span data-ttu-id="88095-125">De la même façon que les paramètres `out`, `in` paramètres sont passés comme références managées avec des garanties supplémentaires de l’appelé.</span><span class="sxs-lookup"><span data-stu-id="88095-125">Similarly to the `out` parameters, `in` parameters are passed as managed references with additional guarantees from the callee.</span></span>  
<span data-ttu-id="88095-126">Contrairement à `out` paramètres qui _doivent_ être assignés par l’appelé avant toute autre utilisation, `in` paramètres ne peuvent pas être attribués par l’appelé.</span><span class="sxs-lookup"><span data-stu-id="88095-126">Unlike `out` parameters which _must_ be assigned by the callee before any other use, `in` parameters cannot be assigned by the callee at all.</span></span>

<span data-ttu-id="88095-127">Par conséquent `in` paramètres permettent l’efficacité du passage indirect des arguments sans exposer les arguments aux mutations par l’appelé.</span><span class="sxs-lookup"><span data-stu-id="88095-127">As a result `in` parameters allow for effectiveness of indirect argument passing without exposing arguments to mutations by the callee.</span></span>

### <a name="declaring-in-parameters"></a><span data-ttu-id="88095-128">Déclarer des paramètres `in`</span><span class="sxs-lookup"><span data-stu-id="88095-128">Declaring `in` parameters</span></span>

<span data-ttu-id="88095-129">`in` paramètres sont déclarés à l’aide du mot clé `in` en tant que modificateur dans la signature de paramètre.</span><span class="sxs-lookup"><span data-stu-id="88095-129">`in` parameters are declared by using `in` keyword as a modifier in the parameter signature.</span></span>

<span data-ttu-id="88095-130">Dans tous les cas, le paramètre `in` est traité comme une variable `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-130">For all purposes the `in` parameter is treated as a `readonly` variable.</span></span> <span data-ttu-id="88095-131">La plupart des restrictions sur l’utilisation des paramètres de `in` à l’intérieur de la méthode sont les mêmes que pour les champs de `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-131">Most of the restrictions on the use of `in` parameters inside the method are the same as with `readonly` fields.</span></span>

> <span data-ttu-id="88095-132">En effet, un paramètre `in` peut représenter un champ `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-132">Indeed an `in` parameter may represent a `readonly` field.</span></span> <span data-ttu-id="88095-133">La similarité des restrictions n’est pas une coïncidence.</span><span class="sxs-lookup"><span data-stu-id="88095-133">Similarity of restrictions is not a coincidence.</span></span>

<span data-ttu-id="88095-134">Par exemple, les champs d’un paramètre `in` qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-134">For example fields of an `in` parameter which has a struct type are all recursively classified as `readonly` variables .</span></span>

```csharp
static Vector3 Add (in Vector3 v1, in Vector3 v2)
{
    // not OK!!
    v1 = default(Vector3);

    // not OK!!
    v1.X = 0;

    // not OK!!
    foo(ref v1.X);

    // OK
    return new Vector3(v1.X + v2.X, v1.Y + v2.Y, v1.Z + v2.Z);
}
```

- <span data-ttu-id="88095-135">les paramètres de `in` sont autorisés partout où des paramètres ByVal ordinaires sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="88095-135">`in` parameters are allowed anywhere where ordinary byval parameters are allowed.</span></span> <span data-ttu-id="88095-136">Cela inclut les indexeurs, les opérateurs (y compris les conversions), les délégués, les expressions lambda, les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="88095-136">This includes indexers, operators (including conversions), delegates, lambdas, local functions.</span></span>

> ```csharp
>  (in int x) => x                                                     // lambda expression  
>  TValue this[in TKey index];                                         // indexer
>  public static Vector3 operator +(in Vector3 x, in Vector3 y) => ... // operator
>  ```

- <span data-ttu-id="88095-137">`in` n’est pas autorisée en combinaison avec `out` ou avec tout ce que `out` ne combine pas avec.</span><span class="sxs-lookup"><span data-stu-id="88095-137">`in` is not allowed in combination with `out` or with anything that `out` does not combine with.</span></span>

- <span data-ttu-id="88095-138">Elle n’est pas autorisée à se surcharger sur `ref`/`out`/les différences `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-138">It is not permitted to overload on `ref`/`out`/`in` differences.</span></span>

- <span data-ttu-id="88095-139">Elle est autorisée à se surcharger sur les différences ordinaires ByVal et `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-139">It is permitted to overload on ordinary byval and `in` differences.</span></span>

- <span data-ttu-id="88095-140">Dans le cadre de OHI (surcharge, masquage, implémentation), `in` se comporte de la même façon qu’un paramètre `out`.</span><span class="sxs-lookup"><span data-stu-id="88095-140">For the purpose of OHI (Overloading, Hiding, Implementing), `in` behaves similarly to an `out` parameter.</span></span>
<span data-ttu-id="88095-141">Toutes les mêmes règles s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="88095-141">All the same rules apply.</span></span>
<span data-ttu-id="88095-142">Par exemple, la méthode de substitution devra faire correspondre `in` paramètres avec `in` paramètres d’un type convertible en identité.</span><span class="sxs-lookup"><span data-stu-id="88095-142">For example the overriding method will have to match `in` parameters with `in` parameters of an identity-convertible type.</span></span>

- <span data-ttu-id="88095-143">À des fins de conversions de groupe délégué/lambda/méthode, `in` se comporte de la même façon qu’un paramètre `out`.</span><span class="sxs-lookup"><span data-stu-id="88095-143">For the purpose of delegate/lambda/method group conversions, `in` behaves similarly to an `out` parameter.</span></span>
<span data-ttu-id="88095-144">Les expressions lambda et les candidats de conversion de groupe de méthodes applicables doivent correspondre aux paramètres de `in` du délégué cible avec les paramètres `in` d’un type convertible en identité.</span><span class="sxs-lookup"><span data-stu-id="88095-144">Lambdas and applicable method group conversion candidates will have to match `in` parameters of the target delegate with `in` parameters of an identity-convertible type.</span></span>

- <span data-ttu-id="88095-145">Dans le cadre de la variance générique, les paramètres de `in` ne sont pas des variantes.</span><span class="sxs-lookup"><span data-stu-id="88095-145">For the purpose of generic variance, `in` parameters are nonvariant.</span></span>

> <span data-ttu-id="88095-146">Remarque : il n’y a aucun avertissement sur les paramètres de `in` qui ont des types de référence ou de Primitives.</span><span class="sxs-lookup"><span data-stu-id="88095-146">NOTE: There are no warnings on `in` parameters that have reference or primitives types.</span></span>
<span data-ttu-id="88095-147">Cela peut s’avérer inutile en général, mais dans certains cas, l’utilisateur doit/veulent passer des primitives comme `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-147">It may be pointless in general, but in some cases user must/want to pass primitives as `in`.</span></span> <span data-ttu-id="88095-148">Exemples : substitution d’une méthode générique comme `Method(in T param)` lorsque `T` a été substitué à `int`ou lorsque des méthodes telles que `Volatile.Read(in int location)`</span><span class="sxs-lookup"><span data-stu-id="88095-148">Examples - overriding a generic method like `Method(in T param)` when `T` was substituted to be `int`, or when having methods like `Volatile.Read(in int location)`</span></span>
>
> <span data-ttu-id="88095-149">Il est concevable de disposer d’un analyseur qui vous avertit en cas d’utilisation inefficace des paramètres de `in`, mais les règles de cette analyse seraient trop approximatives pour faire partie d’une spécification de langage.</span><span class="sxs-lookup"><span data-stu-id="88095-149">It is conceivable to have an analyzer that warns in cases of inefficient use of `in` parameters, but the rules for such analysis would be too fuzzy to be a part of a language specification.</span></span>

### <a name="use-of-in-at-call-sites-in-arguments"></a><span data-ttu-id="88095-150">Utilisation de `in` sur les sites d’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-150">Use of `in` at call sites.</span></span> <span data-ttu-id="88095-151">(arguments`in`)</span><span class="sxs-lookup"><span data-stu-id="88095-151">(`in` arguments)</span></span>

<span data-ttu-id="88095-152">Il existe deux façons de passer des arguments à `in` paramètres.</span><span class="sxs-lookup"><span data-stu-id="88095-152">There are two ways to pass arguments to `in` parameters.</span></span>

#### <a name="in-arguments-can-match-in-parameters"></a><span data-ttu-id="88095-153">`in` arguments peuvent correspondre `in` paramètres :</span><span class="sxs-lookup"><span data-stu-id="88095-153">`in` arguments can match `in` parameters:</span></span>

<span data-ttu-id="88095-154">Un argument avec un modificateur de `in` sur le site d’appel peut faire correspondre `in` paramètres.</span><span class="sxs-lookup"><span data-stu-id="88095-154">An argument with an `in` modifier at the call site can match `in` parameters.</span></span>

```csharp
int x = 1;

void M1<T>(in T x)
{
  // . . .
}

var x = M1(in x);  // in argument to a method

class D
{
    public string this[in Guid index];
}

D dictionary = . . . ;
var y = dictionary[in Guid.Empty]; // in argument to an indexer
```

- <span data-ttu-id="88095-155">`in` argument doit être une LValue _lisible_ (\*).</span><span class="sxs-lookup"><span data-stu-id="88095-155">`in` argument must be a _readable_ LValue(\*).</span></span>
<span data-ttu-id="88095-156">Exemple : `M1(in 42)` n’est pas valide</span><span class="sxs-lookup"><span data-stu-id="88095-156">Example: `M1(in 42)` is invalid</span></span>

> <span data-ttu-id="88095-157">(\*) La notion de [LValue/RValue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) varie selon les langages.</span><span class="sxs-lookup"><span data-stu-id="88095-157">(\*) The notion of [LValue/RValue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) vary between languages.</span></span>  
<span data-ttu-id="88095-158">Ici, par LValue, je entend une expression qui représente un emplacement qui peut être référencé directement.</span><span class="sxs-lookup"><span data-stu-id="88095-158">Here, by LValue I mean an expression that represent a location that can be referred to directly.</span></span>
<span data-ttu-id="88095-159">Et RValue signifie une expression qui produit un résultat temporaire qui ne persiste pas de manière autonome.</span><span class="sxs-lookup"><span data-stu-id="88095-159">And RValue means an expression that yields a temporary result which does not persist on its own.</span></span>  

- <span data-ttu-id="88095-160">En particulier, il est possible de passer des `readonly` des champs, des paramètres de `in` ou d’autres variables de `readonly` formelles en tant qu' `in` arguments.</span><span class="sxs-lookup"><span data-stu-id="88095-160">In particular it is valid to pass `readonly` fields, `in` parameters or other formally `readonly` variables as `in` arguments.</span></span>
<span data-ttu-id="88095-161">Exemple : `dictionary[in Guid.Empty]` est légal.</span><span class="sxs-lookup"><span data-stu-id="88095-161">Example: `dictionary[in Guid.Empty]` is legal.</span></span> <span data-ttu-id="88095-162">`Guid.Empty` est un champ readonly statique.</span><span class="sxs-lookup"><span data-stu-id="88095-162">`Guid.Empty` is a static readonly field.</span></span>

- <span data-ttu-id="88095-163">`in` argument doit avoir un type d' _identité convertible_ en type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="88095-163">`in` argument must have type _identity-convertible_ to the type of the parameter.</span></span>
<span data-ttu-id="88095-164">Exemple : `M1<object>(in Guid.Empty)` n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="88095-164">Example: `M1<object>(in Guid.Empty)` is invalid.</span></span> <span data-ttu-id="88095-165">`Guid.Empty` n’est pas _convertible en identité_ pour `object`</span><span class="sxs-lookup"><span data-stu-id="88095-165">`Guid.Empty` is not _identity-convertible_ to `object`</span></span>

<span data-ttu-id="88095-166">La motivation des règles ci-dessus est que `in` arguments garantissent l' _alias_ de la variable d’argument.</span><span class="sxs-lookup"><span data-stu-id="88095-166">The motivation for the above rules is that `in` arguments guarantee _aliasing_ of the argument variable.</span></span> <span data-ttu-id="88095-167">L’appelé reçoit toujours une référence directe au même emplacement que celui représenté par l’argument.</span><span class="sxs-lookup"><span data-stu-id="88095-167">The callee always receives a direct reference to the same location as represented by the argument.</span></span>

- <span data-ttu-id="88095-168">dans les rares cas où `in` arguments doivent être débordés de pile en raison des expressions `await` utilisées comme opérandes du même appel, le comportement est le même qu’avec les arguments `out` et `ref`. Si la variable ne peut pas être détournée de manière référentielle, une erreur est signalée.</span><span class="sxs-lookup"><span data-stu-id="88095-168">in rare situations when `in` arguments must be stack-spilled due to `await` expressions used as operands of the same call, the behavior is the same as with `out` and `ref` arguments - if the variable cannot be spilled in referentially-transparent manner, an error is reported.</span></span>

<span data-ttu-id="88095-169">Exemples :</span><span class="sxs-lookup"><span data-stu-id="88095-169">Examples:</span></span>
1. <span data-ttu-id="88095-170">`M1(in staticField, await SomethingAsync())` est valide.</span><span class="sxs-lookup"><span data-stu-id="88095-170">`M1(in staticField, await SomethingAsync())`  is valid.</span></span>
<span data-ttu-id="88095-171">`staticField` est un champ statique accessible plusieurs fois sans effets secondaires observables.</span><span class="sxs-lookup"><span data-stu-id="88095-171">`staticField` is a static field which can be accessed more than once without observable side effects.</span></span> <span data-ttu-id="88095-172">Par conséquent, l’ordre des effets secondaires et des exigences d’alias peut être fourni.</span><span class="sxs-lookup"><span data-stu-id="88095-172">Therefore both the order of side effects and aliasing requirements can be provided.</span></span>
2. <span data-ttu-id="88095-173">`M1(in RefReturningMethod(), await SomethingAsync())` génère une erreur.</span><span class="sxs-lookup"><span data-stu-id="88095-173">`M1(in RefReturningMethod(), await SomethingAsync())`  will produce an error.</span></span>
<span data-ttu-id="88095-174">`RefReturningMethod()` est une `ref` méthode de retour.</span><span class="sxs-lookup"><span data-stu-id="88095-174">`RefReturningMethod()` is a `ref` returning method.</span></span> <span data-ttu-id="88095-175">Un appel de méthode peut avoir des effets secondaires observables. par conséquent, il doit être évalué avant l’opérande `SomethingAsync()`.</span><span class="sxs-lookup"><span data-stu-id="88095-175">A method call may have observable side effects, therefore it must be evaluated before the `SomethingAsync()` operand.</span></span> <span data-ttu-id="88095-176">Toutefois, le résultat de l’appel est une référence qui ne peut pas être conservée à travers le point de suspension `await` qui rend l’exigence de référence directe impossible.</span><span class="sxs-lookup"><span data-stu-id="88095-176">However the result of the invocation is a reference that cannot be preserved across the `await` suspension point which make the direct reference requirement impossible.</span></span>

> <span data-ttu-id="88095-177">Remarque : les erreurs de dépassement de la pile sont considérées comme des limitations spécifiques à l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="88095-177">NOTE: the stack spilling errors are considered to be implementation-specific limitations.</span></span> <span data-ttu-id="88095-178">Par conséquent, elles n’ont pas d’effet sur la résolution de surcharge ou l’inférence lambda.</span><span class="sxs-lookup"><span data-stu-id="88095-178">Therefore they do not have effect on overload resolution or lambda inference.</span></span>

#### <a name="ordinary-byval-arguments-can-match-in-parameters"></a><span data-ttu-id="88095-179">Les arguments ByVal ordinaires peuvent correspondre à `in` paramètres :</span><span class="sxs-lookup"><span data-stu-id="88095-179">Ordinary byval arguments can match `in` parameters:</span></span>

<span data-ttu-id="88095-180">Les arguments normaux sans modificateurs peuvent correspondre à des paramètres de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-180">Regular arguments without modifiers can match `in` parameters.</span></span> <span data-ttu-id="88095-181">Dans ce cas, les arguments ont les mêmes contraintes assouplies qu’un argument ByVal ordinaire aurait.</span><span class="sxs-lookup"><span data-stu-id="88095-181">In such case the arguments have the same relaxed constraints as an ordinary byval arguments would have.</span></span>

<span data-ttu-id="88095-182">La motivation de ce scénario est que `in` paramètres dans les API peuvent entraîner des désagréments pour l’utilisateur lorsque les arguments ne peuvent pas être passés en tant que référence directe-ex : littéraux, résultats calculés ou `await`Ed ou arguments qui ont des types plus spécifiques.</span><span class="sxs-lookup"><span data-stu-id="88095-182">The motivation for this scenario is that `in` parameters in APIs may result in inconveniences for the user when arguments cannot be passed as a direct reference - ex: literals, computed or `await`-ed results or arguments that happen to have more specific types.</span></span>  
<span data-ttu-id="88095-183">Tous ces cas ont une solution triviale qui stocke la valeur d’argument dans un local temporaire de type approprié et transmet ce local en tant qu’argument `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-183">All these cases have a trivial solution of storing the argument value in a temporary local of appropriate type and passing that local as an `in` argument.</span></span>  
<span data-ttu-id="88095-184">Pour réduire le besoin d’un tel compilateur de code réutilisable, il est possible d’effectuer la même transformation, si nécessaire, lorsque `in` modificateur n’est pas présent sur le site d’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-184">To reduce the need for such boilerplate code compiler can perform the same transformation, if needed, when `in` modifier is not present at the call site.</span></span>  

<span data-ttu-id="88095-185">En outre, dans certains cas, tels que l’appel d’opérateurs ou les méthodes d’extension `in`, il n’existe pas de méthode syntaxique pour spécifier `in` du tout.</span><span class="sxs-lookup"><span data-stu-id="88095-185">In addition, in some cases, such as invocation of operators, or `in` extension methods, there is no syntactical way to specify `in` at all.</span></span> <span data-ttu-id="88095-186">Cela nécessite de spécifier le comportement des arguments ByVal ordinaires lorsqu’ils correspondent à `in` paramètres.</span><span class="sxs-lookup"><span data-stu-id="88095-186">That alone requires specifying the behavior of ordinary byval arguments when they match `in` parameters.</span></span>

<span data-ttu-id="88095-187">En particulier :</span><span class="sxs-lookup"><span data-stu-id="88095-187">In particular:</span></span>

- <span data-ttu-id="88095-188">Il est possible de passer RValues.</span><span class="sxs-lookup"><span data-stu-id="88095-188">it is valid to pass RValues.</span></span>
<span data-ttu-id="88095-189">Dans ce cas, une référence à un temporaire est passée.</span><span class="sxs-lookup"><span data-stu-id="88095-189">A reference to a temporary is passed in such case.</span></span>
<span data-ttu-id="88095-190">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-190">Example:</span></span>
```csharp
Print("hello");      // not an error.

void Print<T>(in T x)
{
  //. . .
}
```

- <span data-ttu-id="88095-191">les conversions implicites sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="88095-191">implicit conversions are allowed.</span></span>

> <span data-ttu-id="88095-192">Il s’agit en fait d’un cas particulier de passage d’une RValue</span><span class="sxs-lookup"><span data-stu-id="88095-192">This is actually a special case of passing an RValue</span></span>  

<span data-ttu-id="88095-193">Une référence à une valeur convertie temporaire est passée dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="88095-193">A reference to a temporary holding converted value is passed in such case.</span></span>
<span data-ttu-id="88095-194">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-194">Example:</span></span>
```csharp
Print<int>(Short.MaxValue)     // not an error.
```

- <span data-ttu-id="88095-195">dans le cas d’un récepteur d’une méthode d’extension `in` (par opposition aux méthodes d’extension `ref`), RValues ou les _conversions_ implicites de cet argument sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="88095-195">in a case of a receiver of an `in` extension method (as opposed to `ref` extension methods), RValues or implicit _this-argument-conversions_ are allowed.</span></span>
<span data-ttu-id="88095-196">Une référence à une valeur convertie temporaire est passée dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="88095-196">A reference to a temporary holding converted value is passed in such case.</span></span>
<span data-ttu-id="88095-197">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-197">Example:</span></span>
```csharp
public static IEnumerable<T> Concat<T>(in this (IEnumerable<T>, IEnumerable<T>) arg)  => . . .;

("aa", "bb").Concat<char>()    // not an error.
```
<span data-ttu-id="88095-198">Vous trouverez plus d’informations sur les méthodes d’extension `ref`/`in` dans ce document.</span><span class="sxs-lookup"><span data-stu-id="88095-198">More information on `ref`/`in` extension methods is provided further in this document.</span></span>

- <span data-ttu-id="88095-199">le débordement d’argument en raison des opérandes de `await` peut déborder « par valeur », si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="88095-199">argument spilling due to `await` operands could spill "by-value", if necessary.</span></span>
<span data-ttu-id="88095-200">Dans les scénarios où il n’est pas possible de fournir une référence directe à l’argument en raison d’une intervention `await` une copie de la valeur de l’argument est débordée à la place.</span><span class="sxs-lookup"><span data-stu-id="88095-200">In scenarios where providing a direct reference to the argument is not possible due to intervening `await` a copy of the argument's value is spilled instead.</span></span>  
<span data-ttu-id="88095-201">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-201">Example:</span></span>
```csharp
M1(RefReturningMethod(), await SomethingAsync())   // not an error.
```
<span data-ttu-id="88095-202">Étant donné que le résultat d’un appel à effet secondaire est une référence qui ne peut pas être conservée entre `await` suspension, un temporaire contenant la valeur réelle sera conservé à la place (comme dans un cas de paramètre ByVal ordinaire).</span><span class="sxs-lookup"><span data-stu-id="88095-202">Since the result of a side-effecting invocation is a reference that cannot be preserved across `await` suspension, a temporary containing the actual value will be preserved instead (as it would in an ordinary byval parameter case).</span></span>

#### <a name="omitted-optional-arguments"></a><span data-ttu-id="88095-203">Arguments facultatifs omis</span><span class="sxs-lookup"><span data-stu-id="88095-203">Omitted optional arguments</span></span>

<span data-ttu-id="88095-204">Il est permis à un paramètre `in` de spécifier une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="88095-204">It is permitted for an `in` parameter to specify a default value.</span></span> <span data-ttu-id="88095-205">Cela rend l’argument correspondant facultatif.</span><span class="sxs-lookup"><span data-stu-id="88095-205">That makes the corresponding argument optional.</span></span>

<span data-ttu-id="88095-206">L’omission de l’argument facultatif sur le site d’appel permet de passer la valeur par défaut via un temporaire.</span><span class="sxs-lookup"><span data-stu-id="88095-206">Omitting optional argument at the call site results in passing the default value via a temporary.</span></span>

```csharp
Print("hello");      // not an error, same as
Print("hello", c: Color.Black);

void Print(string s, in Color c = Color.Black)
{
    // . . .
}
```

### <a name="aliasing-behavior-in-general"></a><span data-ttu-id="88095-207">Comportement d’alias en général</span><span class="sxs-lookup"><span data-stu-id="88095-207">Aliasing behavior in general</span></span>

<span data-ttu-id="88095-208">Tout comme les variables `ref` et `out`, les variables `in` sont des références/alias à des emplacements existants.</span><span class="sxs-lookup"><span data-stu-id="88095-208">Just like `ref` and `out` variables, `in` variables are references/aliases to existing locations.</span></span>

<span data-ttu-id="88095-209">Alors que l’appelé n’est pas autorisé à y écrire, la lecture d’un paramètre `in` peut observer des valeurs différentes en tant qu’effet secondaire d’autres évaluations.</span><span class="sxs-lookup"><span data-stu-id="88095-209">While callee is not allowed to write into them, reading an `in` parameter can observe different values as a side effect of other evaluations.</span></span>

<span data-ttu-id="88095-210">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-210">Example:</span></span>

```C#
static Vector3 v = Vector3.UnitY;

static void Main()
{
    Test(v);
}

static void Test(in Vector3 v1)
{
    Debug.Assert(v1 == Vector3.UnitY);
    // changes v1 deterministically (no races required)
    ChangeV();
    Debug.Assert(v1 == Vector3.UnitX);
}

static void ChangeV()
{
    v = Vector3.UnitX;
}
```

### <a name="in-parameters-and-capturing-of-local-variables"></a><span data-ttu-id="88095-211">`in` paramètres et la capture de variables locales.</span><span class="sxs-lookup"><span data-stu-id="88095-211">`in` parameters and capturing of local variables.</span></span>  
<span data-ttu-id="88095-212">Dans le cadre de la capture lambda/Async `in` paramètres se comportent de la même façon que les paramètres `out` et `ref`.</span><span class="sxs-lookup"><span data-stu-id="88095-212">For the purpose of lambda/async capturing `in` parameters behave the same as `out` and `ref` parameters.</span></span>

- <span data-ttu-id="88095-213">les paramètres de `in` ne peuvent pas être capturés dans une fermeture</span><span class="sxs-lookup"><span data-stu-id="88095-213">`in` parameters cannot be captured in a closure</span></span>
- <span data-ttu-id="88095-214">les paramètres de `in` ne sont pas autorisés dans les méthodes Iterator</span><span class="sxs-lookup"><span data-stu-id="88095-214">`in` parameters are not allowed in iterator methods</span></span>
- <span data-ttu-id="88095-215">les paramètres de `in` ne sont pas autorisés dans les méthodes Async</span><span class="sxs-lookup"><span data-stu-id="88095-215">`in` parameters are not allowed in async methods</span></span>

### <a name="temporary-variables"></a><span data-ttu-id="88095-216">Variables temporaires.</span><span class="sxs-lookup"><span data-stu-id="88095-216">Temporary variables.</span></span>  
<span data-ttu-id="88095-217">Certaines utilisations de `in` le passage de paramètres peuvent nécessiter l’utilisation indirecte d’une variable locale temporaire :</span><span class="sxs-lookup"><span data-stu-id="88095-217">Some uses of `in` parameter passing may require indirect use of a temporary local variable:</span></span>  
- <span data-ttu-id="88095-218">les arguments `in` sont toujours passés comme alias directs lorsque le site d’appel utilise `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-218">`in` arguments are always passed as direct aliases when call-site uses `in`.</span></span> <span data-ttu-id="88095-219">Le type temporaire n’est jamais utilisé dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="88095-219">Temporary is never used in such case.</span></span>
- <span data-ttu-id="88095-220">les arguments de `in` ne doivent pas obligatoirement être des alias directs lorsque le site d’appel n’utilise pas `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-220">`in` arguments are not required to be direct aliases when call-site does not use `in`.</span></span> <span data-ttu-id="88095-221">Lorsque l’argument n’est pas une LValue, un temporaire peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="88095-221">When argument is not an LValue, a temporary may be used.</span></span>
- <span data-ttu-id="88095-222">`in` paramètre peut avoir une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="88095-222">`in` parameter may have default value.</span></span> <span data-ttu-id="88095-223">Lorsque l’argument correspondant est omis sur le site d’appel, la valeur par défaut est transmise via un temporaire.</span><span class="sxs-lookup"><span data-stu-id="88095-223">When corresponding argument is omitted at the call site, the default value are passed via a temporary.</span></span>
- <span data-ttu-id="88095-224">`in` arguments peuvent avoir des conversions implicites, y compris celles qui ne préservent pas l’identité.</span><span class="sxs-lookup"><span data-stu-id="88095-224">`in` arguments may have implicit conversions, including those that do not preserve identity.</span></span> <span data-ttu-id="88095-225">Un temporaire est utilisé dans ces cas.</span><span class="sxs-lookup"><span data-stu-id="88095-225">A temporary is used in those cases.</span></span>
- <span data-ttu-id="88095-226">les destinataires des appels struct ordinaires peuvent ne pas être inscriptibles LValues (**cas existant !** ).</span><span class="sxs-lookup"><span data-stu-id="88095-226">receivers of ordinary struct calls may not be writeable LValues (**existing case!**).</span></span> <span data-ttu-id="88095-227">Un temporaire est utilisé dans ces cas.</span><span class="sxs-lookup"><span data-stu-id="88095-227">A temporary is used in those cases.</span></span>

<span data-ttu-id="88095-228">La durée de vie de l’argument de temporisation correspond à la portée englobante la plus proche du site d’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-228">The life time of the argument temporaries matches the closest encompassing scope of the call-site.</span></span>

<span data-ttu-id="88095-229">La durée de vie formelle des variables temporaires est sémantiquement significative dans les scénarios impliquant une analyse d’échappement des variables retournées par référence.</span><span class="sxs-lookup"><span data-stu-id="88095-229">The formal life time of temporary variables is semantically significant in scenarios involving escape analysis of variables returned by reference.</span></span>

### <a name="metadata-representation-of-in-parameters"></a><span data-ttu-id="88095-230">Représentation de métadonnées des paramètres de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-230">Metadata representation of `in` parameters.</span></span>
<span data-ttu-id="88095-231">Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué à un paramètre ByRef, cela signifie que le paramètre est un paramètre `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-231">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to a byref parameter, it means that the parameter is an `in` parameter.</span></span>

<span data-ttu-id="88095-232">En outre, si la méthode est *abstraite* ou *virtuelle*, la signature de ces paramètres (et seulement ces paramètres) doit avoir `modreq[System.Runtime.InteropServices.InAttribute]`.</span><span class="sxs-lookup"><span data-stu-id="88095-232">In addition, if the method is *abstract* or *virtual*, then the signature of such parameters (and only such parameters) must have `modreq[System.Runtime.InteropServices.InAttribute]`.</span></span>

<span data-ttu-id="88095-233">**Motivation**: cela permet de s’assurer que, dans le cas d’une méthode, la substitution/la mise en œuvre des paramètres de `in` correspondent.</span><span class="sxs-lookup"><span data-stu-id="88095-233">**Motivation**: this is done to ensure that in a case of method overriding/implementing the `in` parameters match.</span></span>

<span data-ttu-id="88095-234">Les mêmes exigences s’appliquent aux méthodes `Invoke` dans les délégués.</span><span class="sxs-lookup"><span data-stu-id="88095-234">Same requirements apply to `Invoke` methods in delegates.</span></span>

<span data-ttu-id="88095-235">**Motivation**: cela permet de s’assurer que les compilateurs existants ne peuvent pas ignorer simplement `readonly` lors de la création ou de l’assignation de délégués.</span><span class="sxs-lookup"><span data-stu-id="88095-235">**Motivation**: this is to ensure that existing compilers cannot simply ignore `readonly` when creating or assigning delegates.</span></span>

## <a name="returning-by-readonly-reference"></a><span data-ttu-id="88095-236">Retour par référence ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="88095-236">Returning by readonly reference.</span></span>

### <a name="motivation"></a><span data-ttu-id="88095-237">Motivation</span><span class="sxs-lookup"><span data-stu-id="88095-237">Motivation</span></span>
<span data-ttu-id="88095-238">La motivation de cette sous-fonctionnalité est à peu près symétrique pour les raisons des paramètres `in`, à l’exception de la copie, mais du côté du retour.</span><span class="sxs-lookup"><span data-stu-id="88095-238">The motivation for this sub-feature is roughly symmetrical to the reasons for the `in` parameters - avoiding copying, but on the returning side.</span></span> <span data-ttu-id="88095-239">Avant cette fonctionnalité, une méthode ou un indexeur avait deux options : 1) retournent par référence et sont exposées à des mutations possibles ou 2) retournent par valeur, ce qui entraîne la copie.</span><span class="sxs-lookup"><span data-stu-id="88095-239">Prior to this feature, a method or an indexer had two options: 1) return by reference and be exposed to possible mutations or 2) return by value which results in copying.</span></span>

### <a name="solution-ref-readonly-returns"></a><span data-ttu-id="88095-240">Solution (`ref readonly` retourne)</span><span class="sxs-lookup"><span data-stu-id="88095-240">Solution (`ref readonly` returns)</span></span>  
<span data-ttu-id="88095-241">La fonctionnalité permet à un membre de retourner des variables par référence sans les exposer à des mutations.</span><span class="sxs-lookup"><span data-stu-id="88095-241">The feature allows a member to return variables by reference without exposing them to mutations.</span></span>

### <a name="declaring-ref-readonly-returning-members"></a><span data-ttu-id="88095-242">Déclaration de `ref readonly` retournant des membres</span><span class="sxs-lookup"><span data-stu-id="88095-242">Declaring `ref readonly` returning members</span></span>

<span data-ttu-id="88095-243">Une combinaison de modificateurs `ref readonly` sur la signature de retour est utilisée pour indiquer que le membre retourne une référence en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-243">A combination of modifiers `ref readonly` on the return signature is used to to indicate that the member returns a readonly reference.</span></span>

<span data-ttu-id="88095-244">Dans tous les cas, un membre `ref readonly` est traité comme une variable `readonly`, de la même façon que des champs `readonly` et `in` paramètres.</span><span class="sxs-lookup"><span data-stu-id="88095-244">For all purposes a `ref readonly` member is treated as a `readonly` variable - similar to `readonly` fields and `in` parameters.</span></span>

<span data-ttu-id="88095-245">Par exemple, les champs de `ref readonly` membre qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-245">For example fields of `ref readonly` member which has a struct type are all recursively classified as `readonly` variables.</span></span> <span data-ttu-id="88095-246">-Il est autorisé à les passer en tant qu’arguments `in`, mais pas en tant qu’arguments `ref` ou `out`.</span><span class="sxs-lookup"><span data-stu-id="88095-246">- It is permitted to pass them as `in` arguments, but not as `ref` or `out` arguments.</span></span>

```csharp
ref readonly Guid Method1()
{
}

Method2(in Method1()); // valid. Can pass as `in` argument.

Method3(ref Method1()); // not valid. Cannot pass as `ref` argument
```

- <span data-ttu-id="88095-247">`ref readonly` retours sont autorisés dans les mêmes emplacements que les retours `ref` sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="88095-247">`ref readonly` returns are allowed in the same places were `ref` returns are allowed.</span></span>
<span data-ttu-id="88095-248">Cela comprend les indexeurs, les délégués, les expressions lambda, les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="88095-248">This includes indexers, delegates, lambdas, local functions.</span></span>

- <span data-ttu-id="88095-249">Elle n’est pas autorisée à se surcharger sur `ref`/`ref readonly`/différences.</span><span class="sxs-lookup"><span data-stu-id="88095-249">It is not permitted to overload on `ref`/`ref readonly` /  differences.</span></span>

- <span data-ttu-id="88095-250">Elle est autorisée à se surcharger sur les différences de retour ByVal et `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-250">It is permitted to overload on ordinary byval and `ref readonly` return differences.</span></span>

- <span data-ttu-id="88095-251">Dans le cadre de OHI (surcharge, masquage, implémentation), `ref readonly` est similaire, mais distinct de `ref`.</span><span class="sxs-lookup"><span data-stu-id="88095-251">For the purpose of OHI (Overloading, Hiding, Implementing), `ref readonly` is similar but distinct from `ref`.</span></span>
<span data-ttu-id="88095-252">Par exemple, une méthode qui remplace `ref readonly` une, doit elle-même être `ref readonly` et avoir un type convertible en identité.</span><span class="sxs-lookup"><span data-stu-id="88095-252">For example the a method that overrides `ref readonly` one, must itself be `ref readonly` and have identity-convertible type.</span></span>

- <span data-ttu-id="88095-253">À des fins de conversions de groupe délégué/lambda/méthode, `ref readonly` est similaire, mais distinct de `ref`.</span><span class="sxs-lookup"><span data-stu-id="88095-253">For the purpose of delegate/lambda/method group conversions, `ref readonly` is similar but distinct from `ref`.</span></span>
<span data-ttu-id="88095-254">Les expressions lambda et les candidats de conversion de groupe de méthodes applicables doivent correspondre `ref readonly` retour du délégué cible avec `ref readonly` retour du type convertible par l’identité.</span><span class="sxs-lookup"><span data-stu-id="88095-254">Lambdas and applicable method group conversion candidates have to match `ref readonly` return of the target delegate with `ref readonly` return of the type that is identity-convertible.</span></span>

- <span data-ttu-id="88095-255">Dans le cadre de la variance générique, `ref readonly` retourne sont non variants.</span><span class="sxs-lookup"><span data-stu-id="88095-255">For the purpose of generic variance, `ref readonly` returns are nonvariant.</span></span>

> <span data-ttu-id="88095-256">Remarque : il n’y a aucun avertissement sur `ref readonly` retourne qui ont des types de référence ou de Primitives.</span><span class="sxs-lookup"><span data-stu-id="88095-256">NOTE: There are no warnings on `ref readonly` returns that have reference or primitives types.</span></span>
<span data-ttu-id="88095-257">Cela peut s’avérer inutile en général, mais dans certains cas, l’utilisateur doit/veulent passer des primitives comme `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-257">It may be pointless in general, but in some cases user must/want to pass primitives as `in`.</span></span> <span data-ttu-id="88095-258">Exemples : substitution d’une méthode générique comme `ref readonly T Method()` lorsque `T` a été substitué à `int`.</span><span class="sxs-lookup"><span data-stu-id="88095-258">Examples - overriding a generic method like `ref readonly T Method()` when `T` was substituted to be `int`.</span></span>
>
><span data-ttu-id="88095-259">Il est concevable de disposer d’un analyseur qui avertit les cas d’utilisation inefficace de `ref readonly` retourne, mais les règles de cette analyse seraient trop approximatives pour faire partie d’une spécification de langage.</span><span class="sxs-lookup"><span data-stu-id="88095-259">It is conceivable to have an analyzer that warns in cases of inefficient use of `ref readonly` returns, but the rules for such analysis would be too fuzzy to be a part of a language specification.</span></span>

### <a name="returning-from-ref-readonly-members"></a><span data-ttu-id="88095-260">Retourner à partir de `ref readonly` membres</span><span class="sxs-lookup"><span data-stu-id="88095-260">Returning from `ref readonly` members</span></span>
<span data-ttu-id="88095-261">Dans le corps de la méthode, la syntaxe est la même qu’avec les retours Ref standard.</span><span class="sxs-lookup"><span data-stu-id="88095-261">Inside the method body the syntax is the same as with regular ref returns.</span></span> <span data-ttu-id="88095-262">La `readonly` est déduite de la méthode conteneur.</span><span class="sxs-lookup"><span data-stu-id="88095-262">The `readonly` will be inferred from the containing method.</span></span>

<span data-ttu-id="88095-263">La motivation est que `return ref readonly <expression>` est inutile et n’autorise que les incompatibilités sur la partie `readonly` qui aboutirait toujours à des erreurs.</span><span class="sxs-lookup"><span data-stu-id="88095-263">The motivation is that `return ref readonly <expression>` is unnecessary long and only allows for mismatches on the `readonly` part that would always result in errors.</span></span>
<span data-ttu-id="88095-264">Toutefois, le `ref` est requis pour des besoins de cohérence avec d’autres scénarios où un événement est passé via un alias strict et par valeur.</span><span class="sxs-lookup"><span data-stu-id="88095-264">The `ref` is, however, required for consistency with other scenarios where something is passed via strict aliasing vs. by value.</span></span>

> <span data-ttu-id="88095-265">Contrairement au cas avec `in` paramètres, `ref readonly` ne retourne jamais par le biais d’une copie locale.</span><span class="sxs-lookup"><span data-stu-id="88095-265">Unlike the case with `in` parameters, `ref readonly` returns never return via a local copy.</span></span> <span data-ttu-id="88095-266">Considérer que la copie cesserait d’exister immédiatement lors du retour de cette pratique serait inutile et dangereuse.</span><span class="sxs-lookup"><span data-stu-id="88095-266">Considering that the copy would cease to exist immediately upon returning such practice would be pointless and dangerous.</span></span> <span data-ttu-id="88095-267">Par conséquent `ref readonly` retours sont toujours des références directes.</span><span class="sxs-lookup"><span data-stu-id="88095-267">Therefore `ref readonly` returns are always direct references.</span></span>

<span data-ttu-id="88095-268">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-268">Example:</span></span>

```csharp
struct ImmutableArray<T>
{
    private readonly T[] array;

    public ref readonly T ItemRef(int i)
    {
        // returning a readonly reference to an array element
        return ref this.array[i];
    }
}

```

- <span data-ttu-id="88095-269">Un argument de `return ref` doit être une LValue (**règle existante**)</span><span class="sxs-lookup"><span data-stu-id="88095-269">An argument of `return ref` must be an LValue (**existing rule**)</span></span>
- <span data-ttu-id="88095-270">Un argument de `return ref` doit être « Safe to return » (**règle existante**)</span><span class="sxs-lookup"><span data-stu-id="88095-270">An argument of `return ref` must be "safe to return" (**existing rule**)</span></span>
- <span data-ttu-id="88095-271">Dans un membre `ref readonly` un argument de `return ref` ne doit _pas être accessible en écriture_ .</span><span class="sxs-lookup"><span data-stu-id="88095-271">In a `ref readonly` member an argument of `return ref` is _not required to be writeable_ .</span></span>
<span data-ttu-id="88095-272">Par exemple, ce membre peut retourner un champ ReadOnly ou l’un de ses paramètres de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-272">For example such member can ref-return a readonly field or one of its `in` parameters.</span></span>

### <a name="safe-to-return-rules"></a><span data-ttu-id="88095-273">Règles de retour sécurisé.</span><span class="sxs-lookup"><span data-stu-id="88095-273">Safe to Return rules.</span></span>
<span data-ttu-id="88095-274">Les règles normal de retour sécurisé pour les références s’appliquent également aux références ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="88095-274">Normal safe to return rules for references will apply to readonly references as well.</span></span>

<span data-ttu-id="88095-275">Notez qu’un `ref readonly` peut être obtenu à partir d’une `ref` locale/paramètre/retour standard, mais pas l’inverse.</span><span class="sxs-lookup"><span data-stu-id="88095-275">Note that a `ref readonly` can be obtained from a regular `ref` local/parameter/return, but not the other way around.</span></span> <span data-ttu-id="88095-276">Sinon, la sécurité des retours de `ref readonly` est déduite de la même façon que pour les retours de `ref` normaux.</span><span class="sxs-lookup"><span data-stu-id="88095-276">Otherwise the safety of `ref readonly` returns is inferred the same way as for regular `ref` returns.</span></span>

<span data-ttu-id="88095-277">En considérant que RValues peut être passé comme paramètre `in` et retourné comme `ref readonly` nous avons besoin d’une règle supplémentaire- **RValues ne sont pas sécurisés par référence**.</span><span class="sxs-lookup"><span data-stu-id="88095-277">Considering that RValues can be passed as `in` parameter and returned as `ref readonly` we need one more rule - **RValues are not safe-to-return by reference**.</span></span>

> <span data-ttu-id="88095-278">Tenez compte de la situation où une RValue est passée à un paramètre `in` via une copie, puis retournée sous la forme d’un `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-278">Consider the situation when an RValue is passed to an `in` parameter via a copy and then returned back in a form of a `ref readonly`.</span></span> <span data-ttu-id="88095-279">Dans le contexte de l’appelant, le résultat de cet appel est une référence aux données locales et, par conséquent, il n’est pas possible de retourner.</span><span class="sxs-lookup"><span data-stu-id="88095-279">In the context of the caller the result of such invocation is a reference to local data and as such is unsafe to return.</span></span>
> <span data-ttu-id="88095-280">Une fois que les RValues ne peuvent pas être renvoyés, la règle existante `#6` gère déjà ce cas.</span><span class="sxs-lookup"><span data-stu-id="88095-280">Once RValues are not safe to return, the existing rule `#6` already handles this case.</span></span>

<span data-ttu-id="88095-281">Exemple :</span><span class="sxs-lookup"><span data-stu-id="88095-281">Example:</span></span>
```csharp
ref readonly Vector3 Test1()
{
    // can pass an RValue as "in" (via a temp copy)
    // but the result is not safe to return
    // because the RValue argument was not safe to return by reference
    return ref Test2(default(Vector3));
}

ref readonly Vector3 Test2(in Vector3 r)
{
    // this is ok, r is returnable
    return ref r;
}
```

<span data-ttu-id="88095-282">Règles de `safe to return` mises à jour :</span><span class="sxs-lookup"><span data-stu-id="88095-282">Updated `safe to return` rules:</span></span>

1.  <span data-ttu-id="88095-283">**les références aux variables sur le tas peuvent être renvoyées en toute sécurité**</span><span class="sxs-lookup"><span data-stu-id="88095-283">**refs to variables on the heap are safe to return**</span></span>
2.  <span data-ttu-id="88095-284">**les paramètres ref/in sont en toute sécurité** retournés
`in` paramètres ne peuvent être retournés naturellement que en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-284">**ref/in parameters are safe to return**
`in` parameters naturally can only be returned as readonly.</span></span>
3.  <span data-ttu-id="88095-285">les **paramètres out peuvent être renvoyés en toute sécurité** (mais doivent être définitivement affectés, comme c’est déjà le cas aujourd’hui)</span><span class="sxs-lookup"><span data-stu-id="88095-285">**out parameters are safe to return** (but must be definitely assigned, as is already the case today)</span></span>
4.  <span data-ttu-id="88095-286">**les champs de struct d’instance peuvent être renvoyés en toute sécurité tant que le récepteur peut être retourné en toute sécurité**</span><span class="sxs-lookup"><span data-stu-id="88095-286">**instance struct fields are safe to return as long as the receiver is safe to return**</span></span>
5.  <span data-ttu-id="88095-287">**'This’ne peut pas être retourné en toute sécurité à partir de membres struct**</span><span class="sxs-lookup"><span data-stu-id="88095-287">**'this' is not safe to return from struct members**</span></span>
6.  <span data-ttu-id="88095-288">**une référence, retournée à partir d’une autre méthode, peut être retournée en toute sécurité si toutes les références/sorties passées à cette méthode en tant que paramètres formels ont été** retournées en toute sécurité.
*plus particulièrement, il est inutile de retourner le récepteur en toute sécurité, qu’il s’agisse d’un struct, d’une classe ou d’un type en tant que paramètre de type générique.*</span><span class="sxs-lookup"><span data-stu-id="88095-288">**a ref, returned from another method is safe to return if all refs/outs passed to that method as formal parameters were safe to return.**
*Specifically it is irrelevant if receiver is safe to return, regardless whether receiver is a struct, class or typed as a generic type parameter.*</span></span>
7.  <span data-ttu-id="88095-289">**Les rvalues ne peuvent pas être retournés par référence.** 
*spécifiquement rvalues sont sécurisés pour passer comme paramètres in.*</span><span class="sxs-lookup"><span data-stu-id="88095-289">**RValues are not safe to return by reference.**
*Specifically RValues are safe to pass as in parameters.*</span></span>

> <span data-ttu-id="88095-290">Remarque : il existe des règles supplémentaires concernant la sécurité des retours qui entrent en jeu lorsque les types de type REF et les réaffectations de référence sont impliquées.</span><span class="sxs-lookup"><span data-stu-id="88095-290">NOTE: There are additional rules regarding safety of returns that come into play when ref-like types and ref-reassignments are involved.</span></span>
> <span data-ttu-id="88095-291">Les règles s’appliquent de manière équitable aux membres `ref` et `ref readonly` et ne sont donc pas mentionnées ici.</span><span class="sxs-lookup"><span data-stu-id="88095-291">The rules equally apply to `ref` and `ref readonly` members and therefore are not mentioned here.</span></span>

### <a name="aliasing-behavior"></a><span data-ttu-id="88095-292">Comportement de l’alias.</span><span class="sxs-lookup"><span data-stu-id="88095-292">Aliasing behavior.</span></span>
<span data-ttu-id="88095-293">les membres de `ref readonly` fournissent le même comportement d’alias que les membres `ref` ordinaires (sauf en lecture seule).</span><span class="sxs-lookup"><span data-stu-id="88095-293">`ref readonly` members provide the same aliasing behavior as ordinary `ref` members (except for being readonly).</span></span>
<span data-ttu-id="88095-294">Par conséquent, à des fins de capture dans des expressions lambda, async, itérateurs, empilement, etc... les mêmes restrictions s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="88095-294">Therefore for the purpose of capturing in lambdas, async, iterators, stack spilling etc... the same restrictions apply.</span></span> <span data-ttu-id="88095-295">celles.</span><span class="sxs-lookup"><span data-stu-id="88095-295">- I.E.</span></span> <span data-ttu-id="88095-296">en raison de l’impossibilité de capturer les références réelles et en raison de la nature des effets secondaires de l’évaluation des membres, ces scénarios ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="88095-296">due to inability to capture the actual references and due to side-effecting nature of member evaluation such scenarios are disallowed.</span></span>

> <span data-ttu-id="88095-297">Il est autorisé et obligatoire de faire une copie lorsque `ref readonly` retour est un récepteur de méthodes struct régulières, qui acceptent `this` comme référence inscriptible ordinaire.</span><span class="sxs-lookup"><span data-stu-id="88095-297">It is permitted and required to make a copy when `ref readonly` return is a receiver of regular struct methods, which take `this` as an ordinary writeable reference.</span></span> <span data-ttu-id="88095-298">Historiquement dans tous les cas où ces appels sont appliqués à une variable ReadOnly, une copie locale est effectuée.</span><span class="sxs-lookup"><span data-stu-id="88095-298">Historically in all cases where such invocations are applied to readonly variable a local copy is made.</span></span>

### <a name="metadata-representation"></a><span data-ttu-id="88095-299">Représentation de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="88095-299">Metadata representation.</span></span>
<span data-ttu-id="88095-300">Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué au retour d’une méthode de retour par ByRef, cela signifie que la méthode retourne une référence en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-300">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to the return of a byref returning method, it means that the method returns a readonly reference.</span></span>

<span data-ttu-id="88095-301">En outre, la signature de résultats de ces méthodes (et uniquement ces méthodes) doit avoir `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.</span><span class="sxs-lookup"><span data-stu-id="88095-301">In addition, the result signature of such methods (and only those methods) must have `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.</span></span>

<span data-ttu-id="88095-302">**Motivation**: cela permet de s’assurer que les compilateurs existants ne peuvent pas ignorer simplement `readonly` lors de l’appel de méthodes avec `ref readonly` retourne</span><span class="sxs-lookup"><span data-stu-id="88095-302">**Motivation**: this is to ensure that existing compilers cannot simply ignore `readonly` when invoking methods with `ref readonly` returns</span></span>

## <a name="readonly-structs"></a><span data-ttu-id="88095-303">Structs en lecture seule</span><span class="sxs-lookup"><span data-stu-id="88095-303">Readonly structs</span></span>
<span data-ttu-id="88095-304">En bref, fonctionnalité qui rend `this` paramètre de tous les membres d’instance d’un struct, à l’exception des constructeurs, un paramètre `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-304">In short - a feature that makes `this` parameter of all instance members of a struct, except for constructors, an `in` parameter.</span></span>

### <a name="motivation"></a><span data-ttu-id="88095-305">Motivation</span><span class="sxs-lookup"><span data-stu-id="88095-305">Motivation</span></span>
<span data-ttu-id="88095-306">Le compilateur doit supposer que tout appel de méthode sur une instance de struct peut modifier l’instance.</span><span class="sxs-lookup"><span data-stu-id="88095-306">Compiler must assume that any method call on a struct instance may modify the instance.</span></span> <span data-ttu-id="88095-307">En effet, une référence accessible en écriture est transmise à la méthode en tant que paramètre `this` et active entièrement ce comportement.</span><span class="sxs-lookup"><span data-stu-id="88095-307">Indeed a writeable reference is passed to the method as `this` parameter and fully enables this behavior.</span></span> <span data-ttu-id="88095-308">Pour autoriser ces appels sur les variables de `readonly`, les appels sont appliqués aux copies temporaires.</span><span class="sxs-lookup"><span data-stu-id="88095-308">To allow such invocations on `readonly` variables, the invocations are applied to temp copies.</span></span> <span data-ttu-id="88095-309">Cela peut ne pas être intuitif et oblige parfois les utilisateurs à abandonner `readonly` pour des raisons de performances.</span><span class="sxs-lookup"><span data-stu-id="88095-309">That could be unintuitive and sometimes forces people to abandon `readonly` for performance reasons.</span></span>  
<span data-ttu-id="88095-310">Exemple : https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/</span><span class="sxs-lookup"><span data-stu-id="88095-310">Example: https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/</span></span>

<span data-ttu-id="88095-311">Après l’ajout de la prise en charge de `in` paramètres et `ref readonly` retourne le problème de la copie défensive est plus grave puisque les variables ReadOnly deviennent plus courantes.</span><span class="sxs-lookup"><span data-stu-id="88095-311">After adding support for `in` parameters and `ref readonly` returns the problem of defensive copying will get worse since readonly variables will become more common.</span></span>

### <a name="solution"></a><span data-ttu-id="88095-312">Solution</span><span class="sxs-lookup"><span data-stu-id="88095-312">Solution</span></span>
<span data-ttu-id="88095-313">Autorisez `readonly` modificateur sur les déclarations de struct, ce qui entraînerait le traitement des `this` comme `in` paramètre sur toutes les méthodes d’instance de struct, à l’exception des constructeurs.</span><span class="sxs-lookup"><span data-stu-id="88095-313">Allow `readonly` modifier on struct declarations which would result in `this` being treated as `in` parameter on all struct instance methods except for constructors.</span></span>

```csharp
static void Test(in Vector3 v1)
{
    // no need to make a copy of v1 since Vector3 is a readonly struct
    System.Console.WriteLine(v1.ToString());
}

readonly struct Vector3
{
    . . .

    public override string ToString()
    {
        // not OK!!  `this` is an `in` parameter
        foo(ref this.X);

        // OK
        return $"X: {X}, Y: {Y}, Z: {Z}";
    }
}
```

### <a name="restrictions-on-members-of-readonly-struct"></a><span data-ttu-id="88095-314">Restrictions sur les membres d’un struct ReadOnly</span><span class="sxs-lookup"><span data-stu-id="88095-314">Restrictions on members of readonly struct</span></span>
- <span data-ttu-id="88095-315">Les champs d’instance d’un struct ReadOnly doivent être en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-315">Instance fields of a readonly struct must be readonly.</span></span>  
<span data-ttu-id="88095-316">**Motivation :** peut uniquement être écrit en externe, mais pas par le biais de membres.</span><span class="sxs-lookup"><span data-stu-id="88095-316">**Motivation:** can only be written to externally, but not through members.</span></span>
- <span data-ttu-id="88095-317">Les propriétés autoproperties d’un struct ReadOnly doivent être en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-317">Instance autoproperties of a readonly struct must be get-only.</span></span>  
<span data-ttu-id="88095-318">**Motivation :** conséquence de la restriction sur les champs d’instance.</span><span class="sxs-lookup"><span data-stu-id="88095-318">**Motivation:** consequence of restriction on instance fields.</span></span>
- <span data-ttu-id="88095-319">Un struct ReadOnly ne peut pas déclarer des événements de type champ.</span><span class="sxs-lookup"><span data-stu-id="88095-319">Readonly struct may not declare field-like events.</span></span>  
<span data-ttu-id="88095-320">**Motivation :** conséquence de la restriction sur les champs d’instance.</span><span class="sxs-lookup"><span data-stu-id="88095-320">**Motivation:** consequence of restriction on instance fields.</span></span>

### <a name="metadata-representation"></a><span data-ttu-id="88095-321">Représentation de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="88095-321">Metadata representation.</span></span>
<span data-ttu-id="88095-322">Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué à un type valeur, cela signifie que le type est un `readonly struct`.</span><span class="sxs-lookup"><span data-stu-id="88095-322">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to a value type, it means that the type is a `readonly struct`.</span></span>

<span data-ttu-id="88095-323">En particulier :</span><span class="sxs-lookup"><span data-stu-id="88095-323">In particular:</span></span>
-  <span data-ttu-id="88095-324">L’identité du type de `IsReadOnlyAttribute` n’a pas d’importance.</span><span class="sxs-lookup"><span data-stu-id="88095-324">The identity of the `IsReadOnlyAttribute` type is unimportant.</span></span> <span data-ttu-id="88095-325">En fait, il peut être incorporé par le compilateur dans l’assembly conteneur, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="88095-325">In fact it can be embedded by the compiler in the containing assembly if needed.</span></span>

## <a name="refin-extension-methods"></a><span data-ttu-id="88095-326">`ref`/méthodes d’extension `in`</span><span class="sxs-lookup"><span data-stu-id="88095-326">`ref`/`in` extension methods</span></span>
<span data-ttu-id="88095-327">Il existe en fait une proposition existante (https://github.com/dotnet/roslyn/issues/165) et le prototype correspondant (https://github.com/dotnet/roslyn/pull/15650).</span><span class="sxs-lookup"><span data-stu-id="88095-327">There is actually an existing proposal (https://github.com/dotnet/roslyn/issues/165) and corresponding prototype PR (https://github.com/dotnet/roslyn/pull/15650).</span></span>
<span data-ttu-id="88095-328">Je veux simplement confirmer que cette idée n’est pas entièrement nouvelle.</span><span class="sxs-lookup"><span data-stu-id="88095-328">I just want to acknowledge that this idea is not entirely new.</span></span> <span data-ttu-id="88095-329">C’est toutefois le cas ici, car `ref readonly` supprime de façon élégante le problème le plus lié à ces méthodes, que faire avec les récepteurs RValue.</span><span class="sxs-lookup"><span data-stu-id="88095-329">It is, however, relevant here since `ref readonly` elegantly removes the most contentious issue about such methods - what to do with RValue receivers.</span></span>

<span data-ttu-id="88095-330">L’idée générale est d’autoriser les méthodes d’extension à prendre le paramètre `this` par référence, à condition que le type soit connu comme étant un type struct.</span><span class="sxs-lookup"><span data-stu-id="88095-330">The general idea is allowing extension methods to take the `this` parameter by reference, as long as the type is known to be a struct type.</span></span>

```csharp
public static void Extension(ref this Guid self)
{
    // do something
}
```

<span data-ttu-id="88095-331">Les raisons justifiant l’écriture de ces méthodes d’extension sont principalement les suivantes :</span><span class="sxs-lookup"><span data-stu-id="88095-331">The reasons for writing such extension methods are primarily:</span></span>  
1.  <span data-ttu-id="88095-332">Éviter la copie lorsque le récepteur est un grand struct</span><span class="sxs-lookup"><span data-stu-id="88095-332">Avoid copying when receiver is a large struct</span></span>
2.  <span data-ttu-id="88095-333">Autoriser les méthodes d’extension mutantes sur des structs</span><span class="sxs-lookup"><span data-stu-id="88095-333">Allow mutating extension methods on structs</span></span>

<span data-ttu-id="88095-334">Les raisons pour lesquelles nous ne souhaitons pas autoriser cela sur les classes</span><span class="sxs-lookup"><span data-stu-id="88095-334">The reasons why we do not want to allow this on classes</span></span>  
1.  <span data-ttu-id="88095-335">Elle est très limitée.</span><span class="sxs-lookup"><span data-stu-id="88095-335">It would be of very limited purpose.</span></span>
2.  <span data-ttu-id="88095-336">Cela entraînerait un arrêt invariant à long terme qu’un appel de méthode ne peut pas désactiver le destinataire non`null` pour qu’il devienne `null` après l’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-336">It would break long standing invariant that a method call cannot turn non-`null` receiver to become `null` after invocation.</span></span>
> <span data-ttu-id="88095-337">En fait, actuellement, une variable non-`null` ne peut pas être `null`, sauf si elle est _explicitement_ assignée ou transmise par `ref` ou `out`.</span><span class="sxs-lookup"><span data-stu-id="88095-337">In fact, currently a non-`null` variable cannot become `null` unless _explicitly_ assigned or passed by `ref` or `out`.</span></span>
> <span data-ttu-id="88095-338">Cela contribue beaucoup à la lisibilité ou à d’autres formes de l’analyse « peut-il s’agir d’une valeur NULL ici ».</span><span class="sxs-lookup"><span data-stu-id="88095-338">That greatly aids readability or other forms of "can this be a null here" analysis.</span></span>
3.  <span data-ttu-id="88095-339">Il serait difficile de procéder au rapprochement avec la sémantique « évaluer une fois » des accès conditionnels null.</span><span class="sxs-lookup"><span data-stu-id="88095-339">It would be hard to reconcile with "evaluate once" semantics of null-conditional accesses.</span></span>
<span data-ttu-id="88095-340">Exemple : `obj.stringField?.RefExtension(...)`-doit capturer une copie de `stringField` pour rendre le contrôle null explicite, mais les assignations à `this` à l’intérieur de RefExtension ne sont pas reflétées dans le champ.</span><span class="sxs-lookup"><span data-stu-id="88095-340">Example: `obj.stringField?.RefExtension(...)` - need to capture a copy of `stringField` to make the null check meaningful, but then assignments to `this` inside RefExtension would not be reflected back to the field.</span></span>

<span data-ttu-id="88095-341">La possibilité de déclarer des méthodes d’extension sur des **structs** qui acceptent le premier argument par référence était une requête de longue durée.</span><span class="sxs-lookup"><span data-stu-id="88095-341">An ability to declare extension methods on **structs** that take the first argument by reference was a long-standing request.</span></span> <span data-ttu-id="88095-342">L’une des considérations de blocage était « que se passe-t-il si le récepteur n’est pas une LValue ? ».</span><span class="sxs-lookup"><span data-stu-id="88095-342">One of the blocking consideration was "what happens if receiver is not an LValue?".</span></span>

- <span data-ttu-id="88095-343">L’une des méthodes d’extension peut également être appelée comme une méthode statique (parfois, il s’agit de la seule façon de résoudre l’ambiguïté).</span><span class="sxs-lookup"><span data-stu-id="88095-343">There is a precedent that any extension method could also be called as a static method (sometimes it is the only way to resolve ambiguity).</span></span> <span data-ttu-id="88095-344">Cela impliquerait que les récepteurs RValue ne soient pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="88095-344">It would dictate that RValue receivers should be disallowed.</span></span>
- <span data-ttu-id="88095-345">En revanche, il existe une pratique qui consiste à effectuer un appel sur une copie dans des situations similaires lorsque des méthodes d’instance de struct sont impliquées.</span><span class="sxs-lookup"><span data-stu-id="88095-345">On the other hand there is a practice of making invocation on a copy in similar situations when struct instance methods are involved.</span></span>

<span data-ttu-id="88095-346">La raison pour laquelle la « copie implicite » existe, c’est parce que la majorité des méthodes struct ne modifient pas réellement la structure alors qu’elle ne peut pas l’indiquer.</span><span class="sxs-lookup"><span data-stu-id="88095-346">The reason why the "implicit copying" exists is because the majority of struct methods do not actually modify the struct while not being able to indicate that.</span></span> <span data-ttu-id="88095-347">Par conséquent, la solution la plus pratique consistait simplement à effectuer l’appel sur une copie, mais cette pratique est connue pour nuire aux performances et provoquer des bogues.</span><span class="sxs-lookup"><span data-stu-id="88095-347">Therefore the most practical solution was to just make the invocation on a copy, but this practice is known for harming performance and causing bugs.</span></span>

<span data-ttu-id="88095-348">Désormais, avec la disponibilité des paramètres de `in`, il est possible qu’une extension signale l’intention.</span><span class="sxs-lookup"><span data-stu-id="88095-348">Now, with availability of `in` parameters, it is possible for an extension to signal the intent.</span></span> <span data-ttu-id="88095-349">Par conséquent, le énigme peut être résolu en exigeant que les extensions `ref` soient appelées avec des récepteurs accessibles en écriture, tandis que les extensions `in` autorisent la copie implicite si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="88095-349">Therefore the conundrum can be resolved by requiring `ref` extensions to be called with writeable receivers while `in` extensions permit implicit copying if necessary.</span></span>

```csharp
// this can be called on either RValue or an LValue
public static void Reader(in this Guid self)
{
    // do something nonmutating.
    WriteLine(self == default(Guid));
}

// this can be called only on an LValue
public static void Mutator(ref this Guid self)
{
    // can mutate self
    self = new Guid();
}
```

### <a name="in-extensions-and-generics"></a><span data-ttu-id="88095-350">les génériques et les extensions de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-350">`in` extensions and generics.</span></span>
<span data-ttu-id="88095-351">L’objectif des méthodes d’extension `ref` consiste à muter le récepteur directement ou en appelant des membres mutants.</span><span class="sxs-lookup"><span data-stu-id="88095-351">The purpose of `ref` extension methods is to mutate the receiver directly or by invoking mutating members.</span></span> <span data-ttu-id="88095-352">Par conséquent, `ref this T` extensions sont autorisées tant que `T` est imposé comme un struct.</span><span class="sxs-lookup"><span data-stu-id="88095-352">Therefore `ref this T` extensions are allowed as long as `T` is constrained to be a struct.</span></span>

<span data-ttu-id="88095-353">En revanche `in` méthodes d’extension existent spécifiquement pour réduire la copie implicite.</span><span class="sxs-lookup"><span data-stu-id="88095-353">On the other hand `in` extension methods exist specifically to reduce implicit copying.</span></span> <span data-ttu-id="88095-354">Toutefois, toute utilisation d’un paramètre de `in T` doit être effectuée par le biais d’un membre d’interface.</span><span class="sxs-lookup"><span data-stu-id="88095-354">However any use of an `in T` parameter will have to be done through an interface member.</span></span> <span data-ttu-id="88095-355">Étant donné que tous les membres d’interface sont considérés comme mutants, toute utilisation de ce type requiert une copie.</span><span class="sxs-lookup"><span data-stu-id="88095-355">Since all interface members are considered mutating, any such use would require a copy.</span></span> <span data-ttu-id="88095-356">-Au lieu de réduire la copie, l’effet serait le contraire.</span><span class="sxs-lookup"><span data-stu-id="88095-356">- Instead of reducing copying, the effect would be the opposite.</span></span> <span data-ttu-id="88095-357">Par conséquent, `in this T` n’est pas autorisé quand `T` est un paramètre de type générique indépendamment des contraintes.</span><span class="sxs-lookup"><span data-stu-id="88095-357">Therefore `in this T` is not allowed when `T` is a generic type parameter regardless of constraints.</span></span>

### <a name="valid-kinds-of-extension-methods-recap"></a><span data-ttu-id="88095-358">Types valides de méthodes d’extension (récapitulatif) :</span><span class="sxs-lookup"><span data-stu-id="88095-358">Valid kinds of extension methods (recap):</span></span>
<span data-ttu-id="88095-359">Les formes suivantes de `this` déclaration dans une méthode d’extension sont désormais autorisées :</span><span class="sxs-lookup"><span data-stu-id="88095-359">The following forms of `this` declaration in an extension method are now allowed:</span></span>
1) <span data-ttu-id="88095-360">`this T arg`-extension ByVal régulière.</span><span class="sxs-lookup"><span data-stu-id="88095-360">`this T arg` - regular byval extension.</span></span> <span data-ttu-id="88095-361">(**cas existant**)</span><span class="sxs-lookup"><span data-stu-id="88095-361">(**existing case**)</span></span>
- <span data-ttu-id="88095-362">T peut être n’importe quel type, y compris des types référence ou des paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="88095-362">T can be any type, including reference types or type parameters.</span></span>
<span data-ttu-id="88095-363">L’instance sera la même variable après l’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-363">Instance will be the same variable after the call.</span></span>
<span data-ttu-id="88095-364">Autorise les conversions implicites de ce genre de _conversion d’arguments_ .</span><span class="sxs-lookup"><span data-stu-id="88095-364">Allows implicit conversions of _this-argument-conversion_ kind.</span></span>
<span data-ttu-id="88095-365">Peut être appelé sur RValues.</span><span class="sxs-lookup"><span data-stu-id="88095-365">Can be called on RValues.</span></span>

- <span data-ttu-id="88095-366">`in this T self` - extension de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-366">`in this T self` - `in` extension.</span></span>
<span data-ttu-id="88095-367">T doit être un type struct réel.</span><span class="sxs-lookup"><span data-stu-id="88095-367">T must be an actual struct type.</span></span>
<span data-ttu-id="88095-368">L’instance sera la même variable après l’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-368">Instance will be the same variable after the call.</span></span>
<span data-ttu-id="88095-369">Autorise les conversions implicites de ce genre de _conversion d’arguments_ .</span><span class="sxs-lookup"><span data-stu-id="88095-369">Allows implicit conversions of _this-argument-conversion_ kind.</span></span>
<span data-ttu-id="88095-370">Peut être appelé sur RValues (peut être appelé sur un temp si nécessaire).</span><span class="sxs-lookup"><span data-stu-id="88095-370">Can be called on RValues (may be invoked on a temp if needed).</span></span>

- <span data-ttu-id="88095-371">`ref this T self` - extension de `ref`.</span><span class="sxs-lookup"><span data-stu-id="88095-371">`ref this T self` - `ref` extension.</span></span>
<span data-ttu-id="88095-372">T doit être un type struct ou un paramètre de type générique qui est imposé comme struct.</span><span class="sxs-lookup"><span data-stu-id="88095-372">T must be a struct type or a generic type parameter constrained to be a struct.</span></span>
<span data-ttu-id="88095-373">L’instance peut être écrite par l’appel.</span><span class="sxs-lookup"><span data-stu-id="88095-373">Instance may be written to by the invocation.</span></span>
<span data-ttu-id="88095-374">Autorise uniquement les conversions d’identité.</span><span class="sxs-lookup"><span data-stu-id="88095-374">Allows only identity conversions.</span></span>
<span data-ttu-id="88095-375">Doit être appelé sur une LValue accessible en écriture.</span><span class="sxs-lookup"><span data-stu-id="88095-375">Must be called on writeable LValue.</span></span> <span data-ttu-id="88095-376">(jamais appelé via un temp).</span><span class="sxs-lookup"><span data-stu-id="88095-376">(never invoked via a temp).</span></span>

## <a name="readonly-ref-locals"></a><span data-ttu-id="88095-377">Variables locales de référence en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="88095-377">Readonly ref locals.</span></span>

### <a name="motivation"></a><span data-ttu-id="88095-378">Raisons.</span><span class="sxs-lookup"><span data-stu-id="88095-378">Motivation.</span></span>
<span data-ttu-id="88095-379">Une fois `ref readonly` membres introduits, il était évident de l’utilisation qu’ils devaient être associés à un type de local approprié.</span><span class="sxs-lookup"><span data-stu-id="88095-379">Once `ref readonly` members were introduced, it was clear from the use that they need to be paired with appropriate kind of local.</span></span> <span data-ttu-id="88095-380">L’évaluation d’un membre peut produire ou observer des effets secondaires. par conséquent, si le résultat doit être utilisé plusieurs fois, il doit être stocké.</span><span class="sxs-lookup"><span data-stu-id="88095-380">Evaluation of a member may produce or observe side effects, therefore if the result must be used more than once, it needs to be stored.</span></span> <span data-ttu-id="88095-381">Les variables locales `ref` ordinaires ne sont pas là pour vous, car elles ne peuvent pas se voir assigner une référence `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-381">Ordinary `ref` locals do not help here since they cannot be assigned a `readonly` reference.</span></span>   

### <a name="solution"></a><span data-ttu-id="88095-382">Elle.</span><span class="sxs-lookup"><span data-stu-id="88095-382">Solution.</span></span>
<span data-ttu-id="88095-383">Autorisez la déclaration de `ref readonly` variables locales.</span><span class="sxs-lookup"><span data-stu-id="88095-383">Allow declaring `ref readonly` locals.</span></span> <span data-ttu-id="88095-384">Il s’agit d’un nouveau type de `ref` variables locales qui ne sont pas accessibles en écriture.</span><span class="sxs-lookup"><span data-stu-id="88095-384">This is a new kind of `ref` locals that is not writeable.</span></span> <span data-ttu-id="88095-385">Par conséquent `ref readonly` les variables locales peuvent accepter des références à des variables en lecture seule sans exposer ces variables aux écritures.</span><span class="sxs-lookup"><span data-stu-id="88095-385">As a result `ref readonly` locals can accept references to readonly variables without exposing these variables to writes.</span></span>

### <a name="declaring-and-using-ref-readonly-locals"></a><span data-ttu-id="88095-386">Déclaration et utilisation de `ref readonly` variables locales.</span><span class="sxs-lookup"><span data-stu-id="88095-386">Declaring and using `ref readonly` locals.</span></span>

<span data-ttu-id="88095-387">La syntaxe de ces variables locales utilise des modificateurs de `ref readonly` au niveau du site de déclaration (dans cet ordre spécifique).</span><span class="sxs-lookup"><span data-stu-id="88095-387">The syntax of such locals uses `ref readonly` modifiers at declaration site (in that specific order).</span></span> <span data-ttu-id="88095-388">De la même façon que les variables locales de `ref` ordinaires, `ref readonly` variables locales doivent être de type REF au niveau de la déclaration.</span><span class="sxs-lookup"><span data-stu-id="88095-388">Similarly to ordinary `ref` locals, `ref readonly` locals must be ref-initialized at declaration.</span></span> <span data-ttu-id="88095-389">Contrairement aux variables locales de `ref` standard, `ref readonly` variables locales peuvent faire référence à `readonly` LValues comme les paramètres `in`, les champs `readonly`, les méthodes `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-389">Unlike regular `ref` locals, `ref readonly` locals can refer to `readonly` LValues like `in` parameters, `readonly` fields, `ref readonly` methods.</span></span>

<span data-ttu-id="88095-390">Dans tous les cas, un `ref readonly` local est traité comme une variable de `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-390">For all purposes a `ref readonly` local is treated as a `readonly` variable.</span></span> <span data-ttu-id="88095-391">La plupart des restrictions relatives à l’utilisation sont les mêmes que pour les champs de `readonly` ou les paramètres de `in`.</span><span class="sxs-lookup"><span data-stu-id="88095-391">Most of the restrictions on the use are the same as with `readonly` fields or `in` parameters.</span></span>

<span data-ttu-id="88095-392">Par exemple, les champs d’un paramètre `in` qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`.</span><span class="sxs-lookup"><span data-stu-id="88095-392">For example fields of an `in` parameter which has a struct type are all recursively classified as `readonly` variables .</span></span>   

```csharp
static readonly ref Vector3 M1() => . . .

static readonly ref Vector3 M1_Trace()
{
    // OK
    ref readonly var r1 = ref M1();

    // Not valid. Need an LValue
    ref readonly Vector3 r2 = ref default(Vector3);

    // Not valid. r1 is readonly.
    Mutate(ref r1);

    // OK.
    Print(in r1);

    // OK.
    return ref r1;
}
```

### <a name="restrictions-on-use-of-ref-readonly-locals"></a><span data-ttu-id="88095-393">Restrictions sur l’utilisation de `ref readonly` variables locales</span><span class="sxs-lookup"><span data-stu-id="88095-393">Restrictions on use of `ref readonly` locals</span></span>
<span data-ttu-id="88095-394">À l’exception de leur nature `readonly`, `ref readonly` variables locales se comportent comme des variables locales `ref` ordinaires et sont soumises à des restrictions rigoureuses.</span><span class="sxs-lookup"><span data-stu-id="88095-394">Except for their `readonly` nature, `ref readonly` locals behave like ordinary `ref` locals and are subject to exactly same restrictions.</span></span>  
<span data-ttu-id="88095-395">Par exemple, les restrictions liées à la capture dans des fermetures, la déclaration dans `async` méthodes ou l’analyse des `safe-to-return` s’appliquent également à `ref readonly` variables locales.</span><span class="sxs-lookup"><span data-stu-id="88095-395">For example restrictions related to capturing in closures, declaring in `async` methods or the `safe-to-return` analysis equally applies to `ref readonly` locals.</span></span>

## <a name="ternary-ref-expressions-aka-conditional-lvalues"></a><span data-ttu-id="88095-396">Expressions `ref` ternaires.</span><span class="sxs-lookup"><span data-stu-id="88095-396">Ternary `ref` expressions.</span></span> <span data-ttu-id="88095-397">(également appelé « LValues conditionnel »)</span><span class="sxs-lookup"><span data-stu-id="88095-397">(aka "Conditional LValues")</span></span>

### <a name="motivation"></a><span data-ttu-id="88095-398">Motivation</span><span class="sxs-lookup"><span data-stu-id="88095-398">Motivation</span></span>
<span data-ttu-id="88095-399">L’utilisation de `ref` et `ref readonly` des variables locales exposait un besoin de référencer ces variables locales avec une ou une autre variable cible basée sur une condition.</span><span class="sxs-lookup"><span data-stu-id="88095-399">Use of `ref` and `ref readonly` locals exposed a need to ref-initialize such locals with one or another target variable based on a condition.</span></span>

<span data-ttu-id="88095-400">Une solution de contournement classique consiste à introduire une méthode comme :</span><span class="sxs-lookup"><span data-stu-id="88095-400">A typical workaround is to introduce a method like:</span></span>

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

<span data-ttu-id="88095-401">Notez que `Choice` n’est pas un remplacement exact d’un ternaire, car _tous les_ arguments doivent être évalués sur le site d’appel, ce qui a entraîné un comportement non intuitif et des bogues.</span><span class="sxs-lookup"><span data-stu-id="88095-401">Note that `Choice` is not an exact replacement of a ternary since _all_ arguments must be evaluated at the call site, which was leading to unintuitive behavior and bugs.</span></span>

<span data-ttu-id="88095-402">Les éléments suivants ne fonctionnent pas comme prévu :</span><span class="sxs-lookup"><span data-stu-id="88095-402">The following will not work as expected:</span></span>

```csharp
    // will crash with NRE because 'arr[0]' will be executed unconditionally
    ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

### <a name="solution"></a><span data-ttu-id="88095-403">Solution</span><span class="sxs-lookup"><span data-stu-id="88095-403">Solution</span></span>
<span data-ttu-id="88095-404">Autorise un type spécial d’expression conditionnelle qui prend la valeur d’une référence à l’un des arguments LValue en fonction d’une condition.</span><span class="sxs-lookup"><span data-stu-id="88095-404">Allow special kind of conditional expression that evaluates to a reference to one of LValue argument based on a condition.</span></span>

### <a name="using-ref-ternary-expression"></a><span data-ttu-id="88095-405">À l’aide d' `ref` expression ternaire.</span><span class="sxs-lookup"><span data-stu-id="88095-405">Using `ref` ternary expression.</span></span>

<span data-ttu-id="88095-406">La syntaxe de la `ref` version d’une expression conditionnelle est `<condition> ? ref <consequence> : ref <alternative>;`</span><span class="sxs-lookup"><span data-stu-id="88095-406">The syntax for the `ref` flavor of a conditional expression is `<condition> ? ref <consequence> : ref <alternative>;`</span></span>

<span data-ttu-id="88095-407">Comme avec l’expression conditionnelle ordinaire uniquement `<consequence>` ou `<alternative>` est évaluée en fonction du résultat de l’expression de condition booléenne.</span><span class="sxs-lookup"><span data-stu-id="88095-407">Just like with the ordinary conditional expression only `<consequence>` or `<alternative>` is evaluated depending on result of the boolean condition expression.</span></span>

<span data-ttu-id="88095-408">Contrairement à l’expression conditionnelle ordinaire, `ref` expression conditionnelle :</span><span class="sxs-lookup"><span data-stu-id="88095-408">Unlike ordinary conditional expression, `ref` conditional expression:</span></span>
- <span data-ttu-id="88095-409">requiert que `<consequence>` et `<alternative>` soient LValues.</span><span class="sxs-lookup"><span data-stu-id="88095-409">requires that `<consequence>` and `<alternative>` are LValues.</span></span>
- <span data-ttu-id="88095-410">`ref` expression conditionnelle elle-même est une LValue et</span><span class="sxs-lookup"><span data-stu-id="88095-410">`ref` conditional expression itself is an LValue and</span></span>
- <span data-ttu-id="88095-411">`ref` expression conditionnelle peut être accessible en écriture si `<consequence>` et `<alternative>` sont accessibles en écriture LValues</span><span class="sxs-lookup"><span data-stu-id="88095-411">`ref` conditional expression is writeable if both `<consequence>` and `<alternative>` are writeable LValues</span></span>

<span data-ttu-id="88095-412">Exemples :</span><span class="sxs-lookup"><span data-stu-id="88095-412">Examples:</span></span>  
<span data-ttu-id="88095-413">`ref` ternaire est une LValue et, par conséquent, elle peut être passée/affectée/retournée par référence ;</span><span class="sxs-lookup"><span data-stu-id="88095-413">`ref` ternary is an LValue and as such it can be passed/assigned/returned by reference;</span></span>
```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="88095-414">Étant une LValue, elle peut également être assignée à.</span><span class="sxs-lookup"><span data-stu-id="88095-414">Being an LValue, it can also be assigned to.</span></span>
```csharp
     // assign to
     (arr != null ? ref arr[0]: ref otherArr[0]) = 1;

     // error. readOnlyField is readonly and thus conditional expression is readonly
     (arr != null ? ref arr[0]: ref obj.readOnlyField) = 1;
```

<span data-ttu-id="88095-415">Peut être utilisé comme récepteur d’un appel de méthode et ignorer la copie si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="88095-415">Can be used as a receiver of a method call and skip copying if necessary.</span></span>
```csharp
     // no copies
     (arr != null ? ref arr[0]: ref otherArr[0]).StructMethod();

     // invoked on a copy.
     // The receiver is `readonly` because readOnlyField is readonly.
     (arr != null ? ref arr[0]: ref obj.readOnlyField).StructMethod();

     // no copies. `ReadonlyStructMethod` is a method on a `readonly` struct
     // and can be invoked directly on a readonly receiver
     (arr != null ? ref arr[0]: ref obj.readOnlyField).ReadonlyStructMethod();
```

<span data-ttu-id="88095-416">`ref` ternaire peut également être utilisé dans un contexte standard (non Ref).</span><span class="sxs-lookup"><span data-stu-id="88095-416">`ref` ternary can be used in a regular (not ref) context as well.</span></span>
```csharp
     // only an example
     // a regular ternary could work here just the same
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```

### <a name="drawbacks"></a><span data-ttu-id="88095-417">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="88095-417">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="88095-418">Je peux voir deux arguments principaux sur la prise en charge améliorée pour les références et les références ReadOnly :</span><span class="sxs-lookup"><span data-stu-id="88095-418">I can see two major arguments against enhanced support for references and readonly references:</span></span>

1) <span data-ttu-id="88095-419">Les problèmes résolus ici sont très anciens.</span><span class="sxs-lookup"><span data-stu-id="88095-419">The problems that are solved here are very old.</span></span> <span data-ttu-id="88095-420">Pourquoi les résoudre soudainement maintenant, en particulier dans la mesure où elle n’aide pas le code existant ?</span><span class="sxs-lookup"><span data-stu-id="88095-420">Why suddenly solve them now, especially since it would not help existing code?</span></span>

<span data-ttu-id="88095-421">Comme nous l' C# avons trouvé et que .net est utilisé dans les nouveaux domaines, certains problèmes deviennent plus évidents.</span><span class="sxs-lookup"><span data-stu-id="88095-421">As we find C# and .Net used in new domains, some problems become more prominent.</span></span>  
<span data-ttu-id="88095-422">Comme des exemples d’environnements qui sont plus critiques que la moyenne des frais de calcul, je peux répertorier</span><span class="sxs-lookup"><span data-stu-id="88095-422">As examples of environments that are more critical than average about computation overheads, I can list</span></span>

* <span data-ttu-id="88095-423">scénarios Cloud/de centre de donnes où le calcul est facturé et la réactivité est un avantage concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="88095-423">cloud/datacenter scenarios where computation is billed for and responsiveness is a competitive advantage.</span></span>
* <span data-ttu-id="88095-424">Jeux/VR/AR avec exigences de temps réel en temps réel sur les latences</span><span class="sxs-lookup"><span data-stu-id="88095-424">Games/VR/AR with soft-realtime requirements on latencies</span></span>     

<span data-ttu-id="88095-425">Cette fonctionnalité ne sacrifie pas les forces existantes telles que le type-Safety, tout en permettant de réduire les surplus dans certains scénarios courants.</span><span class="sxs-lookup"><span data-stu-id="88095-425">This feature does not sacrifice any of the existing strengths such as type-safety, while allowing to lower overheads in some common scenarios.</span></span>

2) <span data-ttu-id="88095-426">Est-il possible de garantir raisonnablement que l’appelé sera joué par les règles lorsqu’il se contente de `readonly` des contrats ?</span><span class="sxs-lookup"><span data-stu-id="88095-426">Can we reasonably guarantee that the callee will play by the rules when it opts into `readonly` contracts?</span></span>

<span data-ttu-id="88095-427">Nous avons une confiance similaire lors de l’utilisation de `out`.</span><span class="sxs-lookup"><span data-stu-id="88095-427">We have similar trust when using `out`.</span></span> <span data-ttu-id="88095-428">Une implémentation incorrecte de `out` peut entraîner un comportement non spécifié, mais, en réalité, elle se produit rarement.</span><span class="sxs-lookup"><span data-stu-id="88095-428">Incorrect implementation of `out` can cause unspecified behavior, but in reality it rarely happens.</span></span>  

<span data-ttu-id="88095-429">Le fait de faire en sorte que les règles de vérification formelles soient familières à `ref readonly` atténuer le problème d’approbation.</span><span class="sxs-lookup"><span data-stu-id="88095-429">Making the formal verification rules familiar with `ref readonly` would further mitigate the trust issue.</span></span>

### <a name="alternatives"></a><span data-ttu-id="88095-430">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="88095-430">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="88095-431">La conception concurrente principale est vraiment « ne rien faire ».</span><span class="sxs-lookup"><span data-stu-id="88095-431">The main competing design is really "do nothing".</span></span>

### <a name="unresolved-questions"></a><span data-ttu-id="88095-432">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="88095-432">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

### <a name="design-meetings"></a><span data-ttu-id="88095-433">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="88095-433">Design meetings</span></span>

<span data-ttu-id="88095-434">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md</span><span class="sxs-lookup"><span data-stu-id="88095-434">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md</span></span>
