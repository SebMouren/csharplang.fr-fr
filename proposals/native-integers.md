---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: d414836632ba2730545e0b058373a94696bba5e4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81447817"
---
# <a name="native-sized-integers"></a><span data-ttu-id="29ff3-101">Intégrants de taille autochtone</span><span class="sxs-lookup"><span data-stu-id="29ff3-101">Native-sized integers</span></span>

## <a name="summary"></a><span data-ttu-id="29ff3-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="29ff3-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="29ff3-103">Soutien linguistique pour les types d’intégreurs signés et non signés de taille autochtone.</span><span class="sxs-lookup"><span data-stu-id="29ff3-103">Language support for a native-sized signed and unsigned integer types.</span></span>

<span data-ttu-id="29ff3-104">La motivation est pour les scénarios interop et pour les bibliothèques de bas niveau.</span><span class="sxs-lookup"><span data-stu-id="29ff3-104">The motivation is for interop scenarios and for low-level libraries.</span></span>

## <a name="design"></a><span data-ttu-id="29ff3-105">Conception</span><span class="sxs-lookup"><span data-stu-id="29ff3-105">Design</span></span>
[design]: #design

<span data-ttu-id="29ff3-106">Les identificateurs `nint` et `nuint` sont de nouveaux mots-clés contextuels qui représentent les types d’integer signés et non signés natifs.</span><span class="sxs-lookup"><span data-stu-id="29ff3-106">The identifiers `nint` and `nuint` are new contextual keywords that represent native signed and unsigned integer types.</span></span>
<span data-ttu-id="29ff3-107">Les identificateurs ne sont traités comme des mots clés que lorsque le lookup nom ne trouve pas de résultat viable à cet emplacement du programme.</span><span class="sxs-lookup"><span data-stu-id="29ff3-107">The identifiers are only treated as keywords when name lookup does not find a viable result at that program location.</span></span>
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

<span data-ttu-id="29ff3-108">Les `nint` types `nuint` et sont représentés `System.IntPtr` `System.UIntPtr` par les types sous-jacents et avec compilateur revêtement conversions supplémentaires et les opérations pour ces types en tant qu’ints indigènes.</span><span class="sxs-lookup"><span data-stu-id="29ff3-108">The types `nint` and `nuint` are represented by the underlying types `System.IntPtr` and `System.UIntPtr` with compiler surfacing additional conversions and operations for those types as native ints.</span></span>

### <a name="constants"></a><span data-ttu-id="29ff3-109">Constantes</span><span class="sxs-lookup"><span data-stu-id="29ff3-109">Constants</span></span>

<span data-ttu-id="29ff3-110">Les expressions constantes `nint` `nuint`peuvent être de type ou .</span><span class="sxs-lookup"><span data-stu-id="29ff3-110">Constant expressions may be of type `nint` or `nuint`.</span></span>
<span data-ttu-id="29ff3-111">Il n’y a pas de syntaxe directe pour les littérals int indigènes.</span><span class="sxs-lookup"><span data-stu-id="29ff3-111">There is no direct syntax for native int literals.</span></span> <span data-ttu-id="29ff3-112">Des moulages implicites ou explicites d’autres valeurs constantes intégrales peuvent plutôt être utilisés : `const nint i = (nint)42;`.</span><span class="sxs-lookup"><span data-stu-id="29ff3-112">Implicit or explicit casts of other integral constant values can be used instead: `const nint i = (nint)42;`.</span></span>

<span data-ttu-id="29ff3-113">`nint`constantes sont dans `int.MinValue`la `int.MaxValue` gamme [ , ].</span><span class="sxs-lookup"><span data-stu-id="29ff3-113">`nint` constants are in the range [ `int.MinValue`, `int.MaxValue` ].</span></span>

<span data-ttu-id="29ff3-114">`nuint`constantes sont dans `uint.MinValue`la `uint.MaxValue` gamme [ , ].</span><span class="sxs-lookup"><span data-stu-id="29ff3-114">`nuint` constants are in the range [ `uint.MinValue`, `uint.MaxValue` ].</span></span>

<span data-ttu-id="29ff3-115">Il n’y a `MinValue` pas ou `MaxValue` champs sur `nint` ou `nuint` parce que, autre que, `nuint.MinValue`ces valeurs ne peuvent pas être émises comme des constantes.</span><span class="sxs-lookup"><span data-stu-id="29ff3-115">There are no `MinValue` or `MaxValue` fields on `nint` or `nuint` because, other than `nuint.MinValue`, those values cannot be emitted as constants.</span></span>

