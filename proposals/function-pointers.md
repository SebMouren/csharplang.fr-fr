---
ms.openlocfilehash: d9080202f9413f8beb80db222d47f5fc082ae641
ms.sourcegitcommit: f3170512e7a3193efbcea52ec330648375e36915
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79485503"
---
# <a name="function-pointers"></a><span data-ttu-id="a8e1f-101">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="a8e1f-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="a8e1f-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="a8e1f-102">Summary</span></span>

<span data-ttu-id="a8e1f-103">Cette proposition fournit des constructions de langage qui exposent des opcodes IL qui ne sont pas actuellement accessibles efficacement, C# ou du tout, dans aujourd’hui : `ldftn` et `calli`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="a8e1f-104">Ces opcodes IL peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="a8e1f-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="a8e1f-105">Motivation</span></span>

<span data-ttu-id="a8e1f-106">Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="a8e1f-107">Il s’agit d’une proposition de conception alternative aux [intrinsèques du compilateur](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="a8e1f-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="a8e1f-108">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="a8e1f-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="a8e1f-109">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="a8e1f-109">Function pointers</span></span>

<span data-ttu-id="a8e1f-110">Le langage autorisera la déclaration des pointeurs de fonction à l’aide de la syntaxe `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="a8e1f-111">La syntaxe complète est décrite en détail dans la section suivante, mais elle doit ressembler à la syntaxe utilisée par les déclarations de type `Func` et `Action`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="a8e1f-112">Ces types sont représentés à l’aide du type de pointeur de fonction, comme indiqué dans la norme ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="a8e1f-113">Cela signifie que l’appel d’un `delegate*` utilise `calli` où l’appel d’un `delegate` utilisera `callvirt` sur la méthode `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="a8e1f-114">Toutefois, la syntaxe de l’appel est identique pour les deux constructions.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="a8e1f-115">La définition ECMA-335 des pointeurs de méthode comprend la Convention d’appel dans le cadre de la signature de type (section 7,1).</span><span class="sxs-lookup"><span data-stu-id="a8e1f-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="a8e1f-116">La Convention d’appel par défaut sera `managed`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="a8e1f-117">Les autres formulaires peuvent être spécifiés en ajoutant le modificateur approprié après la syntaxe de `delegate*` : `managed`, `cdecl`, `stdcall`, `thiscall`ou `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-117">Alternate forms can be specified by adding the appropriate modifier after the `delegate*` syntax: `managed`, `cdecl`, `stdcall`, `thiscall`, or `unmanaged`.</span></span> <span data-ttu-id="a8e1f-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-118">Example:</span></span>

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

<span data-ttu-id="a8e1f-119">Les conversions entre les types de `delegate*` sont effectuées en fonction de leur signature, y compris la Convention d’appel.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-119">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* cdecl<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

<span data-ttu-id="a8e1f-120">Un type de `delegate*` est un type pointeur, ce qui signifie qu’il possède toutes les fonctionnalités et restrictions d’un type pointeur standard :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-120">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="a8e1f-121">Valide uniquement dans un contexte de `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-121">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="a8e1f-122">Les méthodes qui contiennent un paramètre `delegate*` ou un type de retour ne peuvent être appelées qu’à partir d’un contexte de `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-122">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="a8e1f-123">Ne peut pas être converti en `object`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-123">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="a8e1f-124">Ne peut pas être utilisé comme argument générique.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-124">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="a8e1f-125">Peut convertir implicitement `delegate*` en `void*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-125">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="a8e1f-126">Peut effectuer une conversion explicite de `void*` en `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-126">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="a8e1f-127">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-127">Restrictions:</span></span>

- <span data-ttu-id="a8e1f-128">Les attributs personnalisés ne peuvent pas être appliqués à un `delegate*` ou à l’un de ses éléments.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-128">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="a8e1f-129">Un paramètre de `delegate*` ne peut pas être marqué comme `params`</span><span class="sxs-lookup"><span data-stu-id="a8e1f-129">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="a8e1f-130">Un type de `delegate*` présente toutes les restrictions d’un type pointeur normal.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-130">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="a8e1f-131">Syntaxe du pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="a8e1f-131">Function pointer syntax</span></span>

<span data-ttu-id="a8e1f-132">La syntaxe complète du pointeur fonction est représentée par la grammaire suivante :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-132">The full function pointer syntax is represented by the following grammar:</span></span>

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention? '<' (funcptr_parameter_modifier? type ',')* funcptr_return_modifier? return_type '>'
    ;

calling_convention
    : 'cdecl'
    | 'managed'
    | 'stdcall'
    | 'thiscall'
    | 'unmanaged'
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

<span data-ttu-id="a8e1f-133">La Convention d’appel `unmanaged` représente la Convention d’appel par défaut pour le code natif sur la plateforme actuelle, et est encodée en winapi.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-133">The `unmanaged` calling convention represents the default calling convention for native code on the current platform, and is encoded as winapi.</span></span>
<span data-ttu-id="a8e1f-134">Tous les `calling_convention`s sont des mots clés contextuels lorsqu’ils sont précédés d’un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-134">All `calling_convention`s are contextual keywords when preceded by a `delegate*`.</span></span>

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a><span data-ttu-id="a8e1f-135">Conversions de pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="a8e1f-135">Function pointer conversions</span></span>

<span data-ttu-id="a8e1f-136">Dans un contexte non sécurisé, l’ensemble des conversions implicites disponibles (conversions implicites) est étendu pour inclure les conversions de pointeur implicites suivantes :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-136">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="a8e1f-137">_Conversions existantes_</span><span class="sxs-lookup"><span data-stu-id="a8e1f-137">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="a8e1f-138">À partir de _funcptr\_type_ `F0` à un autre `F1`_type de\_funcptr_ , à condition que les conditions suivantes soient remplies :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-138">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="a8e1f-139">`F0` et `F1` ont le même nombre de paramètres, et chaque paramètre `D0n` dans `F0` a les mêmes modificateurs `ref`, `out`ou `in` que le paramètre correspondant `D1n` dans `F1`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-139">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="a8e1f-140">Pour chaque paramètre de valeur (paramètre sans `ref`, `out`ou modificateur `in`), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `F0` vers le type de paramètre correspondant dans `F1`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-140">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="a8e1f-141">Pour chaque paramètre `ref`, `out`ou `in`, le type de paramètre dans `F0` est le même que le type de paramètre correspondant dans `F1`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-141">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="a8e1f-142">Si le type de retour est par valeur (aucune `ref` ou `ref readonly`), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F1` au type de retour de `F0`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-142">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="a8e1f-143">Si le type de retour est par référence (`ref` ou `ref readonly`), le type de retour et les modificateurs de `ref` de `F1` sont les mêmes que le type de retour et les modificateurs `ref` de `F0`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-143">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="a8e1f-144">La Convention d’appel de `F0` est identique à la Convention d’appel de `F1`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-144">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="a8e1f-145">Autoriser l’adresse à cibler des méthodes</span><span class="sxs-lookup"><span data-stu-id="a8e1f-145">Allow address-of to target methods</span></span>

<span data-ttu-id="a8e1f-146">Les groupes de méthodes sont désormais autorisés comme arguments d’une expression d’adresse.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-146">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="a8e1f-147">Le type d’une telle expression sera un `delegate*` qui a la signature équivalente de la méthode cible et une convention d’appel managée :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-147">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;

        // Okay. Conversion to void* is always allowed.
        void* v = &Util.Log;
   }
}
```

<span data-ttu-id="a8e1f-148">Dans un contexte unsafe, une méthode `M` est compatible avec un type de pointeur fonction `F` si toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-148">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="a8e1f-149">`M` et `F` ont le même nombre de paramètres, et chaque paramètre dans `D` a les mêmes modificateurs `ref`, `out`ou `in` que le paramètre correspondant dans `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-149">`M` and `F` have the same number of parameters, and each parameter in `D` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="a8e1f-150">Pour chaque paramètre de valeur (paramètre sans `ref`, `out`ou modificateur `in`), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `M` vers le type de paramètre correspondant dans `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-150">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="a8e1f-151">Pour chaque paramètre `ref`, `out`ou `in`, le type de paramètre dans `M` est le même que le type de paramètre correspondant dans `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-151">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="a8e1f-152">Si le type de retour est par valeur (aucune `ref` ou `ref readonly`), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F` au type de retour de `M`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-152">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="a8e1f-153">Si le type de retour est par référence (`ref` ou `ref readonly`), le type de retour et les modificateurs de `ref` de `F` sont les mêmes que le type de retour et les modificateurs `ref` de `M`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-153">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="a8e1f-154">La Convention d’appel de `M` est identique à la Convention d’appel de `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-154">The calling convention of `M` is the same as the calling convention of `F`.</span></span>
- <span data-ttu-id="a8e1f-155">`M` est une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-155">`M` is a static method.</span></span>

<span data-ttu-id="a8e1f-156">Dans un contexte non sécurisé, une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe de méthodes `E` à un type pointeur fonction compatible `F` si `E` contient au moins une méthode qui est applicable dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de `F`, comme décrit dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-156">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="a8e1f-157">Une seule méthode `M` est sélectionnée correspondant à un appel de méthode de la forme `E(A)` avec les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-157">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="a8e1f-158">La liste d’arguments `A` est une liste d’expressions, chacune classée en tant que variable et avec le type et le modificateur (`ref`, `out`ou `in`) du _paramètre\_formel correspondant\_liste_ des `D`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-158">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _formal\_parameter\_list_ of `D`.</span></span>
   - <span data-ttu-id="a8e1f-159">Les méthodes candidates sont uniquement les méthodes qui sont uniquement celles qui sont applicables dans leur forme normale, et non celles qui s’appliquent dans leur forme développée.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-159">The candidate methods are only those methods that are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
- <span data-ttu-id="a8e1f-160">Si l’algorithme d’appels de méthode génère une erreur, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-160">If the algorithm of Method invocations produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="a8e1f-161">Dans le cas contraire, l’algorithme produit une seule meilleure méthode `M` avoir le même nombre de paramètres que `F` et la conversion est considérée comme existante.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-161">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="a8e1f-162">La méthode sélectionnée `M` doit être compatible (comme défini ci-dessus) avec le type de pointeur de fonction `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-162">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="a8e1f-163">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-163">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="a8e1f-164">Le résultat de la conversion est un pointeur de fonction de type `F`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-164">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="a8e1f-165">Une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe de méthodes `E` pour `void*` s’il n’existe qu’une seule méthode statique `M` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-165">An implicit conversion exists from an address-of expression whose target is a method group `E` to `void*` if there is only one static method `M` in `E`.</span></span>
<span data-ttu-id="a8e1f-166">S’il y a une méthode statique, la seule meilleure méthode de `E` est `M`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-166">If there is one static method, then the single best method from `E` is `M`.</span></span>
<span data-ttu-id="a8e1f-167">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-167">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="a8e1f-168">Cela signifie que les développeurs peuvent dépendre de règles de résolution de surcharge pour fonctionner conjointement avec l’opérateur d’adresse :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-168">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

