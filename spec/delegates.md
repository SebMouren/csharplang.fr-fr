---
ms.openlocfilehash: d162d4b6a489032dcdfca9094a39d88fd03d4013
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704101"
---
# <a name="delegates"></a><span data-ttu-id="97eb2-101">Délégués</span><span class="sxs-lookup"><span data-stu-id="97eb2-101">Delegates</span></span>

<span data-ttu-id="97eb2-102">Les délégués permettent aux scénarios que d’autres langages, tels que C++, Pascal et Modula, aient résolu les pointeurs de fonction.</span><span class="sxs-lookup"><span data-stu-id="97eb2-102">Delegates enable scenarios that other languages—such as C++, Pascal, and Modula -- have addressed with function pointers.</span></span> <span data-ttu-id="97eb2-103">Contrairement C++ aux pointeurs de fonction, toutefois, les délégués sont entièrement orientés C++ objet et contrairement aux pointeurs vers des fonctions membres, les délégués encapsulent à la fois une instance d’objet et une méthode.</span><span class="sxs-lookup"><span data-stu-id="97eb2-103">Unlike C++ function pointers, however, delegates are fully object oriented, and unlike C++ pointers to member functions, delegates encapsulate both an object instance and a method.</span></span>

<span data-ttu-id="97eb2-104">Une déclaration Delegate définit une classe dérivée de la classe `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-104">A delegate declaration defines a class that is derived from the class `System.Delegate`.</span></span> <span data-ttu-id="97eb2-105">Une instance de délégué encapsule une liste d’appel, qui est une liste d’une ou plusieurs méthodes, chacune d’elles étant désignée sous le terme d’entité pouvant être appelée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-105">A delegate instance encapsulates an invocation list, which is a list of one or more methods, each of which is referred to as a callable entity.</span></span> <span data-ttu-id="97eb2-106">Pour les méthodes d’instance, une entité pouvant être appelée se compose d’une instance et d’une méthode sur cette instance.</span><span class="sxs-lookup"><span data-stu-id="97eb2-106">For instance methods, a callable entity consists of an instance and a method on that instance.</span></span> <span data-ttu-id="97eb2-107">Pour les méthodes statiques, une entité pouvant être appelée se compose simplement d’une méthode.</span><span class="sxs-lookup"><span data-stu-id="97eb2-107">For static methods, a callable entity consists of just a method.</span></span> <span data-ttu-id="97eb2-108">L’appel d’une instance de délégué avec un jeu d’arguments approprié entraîne l’appel de chacune des entités pouvant être appelées par le délégué avec le jeu d’arguments donné.</span><span class="sxs-lookup"><span data-stu-id="97eb2-108">Invoking a delegate instance with an appropriate set of arguments causes each of the delegate's callable entities to be invoked with the given set of arguments.</span></span>

<span data-ttu-id="97eb2-109">Une propriété intéressante et utile d’une instance de délégué est qu’elle ne connaît pas ou ne tient pas compte des classes des méthodes qu’elle encapsule ; Il est important que ces méthodes soient compatibles ([déclarations déléguées](delegates.md#delegate-declarations)) avec le type du délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-109">An interesting and useful property of a delegate instance is that it does not know or care about the classes of the methods it encapsulates; all that matters is that those methods be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with the delegate's type.</span></span> <span data-ttu-id="97eb2-110">Les délégués sont alors parfaitement adaptés à l’appel « anonyme ».</span><span class="sxs-lookup"><span data-stu-id="97eb2-110">This makes delegates perfectly suited for "anonymous" invocation.</span></span>

## <a name="delegate-declarations"></a><span data-ttu-id="97eb2-111">Déclarations de délégué</span><span class="sxs-lookup"><span data-stu-id="97eb2-111">Delegate declarations</span></span>

<span data-ttu-id="97eb2-112">Un *delegate_declaration* est un *type_declaration* ([déclarations de type](namespaces.md#type-declarations)) qui déclare un nouveau type délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-112">A *delegate_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new delegate type.</span></span>

```antlr
delegate_declaration
    : attributes? delegate_modifier* 'delegate' return_type
      identifier variant_type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;

