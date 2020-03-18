---
ms.openlocfilehash: ac4c8760e3b6a0934f01ae634f666af60aa1c0fe
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484565"
---
# <a name="compiler-intrinsics"></a><span data-ttu-id="14c0a-101">compilateur, intrinsèques</span><span class="sxs-lookup"><span data-stu-id="14c0a-101">Compiler Intrinsics</span></span>

## <a name="summary"></a><span data-ttu-id="14c0a-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="14c0a-102">Summary</span></span>

<span data-ttu-id="14c0a-103">Cette proposition fournit des constructions de langage qui exposent des opcodes IL de bas niveau qui ne sont pas actuellement accessibles efficacement, ou du tout : `ldftn`, `ldvirtftn`, `ldtoken` et `calli`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-103">This proposal provides language constructs that expose low level IL opcodes that cannot currently be accessed efficiently, or at all: `ldftn`, `ldvirtftn`, `ldtoken` and `calli`.</span></span> <span data-ttu-id="14c0a-104">Ces opcodes de bas niveau peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="14c0a-104">These low level opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="14c0a-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="14c0a-105">Motivation</span></span>

<span data-ttu-id="14c0a-106">Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) :</span><span class="sxs-lookup"><span data-stu-id="14c0a-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span> 

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="14c0a-107">Cette proposition de conception alternative vient après la révision d’une implémentation prototype de la proposition d’origine par @msjabby, ainsi que de l’utilisation dans une base de code importante.</span><span class="sxs-lookup"><span data-stu-id="14c0a-107">This alternate design proposal comes after reviewing a prototype implementation of the original proposal by @msjabby as well as the use throughout a significant code base.</span></span> <span data-ttu-id="14c0a-108">Cette conception a été effectuée avec une entrée significative de @mjsabby, @tmat et @jkotas.</span><span class="sxs-lookup"><span data-stu-id="14c0a-108">This design was done with significant input from @mjsabby, @tmat and @jkotas.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="14c0a-109">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="14c0a-109">Detailed Design</span></span> 

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="14c0a-110">Autoriser l’adresse de aux méthodes cibles</span><span class="sxs-lookup"><span data-stu-id="14c0a-110">Allow address of to target methods</span></span>

<span data-ttu-id="14c0a-111">Les groupes de méthodes sont désormais autorisés comme arguments d’une expression d’adresse.</span><span class="sxs-lookup"><span data-stu-id="14c0a-111">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="14c0a-112">Le type d’une telle expression sera `void*`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-112">The type of such an expression will be `void*`.</span></span> 

``` csharp
class Util { 
    public static void Log() { } 
}

// ldftn Util.Log
void* ptr = &Util.Log; 
```

<span data-ttu-id="14c0a-113">Étant donné qu’il n’y a pas de conversion de délégué ici, le seul mécanisme de filtrage des membres dans le groupe de méthodes est l’accès statique/d’instance.</span><span class="sxs-lookup"><span data-stu-id="14c0a-113">Given there is no delegate conversion here the only mechanism for filtering members in the method group is by static / instance access.</span></span> <span data-ttu-id="14c0a-114">Si cela ne peut pas faire la distinction entre les membres, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="14c0a-114">If that cannot distinguish the members then a compile time error will occur.</span></span>

``` csharp
class Util { 
    public void Log() { } 
    public void Log(string p1) { } 
    public static void Log(int i) { };
}

unsafe {
    // Error: Method group Log has more than one applicable candidate.
    void* ptr1 = &Log; 

    // Okay: only one static member to consider here.
    void* ptr2 = &Util.Log;
}
```

<span data-ttu-id="14c0a-115">L’expression AddressOf dans ce contexte sera implémentée de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="14c0a-115">The addressof expression in this context will be implemented in the following manner:</span></span>