<span data-ttu-id="a8e1f-169">L’opérateur address-of sera implémenté à l’aide de l’instruction `ldftn`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-169">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="a8e1f-170">Restrictions de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-170">Restrictions of this feature:</span></span>

- <span data-ttu-id="a8e1f-171">S’applique uniquement aux méthodes marquées comme `static`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-171">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="a8e1f-172">Les fonctions locales non`static` ne peuvent pas être utilisées dans les `&`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-172">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="a8e1f-173">Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-173">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="a8e1f-174">Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-174">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="a8e1f-175">Meilleure fonction membre</span><span class="sxs-lookup"><span data-stu-id="a8e1f-175">Better function member</span></span>

<span data-ttu-id="a8e1f-176">La meilleure spécification de membre de fonction sera modifiée pour inclure la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-176">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="a8e1f-177">Une `delegate*` est plus spécifique que `void*`</span><span class="sxs-lookup"><span data-stu-id="a8e1f-177">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="a8e1f-178">Cela signifie qu’il est possible de surcharger sur `void*` et un `delegate*` tout en utilisant judicieusement l’opérateur d’adresse.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-178">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="open-issues"></a><span data-ttu-id="a8e1f-179">Problèmes ouverts</span><span class="sxs-lookup"><span data-stu-id="a8e1f-179">Open Issues</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="a8e1f-180">NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="a8e1f-180">NativeCallableAttribute</span></span>

