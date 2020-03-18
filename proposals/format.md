---
ms.openlocfilehash: 1457c1eb018e12af30ce5b38be704bf8851d4b25
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "79485013"
---
# <a name="efficient-params-and-string-formatting"></a><span data-ttu-id="5f5d3-101">Paramètres et mise en forme de chaîne efficaces</span><span class="sxs-lookup"><span data-stu-id="5f5d3-101">Efficient Params and String Formatting</span></span>

## <a name="summary"></a><span data-ttu-id="5f5d3-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="5f5d3-102">Summary</span></span>
<span data-ttu-id="5f5d3-103">Cette combinaison de fonctionnalités augmente l’efficacité de la mise en forme `string` valeurs et le passage d’arguments de style `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-103">This combination of features will increase the efficiency of formatting `string` values and passing of `params` style arguments.</span></span>

## <a name="motivation"></a><span data-ttu-id="5f5d3-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="5f5d3-104">Motivation</span></span>
<span data-ttu-id="5f5d3-105">La surcharge d’allocation de la mise en forme des valeurs `string` peut dominer les performances de nombreuses applications basées sur du texte : à partir de la pénalité boxing des types de `struct`, l’allocation de `object[]` pour `params` et les allocations de `string` intermédiaires pendant les appels de `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-105">The allocation overhead of formatting `string` values can dominate the performance of many text based applications: from the boxing penalty of `struct` types, the `object[]` allocation for `params` and the intermediate `string` allocations during `string.Format` calls.</span></span> <span data-ttu-id="5f5d3-106">Pour maintenir l’efficacité, ces applications doivent souvent abandonner des fonctionnalités de productivité, telles que `params` et `string` l’interpolation, et passer à des solutions codées non standard.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-106">In order to maintain efficiency such applications often need to abandon productivity features such as `params` and `string` interpolation and move to non-standard, hand coded solutions.</span></span> 

<span data-ttu-id="5f5d3-107">Considérez MSBuild comme un exemple.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-107">Consider MSBuild as an example.</span></span> <span data-ttu-id="5f5d3-108">Cela est écrit à l’aide de nombreuses C# fonctionnalités modernes par les développeurs qui sont conscients des performances.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-108">This is written using a lot of modern C# features by developers who are conscious of performance.</span></span> <span data-ttu-id="5f5d3-109">Pourtant, dans un exemple de build représentative, MSBuild générera 262MB d’allocation de `string` à l’aide de commentaires minimes.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-109">Yet in one representative build sample MSBuild will generate 262MB of `string` allocation using minimal verbosity.</span></span> <span data-ttu-id="5f5d3-110">De ces 1/2 de l’allocation sont des allocations éphémères à l’intérieur de `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-110">Of that 1/2 of the allocations are short lived allocations inside `string.Format`.</span></span> <span data-ttu-id="5f5d3-111">Ces fonctionnalités suppriment la majeure partie de ce dernier sur le bureau .NET et l’ont réduit à presque zéro sur .NET Core en raison de la disponibilité des `Span<T>`</span><span class="sxs-lookup"><span data-stu-id="5f5d3-111">These features would remove much of that on .NET Desktop and get it down to nearly zero on .NET Core due to the availability of `Span<T>`</span></span>

<span data-ttu-id="5f5d3-112">L’ensemble des fonctionnalités de langage décrites ici permettra aux applications de continuer à utiliser ces fonctionnalités, avec très peu ou pas d’évolution vers leur base de code d’application, tout en supprimant la surcharge d’allocation imprévue dans la majorité des cas.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-112">The set of language features described here will enable applications to continue using these features, with very little or no churn to their application code base, while removing the unintended allocation overhead in the majority of cases.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="5f5d3-113">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="5f5d3-113">Detailed Design</span></span> 
<span data-ttu-id="5f5d3-114">Il existe un ensemble de fonctionnalités qui seront utilisées ici pour obtenir ces résultats :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-114">There are a set of features that will be used here to achieve these results:</span></span>

- <span data-ttu-id="5f5d3-115">Le développement `params` pour prendre en charge un ensemble plus large de types de collections.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-115">Expanding `params` to support a broader set of collection types.</span></span>
- <span data-ttu-id="5f5d3-116">Permettre aux développeurs de personnaliser la façon dont `string` interpolation est effectuée.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-116">Allowing for developers to customize how `string` interpolation is achieved.</span></span> 
- <span data-ttu-id="5f5d3-117">Autoriser les `string` interpolées à établir une liaison avec des surcharges de `string.Format` plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-117">Allowing for interpolated `string` to bind to more efficient `string.Format` overloads.</span></span>

