---
ms.openlocfilehash: 76065293f652979ab395e131d657e44899c5a65b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484551"
---
# <a name="simplified-null-argument-checking"></a><span data-ttu-id="80830-101">Vérification simplifiée des arguments null</span><span class="sxs-lookup"><span data-stu-id="80830-101">Simplified Null Argument Checking</span></span>

## <a name="summary"></a><span data-ttu-id="80830-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="80830-102">Summary</span></span>
<span data-ttu-id="80830-103">Cette proposition fournit une syntaxe simplifiée pour la validation des arguments de méthode qui ne sont pas `null` et lèvent `ArgumentNullException` de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="80830-103">This proposal provides a simplified syntax for validating method arguments are not `null` and throwing `ArgumentNullException` appropriately.</span></span>

## <a name="motivation"></a><span data-ttu-id="80830-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="80830-104">Motivation</span></span>
<span data-ttu-id="80830-105">L’utilisation de la conception de types référence Nullable nous a permis d’examiner le code nécessaire à la validation de l’argument `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-105">The work on designing nullable reference types has caused us to examine the code necessary for `null` argument validation.</span></span> <span data-ttu-id="80830-106">Étant donné que Diagnostics proactifs NRT n’affecte pas l’exécution du code, les développeurs doivent toujours ajouter `if (arg is null) throw` code de plaque de chaudière, même dans des projets entièrement `null` propres.</span><span class="sxs-lookup"><span data-stu-id="80830-106">Given that NRT doesn't affect code execution developers still must add `if (arg is null) throw` boiler plate code even in projects which are fully `null` clean.</span></span> <span data-ttu-id="80830-107">Cela nous a permis d’explorer une syntaxe minimale pour l’argument `null` la validation dans le langage.</span><span class="sxs-lookup"><span data-stu-id="80830-107">This gave us the desire to explore a minimal syntax for argument `null` validation in the language.</span></span> 

<span data-ttu-id="80830-108">Bien que cette `null` syntaxe de validation de paramètre soit censée être couplée fréquemment avec Diagnostics proactifs NRT, la proposition est entièrement indépendante de celle-ci.</span><span class="sxs-lookup"><span data-stu-id="80830-108">While this `null` parameter validation syntax is expected to pair frequently with NRT the proposal is fully independent of it.</span></span> <span data-ttu-id="80830-109">La syntaxe peut être utilisée indépendamment des directives de `#nullable`.</span><span class="sxs-lookup"><span data-stu-id="80830-109">The syntax can be used independent of `#nullable` directives.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="80830-110">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="80830-110">Detailed Design</span></span> 

### <a name="null-validation-parameter-syntax"></a><span data-ttu-id="80830-111">Syntaxe des paramètres de validation null</span><span class="sxs-lookup"><span data-stu-id="80830-111">Null validation parameter syntax</span></span>
<span data-ttu-id="80830-112">L’opérateur Bang, `!`, peut être positionné après un nom de paramètre dans une liste de paramètres, ce qui C# entraîne l’émission par le compilateur d’un code de vérification de `null` standard pour ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="80830-112">The bang operator, `!`, can be positioned after a parameter name in a parameter list and this will cause the C# compiler to emit standard `null` checking code for that parameter.</span></span> <span data-ttu-id="80830-113">C’est ce qu’on appelle la syntaxe de paramètre de validation `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-113">This is referred to as `null` validation parameter syntax.</span></span> <span data-ttu-id="80830-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="80830-114">For example:</span></span>

``` csharp
void M(string name!) {
    ...
}
```

<span data-ttu-id="80830-115">Seront traduites dans :</span><span class="sxs-lookup"><span data-stu-id="80830-115">Will be translated into:</span></span>

``` csharp
void M(string name) {
    if (name is null) {
        throw new ArgumentNullException(nameof(name));
    }
    ...
}
```