<span data-ttu-id="29ff3-116">Le pliage constant est pris `+`en `-` `~` charge pour tous `+` `-`les `*` `/`opérateurs `%` `==`nonaires, et les opérateurs binaires, et les opérateurs binaires, et , , , , `!=` `<`, , , `<=`, `>` `>=`, `&`, `^` `<<` `>>` `|`</span><span class="sxs-lookup"><span data-stu-id="29ff3-116">Constant folding is supported for all unary operators { `+`, `-`, `~` } and binary operators { `+`, `-`, `*`, `/`, `%`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `&`, `|`, `^`, `<<`, `>>` }.</span></span>
<span data-ttu-id="29ff3-117">Les opérations de pliage `Int32` `UInt32` constantes sont évaluées avec et opérandes plutôt que des ints indigènes pour un comportement cohérent indépendamment de la plate-forme de compilateur.</span><span class="sxs-lookup"><span data-stu-id="29ff3-117">Constant folding operations are evaluated with `Int32` and `UInt32` operands rather than native ints for consistent behavior regardless of compiler platform.</span></span>
<span data-ttu-id="29ff3-118">Si l’opération se traduit par une valeur constante en 32 bits, le pliage constant est effectué au temps de compilation.</span><span class="sxs-lookup"><span data-stu-id="29ff3-118">If the operation results in a constant value in 32-bits, constant folding is performed at compile-time.</span></span>
<span data-ttu-id="29ff3-119">Dans le cas contraire, l’opération est exécutée au moment de la course et n’est pas considérée comme une constante.</span><span class="sxs-lookup"><span data-stu-id="29ff3-119">Otherwise the operation is executed at runtime and not considered a constant.</span></span>

### <a name="conversions"></a><span data-ttu-id="29ff3-120">Conversions</span><span class="sxs-lookup"><span data-stu-id="29ff3-120">Conversions</span></span>
<span data-ttu-id="29ff3-121">Il y a `nint` une `IntPtr`conversion `nuint` d’identité entre et , et entre et `UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="29ff3-121">There is an identity conversion between `nint` and `IntPtr`, and between `nuint` and `UIntPtr`.</span></span>
<span data-ttu-id="29ff3-122">Il y a une conversion d’identité entre les types de composés qui diffèrent par les ints indigènes et les types sous-jacents seulement : les tableaux, `Nullable<>`les types construits, et les tuples.</span><span class="sxs-lookup"><span data-stu-id="29ff3-122">There is an identity conversion between compound types that differ by native ints and underlying types only: arrays, `Nullable<>`, constructed types, and tuples.</span></span>

<span data-ttu-id="29ff3-123">Les tableaux ci-dessous couvrent les conversions entre les types spéciaux.</span><span class="sxs-lookup"><span data-stu-id="29ff3-123">The tables below cover the conversions between special types.</span></span>
<span data-ttu-id="29ff3-124">(L’IL pour chaque conversion `unchecked` comprend `checked` les variantes et les contextes si différent.)</span><span class="sxs-lookup"><span data-stu-id="29ff3-124">(The IL for each conversion includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="29ff3-125">Opérande</span><span class="sxs-lookup"><span data-stu-id="29ff3-125">Operand</span></span> | <span data-ttu-id="29ff3-126">Cible</span><span class="sxs-lookup"><span data-stu-id="29ff3-126">Target</span></span> | <span data-ttu-id="29ff3-127">Conversion</span><span class="sxs-lookup"><span data-stu-id="29ff3-127">Conversion</span></span> | <span data-ttu-id="29ff3-128">IL</span><span class="sxs-lookup"><span data-stu-id="29ff3-128">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | <span data-ttu-id="29ff3-129">Unboxing</span><span class="sxs-lookup"><span data-stu-id="29ff3-129">Unboxing</span></span> | `unbox` |
| `void*` | `nint` | <span data-ttu-id="29ff3-130">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="29ff3-130">PointerToVoid</span></span> | `conv.i` |
| `sbyte` | `nint` | <span data-ttu-id="29ff3-131">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-131">ImplicitNumeric</span></span> | `conv.i` |
| `byte` | `nint` | <span data-ttu-id="29ff3-132">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-132">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nint` | <span data-ttu-id="29ff3-133">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-133">ImplicitNumeric</span></span> | `conv.i` |
| `ushort` | `nint` | <span data-ttu-id="29ff3-134">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-134">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nint` | <span data-ttu-id="29ff3-135">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-135">ImplicitNumeric</span></span> | `conv.i` |
| `uint` | `nint` | <span data-ttu-id="29ff3-136">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-136">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | <span data-ttu-id="29ff3-137">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-137">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | <span data-ttu-id="29ff3-138">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-138">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | <span data-ttu-id="29ff3-139">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-139">ImplicitNumeric</span></span> | `conv.i` |
| `float` | `nint` | <span data-ttu-id="29ff3-140">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-140">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | <span data-ttu-id="29ff3-141">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-141">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | <span data-ttu-id="29ff3-142">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-142">ExplicitNumeric</span></span> | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | <span data-ttu-id="29ff3-143">Identité</span><span class="sxs-lookup"><span data-stu-id="29ff3-143">Identity</span></span> | |
| `UIntPtr` | `nint` | <span data-ttu-id="29ff3-144">None</span><span class="sxs-lookup"><span data-stu-id="29ff3-144">None</span></span> | |
| `object` | `nuint` | <span data-ttu-id="29ff3-145">Unboxing</span><span class="sxs-lookup"><span data-stu-id="29ff3-145">Unboxing</span></span> | `unbox` |
| `void*` | `nuint` | <span data-ttu-id="29ff3-146">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="29ff3-146">PointerToVoid</span></span> | `conv.u` |
| `sbyte` | `nuint` | <span data-ttu-id="29ff3-147">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-147">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | <span data-ttu-id="29ff3-148">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-148">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nuint` | <span data-ttu-id="29ff3-149">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-149">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | <span data-ttu-id="29ff3-150">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-150">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nuint` | <span data-ttu-id="29ff3-151">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-151">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | <span data-ttu-id="29ff3-152">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-152">ImplicitNumeric</span></span> | `conv.u` |
| `long` | `nuint` | <span data-ttu-id="29ff3-153">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-153">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | <span data-ttu-id="29ff3-154">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-154">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | <span data-ttu-id="29ff3-155">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-155">ImplicitNumeric</span></span> | `conv.u` |
| `float` | `nuint` | <span data-ttu-id="29ff3-156">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-156">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | <span data-ttu-id="29ff3-157">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-157">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | <span data-ttu-id="29ff3-158">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-158">ExplicitNumeric</span></span> | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | <span data-ttu-id="29ff3-159">None</span><span class="sxs-lookup"><span data-stu-id="29ff3-159">None</span></span> | |
| `UIntPtr` | `nuint` | <span data-ttu-id="29ff3-160">Identité</span><span class="sxs-lookup"><span data-stu-id="29ff3-160">Identity</span></span> | |