delegate_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | delegate_modifier_unsafe
    ;
```

<span data-ttu-id="97eb2-113">Il s’agit d’une erreur au moment de la compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration Delegate.</span><span class="sxs-lookup"><span data-stu-id="97eb2-113">It is a compile-time error for the same modifier to appear multiple times in a delegate declaration.</span></span>

<span data-ttu-id="97eb2-114">Le modificateur de `new` est autorisé uniquement sur les délégués déclarés dans un autre type, auquel cas il spécifie qu’un tel délégué masque un membre hérité du même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="97eb2-114">The `new` modifier is only permitted on delegates declared within another type, in which case it specifies that such a delegate hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="97eb2-115">Les modificateurs `public`, `protected`, `internal`et `private` contrôlent l’accessibilité du type délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-115">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the delegate type.</span></span> <span data-ttu-id="97eb2-116">En fonction du contexte dans lequel la déclaration du délégué se produit, certains de ces modificateurs peuvent ne pas être autorisés ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-116">Depending on the context in which the delegate declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="97eb2-117">Le nom de type du délégué est *identifier*.</span><span class="sxs-lookup"><span data-stu-id="97eb2-117">The delegate's type name is *identifier*.</span></span>

<span data-ttu-id="97eb2-118">Le *formal_parameter_list* facultatif spécifie les paramètres du délégué et *return_type* indique le type de retour du délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-118">The optional *formal_parameter_list* specifies the parameters of the delegate, and *return_type* indicates the return type of the delegate.</span></span>

<span data-ttu-id="97eb2-119">Le *variant_type_parameter_list* facultatif ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)) spécifie les paramètres de type pour le délégué lui-même.</span><span class="sxs-lookup"><span data-stu-id="97eb2-119">The optional *variant_type_parameter_list* ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)) specifies the type parameters to the delegate itself.</span></span>

<span data-ttu-id="97eb2-120">Le type de retour d’un type délégué doit être `void`ou sécurisé de sortie (la[sécurité d’écart](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-120">The return type of a delegate type must be either `void`, or output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span>

<span data-ttu-id="97eb2-121">Tous les types de paramètres formels d’un type délégué doivent être de type sécurisé d’entrée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-121">All the formal parameter types of a delegate type must be input-safe.</span></span> <span data-ttu-id="97eb2-122">En outre, tous les types de paramètres `out` ou `ref` doivent également être de type sécurisé de sortie.</span><span class="sxs-lookup"><span data-stu-id="97eb2-122">Additionally, any `out` or `ref` parameter types must also be output-safe.</span></span> <span data-ttu-id="97eb2-123">Notez que même les paramètres de `out` sont requis pour la sécurité d’entrée, en raison d’une limitation de la plateforme d’exécution sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="97eb2-123">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="97eb2-124">Les types délégués C# dans sont équivalents au nom, et non structurellement équivalents.</span><span class="sxs-lookup"><span data-stu-id="97eb2-124">Delegate types in C# are name equivalent, not structurally equivalent.</span></span> <span data-ttu-id="97eb2-125">Plus précisément, deux types délégués différents ayant les mêmes listes de paramètres et type de retour sont considérés comme des types délégués différents.</span><span class="sxs-lookup"><span data-stu-id="97eb2-125">Specifically, two different delegate types that have the same parameter lists and return type are considered different delegate types.</span></span> <span data-ttu-id="97eb2-126">Toutefois, les instances de deux types délégués distincts mais structurellement équivalents peuvent être considérées comme égales ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-126">However, instances of two distinct but structurally equivalent delegate types may compare as equal ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="97eb2-127">Exemple :</span><span class="sxs-lookup"><span data-stu-id="97eb2-127">For example:</span></span>

```csharp
delegate int D1(int i, double d);

class A
{
    public static int M1(int a, double b) {...}
}