<span data-ttu-id="a8e1f-181">Il s’agit d’un attribut utilisé par le CLR pour éviter le prologue managé vers natif lors de l’appel de.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-181">This is an attribute used by the CLR to avoid the managed to native prologue when invoking.</span></span> <span data-ttu-id="a8e1f-182">Les méthodes marquées par cet attribut peuvent uniquement être appelées à partir du code natif, non managées (impossible d’appeler des méthodes, créer un délégué, etc.).</span><span class="sxs-lookup"><span data-stu-id="a8e1f-182">Methods marked by this attribute are only callable from native code, not managed (can’t call methods, create a delegate, etc …).</span></span> <span data-ttu-id="a8e1f-183">L’attribut n’est pas spécial pour mscorlib ; le runtime traitera tout attribut portant ce nom avec la même sémantique.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-183">The attribute is not special to mscorlib; the runtime will treat any attribute with this name with the same semantics.</span></span>

<span data-ttu-id="a8e1f-184">Il est possible que le runtime et le langage fonctionnent ensemble pour une prise en charge complète.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-184">It's possible for the runtime and language to work together to fully support this.</span></span> <span data-ttu-id="a8e1f-185">Le langage peut choisir de traiter l’adresse de `static` membres avec un attribut `NativeCallable` en tant que `delegate*` avec la Convention d’appel spécifiée.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-185">The language could choose to treat address-of `static` members with a `NativeCallable` attribute as a `delegate*` with the specified calling convention.</span></span>