| <span data-ttu-id="29ff3-161">Opérande</span><span class="sxs-lookup"><span data-stu-id="29ff3-161">Operand</span></span> | <span data-ttu-id="29ff3-162">Cible</span><span class="sxs-lookup"><span data-stu-id="29ff3-162">Target</span></span> | <span data-ttu-id="29ff3-163">Conversion</span><span class="sxs-lookup"><span data-stu-id="29ff3-163">Conversion</span></span> | <span data-ttu-id="29ff3-164">IL</span><span class="sxs-lookup"><span data-stu-id="29ff3-164">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | <span data-ttu-id="29ff3-165">Boxing</span><span class="sxs-lookup"><span data-stu-id="29ff3-165">Boxing</span></span> | `box` |
| `nint` | `void*` | <span data-ttu-id="29ff3-166">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="29ff3-166">PointerToVoid</span></span> | `conv.i` |
| `nint` | `nuint` | <span data-ttu-id="29ff3-167">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-167">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | <span data-ttu-id="29ff3-168">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-168">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | <span data-ttu-id="29ff3-169">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-169">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | <span data-ttu-id="29ff3-170">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-170">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | <span data-ttu-id="29ff3-171">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-171">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | <span data-ttu-id="29ff3-172">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-172">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | <span data-ttu-id="29ff3-173">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-173">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | <span data-ttu-id="29ff3-174">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-174">ImplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | <span data-ttu-id="29ff3-175">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-175">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | <span data-ttu-id="29ff3-176">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-176">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | <span data-ttu-id="29ff3-177">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-177">ImplicitNumeric</span></span> | `conv.r4` |
| `nint` | `double` | <span data-ttu-id="29ff3-178">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-178">ImplicitNumeric</span></span> | `conv.r8` |
| `nint` | `decimal` | <span data-ttu-id="29ff3-179">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-179">ImplicitNumeric</span></span> | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | <span data-ttu-id="29ff3-180">Identité</span><span class="sxs-lookup"><span data-stu-id="29ff3-180">Identity</span></span> | |
| `nint` | `UIntPtr` | <span data-ttu-id="29ff3-181">None</span><span class="sxs-lookup"><span data-stu-id="29ff3-181">None</span></span> | |
| `nuint` | `object` | <span data-ttu-id="29ff3-182">Boxing</span><span class="sxs-lookup"><span data-stu-id="29ff3-182">Boxing</span></span> | `box` |
| `nuint` | `void*` | <span data-ttu-id="29ff3-183">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="29ff3-183">PointerToVoid</span></span> | `conv.u` |
| `nuint` | `nint` | <span data-ttu-id="29ff3-184">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-184">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | <span data-ttu-id="29ff3-185">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-185">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | <span data-ttu-id="29ff3-186">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-186">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | <span data-ttu-id="29ff3-187">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-187">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | <span data-ttu-id="29ff3-188">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-188">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | <span data-ttu-id="29ff3-189">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-189">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | <span data-ttu-id="29ff3-190">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-190">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | <span data-ttu-id="29ff3-191">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-191">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | <span data-ttu-id="29ff3-192">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-192">ImplicitNumeric</span></span> | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | <span data-ttu-id="29ff3-193">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-193">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | <span data-ttu-id="29ff3-194">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-194">ImplicitNumeric</span></span> | `conv.r.un conv.r4` |
| `nuint` | `double` | <span data-ttu-id="29ff3-195">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-195">ImplicitNumeric</span></span> | `conv.r.un conv.r8` |
| `nuint` | `decimal` | <span data-ttu-id="29ff3-196">ImpliciteNumeric</span><span class="sxs-lookup"><span data-stu-id="29ff3-196">ImplicitNumeric</span></span> | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | <span data-ttu-id="29ff3-197">None</span><span class="sxs-lookup"><span data-stu-id="29ff3-197">None</span></span> | |
| `nuint` | `UIntPtr` | <span data-ttu-id="29ff3-198">Identité</span><span class="sxs-lookup"><span data-stu-id="29ff3-198">Identity</span></span> | |