<span data-ttu-id="80830-116">La vérification de `null` générée se produit avant tout code créé par le développeur dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="80830-116">The generated `null` check will occur before any developer authored code in the method.</span></span> <span data-ttu-id="80830-117">Lorsque plusieurs paramètres contiennent l’opérateur `!`, les contrôles sont effectués dans le même ordre que celui où les paramètres sont déclarés.</span><span class="sxs-lookup"><span data-stu-id="80830-117">When multiple parameters contain the `!` operator then the checks will occur in the same order as the parameters are declared.</span></span>

``` csharp
void M(string p1, string p2) {
    if (p1 is null) {
        throw new ArgumentNullException(nameof(p1));
    }
    if (p2 is null) {
        throw new ArgumentNullException(nameof(p2));
    }
    ...
}
```

<span data-ttu-id="80830-118">La vérification concernera spécifiquement l’égalité des références pour `null`, elle n’appelle pas `==` ou n’importe quel opérateur défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="80830-118">The check will be specifically for reference equality to `null`, it does not invoke `==` or any user defined operators.</span></span> <span data-ttu-id="80830-119">Cela signifie également que l’opérateur `!` peut uniquement être ajouté aux paramètres dont le type peut être testé pour l’égalité par rapport à `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-119">This also means the `!` operator can only be added to parameters whose type can be tested for equality against `null`.</span></span> <span data-ttu-id="80830-120">Cela signifie qu’il ne peut pas être utilisé sur un paramètre dont le type est connu comme étant un type valeur.</span><span class="sxs-lookup"><span data-stu-id="80830-120">This means it can't be used on a parameter whose type is known to be a value type.</span></span>

``` csharp
// Error: Cannot use ! on parameters who types derive from System.ValueType
void G<T>(T arg!) where T : struct {

}
```

<span data-ttu-id="80830-121">Dans le cas d’un constructeur, la validation `null` se produit avant tout autre code dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="80830-121">In the case of a constructor the `null` validation will occur before any other code in the constructor.</span></span> <span data-ttu-id="80830-122">Cela comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="80830-122">That includes:</span></span> 

- <span data-ttu-id="80830-123">Chaînage à d’autres constructeurs avec `this` ou `base`</span><span class="sxs-lookup"><span data-stu-id="80830-123">Chaining to other constructors with `this` or `base`</span></span> 
- <span data-ttu-id="80830-124">Initialiseurs de champ qui se produisent implicitement dans le constructeur</span><span class="sxs-lookup"><span data-stu-id="80830-124">Field initializers which implicitly occur in the constructor</span></span>

<span data-ttu-id="80830-125">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="80830-125">For example:</span></span>

``` csharp
class C {
    string field = GetString();
    C(string name!): this(name) {
        ...
    }
}
```

<span data-ttu-id="80830-126">Seront traduits approximativement de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="80830-126">Will be roughly translated into the following:</span></span>

``` csharp
class C {
    C(string name)
        if (name is null) {
            throw new ArgumentNullException(nameof(name));
        }
        field = GetString();
        :this(name);
        ...
}
```

<span data-ttu-id="80830-127">Remarque : il ne s’agit C# pas d’un code légal, mais à la place d’une approximation de ce que fait l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="80830-127">Note: this is not legal C# code but instead just an approximation of what the implementation does.</span></span> 

<span data-ttu-id="80830-128">La syntaxe du paramètre de validation `null` est également valide dans les listes de paramètres lambda.</span><span class="sxs-lookup"><span data-stu-id="80830-128">The `null` validation parameter syntax will also be valid on lambda parameter lists.</span></span> <span data-ttu-id="80830-129">Cela est valide même dans la syntaxe de paramètre unique qui n’a pas de parenthèses.</span><span class="sxs-lookup"><span data-stu-id="80830-129">This is valid even in the single parameter syntax that lacks parens.</span></span>

``` csharp
void G() {
    // An identity lambda which throws on a null input
    Func<string, string> s = x! => x;
}
```

<span data-ttu-id="80830-130">La syntaxe est également valide sur les paramètres des méthodes d’itérateur.</span><span class="sxs-lookup"><span data-stu-id="80830-130">The syntax is also valid on parameters to iterator methods.</span></span> <span data-ttu-id="80830-131">Contrairement à l’autre code de l’itérateur, la validation `null` se produit lorsque la méthode Iterator est appelée, et non lorsque l’énumérateur sous-jacent est parcouru.</span><span class="sxs-lookup"><span data-stu-id="80830-131">Unlike other code in the iterator the `null` validation will occur when the iterator method is invoked, not when the underlying enumerator is walked.</span></span> <span data-ttu-id="80830-132">Cela est vrai pour les itérateurs traditionnels ou `async`.</span><span class="sxs-lookup"><span data-stu-id="80830-132">This is true for traditional or `async` iterators.</span></span>

``` csharp
class Iterators {
    IEnumerable<char> GetCharacters(string s!) {
        foreach (var c in s) {
            yield return c;
        }
    }