class B
{
    delegate int D2(int c, double d);
    public static int M1(int f, double g) {...}
    public static void M2(int k, double l) {...}
    public static int M3(int g) {...}
    public static void M4(int g) {...}
}
```

<span data-ttu-id="97eb2-128">Les méthodes `A.M1` et `B.M1` sont compatibles avec les types délégués `D1` et `D2`, car ils ont le même type de retour et la même liste de paramètres ; Toutefois, ces types délégués sont de deux types différents. ils ne sont donc pas interchangeables.</span><span class="sxs-lookup"><span data-stu-id="97eb2-128">The methods `A.M1` and `B.M1` are compatible with both the delegate types `D1` and `D2` , since they have the same return type and parameter list; however, these delegate types are two different types, so they are not interchangeable.</span></span> <span data-ttu-id="97eb2-129">Les méthodes `B.M2`, `B.M3`et `B.M4` sont incompatibles avec les types délégués `D1` et `D2`, car ils ont des types de retour ou des listes de paramètres différents.</span><span class="sxs-lookup"><span data-stu-id="97eb2-129">The methods `B.M2`, `B.M3`, and `B.M4` are incompatible with the delegate types `D1` and `D2`, since they have different return types or parameter lists.</span></span>

<span data-ttu-id="97eb2-130">Comme d’autres déclarations de type générique, les arguments de type doivent être fournis pour créer un type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="97eb2-130">Like other generic type declarations, type arguments must be given to create a constructed delegate type.</span></span> <span data-ttu-id="97eb2-131">Les types de paramètres et le type de retour d’un type délégué construit sont créés en substituant, pour chaque paramètre de type dans la déclaration du délégué, l’argument de type correspondant du type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="97eb2-131">The parameter types and return type of a constructed delegate type are created by substituting, for each type parameter in the delegate declaration, the corresponding type argument of the constructed delegate type.</span></span> <span data-ttu-id="97eb2-132">Le type de retour et les types de paramètres résultants sont utilisés pour déterminer quelles méthodes sont compatibles avec un type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="97eb2-132">The resulting return type and parameter types are used in determining what methods are compatible with a constructed delegate type.</span></span> <span data-ttu-id="97eb2-133">Exemple :</span><span class="sxs-lookup"><span data-stu-id="97eb2-133">For example:</span></span>

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

<span data-ttu-id="97eb2-134">La méthode `X.F` est compatible avec le type délégué `Predicate<int>` et la méthode `X.G` est compatible avec le type délégué `Predicate<string>`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-134">The method `X.F` is compatible with the delegate type `Predicate<int>` and the method `X.G` is compatible with the delegate type `Predicate<string>` .</span></span>

<span data-ttu-id="97eb2-135">La seule façon de déclarer un type délégué est d’utiliser une *delegate_declaration*.</span><span class="sxs-lookup"><span data-stu-id="97eb2-135">The only way to declare a delegate type is via a *delegate_declaration*.</span></span> <span data-ttu-id="97eb2-136">Un type délégué est un type de classe dérivé de `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-136">A delegate type is a class type that is derived from `System.Delegate`.</span></span> <span data-ttu-id="97eb2-137">Les types délégués sont implicitement `sealed`. il n’est donc pas possible de dériver un type d’un type délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-137">Delegate types are implicitly `sealed`, so it is not permissible to derive any type from a delegate type.</span></span> <span data-ttu-id="97eb2-138">Il n’est pas non plus possible de dériver un type de classe non déléguée à partir de `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-138">It is also not permissible to derive a non-delegate class type from `System.Delegate`.</span></span> <span data-ttu-id="97eb2-139">Notez que `System.Delegate` n’est pas lui-même un type délégué ; Il s’agit d’un type de classe dont tous les types délégués sont dérivés.</span><span class="sxs-lookup"><span data-stu-id="97eb2-139">Note that `System.Delegate` is not itself a delegate type; it is a class type from which all delegate types are derived.</span></span>

<span data-ttu-id="97eb2-140">C#fournit une syntaxe spéciale pour l’instanciation et l’appel de délégués.</span><span class="sxs-lookup"><span data-stu-id="97eb2-140">C# provides special syntax for delegate instantiation and invocation.</span></span> <span data-ttu-id="97eb2-141">À l’exception de l’instanciation, toute opération qui peut être appliquée à une classe ou une instance de classe peut également être appliquée à une instance ou une classe déléguée, respectivement.</span><span class="sxs-lookup"><span data-stu-id="97eb2-141">Except for instantiation, any operation that can be applied to a class or class instance can also be applied to a delegate class or instance, respectively.</span></span> <span data-ttu-id="97eb2-142">En particulier, il est possible d’accéder aux membres du type `System.Delegate` par le biais de la syntaxe d’accès aux membres habituelle.</span><span class="sxs-lookup"><span data-stu-id="97eb2-142">In particular, it is possible to access members of the `System.Delegate` type via the usual member access syntax.</span></span>

<span data-ttu-id="97eb2-143">L’ensemble de méthodes encapsulé par une instance de délégué est appelé une liste d’appel.</span><span class="sxs-lookup"><span data-stu-id="97eb2-143">The set of methods encapsulated by a delegate instance is called an invocation list.</span></span> <span data-ttu-id="97eb2-144">Lorsqu’une instance de délégué est créée ([déléguer la compatibilité](delegates.md#delegate-compatibility)) à partir d’une seule méthode, elle encapsule cette méthode et sa liste d’appel contient une seule entrée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-144">When a delegate instance is created ([Delegate compatibility](delegates.md#delegate-compatibility)) from a single method, it encapsulates that method, and its invocation list contains only one entry.</span></span> <span data-ttu-id="97eb2-145">Toutefois, lorsque deux instances de délégués non NULL sont combinées, leurs listes d’appel sont concaténées, dans l’opérande de l’ordre de gauche, puis dans l’opérande de droite, pour former une nouvelle liste d’appel, qui contient au moins deux entrées.</span><span class="sxs-lookup"><span data-stu-id="97eb2-145">However, when two non-null delegate instances are combined, their invocation lists are concatenated -- in the order left operand then right operand -- to form a new invocation list, which contains two or more entries.</span></span>

<span data-ttu-id="97eb2-146">Les délégués sont combinés à l’aide des opérateurs binaires `+` ([opérateur d’addition](expressions.md#addition-operator)) et `+=` ([assignation composée](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-146">Delegates are combined using the binary `+` ([Addition operator](expressions.md#addition-operator)) and `+=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="97eb2-147">Un délégué peut être supprimé d’une combinaison de délégués à l’aide de l' `-` binaire ([opérateur de soustraction](expressions.md#subtraction-operator)) et `-=` opérateurs ([assignation composée](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-147">A delegate can be removed from a combination of delegates, using the binary `-` ([Subtraction operator](expressions.md#subtraction-operator)) and `-=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="97eb2-148">Les délégués peuvent être comparés pour déterminer leur égalité ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="97eb2-148">Delegates can be compared for equality ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="97eb2-149">L’exemple suivant illustre l’instanciation d’un certain nombre de délégués et les listes d’appel correspondantes :</span><span class="sxs-lookup"><span data-stu-id="97eb2-149">The following example shows the instantiation of a number of delegates, and their corresponding invocation lists:</span></span>

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public static void M2(int i) {...}

}

