---
ms.openlocfilehash: 6bf0b29d16297ae5201d9a7773fc9cb95a47f9cb
ms.sourcegitcommit: 1bb454804d017a9ca18c5de47737dd1d68ce3eea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85835141"
---
# <a name="function-pointers"></a><span data-ttu-id="f0597-101">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="f0597-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="f0597-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="f0597-102">Summary</span></span>

<span data-ttu-id="f0597-103">Cette proposition fournit des constructions de langage qui exposent des opcodes IL qui ne sont pas actuellement accessibles efficacement, ou du tout, en C# : `ldftn` et `calli` .</span><span class="sxs-lookup"><span data-stu-id="f0597-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="f0597-104">Ces opcodes IL peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="f0597-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="f0597-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="f0597-105">Motivation</span></span>

<span data-ttu-id="f0597-106">Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) :</span><span class="sxs-lookup"><span data-stu-id="f0597-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="f0597-107">Il s’agit d’une proposition de conception alternative aux [intrinsèques du compilateur](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="f0597-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f0597-108">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="f0597-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="f0597-109">Pointeurs fonction</span><span class="sxs-lookup"><span data-stu-id="f0597-109">Function pointers</span></span>

<span data-ttu-id="f0597-110">Le langage autorisera la déclaration des pointeurs de fonction à l’aide de la `delegate*` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="f0597-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="f0597-111">La syntaxe complète est décrite en détail dans la section suivante, mais elle est destinée à ressembler à la syntaxe utilisée par `Func` et aux `Action` déclarations de type.</span><span class="sxs-lookup"><span data-stu-id="f0597-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="f0597-112">Ces types sont représentés à l’aide du type de pointeur de fonction, comme indiqué dans la norme ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="f0597-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="f0597-113">Cela signifie que l’appel d’un `delegate*` utilisera `calli` lorsque l’appel d’un `delegate` sera `callvirt` utilisé sur la `Invoke` méthode.</span><span class="sxs-lookup"><span data-stu-id="f0597-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="f0597-114">Toutefois, la syntaxe de l’appel est identique pour les deux constructions.</span><span class="sxs-lookup"><span data-stu-id="f0597-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="f0597-115">La définition ECMA-335 des pointeurs de méthode comprend la Convention d’appel dans le cadre de la signature de type (section 7,1).</span><span class="sxs-lookup"><span data-stu-id="f0597-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="f0597-116">La Convention d’appel par défaut sera `managed` .</span><span class="sxs-lookup"><span data-stu-id="f0597-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="f0597-117">Les conventions d’appel non managées peuvent être spécifiées en plaçant un `unmanaged` mot clé après la `delegate*` syntaxe, qui utilise la valeur par défaut de la plateforme Runtime.</span><span class="sxs-lookup"><span data-stu-id="f0597-117">Unmanaged calling conventions can by specified by putting an `unmanaged` keyword afer the `delegate*` syntax, which will use the runtime platform default.</span></span> <span data-ttu-id="f0597-118">Des conventions non managées spécifiques peuvent ensuite être spécifiées entre crochets au `unmanaged` mot clé en spécifiant tout type commençant par `CallConv` dans l' `System.Runtime.CompilerServices` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="f0597-118">Specific unmanaged conventions can then be specified in brackets to the `unmanaged` keyword by specifying any type starting with `CallConv` in the `System.Runtime.CompilerServices` namespace.</span></span> <span data-ttu-id="f0597-119">Ces types doivent provenir de la bibliothèque principale du programme, et l’ensemble des combinaisons valides dépend de la plateforme.</span><span class="sxs-lookup"><span data-stu-id="f0597-119">These types must come from the program's core library, and the set of valid combinations is platform-dependent.</span></span>

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
delegate* unmanaged[CallConvCdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
delegate* unmanaged[CallConvStdCall, CallConvSuppressGCTransition] <int, int>;
```

<span data-ttu-id="f0597-120">Les conversions entre `delegate*` les types sont effectuées en fonction de leur signature, y compris la Convention d’appel.</span><span class="sxs-lookup"><span data-stu-id="f0597-120">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

<span data-ttu-id="f0597-121">Un `delegate*` type est un type pointeur, ce qui signifie qu’il possède toutes les fonctionnalités et restrictions d’un type pointeur standard :</span><span class="sxs-lookup"><span data-stu-id="f0597-121">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="f0597-122">Valide uniquement dans un `unsafe` contexte.</span><span class="sxs-lookup"><span data-stu-id="f0597-122">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="f0597-123">Les méthodes qui contiennent un `delegate*` paramètre ou un type de retour ne peuvent être appelées qu’à partir d’un `unsafe` contexte.</span><span class="sxs-lookup"><span data-stu-id="f0597-123">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="f0597-124">Ne peut pas être converti en `object` .</span><span class="sxs-lookup"><span data-stu-id="f0597-124">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="f0597-125">Ne peut pas être utilisé comme argument générique.</span><span class="sxs-lookup"><span data-stu-id="f0597-125">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="f0597-126">Peut convertir implicitement `delegate*` en `void*` .</span><span class="sxs-lookup"><span data-stu-id="f0597-126">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="f0597-127">Peut effectuer une conversion explicite de `void*` en `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="f0597-127">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="f0597-128">Restrictions :</span><span class="sxs-lookup"><span data-stu-id="f0597-128">Restrictions:</span></span>

- <span data-ttu-id="f0597-129">Les attributs personnalisés ne peuvent pas être appliqués à un `delegate*` ou à l’un de ses éléments.</span><span class="sxs-lookup"><span data-stu-id="f0597-129">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="f0597-130">Un `delegate*` paramètre ne peut pas être marqué comme`params`</span><span class="sxs-lookup"><span data-stu-id="f0597-130">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="f0597-131">Un `delegate*` type présente toutes les restrictions d’un type pointeur normal.</span><span class="sxs-lookup"><span data-stu-id="f0597-131">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>
- <span data-ttu-id="f0597-132">Les opérations arithmétiques sur les pointeurs ne peuvent pas être effectuées directement sur les types de pointeur fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-132">Pointer arithmetic cannot be performed directly on function pointer types.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="f0597-133">Syntaxe du pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="f0597-133">Function pointer syntax</span></span>

<span data-ttu-id="f0597-134">La syntaxe complète du pointeur fonction est représentée par la grammaire suivante :</span><span class="sxs-lookup"><span data-stu-id="f0597-134">The full function pointer syntax is represented by the following grammar:</span></span>

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
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

<span data-ttu-id="f0597-135">Si aucun `calling_convention_specifier` n’est fourni, la valeur par défaut est `managed` .</span><span class="sxs-lookup"><span data-stu-id="f0597-135">If no `calling_convention_specifier` is provided, the default is `managed`.</span></span> <span data-ttu-id="f0597-136">L’encodage des métadonnées précis de `calling_convention_specifier` et de ce qui `identifier` sont valides dans le `unmanaged_calling_convention` est couvert dans la [représentation des métadonnées des conventions d’appel](#Metadata-Representation-of-Calling-Conventions).</span><span class="sxs-lookup"><span data-stu-id="f0597-136">The precise metadata encoding of the `calling_convention_specifier` and what `identifier`s are valid in the `unmanaged_calling_convention` is covered in [Metadata Representation of Calling Conventions](#Metadata-Representation-of-Calling-Conventions).</span></span>

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

### <a name="function-pointer-conversions"></a><span data-ttu-id="f0597-137">Conversions de pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="f0597-137">Function pointer conversions</span></span>

<span data-ttu-id="f0597-138">Dans un contexte non sécurisé, l’ensemble des conversions implicites disponibles (conversions implicites) est étendu pour inclure les conversions de pointeur implicites suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0597-138">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="f0597-139">_Conversions existantes_</span><span class="sxs-lookup"><span data-stu-id="f0597-139">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="f0597-140">De _ \_ type funcptr_ `F0` à un _autre \_ type funcptr_ `F1` , à condition que les conditions suivantes soient remplies :</span><span class="sxs-lookup"><span data-stu-id="f0597-140">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="f0597-141">`F0`et `F1` ont le même nombre de paramètres, et chaque paramètre `D0n` dans `F0` a les mêmes `ref` `out` modificateurs,, ou `in` que le paramètre correspondant `D1n` dans `F1` .</span><span class="sxs-lookup"><span data-stu-id="f0597-141">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="f0597-142">Pour chaque paramètre de valeur (paramètre sans `ref` `out` modificateur, ou `in` ), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `F0` le type de paramètre correspondant dans `F1` .</span><span class="sxs-lookup"><span data-stu-id="f0597-142">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="f0597-143">Pour chaque `ref` `out` paramètre, ou `in` , le type de paramètre dans `F0` est le même que le type de paramètre correspondant dans `F1` .</span><span class="sxs-lookup"><span data-stu-id="f0597-143">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="f0597-144">Si le type de retour est par valeur (no `ref` ou `ref readonly` ), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F1` au type de retour de `F0` .</span><span class="sxs-lookup"><span data-stu-id="f0597-144">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="f0597-145">Si le type de retour est par référence ( `ref` ou `ref readonly` ), le type de retour et les `ref` modificateurs de `F1` sont les mêmes que le type de retour et les `ref` modificateurs de `F0` .</span><span class="sxs-lookup"><span data-stu-id="f0597-145">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="f0597-146">La Convention d’appel de `F0` est identique à la Convention d’appel de `F1` .</span><span class="sxs-lookup"><span data-stu-id="f0597-146">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="f0597-147">Autoriser l’adresse à cibler des méthodes</span><span class="sxs-lookup"><span data-stu-id="f0597-147">Allow address-of to target methods</span></span>

<span data-ttu-id="f0597-148">Les groupes de méthodes sont désormais autorisés comme arguments d’une expression d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f0597-148">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="f0597-149">Le type d’une telle expression sera un `delegate*` qui a la signature équivalente de la méthode cible et une convention d’appel managée :</span><span class="sxs-lookup"><span data-stu-id="f0597-149">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

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

<span data-ttu-id="f0597-150">Dans un contexte non sécurisé, une méthode `M` est compatible avec un type de pointeur de fonction `F` si toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="f0597-150">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="f0597-151">`M`et `F` ont le même nombre de paramètres, et chaque paramètre dans `M` a les mêmes `ref` `out` modificateurs,, ou `in` que le paramètre correspondant dans `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-151">`M` and `F` have the same number of parameters, and each parameter in `M` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="f0597-152">Pour chaque paramètre de valeur (paramètre sans `ref` `out` modificateur, ou `in` ), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `M` le type de paramètre correspondant dans `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-152">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="f0597-153">Pour chaque `ref` `out` paramètre, ou `in` , le type de paramètre dans `M` est le même que le type de paramètre correspondant dans `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-153">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="f0597-154">Si le type de retour est par valeur (no `ref` ou `ref readonly` ), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F` au type de retour de `M` .</span><span class="sxs-lookup"><span data-stu-id="f0597-154">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="f0597-155">Si le type de retour est par référence ( `ref` ou `ref readonly` ), le type de retour et les `ref` modificateurs de `F` sont les mêmes que le type de retour et les `ref` modificateurs de `M` .</span><span class="sxs-lookup"><span data-stu-id="f0597-155">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="f0597-156">La Convention d’appel de `M` est identique à la Convention d’appel de `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-156">The calling convention of `M` is the same as the calling convention of `F`.</span></span> <span data-ttu-id="f0597-157">Cela comprend le bit de convention d’appel, ainsi que tous les indicateurs de convention d’appel spécifiés dans l’identificateur non managé.</span><span class="sxs-lookup"><span data-stu-id="f0597-157">This includes both the calling convention bit, as well as any calling convention flags specified in the unmanaged identifier.</span></span>
- <span data-ttu-id="f0597-158">`M` est une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="f0597-158">`M` is a static method.</span></span>

<span data-ttu-id="f0597-159">Dans un contexte non sécurisé, une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe `E` de méthodes vers un type de pointeur fonction compatible `F` si `E` contient au moins une méthode applicable dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de `F` , comme décrit dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="f0597-159">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="f0597-160">Une méthode unique `M` est sélectionnée correspondant à un appel de méthode de la forme `E(A)` avec les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0597-160">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="f0597-161">La liste `A` d’arguments est une liste d’expressions, chacune classée comme une variable et avec le type et le modificateur ( `ref` , `out` ou `in` ) de _la \_ \_ liste de paramètres funcptr_ correspondante de `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-161">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _funcptr\_parameter\_list_ of `F`.</span></span>
   - <span data-ttu-id="f0597-162">Les méthodes candidates sont uniquement les méthodes applicables dans leur forme normale, et non celles applicables dans leur forme développée.</span><span class="sxs-lookup"><span data-stu-id="f0597-162">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="f0597-163">Les méthodes candidates sont uniquement les méthodes statiques.</span><span class="sxs-lookup"><span data-stu-id="f0597-163">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="f0597-164">Si l’algorithme de la résolution de surcharge génère une erreur, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f0597-164">If the algorithm of overload resolution produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="f0597-165">Dans le cas contraire, l’algorithme produit une seule meilleure méthode qui `M` a le même nombre de paramètres que `F` et la conversion est considérée comme existante.</span><span class="sxs-lookup"><span data-stu-id="f0597-165">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="f0597-166">La méthode sélectionnée `M` doit être compatible (comme défini ci-dessus) avec le type de pointeur de fonction `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-166">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="f0597-167">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="f0597-167">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="f0597-168">Le résultat de la conversion est un pointeur de fonction de type `F` .</span><span class="sxs-lookup"><span data-stu-id="f0597-168">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="f0597-169">Cela signifie que les développeurs peuvent dépendre de règles de résolution de surcharge pour fonctionner conjointement avec l’opérateur d’adresse :</span><span class="sxs-lookup"><span data-stu-id="f0597-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

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

<span data-ttu-id="f0597-170">L’opérateur d’adresse sera implémenté à l’aide de l' `ldftn` instruction.</span><span class="sxs-lookup"><span data-stu-id="f0597-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="f0597-171">Restrictions de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="f0597-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="f0597-172">S’applique uniquement aux méthodes marquées comme `static` .</span><span class="sxs-lookup"><span data-stu-id="f0597-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="f0597-173">Les `static` fonctions non locales ne peuvent pas être utilisées dans `&` .</span><span class="sxs-lookup"><span data-stu-id="f0597-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="f0597-174">Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage.</span><span class="sxs-lookup"><span data-stu-id="f0597-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="f0597-175">Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.</span><span class="sxs-lookup"><span data-stu-id="f0597-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="f0597-176">Opérateurs sur les types de pointeur de fonction</span><span class="sxs-lookup"><span data-stu-id="f0597-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="f0597-177">La section du code unsafe sur les opérateurs est modifiée comme suit :</span><span class="sxs-lookup"><span data-stu-id="f0597-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="f0597-178">Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour fonctionner sur tous les _pointer \_ type_s qui ne sont pas _funcptr \_ type_s :</span><span class="sxs-lookup"><span data-stu-id="f0597-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="f0597-179">L' `*` opérateur peut être utilisé pour effectuer une indirection de pointeur ([indirection de pointeur](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="f0597-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="f0597-180">L' `->` opérateur peut être utilisé pour accéder à un membre d’un struct via un pointeur ([accès aux membres du pointeur](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="f0597-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="f0597-181">L' `[]` opérateur peut être utilisé pour indexer un pointeur ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="f0597-181">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="f0597-182">L' `&` opérateur peut être utilisé pour obtenir l’adresse d’une variable ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="f0597-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="f0597-183">Les `++` `--` opérateurs et peuvent être utilisés pour incrémenter et décrémenter des pointeurs ([incrément et décrémentation de pointeur](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="f0597-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="f0597-184">Les `+` `-` opérateurs et peuvent être utilisés pour effectuer des opérations arithmétiques sur les pointeurs ([opérations arithmétiques](unsafe-code.md#pointer-arithmetic)sur les pointeurs).</span><span class="sxs-lookup"><span data-stu-id="f0597-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="f0597-185">Les `==` `!=` opérateurs,, `<` ,, `>` `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="f0597-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="f0597-186">L' `stackalloc` opérateur peut être utilisé pour allouer de la mémoire à partir de la pile des appels ([mémoires tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="f0597-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="f0597-187">L' `fixed` instruction peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([l’instruction fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="f0597-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="f0597-188">Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour fonctionner sur tous les _funcptr \_ type_s :</span><span class="sxs-lookup"><span data-stu-id="f0597-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="f0597-189">L' `&` opérateur peut être utilisé pour obtenir l’adresse des méthodes statiques ([autoriser l’adresse-de aux méthodes cibles](function-pointers.md#allow-address-of-to-target-methods)).</span><span class="sxs-lookup"><span data-stu-id="f0597-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](function-pointers.md#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="f0597-190">Les `==` `!=` opérateurs,, `<` ,, `>` `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="f0597-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="f0597-191">En outre, nous modifions toutes les sections dans `Pointers in expressions` pour interdire les types de pointeur de fonction, à l’exception `Pointer comparison` de et `The sizeof operator` .</span><span class="sxs-lookup"><span data-stu-id="f0597-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="f0597-192">Meilleure fonction membre</span><span class="sxs-lookup"><span data-stu-id="f0597-192">Better function member</span></span>

<span data-ttu-id="f0597-193">La meilleure spécification de membre de fonction sera modifiée pour inclure la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="f0597-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="f0597-194">Un `delegate*` est plus spécifique que`void*`</span><span class="sxs-lookup"><span data-stu-id="f0597-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="f0597-195">Cela signifie qu’il est possible de surcharger sur `void*` et, `delegate*` et d’utiliser judicieusement l’opérateur d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f0597-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a><span data-ttu-id="f0597-196">Représentation de métadonnées des `in` `out` paramètres,, et `ref readonly` et des types de retour</span><span class="sxs-lookup"><span data-stu-id="f0597-196">Metadata representation of `in`, `out`, and `ref readonly` parameters and return types</span></span>

<span data-ttu-id="f0597-197">Les signatures de pointeur de fonction n’ont pas d’emplacement d’indicateurs de paramètres. nous devons donc Encoder si les paramètres et le type de retour sont `in` , `out` ou à `ref readonly` l’aide de modreqs.</span><span class="sxs-lookup"><span data-stu-id="f0597-197">Function pointer signatures have no parameter flags location, so we must encode whether parameters and the return type are `in`, `out`, or `ref readonly` by using modreqs.</span></span>

### `in`

<span data-ttu-id="f0597-198">Nous utilisons `System.Runtime.InteropServices.InAttribute` la réutilisation, appliquée en tant que `modreq` au spécificateur Ref sur un paramètre ou un type de retour, pour signifier ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f0597-198">We reuse `System.Runtime.InteropServices.InAttribute`, applied as a `modreq` to the ref specifier on a parameter or return type, to mean the following:</span></span>
* <span data-ttu-id="f0597-199">S’il est appliqué à un spécificateur de référence de paramètre, ce paramètre est traité comme `in` .</span><span class="sxs-lookup"><span data-stu-id="f0597-199">If applied to a parameter ref specifier, this parameter is treated as `in`.</span></span>
* <span data-ttu-id="f0597-200">S’il est appliqué au spécificateur Ref de type de retour, le type de retour est traité comme `ref readonly` .</span><span class="sxs-lookup"><span data-stu-id="f0597-200">If applied to the return type ref specifier, the return type is treated as `ref readonly`.</span></span>

### `out`

<span data-ttu-id="f0597-201">Nous utilisons `System.Runtime.InteropServices.OutAttribute` , appliqué en tant que `modreq` au spécificateur Ref sur un type de paramètre, pour signifier que le paramètre est un `out` paramètre.</span><span class="sxs-lookup"><span data-stu-id="f0597-201">We use `System.Runtime.InteropServices.OutAttribute`, applied as a `modreq` to the ref specifier on a parameter type, to mean that the parameter is an `out` parameter.</span></span>

### <a name="errors"></a><span data-ttu-id="f0597-202">Errors</span><span class="sxs-lookup"><span data-stu-id="f0597-202">Errors</span></span>

* <span data-ttu-id="f0597-203">Il s’agit d’une erreur à appliquer `OutAttribute` en tant que modreq à un type de retour.</span><span class="sxs-lookup"><span data-stu-id="f0597-203">It is an error to apply `OutAttribute` as a modreq to a return type.</span></span>
* <span data-ttu-id="f0597-204">L’application de `InAttribute` et `OutAttribute` en tant que modreq à un type de paramètre est une erreur.</span><span class="sxs-lookup"><span data-stu-id="f0597-204">It is an error to apply both `InAttribute` and `OutAttribute` as a modreq to a parameter type.</span></span>
* <span data-ttu-id="f0597-205">Si les deux sont spécifiés via modopt, ils sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="f0597-205">If either are specified via modopt, they are ignored.</span></span>

### <a name="metadata-representation-of-calling-conventions"></a><span data-ttu-id="f0597-206">Représentation des métadonnées des conventions d’appel</span><span class="sxs-lookup"><span data-stu-id="f0597-206">Metadata Representation of Calling Conventions</span></span>

<span data-ttu-id="f0597-207">Les conventions d’appel sont encodées dans une signature de méthode dans les métadonnées par une combinaison de l' `CallKind` indicateur dans la signature et zéro ou plusieurs `modopt` s au début de la signature.</span><span class="sxs-lookup"><span data-stu-id="f0597-207">Calling conventions are encoded in a method signature in metadata by a combination of the `CallKind` flag in the signature and zero or more `modopt`s at the start of the signature.</span></span> <span data-ttu-id="f0597-208">ECMA-335 déclare actuellement les éléments suivants dans l' `CallKind` indicateur :</span><span class="sxs-lookup"><span data-stu-id="f0597-208">ECMA-335 currently declares the following elements in the `CallKind` flag:</span></span>

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

<span data-ttu-id="f0597-209">Parmi ces fonctionnalités, les pointeurs de fonction en C# prendront en charge tous sauf `varargs` .</span><span class="sxs-lookup"><span data-stu-id="f0597-209">Of these, function pointers in C# will support all but `varargs`.</span></span>

<span data-ttu-id="f0597-210">En outre, le runtime (et finalement 335) sera mis à jour pour inclure un nouveau `CallKind` sur les nouvelles plateformes.</span><span class="sxs-lookup"><span data-stu-id="f0597-210">In addition, the runtime (and eventually 335) will be updated to include a new `CallKind` on new platforms.</span></span> <span data-ttu-id="f0597-211">Cela n’a pas de nom formel actuellement, mais ce document sera utilisé `unmanaged ext` comme espace réservé pour créer le nouveau format de convention d’appel extensible.</span><span class="sxs-lookup"><span data-stu-id="f0597-211">This does not have a formal name currently, but this document will use `unmanaged ext` as a placeholder to stand for the new extensible calling convention format.</span></span> <span data-ttu-id="f0597-212">Avec no `modopt` s, `unmanaged ext` est la Convention d’appel de la plateforme par défaut, `unmanaged` sans les crochets.</span><span class="sxs-lookup"><span data-stu-id="f0597-212">With no `modopt`s, `unmanaged ext` is the platform default calling convention, `unmanaged` without the square brackets.</span></span>

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a><span data-ttu-id="f0597-213">Mappage `calling_convention_specifier` de à un`CallKind`</span><span class="sxs-lookup"><span data-stu-id="f0597-213">Mapping the `calling_convention_specifier` to a `CallKind`</span></span>

<span data-ttu-id="f0597-214">`calling_convention_specifier`Qui est omis, ou spécifié comme `managed` , correspond à `default` `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="f0597-214">A `calling_convention_specifier` that is omitted, or specified as `managed`, maps to the `default` `CallKind`.</span></span> <span data-ttu-id="f0597-215">Il s’agit par défaut `CallKind` de toute méthode non attribuée avec `UnmanagedCallersOnly` .</span><span class="sxs-lookup"><span data-stu-id="f0597-215">This is default `CallKind` of any method not attributed with `UnmanagedCallersOnly`.</span></span>

<span data-ttu-id="f0597-216">C# reconnaît 4 identificateurs spéciaux qui mappent à des s non managés spécifiques existants `CallKind` à partir d’ECMA 335.</span><span class="sxs-lookup"><span data-stu-id="f0597-216">C# recognizes 4 special identifiers that map to specific existing unmanaged `CallKind`s from ECMA 335.</span></span> <span data-ttu-id="f0597-217">Pour que ce mappage se produise, ces identificateurs doivent être spécifiés eux-mêmes, sans aucun autre identificateur, et cette spécification est encodée dans la spécification pour `unmanaged_calling_convention` s.</span><span class="sxs-lookup"><span data-stu-id="f0597-217">In order for this mapping to occur, these identifiers must be specified on their own, with no other identifiers, and this requirement is encoded into the spec for `unmanaged_calling_convention`s.</span></span> <span data-ttu-id="f0597-218">Ces identificateurs sont `Cdecl` , `Thiscall` , `Stdcall` et `Fastcall` , qui correspondent respectivement à `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` et `unmanaged fastcall` .</span><span class="sxs-lookup"><span data-stu-id="f0597-218">These identifiers are `Cdecl`, `Thiscall`, `Stdcall`, and `Fastcall`, which correspond to `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, and `unmanaged fastcall`, respectively.</span></span> <span data-ttu-id="f0597-219">Si plusieurs `identifer` identificateurs sont spécifiés, ou si le seul ne `identifier` fait pas partie des identificateurs spécialement reconnus, nous effectuons une recherche de nom spéciale sur l’identificateur avec les règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0597-219">If more than one `identifer` is specified, or the single `identifier` is not of the specially recognized identifiers, we perform special name lookup on the identifier with the following rules:</span></span>

* <span data-ttu-id="f0597-220">Nous allons ajouter la `identifier` chaîne`CallConv`</span><span class="sxs-lookup"><span data-stu-id="f0597-220">We prepend the `identifier` with the string `CallConv`</span></span>
* <span data-ttu-id="f0597-221">Nous examinons uniquement les types définis dans l' `System.Runtime.CompilerServices` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="f0597-221">We look only at types defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="f0597-222">Nous examinons uniquement les types définis dans la bibliothèque principale de l’application, qui est la bibliothèque qui définit `System.Object` et n’a pas de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f0597-222">We look only at types defined in the core library of the application, which is the library that defines `System.Object` and has no dependencies.</span></span>

<span data-ttu-id="f0597-223">Si la recherche aboutit sur tous les `identifier` s spécifiés dans un `unmanaged_calling_convention` , nous encodez le `CallKind` en tant que `unmanaged ext` et encodez chacun des types résolus dans le jeu de `modopt` s au début de la signature du pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-223">If lookup succeeds on all of the `identifier`s specified in an `unmanaged_calling_convention`, we encode the `CallKind` as `unmanaged ext`, and encode each of the resolved types in the set of `modopt`s at the beginning of the function pointer signature.</span></span> <span data-ttu-id="f0597-224">En guise de note, ces règles signifient que les utilisateurs ne peuvent pas préfixer ces `identifier` s avec `CallConv` , car cela entraînera la recherche `CallConvCallConvVectorCall` .</span><span class="sxs-lookup"><span data-stu-id="f0597-224">As a note, these rules mean that users cannot prefix these `identifier`s with `CallConv`, as that will result in looking up `CallConvCallConvVectorCall`.</span></span>

<span data-ttu-id="f0597-225">Lors de l’interprétation des métadonnées, nous examinons d’abord le `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="f0597-225">When interpreting metadata, we first look at the `CallKind`.</span></span> <span data-ttu-id="f0597-226">Si ce n’est pas le cas `unmanaged ext` , nous ignorons tous les `modopt` sur le type de retour dans le cadre de la détermination de la Convention d’appel et n’utilisez que le `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="f0597-226">If it is anything other than `unmanaged ext`, we ignore all `modopt`s on the return type for the purposes of determining the calling convention, and use only the `CallKind`.</span></span> <span data-ttu-id="f0597-227">Si `CallKind` est `unmanaged ext` , nous examinons le modopts au début du type de pointeur de fonction, en prenant l’Union de tous les types qui remplissent les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0597-227">If the `CallKind` is `unmanaged ext`, we look at the modopts at the start of the function pointer type, taking the union of all types that meet the following requirements:</span></span>

* <span data-ttu-id="f0597-228">Est défini dans la bibliothèque principale, qui est la bibliothèque qui ne référence aucune autre bibliothèque et définit `System.Object` .</span><span class="sxs-lookup"><span data-stu-id="f0597-228">The is defined in the core library, which is the library that references no other libraries and defines `System.Object`.</span></span>
* <span data-ttu-id="f0597-229">Le type est défini dans l' `System.Runtime.CompilerServices` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="f0597-229">The type is defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="f0597-230">Le type commence par le préfixe `CallConv` .</span><span class="sxs-lookup"><span data-stu-id="f0597-230">The type starts with the prefix `CallConv`.</span></span>

 <span data-ttu-id="f0597-231">Celles-ci représentent les types qui doivent être trouvés lors de l’exécution de la recherche sur les `identifier` dans une lors de la `unmanaged_calling_convention` définition d’un type de pointeur fonction dans la source.</span><span class="sxs-lookup"><span data-stu-id="f0597-231">These represent the types that must be found when performing lookup on the `identifier`s in an `unmanaged_calling_convention` when defining a function pointer type in source.</span></span>

<span data-ttu-id="f0597-232">La tentative d’utilisation d’un pointeur de fonction avec un `CallKind` de `unmanaged ext` si le runtime cible ne prend pas en charge la fonctionnalité est une erreur.</span><span class="sxs-lookup"><span data-stu-id="f0597-232">It is an error to attempt to use a function pointer with a `CallKind` of `unmanaged ext` if the target runtime does not support the feature.</span></span> <span data-ttu-id="f0597-233">Cela sera déterminé en recherchant la présence de la `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constante.</span><span class="sxs-lookup"><span data-stu-id="f0597-233">This will be determined by looking for the presence of the `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constant.</span></span> <span data-ttu-id="f0597-234">Si cette constante est présente, le runtime est pris en compte pour prendre en charge la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f0597-234">If this constant is present, the runtime is considered to support the feature.</span></span>

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

<span data-ttu-id="f0597-235">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`est un attribut utilisé par le CLR pour indiquer qu’une méthode doit être appelée avec une convention d’appel spécifique.</span><span class="sxs-lookup"><span data-stu-id="f0597-235">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` is an attribute used by the CLR to indicate that a method should be called with a specific calling convention.</span></span> <span data-ttu-id="f0597-236">Pour cette raison, nous présentons la prise en charge suivante de l’utilisation de l’attribut :</span><span class="sxs-lookup"><span data-stu-id="f0597-236">Because of this, we introduce the following support for working with the attribute:</span></span>

* <span data-ttu-id="f0597-237">L’appel direct d’une méthode annotée avec cet attribut à partir de C# est une erreur.</span><span class="sxs-lookup"><span data-stu-id="f0597-237">It is an error to directly call a method annotated with this attribute from C#.</span></span> <span data-ttu-id="f0597-238">Les utilisateurs doivent obtenir un pointeur de fonction vers la méthode, puis appeler ce pointeur.</span><span class="sxs-lookup"><span data-stu-id="f0597-238">Users must obtain a function pointer to the method and then invoke that pointer.</span></span>
* <span data-ttu-id="f0597-239">L’application de l’attribut à autre chose qu’une méthode statique est une erreur.</span><span class="sxs-lookup"><span data-stu-id="f0597-239">It is an error to apply the attribute to anything other than a static method.</span></span> <span data-ttu-id="f0597-240">Le compilateur C# marque toutes les méthodes non statiques importées à partir des métadonnées avec cet attribut comme non prises en charge par le langage.</span><span class="sxs-lookup"><span data-stu-id="f0597-240">The C# compiler will mark any non-static methods imported from metadata with this attribute as unsupported by the language.</span></span>
* <span data-ttu-id="f0597-241">C’est une erreur d’avoir des types non non managés comme paramètres ou le type de retour d’une méthode marquée avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="f0597-241">It is an error to have non-unmanaged types as parameters or the return type of a method marked with the attribute.</span></span>
* <span data-ttu-id="f0597-242">La conversion d’une méthode marquée avec l’attribut en type délégué est une erreur.</span><span class="sxs-lookup"><span data-stu-id="f0597-242">It is an error to convert a method marked with the attribute to a delegate type.</span></span>
* <span data-ttu-id="f0597-243">La spécification de tous les types pour `UnmanagedCallersOnly.CallConvs` qui ne satisfont pas aux conditions requises pour l’appel de Convention dans les métadonnées est une erreur `modopt` .</span><span class="sxs-lookup"><span data-stu-id="f0597-243">It is an error to specify any types for `UnmanagedCallersOnly.CallConvs` that do not meet the requirements for calling convention `modopt`s in metadata.</span></span>

<span data-ttu-id="f0597-244">Lors de la détermination de la Convention d’appel d’une méthode marquée avec un `UnmanagedCallersOnly` attribut valide, le compilateur effectue les vérifications suivantes sur les types spécifiés dans la `CallConvs` propriété afin de déterminer les effectifs `CallKind` et les `modopt` à utiliser pour déterminer la Convention d’appel :</span><span class="sxs-lookup"><span data-stu-id="f0597-244">When determining the calling convention of a method marked with a valid `UnmanagedCallersOnly` attribute, the compiler performs the following checks on the types specified in the `CallConvs` property to determine the effective `CallKind` and `modopt`s that should be used to determine the calling convention:</span></span>

* <span data-ttu-id="f0597-245">Si aucun type n’est spécifié, `CallKind` est traité comme `unmanaged ext` , sans convention d’appel, `modopt` au début du type de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-245">If no types are specified, the `CallKind` is treated as `unmanaged ext`, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="f0597-246">Si un type est spécifié et que ce type est nommé `CallConvCdecl` , `CallConvThiscall` , `CallConvStdcall` ou `CallConvFastcall` , le `CallKind` est traité comme `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` ou `unmanaged fastcall` , respectivement, sans convention d’appel `modopt` au début du type de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-246">If there is one type specified, and that type is named `CallConvCdecl`, `CallConvThiscall`, `CallConvStdcall`, or `CallConvFastcall`, the `CallKind` is treated as `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, or `unmanaged fastcall`, respectively, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="f0597-247">Si plusieurs types sont spécifiés ou si le type unique n’est pas nommé l’un des types spécialement appelés ci-dessus, le `CallKind` est traité comme `unmanaged ext` , avec l’Union des types spécifiés traités comme `modopt` s au début du type de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-247">If multiple types are specified or the single type is not named one of the specially called out types above, the `CallKind` is treated as `unmanaged ext`, with the union of the types specified treated as `modopt`s at the start of the function pointer type.</span></span>

<span data-ttu-id="f0597-248">Le compilateur examine ensuite cet effet `CallKind` et cette `modopt` collection et utilise des règles de métadonnées normales pour déterminer la Convention d’appel finale du type de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-248">The compiler then looks at this effective `CallKind` and `modopt` collection and uses normal metadata rules to determine the final calling convention of the function pointer type.</span></span>

## <a name="open-questions"></a><span data-ttu-id="f0597-249">Questions ouvertes</span><span class="sxs-lookup"><span data-stu-id="f0597-249">Open Questions</span></span>

### <a name="detecting-runtime-support-for-unmanaged-ext"></a><span data-ttu-id="f0597-250">Détection de la prise en charge du runtime pour`unmanaged ext`</span><span class="sxs-lookup"><span data-stu-id="f0597-250">Detecting runtime support for `unmanaged ext`</span></span>

<span data-ttu-id="f0597-251">https://github.com/dotnet/runtime/issues/38135effectue le suivi de l’ajout de cet indicateur.</span><span class="sxs-lookup"><span data-stu-id="f0597-251">https://github.com/dotnet/runtime/issues/38135 tracks adding this flag.</span></span> <span data-ttu-id="f0597-252">Selon les commentaires de la revue, nous utilisons la propriété spécifiée dans le problème ou utilisons la présence de `UnmanagedCallersOnlyAttribute` comme indicateur qui détermine si les runtimes prennent en charge `unmanaged ext` .</span><span class="sxs-lookup"><span data-stu-id="f0597-252">Depending on the feedback from review, we will either use the property specified in the issue, or use the presence of `UnmanagedCallersOnlyAttribute` as the flag that determines whether the runtimes supports `unmanaged ext`.</span></span>

## <a name="considerations"></a><span data-ttu-id="f0597-253">Considérations</span><span class="sxs-lookup"><span data-stu-id="f0597-253">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="f0597-254">Autoriser les méthodes d’instance</span><span class="sxs-lookup"><span data-stu-id="f0597-254">Allow instance methods</span></span>

<span data-ttu-id="f0597-255">La proposition peut être étendue pour prendre en charge les méthodes d’instance en tirant parti de la `EXPLICITTHIS` Convention d’appel CLI (nommée `instance` dans le code C#).</span><span class="sxs-lookup"><span data-stu-id="f0597-255">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="f0597-256">Cette forme de pointeurs de fonction CLI place le `this` paramètre en tant que premier paramètre explicite de la syntaxe du pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-256">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="f0597-257">C’est un son, mais il ajoute une certaine compliquation à la proposition.</span><span class="sxs-lookup"><span data-stu-id="f0597-257">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="f0597-258">En particulier parce que les pointeurs de fonction qui diffèrent par la Convention d’appel `instance` et `managed` seraient incompatibles même si les deux cas sont utilisés pour appeler des méthodes managées avec la même signature C#.</span><span class="sxs-lookup"><span data-stu-id="f0597-258">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="f0597-259">Dans tous les cas, dans tous les cas, il est utile de disposer d’une solution de contournement simple : utilisez une `static` fonction locale.</span><span class="sxs-lookup"><span data-stu-id="f0597-259">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="f0597-260">Ne nécessite pas de non-sécurité au niveau de la déclaration</span><span class="sxs-lookup"><span data-stu-id="f0597-260">Don't require unsafe at declaration</span></span>

<span data-ttu-id="f0597-261">Au lieu de demander `unsafe` à chaque utilisation d’un `delegate*` , n’en avez besoin qu’au point où un groupe de méthodes est converti en `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="f0597-261">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="f0597-262">C’est là qu’interviennent les principaux problèmes de sécurité (sachant que l’assembly conteneur ne peut pas être déchargé tant que la valeur est active).</span><span class="sxs-lookup"><span data-stu-id="f0597-262">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="f0597-263">`unsafe`Le besoin d’un autre emplacement peut être considéré comme excessif.</span><span class="sxs-lookup"><span data-stu-id="f0597-263">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="f0597-264">C’est ainsi que la conception était prévue à l’origine.</span><span class="sxs-lookup"><span data-stu-id="f0597-264">This is how the design was originally intended.</span></span> <span data-ttu-id="f0597-265">Mais les règles de langage qui en résultent sont très maladroites.</span><span class="sxs-lookup"><span data-stu-id="f0597-265">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="f0597-266">Il est impossible de masquer le fait qu’il s’agit d’une valeur de pointeur et qu’elle reste en lecture seule sans le `unsafe` mot clé.</span><span class="sxs-lookup"><span data-stu-id="f0597-266">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="f0597-267">Par exemple, la conversion vers `object` ne peut pas être autorisée, il ne peut pas être membre d’un `class` , etc... La conception C# est nécessaire `unsafe` pour toutes les utilisations de pointeur. par conséquent, cette conception suit cela.</span><span class="sxs-lookup"><span data-stu-id="f0597-267">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="f0597-268">Les développeurs peuvent toujours présenter un wrapper _sécurisé_ sur `delegate*` les valeurs de la même façon que pour les types pointeur normaux.</span><span class="sxs-lookup"><span data-stu-id="f0597-268">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="f0597-269">Considérez les aspects suivants :</span><span class="sxs-lookup"><span data-stu-id="f0597-269">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="f0597-270">Utilisation de délégués</span><span class="sxs-lookup"><span data-stu-id="f0597-270">Using delegates</span></span>

<span data-ttu-id="f0597-271">Au lieu d’utiliser un nouvel élément de syntaxe, `delegate*` , utilisez simplement `delegate` les types existants avec `*` le type suivant :</span><span class="sxs-lookup"><span data-stu-id="f0597-271">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="f0597-272">La gestion de la Convention d’appel peut être effectuée en annotant les `delegate` types avec un attribut qui spécifie une `CallingConvention` valeur.</span><span class="sxs-lookup"><span data-stu-id="f0597-272">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="f0597-273">L’absence d’un attribut signifie la Convention d’appel managée.</span><span class="sxs-lookup"><span data-stu-id="f0597-273">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="f0597-274">L’encodage de ce en IL pose problème.</span><span class="sxs-lookup"><span data-stu-id="f0597-274">Encoding this in IL is problematic.</span></span> <span data-ttu-id="f0597-275">La valeur sous-jacente doit être représentée sous la forme d’un pointeur, mais elle doit également :</span><span class="sxs-lookup"><span data-stu-id="f0597-275">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="f0597-276">Avoir un type unique pour autoriser les surcharges avec différents types de pointeur de fonction.</span><span class="sxs-lookup"><span data-stu-id="f0597-276">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="f0597-277">Être équivalent à des fins de OHI dans les limites de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="f0597-277">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="f0597-278">Le dernier point est particulièrement problématique.</span><span class="sxs-lookup"><span data-stu-id="f0597-278">The last point is particularly problematic.</span></span> <span data-ttu-id="f0597-279">Cela signifie que chaque assembly qui utilise `Func<int>*` doit encoder un type équivalent dans les métadonnées même si `Func<int>*` est défini dans un assembly, même si ne contrôle pas.</span><span class="sxs-lookup"><span data-stu-id="f0597-279">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="f0597-280">En outre, tout autre type défini avec le nom `System.Func<T>` dans un assembly qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.</span><span class="sxs-lookup"><span data-stu-id="f0597-280">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="f0597-281">Une option qui a été explorée a émis un tel pointeur comme `mod_req(Func<int>) void*` .</span><span class="sxs-lookup"><span data-stu-id="f0597-281">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="f0597-282">Cela ne fonctionne pas tant qu’un `mod_req` ne peut pas être lié à un `TypeSpec` et ne peut donc pas cibler des instanciations génériques.</span><span class="sxs-lookup"><span data-stu-id="f0597-282">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="f0597-283">Pointeurs de fonction nommés</span><span class="sxs-lookup"><span data-stu-id="f0597-283">Named function pointers</span></span>

<span data-ttu-id="f0597-284">La syntaxe du pointeur de fonction peut être lourde, en particulier dans les cas complexes comme les pointeurs de fonction imbriqués.</span><span class="sxs-lookup"><span data-stu-id="f0597-284">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="f0597-285">Au lieu de demander aux développeurs de taper la signature chaque fois que le langage peut autoriser des déclarations nommées de pointeurs de fonction comme c’est le cas avec `delegate` .</span><span class="sxs-lookup"><span data-stu-id="f0597-285">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="f0597-286">Une partie du problème est ici que la primitive CLI sous-jacente n’a pas de nom, ce qui est purement une invention C# et nécessite un peu de métadonnées pour permettre l’activation.</span><span class="sxs-lookup"><span data-stu-id="f0597-286">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="f0597-287">C’est réalisable, mais il s’agit d’un travail important.</span><span class="sxs-lookup"><span data-stu-id="f0597-287">That is doable but is a significant about of work.</span></span> <span data-ttu-id="f0597-288">En fait, C# a besoin d’un compagnon pour la table de définition de type pour ces noms uniquement.</span><span class="sxs-lookup"><span data-stu-id="f0597-288">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="f0597-289">De même, lorsque les arguments des pointeurs de fonction nommés ont été examinés, nous avons trouvé qu’ils s’appliquent aussi bien à un certain nombre d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="f0597-289">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="f0597-290">Par exemple, il serait tout aussi commode de déclarer des tuples nommés pour réduire le besoin de taper la signature complète dans tous les cas.</span><span class="sxs-lookup"><span data-stu-id="f0597-290">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="f0597-291">Après la discussion, nous avons décidé de ne pas autoriser la déclaration nommée de `delegate*` types.</span><span class="sxs-lookup"><span data-stu-id="f0597-291">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="f0597-292">Si nous constatons qu’il y a des besoins importants en ce qui concerne les commentaires sur l’utilisation des clients, nous étudierons une solution d’attribution de noms qui fonctionne pour les pointeurs de fonction, les tuples, les génériques, etc. Cela peut être similaire à d’autres suggestions, telles que `typedef` la prise en charge complète dans le langage.</span><span class="sxs-lookup"><span data-stu-id="f0597-292">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="f0597-293">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="f0597-293">Future Considerations</span></span>

### <a name="static-delegates"></a><span data-ttu-id="f0597-294">délégués statiques</span><span class="sxs-lookup"><span data-stu-id="f0597-294">static delegates</span></span>

<span data-ttu-id="f0597-295">Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/302) pour permettre la déclaration de `delegate` types qui peuvent uniquement faire référence à des `static` membres.</span><span class="sxs-lookup"><span data-stu-id="f0597-295">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="f0597-296">L’avantage est qu' `delegate` il est possible d’allouer gratuitement et mieux les scénarios de performances.</span><span class="sxs-lookup"><span data-stu-id="f0597-296">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="f0597-297">Si la fonctionnalité du pointeur de fonction est implémentée `static delegate` , la proposition sera probablement fermée. L’avantage proposé par cette fonctionnalité est la nature libre de l’allocation.</span><span class="sxs-lookup"><span data-stu-id="f0597-297">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="f0597-298">Toutefois, des investigations récentes ont détecté que le déchargement d’assembly n’est pas possible en raison du déchargement de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="f0597-298">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="f0597-299">Il doit y avoir un handle fort de l’objet `static delegate` à la méthode à laquelle il fait référence pour empêcher le déchargement de l’assembly à partir de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="f0597-299">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="f0597-300">Pour conserver chaque `static delegate` instance, il est nécessaire d’allouer un nouveau descripteur qui s’exécute sur les objectifs de la proposition.</span><span class="sxs-lookup"><span data-stu-id="f0597-300">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="f0597-301">Il existait des conceptions où l’allocation pouvait être amortie en une seule allocation par site d’appel, mais cela était un peu complexe et ne semble pas avoir un intérêt pour le compromis.</span><span class="sxs-lookup"><span data-stu-id="f0597-301">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="f0597-302">Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants :</span><span class="sxs-lookup"><span data-stu-id="f0597-302">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="f0597-303">Sécurité face au déchargement d’un assembly : cela nécessite des allocations et `delegate` est donc déjà une option suffisante.</span><span class="sxs-lookup"><span data-stu-id="f0597-303">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="f0597-304">Aucune sécurité en face du déchargement d’assembly : utilisez un `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="f0597-304">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="f0597-305">Cela peut être encapsulé dans un `struct` pour permettre l’utilisation en dehors d’un `unsafe` contexte dans le reste du code.</span><span class="sxs-lookup"><span data-stu-id="f0597-305">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