``` csharp
unsafe class NativeCallableExample {
    [NativeCallable(CallingConvention.CDecl)]
    static void CloseHandle(IntPtr p) => Marshal.FreeHGlobal(p);

    void Use() {
        delegate*<IntPtr, void> p1 = &CloseHandle; // Error: Invalid calling convention

        delegate* cdecl<IntPtr, void> p2 = &CloseHandle; // Okay
    }
}

```

<span data-ttu-id="a8e1f-186">En outre, le langage souhaiterait probablement également :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-186">Additionally the language would likely also want to:</span></span>

- <span data-ttu-id="a8e1f-187">Marquez tous les appels managés à une méthode marquée avec `NativeCallable` comme une erreur.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-187">Flag any managed calls to a method tagged with `NativeCallable` as an error.</span></span> <span data-ttu-id="a8e1f-188">Étant donné que la fonction ne peut pas être appelée à partir du code managé, le compilateur doit empêcher les développeurs de tenter un tel appel.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-188">Given the function can't be invoked from managed code the compiler should prevent developers from attempting such an invocation.</span></span>
- <span data-ttu-id="a8e1f-189">Empêche les conversions de groupe de méthodes en `delegate` lorsque la méthode est marquée avec `NativeCallable`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-189">Prevent method group conversions to `delegate` when the method is tagged with `NativeCallable`.</span></span>

<span data-ttu-id="a8e1f-190">Toutefois, cela n’est pas nécessaire pour prendre en charge `NativeCallable`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-190">This is not necessary to support `NativeCallable` though.</span></span> <span data-ttu-id="a8e1f-191">Le compilateur peut prendre en charge l’attribut `NativeCallable` comme utilise la syntaxe existante.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-191">The compiler can support the `NativeCallable` attribute as is using the existing syntax.</span></span> <span data-ttu-id="a8e1f-192">Le programme devra simplement effectuer un cast en `void*` avant d’effectuer un cast vers la signature de `delegate*` correcte.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-192">The program would simply need to cast to `void*` before casting to the correct `delegate*` signature.</span></span> <span data-ttu-id="a8e1f-193">Cela ne serait pas pire que le support technique aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-193">That would be no worse than the support today.</span></span>

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a><span data-ttu-id="a8e1f-194">Ensemble extensible de conventions d’appel non managées</span><span class="sxs-lookup"><span data-stu-id="a8e1f-194">Extensible set of unmanaged calling conventions</span></span>