    void Use() {
        // The invocation of GetCharacters will throw
        IEnumerable<char> e = GetCharacters(null);
    }
}
```

<span data-ttu-id="80830-133">L’opérateur `!` peut uniquement être utilisé pour les listes de paramètres qui ont un corps de méthode associé.</span><span class="sxs-lookup"><span data-stu-id="80830-133">The `!` operator can only be used for parameter lists which have an associated method body.</span></span> <span data-ttu-id="80830-134">Cela signifie qu’il ne peut pas être utilisé dans une méthode `abstract`, `interface`, `delegate` ou `partial` définition de méthode.</span><span class="sxs-lookup"><span data-stu-id="80830-134">This means it cannot be used in an `abstract` method, `interface`, `delegate` or `partial` method definition.</span></span>

### <a name="extending-is-null"></a><span data-ttu-id="80830-135">L’extension de est null</span><span class="sxs-lookup"><span data-stu-id="80830-135">Extending is null</span></span>
<span data-ttu-id="80830-136">Les types pour lesquels l’expression `is null` est valide sont étendus pour inclure des paramètres de type sans contrainte.</span><span class="sxs-lookup"><span data-stu-id="80830-136">The types for which the expression `is null` is valid will be extended to include unconstrained type parameters.</span></span> <span data-ttu-id="80830-137">Cela lui permettra de rechercher les `null` sur tous les types dont le contrôle de `null` est valide.</span><span class="sxs-lookup"><span data-stu-id="80830-137">This will allow it to fill the intent of checking for `null` on all types which a `null` check is valid.</span></span> <span data-ttu-id="80830-138">En particulier, il s’agit de types qui ne sont pas vraiment connus comme des types valeur.</span><span class="sxs-lookup"><span data-stu-id="80830-138">Specifically that is types which are not definitely known to be value types.</span></span> <span data-ttu-id="80830-139">Par exemple, les paramètres de type qui sont limités à `struct` ne peuvent pas être utilisés avec cette syntaxe.</span><span class="sxs-lookup"><span data-stu-id="80830-139">For example Type parameters which are constrained to `struct` cannot be used with this syntax.</span></span>

``` csharp
void NullCheck<T1, T2>(T1 p1, T2 p2) where T2 : struct {
    // Okay: T1 could be a class or struct here.
    if (p1 is null) {
        ...
    }

    // Error 
    if (p2 is null) { 
        ...
    }
}
```

<span data-ttu-id="80830-140">Le comportement de `is null` sur un paramètre de type sera le même que `== null` aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="80830-140">The behavior of `is null` on a type parameter will be the same as `== null` today.</span></span> <span data-ttu-id="80830-141">Dans les cas où le paramètre de type est instancié en tant que type valeur, le code est évalué comme `false`.</span><span class="sxs-lookup"><span data-stu-id="80830-141">In the cases where the type parameter is instantiated as a value type the code will be evaluated as `false`.</span></span> <span data-ttu-id="80830-142">Dans les cas où il s’agit d’un type référence, le code effectue un contrôle de `is null` approprié.</span><span class="sxs-lookup"><span data-stu-id="80830-142">For cases where it is a reference type the code will do a proper `is null` check.</span></span>

### <a name="intersection-with-nullable-reference-types"></a><span data-ttu-id="80830-143">Intersection avec les types de référence Nullable</span><span class="sxs-lookup"><span data-stu-id="80830-143">Intersection with Nullable Reference Types</span></span>
<span data-ttu-id="80830-144">Tout paramètre ayant un opérateur `!` appliqué à son nom commence avec l’État Nullable non `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-144">Any parameter which has a `!` operator applied to it's name will start with the nullable state being not `null`.</span></span> <span data-ttu-id="80830-145">Cela est vrai même si le type du paramètre lui-même est potentiellement `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-145">This is true even if the type of the parameter itself is potentially `null`.</span></span> <span data-ttu-id="80830-146">Cela peut se produire avec un type explicitement Nullable, comme par exemple `string?`ou avec un paramètre de type sans contrainte.</span><span class="sxs-lookup"><span data-stu-id="80830-146">That can occur with an explicitly nullable type, such as say `string?`, or with an unconstrained type parameter.</span></span> 

