# <a name="structs"></a><span data-ttu-id="0894b-101">Structs</span><span class="sxs-lookup"><span data-stu-id="0894b-101">Structs</span></span>

<span data-ttu-id="0894b-102">Les structs sont semblables aux classes car elles représentent des structures de données qui peuvent contenir des membres de données et des fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="0894b-102">Structs are similar to classes in that they represent data structures that can contain data members and function members.</span></span> <span data-ttu-id="0894b-103">Toutefois, contrairement aux classes, structs sont des types valeur et ne nécessitent pas d’allocation de tas.</span><span class="sxs-lookup"><span data-stu-id="0894b-103">However, unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="0894b-104">Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière valeur connue en tant qu’objet.</span><span class="sxs-lookup"><span data-stu-id="0894b-104">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span>

<span data-ttu-id="0894b-105">Les structures sont particulièrement utiles pour les petites structures de données qui ont une sémantique par rapport à leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="0894b-105">Structs are particularly useful for small data structures that have value semantics.</span></span> <span data-ttu-id="0894b-106">Les nombres complexes, les points dans un système de coordonnées ou les paires clé-valeur dans un dictionnaire sont de bons exemples de structures.</span><span class="sxs-lookup"><span data-stu-id="0894b-106">Complex numbers, points in a coordinate system, or key-value pairs in a dictionary are all good examples of structs.</span></span> <span data-ttu-id="0894b-107">Clé à ces structures de données est qu’ils ont peu de données membres, qu’ils ne nécessitent pas d’utiliser l’héritage ni l’identité référentielle, et qu’ils peuvent être facilement implémentés à l’aide de la sémantique de valeur où assignation copie la valeur au lieu de la référence.</span><span class="sxs-lookup"><span data-stu-id="0894b-107">Key to these data structures is that they have few data members, that they do not require use of inheritance or referential identity, and that they can be conveniently implemented using value semantics where assignment copies the value instead of the reference.</span></span>