<span data-ttu-id="a8e1f-195">L’ensemble de conventions d’appel non managées prises en charge par les encodages ECMA-335 actuels est obsolète.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-195">The set of unmanaged calling conventions supported by the current ECMA-335 encodings is outdated.</span></span> <span data-ttu-id="a8e1f-196">Nous avons vu des demandes d’ajout de la prise en charge de plus de conventions d’appel non managées, par exemple :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-196">We have seen requests to add support for more unmanaged calling conventions, for example:</span></span>

- <span data-ttu-id="a8e1f-197">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120</span><span class="sxs-lookup"><span data-stu-id="a8e1f-197">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120</span></span>
- <span data-ttu-id="a8e1f-198">StdCall avec explicite This https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span><span class="sxs-lookup"><span data-stu-id="a8e1f-198">StdCall with explicit this https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span></span>

<span data-ttu-id="a8e1f-199">La conception de cette fonctionnalité doit permettre d’étendre l’ensemble des conventions d’appel non managées si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-199">The design of this feature should allow extending the set of unmanaged calling conventions as needed in future.</span></span> <span data-ttu-id="a8e1f-200">Les problèmes incluent un espace limité pour l’encodage des conventions d’appel (12 valeurs sur 16 sont prises en `IMAGE_CEE_CS_CALLCONV_MASK`) et le nombre d’emplacements qui doivent être horodatés pour ajouter une nouvelle convention d’appel.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-200">The problems include limited space for encoding calling conventions (12 out of 16 values are taken in `IMAGE_CEE_CS_CALLCONV_MASK`) and number of places that need to be touched in order to add a new calling convention.</span></span> <span data-ttu-id="a8e1f-201">Une solution possible consiste à introduire un nouvel encodage qui représente la Convention d’appel à l’aide de [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) Enum.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-201">A potential solution is to introduce a new encoding that represents the calling convention using [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.</span></span>

<span data-ttu-id="a8e1f-202">Pour référence, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h contient la liste des conventions d’appel prises en charge par LLVM.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-202">For reference, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h has the list of calling conventions supported by LLVM.</span></span> <span data-ttu-id="a8e1f-203">Même s’il est peu probable que .NET doive en prendre en charge tous, il démontre que l’espace de conventions d’appel est très riche.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-203">While it is unlikely that .NET will ever need to support all of them, it demonstrates that the space of calling conventions is very rich.</span></span>

## <a name="considerations"></a><span data-ttu-id="a8e1f-204">Considérations</span><span class="sxs-lookup"><span data-stu-id="a8e1f-204">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="a8e1f-205">Autoriser les méthodes d’instance</span><span class="sxs-lookup"><span data-stu-id="a8e1f-205">Allow instance methods</span></span>

<span data-ttu-id="a8e1f-206">La proposition peut être étendue pour prendre en charge les méthodes d’instance en tirant parti de la Convention d’appel `EXPLICITTHIS` C# CLI (nommée `instance` dans le code).</span><span class="sxs-lookup"><span data-stu-id="a8e1f-206">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="a8e1f-207">Cette forme de pointeurs de fonction CLI place le paramètre `this` en tant que premier paramètre explicite de la syntaxe du pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-207">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="a8e1f-208">C’est un son, mais il ajoute une certaine compliquation à la proposition.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-208">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="a8e1f-209">En particulier, les pointeurs de fonction qui diffèrent par la Convention d’appel `instance` et `managed` seraient incompatibles même si les deux cas sont utilisés pour appeler C# des méthodes managées avec la même signature.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-209">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="a8e1f-210">Dans tous les cas, dans tous les cas, il est utile de disposer d’une solution de contournement simple : utilisez une `static` fonction locale.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-210">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="a8e1f-211">Ne nécessite pas de non-sécurité au niveau de la déclaration</span><span class="sxs-lookup"><span data-stu-id="a8e1f-211">Don't require unsafe at declaration</span></span>