<span data-ttu-id="80830-147">Quand une syntaxe de `!` sur des paramètres est combinée avec un type Nullable explicite sur le paramètre, un avertissement est émis par le compilateur :</span><span class="sxs-lookup"><span data-stu-id="80830-147">When a `!` syntax on parameters is combined with an explicitly nullable type on the parameter then a warning will be issued by the compiler:</span></span>

``` csharp
void WarnCase<T>(
    string? name!, // Warning: combining explicit null checking with a nullable type
    T value1 // Okay
)
```

## <a name="open-issues"></a><span data-ttu-id="80830-148">Problèmes ouverts</span><span class="sxs-lookup"><span data-stu-id="80830-148">Open Issues</span></span>
<span data-ttu-id="80830-149">None</span><span class="sxs-lookup"><span data-stu-id="80830-149">None</span></span>

## <a name="considerations"></a><span data-ttu-id="80830-150">Considérations</span><span class="sxs-lookup"><span data-stu-id="80830-150">Considerations</span></span>

### <a name="constructors"></a><span data-ttu-id="80830-151">Constructeurs</span><span class="sxs-lookup"><span data-stu-id="80830-151">Constructors</span></span>
<span data-ttu-id="80830-152">La génération de code pour les constructeurs signifie qu’il y a un changement de comportement faible, mais observable, lors du passage d’une validation de `null` standard aujourd’hui à la syntaxe de paramètre de validation `null` (`!`).</span><span class="sxs-lookup"><span data-stu-id="80830-152">The code generation for constructors means there is a small, but observable, behavior change when moving from standard `null` validation today and the `null` validation parameter syntax (`!`).</span></span> <span data-ttu-id="80830-153">La validation de la `null` dans la validation standard intervient après les initialiseurs de champ et les appels de `base` ou de `this`.</span><span class="sxs-lookup"><span data-stu-id="80830-153">The `null` check in standard validation occurs after both field initializers and any `base` or `this` calls.</span></span> <span data-ttu-id="80830-154">Cela signifie qu’un développeur ne peut pas nécessairement migrer 100% de sa validation `null` vers la nouvelle syntaxe.</span><span class="sxs-lookup"><span data-stu-id="80830-154">This means a developer can't necessarily migrate 100% of their `null` validation to the new syntax.</span></span> <span data-ttu-id="80830-155">Les constructeurs nécessitent au moins une inspection.</span><span class="sxs-lookup"><span data-stu-id="80830-155">Constructors at least require some inspection.</span></span>

<span data-ttu-id="80830-156">Après discussion, il a été décidé qu’il est très peu probable que cela entraîne des problèmes d’adoption significatifs.</span><span class="sxs-lookup"><span data-stu-id="80830-156">After discussion though it was decided that this is very unlikely to cause any significant adoption issues.</span></span> <span data-ttu-id="80830-157">Il est plus logique que le contrôle de `null` s’exécute avant toute logique du constructeur.</span><span class="sxs-lookup"><span data-stu-id="80830-157">It's more logical that the `null` check run before any logic in the constructor does.</span></span> <span data-ttu-id="80830-158">Peut revoir si des problèmes de compatibilité importants sont détectés.</span><span class="sxs-lookup"><span data-stu-id="80830-158">Can revisit if significant compat issues are discovered.</span></span>

