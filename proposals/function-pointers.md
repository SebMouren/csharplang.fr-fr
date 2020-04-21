---
ms.openlocfilehash: e5ab385f498c0d96c55e60751bb204e7217f6eab
ms.sourcegitcommit: 95f5f86ba2e2a23cd4fb37bd9d1ff690c83d1191
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81646684"
---
# <a name="function-pointers"></a><span data-ttu-id="d0f26-101">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="d0f26-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="d0f26-102">Summary</span></span>

<span data-ttu-id="d0f26-103">Cette proposition fournit des constructions linguistiques qui exposent les codes d’opcodes de `ldftn` `calli`l’IL qui ne peuvent pas actuellement être consultés efficacement, ou du tout, dans Cô aujourd’hui: et .</span><span class="sxs-lookup"><span data-stu-id="d0f26-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="d0f26-104">Ces codes d’opcodes IL peuvent être importants dans le code haute performance et les développeurs ont besoin d’un moyen efficace d’y accéder.</span><span class="sxs-lookup"><span data-stu-id="d0f26-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="d0f26-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="d0f26-105">Motivation</span></span>

<span data-ttu-id="d0f26-106">Les motivations et les antécédents de cette fonctionnalité sont décrits dans le numéro suivant (comme c’est une mise en œuvre potentielle de la fonctionnalité):</span><span class="sxs-lookup"><span data-stu-id="d0f26-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="d0f26-107">Il s’agit d’une proposition de conception alternative pour [compiler intrinsèques](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="d0f26-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="d0f26-108">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="d0f26-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="d0f26-109">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-109">Function pointers</span></span>