### <a name="extending-params"></a><span data-ttu-id="5f5d3-118">Extension des paramètres</span><span class="sxs-lookup"><span data-stu-id="5f5d3-118">Extending params</span></span>
<span data-ttu-id="5f5d3-119">Le langage permettra aux `params` dans une signature de méthode d’avoir les types `Span<T>`, `ReadOnlySpan<T>` et `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-119">The language will allow for `params` in a method signature to have the types `Span<T>`, `ReadOnlySpan<T>` and `IEnumerable<T>`.</span></span> <span data-ttu-id="5f5d3-120">Les mêmes règles pour l’appel s’appliqueront à ces nouveaux types qui s’appliquent à `params T[]`:</span><span class="sxs-lookup"><span data-stu-id="5f5d3-120">The same rules for invocation will apply to these new types that apply to `params T[]`:</span></span>

- <span data-ttu-id="5f5d3-121">Impossible de surcharger lorsque la seule différence est un mot clé `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-121">Can't overload where the only difference is a `params` keyword.</span></span>
- <span data-ttu-id="5f5d3-122">Peut appeler en passant une série d’arguments qui sont implicitement convertibles en `T` ou une seule `Span<T>` / 
`ReadOnlySpan<T>` / `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-122">Can invoke by passing a series of arguments that are implicitly convertible to `T` or a single `Span<T>` / 
`ReadOnlySpan<T>` / `IEnumerable<T>` argument.</span></span>
- <span data-ttu-id="5f5d3-123">Doit être le dernier paramètre dans une signature de méthode.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-123">Must be the last parameter in a method signature.</span></span>
- <span data-ttu-id="5f5d3-124">Etc...</span><span class="sxs-lookup"><span data-stu-id="5f5d3-124">Etc ...</span></span> 

<span data-ttu-id="5f5d3-125">Les variantes `Span<T>` et `ReadOnlySpan<T>` sont désignées comme `Span<T>` ci-dessous pour plus de simplicité.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-125">The `Span<T>` and `ReadOnlySpan<T>` variants will be referred to as `Span<T>` below for simplicity.</span></span> <span data-ttu-id="5f5d3-126">Dans les cas où le comportement de `ReadOnlySpan<T>` diffère, il sera explicitement appelé.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-126">In cases where the behavior of `ReadOnlySpan<T>` differs it will be explicitly called out.</span></span> 

<span data-ttu-id="5f5d3-127">L’avantage du `Span<T>` variantes de `params` est qu’il offre au compilateur une grande flexibilité dans la façon dont il alloue le stockage de sauvegarde pour la valeur de `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-127">The advantage the `Span<T>` variants of `params` provides is it gives the compiler great flexibility in how it allocates the backing storage for the `Span<T>` value.</span></span> <span data-ttu-id="5f5d3-128">Avec un `params T[]` le compilateur doit allouer une nouvelle `T[]` pour chaque appel d’une méthode `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-128">With a `params T[]` the compiler must allocate a new `T[]` for every invocation of a `params` method.</span></span> <span data-ttu-id="5f5d3-129">La réutilisation n’est pas possible, car elle doit supposer que l’appelé est stocké et réutilisé le paramètre.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-129">Re-use is not possible because it must assume the callee stored and reused the parameter.</span></span> <span data-ttu-id="5f5d3-130">Cela peut entraîner une forte inefficacité des méthodes avec un grand nombre d’appels de `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-130">This can lead to a large inefficiency in methods with lots of `params` invocations.</span></span>

<span data-ttu-id="5f5d3-131">`Span<T>` variantes spécifiées sont `ref struct` l’appelé ne peut pas stocker l’argument.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-131">Given `Span<T>` variants are `ref struct` the callee cannot store the argument.</span></span> <span data-ttu-id="5f5d3-132">Par conséquent, le compilateur peut optimiser les sites d’appel en effectuant des actions telles que la réutilisation de l’argument.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-132">Hence the compiler can optimize the call sites by taking actions like re-using the argument.</span></span> <span data-ttu-id="5f5d3-133">Cela peut rendre les appels répétés très efficaces par rapport à `T[]`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-133">This can make repeated invocations very efficient as compared to `T[]`.</span></span> <span data-ttu-id="5f5d3-134">Toutefois, le langage n’apporte aucune garantie spécifique quant à l’optimisation de ces sites DLR.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-134">The language though will make no specific guarantees about how such callsites are optimized.</span></span> <span data-ttu-id="5f5d3-135">Notez uniquement que le compilateur est libre d’utiliser des valeurs autres que `T[]` lors de l’appel d’une méthode `params Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-135">Only note that the compiler is free to use values other than `T[]` when invoking a `params Span<T>` method.</span></span> 

<span data-ttu-id="5f5d3-136">Une telle implémentation potentielle est la suivante.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-136">One such potential implementation is the following.</span></span> <span data-ttu-id="5f5d3-137">Examinez tous les `params` appel dans un corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-137">Consider all `params` invocation in a method body.</span></span> <span data-ttu-id="5f5d3-138">Le compilateur peut allouer un tableau dont la taille est égale à la plus grande `params` appel et l’utilise pour tous les appels en créant des instances de `Span<T>` de taille appropriée sur le tableau.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-138">The compiler could allocate an array which has a size equal to the largest `params` invocation and use that for all of the invocations by creating appropriately sized `Span<T>` instances over the array.</span></span> <span data-ttu-id="5f5d3-139">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-139">For example:</span></span>

``` csharp
static class OneAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("jaredpar");
        Use("hello", "world");
        Use("a", "longer", "set");
    }
}
```

<span data-ttu-id="5f5d3-140">Le compilateur peut choisir d’émettre le corps de `Go` comme suit :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-140">The compiler could choose to emit the body of `Go` as follows:</span></span>

``` csharp
    static void Go() {
        var args = new string[3];
        args[0] = "jaredpar";
        Use(new Span<string>(args, start: 0, length: 1));

        args[0] = "hello";
        args[1] = "world";
        Use(new Span<string>(args, start: 0, length: 2));

        args[0] = "a";
        args[1] = "longer";
        args[2] = "set";
        Use(new Span<string>(args, start: 0, length: 3));
   }