### <a name="warning-when-mixing--and-"></a><span data-ttu-id="80830-159">AVERTISSEMENT lors du mixage</span><span class="sxs-lookup"><span data-stu-id="80830-159">Warning when mixing ?</span></span> <span data-ttu-id="80830-160">les!</span><span class="sxs-lookup"><span data-stu-id="80830-160">and !</span></span>
<span data-ttu-id="80830-161">Il y avait une longue discussion sur la nécessité ou non de l’émission d’un avertissement lorsque la syntaxe de `!` est appliquée à un paramètre explicitement typé en type Nullable.</span><span class="sxs-lookup"><span data-stu-id="80830-161">There was a lengthy discussion on whether or not a warning should be issued when the `!` syntax is applied to a parameter which is explicitly typed to a nullable type.</span></span> <span data-ttu-id="80830-162">Sur la surface, il semblerait qu’il s’agissait d’une déclaration absurde par le développeur, mais dans certains cas, les hiérarchies de types peuvent forcer les développeurs dans une telle situation.</span><span class="sxs-lookup"><span data-stu-id="80830-162">On the surface it seems like a nonsensical declaration by the developer but there are cases where type hierarchies could force developers into such a situation.</span></span> 

<span data-ttu-id="80830-163">Considérez la hiérarchie de classes suivante sur une série d’assemblys (en supposant que tous les éléments sont compilés avec `null` vérification activée) :</span><span class="sxs-lookup"><span data-stu-id="80830-163">Consider the following class hierarchy across a series of assemblies (assuming all are compiled with `null` checking enabled):</span></span>

``` csharp
// Assembly1
abstract class C1 {
    protected abstract void M(object o); 
}

// Assembly2
abstract class C2 : C1 {

}

// Assembly3
abstract class C3 : C2 { 
    protected override void M(object o!) {
        ...
    }
}
```

<span data-ttu-id="80830-164">Ici, l’auteur de `C3` a décidé d’ajouter `null` validation au paramètre `o`.</span><span class="sxs-lookup"><span data-stu-id="80830-164">Here the author of `C3` decided to add `null` validation to the parameter `o`.</span></span> <span data-ttu-id="80830-165">Cela est entièrement conforme à la façon dont la fonctionnalité est destinée à être utilisée.</span><span class="sxs-lookup"><span data-stu-id="80830-165">This is completely in line with how the feature is intended to be used.</span></span>

<span data-ttu-id="80830-166">À présent, imaginez à une date ultérieure que l’auteur de Assembly2 décide d’ajouter la substitution suivante :</span><span class="sxs-lookup"><span data-stu-id="80830-166">Now imagine at a later date the author of Assembly2 decides to add the following override:</span></span>

``` csharp
// Assembly2
abstract class C2 : C1 {
   protected override void M(object? o) { 
       ...
   }
}
```

<span data-ttu-id="80830-167">Cela est autorisé par les types de référence Nullable, car il est légal de rendre le contrat plus flexible pour les positions d’entrée.</span><span class="sxs-lookup"><span data-stu-id="80830-167">This is allowed by nullable reference types as it's legal to make the contract more flexible for input positions.</span></span> <span data-ttu-id="80830-168">La fonctionnalité Diagnostics proactifs NRT en général permet une co-contravariance raisonnable sur le paramètre/la possibilité de valeur null.</span><span class="sxs-lookup"><span data-stu-id="80830-168">The NRT feature in general allows for reasonable co/contravariance on parameter / return nullability.</span></span> <span data-ttu-id="80830-169">Toutefois, le langage effectue la vérification de la coexistence en fonction de la substitution la plus spécifique, et non de la déclaration d’origine.</span><span class="sxs-lookup"><span data-stu-id="80830-169">However the language does the co/contravariance checking based on the most specific override, not the original declaration.</span></span> <span data-ttu-id="80830-170">Cela signifie que l’auteur de Assembly3 recevra un avertissement concernant le type de `o` ne correspondant pas et devra changer la signature en ce qui suit pour l’éliminer :</span><span class="sxs-lookup"><span data-stu-id="80830-170">This means the author of Assembly3 will get a warning about the type of `o` not matching and will need to change the signature to the following to eliminate it:</span></span> 