<span data-ttu-id="29ff3-199">Conversion `A` à `Nullable<B>` partir de est:</span><span class="sxs-lookup"><span data-stu-id="29ff3-199">Conversion from `A` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="29ff3-200">une conversion implicite in annulable s’il `A` y `B`a une conversion d’identité ou une conversion implicite à ;</span><span class="sxs-lookup"><span data-stu-id="29ff3-200">an implicit nullable conversion if there is an identity conversion or implicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="29ff3-201">une conversion annulée explicite s’il `A` y `B`a une conversion explicite de ;</span><span class="sxs-lookup"><span data-stu-id="29ff3-201">an explicit nullable conversion if there is an explicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="29ff3-202">autrement invalide.</span><span class="sxs-lookup"><span data-stu-id="29ff3-202">otherwise invalid.</span></span>

<span data-ttu-id="29ff3-203">Conversion `Nullable<A>` à `B` partir de est:</span><span class="sxs-lookup"><span data-stu-id="29ff3-203">Conversion from `Nullable<A>` to `B` is:</span></span>
- <span data-ttu-id="29ff3-204">une conversion annulée explicite s’il y a une `A` conversion `B`d’identité ou une conversion numérique implicite ou explicite de ;</span><span class="sxs-lookup"><span data-stu-id="29ff3-204">an explicit nullable conversion if there is an identity conversion or implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="29ff3-205">autrement invalide.</span><span class="sxs-lookup"><span data-stu-id="29ff3-205">otherwise invalid.</span></span>

<span data-ttu-id="29ff3-206">Conversion `Nullable<A>` à `Nullable<B>` partir de est:</span><span class="sxs-lookup"><span data-stu-id="29ff3-206">Conversion from `Nullable<A>` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="29ff3-207">une conversion d’identité s’il y a une conversion d’identité de `A` ; `B`</span><span class="sxs-lookup"><span data-stu-id="29ff3-207">an identity conversion if there is an identity conversion from `A` to `B`;</span></span>
- <span data-ttu-id="29ff3-208">une conversion nulle explicite s’il y a `A` `B`une conversion numérique implicite ou explicite de ;</span><span class="sxs-lookup"><span data-stu-id="29ff3-208">an explicit nullable conversion if there is an implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="29ff3-209">autrement invalide.</span><span class="sxs-lookup"><span data-stu-id="29ff3-209">otherwise invalid.</span></span>

### <a name="operators"></a><span data-ttu-id="29ff3-210">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="29ff3-210">Operators</span></span>

<span data-ttu-id="29ff3-211">Les opérateurs prédéfinis sont les suivants.</span><span class="sxs-lookup"><span data-stu-id="29ff3-211">The predefined operators are as follows.</span></span>
<span data-ttu-id="29ff3-212">Ces opérateurs sont considérés lors de la résolution de surcharge basée sur des règles normales pour les conversions implicites _si au moins l’un des `nint` opérandes est de type ou `nuint` _.</span><span class="sxs-lookup"><span data-stu-id="29ff3-212">These operators are considered during overload resolution based on normal rules for implicit conversions _if at least one of the operands is of type `nint` or `nuint`_.</span></span>