```

<span data-ttu-id="5f5d3-141">Cela peut réduire considérablement le nombre de tableaux alloués dans une application.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-141">This can significantly reduce the number of arrays allocated in an application.</span></span> <span data-ttu-id="5f5d3-142">Les allocations peuvent être encore plus réduites si le Runtime fournit des utilitaires pour l’allocation de piles plus intelligente des tableaux.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-142">Allocations can be even further reduced if the runtime provides utilities for smarter stack allocation of arrays.</span></span>

<span data-ttu-id="5f5d3-143">Cette optimisation ne peut cependant pas toujours être appliquée.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-143">This optimization cannot always be applied though.</span></span> <span data-ttu-id="5f5d3-144">Même si l’appelé ne peut pas capturer l’argument `params` il peut toujours être capturé dans l’appelant lorsqu’il existe un paramètre `ref` ou `out / ref` qui est lui-même un type `ref struct`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-144">Even though the callee cannot capture the `params` argument it can still be captured in the caller when there is a `ref` or a `out / ref` parameter that is itself a `ref struct` type.</span></span> 

``` csharp
static class SneakyCapture {
    static ref int M(params Span<T> span) => ref span[0];

    static void Oops() {
        // This now holds onto the memory backing the Span<T> 
        ref int r = ref M(42);
    }
}
```

<span data-ttu-id="5f5d3-145">Toutefois, ces cas sont détectables de manière statique.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-145">These cases are statically detectable though.</span></span> <span data-ttu-id="5f5d3-146">Il se produit potentiellement lorsqu’il existe un retour `ref` ou un paramètre `ref struct` passé par `out` ou `ref`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-146">It potentially occurs whenever there is a `ref` return or a `ref struct` parameter passed by `out` or `ref`.</span></span> <span data-ttu-id="5f5d3-147">Dans ce cas, le compilateur doit allouer une nouvelle `T[]` pour chaque appel.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-147">In such a case the compiler must allocate a fresh `T[]` for every invocation.</span></span> 

<span data-ttu-id="5f5d3-148">Plusieurs autres stratégies d’optimisation potentielles sont décrites à la fin de ce document.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-148">Several other potential optimization strategies are discussed at the end of this document.</span></span>

<span data-ttu-id="5f5d3-149">Le `IEnumerable<T>` variant est une simple surcharge pratique.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-149">The `IEnumerable<T>` variant is a merely a convenience overload.</span></span> <span data-ttu-id="5f5d3-150">Elle est utile dans les scénarios qui utilisent fréquemment des `IEnumerable<T>`, mais qui ont également un grand nombre d’utilisations `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-150">It's useful in scenarios which have frequent uses of `IEnumerable<T>` but also have lots of `params` usage.</span></span> <span data-ttu-id="5f5d3-151">Lorsqu’elle est appelée dans `T` argument, le stockage de sauvegarde est alloué en tant que `T[]` de la même façon que `params T[]` est effectué aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-151">When invoked in `T` argument form the backing storage will be allocated as a `T[]` just as `params T[]` is done today.</span></span>

### <a name="params-overload-resolution-changes"></a><span data-ttu-id="5f5d3-152">modifications de résolution de surcharge des paramètres</span><span class="sxs-lookup"><span data-stu-id="5f5d3-152">params overload resolution changes</span></span>
<span data-ttu-id="5f5d3-153">Cette proposition signifie que la langue présente désormais quatre variantes de `params`, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-153">This proposal means the language now has four variants of `params` where before it had one.</span></span> <span data-ttu-id="5f5d3-154">Il est raisonnable pour les méthodes de définir des surcharges de méthodes qui diffèrent uniquement par le type d’une `params` déclarations.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-154">It is sensible for methods to define overloads of methods that differ only on the type of a `params` declarations.</span></span> 

<span data-ttu-id="5f5d3-155">Tenez compte du fait que `StringBuilder.AppendFormat` ajouterait certainement une surcharge de `params ReadOnlySpan<object>` en plus de la `params object[]`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-155">Consider that `StringBuilder.AppendFormat` would certainly add a `params ReadOnlySpan<object>` overload in addition to the `params object[]`.</span></span> <span data-ttu-id="5f5d3-156">Cela lui permettrait d’améliorer considérablement les performances en réduisant les allocations de collection sans avoir à modifier le code appelant.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-156">This would allow it to substantially improve performance by reducing collection allocations without requiring any changes to the calling code.</span></span> 