- <span data-ttu-id="14c0a-116">ldftn : lorsque la méthode n’est pas virtuelle.</span><span class="sxs-lookup"><span data-stu-id="14c0a-116">ldftn: when the method is non-virtual.</span></span>
- <span data-ttu-id="14c0a-117">ldvirtftn : lorsque la méthode est virtuelle.</span><span class="sxs-lookup"><span data-stu-id="14c0a-117">ldvirtftn: when the method is virtual.</span></span>

<span data-ttu-id="14c0a-118">Restrictions de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="14c0a-118">Restrictions of this feature:</span></span>

- <span data-ttu-id="14c0a-119">Les méthodes d’instance ne peuvent être spécifiées que lors de l’utilisation d’une expression d’appel sur une valeur</span><span class="sxs-lookup"><span data-stu-id="14c0a-119">Instance methods can only be specified when using an invocation expression on a value</span></span>
- <span data-ttu-id="14c0a-120">Les fonctions locales ne peuvent pas être utilisées dans `&`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-120">Local functions cannot be used in `&`.</span></span> <span data-ttu-id="14c0a-121">Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage.</span><span class="sxs-lookup"><span data-stu-id="14c0a-121">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="14c0a-122">Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.</span><span class="sxs-lookup"><span data-stu-id="14c0a-122">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>

### <a name="handleof"></a><span data-ttu-id="14c0a-123">handleof</span><span class="sxs-lookup"><span data-stu-id="14c0a-123">handleof</span></span>

<span data-ttu-id="14c0a-124">Le mot clé contextuel `handleof` traduira un champ, un membre ou un type en son équivalent `RuntimeHandle` type à l’aide de l’instruction `ldtoken`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-124">The `handleof` contextual keyword will translate a field, member or type into their equivalent `RuntimeHandle` type using the `ldtoken` instruction.</span></span> <span data-ttu-id="14c0a-125">Le type exact de l’expression dépend du type du nom dans `handleof`:</span><span class="sxs-lookup"><span data-stu-id="14c0a-125">The exact type of the expression will depend on the kind of the name in `handleof`:</span></span>

- <span data-ttu-id="14c0a-126">champ : `RuntimeFieldHandle`</span><span class="sxs-lookup"><span data-stu-id="14c0a-126">field: `RuntimeFieldHandle`</span></span>
- <span data-ttu-id="14c0a-127">Type : `RuntimeTypeHandle`</span><span class="sxs-lookup"><span data-stu-id="14c0a-127">type: `RuntimeTypeHandle`</span></span>
- <span data-ttu-id="14c0a-128">méthode : `RuntimeMethodHandle`</span><span class="sxs-lookup"><span data-stu-id="14c0a-128">method: `RuntimeMethodHandle`</span></span>

<span data-ttu-id="14c0a-129">Les arguments de `handleof` sont identiques à `nameof`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-129">The arguments to `handleof` are identical to `nameof`.</span></span> <span data-ttu-id="14c0a-130">Il doit s’agir d’un nom simple, d’un nom qualifié, d’un accès aux membres, d’un accès de base avec un membre spécifié ou de cet accès avec un membre spécifié.</span><span class="sxs-lookup"><span data-stu-id="14c0a-130">It must be a simple name, qualified name, member access, base access with a specified member, or this access with a specified member.</span></span> <span data-ttu-id="14c0a-131">L'expression d'argument identifie une définition de code, mais n'est jamais évaluée.</span><span class="sxs-lookup"><span data-stu-id="14c0a-131">The argument expression identifies a code definition, but it is never evaluated.</span></span>

<span data-ttu-id="14c0a-132">L’expression `handleof` est évaluée au moment de l’exécution et a un type de retour `RuntimeHandle`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-132">The `handleof` expression is evaluated at runtime and has a return type of `RuntimeHandle`.</span></span> <span data-ttu-id="14c0a-133">Cela peut être exécuté dans du code sécurisé et non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="14c0a-133">This can be executed in safe code as well as unsafe.</span></span> 

``` 
RuntimeHandle stringHandle = handleof(string);
```