<span data-ttu-id="29ff3-213">(L’IL pour chaque opérateur `unchecked` comprend `checked` les variantes et les contextes si différent.)</span><span class="sxs-lookup"><span data-stu-id="29ff3-213">(The IL for each operator includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="29ff3-214">Unaire</span><span class="sxs-lookup"><span data-stu-id="29ff3-214">Unary</span></span> | <span data-ttu-id="29ff3-215">Signature de l’opérateur</span><span class="sxs-lookup"><span data-stu-id="29ff3-215">Operator Signature</span></span> | <span data-ttu-id="29ff3-216">IL</span><span class="sxs-lookup"><span data-stu-id="29ff3-216">IL</span></span> |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| <span data-ttu-id="29ff3-217">Binary</span><span class="sxs-lookup"><span data-stu-id="29ff3-217">Binary</span></span> | <span data-ttu-id="29ff3-218">Signature de l’opérateur</span><span class="sxs-lookup"><span data-stu-id="29ff3-218">Operator Signature</span></span> | <span data-ttu-id="29ff3-219">IL</span><span class="sxs-lookup"><span data-stu-id="29ff3-219">IL</span></span> |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint left, nint right)` | `add` / `add.ovf` |
| `+` | `nuint operator +(nuint left, nuint right)` | `add` / `add.ovf.un` |
| `-` | `nint operator -(nint left, nint right)` | `sub` / `sub.ovf` |
| `-` | `nuint operator -(nuint left, nuint right)` | `sub` / `sub.ovf.un` |
| `*` | `nint operator *(nint left, nint right)` | `mul` / `mul.ovf` |
| `*` | `nuint operator *(nuint left, nuint right)` | `mul` / `mul.ovf.un` |
| `/` | `nint operator /(nint left, nint right)` | `div` |
| `/` | `nuint operator /(nuint left, nuint right)` | `div.un` |
| `%` | `nint operator %(nint left, nint right)` | `rem` |
| `%` | `nuint operator %(nuint left, nuint right)` | `rem.un` |
| `==` | `bool operator ==(nint left, nint right)` | `beq` / `ceq` |
| `==` | `bool operator ==(nuint left, nuint right)` | `beq` / `ceq` |
| `!=` | `bool operator !=(nint left, nint right)` | `bne` |
| `!=` | `bool operator !=(nuint left, nuint right)` | `bne` |
| `<` | `bool operator <(nint left, nint right)` | `blt` / `clt` |
| `<` | `bool operator <(nuint left, nuint right)` | `blt.un` / `clt.un` |
| `<=` | `bool operator <=(nint left, nint right)` | `ble` |
| `<=` | `bool operator <=(nuint left, nuint right)` | `ble.un` |
| `>` | `bool operator >(nint left, nint right)` | `bgt` / `cgt` |
| `>` | `bool operator >(nuint left, nuint right)` | `bgt.un` / `cgt.un` |
| `>=` | `bool operator >=(nint left, nint right)` | `bge` |
| `>=` | `bool operator >=(nuint left, nuint right)` | `bge.un` |
| `&` | `nint operator &(nint left, nint right)` | `and` |
| `&` | `nuint operator &(nuint left, nuint right)` | `and` |
| <code>&#124;</code> | <code>nint operator &#124;(nint left, nint right)</code> | `or` |
| <code>&#124;</code> | <code>nuint operator &#124;(nuint left, nuint right)</code> | `or` |
| `^` | `nint operator ^(nint left, nint right)` | `xor` |
| `^` | `nuint operator ^(nuint left, nuint right)` | `xor` |
| `<<` | `nint operator <<(nint left, int right)` | `shl` |
| `<<` | `nuint operator <<(nuint left, int right)` | `shl` |
| `>>` | `nint operator >>(nint left, int right)` | `shr` |
| `>>` | `nuint operator >>(nuint left, int right)` | `shr.un` |

<span data-ttu-id="29ff3-220">Pour certains opérateurs binaires, les opérateurs de l’IL prennent en charge d’autres types d’opéra (voir [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Tableau de type Operand).</span><span class="sxs-lookup"><span data-stu-id="29ff3-220">For some binary operators, the IL operators support additional operand types (see [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Operand type table).</span></span>
<span data-ttu-id="29ff3-221">Mais l’ensemble des types d’opéras soutenus par le C est limité pour la simplicité et la cohérence avec les opérateurs existants dans la langue.</span><span class="sxs-lookup"><span data-stu-id="29ff3-221">But the set of operand types supported by C# is limited for simplicity and for consistency with existing operators in the language.</span></span>

<span data-ttu-id="29ff3-222">Les versions levées des opérateurs, où les arguments et les types de retour sont `nint?` et, `nuint?`sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="29ff3-222">Lifted versions of the operators, where the arguments and return types are `nint?` and `nuint?`, are supported.</span></span>

<span data-ttu-id="29ff3-223">Les opérations `x op= y` `x` d’affectation composées où les `y` ints indigènes suivent les mêmes règles qu’avec d’autres types primitifs avec des opérateurs prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="29ff3-223">Compound assignment operations `x op= y` where `x` or `y` are native ints follow the same rules as with other primitive types with pre-defined operators.</span></span>
<span data-ttu-id="29ff3-224">Plus précisément, l’expression est liée `x = (T)(x op y)` comme où `T` est le type et où `x` `x` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="29ff3-224">Specifically the expression is bound as `x = (T)(x op y)` where `T` is the type of `x` and where `x` is only evaluated once.</span></span>

<span data-ttu-id="29ff3-225">Les opérateurs de quart doivent masquer le nombre de `sizeof(nint)` bits à déplacer - à `sizeof(nint)` 5 bits si est de 4, et à 6 bits si est de 8.</span><span class="sxs-lookup"><span data-stu-id="29ff3-225">The shift operators should mask the number of bits to shift - to 5 bits if `sizeof(nint)` is 4, and to 6 bits if `sizeof(nint)` is 8.</span></span>
<span data-ttu-id="29ff3-226">(voir [opérateurs de quarts](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) dans C spec).</span><span class="sxs-lookup"><span data-stu-id="29ff3-226">(see [shift operators](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) in C# spec).</span></span>

### <a name="dynamic"></a><span data-ttu-id="29ff3-227">Dynamique</span><span class="sxs-lookup"><span data-stu-id="29ff3-227">Dynamic</span></span>

<span data-ttu-id="29ff3-228">Les conversions et les opérateurs sont synthétisés par le `IntPtr` `UIntPtr` compilateur et ne font pas partie du sous-jacent et des types.</span><span class="sxs-lookup"><span data-stu-id="29ff3-228">The conversions and operators are synthesized by the compiler and are not part of the underlying `IntPtr` and `UIntPtr` types.</span></span>
<span data-ttu-id="29ff3-229">En conséquence, ces conversions et les opérateurs ne `dynamic`sont pas _disponibles_ à partir du classeur de temps d’exécution pour .</span><span class="sxs-lookup"><span data-stu-id="29ff3-229">As a result those conversions and operators _are not available_ from the runtime binder for `dynamic`.</span></span> 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a><span data-ttu-id="29ff3-230">Membres de type</span><span class="sxs-lookup"><span data-stu-id="29ff3-230">Type members</span></span>

<span data-ttu-id="29ff3-231">Le seul constructeur `nint` `nuint` pour ou est le constructeur sans paramètres.</span><span class="sxs-lookup"><span data-stu-id="29ff3-231">The only constructor for `nint` or `nuint` is the parameter-less constructor.</span></span>

<span data-ttu-id="29ff3-232">Les membres `System.IntPtr` suivants `System.UIntPtr` et sont `nint` explicitement `nuint` _exclus_ ou :</span><span class="sxs-lookup"><span data-stu-id="29ff3-232">The following members of `System.IntPtr` and `System.UIntPtr` _are explicitly excluded_ from `nint` or `nuint`:</span></span>
```C#
// constructors
// arithmetic operators
// implicit and explicit conversions
public static readonly IntPtr Zero; // use 0 instead
public static int Size { get; }     // use sizeof() instead
public static IntPtr Add(IntPtr pointer, int offset);
public static IntPtr Subtract(IntPtr pointer, int offset);
public int ToInt32();
public long ToInt64();
public void* ToPointer();
```

<span data-ttu-id="29ff3-233">Les autres `System.IntPtr` membres `System.UIntPtr` et _sont implicitement inclus_ dans `nint` et `nuint`.</span><span class="sxs-lookup"><span data-stu-id="29ff3-233">The remaining members of `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`.</span></span> <span data-ttu-id="29ff3-234">Pour .NET Framework 4.7.2:</span><span class="sxs-lookup"><span data-stu-id="29ff3-234">For .NET Framework 4.7.2:</span></span>
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

<span data-ttu-id="29ff3-235">Les interfaces `System.IntPtr` `System.UIntPtr` mises en œuvre par et _sont implicitement incluses_ dans `nint` et, `nuint`avec des occurrences des types sous-jacents remplacés par les types d’intégreurs indigènes correspondants.</span><span class="sxs-lookup"><span data-stu-id="29ff3-235">Interfaces implemented by `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`, with occurrences of the underlying types replaced by the corresponding native integer types.</span></span>
<span data-ttu-id="29ff3-236">Par `IntPtr` exemple, `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`si `nint` les `ISerializable, IEquatable<nint>, IComparable<nint>`impléments , puis implémente .</span><span class="sxs-lookup"><span data-stu-id="29ff3-236">For instance if `IntPtr` implements `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`, then `nint` implements `ISerializable, IEquatable<nint>, IComparable<nint>`.</span></span>

### <a name="overriding-hiding-and-implementing"></a><span data-ttu-id="29ff3-237">Dépassement, dissimulation et mise en œuvre</span><span class="sxs-lookup"><span data-stu-id="29ff3-237">Overriding, hiding, and implementing</span></span>

<span data-ttu-id="29ff3-238">`nint`et, `System.IntPtr` `nuint` et `System.UIntPtr`, sont considérés comme équivalents pour la prépondérer, la dissimulation et la mise en œuvre.</span><span class="sxs-lookup"><span data-stu-id="29ff3-238">`nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, are considered equivalent for overriding, hiding, and implementing.</span></span>