class Test
{
    static void Main() {
        D cd1 = new D(C.M1);      // M1
        D cd2 = new D(C.M2);      // M2
        D cd3 = cd1 + cd2;        // M1 + M2
        D cd4 = cd3 + cd1;        // M1 + M2 + M1
        D cd5 = cd4 + cd3;        // M1 + M2 + M1 + M1 + M2
    }

}
```

<span data-ttu-id="97eb2-150">Lorsque `cd1` et `cd2` sont instanciés, ils encapsulent chacun une méthode.</span><span class="sxs-lookup"><span data-stu-id="97eb2-150">When `cd1` and `cd2` are instantiated, they each encapsulate one method.</span></span> <span data-ttu-id="97eb2-151">Lorsque `cd3` est instancié, il dispose d’une liste d’appel de deux méthodes, `M1` et `M2`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="97eb2-151">When `cd3` is instantiated, it has an invocation list of two methods, `M1` and `M2`, in that order.</span></span> <span data-ttu-id="97eb2-152">la liste d’appel de `cd4`contient `M1`, `M2`et `M1`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="97eb2-152">`cd4`'s invocation list contains `M1`, `M2`, and `M1`, in that order.</span></span> <span data-ttu-id="97eb2-153">Enfin, la liste d’appel de `cd5`contient `M1`, `M2`, `M1`, `M1`et `M2`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="97eb2-153">Finally, `cd5`'s invocation list contains `M1`, `M2`, `M1`, `M1`, and `M2`, in that order.</span></span> <span data-ttu-id="97eb2-154">Pour obtenir plus d’exemples de combinaison (et de suppression) de délégués, consultez [appel de délégué](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="97eb2-154">For more examples of combining (as well as removing) delegates, see [Delegate invocation](delegates.md#delegate-invocation).</span></span>

## <a name="delegate-compatibility"></a><span data-ttu-id="97eb2-155">Déléguer la compatibilité</span><span class="sxs-lookup"><span data-stu-id="97eb2-155">Delegate compatibility</span></span>

<span data-ttu-id="97eb2-156">Une méthode ou un délégué `M` est ***compatible*** avec un type délégué `D` si toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="97eb2-156">A method or delegate `M` is ***compatible*** with a delegate type `D` if all of the following are true:</span></span>

*  <span data-ttu-id="97eb2-157">`D` et `M` ont le même nombre de paramètres, et chaque paramètre dans `D` a les mêmes modificateurs `ref` ou `out` que le paramètre correspondant dans `M`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-157">`D` and `M` have the same number of parameters, and each parameter in `D` has the same `ref` or `out` modifiers as the corresponding parameter in `M`.</span></span>
*  <span data-ttu-id="97eb2-158">Pour chaque paramètre de valeur (paramètre sans `ref` ou modificateur de `out`), une conversion d’identité ([conversion d’identité](conversions.md#identity-conversion)) ou une conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe à partir du type de paramètre dans `D` vers le type de paramètre correspondant dans `M`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-158">For each value parameter (a parameter with no `ref` or `out` modifier), an identity conversion ([Identity conversion](conversions.md#identity-conversion)) or implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the parameter type in `D` to the corresponding parameter type in `M`.</span></span>
*  <span data-ttu-id="97eb2-159">Pour chaque `ref` ou `out` paramètre, le type de paramètre dans `D` est le même que le type de paramètre dans `M`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-159">For each `ref` or `out` parameter, the parameter type in `D` is the same as the parameter type in `M`.</span></span>
*  <span data-ttu-id="97eb2-160">Une conversion d’identité ou de référence implicite existe du type de retour de `M` au type de retour de `D`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-160">An identity or implicit reference conversion exists from the return type of `M` to the return type of `D`.</span></span>

## <a name="delegate-instantiation"></a><span data-ttu-id="97eb2-161">Instanciation de délégué</span><span class="sxs-lookup"><span data-stu-id="97eb2-161">Delegate instantiation</span></span>

<span data-ttu-id="97eb2-162">Une instance d’un délégué est créée par un *delegate_creation_expression* ([expressions de création de délégué](expressions.md#delegate-creation-expressions)) ou une conversion vers un type délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-162">An instance of a delegate is created by a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) or a conversion to a delegate type.</span></span> <span data-ttu-id="97eb2-163">L’instance de délégué nouvellement créée fait référence à l’une des deux :</span><span class="sxs-lookup"><span data-stu-id="97eb2-163">The newly created delegate instance then refers to either:</span></span>

*  <span data-ttu-id="97eb2-164">Méthode statique référencée dans le *delegate_creation_expression*ou</span><span class="sxs-lookup"><span data-stu-id="97eb2-164">The static method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="97eb2-165">L’objet cible (qui ne peut pas être `null`) et la méthode d’instance référencée dans le *delegate_creation_expression*, ou</span><span class="sxs-lookup"><span data-stu-id="97eb2-165">The target object (which cannot be `null`) and instance method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="97eb2-166">Autre délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-166">Another delegate.</span></span>

<span data-ttu-id="97eb2-167">Exemple :</span><span class="sxs-lookup"><span data-stu-id="97eb2-167">For example:</span></span>

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public void M2(int i) {...}
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);        // static method
        C t = new C();
        D cd2 = new D(t.M2);        // instance method
        D cd3 = new D(cd2);        // another delegate
    }
}
```

