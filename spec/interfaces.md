# <a name="interfaces"></a><span data-ttu-id="7d62d-101">Interfaces</span><span class="sxs-lookup"><span data-stu-id="7d62d-101">Interfaces</span></span>

<span data-ttu-id="7d62d-102">Une interface définit un contrat.</span><span class="sxs-lookup"><span data-stu-id="7d62d-102">An interface defines a contract.</span></span> <span data-ttu-id="7d62d-103">Une classe ou un struct qui implémente une interface doit adhérer à ce contrat.</span><span class="sxs-lookup"><span data-stu-id="7d62d-103">A class or struct that implements an interface must adhere to its contract.</span></span> <span data-ttu-id="7d62d-104">Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.</span><span class="sxs-lookup"><span data-stu-id="7d62d-104">An interface may inherit from multiple base interfaces, and a class or struct may implement multiple interfaces.</span></span>

<span data-ttu-id="7d62d-105">Interfaces peuvent contenir des méthodes, propriétés, événements et indexeurs.</span><span class="sxs-lookup"><span data-stu-id="7d62d-105">Interfaces can contain methods, properties, events, and indexers.</span></span> <span data-ttu-id="7d62d-106">L’interface elle-même ne fournit pas d’implémentations pour les membres qu’elle définit.</span><span class="sxs-lookup"><span data-stu-id="7d62d-106">The interface itself does not provide implementations for the members that it defines.</span></span> <span data-ttu-id="7d62d-107">L’interface spécifie simplement les membres qui doivent être fournis par les classes ou structs qui implémentent l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-107">The interface merely specifies the members that must be supplied by classes or structs that implement the interface.</span></span>

## <a name="interface-declarations"></a><span data-ttu-id="7d62d-108">Déclarations d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-108">Interface declarations</span></span>

<span data-ttu-id="7d62d-109">Un *interface_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau type d’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-109">An *interface_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new interface type.</span></span>

```antlr
interface_declaration
    : attributes? interface_modifier* 'partial'? 'interface'
      identifier variant_type_parameter_list? interface_base?
      type_parameter_constraints_clause* interface_body ';'?
    ;
```