<span data-ttu-id="29ff3-239">Les surcharges ne `nint` `System.IntPtr`peuvent `nuint` pas `System.UIntPtr`différer par et, et, à elle seule.</span><span class="sxs-lookup"><span data-stu-id="29ff3-239">Overloads cannot differ by `nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="29ff3-240">Les remplacements et les `nint` implémentations peuvent différer d’ici et, `System.IntPtr` `nuint` `System.UIntPtr`ou, seul.</span><span class="sxs-lookup"><span data-stu-id="29ff3-240">Overrides and implementations may differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="29ff3-241">Les méthodes cachent `nint` d’autres `System.UIntPtr`méthodes qui diffèrent par et, `System.IntPtr`ou, `nuint` seul.</span><span class="sxs-lookup"><span data-stu-id="29ff3-241">Methods hide other methods that differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="29ff3-242">Divers</span><span class="sxs-lookup"><span data-stu-id="29ff3-242">Miscellaneous</span></span>

<span data-ttu-id="29ff3-243">`nint`et `nuint` les expressions utilisées comme indices de tableau sont émises sans conversion.</span><span class="sxs-lookup"><span data-stu-id="29ff3-243">`nint` and `nuint` expressions used as array indices are emitted without conversion.</span></span>
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

<span data-ttu-id="29ff3-244">`nint`et `nuint` peut être `enum` utilisé comme type de base.</span><span class="sxs-lookup"><span data-stu-id="29ff3-244">`nint` and `nuint` can be used as an `enum` base type.</span></span>
```C#
enum E : nint // ok
{
}
```

<span data-ttu-id="29ff3-245">Lectures et écrits `nint`sont `nuint`atomiques pour les types , , et `enum` avec le type `nint` de base ou `nuint`.</span><span class="sxs-lookup"><span data-stu-id="29ff3-245">Reads and writes are atomic for types `nint`, `nuint`, and `enum` with base type `nint` or `nuint`.</span></span>

<span data-ttu-id="29ff3-246">Les champs `volatile` peuvent `nint` être `nuint`marqués pour les types et .</span><span class="sxs-lookup"><span data-stu-id="29ff3-246">Fields may be marked `volatile` for types `nint` and `nuint`.</span></span>
<span data-ttu-id="29ff3-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 n’inclut `enum` `System.IntPtr` pas `System.UIntPtr` avec le type de base ou cependant.</span><span class="sxs-lookup"><span data-stu-id="29ff3-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 does not include `enum` with base type `System.IntPtr` or `System.UIntPtr` however.</span></span>

<span data-ttu-id="29ff3-248">`default(nint)`et `new nint()` sont `(nint)0`équivalents à .</span><span class="sxs-lookup"><span data-stu-id="29ff3-248">`default(nint)` and `new nint()` are equivalent to `(nint)0`.</span></span>

<span data-ttu-id="29ff3-249">`typeof(nint)` a la valeur `typeof(IntPtr)`.</span><span class="sxs-lookup"><span data-stu-id="29ff3-249">`typeof(nint)` is `typeof(IntPtr)`.</span></span>

<span data-ttu-id="29ff3-250">`sizeof(nint)`est pris en charge, mais nécessite la `sizeof(IntPtr)`compilation dans un contexte dangereux (comme le fait ).</span><span class="sxs-lookup"><span data-stu-id="29ff3-250">`sizeof(nint)` is supported but requires compiling in an unsafe context (as does `sizeof(IntPtr)`).</span></span>
<span data-ttu-id="29ff3-251">La valeur n’est pas une constante de compilateur.</span><span class="sxs-lookup"><span data-stu-id="29ff3-251">The value is not a compile-time constant.</span></span>
<span data-ttu-id="29ff3-252">`sizeof(nint)`est mis `sizeof(IntPtr)` en `IntPtr.Size`œuvre comme plutôt que .</span><span class="sxs-lookup"><span data-stu-id="29ff3-252">`sizeof(nint)` is implemented as `sizeof(IntPtr)` rather than `IntPtr.Size`.</span></span>

<span data-ttu-id="29ff3-253">Compilateur de diagnostics `nint` pour `nuint` `nint` les `nuint` références `IntPtr` `UIntPtr`de type impliquant ou de rapport ou plutôt ou .</span><span class="sxs-lookup"><span data-stu-id="29ff3-253">Compiler diagnostics for type references involving `nint` or `nuint` report `nint` or `nuint` rather than `IntPtr` or `UIntPtr`.</span></span>

### <a name="metadata"></a><span data-ttu-id="29ff3-254">Métadonnées</span><span class="sxs-lookup"><span data-stu-id="29ff3-254">Metadata</span></span>

<span data-ttu-id="29ff3-255">`nint`et `nuint` sont représentés dans `System.IntPtr` les `System.UIntPtr`métadonnées au fur et à mesure.</span><span class="sxs-lookup"><span data-stu-id="29ff3-255">`nint` and `nuint` are represented in metadata as `System.IntPtr` and `System.UIntPtr`.</span></span>

<span data-ttu-id="29ff3-256">Type de `nint` références `nuint` qui incluent `System.Runtime.CompilerServices.NativeIntegerAttribute` ou sont émises avec un pour indiquer quelles parties de la référence de type sont des ints indigènes.</span><span class="sxs-lookup"><span data-stu-id="29ff3-256">Type references that include `nint` or `nuint` are emitted with a `System.Runtime.CompilerServices.NativeIntegerAttribute` to indicate which parts of the type reference are native ints.</span></span>

```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(
        AttributeTargets.Class |
        AttributeTargets.Event |
        AttributeTargets.Field |
        AttributeTargets.GenericParameter |
        AttributeTargets.Parameter |
        AttributeTargets.Property |
        AttributeTargets.ReturnValue,
        AllowMultiple = false,
        Inherited = false)]
    public sealed class NativeIntegerAttribute : Attribute
    {
        public NativeIntegerAttribute()
        {
            TransformFlags = new[] { true };
        }
        public NativeIntegerAttribute(bool[] flags)
        {
            TransformFlags = flags;
        }
        public IList<bool> TransformFlags { get; }
    }
}
```

<span data-ttu-id="29ff3-257">L’encodage utilise l’approche utilisée pour coder `DynamicAttribute`, bien qu’il soit évident `DynamicAttribute` qu’il s’agit d’encoder quels types de référence sont `dynamic` plutôt que les types d’ints natifs.</span><span class="sxs-lookup"><span data-stu-id="29ff3-257">The encoding uses the approach as used to encode `DynamicAttribute`, although obviously `DynamicAttribute` is encoding which types within the type reference are `dynamic` rather than which types are native ints.</span></span>
<span data-ttu-id="29ff3-258">Si l’encodage `false` donne lieu `NativeIntegerAttribute` à un éventail de valeurs, aucun n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="29ff3-258">If the encoding results in an array of `false` values, no `NativeIntegerAttribute` is needed.</span></span>
<span data-ttu-id="29ff3-259">Le constructeur `NativeIntegerAttribute` sans paramètres génère un codage d’une seule `true` valeur.</span><span class="sxs-lookup"><span data-stu-id="29ff3-259">The parameterless `NativeIntegerAttribute` constructor generates an encoding with a single `true` value.</span></span>

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a><span data-ttu-id="29ff3-260">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="29ff3-260">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="29ff3-261">Une alternative à l’approche de « l’effacement `System.NativeInt` `System.NativeUInt`de type » ci-dessus est d’introduire de nouveaux types : et .</span><span class="sxs-lookup"><span data-stu-id="29ff3-261">An alternative to the "type erasure" approach above is to introduce new types: `System.NativeInt` and `System.NativeUInt`.</span></span>
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
<span data-ttu-id="29ff3-262">Les types distincts permettraient `IntPtr` une surcharge distincte de `ToString()`l’analyse et de l’analyse distinctes et.</span><span class="sxs-lookup"><span data-stu-id="29ff3-262">Distinct types would allow overloading distinct from `IntPtr` and would allow distinct parsing and `ToString()`.</span></span>
<span data-ttu-id="29ff3-263">Mais il y aurait plus de travail pour le CLR pour gérer ces types efficacement qui va à l’encontre de l’objectif principal de la fonctionnalité - l’efficacité.</span><span class="sxs-lookup"><span data-stu-id="29ff3-263">But there would be more work for the CLR to handle these types efficiently which defeats the primary purpose of the feature - efficiency.</span></span>
<span data-ttu-id="29ff3-264">Et interop avec le code `IntPtr` int indigène existant qui utilise serait plus difficile.</span><span class="sxs-lookup"><span data-stu-id="29ff3-264">And interop with existing native int code that uses `IntPtr` would be more difficult.</span></span> 

<span data-ttu-id="29ff3-265">Une autre alternative est d’ajouter `IntPtr` plus de soutien int natif pour dans le cadre, mais sans aucun support compilateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="29ff3-265">Another alternative is to add more native int support for `IntPtr` in the framework but without any specific compiler support.</span></span>
<span data-ttu-id="29ff3-266">Toute nouvelle conversion et opérations d’arithmétique seraient prises en charge automatiquement par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="29ff3-266">Any new conversions and arithmetic operations would be supported by the compiler automatically.</span></span>
<span data-ttu-id="29ff3-267">Mais la langue ne fournirait pas `checked` de mots clés, de constantes ou d’opérations.</span><span class="sxs-lookup"><span data-stu-id="29ff3-267">But the language would not provide keywords, constants, or `checked` operations.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="29ff3-268">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="29ff3-268">Design meetings</span></span>

<span data-ttu-id="29ff3-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span><span class="sxs-lookup"><span data-stu-id="29ff3-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span></span>