<span data-ttu-id="97eb2-168">Une fois instanciés, les instances de délégué font toujours référence aux mêmes objet et méthode cible.</span><span class="sxs-lookup"><span data-stu-id="97eb2-168">Once instantiated, delegate instances always refer to the same target object and method.</span></span> <span data-ttu-id="97eb2-169">Rappelez-vous que lorsque deux délégués sont combinés, ou lorsqu’un nouveau délégué est supprimé d’un autre, un nouveau délégué est obtenu avec sa propre liste d’appel ; les listes d’appel des délégués combinés ou supprimés restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="97eb2-169">Remember, when two delegates are combined, or one is removed from another, a new delegate results with its own invocation list; the invocation lists of the delegates combined or removed remain unchanged.</span></span>

## <a name="delegate-invocation"></a><span data-ttu-id="97eb2-170">Appel de délégué</span><span class="sxs-lookup"><span data-stu-id="97eb2-170">Delegate invocation</span></span>

<span data-ttu-id="97eb2-171">C#fournit une syntaxe spéciale pour appeler un délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-171">C# provides special syntax for invoking a delegate.</span></span> <span data-ttu-id="97eb2-172">Lorsqu’une instance de délégué non null dont la liste d’appel contient une entrée est appelée, elle appelle la méthode à l’aide des mêmes arguments qu’elle a été donnée et retourne la même valeur que la méthode référencée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-172">When a non-null delegate instance whose invocation list contains one entry is invoked, it invokes the one method with the same arguments it was given, and returns the same value as the referred to method.</span></span> <span data-ttu-id="97eb2-173">(Pour plus d’informations sur l’appel de délégué, consultez [appel de délégué](expressions.md#delegate-invocations) .) Si une exception se produit pendant l’appel d’un tel délégué et que cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception continue dans la méthode qui a appelé le délégué, comme si cette méthode avait directement appelé la méthode à laquelle ce délégué a fait face.</span><span class="sxs-lookup"><span data-stu-id="97eb2-173">(See [Delegate invocations](expressions.md#delegate-invocations) for detailed information on delegate invocation.) If an exception occurs during the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, as if that method had directly called the method to which that delegate referred.</span></span>