<span data-ttu-id="7d62d-110">Un *interface_declaration* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *interface_modifier*s ([modificateurs d’Interface](interfaces.md#interface-modifiers)), suivie éventuellement d’un `partial` modificateur, suivi par le mot clé `interface` et un *identificateur* qui nomme l’interface, suivi d’un texte facultatif *variant_type_parameter_list* spécification ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)), suivie éventuellement d’un *interface_base* spécification ([interfaces de Base](interfaces.md#base-interfaces)), suivie éventuellement d’un *type_parameter_constraints_clause*spécification s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) , suivie d’une *interface_body* ([corps de l’Interface](interfaces.md#interface-body)), éventuellement suivi par un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-110">An *interface_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *interface_modifier*s ([Interface modifiers](interfaces.md#interface-modifiers)), followed by an optional `partial` modifier, followed by the keyword `interface` and an *identifier* that names the interface, followed by an optional *variant_type_parameter_list* specification ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)), followed by an optional *interface_base* specification ([Base interfaces](interfaces.md#base-interfaces)), followed by an optional *type_parameter_constraints_clause*s specification ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by an *interface_body* ([Interface body](interfaces.md#interface-body)), optionally followed by a semicolon.</span></span>

### <a name="interface-modifiers"></a><span data-ttu-id="7d62d-111">Modificateurs d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-111">Interface modifiers</span></span>

<span data-ttu-id="7d62d-112">Un *interface_declaration* peut éventuellement inclure une séquence de modificateurs d’interface :</span><span class="sxs-lookup"><span data-stu-id="7d62d-112">An *interface_declaration* may optionally include a sequence of interface modifiers:</span></span>

```antlr
interface_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | interface_modifier_unsafe
    ;
```

<span data-ttu-id="7d62d-113">Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration d’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-113">It is a compile-time error for the same modifier to appear multiple times in an interface declaration.</span></span>

<span data-ttu-id="7d62d-114">Le `new` modificateur est autorisé uniquement sur les interfaces définies dans une classe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-114">The `new` modifier is only permitted on interfaces defined within a class.</span></span> <span data-ttu-id="7d62d-115">Il spécifie que l’interface masque un membre hérité par le même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="7d62d-115">It specifies that the interface hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="7d62d-116">Le `public`, `protected`, `internal`, et `private` modificateurs contrôlent l’accessibilité de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-116">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the interface.</span></span> <span data-ttu-id="7d62d-117">En fonction du contexte dans lequel la déclaration d’interface se produit, seuls certains de ces modificateurs peuvent être autorisée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-117">Depending on the context in which the interface declaration occurs, only some of these modifiers may be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="7d62d-118">Modificateur partiel</span><span class="sxs-lookup"><span data-stu-id="7d62d-118">Partial modifier</span></span>

<span data-ttu-id="7d62d-119">Le `partial` modificateur indique que cela *interface_declaration* est une déclaration de type partiel.</span><span class="sxs-lookup"><span data-stu-id="7d62d-119">The `partial` modifier indicates that this *interface_declaration* is a partial type declaration.</span></span> <span data-ttu-id="7d62d-120">Plusieurs déclarations partielles interface portant le même nom dans une déclaration d’espace de noms ou type englobant combinent à la déclaration d’une interface de formulaire, conformément aux règles spécifiées dans [types partiels](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="7d62d-120">Multiple partial interface declarations with the same name within an enclosing namespace or type declaration combine to form one interface declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

### <a name="variant-type-parameter-lists"></a><span data-ttu-id="7d62d-121">Listes de paramètres de type Variant</span><span class="sxs-lookup"><span data-stu-id="7d62d-121">Variant type parameter lists</span></span>

<span data-ttu-id="7d62d-122">Listes de paramètres de type Variant peuvent se produire uniquement sur les types d’interface et délégué.</span><span class="sxs-lookup"><span data-stu-id="7d62d-122">Variant type parameter lists can only occur on interface and delegate types.</span></span> <span data-ttu-id="7d62d-123">La différence d’ordinaire *type_parameter_list*s est facultatif *variance_annotation* sur chaque paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="7d62d-123">The difference from ordinary *type_parameter_list*s is the optional *variance_annotation* on each type parameter.</span></span>

```antlr
variant_type_parameter_list
    : '<' variant_type_parameters '>'
    ;

variant_type_parameters
    : attributes? variance_annotation? type_parameter
    | variant_type_parameters ',' attributes? variance_annotation? type_parameter
    ;

variance_annotation
    : 'in'
    | 'out'
    ;
```

<span data-ttu-id="7d62d-124">Si l’annotation de variance est `out`, le paramètre de type est dite ***covariant***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-124">If the variance annotation is `out`, the type parameter is said to be ***covariant***.</span></span> <span data-ttu-id="7d62d-125">Si l’annotation de variance est `in`, le paramètre de type est dite ***contravariant***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-125">If the variance annotation is `in`, the type parameter is said to be ***contravariant***.</span></span> <span data-ttu-id="7d62d-126">S’il n’existe aucune annotation de variance, le paramètre de type est dite ***invariant***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-126">If there is no variance annotation, the type parameter is said to be ***invariant***.</span></span>

<span data-ttu-id="7d62d-127">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-127">In the example</span></span>
```csharp
interface C<out X, in Y, Z> 
{
  X M(Y y);
  Z P { get; set; }
}
```
<span data-ttu-id="7d62d-128">`X` est covariant, `Y` est contravariant et `Z` est invariant.</span><span class="sxs-lookup"><span data-stu-id="7d62d-128">`X` is covariant, `Y` is contravariant and `Z` is invariant.</span></span>

#### <a name="variance-safety"></a><span data-ttu-id="7d62d-129">Sécurité de variance</span><span class="sxs-lookup"><span data-stu-id="7d62d-129">Variance safety</span></span>

<span data-ttu-id="7d62d-130">L’occurrence des annotations de variance dans la liste de paramètres de type d’un type restreint les emplacements où les types peuvent se produire dans la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="7d62d-130">The occurrence of variance annotations in the type parameter list of a type restricts the places where types can occur within the type declaration.</span></span>

<span data-ttu-id="7d62d-131">Un type `T` est ***sortie non sécurisés*** si dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7d62d-131">A type `T` is ***output-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="7d62d-132">`T` est un paramètre de type contravariant</span><span class="sxs-lookup"><span data-stu-id="7d62d-132">`T` is a contravariant type parameter</span></span>
*  <span data-ttu-id="7d62d-133">`T` est un type de tableau avec un type d’élément de sortie non sécurisés</span><span class="sxs-lookup"><span data-stu-id="7d62d-133">`T` is an array type with an output-unsafe element type</span></span>
*  <span data-ttu-id="7d62d-134">`T` est un type d’interface ou un délégué `S<A1,...,Ak>` construit à partir d’un type générique `S<X1,...,Xk>` where pour au moins un `Ai` dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7d62d-134">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="7d62d-135">`Xi` est covariant ou invariants et `Ai` n’est pas sûre de sortie.</span><span class="sxs-lookup"><span data-stu-id="7d62d-135">`Xi` is covariant or invariant and `Ai` is output-unsafe.</span></span>
   * <span data-ttu-id="7d62d-136">`Xi` est contravariant ou invariant et `Ai` est entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-136">`Xi` is contravariant or invariant and `Ai` is input-safe.</span></span>
   
<span data-ttu-id="7d62d-137">Un type `T` est ***entrée non fiables*** si dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7d62d-137">A type `T` is ***input-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="7d62d-138">`T` est un paramètre de type covariant</span><span class="sxs-lookup"><span data-stu-id="7d62d-138">`T` is a covariant type parameter</span></span>
*  <span data-ttu-id="7d62d-139">`T` est un type de tableau avec un type d’élément d’entrée non fiables</span><span class="sxs-lookup"><span data-stu-id="7d62d-139">`T` is an array type with an input-unsafe element type</span></span>
*  <span data-ttu-id="7d62d-140">`T` est un type d’interface ou un délégué `S<A1,...,Ak>` construit à partir d’un type générique `S<X1,...,Xk>` where pour au moins un `Ai` dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7d62d-140">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="7d62d-141">`Xi` est covariant ou invariants et `Ai` est entrée non fiables.</span><span class="sxs-lookup"><span data-stu-id="7d62d-141">`Xi` is covariant or invariant and `Ai` is input-unsafe.</span></span>
   * <span data-ttu-id="7d62d-142">`Xi` est contravariant ou invariant et `Ai` n’est pas sûre de sortie.</span><span class="sxs-lookup"><span data-stu-id="7d62d-142">`Xi` is contravariant or invariant and `Ai` is output-unsafe.</span></span>

<span data-ttu-id="7d62d-143">Intuitivement, un type non sécurisé de sortie est interdit dans une position de sortie, et un type d’entrée non fiables est interdit dans une position d’entrée.</span><span class="sxs-lookup"><span data-stu-id="7d62d-143">Intuitively, an output-unsafe type is prohibited in an output position, and an input-unsafe type is prohibited in an input position.</span></span>

<span data-ttu-id="7d62d-144">Est un type ***sortie-safe*** s’il n’est pas dangereux de la sortie, et ***entrée-safe*** si elle n’est pas entrée non fiables.</span><span class="sxs-lookup"><span data-stu-id="7d62d-144">A type is ***output-safe*** if it is not output-unsafe, and ***input-safe*** if it is not input-unsafe.</span></span>

#### <a name="variance-conversion"></a><span data-ttu-id="7d62d-145">Conversion de variance</span><span class="sxs-lookup"><span data-stu-id="7d62d-145">Variance conversion</span></span>

<span data-ttu-id="7d62d-146">Les annotations de variance vise à fournir pour les conversions des types interface et délégué plus modérées (mais toujours type sécurisé).</span><span class="sxs-lookup"><span data-stu-id="7d62d-146">The purpose of variance annotations is to provide for more lenient (but still type safe) conversions to interface and delegate types.</span></span> <span data-ttu-id="7d62d-147">À cette fin les définitions d’implicite ([conversions implicites](conversions.md#implicit-conversions)) et les conversions explicites ([conversions explicites](conversions.md#explicit-conversions)) rendre utilisent la notion de la convertibilité de variance, qui est définie comme suit :</span><span class="sxs-lookup"><span data-stu-id="7d62d-147">To this end the definitions of implicit ([Implicit conversions](conversions.md#implicit-conversions)) and explicit conversions ([Explicit conversions](conversions.md#explicit-conversions)) make use of the notion of variance-convertibility, which is defined as follows:</span></span>

<span data-ttu-id="7d62d-148">Un type `T<A1,...,An>` est convertible en variance en un type `T<B1,...,Bn>` si `T` est une interface ou un type délégué déclaré avec les paramètres de type variant `T<X1,...,Xn>`et pour chaque paramètre de type variant `Xi` une des opérations suivantes contient :</span><span class="sxs-lookup"><span data-stu-id="7d62d-148">A type `T<A1,...,An>` is variance-convertible to a type `T<B1,...,Bn>` if `T` is either an interface or a delegate type declared with the variant type parameters `T<X1,...,Xn>`, and for each variant type parameter `Xi` one of the following holds:</span></span>

*  <span data-ttu-id="7d62d-149">`Xi` est covariant et il existe une conversion implicite de l’identité ou de référence à partir de `Ai` à `Bi`</span><span class="sxs-lookup"><span data-stu-id="7d62d-149">`Xi` is covariant and an implicit reference or identity conversion exists from `Ai` to `Bi`</span></span>
*  <span data-ttu-id="7d62d-150">`Xi` est contravariant et une référence implicite ou la conversion d’identité existe à partir de `Bi` à `Ai`</span><span class="sxs-lookup"><span data-stu-id="7d62d-150">`Xi` is contravariant and an implicit reference or identity conversion exists from `Bi` to `Ai`</span></span>
*  <span data-ttu-id="7d62d-151">`Xi` est invariant et une identité conversion existe entre `Ai` à `Bi`</span><span class="sxs-lookup"><span data-stu-id="7d62d-151">`Xi` is invariant and an identity conversion exists from `Ai` to `Bi`</span></span>

### <a name="base-interfaces"></a><span data-ttu-id="7d62d-152">Interfaces de base</span><span class="sxs-lookup"><span data-stu-id="7d62d-152">Base interfaces</span></span>

<span data-ttu-id="7d62d-153">Une interface peut hériter de zéro ou plusieurs types d’interface, qui sont appelés les ***interfaces de base explicites*** de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-153">An interface can inherit from zero or more interface types, which are called the ***explicit base interfaces*** of the interface.</span></span> <span data-ttu-id="7d62d-154">Lorsqu’une interface possède une ou plusieurs interfaces de base explicites, dans la déclaration de cette interface, l’identificateur d’interface est suivie par un signe deux-points et d’une virgule liste séparée par des types d’interface de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-154">When an interface has one or more explicit base interfaces, then in the declaration of that interface, the interface identifier is followed by a colon and a comma separated list of base interface types.</span></span>

```antlr
interface_base
    : ':' interface_type_list
    ;
```

<span data-ttu-id="7d62d-155">Pour un type interface construite, les interfaces de base explicites sont formés en prenant les déclarations d’interface de base explicite sur la déclaration de type générique et en remplaçant, pour chaque *type_parameter* dans l’interface de base déclaration, correspondants *type_argument* du type construit.</span><span class="sxs-lookup"><span data-stu-id="7d62d-155">For a constructed interface type, the explicit base interfaces are formed by taking the explicit base interface declarations on the generic type declaration, and substituting, for each *type_parameter* in the base interface declaration, the corresponding *type_argument* of the constructed type.</span></span>

<span data-ttu-id="7d62d-156">Les interfaces de base explicites d’une interface doivent être au moins aussi accessibles que l’interface elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-156">The explicit base interfaces of an interface must be at least as accessible as the interface itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span> <span data-ttu-id="7d62d-157">Par exemple, il est une erreur de compilation pour spécifier un `private` ou `internal` interface dans le *interface_base* d’un `public` interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-157">For example, it is a compile-time error to specify a `private` or `internal` interface in the *interface_base* of a `public` interface.</span></span>

<span data-ttu-id="7d62d-158">Il s’agit d’une erreur de compilation pour qu’une interface hérite directement ou indirectement d’elle-même.</span><span class="sxs-lookup"><span data-stu-id="7d62d-158">It is a compile-time error for an interface to directly or indirectly inherit from itself.</span></span>

<span data-ttu-id="7d62d-159">Le ***interfaces de base*** d’une interface sont les interfaces de base explicites et leurs interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-159">The ***base interfaces*** of an interface are the explicit base interfaces and their base interfaces.</span></span> <span data-ttu-id="7d62d-160">En d’autres termes, le jeu d’interfaces de base est la fermeture transitive complète des interfaces de base explicites, leurs interfaces de base explicites et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-160">In other words, the set of base interfaces is the complete transitive closure of the explicit base interfaces, their explicit base interfaces, and so on.</span></span> <span data-ttu-id="7d62d-161">Une interface hérite de tous les membres de ses interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-161">An interface inherits all members of its base interfaces.</span></span> <span data-ttu-id="7d62d-162">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-162">In the example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
<span data-ttu-id="7d62d-163">les interfaces de base de `IComboBox` sont `IControl`, `ITextBox`, et `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-163">the base interfaces of `IComboBox` are `IControl`, `ITextBox`, and `IListBox`.</span></span>

<span data-ttu-id="7d62d-164">En d’autres termes, le `IComboBox` interface ci-dessus hérite des membres `SetText` et `SetItems` ainsi que `Paint`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-164">In other words, the `IComboBox` interface above inherits members `SetText` and `SetItems` as well as `Paint`.</span></span>

<span data-ttu-id="7d62d-165">Chaque interface de base d’une interface doit être compatibles avec la sortie ([sécurité de Variance](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-165">Every base interface of an interface must be output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span> <span data-ttu-id="7d62d-166">Une classe ou un struct qui implémente une interface également implicitement implémente toutes les interfaces de base de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-166">A class or struct that implements an interface also implicitly implements all of the interface's base interfaces.</span></span>

### <a name="interface-body"></a><span data-ttu-id="7d62d-167">Corps de l’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-167">Interface body</span></span>

<span data-ttu-id="7d62d-168">Le *interface_body* d’une interface définit les membres de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-168">The *interface_body* of an interface defines the members of the interface.</span></span>

```antlr
interface_body
    : '{' interface_member_declaration* '}'
    ;
```

## <a name="interface-members"></a><span data-ttu-id="7d62d-169">Membres de l’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-169">Interface members</span></span>

<span data-ttu-id="7d62d-170">Les membres d’une interface sont les membres hérités des interfaces de base et les membres déclarés par l’interface elle-même.</span><span class="sxs-lookup"><span data-stu-id="7d62d-170">The members of an interface are the members inherited from the base interfaces and the members declared by the interface itself.</span></span>

```antlr
interface_member_declaration
    : interface_method_declaration
    | interface_property_declaration
    | interface_event_declaration
    | interface_indexer_declaration
    ;
```

<span data-ttu-id="7d62d-171">Une déclaration d’interface peut déclarer zéro ou plusieurs membres.</span><span class="sxs-lookup"><span data-stu-id="7d62d-171">An interface declaration may declare zero or more members.</span></span> <span data-ttu-id="7d62d-172">Les membres d’une interface doivent être des méthodes, propriétés, événements ou indexeurs.</span><span class="sxs-lookup"><span data-stu-id="7d62d-172">The members of an interface must be methods, properties, events, or indexers.</span></span> <span data-ttu-id="7d62d-173">Une interface ne peut pas contenir de constantes, champs, opérateurs, constructeurs d’instance, destructeurs ou types, ni une interface peut contenir les membres statiques d’aucune sorte.</span><span class="sxs-lookup"><span data-stu-id="7d62d-173">An interface cannot contain constants, fields, operators, instance constructors, destructors, or types, nor can an interface contain static members of any kind.</span></span>

<span data-ttu-id="7d62d-174">Tous les membres d’interface sont implicitement publics.</span><span class="sxs-lookup"><span data-stu-id="7d62d-174">All interface members implicitly have public access.</span></span> <span data-ttu-id="7d62d-175">Il s’agit d’une erreur lors de la compilation des déclarations de membre d’interface comprennent des modificateurs.</span><span class="sxs-lookup"><span data-stu-id="7d62d-175">It is a compile-time error for interface member declarations to include any modifiers.</span></span> <span data-ttu-id="7d62d-176">En particulier, les membres d’interface ne peuvent pas être déclarés avec les modificateurs `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, ou `static`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-176">In particular, interfaces members cannot be declared with the modifiers `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, or `static`.</span></span>

<span data-ttu-id="7d62d-177">L’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-177">The example</span></span>
```csharp
public delegate void StringListEvent(IStringList sender);

public interface IStringList
{
    void Add(string s);
    int Count { get; }
    event StringListEvent Changed;
    string this[int index] { get; set; }
}
```
<span data-ttu-id="7d62d-178">déclare une interface qui contient l’un des types de membres possibles : une méthode, une propriété, un événement et un indexeur.</span><span class="sxs-lookup"><span data-stu-id="7d62d-178">declares an interface that contains one each of the possible kinds of members: A method, a property, an event, and an indexer.</span></span>

<span data-ttu-id="7d62d-179">Un *interface_declaration* crée un nouvel espace de déclaration ([déclarations](basic-concepts.md#declarations)) et le *interface_member_declaration*s immédiatement contenus par le *interface_declaration* introduire de nouveaux membres dans cet espace de déclaration.</span><span class="sxs-lookup"><span data-stu-id="7d62d-179">An *interface_declaration* creates a new declaration space ([Declarations](basic-concepts.md#declarations)), and the *interface_member_declaration*s immediately contained by the *interface_declaration* introduce new members into this declaration space.</span></span> <span data-ttu-id="7d62d-180">Les règles suivantes s’appliquent aux *interface_member_declaration*%s :</span><span class="sxs-lookup"><span data-stu-id="7d62d-180">The following rules apply to *interface_member_declaration*s:</span></span>

*  <span data-ttu-id="7d62d-181">Le nom d’une méthode doit différer des noms de toutes les propriétés et les événements déclarés dans la même interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-181">The name of a method must differ from the names of all properties and events declared in the same interface.</span></span> <span data-ttu-id="7d62d-182">En outre, la signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même interface, et deux méthodes déclarées dans la même interface ne peuvent pas avoir signatures qui diffèrent uniquement par `ref` et `out`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-182">In addition, the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of a method must differ from the signatures of all other methods declared in the same interface, and two methods declared in the same interface may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="7d62d-183">Le nom d’une propriété ou un événement doit différer des noms de tous les autres membres déclarés dans la même interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-183">The name of a property or event must differ from the names of all other members declared in the same interface.</span></span>
*  <span data-ttu-id="7d62d-184">La signature d’un indexeur doit se distinguer de tous les autres indexeurs déclarés dans la même interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-184">The signature of an indexer must differ from the signatures of all other indexers declared in the same interface.</span></span>

<span data-ttu-id="7d62d-185">Les membres hérités d’une interface sont spécifiquement ne fait pas partie de l’espace de déclaration de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-185">The inherited members of an interface are specifically not part of the declaration space of the interface.</span></span> <span data-ttu-id="7d62d-186">Par conséquent, une interface est autorisée à déclarer un membre portant le même nom ou la même signature qu’un membre hérité.</span><span class="sxs-lookup"><span data-stu-id="7d62d-186">Thus, an interface is allowed to declare a member with the same name or signature as an inherited member.</span></span> <span data-ttu-id="7d62d-187">Lorsque cela se produit, le membre d’interface dérivée est dite pour masquer le membre de l’interface de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-187">When this occurs, the derived interface member is said to hide the base interface member.</span></span> <span data-ttu-id="7d62d-188">Masquer un membre hérité n'est pas considéré comme une erreur, mais elle entraîne le compilateur à émettre un avertissement.</span><span class="sxs-lookup"><span data-stu-id="7d62d-188">Hiding an inherited member is not considered an error, but it does cause the compiler to issue a warning.</span></span> <span data-ttu-id="7d62d-189">Pour supprimer l’avertissement, la déclaration du membre d’interface dérivée doit inclure un `new` modificateur pour indiquer que le membre dérivé est conçu pour masquer le membre de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-189">To suppress the warning, the declaration of the derived interface member must include a `new` modifier to indicate that the derived member is intended to hide the base member.</span></span> <span data-ttu-id="7d62d-190">Ce sujet est abordé plus en détail dans [masquage par héritage](basic-concepts.md#hiding-through-inheritance).</span><span class="sxs-lookup"><span data-stu-id="7d62d-190">This topic is discussed further in [Hiding through inheritance](basic-concepts.md#hiding-through-inheritance).</span></span>

<span data-ttu-id="7d62d-191">Si un `new` modificateur est inclus dans une déclaration qui ne masque pas un membre hérité, un avertissement est émis à cet effet.</span><span class="sxs-lookup"><span data-stu-id="7d62d-191">If a `new` modifier is included in a declaration that doesn't hide an inherited member, a warning is issued to that effect.</span></span> <span data-ttu-id="7d62d-192">Cet avertissement est supprimé en supprimant le `new` modificateur.</span><span class="sxs-lookup"><span data-stu-id="7d62d-192">This warning is suppressed by removing the `new` modifier.</span></span>

<span data-ttu-id="7d62d-193">Notez que les membres de classe `object` ne sont pas, strictement parlant, les membres de n’importe quelle interface ([membres de l’Interface](interfaces.md#interface-members)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-193">Note that the members in class `object` are not, strictly speaking, members of any interface ([Interface members](interfaces.md#interface-members)).</span></span> <span data-ttu-id="7d62d-194">Toutefois, les membres de classe `object` sont disponibles via la recherche de membres dans n’importe quel type d’interface ([recherche de membres](expressions.md#member-lookup)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-194">However, the members in class `object` are available via member lookup in any interface type ([Member lookup](expressions.md#member-lookup)).</span></span>

### <a name="interface-methods"></a><span data-ttu-id="7d62d-195">Méthodes d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-195">Interface methods</span></span>

<span data-ttu-id="7d62d-196">Méthodes d’interface sont déclarés à l’aide de *interface_method_declaration*%s :</span><span class="sxs-lookup"><span data-stu-id="7d62d-196">Interface methods are declared using *interface_method_declaration*s:</span></span>

```antlr
interface_method_declaration
    : attributes? 'new'? return_type identifier type_parameter_list
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;
```

<span data-ttu-id="7d62d-197">Le *attributs*, *return_type*, *identificateur*, et *formal_parameter_list* d’une déclaration de méthode d’interface ont la même ce qui signifie que ceux d’une déclaration de méthode dans une classe ([méthodes](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-197">The *attributes*, *return_type*, *identifier*, and *formal_parameter_list* of an interface method declaration have the same meaning as those of a method declaration in a class ([Methods](classes.md#methods)).</span></span> <span data-ttu-id="7d62d-198">Une déclaration de méthode d’interface n’est pas autorisée à spécifier un corps de méthode et la déclaration par conséquent se termine toujours par un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-198">An interface method declaration is not permitted to specify a method body, and the declaration therefore always ends with a semicolon.</span></span>

<span data-ttu-id="7d62d-199">Chaque type de paramètre formel d’une méthode d’interface doit être entrée-safe ([sécurité de Variance](interfaces.md#variance-safety)), et le type de retour doit être `void` ou compatibles avec la sortie.</span><span class="sxs-lookup"><span data-stu-id="7d62d-199">Each formal parameter type of an interface method must be input-safe ([Variance safety](interfaces.md#variance-safety)), and the return type must be either `void` or output-safe.</span></span> <span data-ttu-id="7d62d-200">En outre, chaque contrainte de type classe, la contrainte de type interface et la contrainte de paramètre de type sur n’importe quel paramètre de type de la méthode doivent être entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-200">Furthermore, each class type constraint, interface type constraint and type parameter constraint on any type parameter of the method must be input-safe.</span></span>

<span data-ttu-id="7d62d-201">Ces règles garantissent que tout covariant ou contravariant d’utilisation de l’interface reste type sécurisé.</span><span class="sxs-lookup"><span data-stu-id="7d62d-201">These rules ensure that any covariant or contravariant usage of the interface remains type-safe.</span></span> <span data-ttu-id="7d62d-202">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-202">For example,</span></span>
```csharp
interface I<out T> { void M<U>() where U : T; }
```
<span data-ttu-id="7d62d-203">n’est pas conforme, car l’utilisation de `T` comme une contrainte de paramètre de type sur `U` n’est pas entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-203">is illegal because the usage of `T` as a type parameter constraint on `U` is not input-safe.</span></span>

<span data-ttu-id="7d62d-204">Cette restriction n’étaient pas en place il serait possible d’enfreindre la sécurité de type de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="7d62d-204">Were this restriction not in place it would be possible to violate type safety in the following manner:</span></span>
```csharp
class B {}
class D : B{}
class E : B {}
class C : I<D> { public void M<U>() {...} }
...
I<B> b = new C();
b.M<E>();
```
<span data-ttu-id="7d62d-205">Il s’agit en fait un appel à `C.M<E>`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-205">This is actually a call to `C.M<E>`.</span></span> <span data-ttu-id="7d62d-206">Mais cet appel nécessite que `E` dérivent `D`, de sorte que la sécurité de type sera enfreinte ici.</span><span class="sxs-lookup"><span data-stu-id="7d62d-206">But that call requires that `E` derive from `D`, so type safety would be violated here.</span></span>

### <a name="interface-properties"></a><span data-ttu-id="7d62d-207">Propriétés de l'interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-207">Interface properties</span></span>

<span data-ttu-id="7d62d-208">Propriétés d’interface sont déclarées à l’aide de *interface_property_declaration*%s :</span><span class="sxs-lookup"><span data-stu-id="7d62d-208">Interface properties are declared using *interface_property_declaration*s:</span></span>

```antlr
interface_property_declaration
    : attributes? 'new'? type identifier '{' interface_accessors '}'
    ;

interface_accessors
    : attributes? 'get' ';'
    | attributes? 'set' ';'
    | attributes? 'get' ';' attributes? 'set' ';'
    | attributes? 'set' ';' attributes? 'get' ';'
    ;
```

<span data-ttu-id="7d62d-209">Le *attributs*, *type*, et *identificateur* d’une déclaration de propriété d’interface ont la même signification que ceux d’une déclaration de propriété dans une classe ([ Propriétés](classes.md#properties)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-209">The *attributes*, *type*, and *identifier* of an interface property declaration have the same meaning as those of a property declaration in a class ([Properties](classes.md#properties)).</span></span>

<span data-ttu-id="7d62d-210">Les accesseurs d’une déclaration de propriété d’interface correspondent aux accesseurs d’une déclaration de propriété de classe ([accesseurs](classes.md#accessors)), sauf que le corps de l’accesseur doit toujours être un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-210">The accessors of an interface property declaration correspond to the accessors of a class property declaration ([Accessors](classes.md#accessors)), except that the accessor body must always be a semicolon.</span></span> <span data-ttu-id="7d62d-211">Par conséquent, les accesseurs indiquent simplement si la propriété est en lecture-écriture, en lecture seule ou en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-211">Thus, the accessors simply indicate whether the property is read-write, read-only, or write-only.</span></span>

<span data-ttu-id="7d62d-212">Le type d’une propriété d’interface doit être sécurisé de sortie s’il existe un accesseur get et doit être entrée-safe s’il existe un accesseur set.</span><span class="sxs-lookup"><span data-stu-id="7d62d-212">The type of an interface property must be output-safe if there is a get accessor, and must be input-safe if there is a set accessor.</span></span>

### <a name="interface-events"></a><span data-ttu-id="7d62d-213">Événements d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-213">Interface events</span></span>

<span data-ttu-id="7d62d-214">Événements d’interface sont déclarés à l’aide de *interface_event_declaration*%s :</span><span class="sxs-lookup"><span data-stu-id="7d62d-214">Interface events are declared using *interface_event_declaration*s:</span></span>

```antlr
interface_event_declaration
    : attributes? 'new'? 'event' type identifier ';'
    ;
```

<span data-ttu-id="7d62d-215">Le *attributs*, *type*, et *identificateur* d’une déclaration event d’interface ont la même signification que ceux d’une déclaration d’événement dans une classe ([événements ](classes.md#events)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-215">The *attributes*, *type*, and *identifier* of an interface event declaration have the same meaning as those of an event declaration in a class ([Events](classes.md#events)).</span></span>

<span data-ttu-id="7d62d-216">Le type d’un événement d’interface doit être entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-216">The type of an interface event must be input-safe.</span></span>

### <a name="interface-indexers"></a><span data-ttu-id="7d62d-217">Indexeurs d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-217">Interface indexers</span></span>

<span data-ttu-id="7d62d-218">Indexeurs d’interface sont déclarés à l’aide de *interface_indexer_declaration*%s :</span><span class="sxs-lookup"><span data-stu-id="7d62d-218">Interface indexers are declared using *interface_indexer_declaration*s:</span></span>

```antlr
interface_indexer_declaration
    : attributes? 'new'? type 'this' '[' formal_parameter_list ']' '{' interface_accessors '}'
    ;
```

<span data-ttu-id="7d62d-219">Le *attributs*, *type*, et *formal_parameter_list* d’une déclaration d’indexeur interface ont la même signification que ceux d’une déclaration d’indexeur dans une classe ([ Indexeurs](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-219">The *attributes*, *type*, and *formal_parameter_list* of an interface indexer declaration have the same meaning as those of an indexer declaration in a class ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="7d62d-220">Les accesseurs d’une déclaration d’indexeur interface correspondent aux accesseurs d’une déclaration d’indexeur de classe ([indexeurs](classes.md#indexers)), sauf que le corps de l’accesseur doit toujours être un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-220">The accessors of an interface indexer declaration correspond to the accessors of a class indexer declaration ([Indexers](classes.md#indexers)), except that the accessor body must always be a semicolon.</span></span> <span data-ttu-id="7d62d-221">Par conséquent, les accesseurs indiquent simplement si l’indexeur est en lecture-écriture, en lecture seule ou en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="7d62d-221">Thus, the accessors simply indicate whether the indexer is read-write, read-only, or write-only.</span></span>

<span data-ttu-id="7d62d-222">Tous les types de paramètres formels d’un indexeur d’interface doivent être entrée-safe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-222">All the formal parameter types of an interface indexer must be input-safe .</span></span> <span data-ttu-id="7d62d-223">En outre, n’importe quel `out` ou `ref` les types de paramètre formel doivent également être compatibles avec la sortie.</span><span class="sxs-lookup"><span data-stu-id="7d62d-223">In addition, any `out` or `ref` formal parameter types must also be output-safe.</span></span> <span data-ttu-id="7d62d-224">Notez que même `out` paramètres sont requis pour être sécurisé à l’entrée, en raison d’une limitation de la plateforme sous-jacente de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="7d62d-224">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="7d62d-225">Le type d’un indexeur d’interface doit être sécurisé de sortie s’il existe un accesseur get et doit être entrée-safe s’il existe un accesseur set.</span><span class="sxs-lookup"><span data-stu-id="7d62d-225">The type of an interface indexer must be output-safe if there is a get accessor, and must be input-safe if there is a set accessor.</span></span>

### <a name="interface-member-access"></a><span data-ttu-id="7d62d-226">Accès aux membres d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-226">Interface member access</span></span>

<span data-ttu-id="7d62d-227">Membres d’interface sont accessibles via l’accès au membre ([l’accès au membre](expressions.md#member-access)) et accès d’indexeur ([accès indexeur](expressions.md#indexer-access)) expressions de la forme `I.M` et `I[A]`, où `I` est un type interface, `M` est une méthode, une propriété ou un événement de ce type d’interface, et `A` est une liste d’arguments indexeur.</span><span class="sxs-lookup"><span data-stu-id="7d62d-227">Interface members are accessed through member access ([Member access](expressions.md#member-access)) and indexer access ([Indexer access](expressions.md#indexer-access)) expressions of the form `I.M` and `I[A]`, where `I` is an interface type, `M` is a method, property, or event of that interface type, and `A` is an indexer argument list.</span></span>

<span data-ttu-id="7d62d-228">Pour les interfaces qui sont strictement à héritage unique (chaque interface dans la chaîne d’héritage possède exactement zéro ou une interface de base directe), les effets de la recherche de membres ([recherche de membres](expressions.md#member-lookup)), appel de méthode ([ Appels de méthode](expressions.md#method-invocations)) et accès d’indexeur ([accès indexeur](expressions.md#indexer-access)) règles sont exactement les mêmes que pour les classes et structs : masquage de membres plus dérivé moins dérivées membres avec le même nom ou la même signature.</span><span class="sxs-lookup"><span data-stu-id="7d62d-228">For interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effects of the member lookup ([Member lookup](expressions.md#member-lookup)), method invocation ([Method invocations](expressions.md#method-invocations)), and indexer access ([Indexer access](expressions.md#indexer-access)) rules are exactly the same as for classes and structs: More derived members hide less derived members with the same name or signature.</span></span> <span data-ttu-id="7d62d-229">Toutefois, pour les interfaces de l’héritage multiple, ambiguïtés peuvent se produire lorsque deux ou plus des interfaces de base non liées déclarent des membres avec le même nom ou la même signature.</span><span class="sxs-lookup"><span data-stu-id="7d62d-229">However, for multiple-inheritance interfaces, ambiguities can occur when two or more unrelated base interfaces declare members with the same name or signature.</span></span> <span data-ttu-id="7d62d-230">Cette section montre plusieurs exemples de telles situations.</span><span class="sxs-lookup"><span data-stu-id="7d62d-230">This section shows several examples of such situations.</span></span> <span data-ttu-id="7d62d-231">Dans tous les cas, des casts explicites peuvent être utilisés pour résoudre les ambiguïtés.</span><span class="sxs-lookup"><span data-stu-id="7d62d-231">In all cases, explicit casts can be used to resolve the ambiguities.</span></span>

<span data-ttu-id="7d62d-232">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-232">In the example</span></span>
```csharp
interface IList
{
    int Count { get; set; }
}

interface ICounter
{
    void Count(int i);
}

interface IListCounter: IList, ICounter {}

class C
{
    void Test(IListCounter x) {
        x.Count(1);                  // Error
        x.Count = 1;                 // Error
        ((IList)x).Count = 1;        // Ok, invokes IList.Count.set
        ((ICounter)x).Count(1);      // Ok, invokes ICounter.Count
    }
}
```
<span data-ttu-id="7d62d-233">les deux premières instructions provoquent des erreurs de compilation, car la recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `Count` dans `IListCounter` est ambigu.</span><span class="sxs-lookup"><span data-stu-id="7d62d-233">the first two statements cause compile-time errors because the member lookup ([Member lookup](expressions.md#member-lookup)) of `Count` in `IListCounter` is ambiguous.</span></span> <span data-ttu-id="7d62d-234">Comme l’illustre l’exemple, l’ambiguïté est résolue en effectuant un cast `x` au type d’interface de base appropriée.</span><span class="sxs-lookup"><span data-stu-id="7d62d-234">As illustrated by the example, the ambiguity is resolved by casting `x` to the appropriate base interface type.</span></span> <span data-ttu-id="7d62d-235">De telles conversions n’ont aucun coût d’exécution, il s’agit simplement d’affichage de l’instance comme un type moins dérivé au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="7d62d-235">Such casts have no run-time costs—they merely consist of viewing the instance as a less derived type at compile-time.</span></span>

<span data-ttu-id="7d62d-236">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-236">In the example</span></span>
```csharp
interface IInteger
{
    void Add(int i);
}

interface IDouble
{
    void Add(double d);
}

interface INumber: IInteger, IDouble {}

class C
{
    void Test(INumber n) {
        n.Add(1);                // Invokes IInteger.Add
        n.Add(1.0);              // Only IDouble.Add is applicable
        ((IInteger)n).Add(1);    // Only IInteger.Add is a candidate
        ((IDouble)n).Add(1);     // Only IDouble.Add is a candidate
    }
}
```
<span data-ttu-id="7d62d-237">l’appel `n.Add(1)` sélectionne `IInteger.Add` en appliquant les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="7d62d-237">the invocation `n.Add(1)` selects `IInteger.Add` by applying the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="7d62d-238">De même, l’appel `n.Add(1.0)` sélectionne `IDouble.Add`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-238">Similarly the invocation `n.Add(1.0)` selects `IDouble.Add`.</span></span> <span data-ttu-id="7d62d-239">Lorsque des casts explicites sont insérés, il y a des candidate qu’une seule méthode et donc pas d’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="7d62d-239">When explicit casts are inserted, there is only one candidate method, and thus no ambiguity.</span></span>

<span data-ttu-id="7d62d-240">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-240">In the example</span></span>
```csharp
interface IBase
{
    void F(int i);
}

interface ILeft: IBase
{
    new void F(int i);
}

interface IRight: IBase
{
    void G();
}

interface IDerived: ILeft, IRight {}

class A
{
    void Test(IDerived d) {
        d.F(1);                 // Invokes ILeft.F
        ((IBase)d).F(1);        // Invokes IBase.F
        ((ILeft)d).F(1);        // Invokes ILeft.F
        ((IRight)d).F(1);       // Invokes IBase.F
    }
}
```
<span data-ttu-id="7d62d-241">le `IBase.F` membre est masqué par le `ILeft.F` membre.</span><span class="sxs-lookup"><span data-stu-id="7d62d-241">the `IBase.F` member is hidden by the `ILeft.F` member.</span></span> <span data-ttu-id="7d62d-242">L’appel `d.F(1)` sélectionne le `ILeft.F`, même si `IBase.F` semble ne pas être masqué dans le chemin d’accès qui mène à `IRight`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-242">The invocation `d.F(1)` thus selects `ILeft.F`, even though `IBase.F` appears to not be hidden in the access path that leads through `IRight`.</span></span>

<span data-ttu-id="7d62d-243">La règle intuitive de masquage des interfaces à héritage multiple est simple : si un membre est masqué dans n’importe quel chemin d’accès, il est masqué dans tous les chemins d’accès.</span><span class="sxs-lookup"><span data-stu-id="7d62d-243">The intuitive rule for hiding in multiple-inheritance interfaces is simply this: If a member is hidden in any access path, it is hidden in all access paths.</span></span> <span data-ttu-id="7d62d-244">Étant donné que le chemin d’accès à partir de `IDerived` à `ILeft` à `IBase` masque `IBase.F`, le membre est également masqué dans le chemin d’accès à partir de `IDerived` à `IRight` à `IBase`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-244">Because the access path from `IDerived` to `ILeft` to `IBase` hides `IBase.F`, the member is also hidden in the access path from `IDerived` to `IRight` to `IBase`.</span></span>

## <a name="fully-qualified-interface-member-names"></a><span data-ttu-id="7d62d-245">Noms de membre d’interface qualifié complet</span><span class="sxs-lookup"><span data-stu-id="7d62d-245">Fully qualified interface member names</span></span>

<span data-ttu-id="7d62d-246">Un membre d’interface est parfois désigné par son ***nom qualifié complet***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-246">An interface member is sometimes referred to by its ***fully qualified name***.</span></span> <span data-ttu-id="7d62d-247">Le nom qualifié complet d’un membre d’interface se compose du nom de l’interface dans laquelle le membre est déclaré par un point, suivi par le nom du membre.</span><span class="sxs-lookup"><span data-stu-id="7d62d-247">The fully qualified name of an interface member consists of the name of the interface in which the member is declared, followed by a dot, followed by the name of the member.</span></span> <span data-ttu-id="7d62d-248">Le nom qualifié complet d’un membre fait référence à l’interface dans laquelle le membre est déclaré.</span><span class="sxs-lookup"><span data-stu-id="7d62d-248">The fully qualified name of a member references the interface in which the member is declared.</span></span> <span data-ttu-id="7d62d-249">Par exemple, compte tenu des déclarations</span><span class="sxs-lookup"><span data-stu-id="7d62d-249">For example, given the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}
```
<span data-ttu-id="7d62d-250">le nom qualifié complet de `Paint` est `IControl.Paint` et le nom qualifié complet de `SetText` est `ITextBox.SetText`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-250">the fully qualified name of `Paint` is `IControl.Paint` and the fully qualified name of `SetText` is `ITextBox.SetText`.</span></span>

<span data-ttu-id="7d62d-251">Dans l’exemple ci-dessus, il n’est pas possible de faire référence à `Paint` comme `ITextBox.Paint`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-251">In the example above, it is not possible to refer to `Paint` as `ITextBox.Paint`.</span></span>

<span data-ttu-id="7d62d-252">Lorsqu’une interface fait partie d’un espace de noms, le nom qualifié complet d’un membre d’interface inclut le nom de l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="7d62d-252">When an interface is part of a namespace, the fully qualified name of an interface member includes the namespace name.</span></span> <span data-ttu-id="7d62d-253">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-253">For example</span></span>
```csharp
namespace System
{
    public interface ICloneable
    {
        object Clone();
    }
}
```

<span data-ttu-id="7d62d-254">Ici, le nom qualifié complet de le `Clone` méthode est `System.ICloneable.Clone`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-254">Here, the fully qualified name of the `Clone` method is `System.ICloneable.Clone`.</span></span>

## <a name="interface-implementations"></a><span data-ttu-id="7d62d-255">Implémentations d'interfaces</span><span class="sxs-lookup"><span data-stu-id="7d62d-255">Interface implementations</span></span>

<span data-ttu-id="7d62d-256">Interfaces peuvent être implémentées par les classes et structs.</span><span class="sxs-lookup"><span data-stu-id="7d62d-256">Interfaces may be implemented by classes and structs.</span></span> <span data-ttu-id="7d62d-257">Pour indiquer qu’une classe ou un struct implémente directement une interface, l’identificateur d’interface est inclus dans la liste de classe de base de la classe ou structure.</span><span class="sxs-lookup"><span data-stu-id="7d62d-257">To indicate that a class or struct directly implements an interface, the interface identifier is included in the base class list of the class or struct.</span></span> <span data-ttu-id="7d62d-258">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-258">For example:</span></span>
```csharp
interface ICloneable
{
    object Clone();
}

interface IComparable
{
    int CompareTo(object other);
}

class ListEntry: ICloneable, IComparable
{
    public object Clone() {...}
    public int CompareTo(object other) {...}
}
```

<span data-ttu-id="7d62d-259">Une classe ou un struct qui implémente directement une interface également directement implémente toutes les interfaces de base de l’interface implicitement.</span><span class="sxs-lookup"><span data-stu-id="7d62d-259">A class or struct that directly implements an interface also directly implements all of the interface's base interfaces implicitly.</span></span> <span data-ttu-id="7d62d-260">Cela est vrai même si la classe ou structure ne répertorie pas explicitement toutes les interfaces de base dans la liste de classe de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-260">This is true even if the class or struct doesn't explicitly list all base interfaces in the base class list.</span></span> <span data-ttu-id="7d62d-261">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-261">For example:</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    public void Paint() {...}
    public void SetText(string text) {...}
}
```

<span data-ttu-id="7d62d-262">Ici, la classe `TextBox` implémente à la fois `IControl` et `ITextBox`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-262">Here, class `TextBox` implements both `IControl` and `ITextBox`.</span></span>

<span data-ttu-id="7d62d-263">Lorsqu’une classe `C` directement implémente une interface, toutes les classes dérivées de C également implémente l’interface implicitement.</span><span class="sxs-lookup"><span data-stu-id="7d62d-263">When a class `C` directly implements an interface, all classes derived from C also implement the interface implicitly.</span></span> <span data-ttu-id="7d62d-264">Les interfaces de base spécifiés dans une déclaration de classe peuvent être des types d’interface construite ([types construits](types.md#constructed-types)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-264">The base interfaces specified in a class declaration can be constructed interface types ([Constructed types](types.md#constructed-types)).</span></span> <span data-ttu-id="7d62d-265">Une interface de base ne peut pas être un paramètre de type sur son propre, même si elle peut impliquer les paramètres de type qui sont dans la portée.</span><span class="sxs-lookup"><span data-stu-id="7d62d-265">A base interface cannot be a type parameter on its own, though it can involve the type parameters that are in scope.</span></span> <span data-ttu-id="7d62d-266">Le code suivant illustre comment une classe peut implémenter et étendre des types construits :</span><span class="sxs-lookup"><span data-stu-id="7d62d-266">The following code illustrates how a class can implement and extend constructed types:</span></span>
```csharp
class C<U,V> {}

interface I1<V> {}

class D: C<string,int>, I1<string> {}

class E<T>: C<int,T>, I1<T> {}
```

<span data-ttu-id="7d62d-267">Les interfaces de base d’une déclaration de classe générique doivent respecter la règle de l’unicité décrite dans [l’unicité des interfaces implémentées](interfaces.md#uniqueness-of-implemented-interfaces).</span><span class="sxs-lookup"><span data-stu-id="7d62d-267">The base interfaces of a generic class declaration must satisfy the uniqueness rule described in [Uniqueness of implemented interfaces](interfaces.md#uniqueness-of-implemented-interfaces).</span></span>

### <a name="explicit-interface-member-implementations"></a><span data-ttu-id="7d62d-268">Implémentations de membres d’interface explicite</span><span class="sxs-lookup"><span data-stu-id="7d62d-268">Explicit interface member implementations</span></span>

<span data-ttu-id="7d62d-269">Dans le cadre de l’implémentation des interfaces, une classe ou un struct peut déclarer ***implémentations de membres d’interface explicite***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-269">For purposes of implementing interfaces, a class or struct may declare ***explicit interface member implementations***.</span></span> <span data-ttu-id="7d62d-270">Une implémentation de membre d’interface explicite est une déclaration de méthode, propriété, événement ou l’indexeur qui fait référence à un nom de membre d’interface qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="7d62d-270">An explicit interface member implementation is a method, property, event, or indexer declaration that references a fully qualified interface member name.</span></span> <span data-ttu-id="7d62d-271">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-271">For example</span></span>
```csharp
interface IList<T>
{
    T[] GetElements();
}

interface IDictionary<K,V>
{
    V this[K key];
    void Add(K key, V value);
}

class List<T>: IList<T>, IDictionary<int,T>
{
    T[] IList<T>.GetElements() {...}
    T IDictionary<int,T>.this[int index] {...}
    void IDictionary<int,T>.Add(int index, T value) {...}
}
```

<span data-ttu-id="7d62d-272">Ici `IDictionary<int,T>.this` et `IDictionary<int,T>.Add` est des implémentations de membres d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-272">Here `IDictionary<int,T>.this` and `IDictionary<int,T>.Add` are explicit interface member implementations.</span></span>

<span data-ttu-id="7d62d-273">Dans certains cas, le nom d’un membre d’interface n’est peut-être pas approprié pour la classe d’implémentation, dans lequel cas le membre d’interface peut être implémentée à l’aide d’implémentation de membre d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-273">In some cases, the name of an interface member may not be appropriate for the implementing class, in which case the interface member may be implemented using explicit interface member implementation.</span></span> <span data-ttu-id="7d62d-274">Implémente une classe qui implémente une abstraction de fichier, par exemple, probablement un `Close` fonction membre qui a pour effet de libérer la ressource de fichier et implémenter le `Dispose` méthode de la `IDisposable` interface à l’aide d’interface explicite implémentation de membre :</span><span class="sxs-lookup"><span data-stu-id="7d62d-274">A class implementing a file abstraction, for example, would likely implement a `Close` member function that has the effect of releasing the file resource, and implement the `Dispose` method of the `IDisposable` interface using explicit interface member implementation:</span></span>
```csharp
interface IDisposable
{
    void Dispose();
}

class MyFile: IDisposable
{
    void IDisposable.Dispose() {
        Close();
    }

    public void Close() {
        // Do what's necessary to close the file
        System.GC.SuppressFinalize(this);
    }
}
```

<span data-ttu-id="7d62d-275">Il n’est pas possible d’accéder à une implémentation de membre d’interface explicite via son nom qualifié complet dans un appel de méthode, propriété ou accès indexeur.</span><span class="sxs-lookup"><span data-stu-id="7d62d-275">It is not possible to access an explicit interface member implementation through its fully qualified name in a method invocation, property access, or indexer access.</span></span> <span data-ttu-id="7d62d-276">Une implémentation de membre d’interface explicite est uniquement accessible via une instance d’interface et est référencée dans ce cas simplement par son nom de membre.</span><span class="sxs-lookup"><span data-stu-id="7d62d-276">An explicit interface member implementation can only be accessed through an interface instance, and is in that case referenced simply by its member name.</span></span>

<span data-ttu-id="7d62d-277">Il s’agit d’une erreur de compilation pour une implémentation de membre d’interface explicite inclure des modificateurs d’accès, et c’est une erreur de compilation pour inclure les modificateurs `abstract`, `virtual`, `override`, ou `static`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-277">It is a compile-time error for an explicit interface member implementation to include access modifiers, and it is a compile-time error to include the modifiers `abstract`, `virtual`, `override`, or `static`.</span></span>

<span data-ttu-id="7d62d-278">Implémentations de membres d’interface explicites ont des caractéristiques d’une accessibilité différente que d’autres membres.</span><span class="sxs-lookup"><span data-stu-id="7d62d-278">Explicit interface member implementations have different accessibility characteristics than other members.</span></span> <span data-ttu-id="7d62d-279">Étant donné que les implémentations de membres d’interface explicites ne sont jamais accessibles par leur nom qualifié complet dans un appel de méthode ou d’un accès à la propriété, elles se trouvent dans un sens privé.</span><span class="sxs-lookup"><span data-stu-id="7d62d-279">Because explicit interface member implementations are never accessible through their fully qualified name in a method invocation or a property access, they are in a sense private.</span></span> <span data-ttu-id="7d62d-280">Toutefois, dans la mesure où ils sont accessibles via une instance d’interface, ils sont dans un sens également public.</span><span class="sxs-lookup"><span data-stu-id="7d62d-280">However, since they can be accessed through an interface instance, they are in a sense also public.</span></span>

<span data-ttu-id="7d62d-281">Implémentations de membres d’interface explicites ont pour objectif principal :</span><span class="sxs-lookup"><span data-stu-id="7d62d-281">Explicit interface member implementations serve two primary purposes:</span></span>

*  <span data-ttu-id="7d62d-282">Étant donné que les implémentations de membres d’interface explicites ne sont pas accessibles via des instances de classe ou un struct, ils permettent d’implémentations d’interface à exclure de l’interface publique d’une classe ou un struct.</span><span class="sxs-lookup"><span data-stu-id="7d62d-282">Because explicit interface member implementations are not accessible through class or struct instances, they allow interface implementations to be excluded from the public interface of a class or struct.</span></span> <span data-ttu-id="7d62d-283">Cela est particulièrement utile lorsqu’une classe ou struct implémente une interface interne qui ne présente aucun intérêt pour le consommateur de cette classe ou struct.</span><span class="sxs-lookup"><span data-stu-id="7d62d-283">This is particularly useful when a class or struct implements an internal interface that is of no interest to a consumer of that class or struct.</span></span>
*  <span data-ttu-id="7d62d-284">Implémentations de membres d’interface explicites permettent de lever l’ambiguïté de membres d’interface avec la même signature.</span><span class="sxs-lookup"><span data-stu-id="7d62d-284">Explicit interface member implementations allow disambiguation of interface members with the same signature.</span></span> <span data-ttu-id="7d62d-285">Sans les implémentations de membres d’interface explicite il serait impossible pour une classe ou un struct d’avoir différentes implémentations de membres avec la même signature de l’interface et le type de retour, comme il serait impossible pour une classe ou un struct d’avoir une implémentation tous les membres d’interface avec la même signature, mais avec différents types de retournés.</span><span class="sxs-lookup"><span data-stu-id="7d62d-285">Without explicit interface member implementations it would be impossible for a class or struct to have different implementations of interface members with the same signature and return type, as would it be impossible for a class or struct to have any implementation at all of interface members with the same signature but with different return types.</span></span>

<span data-ttu-id="7d62d-286">Pour une implémentation de membre d’interface explicite soit valide, la classe ou structure doit nommer une interface dans sa liste de classe de base qui contient un membre dont le nom qualifié complet, type et les types de paramètres correspondent exactement à ceux du membre d’interface explicite mise en œuvre.</span><span class="sxs-lookup"><span data-stu-id="7d62d-286">For an explicit interface member implementation to be valid, the class or struct must name an interface in its base class list that contains a member whose fully qualified name, type, and parameter types exactly match those of the explicit interface member implementation.</span></span> <span data-ttu-id="7d62d-287">Par conséquent, dans la classe suivante</span><span class="sxs-lookup"><span data-stu-id="7d62d-287">Thus, in the following class</span></span>
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
    int IComparable.CompareTo(object other) {...}    // invalid
}
```
<span data-ttu-id="7d62d-288">la déclaration de `IComparable.CompareTo` génère une erreur de compilation, car `IComparable` n’est pas répertorié dans la liste de classe de base de `Shape` et n’est pas une interface de base de `ICloneable`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-288">the declaration of `IComparable.CompareTo` results in a compile-time error because `IComparable` is not listed in the base class list of `Shape` and is not a base interface of `ICloneable`.</span></span> <span data-ttu-id="7d62d-289">De même, dans les déclarations</span><span class="sxs-lookup"><span data-stu-id="7d62d-289">Likewise, in the declarations</span></span>
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
}

class Ellipse: Shape
{
    object ICloneable.Clone() {...}    // invalid
}
```
<span data-ttu-id="7d62d-290">la déclaration de `ICloneable.Clone` dans `Ellipse` génère une erreur de compilation, car `ICloneable` n’est pas explicitement répertorié dans la liste de classe de base de `Ellipse`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-290">the declaration of `ICloneable.Clone` in `Ellipse` results in a compile-time error because `ICloneable` is not explicitly listed in the base class list of `Ellipse`.</span></span>

<span data-ttu-id="7d62d-291">Le nom qualifié complet d’un membre d’interface doit faire référence à l’interface dans laquelle le membre a été déclaré.</span><span class="sxs-lookup"><span data-stu-id="7d62d-291">The fully qualified name of an interface member must reference the interface in which the member was declared.</span></span> <span data-ttu-id="7d62d-292">Par conséquent, dans les déclarations</span><span class="sxs-lookup"><span data-stu-id="7d62d-292">Thus, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
}
```
<span data-ttu-id="7d62d-293">l’implémentation de membre d’interface explicite de `Paint` doit être écrit en tant que `IControl.Paint`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-293">the explicit interface member implementation of `Paint` must be written as `IControl.Paint`.</span></span>

### <a name="uniqueness-of-implemented-interfaces"></a><span data-ttu-id="7d62d-294">Unicité des interfaces implémentées</span><span class="sxs-lookup"><span data-stu-id="7d62d-294">Uniqueness of implemented interfaces</span></span>

<span data-ttu-id="7d62d-295">Les interfaces implémentées par une déclaration de type générique doivent rester uniques pour tous les types construits possible.</span><span class="sxs-lookup"><span data-stu-id="7d62d-295">The interfaces implemented by a generic type declaration must remain unique for all possible constructed types.</span></span> <span data-ttu-id="7d62d-296">Sans cette règle, il serait impossible de déterminer la méthode à appeler pour certains types construits.</span><span class="sxs-lookup"><span data-stu-id="7d62d-296">Without this rule, it would be impossible to determine the correct method to call for certain constructed types.</span></span> <span data-ttu-id="7d62d-297">Par exemple, une déclaration de classe générique ont été autorisés à être écrit comme suit :</span><span class="sxs-lookup"><span data-stu-id="7d62d-297">For example, suppose a generic class declaration were permitted to be written as follows:</span></span>
```csharp
interface I<T>
{
    void F();
}

class X<U,V>: I<U>, I<V>                    // Error: I<U> and I<V> conflict
{
    void I<U>.F() {...}
    void I<V>.F() {...}
}
```

<span data-ttu-id="7d62d-298">Ont été cela autorisé, il serait impossible de déterminer le code à exécuter dans le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="7d62d-298">Were this permitted, it would be impossible to determine which code to execute in the following case:</span></span>
```csharp
I<int> x = new X<int,int>();
x.F();
```

<span data-ttu-id="7d62d-299">Pour déterminer si la liste de l’interface d’une déclaration de type générique est valide, les étapes suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="7d62d-299">To determine if the interface list of a generic type declaration is valid, the following steps are performed:</span></span>

*  <span data-ttu-id="7d62d-300">Laisser `L` la liste des interfaces spécifiées directement dans une classe générique, un struct ou une déclaration d’interface `C`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-300">Let `L` be the list of interfaces directly specified in a generic class, struct, or interface declaration `C`.</span></span>
*  <span data-ttu-id="7d62d-301">Ajouter à `L` les interfaces des interfaces déjà de base `L`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-301">Add to `L` any base interfaces of the interfaces already in `L`.</span></span>
*  <span data-ttu-id="7d62d-302">Supprimer les doublons de `L`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-302">Remove any duplicates from `L`.</span></span>
*  <span data-ttu-id="7d62d-303">Si tout construit type créé à partir de `C` serait, une fois que les arguments de type sont substitués dans `L`, provoquer deux interfaces dans `L` sont identiques, puis la déclaration de `C` n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="7d62d-303">If any possible constructed type created from `C` would, after type arguments are substituted into `L`, cause two interfaces in `L` to be identical, then the declaration of `C` is invalid.</span></span> <span data-ttu-id="7d62d-304">Déclarations de contrainte ne sont pas considérées lors de la détermination de tous les types construits possible.</span><span class="sxs-lookup"><span data-stu-id="7d62d-304">Constraint declarations are not considered when determining all possible constructed types.</span></span>

<span data-ttu-id="7d62d-305">Dans la déclaration de classe `X` ci-dessus, la liste des interfaces `L` se compose de `I<U>` et `I<V>`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-305">In the class declaration `X` above, the interface list `L` consists of `I<U>` and `I<V>`.</span></span> <span data-ttu-id="7d62d-306">La déclaration n’est pas valide, car un type avec construit `U` et `V` en cours du même type entraînerait ces deux interfaces comme des types identiques.</span><span class="sxs-lookup"><span data-stu-id="7d62d-306">The declaration is invalid because any constructed type with `U` and `V` being the same type would cause these two interfaces to be identical types.</span></span>

<span data-ttu-id="7d62d-307">Il est possible pour les interfaces spécifiées aux niveaux d’héritage différents pour unifier :</span><span class="sxs-lookup"><span data-stu-id="7d62d-307">It is possible for interfaces specified at different inheritance levels to unify:</span></span>
```csharp
interface I<T>
{
    void F();
}

class Base<U>: I<U>
{
    void I<U>.F() {...}
}

class Derived<U,V>: Base<U>, I<V>    // Ok
{
    void I<V>.F() {...}
}
```

<span data-ttu-id="7d62d-308">Ce code est valid, même si `Derived<U,V>` implémente à la fois `I<U>` et `I<V>`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-308">This code is valid even though `Derived<U,V>` implements both `I<U>` and `I<V>`.</span></span> <span data-ttu-id="7d62d-309">Le code</span><span class="sxs-lookup"><span data-stu-id="7d62d-309">The code</span></span>
```csharp
I<int> x = new Derived<int,int>();
x.F();
```
<span data-ttu-id="7d62d-310">appelle la méthode dans `Derived`, étant donné que `Derived<int,int>` efficacement ré-implémente `I<int>` ([réimplémenter l’Interface](interfaces.md#interface-re-implementation)).</span><span class="sxs-lookup"><span data-stu-id="7d62d-310">invokes the method in `Derived`, since `Derived<int,int>` effectively re-implements `I<int>` ([Interface re-implementation](interfaces.md#interface-re-implementation)).</span></span>

### <a name="implementation-of-generic-methods"></a><span data-ttu-id="7d62d-311">Implémentation des méthodes génériques</span><span class="sxs-lookup"><span data-stu-id="7d62d-311">Implementation of generic methods</span></span>

<span data-ttu-id="7d62d-312">Lorsqu’une méthode générique implicitement implémente une méthode d’interface, les contraintes données chaque paramètre de type de méthode doit être équivalente dans les deux déclarations (après n’importe quel type d’interface paramètres sont remplacés par les arguments de type approprié), où (méthode) paramètres de type sont identifiées par les positions ordinales, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-312">When a generic method implicitly implements an interface method, the constraints given for each method type parameter must be equivalent in both declarations (after any interface type parameters are replaced with the appropriate type arguments), where method type parameters are identified by ordinal positions, left to right.</span></span>

<span data-ttu-id="7d62d-313">Lorsqu’une méthode générique implémente explicitement une méthode d’interface, toutefois, aucune contrainte n’est autorisées sur la méthode d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="7d62d-313">When a generic method explicitly implements an interface method, however, no constraints are allowed on the implementing method.</span></span> <span data-ttu-id="7d62d-314">Au lieu de cela, les contraintes sont héritées de la méthode d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-314">Instead, the constraints are inherited from the interface method</span></span>

```csharp
interface I<A,B,C>
{
    void F<T>(T t) where T: A;
    void G<T>(T t) where T: B;
    void H<T>(T t) where T: C;
}

class C: I<object,C,string>
{
    public void F<T>(T t) {...}                    // Ok
    public void G<T>(T t) where T: C {...}         // Ok
    public void H<T>(T t) where T: string {...}    // Error
}
```

<span data-ttu-id="7d62d-315">La méthode `C.F<T>` implémente implicitement les `I<object,C,string>.F<T>`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-315">The method `C.F<T>` implicitly implements `I<object,C,string>.F<T>`.</span></span> <span data-ttu-id="7d62d-316">Dans ce cas, `C.F<T>` n’est pas nécessaire (ni autorisés) pour spécifier la contrainte `T:object` dans la mesure où `object` est une contrainte implicite sur tous les paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="7d62d-316">In this case, `C.F<T>` is not required (nor permitted) to specify the constraint `T:object` since `object` is an implicit constraint on all type parameters.</span></span> <span data-ttu-id="7d62d-317">La méthode `C.G<T>` implémente implicitement `I<object,C,string>.G<T>` , car les contraintes correspondent à celles dans l’interface, une fois que les paramètres de type d’interface sont remplacés par les arguments de type correspondant.</span><span class="sxs-lookup"><span data-stu-id="7d62d-317">The method `C.G<T>` implicitly implements `I<object,C,string>.G<T>` because the constraints match those in the interface, after the interface type parameters are replaced with the corresponding type arguments.</span></span> <span data-ttu-id="7d62d-318">La contrainte pour la méthode `C.H<T>` est une erreur, car les types sealed (`string` dans ce cas) ne peut pas être utilisé en tant que contraintes.</span><span class="sxs-lookup"><span data-stu-id="7d62d-318">The constraint for method `C.H<T>` is an error because sealed types (`string` in this case) cannot be used as constraints.</span></span> <span data-ttu-id="7d62d-319">L’omission de la contrainte également serait une erreur, car les contraintes d’implémentations de méthode d’interface implicite sont requises pour faire correspondre.</span><span class="sxs-lookup"><span data-stu-id="7d62d-319">Omitting the constraint would also be an error since constraints of implicit interface method implementations are required to match.</span></span> <span data-ttu-id="7d62d-320">Par conséquent, il est impossible d’implémenter implicitement `I<object,C,string>.H<T>`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-320">Thus, it is impossible to implicitly implement `I<object,C,string>.H<T>`.</span></span> <span data-ttu-id="7d62d-321">Cette méthode d’interface peut uniquement être implémentée à l’aide d’une implémentation de membre d’interface explicite :</span><span class="sxs-lookup"><span data-stu-id="7d62d-321">This interface method can only be implemented using an explicit interface member implementation:</span></span>
```csharp
class C: I<object,C,string>
{
    ...

    public void H<U>(U u) where U: class {...}

    void I<object,C,string>.H<T>(T t) {
        string s = t;    // Ok
        H<T>(t);
    }
}
```

<span data-ttu-id="7d62d-322">Dans cet exemple, l’implémentation de membre d’interface explicite appelle une méthode publique avec des contraintes strictement plus faibles.</span><span class="sxs-lookup"><span data-stu-id="7d62d-322">In this example, the explicit interface member implementation invokes a public method having strictly weaker constraints.</span></span> <span data-ttu-id="7d62d-323">Notez que l’affectation de `t` à `s` est valide depuis `T` hérite d’une contrainte de `T:string`, même si cette contrainte n’est pas pouvant être exprimée dans le code source.</span><span class="sxs-lookup"><span data-stu-id="7d62d-323">Note that the assignment from `t` to `s` is valid since `T` inherits a constraint of `T:string`, even though this constraint is not expressible in source code.</span></span>

### <a name="interface-mapping"></a><span data-ttu-id="7d62d-324">Mappage d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-324">Interface mapping</span></span>

<span data-ttu-id="7d62d-325">Une classe ou un struct doit fournir des implémentations de tous les membres des interfaces qui sont répertoriées dans la liste de classe de base de la classe ou structure.</span><span class="sxs-lookup"><span data-stu-id="7d62d-325">A class or struct must provide implementations of all members of the interfaces that are listed in the base class list of the class or struct.</span></span> <span data-ttu-id="7d62d-326">Le processus de localisation des implémentations de membres d’interface dans une classe ou struct d’implémentation est appelé ***mappage d’interface***.</span><span class="sxs-lookup"><span data-stu-id="7d62d-326">The process of locating implementations of interface members in an implementing class or struct is known as ***interface mapping***.</span></span>

<span data-ttu-id="7d62d-327">Mappage d’interface pour une classe ou un struct `C` recherche une implémentation pour chaque membre de chaque interface spécifiée dans la liste de classe de base de `C`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-327">Interface mapping for a class or struct `C` locates an implementation for each member of each interface specified in the base class list of `C`.</span></span> <span data-ttu-id="7d62d-328">L’implémentation d’un membre d’interface particulier `I.M`, où `I` est l’interface dans laquelle le membre `M` est déclaré, est déterminée en examinant chaque classe ou struct `S`, en commençant par `C` et répétition pour chaque classe de base consécutive de `C`, jusqu'à ce qu’il trouve une correspondance :</span><span class="sxs-lookup"><span data-stu-id="7d62d-328">The implementation of a particular interface member `I.M`, where `I` is the interface in which the member `M` is declared, is determined by examining each class or struct `S`, starting with `C` and repeating for each successive base class of `C`, until a match is located:</span></span>

*  <span data-ttu-id="7d62d-329">Si `S` contient une déclaration d’une implémentation de membre d’interface explicite qui correspond à `I` et `M`, ce membre est l’implémentation de `I.M`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-329">If `S` contains a declaration of an explicit interface member implementation that matches `I` and `M`, then this member is the implementation of `I.M`.</span></span>
*  <span data-ttu-id="7d62d-330">Sinon, si `S` contient une déclaration d’un membre public non statique qui correspond à `M`, ce membre est l’implémentation de `I.M`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-330">Otherwise, if `S` contains a declaration of a non-static public member that matches `M`, then this member is the implementation of `I.M`.</span></span> <span data-ttu-id="7d62d-331">Si plusieurs correspondances d’un seul membre, il n’est pas spécifié le membre qui est l’implémentation de `I.M`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-331">If more than one member matches, it is unspecified which member is the implementation of `I.M`.</span></span> <span data-ttu-id="7d62d-332">Cette situation peut se produire uniquement si `S` est un type construit, où les deux membres tel que déclaré dans le type générique ont des signatures différentes, mais les arguments de type que leurs signatures identique.</span><span class="sxs-lookup"><span data-stu-id="7d62d-332">This situation can only occur if `S` is a constructed type where the two members as declared in the generic type have different signatures, but the type arguments make their signatures identical.</span></span>

<span data-ttu-id="7d62d-333">Une erreur de compilation se produit si les implémentations ne peut pas être localisées pour tous les membres de toutes les interfaces spécifiées dans la liste de classe de base de `C`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-333">A compile-time error occurs if implementations cannot be located for all members of all interfaces specified in the base class list of `C`.</span></span> <span data-ttu-id="7d62d-334">Notez que les membres d’une interface incluent les membres qui sont héritées d’interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-334">Note that the members of an interface include those members that are inherited from base interfaces.</span></span>

<span data-ttu-id="7d62d-335">À des fins de mappage d’interface, un membre de classe `A` correspond à un membre d’interface `B` lorsque :</span><span class="sxs-lookup"><span data-stu-id="7d62d-335">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>

*  <span data-ttu-id="7d62d-336">`A` et `B` sont des méthodes et le nom, le type, et les listes de paramètres formels de `A` et `B` sont identiques.</span><span class="sxs-lookup"><span data-stu-id="7d62d-336">`A` and `B` are methods, and the name, type, and formal parameter lists of `A` and `B` are identical.</span></span>
*  <span data-ttu-id="7d62d-337">`A` et `B` sont des propriétés, le nom et le type de `A` et `B` sont identiques, et `A` a les mêmes accesseurs que `B` (`A` est autorisé à avoir des accesseurs supplémentaires s’il n’est pas une interface explicite implémentation de membre).</span><span class="sxs-lookup"><span data-stu-id="7d62d-337">`A` and `B` are properties, the name and type of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>
*  <span data-ttu-id="7d62d-338">`A` et `B` sont des événements et le nom et le type de `A` et `B` sont identiques.</span><span class="sxs-lookup"><span data-stu-id="7d62d-338">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
*  <span data-ttu-id="7d62d-339">`A` et `B` sont des indexeurs, le type et les listes de paramètres formels de `A` et `B` sont identiques, et `A` a les mêmes accesseurs que `B` (`A` est autorisé à avoir des accesseurs supplémentaires s’il n’est pas un implémentation de membre d’interface explicite).</span><span class="sxs-lookup"><span data-stu-id="7d62d-339">`A` and `B` are indexers, the type and formal parameter lists of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>

<span data-ttu-id="7d62d-340">Les implications notables de l’algorithme de mappage d’interface sont :</span><span class="sxs-lookup"><span data-stu-id="7d62d-340">Notable implications of the interface mapping algorithm are:</span></span>

*  <span data-ttu-id="7d62d-341">Implémentations de membres d’interface explicites sont prioritaires sur les autres membres dans la même classe ou struct lors de la détermination du membre de classe ou struct qui implémente un membre d’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-341">Explicit interface member implementations take precedence over other members in the same class or struct when determining the class or struct member that implements an interface member.</span></span>
*  <span data-ttu-id="7d62d-342">Les membres non publics ni statiques participent au mappage d’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-342">Neither non-public nor static members participate in interface mapping.</span></span>

<span data-ttu-id="7d62d-343">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-343">In the example</span></span>
```csharp
interface ICloneable
{
    object Clone();
}

class C: ICloneable
{
    object ICloneable.Clone() {...}
    public object Clone() {...}
}
```
<span data-ttu-id="7d62d-344">le `ICloneable.Clone` membre `C` devient l’implémentation de `Clone` dans `ICloneable` , car les implémentations de membres d’interface explicites sont prioritaires sur les autres membres.</span><span class="sxs-lookup"><span data-stu-id="7d62d-344">the `ICloneable.Clone` member of `C` becomes the implementation of `Clone` in `ICloneable` because explicit interface member implementations take precedence over other members.</span></span>

<span data-ttu-id="7d62d-345">Si une classe ou un struct implémente deux ou plusieurs interfaces contenant un membre avec le même nom, type et les types de paramètre, il est possible de mapper chacun de ces membres d’interface à un seul membre de classe ou un struct.</span><span class="sxs-lookup"><span data-stu-id="7d62d-345">If a class or struct implements two or more interfaces containing a member with the same name, type, and parameter types, it is possible to map each of those interface members onto a single class or struct member.</span></span> <span data-ttu-id="7d62d-346">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-346">For example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface IForm
{
    void Paint();
}

class Page: IControl, IForm
{
    public void Paint() {...}
}
```

<span data-ttu-id="7d62d-347">Ici, le `Paint` méthodes des deux `IControl` et `IForm` sont mappées sur le `Paint` méthode dans `Page`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-347">Here, the `Paint` methods of both `IControl` and `IForm` are mapped onto the `Paint` method in `Page`.</span></span> <span data-ttu-id="7d62d-348">Il est bien sûr également possible d’avoir des implémentations de membres d’interface explicite distincte pour les deux méthodes.</span><span class="sxs-lookup"><span data-stu-id="7d62d-348">It is of course also possible to have separate explicit interface member implementations for the two methods.</span></span>

<span data-ttu-id="7d62d-349">Si une classe ou un struct implémente une interface qui contient les membres masqués, certains membres doivent nécessairement être implémentés via des implémentations de membres d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-349">If a class or struct implements an interface that contains hidden members, then some members must necessarily be implemented through explicit interface member implementations.</span></span> <span data-ttu-id="7d62d-350">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-350">For example</span></span>
```csharp
interface IBase
{
    int P { get; }
}

interface IDerived: IBase
{
    new int P();
}
```

<span data-ttu-id="7d62d-351">Une implémentation de cette interface nécessite au moins une implémentation de membre d’interface explicite et prendrait l’une des formes suivantes</span><span class="sxs-lookup"><span data-stu-id="7d62d-351">An implementation of this interface would require at least one explicit interface member implementation, and would take one of the following forms</span></span>
```csharp
class C: IDerived
{
    int IBase.P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    public int P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    int IBase.P { get {...} }
    public int P() {...}
}
```

<span data-ttu-id="7d62d-352">Lorsqu’une classe implémente plusieurs interfaces qui ont la même interface de base, il peut y avoir qu’une seule implémentation de l’interface de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-352">When a class implements multiple interfaces that have the same base interface, there can be only one implementation of the base interface.</span></span> <span data-ttu-id="7d62d-353">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-353">In the example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

class ComboBox: IControl, ITextBox, IListBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
    void IListBox.SetItems(string[] items) {...}
}
```
<span data-ttu-id="7d62d-354">Il n’est pas possible d’avoir des implémentations distinctes pour le `IControl` nommé dans la liste de classe de base, le `IControl` héritée par `ITextBox`et le `IControl` héritée par `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-354">it is not possible to have separate implementations for the `IControl` named in the base class list, the `IControl` inherited by `ITextBox`, and the `IControl` inherited by `IListBox`.</span></span> <span data-ttu-id="7d62d-355">En effet, il n’existe aucune notion d’identité séparée pour ces interfaces.</span><span class="sxs-lookup"><span data-stu-id="7d62d-355">Indeed, there is no notion of a separate identity for these interfaces.</span></span> <span data-ttu-id="7d62d-356">Au lieu de cela, les implémentations de `ITextBox` et `IListBox` partagent la même implémentation de `IControl`, et `ComboBox` est considéré comme il vous suffit d’implémenter trois interfaces, `IControl`, `ITextBox`, et `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-356">Rather, the implementations of `ITextBox` and `IListBox` share the same implementation of `IControl`, and `ComboBox` is simply considered to implement three interfaces, `IControl`, `ITextBox`, and `IListBox`.</span></span>

<span data-ttu-id="7d62d-357">Les membres de classe de base participent au mappage d’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-357">The members of a base class participate in interface mapping.</span></span> <span data-ttu-id="7d62d-358">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="7d62d-358">In the example</span></span>
```csharp
interface Interface1
{
    void F();
}

class Class1
{
    public void F() {}
    public void G() {}
}

class Class2: Class1, Interface1
{
    new public void G() {}
}
```
<span data-ttu-id="7d62d-359">la méthode `F` dans `Class1` est utilisée dans `Class2`d’implémentation de `Interface1`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-359">the method `F` in `Class1` is used in `Class2`'s implementation of `Interface1`.</span></span>

### <a name="interface-implementation-inheritance"></a><span data-ttu-id="7d62d-360">Héritage de l’implémentation d’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-360">Interface implementation inheritance</span></span>

<span data-ttu-id="7d62d-361">Une classe hérite de toutes les implémentations d’interface fournies par ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-361">A class inherits all interface implementations provided by its base classes.</span></span>

<span data-ttu-id="7d62d-362">Sans explicitement ***ré-implémenter*** une interface, une classe dérivée ne peut en aucun cas modifier les mappages d’interface hérite de ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-362">Without explicitly ***re-implementing*** an interface, a derived class cannot in any way alter the interface mappings it inherits from its base classes.</span></span> <span data-ttu-id="7d62d-363">Par exemple, dans les déclarations</span><span class="sxs-lookup"><span data-stu-id="7d62d-363">For example, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public void Paint() {...}
}

class TextBox: Control
{
    new public void Paint() {...}
}
```
<span data-ttu-id="7d62d-364">le `Paint` méthode dans `TextBox` masque la `Paint` méthode dans `Control`, mais il ne modifie pas le mappage de `Control.Paint` sur `IControl.Paint`et les appels à `Paint` via la classe seront instances et les instances de l’interface avoir les effets suivants</span><span class="sxs-lookup"><span data-stu-id="7d62d-364">the `Paint` method in `TextBox` hides the `Paint` method in `Control`, but it does not alter the mapping of `Control.Paint` onto `IControl.Paint`, and calls to `Paint` through class instances and interface instances will have the following effects</span></span>
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes Control.Paint();
```

<span data-ttu-id="7d62d-365">Toutefois, lorsqu’une méthode d’interface est mappée à une méthode virtuelle dans une classe, il est possible pour les classes dérivées de substituer la méthode virtuelle et de modifier l’implémentation de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-365">However, when an interface method is mapped onto a virtual method in a class, it is possible for derived classes to override the virtual method and alter the implementation of the interface.</span></span> <span data-ttu-id="7d62d-366">Par exemple, les déclarations ci-dessus de réécriture</span><span class="sxs-lookup"><span data-stu-id="7d62d-366">For example, rewriting the declarations above to</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public virtual void Paint() {...}
}

class TextBox: Control
{
    public override void Paint() {...}
}
```
<span data-ttu-id="7d62d-367">les effets suivants seront maintenant appliqués</span><span class="sxs-lookup"><span data-stu-id="7d62d-367">the following effects will now be observed</span></span>
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes TextBox.Paint();
```

<span data-ttu-id="7d62d-368">Dans la mesure où les implémentations de membres d’interface explicite ne peut pas être déclarées comme virtuelles, il n’est pas possible de remplacer une implémentation de membre d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="7d62d-368">Since explicit interface member implementations cannot be declared virtual, it is not possible to override an explicit interface member implementation.</span></span> <span data-ttu-id="7d62d-369">Toutefois, il est parfaitement valide pour une implémentation de membre d’interface explicite appeler une autre méthode, et que les autres méthode peut être déclarée virtuel pour autoriser les classes dérivées pour la remplacer.</span><span class="sxs-lookup"><span data-stu-id="7d62d-369">However, it is perfectly valid for an explicit interface member implementation to call another method, and that other method can be declared virtual to allow derived classes to override it.</span></span> <span data-ttu-id="7d62d-370">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-370">For example</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() { PaintControl(); }
    protected virtual void PaintControl() {...}
}

class TextBox: Control
{
    protected override void PaintControl() {...}
}
```

<span data-ttu-id="7d62d-371">Ici, les classes dérivées de `Control` pouvez le spécialiser l’implémentation de `IControl.Paint` en substituant le `PaintControl` (méthode).</span><span class="sxs-lookup"><span data-stu-id="7d62d-371">Here, classes derived from `Control` can specialize the implementation of `IControl.Paint` by overriding the `PaintControl` method.</span></span>

### <a name="interface-re-implementation"></a><span data-ttu-id="7d62d-372">Réimplémentation de l’interface</span><span class="sxs-lookup"><span data-stu-id="7d62d-372">Interface re-implementation</span></span>

<span data-ttu-id="7d62d-373">Une classe qui hérite d’une implémentation d’interface est autorisée à ***ré-implémenter*** l’interface en l’incluant dans la liste de classe de base.</span><span class="sxs-lookup"><span data-stu-id="7d62d-373">A class that inherits an interface implementation is permitted to ***re-implement*** the interface by including it in the base class list.</span></span>

<span data-ttu-id="7d62d-374">Une nouvelle implémentation d’une interface suit exactement les règles de mappage interface même que l’implémentation initiale d’une interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-374">A re-implementation of an interface follows exactly the same interface mapping rules as an initial implementation of an interface.</span></span> <span data-ttu-id="7d62d-375">Par conséquent, le mappage d’interface hérité n’a aucun effet sur le mappage d’interface établie pour la nouvelle implémentation de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-375">Thus, the inherited interface mapping has no effect whatsoever on the interface mapping established for the re-implementation of the interface.</span></span> <span data-ttu-id="7d62d-376">Par exemple, dans les déclarations</span><span class="sxs-lookup"><span data-stu-id="7d62d-376">For example, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() {...}
}

class MyControl: Control, IControl
{
    public void Paint() {}
}
```
<span data-ttu-id="7d62d-377">le fait que `Control` mappe `IControl.Paint` sur `Control.IControl.Paint` n’affecte pas la réimplémentation dans `MyControl`, laquelle correspond `IControl.Paint` sur `MyControl.Paint`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-377">the fact that `Control` maps `IControl.Paint` onto `Control.IControl.Paint` doesn't affect the re-implementation in `MyControl`, which maps `IControl.Paint` onto `MyControl.Paint`.</span></span>

<span data-ttu-id="7d62d-378">Hérité des déclarations de membre public et membre d’interface explicite hérité déclarations participent dans le processus de mappage d’interface pour les interfaces implémentées de nouveau.</span><span class="sxs-lookup"><span data-stu-id="7d62d-378">Inherited public member declarations and inherited explicit interface member declarations participate in the interface mapping process for re-implemented interfaces.</span></span> <span data-ttu-id="7d62d-379">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-379">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
    void H();
    void I();
}

class Base: IMethods
{
    void IMethods.F() {}
    void IMethods.G() {}
    public void H() {}
    public void I() {}
}

class Derived: Base, IMethods
{
    public void F() {}
    void IMethods.H() {}
}
```

<span data-ttu-id="7d62d-380">Ici, l’implémentation de `IMethods` dans `Derived` mappe les méthodes d’interface sur `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, et `Base.I`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-380">Here, the implementation of `IMethods` in `Derived` maps the interface methods onto `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, and `Base.I`.</span></span>

<span data-ttu-id="7d62d-381">Lorsqu’une classe implémente une interface, il implicitement toutes les interfaces de base de cette interface implémente également.</span><span class="sxs-lookup"><span data-stu-id="7d62d-381">When a class implements an interface, it implicitly also implements all of that interface's base interfaces.</span></span> <span data-ttu-id="7d62d-382">De même, une nouvelle implémentation d’une interface est également implicitement une réimplémentation de toutes les interfaces de base de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7d62d-382">Likewise, a re-implementation of an interface is also implicitly a re-implementation of all of the interface's base interfaces.</span></span> <span data-ttu-id="7d62d-383">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-383">For example</span></span>
```csharp
interface IBase
{
    void F();
}

interface IDerived: IBase
{
    void G();
}

class C: IDerived
{
    void IBase.F() {...}
    void IDerived.G() {...}
}

class D: C, IDerived
{
    public void F() {...}
    public void G() {...}
}
```

<span data-ttu-id="7d62d-384">Ici, la réimplémentation de `IDerived` nouveau implémente également `IBase`, le mappage `IBase.F` sur `D.F`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-384">Here, the re-implementation of `IDerived` also re-implements `IBase`, mapping `IBase.F` onto `D.F`.</span></span>

### <a name="abstract-classes-and-interfaces"></a><span data-ttu-id="7d62d-385">Interfaces et classes abstraites</span><span class="sxs-lookup"><span data-stu-id="7d62d-385">Abstract classes and interfaces</span></span>

<span data-ttu-id="7d62d-386">Comme une classe non abstraite, une classe abstraite doit fournir des implémentations de tous les membres des interfaces qui sont répertoriées dans la liste de classe de base de la classe.</span><span class="sxs-lookup"><span data-stu-id="7d62d-386">Like a non-abstract class, an abstract class must provide implementations of all members of the interfaces that are listed in the base class list of the class.</span></span> <span data-ttu-id="7d62d-387">Toutefois, une classe abstraite est autorisée à mapper les méthodes d’interface à des méthodes abstraites.</span><span class="sxs-lookup"><span data-stu-id="7d62d-387">However, an abstract class is permitted to map interface methods onto abstract methods.</span></span> <span data-ttu-id="7d62d-388">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-388">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    public abstract void F();
    public abstract void G();
}
```

<span data-ttu-id="7d62d-389">Ici, l’implémentation de `IMethods` mappe `F` et `G` à des méthodes abstraites, qui doit être substitué dans les classes non abstraites dérivées de `C`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-389">Here, the implementation of `IMethods` maps `F` and `G` onto abstract methods, which must be overridden in non-abstract classes that derive from `C`.</span></span>

<span data-ttu-id="7d62d-390">Notez que les implémentations de membres d’interface explicite ne peut pas être abstraites, mais implémentations de membres d’interface explicites sont bien entendu autorisées à appeler les méthodes abstraites.</span><span class="sxs-lookup"><span data-stu-id="7d62d-390">Note that explicit interface member implementations cannot be abstract, but explicit interface member implementations are of course permitted to call abstract methods.</span></span> <span data-ttu-id="7d62d-391">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d62d-391">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    void IMethods.F() { FF(); }
    void IMethods.G() { GG(); }
    protected abstract void FF();
    protected abstract void GG();
}
```

<span data-ttu-id="7d62d-392">Ici, les classes non abstraites dérivées de `C` serait nécessaire pour remplacer `FF` et `GG`, et offrir ainsi l’implémentation réelle de `IMethods`.</span><span class="sxs-lookup"><span data-stu-id="7d62d-392">Here, non-abstract classes that derive from `C` would be required to override `FF` and `GG`, thus providing the actual implementation of `IMethods`.</span></span>