<span data-ttu-id="a8e1f-212">Au lieu de demander à `unsafe` à chaque utilisation d’une `delegate*`, n’en avez besoin qu’au point où un groupe de méthodes est converti en `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-212">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="a8e1f-213">C’est là qu’interviennent les principaux problèmes de sécurité (sachant que l’assembly conteneur ne peut pas être déchargé tant que la valeur est active).</span><span class="sxs-lookup"><span data-stu-id="a8e1f-213">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="a8e1f-214">Il peut être considéré comme excessif de `unsafe` les autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-214">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="a8e1f-215">C’est ainsi que la conception était prévue à l’origine.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-215">This is how the design was originally intended.</span></span> <span data-ttu-id="a8e1f-216">Mais les règles de langage qui en résultent sont très maladroites.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-216">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="a8e1f-217">Il est impossible de masquer le fait qu’il s’agit d’une valeur de pointeur et qu’elle reste en lecture seule sans le mot clé `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-217">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="a8e1f-218">Par exemple, la conversion en `object` ne peut pas être autorisée, elle ne peut pas être membre d’un `class`, etc... La C# conception est d’exiger `unsafe` pour toutes les utilisations du pointeur et cette conception suit donc cela.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-218">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="a8e1f-219">Les développeurs pourront toujours présenter un wrapper _sécurisé_ au-dessus des valeurs `delegate*` de la même façon que pour les types pointeur normaux.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-219">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="a8e1f-220">Vous devez :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-220">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="a8e1f-221">Utilisation de délégués</span><span class="sxs-lookup"><span data-stu-id="a8e1f-221">Using delegates</span></span>

<span data-ttu-id="a8e1f-222">Au lieu d’utiliser un nouvel élément de syntaxe, `delegate*`, il vous suffit d’utiliser les types de `delegate` existants avec un `*` suivant le type :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-222">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="a8e1f-223">La gestion de la Convention d’appel peut être effectuée en annotant les types de `delegate` avec un attribut qui spécifie une valeur de `CallingConvention`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-223">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="a8e1f-224">L’absence d’un attribut signifie la Convention d’appel managée.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-224">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="a8e1f-225">L’encodage de ce en IL pose problème.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-225">Encoding this in IL is problematic.</span></span> <span data-ttu-id="a8e1f-226">La valeur sous-jacente doit être représentée sous la forme d’un pointeur, mais elle doit également :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-226">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="a8e1f-227">Avoir un type unique pour autoriser les surcharges avec différents types de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-227">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="a8e1f-228">Être équivalent à des fins de OHI dans les limites de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-228">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="a8e1f-229">Le dernier point est particulièrement problématique.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-229">The last point is particularly problematic.</span></span> <span data-ttu-id="a8e1f-230">Cela signifie que chaque assembly qui utilise `Func<int>*` doit encoder un type équivalent dans les métadonnées même si `Func<int>*` est défini dans un assembly, même si ne contrôle pas.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-230">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="a8e1f-231">En outre, tout autre type défini avec le nom `System.Func<T>` dans un assembly qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-231">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="a8e1f-232">Une option qui a été explored a émis un tel pointeur comme `mod_req(Func<int>) void*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-232">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="a8e1f-233">Cela ne fonctionne pas tant qu’un `mod_req` ne peut pas être lié à un `TypeSpec` et ne peut donc pas cibler des instanciations génériques.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-233">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="a8e1f-234">Pointeurs de fonction nommés</span><span class="sxs-lookup"><span data-stu-id="a8e1f-234">Named function pointers</span></span>

<span data-ttu-id="a8e1f-235">La syntaxe du pointeur de fonction peut être lourde, en particulier dans les cas complexes comme les pointeurs de fonction imbriqués.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-235">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="a8e1f-236">Au lieu de demander aux développeurs de taper la signature chaque fois que le langage peut autoriser des déclarations nommées de pointeurs de fonction, comme c’est le cas avec `delegate`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-236">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="a8e1f-237">Une partie du problème ici est que la primitive de l’interface CLI sous-jacente n’a pas C# de nom, ce qui n’est pas vraiment une invention et nécessite un peu de métadonnées à activer.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-237">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="a8e1f-238">C’est réalisable, mais il s’agit d’un travail important.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-238">That is doable but is a significant about of work.</span></span> <span data-ttu-id="a8e1f-239">Il est essentiel C# d’avoir un complément à la table de définition de type pour ces noms uniquement.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-239">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="a8e1f-240">De même, lorsque les arguments des pointeurs de fonction nommés ont été examinés, nous avons trouvé qu’ils s’appliquent aussi bien à un certain nombre d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-240">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="a8e1f-241">Par exemple, il serait tout aussi commode de déclarer des tuples nommés pour réduire le besoin de taper la signature complète dans tous les cas.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-241">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="a8e1f-242">Après la discussion, nous avons décidé de ne pas autoriser la déclaration nommée des types de `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-242">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="a8e1f-243">Si nous constatons qu’il y a des besoins importants en ce qui concerne les commentaires sur l’utilisation des clients, nous étudierons une solution d’attribution de noms qui fonctionne pour les pointeurs de fonction, les tuples, les génériques, etc. Cela est susceptible d’être similaire à d’autres suggestions, par exemple la prise en charge de `typedef` complète dans le langage.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-243">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="a8e1f-244">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="a8e1f-244">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="a8e1f-245">fonctions locales statiques</span><span class="sxs-lookup"><span data-stu-id="a8e1f-245">static local functions</span></span>