<span data-ttu-id="5f5d3-157">Pour faciliter cette opération, le langage introduira la règle suivante de rupture de la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-157">To facilitate this the language will introduce the following overload resolution tie breaking rule.</span></span> <span data-ttu-id="5f5d3-158">Lorsque les méthodes candidates diffèrent uniquement par le paramètre `params`, les candidats sont préférés dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-158">When the candidate methods differ only by the `params` parameter then the candidates will be preferred in the following order:</span></span>

1. `ReadOnlySpan<T>`
1. `Span<T>`
1. `T[]`
1. `IEnumerable<T>`

<span data-ttu-id="5f5d3-159">Cet ordre est le plus efficace pour le cas général.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-159">This order is the most to the least efficient for the general case.</span></span>

### <a name="variant"></a><span data-ttu-id="5f5d3-160">Variant</span><span class="sxs-lookup"><span data-stu-id="5f5d3-160">Variant</span></span>
<span data-ttu-id="5f5d3-161">CoreFX prototype un nouveau type géré nommé [Variant](https://github.com/dotnet/corefxlab/pull/2595).</span><span class="sxs-lookup"><span data-stu-id="5f5d3-161">CoreFX is prototyping a new managed type named [Variant](https://github.com/dotnet/corefxlab/pull/2595).</span></span> <span data-ttu-id="5f5d3-162">Ce type est destiné à être utilisé dans les API qui attendent des valeurs hétérogènes, mais ne souhaitent pas que la surcharge soit mise en oeuvre en utilisant `object` comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-162">This type is meant to be used in APIs which expect heterogeneous values but don't want the overhead brought on by using `object` as the parameter.</span></span> <span data-ttu-id="5f5d3-163">Le type de `Variant` fournit le stockage universel, mais évite l’allocation Boxing pour les types les plus couramment utilisés.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-163">The `Variant` type provides universal storage but avoids the boxing allocation for the most commonly used types.</span></span> <span data-ttu-id="5f5d3-164">L’utilisation de ce type dans des API comme `string.Format` peut éliminer la surcharge de boxing dans la majorité des cas.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-164">Using this type in APIs like `string.Format` can eliminate the boxing overhead in the majority of cases.</span></span>

<span data-ttu-id="5f5d3-165">Ce type lui-même n’est pas nécessairement spécial pour le langage.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-165">This type itself is not necessarily special to the language.</span></span> <span data-ttu-id="5f5d3-166">Il est présenté dans ce document séparément, même s’il devient un détail d’implémentation d’autres parties de la proposition.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-166">It is being introduced in this document separately though as it becomes an implementation detail of other parts of the proposal.</span></span> 

### <a name="efficient-interpolated-strings"></a><span data-ttu-id="5f5d3-167">Chaînes interpolées efficaces</span><span class="sxs-lookup"><span data-stu-id="5f5d3-167">Efficient interpolated strings</span></span>
<span data-ttu-id="5f5d3-168">Les chaînes interpolées sont une fonctionnalité courante mais inefficace C#dans.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-168">Interpolated strings are a popular yet inefficient feature in C#.</span></span> <span data-ttu-id="5f5d3-169">La syntaxe la plus courante, utilisant une `string` interpolée en tant que `string`, se traduit par un appel de `string.Format(string, params object[])`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-169">The most common syntax, using an interpolated `string` as a `string`, translates into a `string.Format(string, params object[])` call.</span></span> <span data-ttu-id="5f5d3-170">Cela entraînera des allocations Boxing pour tous les types de valeur, les allocations de `string` intermédiaires, car l’implémentation utilise principalement `object.ToString` pour la mise en forme, ainsi que les allocations de tableau une fois que le nombre d’arguments dépasse la quantité de paramètres sur les surcharges « rapides » de `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-170">That will incur boxing allocations for all value types, intermediate `string` allocations as the implementation largely uses `object.ToString` for formatting as well as array allocations once the number of arguments exceeds the amount of parameters on the "fast" overloads of `string.Format`.</span></span> 

<span data-ttu-id="5f5d3-171">La langue modifie sa diminution de l’interpolation pour tenir compte des surcharges alternatives de `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-171">The language will change its interpolation lowering to consider alternate overloads of `string.Format`.</span></span> <span data-ttu-id="5f5d3-172">Elle prend en compte toutes les formes de `string.Format(string, params)` et sélectionne la « meilleure » surcharge qui satisfait les types d’arguments.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-172">It will consider all forms of `string.Format(string, params)` and pick the "best" overload which satisfies the argument types.</span></span>
<span data-ttu-id="5f5d3-173">La surcharge de `params` optimale sera déterminée par les règles décrites ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-173">The "best" `params` overload will be determined by the rules discussed above.</span></span> <span data-ttu-id="5f5d3-174">Cela signifie que les `string` interpolées peuvent désormais être liées à des surcharges très efficaces comme `string.Format(string format, params ReadOnlySpan<Variant> args)`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-174">This means interpolated `string` can now bind to very efficient overloads like `string.Format(string format, params ReadOnlySpan<Variant> args)`.</span></span> <span data-ttu-id="5f5d3-175">Dans de nombreux cas, cela entraîne la suppression de toutes les allocations intermédiaires.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-175">In many cases this will remove all intermediate allocations.</span></span>

### <a name="customizable-interpolated-strings"></a><span data-ttu-id="5f5d3-176">Chaînes interpolées personnalisables</span><span class="sxs-lookup"><span data-stu-id="5f5d3-176">Customizable interpolated strings</span></span>
<span data-ttu-id="5f5d3-177">Les développeurs peuvent personnaliser le comportement des chaînes interpolées avec `FormattableString`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-177">Developers are able to customize the behavior of interpolated strings with `FormattableString`.</span></span> <span data-ttu-id="5f5d3-178">Contient les données qui sont placées dans une chaîne interpolée : le format `string` et les arguments en tant que tableau.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-178">This contains the data which goes into an interpolated string: the format `string` and the arguments as an array.</span></span> <span data-ttu-id="5f5d3-179">Cela conserve toujours l’allocation de tableau boxing et d’arguments, ainsi que l’allocation pour `FormattableString` (il s’agit d’un `abstract class`).</span><span class="sxs-lookup"><span data-stu-id="5f5d3-179">This though still has the boxing and argument array allocation as well as the allocation for `FormattableString` (it's an `abstract class`).</span></span> <span data-ttu-id="5f5d3-180">Il s’agit donc d’une utilisation minime des applications qui sont lourdes dans la mise en forme `string`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-180">Hence it's of little use to applications which are allocation heavy in `string` formatting.</span></span>

<span data-ttu-id="5f5d3-181">Pour améliorer l’efficacité de la mise en forme des chaînes interpolées, le langage reconnaîtra un nouveau type : `System.ValueFormattableString`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-181">To make interpolated string formatting efficient the language will recognize a new type: `System.ValueFormattableString`.</span></span> <span data-ttu-id="5f5d3-182">Toutes les chaînes interpolées auront une conversion de type cible en ce type.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-182">All interpolated strings will have a target type conversion to this type.</span></span> <span data-ttu-id="5f5d3-183">Cette opération sera implémentée en traduisant la chaîne interpolée dans l’appel `ValueFormattableString.Create` exactement comme pour `FormattableString.Create` aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-183">This will be implemented by translating the interpolated string into the call `ValueFormattableString.Create` exactly as is done for `FormattableString.Create` today.</span></span> <span data-ttu-id="5f5d3-184">Le langage prend en charge toutes les options de `params` décrites dans ce document lors de la recherche de la méthode de `ValueFormattableString.Create` la plus appropriée.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-184">The language will support all `params` options described in this document when looking for the most suitable `ValueFormattableString.Create` method.</span></span> 

``` csharp
readonly struct ValueFormattableString {
    public static ValueFormattableString Create(Variant v) { ... } 
    public static ValueFormattableString Create(string s) { ... } 
    public static ValueFormattableString Create(string s, params ReadOnlySpan<Variant> collection) { ... } 
}

class ConsoleEx { 
    static void Write(ValueFormattableString f) { ... }
}

class Program { 
    static void Main() { 
        ConsoleEx.Write(42);
        ConsoleEx.Write($"hello {DateTime.UtcNow}");

        // Translates into 
        ConsoleEx.Write(ValueFormattableString.Create((Variant)42));
        ConsoleEx.Write(ValueFormattableString.Create(
            "hello {0}", 
            new Variant(DateTime.UtcNow));
    }
}
```

<span data-ttu-id="5f5d3-185">Les règles de résolution de surcharge seront modifiées pour préférer `ValueFormattableString` sur `string` lorsque l’argument est une chaîne interpolée.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-185">Overload resolution rules will be changed to prefer `ValueFormattableString` over `string` when the argument is an interpolated string.</span></span> <span data-ttu-id="5f5d3-186">Cela signifie qu’il est utile d’avoir des surcharges qui diffèrent uniquement sur `string` et `ValueFormattableString`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-186">This means it will be valuable to have overloads which differ only on `string` and `ValueFormattableString`.</span></span> <span data-ttu-id="5f5d3-187">Une telle surcharge aujourd’hui avec `FormattableString` n’est pas utile, car le compilateur préfèrera toujours la version `string` (sauf si le développeur utilise un cast explicite).</span><span class="sxs-lookup"><span data-stu-id="5f5d3-187">Such an overload today with `FormattableString` is not valuable as the compiler will always prefer the `string` version (unless the developer uses an explicit cast).</span></span> 

## <a name="open-issues"></a><span data-ttu-id="5f5d3-188">Problèmes ouverts</span><span class="sxs-lookup"><span data-stu-id="5f5d3-188">Open Issues</span></span>

### <a name="valueformattablestring-breaking-change"></a><span data-ttu-id="5f5d3-189">Modification avec rupture ValueFormattableString</span><span class="sxs-lookup"><span data-stu-id="5f5d3-189">ValueFormattableString breaking change</span></span>
<span data-ttu-id="5f5d3-190">La modification à préférer `ValueFormattableString` lors de la résolution de surcharge sur `string` est une modification avec rupture.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-190">The change to prefer `ValueFormattableString` during overload resolution over `string` is a breaking change.</span></span> <span data-ttu-id="5f5d3-191">Il est possible pour un développeur d’avoir défini un type appelé `ValueFormattableString` aujourd’hui et de l’utiliser dans des surcharges de méthode avec `string`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-191">It is possible for a developer to have defined a type called `ValueFormattableString` today and use it in method overloads with `string`.</span></span> <span data-ttu-id="5f5d3-192">Cette modification proposée ferait en sorte que le compilateur sélectionne une surcharge différente une fois que cet ensemble de fonctionnalités a été implémenté.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-192">This proposed change would cause the compiler to pick a different overload once this set of features was implemented.</span></span> 

<span data-ttu-id="5f5d3-193">La possibilité de cela semble relativement faible.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-193">The possibility of this seems reasonably low.</span></span> <span data-ttu-id="5f5d3-194">Le type a besoin du nom complet `System.ValueFormattableString` et il doit avoir `static` méthodes nommées `Create`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-194">The type would need the full name `System.ValueFormattableString` and it would need to have `static` methods named `Create`.</span></span> <span data-ttu-id="5f5d3-195">Étant donné que les développeurs sont fortement déconseillés de définir n’importe quel type dans l’espace de noms `System`, cet arrêt semble être un compromis raisonnable.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-195">Given that developers are strongly discouraged from defining any type in the `System` namespace this break seems like a reasonable compromise.</span></span>

### <a name="expanding-to-more-types"></a><span data-ttu-id="5f5d3-196">Développement d’un plus grand nombre de types</span><span class="sxs-lookup"><span data-stu-id="5f5d3-196">Expanding to more types</span></span>
<span data-ttu-id="5f5d3-197">Étant donné que nous sommes dans le domaine, nous devons envisager d’ajouter `IList<T>`, `ICollection<T>` et `IReadOnlyList<T>` à l’ensemble des collections pour lesquelles `params` est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-197">Given we're in the area we should consider adding `IList<T>`, `ICollection<T>` and `IReadOnlyList<T>` to the set of collections for which `params` is supported.</span></span> <span data-ttu-id="5f5d3-198">En termes d’implémentation, le coût est réduit sur l’autre travail ici.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-198">In terms of implementation it will cost a small amount over the other work here.</span></span>

<span data-ttu-id="5f5d3-199">LDM doit décider si la complication du langage en est la plus intéressante.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-199">LDM needs to decide if the complication to the language is worth it though.</span></span> <span data-ttu-id="5f5d3-200">L’ajout de `IEnumerable<T>` supprime un point de frottement très spécifique.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-200">The addition of `IEnumerable<T>` removes a very specific friction point.</span></span> <span data-ttu-id="5f5d3-201">L’absence de cette `params` solution de nombreux clients étaient imposés à allouer des `T[]` à partir d’un `IEnumerable<T>` lors de l’appel d’une méthode `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-201">Lacking this `params` solution many customers were forced to allocate `T[]` from an `IEnumerable<T>` when calling a `params` method.</span></span> <span data-ttu-id="5f5d3-202">Toutefois, l’ajout de `IEnumerable<T>` le corrige.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-202">The addition of `IEnumerable<T>` fixes this though.</span></span> <span data-ttu-id="5f5d3-203">Il n’existe pas de point de friction spécifique résolu ici par les autres interfaces.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-203">There is no specific friction point that the other interfaces fix here.</span></span> 

## <a name="considerations"></a><span data-ttu-id="5f5d3-204">Considérations</span><span class="sxs-lookup"><span data-stu-id="5f5d3-204">Considerations</span></span>

### <a name="variant2-and-variant3"></a><span data-ttu-id="5f5d3-205">Variant2 et Variant3</span><span class="sxs-lookup"><span data-stu-id="5f5d3-205">Variant2 and Variant3</span></span>
<span data-ttu-id="5f5d3-206">L’équipe CoreFX dispose également d’un ensemble de types de stockage qui ne permet pas d’allouer jusqu’à trois arguments `Variant`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-206">The CoreFX team also has a non-allocating set of storage types for up to three `Variant` arguments.</span></span> <span data-ttu-id="5f5d3-207">Il s’agit d’un `Variant`unique, `Variant2` et `Variant3`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-207">These are a single `Variant`, `Variant2` and `Variant3`.</span></span> <span data-ttu-id="5f5d3-208">Tous ont une paire de méthodes permettant d’obtenir une allocation libre `Span<Variant>` de l’être : `CreateSpan` et `KeepAlive`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-208">All have a pair of methods for getting an allocation free `Span<Variant>` off of them: `CreateSpan` and `KeepAlive`.</span></span> <span data-ttu-id="5f5d3-209">Cela signifie que pour un `params Span<Variant>` de trois arguments au maximum, le site d’appel peut être entièrement alloué gratuitement.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-209">This means for a `params Span<Variant>` of up to three arguments the call site can be entirely allocation free.</span></span>

``` csharp
static class ZeroAllocation {
    static void Use(params Span<Variant> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

<span data-ttu-id="5f5d3-210">La méthode `Go` peut être abaissée aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-210">The `Go` method can be lowered to the following:</span></span>

``` csharp
static class ZeroAllocation {
    static void Go() {
        Variant2 _v;
        _v.Variant1 = new Variant("hello");
        _v.Variant2 = new Variant("word");
        Use(_v.CreateSpan());
        _v.KeepAlive();
    }
}
```

<span data-ttu-id="5f5d3-211">Cela nécessite très peu de travail au-dessus de la proposition pour réutiliser `T[]` entre les appels `params Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-211">This requires very little work on top of the proposal to re-use `T[]` between `params Span<T>` calls.</span></span> <span data-ttu-id="5f5d3-212">Le compilateur a déjà besoin de gérer un temporaire par appel et de nettoyer le travail après (même si, dans un cas, il s’agit simplement de marquer un temp interne comme libre).</span><span class="sxs-lookup"><span data-stu-id="5f5d3-212">The compiler already needs to manage a temporary per call and do clean up work after (even if in one case it's just marking an internal temp as free).</span></span> 

<span data-ttu-id="5f5d3-213">Remarque : la fonction `KeepAlive` est uniquement nécessaire sur le bureau.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-213">Note: the `KeepAlive` function is only necessary on desktop.</span></span> <span data-ttu-id="5f5d3-214">Sur .NET Core, la méthode n’est pas disponible et, par conséquent, le compilateur n’émet pas d’appel à celui-ci.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-214">On .NET Core the method will not be available and hence the compiler won't emit a call to it.</span></span>

### <a name="clr-stack-allocation-helpers"></a><span data-ttu-id="5f5d3-215">Applications d’assistance de l’allocation de pile CLR</span><span class="sxs-lookup"><span data-stu-id="5f5d3-215">CLR stack allocation helpers</span></span>
<span data-ttu-id="5f5d3-216">Le CLR fournit uniquement [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) pour l’allocation de pile de mémoire contiguë.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-216">The CLR only provides only [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) for stack allocation of contiguous memory.</span></span> <span data-ttu-id="5f5d3-217">Cette instruction est limitée en ce qu’elle fonctionne uniquement pour les types de `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-217">This instruction is limited in that it only works for `unmanaged` types.</span></span> <span data-ttu-id="5f5d3-218">Cela signifie qu’il ne peut pas être utilisé comme solution universelle pour allouer efficacement le stockage de sauvegarde pour `params 
 Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-218">This means it can't be used as a universal solution for efficiently allocating the backing storage for `params 
 Span<T>`.</span></span> 

<span data-ttu-id="5f5d3-219">Toutefois, cette limitation n’est pas une restriction fondamentale, mais plus un artefact de l’historique.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-219">This limitation is not some fundamental restriction though but instead more an artifact of history.</span></span> <span data-ttu-id="5f5d3-220">Le CLR peut choisir d’ajouter de nouveaux codes d’opération/intrinsèques qui fournissent une allocation de pile universelle.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-220">The CLR could choose to add new op codes / intrinsics which provide universal stack allocation.</span></span> <span data-ttu-id="5f5d3-221">Elles peuvent ensuite être utilisées pour allouer le stockage de sauvegarde pour la plupart des appels de `params Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-221">These could then be used to allocate the backing storage for most `params Span<T>` calls.</span></span>

``` csharp
static class BetterAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

<span data-ttu-id="5f5d3-222">La méthode `Go` peut être abaissée aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-222">The `Go` method can be lowered to the following:</span></span>

``` csharp
static class ZeroAllocation {
    static void Go() {
        Span<T> span = RuntimeIntrinsic.StackAlloc<string>(length: 2);
        span[0] = "hello";
        span[1] = "world";
        Use(span);
    }
}
```

<span data-ttu-id="5f5d3-223">Bien que cette approche soit très efficace dans le segment de mémoire, elle entraîne une utilisation supplémentaire de la pile.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-223">While this approach is very heap efficient it does cause extra stack usage.</span></span> <span data-ttu-id="5f5d3-224">Dans un algorithme qui a une pile profonde et un grand nombre d’utilisations `params`, cela peut provoquer la génération d’une `StackOverflowException` où une allocation de `T[]` simple aboutirait.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-224">In an algorithm which has a deep stack and lots of `params` usage it's possible this could cause a `StackOverflowException` to be generated where a simple `T[]` allocation would succeed.</span></span> 

<span data-ttu-id="5f5d3-225">Malheureusement C# , il n’est pas possible de configurer le type d’analyse inter-méthode, où il pourrait déterminer si l’appel doit ou non utiliser l’allocation de pile ou de tas de `params`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-225">Unfortunately C# is not set up for the type of inter-method analysis where it could make an educated determination of whether or not call should use stack or heap allocation of `params`.</span></span> <span data-ttu-id="5f5d3-226">Il peut uniquement considérer chaque appel seul.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-226">It can only really consider each call on its own.</span></span>

<span data-ttu-id="5f5d3-227">Le CLR est la meilleure configuration pour effectuer ce type de détermination au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-227">The CLR is best setup for making this type of determination at runtime.</span></span> <span data-ttu-id="5f5d3-228">Par conséquent, il est probable que le runtime fournisse deux méthodes pour l’allocation de la pile universelle :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-228">Hence we'd likely have the runtime provide two methods for universal stack allocation:</span></span>

1. <span data-ttu-id="5f5d3-229">`Span<T> StackAlloc<T>(int length)`: cela a les mêmes comportements et limitations que `localloc`, sauf qu’elle peut fonctionner sur n’importe quel type `T`.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-229">`Span<T> StackAlloc<T>(int length)`: this has the same behaviors and limitations of `localloc` except it can work on any type `T`.</span></span> 
1. <span data-ttu-id="5f5d3-230">`Span<T> MaybeStackAlloc<T>(int length)`: ce Runtime peut choisir d’implémenter cela en procédant à une allocation de pile ou de tas.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-230">`Span<T> MaybeStackAlloc<T>(int length)`: this runtime can choose to implement this by doing a stack or heap allocation.</span></span> <span data-ttu-id="5f5d3-231">Le runtime peut ensuite utiliser le contexte d’exécution dans lequel il est appelé pour déterminer la façon dont le `Span<T>` est alloué.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-231">The runtime can then use the execution context in which it's called to determine how the `Span<T>` is allocated.</span></span> <span data-ttu-id="5f5d3-232">Toutefois, l’appelant le traite toujours comme s’il était alloué à la pile.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-232">The caller though will always treat it as if it were stack allocated.</span></span>

<span data-ttu-id="5f5d3-233">Pour les cas très simples, comme un à deux arguments, C# le compilateur peut toujours utiliser `StackAlloc<T>` variant.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-233">For very simple cases, like one to two arguments, the C# compiler could always use `StackAlloc<T>` variant.</span></span> <span data-ttu-id="5f5d3-234">Il est peu probable que cela contribue à réduire l’épuisement des piles dans la plupart des cas.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-234">This is unlikely to significantly contribute to stack exhaustion in most cases.</span></span> <span data-ttu-id="5f5d3-235">Dans d’autres cas, le compilateur peut choisir d’utiliser `MaybeStackAlloc<T>` à la place et laisser le runtime effectuer l’appel.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-235">For other cases the compiler could choose to use `MaybeStackAlloc<T>` instead and let the runtime make the call.</span></span>

<span data-ttu-id="5f5d3-236">La façon dont nous choisissons probablement une investigation plus approfondie et l’examen des applications réelles.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-236">How we choose will likely require a deeper investigation and examination of real world apps.</span></span> <span data-ttu-id="5f5d3-237">Toutefois, si ces nouvelles fonctions intrinsèques sont disponibles, elles nous offrent ce type de flexibilité.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-237">But if these new intrinsics are available then it will give us this type of flexibility.</span></span>

### <a name="why-not-varargs"></a><span data-ttu-id="5f5d3-238">Pourquoi ne pas varargs ?</span><span class="sxs-lookup"><span data-stu-id="5f5d3-238">Why not varargs?</span></span> 
<span data-ttu-id="5f5d3-239">La fonctionnalité [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) existante a été considérée comme étant une solution possible.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-239">The existing [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) feature was considered here as a possible solution.</span></span> <span data-ttu-id="5f5d3-240">Toutefois, cette fonctionnalité est principalement destinée C++aux scénarios/CLI et a des trous connus pour d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-240">This feature though is meant primarily for C++/CLI scenarios and has known holes for other scenarios.</span></span> <span data-ttu-id="5f5d3-241">En outre, le portage vers UNIX présente un coût considérable.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-241">Additionally there is significant cost in porting this to Unix.</span></span> <span data-ttu-id="5f5d3-242">Par conséquent, il n’a pas été considéré comme une solution viable.</span><span class="sxs-lookup"><span data-stu-id="5f5d3-242">Hence it wasn't seen as a viable solution.</span></span>

## <a name="related-issues"></a><span data-ttu-id="5f5d3-243">Problèmes connexes</span><span class="sxs-lookup"><span data-stu-id="5f5d3-243">Related Issues</span></span>
<span data-ttu-id="5f5d3-244">Cette spécification est liée aux problèmes suivants :</span><span class="sxs-lookup"><span data-stu-id="5f5d3-244">This spec is related to the following issues:</span></span> 

- https://github.com/dotnet/csharplang/issues/1757
- https://github.com/dotnet/csharplang/issues/179
- https://github.com/dotnet/corefxlab/pull/2595