<span data-ttu-id="14c0a-134">Restrictions de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="14c0a-134">Restrictions of this feature:</span></span>

- <span data-ttu-id="14c0a-135">Les propriétés ne peuvent pas être utilisées dans une expression `handleof`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-135">Properties cannot be used in a `handleof` expression.</span></span>
- <span data-ttu-id="14c0a-136">L’expression `handleof` ne peut pas être utilisée lorsqu’un nom de `handleof` existe dans l’étendue.</span><span class="sxs-lookup"><span data-stu-id="14c0a-136">The `handleof` expression cannot be used when there is an existing `handleof` name in scope.</span></span> <span data-ttu-id="14c0a-137">Par exemple, un type, un espace de noms, etc.</span><span class="sxs-lookup"><span data-stu-id="14c0a-137">For example a type, namespace, etc ...</span></span>

### <a name="calli"></a><span data-ttu-id="14c0a-138">calli</span><span class="sxs-lookup"><span data-stu-id="14c0a-138">calli</span></span>

<span data-ttu-id="14c0a-139">Le compilateur ajoute la prise en charge d’un nouveau type de `extern` fonction qui se traduit efficacement en une instruction `.calli`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-139">The compiler will add support for a new type of `extern` function that efficiently translates into a `.calli` instruction.</span></span> <span data-ttu-id="14c0a-140">L’attribut extern est marqué avec un attribut de la forme suivante :</span><span class="sxs-lookup"><span data-stu-id="14c0a-140">The extern attribute will be marked with an attribute of the following shape:</span></span>

``` csharp
[AttributeUsage(AttributeTargets.Method)]
public sealed class CallIndirectAttribute : Attribute
{
    public CallingConvention CallingConvention { get; }
    public CallIndirectAttribute(CallingConvention callingConvention)
    {
        CallingConvention = callingConvention;
    }
}
```

<span data-ttu-id="14c0a-141">Cela permet aux développeurs de définir des méthodes sous la forme suivante :</span><span class="sxs-lookup"><span data-stu-id="14c0a-141">This allows developers to define methods in the following form:</span></span>

``` csharp
[CallIndirect(CallingConvention.Cdecl)]
static extern int MapValue(string s, void *ptr);

unsafe {
    var i = MapValue("42", &int.Parse);
    Console.WriteLine(i);
}
```

<span data-ttu-id="14c0a-142">Restrictions sur la méthode qui a l’attribut `CallIndirect` appliqué :</span><span class="sxs-lookup"><span data-stu-id="14c0a-142">Restrictions on the method which has the `CallIndirect` attribute applied:</span></span>

- <span data-ttu-id="14c0a-143">Impossible d’avoir un attribut `DllImport`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-143">Cannot have a `DllImport` attribute.</span></span>
- <span data-ttu-id="14c0a-144">Ne peut pas être générique.</span><span class="sxs-lookup"><span data-stu-id="14c0a-144">Cannot be generic.</span></span>

## <a name="open-issues"></a><span data-ttu-id="14c0a-145">Problèmes ouverts</span><span class="sxs-lookup"><span data-stu-id="14c0a-145">Open Issues</span></span>

### <a name="callingconvention"></a><span data-ttu-id="14c0a-146">CallingConvention</span><span class="sxs-lookup"><span data-stu-id="14c0a-146">CallingConvention</span></span>

<span data-ttu-id="14c0a-147">La `CallIndirectAttribute` conçue utilise l’énumération `CallingConvention` qui n’a pas d’entrée pour les conventions d’appel managées.</span><span class="sxs-lookup"><span data-stu-id="14c0a-147">The `CallIndirectAttribute` as designed uses the `CallingConvention` enum which lacks an entry for managed calling conventions.</span></span> <span data-ttu-id="14c0a-148">L’énumération doit être étendue pour inclure cette Convention d’appel ou l’attribut doit adopter une approche différente.</span><span class="sxs-lookup"><span data-stu-id="14c0a-148">The enum either needs to be extended to include this calling convention or the attribute needs to take a different approach.</span></span>