<span data-ttu-id="a8e1f-246">Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/1565) pour autoriser le modificateur `static` sur les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-246">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="a8e1f-247">Une telle fonction serait toujours émise comme `static` et avec la signature exacte spécifiée dans le code source.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-247">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="a8e1f-248">Une telle fonction doit être un argument valide pour `&`, car elle ne contient aucun des problèmes liés aux fonctions locales aujourd’hui</span><span class="sxs-lookup"><span data-stu-id="a8e1f-248">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today</span></span>

### <a name="static-delegates"></a><span data-ttu-id="a8e1f-249">délégués statiques</span><span class="sxs-lookup"><span data-stu-id="a8e1f-249">static delegates</span></span>

<span data-ttu-id="a8e1f-250">Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/302) permettant de déclarer des types de `delegate` qui peuvent uniquement faire référence à des membres de `static`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-250">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="a8e1f-251">L’avantage est que ces instances de `delegate` peuvent être allouées gratuitement et mieux dans des scénarios de performances sensibles.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-251">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="a8e1f-252">Si la fonctionnalité du pointeur de fonction est implémentée, le `static delegate` proposition sera probablement fermée. L’avantage proposé par cette fonctionnalité est la nature libre de l’allocation.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-252">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="a8e1f-253">Toutefois, des investigations récentes ont détecté que le déchargement d’assembly n’est pas possible en raison du déchargement de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-253">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="a8e1f-254">Il doit y avoir un handle fort de l' `static delegate` à la méthode à laquelle il fait référence pour empêcher l’assembly d’être déchargé à partir de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-254">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="a8e1f-255">Pour conserver chaque `static delegate` instance est nécessaire pour allouer un nouveau descripteur qui s’exécute sur les objectifs de la proposition.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-255">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="a8e1f-256">Il existait des conceptions où l’allocation pouvait être amortie en une seule allocation par site d’appel, mais cela était un peu complexe et ne semble pas avoir un intérêt pour le compromis.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-256">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="a8e1f-257">Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants :</span><span class="sxs-lookup"><span data-stu-id="a8e1f-257">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="a8e1f-258">Sécurité face au déchargement d’un assembly : cela nécessite des allocations et par conséquent `delegate` est déjà une option suffisante.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-258">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="a8e1f-259">Aucune sécurité en face du déchargement d’assembly : utilisez un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-259">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="a8e1f-260">Cela peut être encapsulé dans un `struct` pour permettre l’utilisation en dehors d’un contexte de `unsafe` dans le reste du code.</span><span class="sxs-lookup"><span data-stu-id="a8e1f-260">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