<span data-ttu-id="0894b-108">Comme décrit dans [types simples](types.md#simple-types), les types simples fournis par c#, tels que `int`, `double`, et `bool`, sont en fait tous les types struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-108">As described in [Simple types](types.md#simple-types), the simple types provided by C#, such as `int`, `double`, and `bool`, are in fact all struct types.</span></span> <span data-ttu-id="0894b-109">Comme ces types prédéfinis sont des structures, il est également possible d’utiliser les structures et la surcharge d’opérateur pour implémenter de nouveaux types « primitifs » dans le langage c#.</span><span class="sxs-lookup"><span data-stu-id="0894b-109">Just as these predefined types are structs, it is also possible to use structs and operator overloading to implement new "primitive" types in the C# language.</span></span> <span data-ttu-id="0894b-110">Deux exemples de ces types sont fournis à la fin de ce chapitre ([exemples de Struct](structs.md#struct-examples)).</span><span class="sxs-lookup"><span data-stu-id="0894b-110">Two examples of such types are given at the end of this chapter ([Struct examples](structs.md#struct-examples)).</span></span>

## <a name="struct-declarations"></a><span data-ttu-id="0894b-111">Déclarations de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-111">Struct declarations</span></span>

<span data-ttu-id="0894b-112">Un *struct_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau struct :</span><span class="sxs-lookup"><span data-stu-id="0894b-112">A *struct_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new struct:</span></span>

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

<span data-ttu-id="0894b-113">Un *struct_declaration* se compose d’un ensemble facultatif de *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *struct_modifier*s ([modificateurs de Struct](structs.md#struct-modifiers)), suivie éventuellement d’un `partial` modificateur, suivi par le mot clé `struct` et un *identificateur* qui nomme le struct, suivi d’une facultatif *type_parameter_list* spécification ([paramètres de Type](classes.md#type-parameters)), suivie éventuellement d’un *struct_interfaces* spécification ([Modificateur partiel](structs.md#partial-modifier))), suivie éventuellement d’un *type_parameter_constraints_clause*spécification s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), suivi par un *struct_body* ([Struct corps](structs.md#struct-body)), éventuellement suivi par un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0894b-113">A *struct_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *struct_modifier*s ([Struct modifiers](structs.md#struct-modifiers)), followed by an optional `partial` modifier, followed by the keyword `struct` and an *identifier* that names the struct, followed by an optional *type_parameter_list* specification ([Type parameters](classes.md#type-parameters)), followed by an optional *struct_interfaces* specification ([Partial modifier](structs.md#partial-modifier)) ), followed by an optional *type_parameter_constraints_clause*s specification ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *struct_body* ([Struct body](structs.md#struct-body)), optionally followed by a semicolon.</span></span>

### <a name="struct-modifiers"></a><span data-ttu-id="0894b-114">Modificateurs de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-114">Struct modifiers</span></span>

<span data-ttu-id="0894b-115">Un *struct_declaration* peut éventuellement inclure une séquence de modificateurs de struct :</span><span class="sxs-lookup"><span data-stu-id="0894b-115">A *struct_declaration* may optionally include a sequence of struct modifiers:</span></span>

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

<span data-ttu-id="0894b-116">Il s’agit d’une erreur de compilation pour le même modificateur apparaît plusieurs fois dans une déclaration de struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-116">It is a compile-time error for the same modifier to appear multiple times in a struct declaration.</span></span>

<span data-ttu-id="0894b-117">Les modificateurs d’une déclaration de struct ont la même signification que ceux d’une déclaration de classe ([déclarations de classe](classes.md#class-declarations)).</span><span class="sxs-lookup"><span data-stu-id="0894b-117">The modifiers of a struct declaration have the same meaning as those of a class declaration ([Class declarations](classes.md#class-declarations)).</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="0894b-118">Modificateur partiel</span><span class="sxs-lookup"><span data-stu-id="0894b-118">Partial modifier</span></span>

<span data-ttu-id="0894b-119">Le `partial` modificateur indique que cela *struct_declaration* est une déclaration de type partiel.</span><span class="sxs-lookup"><span data-stu-id="0894b-119">The `partial` modifier indicates that this *struct_declaration* is a partial type declaration.</span></span> <span data-ttu-id="0894b-120">Plusieurs déclarations de struct partiel avec le même nom dans une déclaration d’espace de noms ou type englobant se combinent pour former une déclaration de struct, conformément aux règles spécifiées dans [types partiels](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="0894b-120">Multiple partial struct declarations with the same name within an enclosing namespace or type declaration combine to form one struct declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

### <a name="struct-interfaces"></a><span data-ttu-id="0894b-121">Interfaces de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-121">Struct interfaces</span></span>

<span data-ttu-id="0894b-122">Une déclaration de struct peut inclure un *struct_interfaces* spécification, dans lequel cas le struct est dite implémenter directement les types d’interface donné.</span><span class="sxs-lookup"><span data-stu-id="0894b-122">A struct declaration may include a *struct_interfaces* specification, in which case the struct is said to directly implement the given interface types.</span></span>

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

<span data-ttu-id="0894b-123">Implémentations d’interface sont abordées plus en détail dans [implémentations d’Interface](interfaces.md#interface-implementations).</span><span class="sxs-lookup"><span data-stu-id="0894b-123">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="struct-body"></a><span data-ttu-id="0894b-124">Corps de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-124">Struct body</span></span>

<span data-ttu-id="0894b-125">Le *struct_body* d’un struct définit les membres du struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-125">The *struct_body* of a struct defines the members of the struct.</span></span>

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a><span data-ttu-id="0894b-126">Membres de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-126">Struct members</span></span>

<span data-ttu-id="0894b-127">Les membres d’un struct se composent des membres introduits par ses *struct_member_declaration*s et les membres héritent à partir du type `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="0894b-127">The members of a struct consist of the members introduced by its *struct_member_declaration*s and the members inherited from the type `System.ValueType`.</span></span>

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

<span data-ttu-id="0894b-128">À part les différences de noter dans [les différences de classe et de struct](structs.md#class-and-struct-differences), les descriptions des membres de classes dans [membres de classe](classes.md#class-members) via [itérateurs](classes.md#iterators) s’appliquent à struct les membres.</span><span class="sxs-lookup"><span data-stu-id="0894b-128">Except for the differences noted in [Class and struct differences](structs.md#class-and-struct-differences), the descriptions of class members provided in [Class members](classes.md#class-members) through [Iterators](classes.md#iterators) apply to struct members as well.</span></span>

## <a name="class-and-struct-differences"></a><span data-ttu-id="0894b-129">Différences entre classe et struct</span><span class="sxs-lookup"><span data-stu-id="0894b-129">Class and struct differences</span></span>

<span data-ttu-id="0894b-130">Les structs diffèrent des classes à plusieurs niveaux importants :</span><span class="sxs-lookup"><span data-stu-id="0894b-130">Structs differ from classes in several important ways:</span></span>

*  <span data-ttu-id="0894b-131">Les structs sont des types valeur ([valeur sémantique](structs.md#value-semantics)).</span><span class="sxs-lookup"><span data-stu-id="0894b-131">Structs are value types ([Value semantics](structs.md#value-semantics)).</span></span>
*  <span data-ttu-id="0894b-132">Tous les types struct héritent implicitement de la classe `System.ValueType` ([héritage](structs.md#inheritance)).</span><span class="sxs-lookup"><span data-stu-id="0894b-132">All struct types implicitly inherit from the class `System.ValueType` ([Inheritance](structs.md#inheritance)).</span></span>
*  <span data-ttu-id="0894b-133">Assignation à une variable d’un type struct crée une copie de la valeur assignée ([attribution](structs.md#assignment)).</span><span class="sxs-lookup"><span data-stu-id="0894b-133">Assignment to a variable of a struct type creates a copy of the value being assigned ([Assignment](structs.md#assignment)).</span></span>
*  <span data-ttu-id="0894b-134">La valeur par défaut d’un struct est la valeur produite en affectant tous les champs de type valeur leur valeur par défaut et référence de tous les champs de type `null` ([valeurs par défaut](structs.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="0894b-134">The default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null` ([Default values](structs.md#default-values)).</span></span>
*  <span data-ttu-id="0894b-135">Opérations de boxing et unboxing servent à effectuer une conversion entre un type struct et `object` ([Boxing et unboxing](structs.md#boxing-and-unboxing)).</span><span class="sxs-lookup"><span data-stu-id="0894b-135">Boxing and unboxing operations are used to convert between a struct type and `object` ([Boxing and unboxing](structs.md#boxing-and-unboxing)).</span></span>
*  <span data-ttu-id="0894b-136">La signification de `this` est différente pour les structs ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="0894b-136">The meaning of `this` is different for structs ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="0894b-137">Déclarations de champ d’instance pour un struct ne sont pas autorisées à inclure des initialiseurs de variable ([initialiseurs de champ](structs.md#field-initializers)).</span><span class="sxs-lookup"><span data-stu-id="0894b-137">Instance field declarations for a struct are not permitted to include variable initializers ([Field initializers](structs.md#field-initializers)).</span></span>
*  <span data-ttu-id="0894b-138">Un struct n’est pas autorisé à déclarer un constructeur d’instance sans paramètre ([constructeurs](structs.md#constructors)).</span><span class="sxs-lookup"><span data-stu-id="0894b-138">A struct is not permitted to declare a parameterless instance constructor ([Constructors](structs.md#constructors)).</span></span>
*  <span data-ttu-id="0894b-139">Un struct n’est pas autorisé à déclarer un destructeur ([destructeurs](structs.md#destructors)).</span><span class="sxs-lookup"><span data-stu-id="0894b-139">A struct is not permitted to declare a destructor ([Destructors](structs.md#destructors)).</span></span>

### <a name="value-semantics"></a><span data-ttu-id="0894b-140">Sémantique de valeur</span><span class="sxs-lookup"><span data-stu-id="0894b-140">Value semantics</span></span>

<span data-ttu-id="0894b-141">Les structs sont des types valeur ([types valeur](types.md#value-types)) et sont réputées pour avoir la sémantique de valeur.</span><span class="sxs-lookup"><span data-stu-id="0894b-141">Structs are value types ([Value types](types.md#value-types)) and are said to have value semantics.</span></span> <span data-ttu-id="0894b-142">Classes, quant à eux, sont des types référence ([types référence](types.md#reference-types)) et sont réputées pour avoir une sémantique de référence.</span><span class="sxs-lookup"><span data-stu-id="0894b-142">Classes, on the other hand, are reference types ([Reference types](types.md#reference-types)) and are said to have reference semantics.</span></span>

<span data-ttu-id="0894b-143">Une variable d’un type struct contient directement les données de la structure, alors qu’une variable d’un type de classe contient une référence aux données, la dernière valeur connue en tant qu’objet.</span><span class="sxs-lookup"><span data-stu-id="0894b-143">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span> <span data-ttu-id="0894b-144">Lorsqu’un struct `B` contient un champ d’instance de type `A` et `A` est un type struct, c’est une erreur de compilation pour `A` dépendre `B` ou un type construit à partir de `B`.</span><span class="sxs-lookup"><span data-stu-id="0894b-144">When a struct `B` contains an instance field of type `A` and `A` is a struct type, it is a compile-time error for `A` to depend on `B` or a type constructed from `B`.</span></span> <span data-ttu-id="0894b-145">Un struct `X` ***dépend directement de*** un struct `Y` si `X` contient un champ d’instance de type `Y`.</span><span class="sxs-lookup"><span data-stu-id="0894b-145">A struct `X` ***directly depends on*** a struct `Y` if `X` contains an instance field of type `Y`.</span></span> <span data-ttu-id="0894b-146">Étant donné cette définition, l’ensemble complet des structs dont dépend un struct est la fermeture transitive de la ***dépend directement*** relation.</span><span class="sxs-lookup"><span data-stu-id="0894b-146">Given this definition, the complete set of structs upon which a struct depends is the transitive closure of the ***directly depends on*** relationship.</span></span>  <span data-ttu-id="0894b-147">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0894b-147">For example</span></span>
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
<span data-ttu-id="0894b-148">est une erreur car `Node` contient un champ d’instance de son propre type.</span><span class="sxs-lookup"><span data-stu-id="0894b-148">is an error because `Node` contains an instance field of its own type.</span></span>  <span data-ttu-id="0894b-149">Un autre exemple</span><span class="sxs-lookup"><span data-stu-id="0894b-149">Another example</span></span>
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
<span data-ttu-id="0894b-150">est une erreur, car chacun des types `A`, `B`, et `C` dépendent d’eux.</span><span class="sxs-lookup"><span data-stu-id="0894b-150">is an error because each of the types `A`, `B`, and `C` depend on each other.</span></span>

<span data-ttu-id="0894b-151">Les classes, il est possible de deux variables référencent le même objet et par conséquent, les opérations sur une variable peuvent affecter l’objet référencé par l’autre variable.</span><span class="sxs-lookup"><span data-stu-id="0894b-151">With classes, it is possible for two variables to reference the same object, and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="0894b-152">Avec les structures, les variables disposent chacune leur propre copie des données (sauf dans le cas de `ref` et `out` variables de paramètre), et il n’est pas possible pour les opérations sur une d’affecter l’autre.</span><span class="sxs-lookup"><span data-stu-id="0894b-152">With structs, the variables each have their own copy of the data (except in the case of `ref` and `out` parameter variables), and it is not possible for operations on one to affect the other.</span></span> <span data-ttu-id="0894b-153">En outre, étant donné que les structs ne sont pas des types référence, il n’est pas possible pour les valeurs d’un type struct pour être `null`.</span><span class="sxs-lookup"><span data-stu-id="0894b-153">Furthermore, because structs are not reference types, it is not possible for values of a struct type to be `null`.</span></span>

<span data-ttu-id="0894b-154">Étant donné la déclaration</span><span class="sxs-lookup"><span data-stu-id="0894b-154">Given the declaration</span></span>
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
<span data-ttu-id="0894b-155">le fragment de code</span><span class="sxs-lookup"><span data-stu-id="0894b-155">the code fragment</span></span>
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
<span data-ttu-id="0894b-156">Affiche la valeur `10`.</span><span class="sxs-lookup"><span data-stu-id="0894b-156">outputs the value `10`.</span></span> <span data-ttu-id="0894b-157">L’attribution de `a` à `b` crée une copie de la valeur, et `b` est donc pas affecté par l’assignation à `a.x`.</span><span class="sxs-lookup"><span data-stu-id="0894b-157">The assignment of `a` to `b` creates a copy of the value, and `b` is thus unaffected by the assignment to `a.x`.</span></span> <span data-ttu-id="0894b-158">Avait `Point` au lieu de cela été déclaré en tant que classe, le résultat serait `100` car `a` et `b` serait référencent le même objet.</span><span class="sxs-lookup"><span data-stu-id="0894b-158">Had `Point` instead been declared as a class, the output would be `100` because `a` and `b` would reference the same object.</span></span>

### <a name="inheritance"></a><span data-ttu-id="0894b-159">Héritage</span><span class="sxs-lookup"><span data-stu-id="0894b-159">Inheritance</span></span>

<span data-ttu-id="0894b-160">Tous les types struct héritent implicitement de la classe `System.ValueType`, qui, à son tour, hérite de la classe `object`.</span><span class="sxs-lookup"><span data-stu-id="0894b-160">All struct types implicitly inherit from the class `System.ValueType`, which, in turn, inherits from class `object`.</span></span> <span data-ttu-id="0894b-161">Une déclaration de struct peut spécifier une liste d’interfaces implémentées, mais il n’est pas possible pour une déclaration de struct spécifier une classe de base.</span><span class="sxs-lookup"><span data-stu-id="0894b-161">A struct declaration may specify a list of implemented interfaces, but it is not possible for a struct declaration to specify a base class.</span></span>

<span data-ttu-id="0894b-162">Les types struct ne sont jamais abstraites et sont toujours implicitement sealed.</span><span class="sxs-lookup"><span data-stu-id="0894b-162">Struct types are never abstract and are always implicitly sealed.</span></span> <span data-ttu-id="0894b-163">Le `abstract` et `sealed` par conséquent, les modificateurs ne sont pas autorisés dans une déclaration de struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-163">The `abstract` and `sealed` modifiers are therefore not permitted in a struct declaration.</span></span>

<span data-ttu-id="0894b-164">Étant donné que l’héritage n’est pas pris en charge pour les structs, l’accessibilité déclarée d’un membre de struct ne peut pas être `protected` ou `protected internal`.</span><span class="sxs-lookup"><span data-stu-id="0894b-164">Since inheritance isn't supported for structs, the declared accessibility of a struct member cannot be `protected` or `protected internal`.</span></span>

<span data-ttu-id="0894b-165">Les fonctions membres d’un struct ne peut pas être `abstract` ou `virtual`et le `override` modificateur est autorisé uniquement pour substituer les méthodes héritées de `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="0894b-165">Function members in a struct cannot be `abstract` or `virtual`, and the `override` modifier is allowed only to override methods inherited from `System.ValueType`.</span></span>

### <a name="assignment"></a><span data-ttu-id="0894b-166">Attribution</span><span class="sxs-lookup"><span data-stu-id="0894b-166">Assignment</span></span>

<span data-ttu-id="0894b-167">Affectation à une variable d’un type struct crée une copie de la valeur assignée.</span><span class="sxs-lookup"><span data-stu-id="0894b-167">Assignment to a variable of a struct type creates a copy of the value being assigned.</span></span> <span data-ttu-id="0894b-168">Cela diffère d’affectation à une variable d’un type de classe, qui copie la référence mais pas l’objet identifié par la référence.</span><span class="sxs-lookup"><span data-stu-id="0894b-168">This differs from assignment to a variable of a class type, which copies the reference but not the object identified by the reference.</span></span>

<span data-ttu-id="0894b-169">Comme pour une assignation, lorsqu’un struct est passé comme un paramètre de valeur ou retourné comme résultat d’une fonction membre, une copie du struct est créée.</span><span class="sxs-lookup"><span data-stu-id="0894b-169">Similar to an assignment, when a struct is passed as a value parameter or returned as the result of a function member, a copy of the struct is created.</span></span> <span data-ttu-id="0894b-170">Un struct peut être passé par référence à une fonction membre à l’aide un `ref` ou `out` paramètre.</span><span class="sxs-lookup"><span data-stu-id="0894b-170">A struct may be passed by reference to a function member using a `ref` or `out` parameter.</span></span>

<span data-ttu-id="0894b-171">Lorsqu’une propriété ou un indexeur d’un struct est la cible d’une assignation, l’expression d’instance associée de l’accès de propriété ou l’indexeur doit être classée en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="0894b-171">When a property or indexer of a struct is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="0894b-172">Si l’expression d’instance est classifiée en tant que valeur, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0894b-172">If the instance expression is classified as a value, a compile-time error occurs.</span></span> <span data-ttu-id="0894b-173">Cette opération est décrite plus en détail dans [assignation Simple](expressions.md#simple-assignment).</span><span class="sxs-lookup"><span data-stu-id="0894b-173">This is described in further detail in [Simple assignment](expressions.md#simple-assignment).</span></span>

### <a name="default-values"></a><span data-ttu-id="0894b-174">Valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="0894b-174">Default values</span></span>

<span data-ttu-id="0894b-175">Comme décrit dans [valeurs par défaut](variables.md#default-values), plusieurs types de variables sont initialisées automatiquement à leur valeur par défaut lorsqu’elles sont créées.</span><span class="sxs-lookup"><span data-stu-id="0894b-175">As described in [Default values](variables.md#default-values), several kinds of variables are automatically initialized to their default value when they are created.</span></span> <span data-ttu-id="0894b-176">Pour les variables de types de classe et d’autres types de référence, cette valeur par défaut est `null`.</span><span class="sxs-lookup"><span data-stu-id="0894b-176">For variables of class types and other reference types, this default value is `null`.</span></span> <span data-ttu-id="0894b-177">Toutefois, étant donné que les structs sont des types de valeur ne peut pas être `null`, la valeur par défaut d’un struct est la valeur produite en affectant tous les champs de type valeur leur valeur par défaut et référence de tous les champs de type `null`.</span><span class="sxs-lookup"><span data-stu-id="0894b-177">However, since structs are value types that cannot be `null`, the default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="0894b-178">Faisant référence à la `Point` struct est déclaré, l’exemple ci-dessus</span><span class="sxs-lookup"><span data-stu-id="0894b-178">Referring to the `Point` struct declared above, the example</span></span>
```csharp
Point[] a = new Point[100];
```
<span data-ttu-id="0894b-179">Initialise chaque `Point` dans le tableau à la valeur produite en définissant le `x` et `y` champs à zéro.</span><span class="sxs-lookup"><span data-stu-id="0894b-179">initializes each `Point` in the array to the value produced by setting the `x` and `y` fields to zero.</span></span>

<span data-ttu-id="0894b-180">La valeur par défaut d’un struct correspond à la valeur retournée par le constructeur par défaut du struct ([constructeurs par défaut](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="0894b-180">The default value of a struct corresponds to the value returned by the default constructor of the struct ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="0894b-181">Contrairement à une classe, un struct n’est pas autorisé pour déclarer un constructeur d’instance sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="0894b-181">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="0894b-182">Au lieu de cela, chaque struct possède implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type à `null`.</span><span class="sxs-lookup"><span data-stu-id="0894b-182">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="0894b-183">Les structures doivent être conçus pour prendre en compte l’état de l’initialisation par défaut un état valide.</span><span class="sxs-lookup"><span data-stu-id="0894b-183">Structs should be designed to consider the default initialization state a valid state.</span></span> <span data-ttu-id="0894b-184">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0894b-184">In the example</span></span>
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
<span data-ttu-id="0894b-185">le constructeur d’instance défini par l’utilisateur protège contre les valeurs null uniquement où elle est explicitement appelée.</span><span class="sxs-lookup"><span data-stu-id="0894b-185">the user-defined instance constructor protects against null values only where it is explicitly called.</span></span> <span data-ttu-id="0894b-186">Dans le cas où un `KeyValuePair` variable est soumis à l’initialisation de valeurs par défaut, le `key` et `value` champs seront null, et la structure doit être préparée à gérer cet état.</span><span class="sxs-lookup"><span data-stu-id="0894b-186">In cases where a `KeyValuePair` variable is subject to default value initialization, the `key` and `value` fields will be null, and the struct must be prepared to handle this state.</span></span>

### <a name="boxing-and-unboxing"></a><span data-ttu-id="0894b-187">Boxing et unboxing</span><span class="sxs-lookup"><span data-stu-id="0894b-187">Boxing and unboxing</span></span>

<span data-ttu-id="0894b-188">Une valeur d’un type de classe peut être convertie en type `object` ou à un type d’interface est implémenté par la classe simplement en traitant la référence comme un autre type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0894b-188">A value of a class type can be converted to type `object` or to an interface type that is implemented by the class simply by treating the reference as another type at compile-time.</span></span> <span data-ttu-id="0894b-189">De même, une valeur de type `object` ou une valeur d’un type d’interface peut être convertie vers un type de classe sans modifier la référence (mais bien sûr une vérification de type au moment de l’exécution est requise dans ce cas).</span><span class="sxs-lookup"><span data-stu-id="0894b-189">Likewise, a value of type `object` or a value of an interface type can be converted back to a class type without changing the reference (but of course a run-time type check is required in this case).</span></span>

<span data-ttu-id="0894b-190">Dans la mesure où les structs ne sont pas des types référence, ces opérations sont implémentées différemment pour les types struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-190">Since structs are not reference types, these operations are implemented differently for struct types.</span></span> <span data-ttu-id="0894b-191">Quand une valeur d’un type struct est convertie en type `object` ou en un type interface implémenté par le struct, une opération de boxing a lieu.</span><span class="sxs-lookup"><span data-stu-id="0894b-191">When a value of a struct type is converted to type `object` or to an interface type that is implemented by the struct, a boxing operation takes place.</span></span> <span data-ttu-id="0894b-192">De même, lorsqu’une valeur de type `object` ou une valeur d’un type d’interface est convertie vers un type struct, une opération d’unboxing a lieu.</span><span class="sxs-lookup"><span data-stu-id="0894b-192">Likewise, when a value of type `object` or a value of an interface type is converted back to a struct type, an unboxing operation takes place.</span></span> <span data-ttu-id="0894b-193">La principale différence dans les mêmes opérations sur les types de classes est que la conversion boxing et unboxing copie la valeur du struct ou non par l’instance boxed.</span><span class="sxs-lookup"><span data-stu-id="0894b-193">A key difference from the same operations on class types is that boxing and unboxing copies the struct value either into or out of the boxed instance.</span></span> <span data-ttu-id="0894b-194">Par conséquent, après une opération boxing ou unboxing, modifications apportées à la structure unboxed ne sont pas répercutées dans la structure boxed.</span><span class="sxs-lookup"><span data-stu-id="0894b-194">Thus, following a boxing or unboxing operation, changes made to the unboxed struct are not reflected in the boxed struct.</span></span>

<span data-ttu-id="0894b-195">Quand un type struct substitue une méthode virtuelle héritée `System.Object` (tel que `Equals`, `GetHashCode`, ou `ToString`), appel de la méthode virtuelle via une instance du type struct n’entraîne pas de boxing se produise.</span><span class="sxs-lookup"><span data-stu-id="0894b-195">When a struct type overrides a virtual method inherited from `System.Object` (such as `Equals`, `GetHashCode`, or `ToString`), invocation of the virtual method through an instance of the struct type does not cause boxing to occur.</span></span> <span data-ttu-id="0894b-196">Cela est vrai même lorsque le struct est utilisé comme un paramètre de type et l’appel s’effectue via une instance du type de paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="0894b-196">This is true even when the struct is used as a type parameter and the invocation occurs through an instance of the type parameter type.</span></span> <span data-ttu-id="0894b-197">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0894b-197">For example:</span></span>
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="0894b-198">La sortie du programme est :</span><span class="sxs-lookup"><span data-stu-id="0894b-198">The output of the program is:</span></span>
```
1
2
3
```

<span data-ttu-id="0894b-199">Bien qu’il soit style incorrecte pour `ToString` pour avoir des effets secondaires, l’exemple montre qu’aucun boxing s’est produite pour les trois appels de `x.ToString()`.</span><span class="sxs-lookup"><span data-stu-id="0894b-199">Although it is bad style for `ToString` to have side effects, the example demonstrates that no boxing occurred for the three invocations of `x.ToString()`.</span></span>

<span data-ttu-id="0894b-200">De même, le boxing jamais implicitement se produit lorsque l’accès à un membre sur un paramètre de type contraint.</span><span class="sxs-lookup"><span data-stu-id="0894b-200">Similarly, boxing never implicitly occurs when accessing a member on a constrained type parameter.</span></span> <span data-ttu-id="0894b-201">Par exemple, une interface `ICounter` contient une méthode `Increment` qui peut être utilisé pour modifier une valeur.</span><span class="sxs-lookup"><span data-stu-id="0894b-201">For example, suppose an interface `ICounter` contains a method `Increment` which can be used to modify a value.</span></span> <span data-ttu-id="0894b-202">Si `ICounter` est utilisé en tant que contrainte, l’implémentation de la `Increment` méthode est appelée avec une référence à la variable qui `Increment` a été appelé sur jamais une copie convertie.</span><span class="sxs-lookup"><span data-stu-id="0894b-202">If `ICounter` is used as a constraint, the implementation of the `Increment` method is called with a reference to the variable that `Increment` was called on, never a boxed copy.</span></span>

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="0894b-203">Le premier appel à `Increment` modifie la valeur dans la variable `x`.</span><span class="sxs-lookup"><span data-stu-id="0894b-203">The first call to `Increment` modifies the value in the variable `x`.</span></span> <span data-ttu-id="0894b-204">Ce n’est pas équivalent à la deuxième appel à `Increment`, ce qui modifie la valeur dans une copie convertie de `x`.</span><span class="sxs-lookup"><span data-stu-id="0894b-204">This is not equivalent to the second call to `Increment`, which modifies the value in a boxed copy of `x`.</span></span> <span data-ttu-id="0894b-205">Par conséquent, la sortie du programme est :</span><span class="sxs-lookup"><span data-stu-id="0894b-205">Thus, the output of the program is:</span></span>
```
0
1
1
```

<span data-ttu-id="0894b-206">Pour plus d’informations sur les conversions boxing et unboxing, consultez [Boxing et unboxing](types.md#boxing-and-unboxing).</span><span class="sxs-lookup"><span data-stu-id="0894b-206">For further details on boxing and unboxing, see [Boxing and unboxing](types.md#boxing-and-unboxing).</span></span>

### <a name="meaning-of-this"></a><span data-ttu-id="0894b-207">Signification de ce</span><span class="sxs-lookup"><span data-stu-id="0894b-207">Meaning of this</span></span>

<span data-ttu-id="0894b-208">Au sein d’un constructeur d’instance ou une fonction membre d’instance d’une classe, `this` est classé en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="0894b-208">Within an instance constructor or instance function member of a class, `this` is classified as a value.</span></span> <span data-ttu-id="0894b-209">Par conséquent, tandis que `this` peut être utilisé pour faire référence à l’instance pour lequel la fonction membre a été appelé, il n’est pas possible d’affecter à `this` dans une fonction membre d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0894b-209">Thus, while `this` can be used to refer to the instance for which the function member was invoked, it is not possible to assign to `this` in a function member of a class.</span></span>

<span data-ttu-id="0894b-210">Au sein d’un constructeur d’instance d’un struct, `this` correspond à un `out` paramètre du type struct et au sein d’une fonction membre d’instance d’un struct, `this` correspond à un `ref` paramètre du type struct.</span><span class="sxs-lookup"><span data-stu-id="0894b-210">Within an instance constructor of a struct, `this` corresponds to an `out` parameter of the struct type, and within an instance function member of a struct, `this` corresponds to a `ref` parameter of the struct type.</span></span> <span data-ttu-id="0894b-211">Dans les deux cas, `this` est classé en tant que variable, et il est possible de modifier le struct entier pour lequel la fonction membre a été appelé en affectant à `this` ou en passant en tant qu’un `ref` ou `out` paramètre.</span><span class="sxs-lookup"><span data-stu-id="0894b-211">In both cases, `this` is classified as a variable, and it is possible to modify the entire struct for which the function member was invoked by assigning to `this` or by passing this as a `ref` or `out` parameter.</span></span>

### <a name="field-initializers"></a><span data-ttu-id="0894b-212">Initialiseurs de champ</span><span class="sxs-lookup"><span data-stu-id="0894b-212">Field initializers</span></span>

<span data-ttu-id="0894b-213">Comme décrit dans [valeurs par défaut](structs.md#default-values), la valeur par défaut d’un struct se compose de la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type à `null`.</span><span class="sxs-lookup"><span data-stu-id="0894b-213">As described in [Default values](structs.md#default-values), the default value of a struct consists of the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span> <span data-ttu-id="0894b-214">Pour cette raison, un struct n’autorise pas les déclarations de champ d’instance pour inclure des initialiseurs de variable.</span><span class="sxs-lookup"><span data-stu-id="0894b-214">For this reason, a struct does not permit instance field declarations to include variable initializers.</span></span> <span data-ttu-id="0894b-215">Cette restriction s’applique uniquement aux champs d’instance.</span><span class="sxs-lookup"><span data-stu-id="0894b-215">This restriction applies only to instance fields.</span></span> <span data-ttu-id="0894b-216">Les champs statiques d’un struct peuvent inclure des initialiseurs de variable.</span><span class="sxs-lookup"><span data-stu-id="0894b-216">Static fields of a struct are permitted to include variable initializers.</span></span>

<span data-ttu-id="0894b-217">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0894b-217">The example</span></span>
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
<span data-ttu-id="0894b-218">est erroné, car les déclarations de champ d’instance incluent des initialiseurs de variable.</span><span class="sxs-lookup"><span data-stu-id="0894b-218">is in error because the instance field declarations include variable initializers.</span></span>

### <a name="constructors"></a><span data-ttu-id="0894b-219">Constructeurs</span><span class="sxs-lookup"><span data-stu-id="0894b-219">Constructors</span></span>

<span data-ttu-id="0894b-220">Contrairement à une classe, un struct n’est pas autorisé pour déclarer un constructeur d’instance sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="0894b-220">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="0894b-221">Au lieu de cela, chaque struct possède implicitement un constructeur d’instance sans paramètre qui retourne toujours la valeur qui résulte de la configuration de tous les champs de type valeur à leur valeur par défaut et toutes les références des champs de type null ([deconstructeurspardéfaut](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="0894b-221">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="0894b-222">Un struct peut déclarer des constructeurs d’instances ayant des paramètres.</span><span class="sxs-lookup"><span data-stu-id="0894b-222">A struct can declare instance constructors having parameters.</span></span> <span data-ttu-id="0894b-223">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0894b-223">For example</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

<span data-ttu-id="0894b-224">Étant donné la déclaration ci-dessus, les instructions</span><span class="sxs-lookup"><span data-stu-id="0894b-224">Given the above declaration, the statements</span></span>
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
<span data-ttu-id="0894b-225">ils créent un `Point` avec `x` et `y` initialisée à zéro.</span><span class="sxs-lookup"><span data-stu-id="0894b-225">both create a `Point` with `x` and `y` initialized to zero.</span></span>

<span data-ttu-id="0894b-226">Un constructeur d’instance struct n’est pas autorisé à inclure un initialiseur de constructeur du formulaire `base(...)`.</span><span class="sxs-lookup"><span data-stu-id="0894b-226">A struct instance constructor is not permitted to include a constructor initializer of the form `base(...)`.</span></span>

<span data-ttu-id="0894b-227">Si le constructeur d’instance struct ne spécifie pas un initialiseur de constructeur, le `this` variable correspond à un `out` paramètre du type struct et est similaire à un `out` paramètre, `this` doit absolument être assigné () [Assignation définie](variables.md#definite-assignment)) à chaque emplacement où le constructeur retourne.</span><span class="sxs-lookup"><span data-stu-id="0894b-227">If the struct instance constructor doesn't specify a constructor initializer, the `this` variable corresponds to an `out` parameter of the struct type, and similar to an `out` parameter, `this` must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at every location where the constructor returns.</span></span> <span data-ttu-id="0894b-228">Si le constructeur d’instance struct spécifie un initialiseur de constructeur, le `this` variable correspond à un `ref` paramètre du type struct et est similaire à un `ref` paramètre, `this` est considéré comme définitivement assignée sur entrée pour le corps du constructeur.</span><span class="sxs-lookup"><span data-stu-id="0894b-228">If the struct instance constructor specifies a constructor initializer, the `this` variable corresponds to a `ref` parameter of the struct type, and similar to a `ref` parameter, `this` is considered definitely assigned on entry to the constructor body.</span></span> <span data-ttu-id="0894b-229">Prenez en compte l’implémentation de constructeur d’instance ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="0894b-229">Consider the instance constructor implementation below:</span></span>
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

<span data-ttu-id="0894b-230">Aucune fonction membre d’instance (y compris les accesseurs set des propriétés `X` et `Y`) peut être appelée jusqu'à ce que tous les champs du struct en cours de construction ont été définitivement assignés.</span><span class="sxs-lookup"><span data-stu-id="0894b-230">No instance member function (including the set accessors for the properties `X` and `Y`) can be called until all fields of the struct being constructed have been definitely assigned.</span></span> <span data-ttu-id="0894b-231">La seule exception implique des propriétés implémentées automatiquement ([implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties)).</span><span class="sxs-lookup"><span data-stu-id="0894b-231">The only exception involves automatically implemented properties ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="0894b-232">Les règles d’affectation ([expressions d’assignation Simple](variables.md#simple-assignment-expressions)) spécifiquement exempter assignation à une auto-property d’un type struct au sein d’un constructeur d’instance de ce type struct : ce type d’assignation est considéré comme une nette affectation de champ de stockage masqué de l’auto-property.</span><span class="sxs-lookup"><span data-stu-id="0894b-232">The definite assignment rules ([Simple assignment expressions](variables.md#simple-assignment-expressions)) specifically exempt assignment to an auto-property of a struct type within an instance constructor of that struct type: such an assignment is considered a definite assignment of the hidden backing field of the auto-property.</span></span> <span data-ttu-id="0894b-233">Par conséquent, ce qui suit est autorisé :</span><span class="sxs-lookup"><span data-stu-id="0894b-233">Thus, the following is allowed:</span></span>

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a><span data-ttu-id="0894b-234">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="0894b-234">Destructors</span></span>

<span data-ttu-id="0894b-235">Un struct n’est pas autorisé à déclarer un destructeur.</span><span class="sxs-lookup"><span data-stu-id="0894b-235">A struct is not permitted to declare a destructor.</span></span>

### <a name="static-constructors"></a><span data-ttu-id="0894b-236">Constructeurs statiques</span><span class="sxs-lookup"><span data-stu-id="0894b-236">Static constructors</span></span>

<span data-ttu-id="0894b-237">Les constructeurs statiques pour les structs suivent la plupart des mêmes règles que pour les classes.</span><span class="sxs-lookup"><span data-stu-id="0894b-237">Static constructors for structs follow most of the same rules as for classes.</span></span> <span data-ttu-id="0894b-238">L’exécution d’un constructeur statique pour un type struct est déclenchée par la première des événements suivants se produise au sein d’un domaine d’application :</span><span class="sxs-lookup"><span data-stu-id="0894b-238">The execution of a static constructor for a struct type is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="0894b-239">Un membre statique du type struct est référencé.</span><span class="sxs-lookup"><span data-stu-id="0894b-239">A static member of the struct type is referenced.</span></span>
*  <span data-ttu-id="0894b-240">Un constructeur déclaré explicitement du type struct est appelé.</span><span class="sxs-lookup"><span data-stu-id="0894b-240">An explicitly declared constructor of the struct type is called.</span></span>

<span data-ttu-id="0894b-241">La création de valeurs par défaut ([valeurs par défaut](structs.md#default-values)) du struct de types ne déclenche pas le constructeur statique.</span><span class="sxs-lookup"><span data-stu-id="0894b-241">The creation of default values ([Default values](structs.md#default-values)) of struct types does not trigger the static constructor.</span></span> <span data-ttu-id="0894b-242">(Un exemple de ceci est la valeur initiale d’éléments dans un tableau.)</span><span class="sxs-lookup"><span data-stu-id="0894b-242">(An example of this is the initial value of elements in an array.)</span></span>

## <a name="struct-examples"></a><span data-ttu-id="0894b-243">Exemples de struct</span><span class="sxs-lookup"><span data-stu-id="0894b-243">Struct examples</span></span>

<span data-ttu-id="0894b-244">L’exemple suivant montre deux exemples significatifs d’utilisation `struct` types afin de créer des types utilisables de même pour les types prédéfinis du langage, mais avec une sémantique modifiée.</span><span class="sxs-lookup"><span data-stu-id="0894b-244">The following shows two significant examples of using `struct` types to create types that can be used similarly to the predefined types of the language, but with modified semantics.</span></span>

### <a name="database-integer-type"></a><span data-ttu-id="0894b-245">Type d’entier de base de données</span><span class="sxs-lookup"><span data-stu-id="0894b-245">Database integer type</span></span>

<span data-ttu-id="0894b-246">Le `DBInt` struct ci-dessous implémente un type entier qui peut représenter le jeu complet de valeurs de la `int` type, plus un état supplémentaire qui indique une valeur inconnue.</span><span class="sxs-lookup"><span data-stu-id="0894b-246">The `DBInt` struct below implements an integer type that can represent the complete set of values of the `int` type, plus an additional state that indicates an unknown value.</span></span> <span data-ttu-id="0894b-247">Un type présentant ces caractéristiques est couramment utilisé dans les bases de données.</span><span class="sxs-lookup"><span data-stu-id="0894b-247">A type with these characteristics is commonly used in databases.</span></span>

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a><span data-ttu-id="0894b-248">Type booléen de base de données</span><span class="sxs-lookup"><span data-stu-id="0894b-248">Database boolean type</span></span>

<span data-ttu-id="0894b-249">Le `DBBool` struct ci-dessous implémente un type de logique à trois valeurs.</span><span class="sxs-lookup"><span data-stu-id="0894b-249">The `DBBool` struct below implements a three-valued logical type.</span></span> <span data-ttu-id="0894b-250">Les valeurs possibles de ce type sont `DBBool.True`, `DBBool.False`, et `DBBool.Null`, où le `Null` membre indique une valeur inconnue.</span><span class="sxs-lookup"><span data-stu-id="0894b-250">The possible values of this type are `DBBool.True`, `DBBool.False`, and `DBBool.Null`, where the `Null` member indicates an unknown value.</span></span> <span data-ttu-id="0894b-251">Ces types de logiques à trois valeurs sont couramment utilisés dans les bases de données.</span><span class="sxs-lookup"><span data-stu-id="0894b-251">Such three-valued logical types are commonly used in databases.</span></span>

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```