<span data-ttu-id="d0f26-110">Le langage permettra la déclaration des pointeurs de fonction à l’aide de la `delegate*` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="d0f26-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="d0f26-111">La syntaxe complète est décrite en détail dans la section suivante, mais elle est destinée à ressembler à la syntaxe utilisée par `Func` et `Action` les déclarations de type.</span><span class="sxs-lookup"><span data-stu-id="d0f26-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="d0f26-112">Ces types sont représentés à l’aide du type de pointeur de fonction tel qu’il est décrit dans ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="d0f26-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="d0f26-113">Cela `delegate*` signifie l’invocation `calli` d’un volonté `delegate` utilisé `callvirt` où l’invocation d’un volonté utilisé sur la `Invoke` méthode.</span><span class="sxs-lookup"><span data-stu-id="d0f26-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="d0f26-114">Syntactiquement cependant l’invocation est identique pour les deux constructions.</span><span class="sxs-lookup"><span data-stu-id="d0f26-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="d0f26-115">La définition ecMA-335 des pointeurs de méthode inclut la convention d’appel dans le cadre de la signature type (article 7.1).</span><span class="sxs-lookup"><span data-stu-id="d0f26-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="d0f26-116">La convention d’appel par défaut sera `managed`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="d0f26-117">D’autres formes peuvent être spécifiées `delegate*` en `managed`ajoutant `cdecl` `stdcall`le `thiscall`modificateur approprié après la syntaxe : , , , , ou `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-117">Alternate forms can be specified by adding the appropriate modifier after the `delegate*` syntax: `managed`, `cdecl`, `stdcall`, `thiscall`, or `unmanaged`.</span></span> <span data-ttu-id="d0f26-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d0f26-118">Example:</span></span>

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

<span data-ttu-id="d0f26-119">Les conversions entre `delegate*` types se font en fonction de leur signature, y compris la convention d’appel.</span><span class="sxs-lookup"><span data-stu-id="d0f26-119">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

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

<span data-ttu-id="d0f26-120">Un `delegate*` type est un type de pointeur qui signifie qu’il a toutes les capacités et les restrictions d’un type de pointeur standard:</span><span class="sxs-lookup"><span data-stu-id="d0f26-120">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="d0f26-121">Seulement valable `unsafe` dans un contexte.</span><span class="sxs-lookup"><span data-stu-id="d0f26-121">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="d0f26-122">Les méthodes `delegate*` qui contiennent un paramètre ou `unsafe` un type de retour ne peuvent être appelées que dans un contexte.</span><span class="sxs-lookup"><span data-stu-id="d0f26-122">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="d0f26-123">Impossible de convertir `object`en .</span><span class="sxs-lookup"><span data-stu-id="d0f26-123">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="d0f26-124">Ne peut pas être utilisé comme argument générique.</span><span class="sxs-lookup"><span data-stu-id="d0f26-124">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="d0f26-125">Peut implicitement `delegate*` `void*`convertir en .</span><span class="sxs-lookup"><span data-stu-id="d0f26-125">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="d0f26-126">Peut explicitement `void*` convertir `delegate*`de .</span><span class="sxs-lookup"><span data-stu-id="d0f26-126">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="d0f26-127">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="d0f26-127">Restrictions:</span></span>

- <span data-ttu-id="d0f26-128">Les attributs personnalisés `delegate*` ne peuvent pas être appliqués à un ou à l’un de ses éléments.</span><span class="sxs-lookup"><span data-stu-id="d0f26-128">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="d0f26-129">Un `delegate*` paramètre ne peut pas être marqué comme`params`</span><span class="sxs-lookup"><span data-stu-id="d0f26-129">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="d0f26-130">Un `delegate*` type a toutes les restrictions d’un type de pointeur normal.</span><span class="sxs-lookup"><span data-stu-id="d0f26-130">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="d0f26-131">Syntaxe de pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-131">Function pointer syntax</span></span>

<span data-ttu-id="d0f26-132">La syntaxe de pointeur de fonction complète est représentée par la grammaire suivante :</span><span class="sxs-lookup"><span data-stu-id="d0f26-132">The full function pointer syntax is represented by the following grammar:</span></span>

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

<span data-ttu-id="d0f26-133">La `unmanaged` convention d’appel représente la convention d’appel par défaut pour le code natif sur la plate-forme actuelle, et est codé comme winapi.</span><span class="sxs-lookup"><span data-stu-id="d0f26-133">The `unmanaged` calling convention represents the default calling convention for native code on the current platform, and is encoded as winapi.</span></span>
<span data-ttu-id="d0f26-134">Tous `calling_convention`sont des mots-clés contextuels lorsqu’ils sont précédés d’un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-134">All `calling_convention`s are contextual keywords when preceded by a `delegate*`.</span></span>

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

### <a name="function-pointer-conversions"></a><span data-ttu-id="d0f26-135">Conversions de pointeurs de fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-135">Function pointer conversions</span></span>

<span data-ttu-id="d0f26-136">Dans un contexte dangereux, l’ensemble des conversions implicites disponibles (conversions implicites) est étendu pour inclure les conversions implicites suivantes :</span><span class="sxs-lookup"><span data-stu-id="d0f26-136">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="d0f26-137">_Conversions existantes_</span><span class="sxs-lookup"><span data-stu-id="d0f26-137">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="d0f26-138">Du `F0` type _funcptr\__ à un autre type `F1` _funcptr\__ , à condition que tous les éléments suivants sont vrais:</span><span class="sxs-lookup"><span data-stu-id="d0f26-138">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="d0f26-139">`F0`et `F1` ont le même nombre de `D0n` paramètres, et `ref` `out`chaque `in` paramètre a `F0` le `D1n` `F1`même , , ou modificateurs que le paramètre correspondant dans .</span><span class="sxs-lookup"><span data-stu-id="d0f26-139">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="d0f26-140">Pour chaque paramètre de `ref`valeur `out`(un paramètre sans, ou `in` modificateur), une conversion d’identité, une conversion implicite de référence, ou la conversion implicite de pointeur existe du type de paramètre dans `F0` le type de paramètre correspondant dans `F1`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-140">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="d0f26-141">Pour `ref`chaque `out`, `in` , ou paramètre, le type de paramètre `F0` est le même que le type de paramètre correspondant dans `F1`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-141">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="d0f26-142">Si le type de retour `ref` `ref readonly`est par valeur (non ou ), une identité, `F1` une référence implicite, ou la conversion implicite pointeur existe du type de retour au type de retour de `F0`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-142">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="d0f26-143">Si le type de`ref` retour `ref readonly`est par `ref` référence (ou), le type de retour et les modificateurs de `F1` sont les mêmes que le type de retour et `ref` les modificateurs de `F0`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-143">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="d0f26-144">La convention `F0` d’appel de est `F1`la même que la convention d’appel de .</span><span class="sxs-lookup"><span data-stu-id="d0f26-144">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="d0f26-145">Autoriser l’adresse des méthodes cibles</span><span class="sxs-lookup"><span data-stu-id="d0f26-145">Allow address-of to target methods</span></span>

<span data-ttu-id="d0f26-146">Les groupes de méthode seront désormais autorisés comme arguments à une adresse d’expression.</span><span class="sxs-lookup"><span data-stu-id="d0f26-146">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="d0f26-147">Le type d’une telle `delegate*` expression sera un qui a la signature équivalente de la méthode cible et une convention d’appel gérée:</span><span class="sxs-lookup"><span data-stu-id="d0f26-147">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

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

<span data-ttu-id="d0f26-148">Dans un contexte dangereux, une méthode `M` est `F` compatible avec un type de pointeur de fonction si tous les éléments suivants sont vrais :</span><span class="sxs-lookup"><span data-stu-id="d0f26-148">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="d0f26-149">`M`et `F` ont le même nombre de paramètres, et `ref` `out`chaque `in` paramètre a `D` le `F`même , , ou modificateurs que le paramètre correspondant dans .</span><span class="sxs-lookup"><span data-stu-id="d0f26-149">`M` and `F` have the same number of parameters, and each parameter in `D` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="d0f26-150">Pour chaque paramètre de `ref`valeur `out`(un paramètre sans, ou `in` modificateur), une conversion d’identité, une conversion implicite de référence, ou la conversion implicite de pointeur existe du type de paramètre dans `M` le type de paramètre correspondant dans `F`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-150">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="d0f26-151">Pour `ref`chaque `out`, `in` , ou paramètre, le type de paramètre `M` est le même que le type de paramètre correspondant dans `F`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-151">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="d0f26-152">Si le type de retour `ref` `ref readonly`est par valeur (non ou ), une identité, `F` une référence implicite, ou la conversion implicite pointeur existe du type de retour au type de retour de `M`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-152">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="d0f26-153">Si le type de`ref` retour `ref readonly`est par `ref` référence (ou), le type de retour et les modificateurs de `F` sont les mêmes que le type de retour et `ref` les modificateurs de `M`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-153">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="d0f26-154">La convention `M` d’appel de est `F`la même que la convention d’appel de .</span><span class="sxs-lookup"><span data-stu-id="d0f26-154">The calling convention of `M` is the same as the calling convention of `F`.</span></span>
- <span data-ttu-id="d0f26-155">`M` est une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="d0f26-155">`M` is a static method.</span></span>

<span data-ttu-id="d0f26-156">Dans un contexte dangereux, une conversion implicite existe d’une adresse `E` d’expression dont `F` `E` la cible est un groupe de méthode à un type compatible de pointeur `F`de fonction si elle contient au moins une méthode qui s’applique dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de , tel que décrit dans ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d0f26-156">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="d0f26-157">Une seule `M` méthode est sélectionnée correspondant à une `E(A)` invocation de la forme par méthode avec les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="d0f26-157">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="d0f26-158">La liste `A` des arguments est une liste d’expressions, chacune`ref` `out`classée `in`comme variable et avec le type et le modificateur ( , , ou ) de la liste de _\_\_paramètres formels_ correspondantes de `D`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-158">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _formal\_parameter\_list_ of `D`.</span></span>
   - <span data-ttu-id="d0f26-159">Les méthodes candidates ne sont que les méthodes qui s’appliquent dans leur forme normale, et non celles applicables sous leur forme élargie.</span><span class="sxs-lookup"><span data-stu-id="d0f26-159">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="d0f26-160">Les méthodes candidates ne sont que les méthodes statiques.</span><span class="sxs-lookup"><span data-stu-id="d0f26-160">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="d0f26-161">Si l’algorithme des invocations de méthode produit une erreur, alors une erreur de compilation-temps se produit.</span><span class="sxs-lookup"><span data-stu-id="d0f26-161">If the algorithm of Method invocations produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="d0f26-162">Dans le cas contraire, `M` l’algorithme produit une `F` meilleure méthode ayant le même nombre de paramètres que la conversion est considérée comme existant.</span><span class="sxs-lookup"><span data-stu-id="d0f26-162">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="d0f26-163">La méthode `M` sélectionnée doit être compatible (comme défini `F`ci-dessus) avec le type de pointeur de fonction .</span><span class="sxs-lookup"><span data-stu-id="d0f26-163">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="d0f26-164">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="d0f26-164">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="d0f26-165">Le résultat de la conversion est `F`un pointeur de fonction de type .</span><span class="sxs-lookup"><span data-stu-id="d0f26-165">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="d0f26-166">Une conversion implicite existe à partir d’une adresse `E` `void*` d’expression dont la `M` `E`cible est un groupe de méthode à s’il n’y a qu’une seule méthode statique dans .</span><span class="sxs-lookup"><span data-stu-id="d0f26-166">An implicit conversion exists from an address-of expression whose target is a method group `E` to `void*` if there is only one static method `M` in `E`.</span></span>
<span data-ttu-id="d0f26-167">S’il ya une méthode statique, `E` alors `M`la meilleure méthode unique de est .</span><span class="sxs-lookup"><span data-stu-id="d0f26-167">If there is one static method, then the single best method from `E` is `M`.</span></span>
<span data-ttu-id="d0f26-168">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="d0f26-168">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="d0f26-169">Cela signifie que les développeurs peuvent compter sur les règles de résolution de surcharge pour fonctionner en collaboration avec l’adresse de l’opérateur :</span><span class="sxs-lookup"><span data-stu-id="d0f26-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

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

<span data-ttu-id="d0f26-170">L’adresse de l’opérateur `ldftn` sera mise en œuvre à l’aide de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="d0f26-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="d0f26-171">Restrictions de cette fonctionnalité:</span><span class="sxs-lookup"><span data-stu-id="d0f26-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="d0f26-172">Ne s’applique `static`qu’aux méthodes marquées comme .</span><span class="sxs-lookup"><span data-stu-id="d0f26-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="d0f26-173">Les`static` fonctions non locales `&`ne peuvent pas être utilisées dans .</span><span class="sxs-lookup"><span data-stu-id="d0f26-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="d0f26-174">Les détails de mise en œuvre de ces méthodes ne sont délibérément pas spécifiés par la langue.</span><span class="sxs-lookup"><span data-stu-id="d0f26-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="d0f26-175">Cela comprend s’ils sont statiques par rapport à l’instance ou exactement avec quelle signature ils sont émis.</span><span class="sxs-lookup"><span data-stu-id="d0f26-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="d0f26-176">Opérateurs sur les types de pointeurs de fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="d0f26-177">La section en code dangereux sur les opérateurs est modifiée en tant que telle :</span><span class="sxs-lookup"><span data-stu-id="d0f26-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="d0f26-178">Dans un contexte dangereux, plusieurs constructions sont disponibles\_pour fonctionner sur tous les\__pointer type_s qui ne sont pas _funcptr type_s :</span><span class="sxs-lookup"><span data-stu-id="d0f26-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="d0f26-179">L’opérateur `*` peut être utilisé pour effectuer l’indirection pointeur ([indirection pointeur](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="d0f26-180">L’opérateur `->` peut être utilisé pour accéder à un membre d’une struction par l’intermédiaire d’un pointeur[(accès au membre Pointeur](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="d0f26-181">L’opérateur `[]` peut être utilisé pour indexer un pointeur[(accès à l’élément Pointeur](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-181">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="d0f26-182">L’opérateur `&` peut être utilisé pour obtenir l’adresse d’une variable[(L’adresse de l’opérateur](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="d0f26-183">Les `++` `--` opérateurs et les opérateurs peuvent être utilisés pour incrémenter et décroisser les pointeurs[(augmentation et décroissement des pointeurs).](unsafe-code.md#pointer-increment-and-decrement)</span><span class="sxs-lookup"><span data-stu-id="d0f26-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="d0f26-184">Les `+` `-` opérateurs et les opérateurs peuvent être utilisés pour effectuer l’arithmétique pointeur ([arithmétique pointeur](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="d0f26-185">Le `==` `!=`, `<` `>`, `<=`, `=>` , et les opérateurs peuvent être utilisés pour comparer les pointeurs ([comparaison Pointer](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="d0f26-186">L’opérateur `stackalloc` peut être utilisé pour allouer la mémoire de la pile d’appels[(tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="d0f26-187">L’instruction `fixed` peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([L’instruction fixe](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="d0f26-188">Dans un contexte dangereux, plusieurs constructions sont disponibles\_pour fonctionner sur tous les _funcptr type_s :</span><span class="sxs-lookup"><span data-stu-id="d0f26-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="d0f26-189">L’opérateur `&` peut être utilisé pour obtenir l’adresse des méthodes statiques[(Autoriser l’adresse des méthodes cibles](function-pointers.md#allow-address-of-to-target-methods))</span><span class="sxs-lookup"><span data-stu-id="d0f26-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](function-pointers.md#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="d0f26-190">Le `==` `!=`, `<` `>`, `<=`, `=>` , et les opérateurs peuvent être utilisés pour comparer les pointeurs ([comparaison Pointer](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="d0f26-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="d0f26-191">En outre, nous modifions toutes les sections pour `Pointers in expressions` interdire les types de pointeurs de fonction, sauf `Pointer comparison` et `The sizeof operator`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="d0f26-192">Meilleur membre de la fonction</span><span class="sxs-lookup"><span data-stu-id="d0f26-192">Better function member</span></span>

<span data-ttu-id="d0f26-193">La meilleure spécification des membres de fonction sera modifiée pour inclure la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="d0f26-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="d0f26-194">A `delegate*` est plus spécifique que`void*`</span><span class="sxs-lookup"><span data-stu-id="d0f26-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="d0f26-195">Cela signifie qu’il est `void*` possible `delegate*` de surcharger et d’utiliser l’adresse de l’opérateur et d’utiliser toujours judicieusement.</span><span class="sxs-lookup"><span data-stu-id="d0f26-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a><span data-ttu-id="d0f26-196">Représentation des métadonnées des `in`types de , `out`et `ref readonly` de paramètres et de retour</span><span class="sxs-lookup"><span data-stu-id="d0f26-196">Metadata representation of `in`, `out`, and `ref readonly` parameters and return types</span></span>

<span data-ttu-id="d0f26-197">Les signatures de pointeur de fonction n’ont pas d’emplacement `in` `out`de `ref readonly` drapeaux de paramètres, ainsi nous devons coder si les paramètres et le type de retour sont, , ou en utilisant des modreqs.</span><span class="sxs-lookup"><span data-stu-id="d0f26-197">Function pointer signatures have no parameter flags location, so we must encode whether parameters and the return type are `in`, `out`, or `ref readonly` by using modreqs.</span></span>

### `in`

<span data-ttu-id="d0f26-198">Nous réutiliser `System.Runtime.InteropServices.InAttribute` `modreq` , appliqué comme un spécificateur réf sur un paramètre ou un type de retour, pour signifier ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="d0f26-198">We reuse `System.Runtime.InteropServices.InAttribute`, applied as a `modreq` to the ref specifier on a parameter or return type, to mean the following:</span></span>
* <span data-ttu-id="d0f26-199">S’il est appliqué sur un spécificateur réciscivrateur de paramètres, ce paramètre est traité comme `in`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-199">If applied to a parameter ref specifier, this parameter is treated as `in`.</span></span>
* <span data-ttu-id="d0f26-200">S’il est appliqué sur le spécificateur `ref readonly`de réf de retour, le type de retour est traité comme .</span><span class="sxs-lookup"><span data-stu-id="d0f26-200">If applied to the return type ref specifier, the return type is treated as `ref readonly`.</span></span>

### `out`

<span data-ttu-id="d0f26-201">Nous `System.Runtime.InteropServices.OutAttribute`utilisons , `modreq` appliqué comme un spécificateur de réf sur `out` un type de paramètre, pour signifier que le paramètre est un paramètre.</span><span class="sxs-lookup"><span data-stu-id="d0f26-201">We use `System.Runtime.InteropServices.OutAttribute`, applied as a `modreq` to the ref specifier on a parameter type, to mean that the parameter is an `out` parameter.</span></span>

### <a name="errors"></a><span data-ttu-id="d0f26-202">Erreurs</span><span class="sxs-lookup"><span data-stu-id="d0f26-202">Errors</span></span>

* <span data-ttu-id="d0f26-203">C’est une `OutAttribute` erreur d’appliquer comme modreq à un type de retour.</span><span class="sxs-lookup"><span data-stu-id="d0f26-203">It is an error to apply `OutAttribute` as a modreq to a return type.</span></span>
* <span data-ttu-id="d0f26-204">C’est une erreur `InAttribute` `OutAttribute` d’appliquer à la fois et comme modreq à un type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="d0f26-204">It is an error to apply both `InAttribute` and `OutAttribute` as a modreq to a parameter type.</span></span>
* <span data-ttu-id="d0f26-205">Si l’un ou l’autre sont spécifiés par modopt, ils sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="d0f26-205">If either are specified via modopt, they are ignored.</span></span>

## <a name="open-issues"></a><span data-ttu-id="d0f26-206">Open Issues</span><span class="sxs-lookup"><span data-stu-id="d0f26-206">Open Issues</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="d0f26-207">NativeCallableAttribute NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="d0f26-207">NativeCallableAttribute</span></span>

<span data-ttu-id="d0f26-208">Il s’agit d’un attribut utilisé par le CLR pour éviter le prologue réussi à native lors de l’invocation.</span><span class="sxs-lookup"><span data-stu-id="d0f26-208">This is an attribute used by the CLR to avoid the managed to native prologue when invoking.</span></span> <span data-ttu-id="d0f26-209">Les méthodes marquées par cet attribut ne sont callables qu’à partir du code natif, non gérées (ne peut pas appeler des méthodes, créer un délégué, etc...).</span><span class="sxs-lookup"><span data-stu-id="d0f26-209">Methods marked by this attribute are only callable from native code, not managed (can’t call methods, create a delegate, etc …).</span></span> <span data-ttu-id="d0f26-210">L’attribut n’est pas spécial pour mscorlib; l’exécution traitera n’importe quel attribut avec ce nom avec la même sémantique.</span><span class="sxs-lookup"><span data-stu-id="d0f26-210">The attribute is not special to mscorlib; the runtime will treat any attribute with this name with the same semantics.</span></span>

<span data-ttu-id="d0f26-211">Il est possible que le temps d’exécution et la langue travaillent ensemble pour soutenir pleinement cela.</span><span class="sxs-lookup"><span data-stu-id="d0f26-211">It's possible for the runtime and language to work together to fully support this.</span></span> <span data-ttu-id="d0f26-212">La langue pourrait choisir de `static` traiter `NativeCallable` l’adresse `delegate*` des membres avec un attribut comme un avec la convention d’appel spécifiée.</span><span class="sxs-lookup"><span data-stu-id="d0f26-212">The language could choose to treat address-of `static` members with a `NativeCallable` attribute as a `delegate*` with the specified calling convention.</span></span>

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

<span data-ttu-id="d0f26-213">En outre, la langue voudrait probablement aussi:</span><span class="sxs-lookup"><span data-stu-id="d0f26-213">Additionally the language would likely also want to:</span></span>

- <span data-ttu-id="d0f26-214">Signalez tous les appels gérés `NativeCallable` vers une méthode étiquetée comme une erreur.</span><span class="sxs-lookup"><span data-stu-id="d0f26-214">Flag any managed calls to a method tagged with `NativeCallable` as an error.</span></span> <span data-ttu-id="d0f26-215">Étant donné que la fonction ne peut pas être invoquée à partir de code géré, le compilateur devrait empêcher les développeurs de tenter une telle invocation.</span><span class="sxs-lookup"><span data-stu-id="d0f26-215">Given the function can't be invoked from managed code the compiler should prevent developers from attempting such an invocation.</span></span>
- <span data-ttu-id="d0f26-216">Empêcher les conversions `delegate` de groupe de `NativeCallable`méthode à quand la méthode est étiquetée avec .</span><span class="sxs-lookup"><span data-stu-id="d0f26-216">Prevent method group conversions to `delegate` when the method is tagged with `NativeCallable`.</span></span>

<span data-ttu-id="d0f26-217">Ce n’est `NativeCallable` pas nécessaire pour soutenir cependant.</span><span class="sxs-lookup"><span data-stu-id="d0f26-217">This is not necessary to support `NativeCallable` though.</span></span> <span data-ttu-id="d0f26-218">Le compilateur peut `NativeCallable` prendre en charge l’attribut à l’aide de la syntaxe existante.</span><span class="sxs-lookup"><span data-stu-id="d0f26-218">The compiler can support the `NativeCallable` attribute as is using the existing syntax.</span></span> <span data-ttu-id="d0f26-219">Le programme aurait simplement `void*` besoin de jeter `delegate*` à avant de lancer à la signature correcte.</span><span class="sxs-lookup"><span data-stu-id="d0f26-219">The program would simply need to cast to `void*` before casting to the correct `delegate*` signature.</span></span> <span data-ttu-id="d0f26-220">Ce ne serait pas pire que le soutien aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="d0f26-220">That would be no worse than the support today.</span></span>

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a><span data-ttu-id="d0f26-221">Ensemble extensible de conventions d’appel non gestion</span><span class="sxs-lookup"><span data-stu-id="d0f26-221">Extensible set of unmanaged calling conventions</span></span>

<span data-ttu-id="d0f26-222">L’ensemble des conventions d’appel non gestion soutenues par les codages actuels de l’ECMA-335 est dépassé.</span><span class="sxs-lookup"><span data-stu-id="d0f26-222">The set of unmanaged calling conventions supported by the current ECMA-335 encodings is outdated.</span></span> <span data-ttu-id="d0f26-223">Nous avons vu des demandes d’ajout d’un soutien pour des conventions d’appel plus non gestion, par exemple:</span><span class="sxs-lookup"><span data-stu-id="d0f26-223">We have seen requests to add support for more unmanaged calling conventions, for example:</span></span>

- <span data-ttu-id="d0f26-224">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall)https://github.com/dotnet/coreclr/issues/12120</span><span class="sxs-lookup"><span data-stu-id="d0f26-224">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120</span></span>
- <span data-ttu-id="d0f26-225">StdCall avec explicite cettehttps://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span><span class="sxs-lookup"><span data-stu-id="d0f26-225">StdCall with explicit this https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span></span>

<span data-ttu-id="d0f26-226">La conception de cette fonctionnalité devrait permettre d’étendre l’ensemble des conventions d’appel non gestion au besoin à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="d0f26-226">The design of this feature should allow extending the set of unmanaged calling conventions as needed in future.</span></span> <span data-ttu-id="d0f26-227">Les problèmes comprennent un espace limité pour l’encodage des `IMAGE_CEE_CS_CALLCONV_MASK`conventions d’appel (12 sur 16 valeurs sont prises en) et le nombre d’endroits qui doivent être touchés afin d’ajouter une nouvelle convention d’appel.</span><span class="sxs-lookup"><span data-stu-id="d0f26-227">The problems include limited space for encoding calling conventions (12 out of 16 values are taken in `IMAGE_CEE_CS_CALLCONV_MASK`) and number of places that need to be touched in order to add a new calling convention.</span></span> <span data-ttu-id="d0f26-228">Une solution potentielle consiste à introduire un nouveau [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) codage qui représente la convention d’appel à l’aide d’enum.</span><span class="sxs-lookup"><span data-stu-id="d0f26-228">A potential solution is to introduce a new encoding that represents the calling convention using [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.</span></span>

<span data-ttu-id="d0f26-229">Pour référence, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h a la liste des conventions d’appel soutenue par LLVM.</span><span class="sxs-lookup"><span data-stu-id="d0f26-229">For reference, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h has the list of calling conventions supported by LLVM.</span></span> <span data-ttu-id="d0f26-230">Bien qu’il soit peu probable que .NET n’aura jamais besoin de les soutenir tous, il démontre que l’espace d’appeler les conventions est très riche.</span><span class="sxs-lookup"><span data-stu-id="d0f26-230">While it is unlikely that .NET will ever need to support all of them, it demonstrates that the space of calling conventions is very rich.</span></span>

## <a name="considerations"></a><span data-ttu-id="d0f26-231">Considérations</span><span class="sxs-lookup"><span data-stu-id="d0f26-231">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="d0f26-232">Autoriser les méthodes d’instance</span><span class="sxs-lookup"><span data-stu-id="d0f26-232">Allow instance methods</span></span>

<span data-ttu-id="d0f26-233">La proposition pourrait être étendue pour soutenir `EXPLICITTHIS` les méthodes d’instance en tirant parti de la convention d’appel de l’ICM (nommée `instance` dans le code C).</span><span class="sxs-lookup"><span data-stu-id="d0f26-233">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="d0f26-234">Cette forme de pointeurs de `this` fonction CLI met le paramètre comme un premier paramètre explicite de la syntaxe pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="d0f26-234">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="d0f26-235">C’est sain, mais ajoute une certaine complication à la proposition.</span><span class="sxs-lookup"><span data-stu-id="d0f26-235">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="d0f26-236">En particulier parce que les pointeurs `instance` `managed` de fonction qui différaient par la convention d’appel et seraient incompatibles même si les deux cas sont utilisés pour invoquer des méthodes gérées avec la même signature C.</span><span class="sxs-lookup"><span data-stu-id="d0f26-236">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="d0f26-237">Aussi dans tous les cas considérés où cela serait précieux `static` pour avoir il y avait un travail simple autour: utiliser une fonction locale.</span><span class="sxs-lookup"><span data-stu-id="d0f26-237">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="d0f26-238">Ne pas exiger de sécurité à la déclaration</span><span class="sxs-lookup"><span data-stu-id="d0f26-238">Don't require unsafe at declaration</span></span>

<span data-ttu-id="d0f26-239">Au lieu `unsafe` d’exiger `delegate*`à chaque utilisation d’un , seulement l’exiger au point où un groupe de méthode est converti en un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="d0f26-239">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="d0f26-240">C’est là que les principaux problèmes de sécurité entrent en jeu (sachant que l’assemblage contenant ne peut pas être déchargé pendant que la valeur est vivante).</span><span class="sxs-lookup"><span data-stu-id="d0f26-240">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="d0f26-241">Exiger `unsafe` sur les autres endroits peut être considéré comme excessif.</span><span class="sxs-lookup"><span data-stu-id="d0f26-241">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="d0f26-242">C’est ainsi que la conception était initialement prévue.</span><span class="sxs-lookup"><span data-stu-id="d0f26-242">This is how the design was originally intended.</span></span> <span data-ttu-id="d0f26-243">Mais les règles linguistiques qui en résultaient se sentaient très maladroites.</span><span class="sxs-lookup"><span data-stu-id="d0f26-243">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="d0f26-244">Il est impossible de cacher le fait qu’il s’agit d’une valeur pointeur et il a continué à jeter un coup d’oeil à travers même sans le `unsafe` mot clé.</span><span class="sxs-lookup"><span data-stu-id="d0f26-244">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="d0f26-245">Par exemple, `object` la conversion à ne peut pas être autorisé, il ne peut pas être un membre d’un `class`, etc ... La conception C est `unsafe` d’exiger pour toutes les utilisations de pointeur et donc cette conception suit cela.</span><span class="sxs-lookup"><span data-stu-id="d0f26-245">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="d0f26-246">Les développeurs seront toujours _safe_ capables de présenter `delegate*` un emballage sûr sur le dessus des valeurs de la même manière qu’ils le font pour les types de pointeurs normaux aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="d0f26-246">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="d0f26-247">Vous devez :</span><span class="sxs-lookup"><span data-stu-id="d0f26-247">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="d0f26-248">Utilisation des délégués</span><span class="sxs-lookup"><span data-stu-id="d0f26-248">Using delegates</span></span>

<span data-ttu-id="d0f26-249">Au lieu d’utiliser un `delegate*`nouvel élément `delegate` syntaxe, il suffit d’utiliser les types existants avec un `*` suivant le type:</span><span class="sxs-lookup"><span data-stu-id="d0f26-249">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="d0f26-250">La gestion de la convention d’appel peut se faire en annotant les `delegate` types avec un attribut qui spécifie une `CallingConvention` valeur.</span><span class="sxs-lookup"><span data-stu-id="d0f26-250">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="d0f26-251">L’absence d’attribut signifierait la convention d’appel gérée.</span><span class="sxs-lookup"><span data-stu-id="d0f26-251">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="d0f26-252">L’encodage de ce dans IL est problématique.</span><span class="sxs-lookup"><span data-stu-id="d0f26-252">Encoding this in IL is problematic.</span></span> <span data-ttu-id="d0f26-253">La valeur sous-jacente doit être représentée comme un pointeur mais elle doit aussi :</span><span class="sxs-lookup"><span data-stu-id="d0f26-253">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="d0f26-254">Avoir un type unique pour permettre des surcharges avec différents types de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="d0f26-254">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="d0f26-255">Être équivalent aux fins de l’OHI au-delà des limites de l’assemblage.</span><span class="sxs-lookup"><span data-stu-id="d0f26-255">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="d0f26-256">Le dernier point est particulièrement problématique.</span><span class="sxs-lookup"><span data-stu-id="d0f26-256">The last point is particularly problematic.</span></span> <span data-ttu-id="d0f26-257">Cela signifie que chaque `Func<int>*` assemblage qui utilise doit coder `Func<int>*` un type équivalent dans les métadonnées, même si elle est définie dans un assemblage mais ne contrôle pas.</span><span class="sxs-lookup"><span data-stu-id="d0f26-257">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="d0f26-258">En outre, tout autre type `System.Func<T>` qui est défini avec le nom dans un assemblage qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.</span><span class="sxs-lookup"><span data-stu-id="d0f26-258">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="d0f26-259">Une option qui a été explorée `mod_req(Func<int>) void*`était d’émettre un tel pointeur que .</span><span class="sxs-lookup"><span data-stu-id="d0f26-259">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="d0f26-260">Cela ne fonctionne pas `mod_req` bien que `TypeSpec` comme un ne peut pas se lier à un et ne peut donc pas cibler les instantanéisations génériques.</span><span class="sxs-lookup"><span data-stu-id="d0f26-260">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="d0f26-261">Indicateurs de fonction nommés</span><span class="sxs-lookup"><span data-stu-id="d0f26-261">Named function pointers</span></span>

<span data-ttu-id="d0f26-262">La syntaxe de pointeur de fonction peut être lourde, particulièrement dans les cas complexes comme les pointeurs de fonction imbriqués.</span><span class="sxs-lookup"><span data-stu-id="d0f26-262">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="d0f26-263">Plutôt que d’avoir des développeurs tapent la signature chaque fois que la `delegate`langue pourrait permettre des déclarations nommées de pointeurs de fonction comme c’est fait avec .</span><span class="sxs-lookup"><span data-stu-id="d0f26-263">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="d0f26-264">Une partie du problème ici est le primitif CLI sous-jacent n’a pas de noms d’où ce serait purement une invention C et nécessitent un peu de travail de métadonnées pour permettre.</span><span class="sxs-lookup"><span data-stu-id="d0f26-264">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="d0f26-265">C’est faisable, mais c’est une question importante de travail.</span><span class="sxs-lookup"><span data-stu-id="d0f26-265">That is doable but is a significant about of work.</span></span> <span data-ttu-id="d0f26-266">Il exige essentiellement que CMD ait un compagnon à la table de def de type uniquement pour ces noms.</span><span class="sxs-lookup"><span data-stu-id="d0f26-266">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="d0f26-267">Aussi, lorsque les arguments pour les pointeurs de fonction nommés ont été examinés, nous avons constaté qu’ils pouvaient s’appliquer tout aussi bien à un certain nombre d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="d0f26-267">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="d0f26-268">Par exemple, il serait tout aussi commode de déclarer les tuples nommés pour réduire la nécessité de taper la signature complète dans tous les cas.</span><span class="sxs-lookup"><span data-stu-id="d0f26-268">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="d0f26-269">Après discussion, nous avons décidé `delegate*` de ne pas autoriser la déclaration nommée de types.</span><span class="sxs-lookup"><span data-stu-id="d0f26-269">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="d0f26-270">Si nous constatons qu’il ya un besoin important pour cela basé sur la rétroaction de l’utilisation du client, alors nous allons enquêter sur une solution de nommage qui fonctionne pour les pointeurs de fonction, tuples, génériques, etc ... Cela est susceptible d’être similaire dans `typedef` la forme à d’autres suggestions comme le plein soutien dans la langue.</span><span class="sxs-lookup"><span data-stu-id="d0f26-270">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="d0f26-271">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="d0f26-271">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="d0f26-272">fonctions locales statiques</span><span class="sxs-lookup"><span data-stu-id="d0f26-272">static local functions</span></span>

<span data-ttu-id="d0f26-273">Il s’agit de la `static` [proposition](https://github.com/dotnet/csharplang/issues/1565) d’autoriser le modificateur sur les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="d0f26-273">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="d0f26-274">Une telle fonction serait garantie d’être émise au fur et à mesure que `static` la signature exacte est spécifiée dans le code source.</span><span class="sxs-lookup"><span data-stu-id="d0f26-274">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="d0f26-275">Une telle fonction devrait être `&` un argument valable à car il ne contient aucun des problèmes des fonctions locales ont aujourd’hui</span><span class="sxs-lookup"><span data-stu-id="d0f26-275">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today</span></span>

### <a name="static-delegates"></a><span data-ttu-id="d0f26-276">délégués statiques</span><span class="sxs-lookup"><span data-stu-id="d0f26-276">static delegates</span></span>

<span data-ttu-id="d0f26-277">Il s’agit de [la proposition](https://github.com/dotnet/csharplang/issues/302) `delegate` visant à permettre `static` la déclaration des types qui ne peuvent se référer qu’aux membres.</span><span class="sxs-lookup"><span data-stu-id="d0f26-277">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="d0f26-278">L’avantage étant `delegate` que de tels cas peuvent être libres d’allocation et mieux dans les scénarios sensibles aux performances.</span><span class="sxs-lookup"><span data-stu-id="d0f26-278">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="d0f26-279">Si la fonction de `static delegate` pointeur de fonction est mise en œuvre, la proposition sera probablement fermée. L’avantage proposé de cette fonctionnalité est la nature libre d’allocation.</span><span class="sxs-lookup"><span data-stu-id="d0f26-279">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="d0f26-280">Toutefois, des enquêtes récentes ont révélé que ce n’est pas possible d’atteindre en raison du déchargement de l’assemblage.</span><span class="sxs-lookup"><span data-stu-id="d0f26-280">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="d0f26-281">Il doit y avoir `static delegate` une poignée forte de la méthode à laquelle il se réfère afin d’empêcher l’assemblage d’être déchargé hors de dessous elle.</span><span class="sxs-lookup"><span data-stu-id="d0f26-281">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="d0f26-282">Pour maintenir `static delegate` chaque instance serait nécessaire d’allouer une nouvelle poignée qui va à l’encontre des objectifs de la proposition.</span><span class="sxs-lookup"><span data-stu-id="d0f26-282">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="d0f26-283">Il y avait quelques conceptions où l’allocation pourrait être amortie à une seule allocation par site d’appel, mais qui était un peu complexe et ne semblait pas valoir le compromis.</span><span class="sxs-lookup"><span data-stu-id="d0f26-283">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="d0f26-284">Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants:</span><span class="sxs-lookup"><span data-stu-id="d0f26-284">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="d0f26-285">Sécurité face au déchargement de l’assemblage : `delegate` cela nécessite des allocations et est donc déjà une option suffisante.</span><span class="sxs-lookup"><span data-stu-id="d0f26-285">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="d0f26-286">Pas de sécurité face au déchargement `delegate*`de l’assemblage : utilisez un .</span><span class="sxs-lookup"><span data-stu-id="d0f26-286">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="d0f26-287">Cela peut être `struct` enveloppé dans un `unsafe` pour permettre l’utilisation en dehors d’un contexte dans le reste du code.</span><span class="sxs-lookup"><span data-stu-id="d0f26-287">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
