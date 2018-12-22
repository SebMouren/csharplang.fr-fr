# <a name="delegates"></a><span data-ttu-id="6d13e-101">Délégués</span><span class="sxs-lookup"><span data-stu-id="6d13e-101">Delegates</span></span>

<span data-ttu-id="6d13e-102">Les délégués activent des scénarios que d’autres langages, tels que C++, Pascal et Modula--ont abordés avec des pointeurs de fonction.</span><span class="sxs-lookup"><span data-stu-id="6d13e-102">Delegates enable scenarios that other languages—such as C++, Pascal, and Modula -- have addressed with function pointers.</span></span> <span data-ttu-id="6d13e-103">Toutefois, contrairement aux pointeurs de fonction C++, les délégués sont entièrement orienté objet et contrairement aux pointeurs C++ vers des fonctions membres, les délégués encapsuler une instance d’objet et une méthode.</span><span class="sxs-lookup"><span data-stu-id="6d13e-103">Unlike C++ function pointers, however, delegates are fully object oriented, and unlike C++ pointers to member functions, delegates encapsulate both an object instance and a method.</span></span>

<span data-ttu-id="6d13e-104">Une déclaration delegate définit une classe qui est dérivée de la classe `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-104">A delegate declaration defines a class that is derived from the class `System.Delegate`.</span></span> <span data-ttu-id="6d13e-105">Une instance de délégué encapsule une liste d’appel, qui est une liste d’une ou plusieurs méthodes, chacune d’elles est appelé une entité pouvant être appelée.</span><span class="sxs-lookup"><span data-stu-id="6d13e-105">A delegate instance encapsulates an invocation list, which is a list of one or more methods, each of which is referred to as a callable entity.</span></span> <span data-ttu-id="6d13e-106">Par exemple méthodes, une entité pouvant être appelée se compose d’une instance et une méthode sur cette instance.</span><span class="sxs-lookup"><span data-stu-id="6d13e-106">For instance methods, a callable entity consists of an instance and a method on that instance.</span></span> <span data-ttu-id="6d13e-107">Pour les méthodes statiques, une entité pouvant être appelée est simplement une méthode.</span><span class="sxs-lookup"><span data-stu-id="6d13e-107">For static methods, a callable entity consists of just a method.</span></span> <span data-ttu-id="6d13e-108">Appel d’une instance de délégué avec un ensemble approprié d’arguments, chacune des entités pouvant être appelées du délégué à appeler avec l’ensemble donné d’arguments.</span><span class="sxs-lookup"><span data-stu-id="6d13e-108">Invoking a delegate instance with an appropriate set of arguments causes each of the delegate's callable entities to be invoked with the given set of arguments.</span></span>

<span data-ttu-id="6d13e-109">Une propriété intéressante et utile d’une instance de délégué est qu’il ne pas savoir ou en charge sur les classes des méthodes qu’il encapsule ; qui importe est que ces méthodes soient compatibles ([déclarations Delegate](delegates.md#delegate-declarations)) avec le type du délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-109">An interesting and useful property of a delegate instance is that it does not know or care about the classes of the methods it encapsulates; all that matters is that those methods be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with the delegate's type.</span></span> <span data-ttu-id="6d13e-110">Cela rend les délégués parfaitement adaptés pour les appels « anonyme ».</span><span class="sxs-lookup"><span data-stu-id="6d13e-110">This makes delegates perfectly suited for "anonymous" invocation.</span></span>

## <a name="delegate-declarations"></a><span data-ttu-id="6d13e-111">Déclarations de délégué</span><span class="sxs-lookup"><span data-stu-id="6d13e-111">Delegate declarations</span></span>

<span data-ttu-id="6d13e-112">Un *delegate_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau type délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-112">A *delegate_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new delegate type.</span></span>

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

<span data-ttu-id="6d13e-113">Il s’agit d’une erreur lors de la compilation pour le même modificateur apparaît plusieurs fois dans une déclaration delegate.</span><span class="sxs-lookup"><span data-stu-id="6d13e-113">It is a compile-time error for the same modifier to appear multiple times in a delegate declaration.</span></span>

<span data-ttu-id="6d13e-114">Le `new` modificateur est autorisé uniquement sur les délégués déclarés dans un autre type, auquel cas il spécifie qu’un tel délégué masque un membre hérité par le même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="6d13e-114">The `new` modifier is only permitted on delegates declared within another type, in which case it specifies that such a delegate hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="6d13e-115">Le `public`, `protected`, `internal`, et `private` modificateurs contrôlent l’accessibilité du type délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-115">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the delegate type.</span></span> <span data-ttu-id="6d13e-116">En fonction du contexte dans lequel la déclaration du délégué se produit, certains de ces modificateurs ne sont pas autorisées ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-116">Depending on the context in which the delegate declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="6d13e-117">Le nom du type délégué est *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="6d13e-117">The delegate's type name is *identifier*.</span></span>

<span data-ttu-id="6d13e-118">Le paramètre facultatif *formal_parameter_list* spécifie les paramètres du délégué, et *return_type* indique le type de retour du délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-118">The optional *formal_parameter_list* specifies the parameters of the delegate, and *return_type* indicates the return type of the delegate.</span></span>

<span data-ttu-id="6d13e-119">Le paramètre facultatif *variant_type_parameter_list* ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)) spécifie les paramètres de type pour le délégué lui-même.</span><span class="sxs-lookup"><span data-stu-id="6d13e-119">The optional *variant_type_parameter_list* ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)) specifies the type parameters to the delegate itself.</span></span>

<span data-ttu-id="6d13e-120">Le type de retour d’un type délégué doit être `void`, ou de sortie-safe ([sécurité de Variance](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-120">The return type of a delegate type must be either `void`, or output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span>

<span data-ttu-id="6d13e-121">Tous les types de paramètres formels d’un type délégué doivent être entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="6d13e-121">All the formal parameter types of a delegate type must be input-safe.</span></span> <span data-ttu-id="6d13e-122">En outre, les `out` ou `ref` types de paramètres doivent également être compatibles avec la sortie.</span><span class="sxs-lookup"><span data-stu-id="6d13e-122">Additionally, any `out` or `ref` parameter types must also be output-safe.</span></span> <span data-ttu-id="6d13e-123">Notez que même `out` paramètres sont requis pour être sécurisé à l’entrée, en raison d’une limitation de la plateforme sous-jacente de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6d13e-123">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="6d13e-124">Les types délégués en c# sont nom équivalent, pas structurellement équivalents.</span><span class="sxs-lookup"><span data-stu-id="6d13e-124">Delegate types in C# are name equivalent, not structurally equivalent.</span></span> <span data-ttu-id="6d13e-125">Plus précisément, deux types délégués différents qui ont le même paramètre répertorie et retourner le type sont considérés comme des types délégués différents.</span><span class="sxs-lookup"><span data-stu-id="6d13e-125">Specifically, two different delegate types that have the same parameter lists and return type are considered different delegate types.</span></span> <span data-ttu-id="6d13e-126">Toutefois, les instances de deux types délégué distincts mais structurellement équivalents peuvent considérés comme égaux ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-126">However, instances of two distinct but structurally equivalent delegate types may compare as equal ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="6d13e-127">Exemple :</span><span class="sxs-lookup"><span data-stu-id="6d13e-127">For example:</span></span>

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

<span data-ttu-id="6d13e-128">Les méthodes `A.M1` et `B.M1 `sont compatibles avec les deux types de délégués `D1` et `D2` , dans la mesure où ils ont le même retourner le type et liste de paramètres ; Toutefois, ces types de délégué sont deux types différents, ils sont donc pas interchangeables.</span><span class="sxs-lookup"><span data-stu-id="6d13e-128">The methods `A.M1` and `B.M1 `are compatible with both the delegate types `D1` and `D2` , since they have the same return type and parameter list; however, these delegate types are two different types, so they are not interchangeable.</span></span> <span data-ttu-id="6d13e-129">Les méthodes `B.M2`, `B.M3`, et `B.M4` ne sont pas compatibles avec les types délégués `D1` et `D2`, dans la mesure où ils ont différents types de retournés ou des listes de paramètres.</span><span class="sxs-lookup"><span data-stu-id="6d13e-129">The methods `B.M2`, `B.M3`, and `B.M4` are incompatible with the delegate types `D1` and `D2`, since they have different return types or parameter lists.</span></span>

<span data-ttu-id="6d13e-130">Comme les autres déclarations de type générique, les arguments de type doivent être accordées à créer un type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="6d13e-130">Like other generic type declarations, type arguments must be given to create a constructed delegate type.</span></span> <span data-ttu-id="6d13e-131">Les types de paramètre et le type de retour d’un type délégué construit sont créés en substituant, pour chaque paramètre de type dans la déclaration du délégué, l’argument de type correspondant du type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="6d13e-131">The parameter types and return type of a constructed delegate type are created by substituting, for each type parameter in the delegate declaration, the corresponding type argument of the constructed delegate type.</span></span> <span data-ttu-id="6d13e-132">Le type de retour qui en résulte et les types de paramètres sont utilisés pour déterminer quelles méthodes sont compatibles avec un type délégué construit.</span><span class="sxs-lookup"><span data-stu-id="6d13e-132">The resulting return type and parameter types are used in determining what methods are compatible with a constructed delegate type.</span></span> <span data-ttu-id="6d13e-133">Exemple :</span><span class="sxs-lookup"><span data-stu-id="6d13e-133">For example:</span></span>

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

<span data-ttu-id="6d13e-134">La méthode `X.F` est compatible avec le type de délégué `Predicate<int>` et la méthode `X.G` est compatible avec le type de délégué `Predicate<string>` .</span><span class="sxs-lookup"><span data-stu-id="6d13e-134">The method `X.F` is compatible with the delegate type `Predicate<int>` and the method `X.G` is compatible with the delegate type `Predicate<string>` .</span></span>

<span data-ttu-id="6d13e-135">La seule façon de déclarer un type délégué est un *delegate_declaration*.</span><span class="sxs-lookup"><span data-stu-id="6d13e-135">The only way to declare a delegate type is via a *delegate_declaration*.</span></span> <span data-ttu-id="6d13e-136">Un type délégué est un type de classe qui est dérivé de `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-136">A delegate type is a class type that is derived from `System.Delegate`.</span></span> <span data-ttu-id="6d13e-137">Types délégués sont implicitement `sealed`, donc il n’est pas possible de dériver de n’importe quel type d’un type délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-137">Delegate types are implicitly `sealed`, so it is not permissible to derive any type from a delegate type.</span></span> <span data-ttu-id="6d13e-138">Il est également possible de dériver un type de classe non délégué à partir de `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-138">It is also not permissible to derive a non-delegate class type from `System.Delegate`.</span></span> <span data-ttu-id="6d13e-139">Notez que `System.Delegate` est pas lui-même un délégué tapez ; c’est un type de classe à partir de laquelle tous les types délégués sont dérivés.</span><span class="sxs-lookup"><span data-stu-id="6d13e-139">Note that `System.Delegate` is not itself a delegate type; it is a class type from which all delegate types are derived.</span></span>

<span data-ttu-id="6d13e-140">C# fournit une syntaxe spéciale pour le délégué l’instanciation et l’appel.</span><span class="sxs-lookup"><span data-stu-id="6d13e-140">C# provides special syntax for delegate instantiation and invocation.</span></span> <span data-ttu-id="6d13e-141">À l’exception de l’instanciation, toute opération qui peut être appliquée à une classe ou une instance de classe peut également être appliquée à une instance, ou une classe déléguée respectivement.</span><span class="sxs-lookup"><span data-stu-id="6d13e-141">Except for instantiation, any operation that can be applied to a class or class instance can also be applied to a delegate class or instance, respectively.</span></span> <span data-ttu-id="6d13e-142">En particulier, il est possible d’accéder aux membres de la `System.Delegate` type via la syntaxe d’accès de membre habituel.</span><span class="sxs-lookup"><span data-stu-id="6d13e-142">In particular, it is possible to access members of the `System.Delegate` type via the usual member access syntax.</span></span>

<span data-ttu-id="6d13e-143">L’ensemble des méthodes encapsulées par une instance de délégué est appelé une liste d’appel.</span><span class="sxs-lookup"><span data-stu-id="6d13e-143">The set of methods encapsulated by a delegate instance is called an invocation list.</span></span> <span data-ttu-id="6d13e-144">Création d’une instance de délégué ([compatibilité des délégués](delegates.md#delegate-compatibility)) à partir d’une méthode unique, elle encapsule cette méthode et sa liste d’appel contient une seule entrée.</span><span class="sxs-lookup"><span data-stu-id="6d13e-144">When a delegate instance is created ([Delegate compatibility](delegates.md#delegate-compatibility)) from a single method, it encapsulates that method, and its invocation list contains only one entry.</span></span> <span data-ttu-id="6d13e-145">Toutefois, lorsque deux instances de délégué non null sont combinées, leurs listes d’appel sont concaténées--de gauche opérande de droite, pour former une nouvelle liste d’appel, qui contient deux ou plusieurs entrées.</span><span class="sxs-lookup"><span data-stu-id="6d13e-145">However, when two non-null delegate instances are combined, their invocation lists are concatenated -- in the order left operand then right operand -- to form a new invocation list, which contains two or more entries.</span></span>

<span data-ttu-id="6d13e-146">Les délégués sont combinées à l’aide du fichier binaire `+` ([opérateur d’Addition](expressions.md#addition-operator)) et `+=` opérateurs ([assignation composée](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-146">Delegates are combined using the binary `+` ([Addition operator](expressions.md#addition-operator)) and `+=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="6d13e-147">Un délégué peut être supprimé à partir d’une combinaison de délégués, à l’aide du fichier binaire `-` ([opérateur de soustraction](expressions.md#subtraction-operator)) et `-=` opérateurs ([assignation composée](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-147">A delegate can be removed from a combination of delegates, using the binary `-` ([Subtraction operator](expressions.md#subtraction-operator)) and `-=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="6d13e-148">Les délégués peuvent être comparées pour l’égalité ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="6d13e-148">Delegates can be compared for equality ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="6d13e-149">L’exemple suivant illustre l’instanciation d’un nombre de délégués, et répertorie leur appel correspondant :</span><span class="sxs-lookup"><span data-stu-id="6d13e-149">The following example shows the instantiation of a number of delegates, and their corresponding invocation lists:</span></span>

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

<span data-ttu-id="6d13e-150">Lorsque `cd1` et `cd2` sont instanciés, ils encapsulent chacun une méthode.</span><span class="sxs-lookup"><span data-stu-id="6d13e-150">When `cd1` and `cd2` are instantiated, they each encapsulate one method.</span></span> <span data-ttu-id="6d13e-151">Lorsque `cd3` est instancié, il a une liste d’appel des deux méthodes, `M1` et `M2`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="6d13e-151">When `cd3` is instantiated, it has an invocation list of two methods, `M1` and `M2`, in that order.</span></span> <span data-ttu-id="6d13e-152">`cd4`de la liste d’appel contient `M1`, `M2`, et `M1`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="6d13e-152">`cd4`'s invocation list contains `M1`, `M2`, and `M1`, in that order.</span></span> <span data-ttu-id="6d13e-153">Enfin, `cd5`de liste d’appel contient `M1`, `M2`, `M1`, `M1`, et `M2`, dans cet ordre.</span><span class="sxs-lookup"><span data-stu-id="6d13e-153">Finally, `cd5`'s invocation list contains `M1`, `M2`, `M1`, `M1`, and `M2`, in that order.</span></span> <span data-ttu-id="6d13e-154">Pour plus d’exemples de combinaison de délégués (ainsi que la suppression), consultez [appel de délégué](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="6d13e-154">For more examples of combining (as well as removing) delegates, see [Delegate invocation](delegates.md#delegate-invocation).</span></span>

## <a name="delegate-compatibility"></a><span data-ttu-id="6d13e-155">Compatibilité des délégués</span><span class="sxs-lookup"><span data-stu-id="6d13e-155">Delegate compatibility</span></span>

<span data-ttu-id="6d13e-156">Une méthode ou un délégué `M` est ***compatible*** avec un type délégué `D` si toutes les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="6d13e-156">A method or delegate `M` is ***compatible*** with a delegate type `D` if all of the following are true:</span></span>

*  <span data-ttu-id="6d13e-157">`D` et `M` ont le même nombre de paramètres et chaque paramètre dans `D` a les mêmes `ref` ou `out` modificateurs que le paramètre correspondant dans `M`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-157">`D` and `M` have the same number of parameters, and each parameter in `D` has the same `ref` or `out` modifiers as the corresponding parameter in `M`.</span></span>
*  <span data-ttu-id="6d13e-158">Pour chaque paramètre de valeur (un paramètre sans aucune `ref` ou `out` modificateur), une conversion d’identité ([conversion d’identité](conversions.md#identity-conversion)) ou conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) Il existe à partir du type de paramètre dans `D` pour le type de paramètre correspondant dans `M`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-158">For each value parameter (a parameter with no `ref` or `out` modifier), an identity conversion ([Identity conversion](conversions.md#identity-conversion)) or implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the parameter type in `D` to the corresponding parameter type in `M`.</span></span>
*  <span data-ttu-id="6d13e-159">Pour chaque `ref` ou `out` paramètre, le type de paramètre dans `D` est le même que le type de paramètre dans `M`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-159">For each `ref` or `out` parameter, the parameter type in `D` is the same as the parameter type in `M`.</span></span>
*  <span data-ttu-id="6d13e-160">Il existe une identité ou une conversion de référence implicite du type de retour de `M` pour le type de retour de `D`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-160">An identity or implicit reference conversion exists from the return type of `M` to the return type of `D`.</span></span>

## <a name="delegate-instantiation"></a><span data-ttu-id="6d13e-161">Instanciation des délégués</span><span class="sxs-lookup"><span data-stu-id="6d13e-161">Delegate instantiation</span></span>

<span data-ttu-id="6d13e-162">Une instance d’un délégué est créée par un *delegate_creation_expression* ([expressions de création de délégué](expressions.md#delegate-creation-expressions)) ou une conversion vers un type délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-162">An instance of a delegate is created by a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) or a conversion to a delegate type.</span></span> <span data-ttu-id="6d13e-163">L’instance de délégué nouvellement créé fait ensuite référence à un :</span><span class="sxs-lookup"><span data-stu-id="6d13e-163">The newly created delegate instance then refers to either:</span></span>

*  <span data-ttu-id="6d13e-164">La méthode statique référencée dans le *delegate_creation_expression*, ou</span><span class="sxs-lookup"><span data-stu-id="6d13e-164">The static method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="6d13e-165">L’objet cible (qui ne peut pas être `null`) et l’instance de méthode référencée dans le *delegate_creation_expression*, ou</span><span class="sxs-lookup"><span data-stu-id="6d13e-165">The target object (which cannot be `null`) and instance method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="6d13e-166">Un autre délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-166">Another delegate.</span></span>

<span data-ttu-id="6d13e-167">Exemple :</span><span class="sxs-lookup"><span data-stu-id="6d13e-167">For example:</span></span>

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

<span data-ttu-id="6d13e-168">Une fois instancié, instances de délégué consultez toujours le même objet cible et la méthode.</span><span class="sxs-lookup"><span data-stu-id="6d13e-168">Once instantiated, delegate instances always refer to the same target object and method.</span></span> <span data-ttu-id="6d13e-169">N’oubliez pas, lorsque deux délégués sont combinés, ou un est supprimé d’un autre, un nouveau délégué des résultats avec sa propre liste d’appel ; les listes d’appel des délégués combinés ou supprimés restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="6d13e-169">Remember, when two delegates are combined, or one is removed from another, a new delegate results with its own invocation list; the invocation lists of the delegates combined or removed remain unchanged.</span></span>

## <a name="delegate-invocation"></a><span data-ttu-id="6d13e-170">Appel de délégué</span><span class="sxs-lookup"><span data-stu-id="6d13e-170">Delegate invocation</span></span>

<span data-ttu-id="6d13e-171">C# fournit une syntaxe spéciale pour l’appel d’un délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-171">C# provides special syntax for invoking a delegate.</span></span> <span data-ttu-id="6d13e-172">Lorsqu’une instance de délégué non null dont la liste d’appel contient une entrée est appelée, elle appelle la méthode avec les mêmes arguments a été spécifié, elle retourne la même valeur que les données désignée à méthode.</span><span class="sxs-lookup"><span data-stu-id="6d13e-172">When a non-null delegate instance whose invocation list contains one entry is invoked, it invokes the one method with the same arguments it was given, and returns the same value as the referred to method.</span></span> <span data-ttu-id="6d13e-173">(Consultez [appels de délégué](expressions.md#delegate-invocations) pour plus d’informations sur l’appel de délégué.) Si une exception se produit pendant l’appel d’un délégué de ce type, et cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception se poursuit dans la méthode ayant appelé le délégué, comme si cette méthode avait appelé directement la méthode à laquelle ce délégué est appelée.</span><span class="sxs-lookup"><span data-stu-id="6d13e-173">(See [Delegate invocations](expressions.md#delegate-invocations) for detailed information on delegate invocation.) If an exception occurs during the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, as if that method had directly called the method to which that delegate referred.</span></span>

<span data-ttu-id="6d13e-174">Appel d’une instance de délégué dont la liste d’appel contient plusieurs entrées se poursuit en appelant chacune des méthodes dans la liste d’appel, de façon synchrone, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="6d13e-174">Invocation of a delegate instance whose invocation list contains multiple entries proceeds by invoking each of the methods in the invocation list, synchronously, in order.</span></span> <span data-ttu-id="6d13e-175">Chaque méthode ce qu’on appelé reçoit le même ensemble d’arguments comme a été spécifié pour l’instance de délégué.</span><span class="sxs-lookup"><span data-stu-id="6d13e-175">Each method so called is passed the same set of arguments as was given to the delegate instance.</span></span> <span data-ttu-id="6d13e-176">Si un appel de délégué de ce type inclut les paramètres de référence ([paramètres de référence](classes.md#reference-parameters)), chaque appel de méthode se produit avec une référence à la même variable ; les modifications apportées à cette variable par une méthode dans la liste d’appel seront méthodes plus visible dans la liste d’appel.</span><span class="sxs-lookup"><span data-stu-id="6d13e-176">If such a delegate invocation includes reference parameters ([Reference parameters](classes.md#reference-parameters)), each method invocation will occur with a reference to the same variable; changes to that variable by one method in the invocation list will be visible to methods further down the invocation list.</span></span> <span data-ttu-id="6d13e-177">Si l’appel de délégué comprend des paramètres de sortie ou une valeur de retour, leur valeur finale proviendront de l’appel du dernier délégué dans la liste.</span><span class="sxs-lookup"><span data-stu-id="6d13e-177">If the delegate invocation includes output parameters or a return value, their final value will come from the invocation of the last delegate in the list.</span></span>

<span data-ttu-id="6d13e-178">Si une exception se produit pendant le traitement de l’appel d’un délégué de ce type, et cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception se poursuit dans la méthode ayant appelé le délégué, et toutes les méthodes plus bas la liste d’appel ne sont pas appelés.</span><span class="sxs-lookup"><span data-stu-id="6d13e-178">If an exception occurs during processing of the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, and any methods further down the invocation list are not invoked.</span></span>

<span data-ttu-id="6d13e-179">Tentative d’appel d’une instance de délégué dont la valeur est null lève une exception de type `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="6d13e-179">Attempting to invoke a delegate instance whose value is null results in an exception of type `System.NullReferenceException`.</span></span>

<span data-ttu-id="6d13e-180">L’exemple suivant montre comment instancier, combiner, supprimer et appeler des délégués :</span><span class="sxs-lookup"><span data-stu-id="6d13e-180">The following example shows how to instantiate, combine, remove, and invoke delegates:</span></span>

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

<span data-ttu-id="6d13e-181">Comme indiqué dans l’instruction `cd3 += cd1;`, un délégué peut être présent dans une liste d’appel plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="6d13e-181">As shown in the statement `cd3 += cd1;`, a delegate can be present in an invocation list multiple times.</span></span> <span data-ttu-id="6d13e-182">Dans ce cas, il est simplement appelé une fois par occurrence.</span><span class="sxs-lookup"><span data-stu-id="6d13e-182">In this case, it is simply invoked once per occurrence.</span></span> <span data-ttu-id="6d13e-183">Dans une liste d’appel telle que celle-ci, lorsque ce délégué est supprimé, la dernière occurrence dans la liste d’appel est celui réellement supprimé.</span><span class="sxs-lookup"><span data-stu-id="6d13e-183">In an invocation list such as this, when that delegate is removed, the last occurrence in the invocation list is the one actually removed.</span></span>

<span data-ttu-id="6d13e-184">Immédiatement avant l’exécution de l’instruction finale, `cd3 -= cd1;`, le délégué `cd3` fait référence à une liste d’appel vide.</span><span class="sxs-lookup"><span data-stu-id="6d13e-184">Immediately prior to the execution of the final statement, `cd3 -= cd1;`, the delegate `cd3` refers to an empty invocation list.</span></span> <span data-ttu-id="6d13e-185">Une tentative pour supprimer un délégué d’une liste vide (ou pour supprimer un délégué inexistant dans une liste non vide) n’est pas une erreur.</span><span class="sxs-lookup"><span data-stu-id="6d13e-185">Attempting to remove a delegate from an empty list (or to remove a non-existent delegate from a non-empty list) is not an error.</span></span>

<span data-ttu-id="6d13e-186">La sortie produite est :</span><span class="sxs-lookup"><span data-stu-id="6d13e-186">The output produced is:</span></span>

```
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