## <a name="considerations"></a><span data-ttu-id="14c0a-149">Considérations</span><span class="sxs-lookup"><span data-stu-id="14c0a-149">Considerations</span></span>

### <a name="disambiguating-method-groups"></a><span data-ttu-id="14c0a-150">Groupes de méthodes disambiguating</span><span class="sxs-lookup"><span data-stu-id="14c0a-150">Disambiguating method groups</span></span>

<span data-ttu-id="14c0a-151">Il y a eu une discussion sur les fonctionnalités qui facilitent la désambiguisation des groupes de méthodes transmises à une expression d’adresse.</span><span class="sxs-lookup"><span data-stu-id="14c0a-151">There was some discussion around features that would make it easier to disambiguate method groups passed to an address-of expression.</span></span> <span data-ttu-id="14c0a-152">Par exemple, il est possible d’ajouter des éléments de signature à la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="14c0a-152">For instance potentially adding signature elements to the syntax:</span></span>

``` csharp
class Util {
    public static void Log() { ... }
    public static void Log(string) { ... }
}

unsafe {
    // Error: ambiguous Log
    void *ptr1 = &Util.Log;

    // Use Util.Log();
    void *ptr2 = &Util.Log();
}
```

<span data-ttu-id="14c0a-153">Cette erreur a été rejetée, car il n’a pas été possible de faire une syntaxe simple.</span><span class="sxs-lookup"><span data-stu-id="14c0a-153">This was rejected because a compelling case could not be made nor could a simple syntax be envisioned here.</span></span> <span data-ttu-id="14c0a-154">En outre, il existe une solution de contournement relativement simple : définissez une autre méthode qui n’est pas C# ambiguë et qui utilise du code pour appeler la fonction souhaitée.</span><span class="sxs-lookup"><span data-stu-id="14c0a-154">Also there is a fairly straight forward work around: simple define another method that is unambiguous and uses C# code to call into the desired function.</span></span> 

``` csharp
class Workaround {
    public static void LocalLog() => Util.Log();
}
unsafe { 
    void* ptr = &Workaround.LocalLog;
}
```

<span data-ttu-id="14c0a-155">Cela devient encore plus simple si `static` fonctions locales entrent dans la langue.</span><span class="sxs-lookup"><span data-stu-id="14c0a-155">This becomes even simpler if `static` local functions enter the language.</span></span> <span data-ttu-id="14c0a-156">La solution peut être définie dans la même fonction que celle qui utilisait l’opération d’adresse ambiguë :</span><span class="sxs-lookup"><span data-stu-id="14c0a-156">Then the work around could be defined in the same function that used the ambiguous address-of operation:</span></span>

``` csharp
unsafe { 
    static void LocalLog() => Util.Log();
    void* ptr = &Workaround.LocalLog;
}
```

### <a name="loadtypetokenint32"></a><span data-ttu-id="14c0a-157">LoadTypeTokenInt32</span><span class="sxs-lookup"><span data-stu-id="14c0a-157">LoadTypeTokenInt32</span></span>

<span data-ttu-id="14c0a-158">Proposition d’origine autorisée pour les jetons de métadonnées à charger en tant que valeurs de `int` au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="14c0a-158">The original proposal allowed for metadata tokens to be loaded as `int` values at compile time.</span></span> <span data-ttu-id="14c0a-159">A essentiellement `tokenof` qui a les mêmes arguments que `handleof` mais est évalué au moment de la compilation sur une constante `int`.</span><span class="sxs-lookup"><span data-stu-id="14c0a-159">Essentially have `tokenof` that has the same arguments as `handleof` but is evaluated at compile time to an `int` constant.</span></span> 