<span data-ttu-id="97eb2-174">L’appel d’une instance de délégué dont la liste d’appel contient plusieurs entrées se poursuit en appelant chaque méthode de la liste d’appel, de manière synchrone, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="97eb2-174">Invocation of a delegate instance whose invocation list contains multiple entries proceeds by invoking each of the methods in the invocation list, synchronously, in order.</span></span> <span data-ttu-id="97eb2-175">Chaque méthode appelée reçoit le même jeu d’arguments que celui qui a été donné à l’instance de délégué.</span><span class="sxs-lookup"><span data-stu-id="97eb2-175">Each method so called is passed the same set of arguments as was given to the delegate instance.</span></span> <span data-ttu-id="97eb2-176">Si un tel appel de délégué comprend des paramètres de référence ([paramètres de référence](classes.md#reference-parameters)), chaque appel de méthode se produit avec une référence à la même variable ; les modifications apportées à cette variable par une méthode dans la liste d’appel seront visibles par les méthodes plus loin dans la liste d’appel.</span><span class="sxs-lookup"><span data-stu-id="97eb2-176">If such a delegate invocation includes reference parameters ([Reference parameters](classes.md#reference-parameters)), each method invocation will occur with a reference to the same variable; changes to that variable by one method in the invocation list will be visible to methods further down the invocation list.</span></span> <span data-ttu-id="97eb2-177">Si l’appel de délégué comprend des paramètres de sortie ou une valeur de retour, la valeur finale provient de l’appel du dernier délégué de la liste.</span><span class="sxs-lookup"><span data-stu-id="97eb2-177">If the delegate invocation includes output parameters or a return value, their final value will come from the invocation of the last delegate in the list.</span></span>

<span data-ttu-id="97eb2-178">Si une exception se produit pendant le traitement de l’appel d’un délégué de ce type, et si cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception continue dans la méthode qui a appelé le délégué, et toutes les méthodes plus loin la liste d’appel n’est pas appelée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-178">If an exception occurs during processing of the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, and any methods further down the invocation list are not invoked.</span></span>

<span data-ttu-id="97eb2-179">Toute tentative d’appel d’une instance de délégué dont la valeur est null entraîne une exception de type `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="97eb2-179">Attempting to invoke a delegate instance whose value is null results in an exception of type `System.NullReferenceException`.</span></span>

<span data-ttu-id="97eb2-180">L’exemple suivant montre comment instancier, combiner, supprimer et appeler des délégués :</span><span class="sxs-lookup"><span data-stu-id="97eb2-180">The following example shows how to instantiate, combine, remove, and invoke delegates:</span></span>

```csharp
using System;

delegate void D(int x);

class C
{
    public static void M1(int i) {
        Console.WriteLine("C.M1: " + i);
    }

    public static void M2(int i) {
        Console.WriteLine("C.M2: " + i);
    }

    public void M3(int i) {
        Console.WriteLine("C.M3: " + i);
    }
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);
        cd1(-1);                // call M1

        D cd2 = new D(C.M2);
        cd2(-2);                // call M2

        D cd3 = cd1 + cd2;
        cd3(10);                // call M1 then M2

        cd3 += cd1;
        cd3(20);                // call M1, M2, then M1

        C c = new C();
        D cd4 = new D(c.M3);
        cd3 += cd4;
        cd3(30);                // call M1, M2, M1, then M3

        cd3 -= cd1;             // remove last M1
        cd3(40);                // call M1, M2, then M3

        cd3 -= cd4;
        cd3(50);                // call M1 then M2

        cd3 -= cd2;
        cd3(60);                // call M1

        cd3 -= cd2;             // impossible removal is benign
        cd3(60);                // call M1

        cd3 -= cd1;             // invocation list is empty so cd3 is null

        cd3(70);                // System.NullReferenceException thrown

        cd3 -= cd1;             // impossible removal is benign
    }
}
```

<span data-ttu-id="97eb2-181">Comme indiqué dans l’instruction `cd3 += cd1;`, un délégué peut être présent plusieurs fois dans une liste d’appel.</span><span class="sxs-lookup"><span data-stu-id="97eb2-181">As shown in the statement `cd3 += cd1;`, a delegate can be present in an invocation list multiple times.</span></span> <span data-ttu-id="97eb2-182">Dans ce cas, elle est simplement appelée une fois par occurrence.</span><span class="sxs-lookup"><span data-stu-id="97eb2-182">In this case, it is simply invoked once per occurrence.</span></span> <span data-ttu-id="97eb2-183">Dans une liste d’appel telle que celle-ci, lorsque ce délégué est supprimé, la dernière occurrence dans la liste d’appel est celle qui est réellement supprimée.</span><span class="sxs-lookup"><span data-stu-id="97eb2-183">In an invocation list such as this, when that delegate is removed, the last occurrence in the invocation list is the one actually removed.</span></span>

<span data-ttu-id="97eb2-184">Juste avant l’exécution de l’instruction finale, `cd3 -= cd1;`, le délégué `cd3` fait référence à une liste d’appel vide.</span><span class="sxs-lookup"><span data-stu-id="97eb2-184">Immediately prior to the execution of the final statement, `cd3 -= cd1;`, the delegate `cd3` refers to an empty invocation list.</span></span> <span data-ttu-id="97eb2-185">Toute tentative de suppression d’un délégué d’une liste vide (ou de suppression d’un délégué inexistant d’une liste non vide) n’est pas une erreur.</span><span class="sxs-lookup"><span data-stu-id="97eb2-185">Attempting to remove a delegate from an empty list (or to remove a non-existent delegate from a non-empty list) is not an error.</span></span>

<span data-ttu-id="97eb2-186">La sortie produite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="97eb2-186">The output produced is:</span></span>

```console
C.M1: -1
C.M2: -2
C.M1: 10
C.M2: 10
C.M1: 20
C.M2: 20
C.M1: 20
C.M1: 30
C.M2: 30
C.M1: 30
C.M3: 30
C.M1: 40
C.M2: 40
C.M3: 40
C.M1: 50
C.M2: 50
C.M1: 60
C.M1: 60
```