``` csharp
// Assembly3
abstract class C3 : C2 { 
    protected override void M(object? o!) {
        ...
    }
}
```

<span data-ttu-id="80830-171">À ce stade, l’auteur de Assembly3 a plusieurs choix :</span><span class="sxs-lookup"><span data-stu-id="80830-171">At this point the author of Assembly3 has a few choices:</span></span>

- <span data-ttu-id="80830-172">Ils peuvent accepter/supprimer l’avertissement concernant `object?` et `object` incompatibilité.</span><span class="sxs-lookup"><span data-stu-id="80830-172">They can accept / suppress the warning about `object?` and `object` mismatch.</span></span>
- <span data-ttu-id="80830-173">Ils peuvent accepter/supprimer l’avertissement concernant `object?` et `!` incompatibilité.</span><span class="sxs-lookup"><span data-stu-id="80830-173">They can accept / suppress the warning about `object?` and `!` mismatch.</span></span>
- <span data-ttu-id="80830-174">Ils peuvent simplement supprimer le contrôle de validation `null` (supprimer `!` et effectuer des vérifications explicites)</span><span class="sxs-lookup"><span data-stu-id="80830-174">They can just remove the `null` validation check (delete `!` and do explicit checking)</span></span>

<span data-ttu-id="80830-175">Il s’agit d’un scénario réel, mais pour l’instant, l’idée est d’avancer avec l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="80830-175">This is a real scenario but for now the idea is to move forward with the warning.</span></span> <span data-ttu-id="80830-176">S’il s’avère que l’avertissement se produit plus fréquemment que prévu, nous pouvons le supprimer plus tard (l’inverse n’est pas vrai).</span><span class="sxs-lookup"><span data-stu-id="80830-176">If it turns out the warning happens more frequently than we anticipate then we can remove it later (the reverse is not true).</span></span>

### <a name="implicit-property-setter-arguments"></a><span data-ttu-id="80830-177">Arguments de l’accesseur Set de propriété implicite</span><span class="sxs-lookup"><span data-stu-id="80830-177">Implicit property setter arguments</span></span>
<span data-ttu-id="80830-178">L’argument `value` d’un paramètre est implicite et n’apparaît dans aucune liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="80830-178">The `value` argument of a parameter is implicit and does not appear in any parameter list.</span></span> <span data-ttu-id="80830-179">Cela signifie qu’il ne peut pas être une cible de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="80830-179">That means it cannot be a target of this feature.</span></span> <span data-ttu-id="80830-180">La syntaxe de l’accesseur Set de la propriété peut être étendue pour inclure une liste de paramètres pour permettre l’application de l’opérateur `!`.</span><span class="sxs-lookup"><span data-stu-id="80830-180">The property setter syntax could be extended to include a parameter list to allow the `!` operator to be applied.</span></span> <span data-ttu-id="80830-181">Mais cela réduit l’idée de cette fonctionnalité en simplifiant la validation de `null`.</span><span class="sxs-lookup"><span data-stu-id="80830-181">But that cuts against the idea of this feature making `null` validation simpler.</span></span> <span data-ttu-id="80830-182">En tant que tel, l’argument `value` implicite ne fonctionne pas avec cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="80830-182">As such the implicit `value` argument just won't work with this feature.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="80830-183">Éléments futurs à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="80830-183">Future Considerations</span></span>
<span data-ttu-id="80830-184">None</span><span class="sxs-lookup"><span data-stu-id="80830-184">None</span></span>