<span data-ttu-id="14c0a-160">Cela a été rejeté, car cela provoque un problème important pour les réécritures de langage intermédiaire (dont .NET a beaucoup).</span><span class="sxs-lookup"><span data-stu-id="14c0a-160">This was rejected as it causes significant problem for IL rewrites (of which .NET has many).</span></span> <span data-ttu-id="14c0a-161">De tels réécriturs manipulent souvent les tables de métadonnées d’une manière qui pourrait invalider ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="14c0a-161">Such rewriters often manipulate the metadata tables in a way that could invalidate these values.</span></span> <span data-ttu-id="14c0a-162">Il n’existe pas de moyen raisonnable pour ces réécritions de mettre à jour ces valeurs quand elles sont stockées en tant que valeurs de `int` simples.</span><span class="sxs-lookup"><span data-stu-id="14c0a-162">There is no reasonable way for such rewriters to update these values when they are stored as simple `int` values.</span></span>

<span data-ttu-id="14c0a-163">L’idée sous-jacente de la présence d’un handle opaque pour les entrées de métadonnées continuera d’être explorée par l’équipe du Runtime.</span><span class="sxs-lookup"><span data-stu-id="14c0a-163">The underlying idea of having an opaque handle for metadata entries will continue to be explored by the runtime team.</span></span> 

## <a name="future-considerations"></a><span data-ttu-id="14c0a-164">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="14c0a-164">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="14c0a-165">fonctions locales statiques</span><span class="sxs-lookup"><span data-stu-id="14c0a-165">static local functions</span></span>

<span data-ttu-id="14c0a-166">Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/1565) pour autoriser le modificateur `static` sur les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="14c0a-166">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="14c0a-167">Une telle fonction serait toujours émise comme `static` et avec la signature exacte spécifiée dans le code source.</span><span class="sxs-lookup"><span data-stu-id="14c0a-167">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="14c0a-168">Une telle fonction doit être un argument valide pour `&`, car elle ne contient aucun des problèmes liés aux fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="14c0a-168">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today.</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="14c0a-169">NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="14c0a-169">NativeCallableAttribute</span></span>

<span data-ttu-id="14c0a-170">Le CLR dispose d’une fonctionnalité qui permet aux méthodes managées d’être émises de sorte qu’elles puissent être appelées directement à partir du code natif.</span><span class="sxs-lookup"><span data-stu-id="14c0a-170">The CLR has a feature that allows for managed methods to be emitted in such a way that they are directly callable from native code.</span></span> <span data-ttu-id="14c0a-171">Pour ce faire, ajoutez les `NativeCallableAttribute` aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="14c0a-171">This is done by adding the `NativeCallableAttribute` to methods.</span></span> <span data-ttu-id="14c0a-172">Une telle méthode peut uniquement être appelée à partir du code natif et doit donc contenir uniquement des types blittables dans la signature.</span><span class="sxs-lookup"><span data-stu-id="14c0a-172">Such a method is only callable from native code and hence must contain only blittable types in the signature.</span></span> <span data-ttu-id="14c0a-173">L’appel à partir du code managé génère une erreur d’exécution.</span><span class="sxs-lookup"><span data-stu-id="14c0a-173">Calling from managed code results in a runtime error.</span></span> 

<span data-ttu-id="14c0a-174">Cette fonctionnalité est parfaitement adaptée à cette offre, comme cela serait le cas :</span><span class="sxs-lookup"><span data-stu-id="14c0a-174">This feature would pattern well with this proposal as it would allow:</span></span>

- <span data-ttu-id="14c0a-175">Passage d’une fonction définie en code managé au code natif en tant que pointeur de fonction (via Address-of) sans surcharge en code managé ou natif.</span><span class="sxs-lookup"><span data-stu-id="14c0a-175">Passing a function defined in managed code to native code as a function pointer (via address-of) with no overhead in managed or native code.</span></span> 
- <span data-ttu-id="14c0a-176">Le runtime peut introduire des erreurs de site à l’aide de ces fonctions dans du code managé afin de les empêcher d’être appelées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="14c0a-176">Runtime can introduce use site errors for such functions in managed code to prevent them from being invoked at compile time.</span></span>




