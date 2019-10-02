---
ms.openlocfilehash: 4faef9a12bdff54fa59a55a0206fa72bda4ea585
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704059"
---
# <a name="unsafe-code"></a><span data-ttu-id="b95b9-101">Code unsafe</span><span class="sxs-lookup"><span data-stu-id="b95b9-101">Unsafe code</span></span>

<span data-ttu-id="b95b9-102">Le langage C# principal, tel que défini dans les chapitres précédents, diffère notamment de C C++ et de l’omission des pointeurs en tant que type de données.</span><span class="sxs-lookup"><span data-stu-id="b95b9-102">The core C# language, as defined in the preceding chapters, differs notably from C and C++ in its omission of pointers as a data type.</span></span> <span data-ttu-id="b95b9-103">À la C# place, fournit des références et la capacité de créer des objets gérés par un garbage collector.</span><span class="sxs-lookup"><span data-stu-id="b95b9-103">Instead, C# provides references and the ability to create objects that are managed by a garbage collector.</span></span> <span data-ttu-id="b95b9-104">Cette conception, couplée à d’autres fonctionnalités C# , rend un langage plus sûr que C C++ou.</span><span class="sxs-lookup"><span data-stu-id="b95b9-104">This design, coupled with other features, makes C# a much safer language than C or C++.</span></span> <span data-ttu-id="b95b9-105">Dans le langage C# principal, il n’est tout simplement pas possible d’avoir une variable non initialisée, un pointeur « non résolu » ou une expression qui indexe un tableau au-delà de ses limites.</span><span class="sxs-lookup"><span data-stu-id="b95b9-105">In the core C# language it is simply not possible to have an uninitialized variable, a "dangling" pointer, or an expression that indexes an array beyond its bounds.</span></span> <span data-ttu-id="b95b9-106">Les catégories entières de bogues qui imposent C++ régulièrement le C et les programmes sont donc éliminées.</span><span class="sxs-lookup"><span data-stu-id="b95b9-106">Whole categories of bugs that routinely plague C and C++ programs are thus eliminated.</span></span>

<span data-ttu-id="b95b9-107">Bien que presque chaque construction de type pointeur en C++ C ou ait un équivalent de C#type référence dans, néanmoins, il existe des situations où l’accès aux types pointeur devient une nécessité.</span><span class="sxs-lookup"><span data-stu-id="b95b9-107">While practically every pointer type construct in C or C++ has a reference type counterpart in C#, nonetheless, there are situations where access to pointer types becomes a necessity.</span></span> <span data-ttu-id="b95b9-108">Par exemple, l’interfaçage avec le système d’exploitation sous-jacent, l’accès à un périphérique mappé en mémoire ou l’implémentation d’un algorithme de temps critique peut ne pas être possible ou pratique sans accès aux pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-108">For example, interfacing with the underlying operating system, accessing a memory-mapped device, or implementing a time-critical algorithm may not be possible or practical without access to pointers.</span></span> <span data-ttu-id="b95b9-109">Pour répondre à ce besoin C# , offre la possibilité d’écrire du ***code non sécurisé***.</span><span class="sxs-lookup"><span data-stu-id="b95b9-109">To address this need, C# provides the ability to write ***unsafe code***.</span></span>

<span data-ttu-id="b95b9-110">Dans du code non sécurisé, il est possible de déclarer et d’utiliser des pointeurs, d’effectuer des conversions entre les pointeurs et les types intégraux, de prendre l’adresse des variables, et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="b95b9-110">In unsafe code it is possible to declare and operate on pointers, to perform conversions between pointers and integral types, to take the address of variables, and so forth.</span></span> <span data-ttu-id="b95b9-111">Dans un sens, l’écriture de code non sécurisé est très similaire à l’écriture C# de code C dans un programme.</span><span class="sxs-lookup"><span data-stu-id="b95b9-111">In a sense, writing unsafe code is much like writing C code within a C# program.</span></span>

<span data-ttu-id="b95b9-112">Le code non sécurisé est en fait une fonctionnalité « sécurisée » du point de vue des développeurs et des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-112">Unsafe code is in fact a "safe" feature from the perspective of both developers and users.</span></span> <span data-ttu-id="b95b9-113">Le code unsafe doit être clairement marqué avec le modificateur `unsafe`, de sorte que les développeurs ne peuvent pas utiliser accidentellement des fonctionnalités potentiellement dangereuses, et le moteur d’exécution fonctionne pour s’assurer que le code unsafe ne peut pas être exécuté dans un environnement non fiable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-113">Unsafe code must be clearly marked with the modifier `unsafe`, so developers can't possibly use unsafe features accidentally, and the execution engine works to ensure that unsafe code cannot be executed in an untrusted environment.</span></span>

## <a name="unsafe-contexts"></a><span data-ttu-id="b95b9-114">Contextes non sécurisés</span><span class="sxs-lookup"><span data-stu-id="b95b9-114">Unsafe contexts</span></span>

<span data-ttu-id="b95b9-115">Les fonctionnalités non sécurisées C# de sont disponibles uniquement dans les contextes non sécurisés.</span><span class="sxs-lookup"><span data-stu-id="b95b9-115">The unsafe features of C# are available only in unsafe contexts.</span></span> <span data-ttu-id="b95b9-116">Un contexte unsafe est introduit en incluant un modificateur `unsafe` dans la déclaration d’un type ou d’un membre, ou en utilisant un *unsafe_statement*:</span><span class="sxs-lookup"><span data-stu-id="b95b9-116">An unsafe context is introduced by including an `unsafe` modifier in the declaration of a type or member, or by employing an *unsafe_statement*:</span></span>

*  <span data-ttu-id="b95b9-117">Une déclaration d’une classe, d’une structure, d’une interface ou d’un délégué peut inclure un modificateur `unsafe`, auquel cas la totalité de l’étendue textuelle de cette déclaration de type (y compris le corps de la classe, de la structure ou de l’interface) est considérée comme un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-117">A declaration of a class, struct, interface, or delegate may include an `unsafe` modifier, in which case the entire textual extent of that type declaration (including the body of the class, struct, or interface) is considered an unsafe context.</span></span>
*  <span data-ttu-id="b95b9-118">Une déclaration d’un champ, d’une méthode, d’une propriété, d’un événement, d’un indexeur, d’un opérateur, d’un constructeur d’instance, d’un destructeur ou d’un constructeur statique peut inclure un modificateur `unsafe`, auquel cas la totalité de l’étendue textuelle de cette déclaration de membre est considérée comme un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-118">A declaration of a field, method, property, event, indexer, operator, instance constructor, destructor, or static constructor may include an `unsafe` modifier, in which case the entire textual extent of that member declaration is considered an unsafe context.</span></span>
*  <span data-ttu-id="b95b9-119">Un *unsafe_statement* permet l’utilisation d’un contexte non sécurisé au sein d’un *bloc*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-119">An *unsafe_statement* enables the use of an unsafe context within a *block*.</span></span> <span data-ttu-id="b95b9-120">La totalité de l’étendue textuelle du *bloc* associé est considérée comme un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-120">The entire textual extent of the associated *block* is considered an unsafe context.</span></span>

<span data-ttu-id="b95b9-121">Les productions grammaticales associées sont indiquées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b95b9-121">The associated grammar productions are shown below.</span></span>

```antlr
class_modifier_unsafe
    : 'unsafe'
    ;

struct_modifier_unsafe
    : 'unsafe'
    ;

interface_modifier_unsafe
    : 'unsafe'
    ;

delegate_modifier_unsafe
    : 'unsafe'
    ;

field_modifier_unsafe
    : 'unsafe'
    ;

method_modifier_unsafe
    : 'unsafe'
    ;

property_modifier_unsafe
    : 'unsafe'
    ;

event_modifier_unsafe
    : 'unsafe'
    ;

indexer_modifier_unsafe
    : 'unsafe'
    ;

operator_modifier_unsafe
    : 'unsafe'
    ;

constructor_modifier_unsafe
    : 'unsafe'
    ;

destructor_declaration_unsafe
    : attributes? 'extern'? 'unsafe'? '~' identifier '(' ')' destructor_body
    | attributes? 'unsafe'? 'extern'? '~' identifier '(' ')' destructor_body
    ;

static_constructor_modifiers_unsafe
    : 'extern'? 'unsafe'? 'static'
    | 'unsafe'? 'extern'? 'static'
    | 'extern'? 'static' 'unsafe'?
    | 'unsafe'? 'static' 'extern'?
    | 'static' 'extern'? 'unsafe'?
    | 'static' 'unsafe'? 'extern'?
    ;

embedded_statement_unsafe
    : unsafe_statement
    | fixed_statement
    ;

unsafe_statement
    : 'unsafe' block
    ;
```

<span data-ttu-id="b95b9-122">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-122">In the example</span></span>

```csharp
public unsafe struct Node
{
    public int Value;
    public Node* Left;
    public Node* Right;
}
```

<span data-ttu-id="b95b9-123">le modificateur `unsafe` spécifié dans la déclaration de struct fait en sorte que la totalité de l’étendue textuelle de la déclaration de struct devienne un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-123">the `unsafe` modifier specified in the struct declaration causes the entire textual extent of the struct declaration to become an unsafe context.</span></span> <span data-ttu-id="b95b9-124">Par conséquent, il est possible de déclarer les champs `Left` et `Right` comme étant d’un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-124">Thus, it is possible to declare the `Left` and `Right` fields to be of a pointer type.</span></span> <span data-ttu-id="b95b9-125">L’exemple ci-dessus peut également être écrit</span><span class="sxs-lookup"><span data-stu-id="b95b9-125">The example above could also be written</span></span>

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

<span data-ttu-id="b95b9-126">Ici, les modificateurs `unsafe` dans les déclarations de champ provoquent la prise en compte de contextes non sécurisés par ces déclarations.</span><span class="sxs-lookup"><span data-stu-id="b95b9-126">Here, the `unsafe` modifiers in the field declarations cause those declarations to be considered unsafe contexts.</span></span>

<span data-ttu-id="b95b9-127">En dehors de l’établissement d’un contexte non sécurisé, ce qui autorise l’utilisation de types pointeur, le modificateur `unsafe` n’a aucun effet sur un type ou un membre.</span><span class="sxs-lookup"><span data-stu-id="b95b9-127">Other than establishing an unsafe context, thus permitting the use of pointer types, the `unsafe` modifier has no effect on a type or a member.</span></span> <span data-ttu-id="b95b9-128">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-128">In the example</span></span>

```csharp
public class A
{
    public unsafe virtual void F() {
        char* p;
        ...
    }
}

public class B: A
{
    public override void F() {
        base.F();
        ...
    }
}
```

<span data-ttu-id="b95b9-129">le modificateur `unsafe` sur la méthode `F` dans `A` fait simplement en sorte que l’étendue textuelle de `F` devienne un contexte non sécurisé dans lequel les fonctionnalités potentiellement dangereuses du langage peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="b95b9-129">the `unsafe` modifier on the `F` method in `A` simply causes the textual extent of `F` to become an unsafe context in which the unsafe features of the language can be used.</span></span> <span data-ttu-id="b95b9-130">Dans la substitution de `F` dans `B`, il n’est pas nécessaire de spécifier à nouveau le modificateur `unsafe`, sauf si, bien sûr, la méthode `F` dans `B` lui-même a besoin d’accéder à des fonctionnalités non sécurisées.</span><span class="sxs-lookup"><span data-stu-id="b95b9-130">In the override of `F` in `B`, there is no need to re-specify the `unsafe` modifier -- unless, of course, the `F` method in `B` itself needs access to unsafe features.</span></span>

<span data-ttu-id="b95b9-131">La situation est légèrement différente lorsqu’un type pointeur fait partie de la signature de la méthode.</span><span class="sxs-lookup"><span data-stu-id="b95b9-131">The situation is slightly different when a pointer type is part of the method's signature</span></span>

```csharp
public unsafe class A
{
    public virtual void F(char* p) {...}
}

public class B: A
{
    public unsafe override void F(char* p) {...}
}
```

<span data-ttu-id="b95b9-132">Dans ce cas, étant donné que la signature de `F` comprend un type pointeur, elle peut uniquement être écrite dans un contexte non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-132">Here, because `F`'s signature includes a pointer type, it can only be written in an unsafe context.</span></span> <span data-ttu-id="b95b9-133">Toutefois, le contexte unsafe peut être introduit soit en rendant la classe entière non sécurisée, comme c’est le cas dans `A`, soit en incluant un modificateur `unsafe` dans la déclaration de méthode, comme c’est le cas dans `B`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-133">However, the unsafe context can be introduced by either making the entire class unsafe, as is the case in `A`, or by including an `unsafe` modifier in the method declaration, as is the case in `B`.</span></span>

## <a name="pointer-types"></a><span data-ttu-id="b95b9-134">Types de pointeur</span><span class="sxs-lookup"><span data-stu-id="b95b9-134">Pointer types</span></span>

<span data-ttu-id="b95b9-135">Dans un contexte unsafe, un *type* ([types](types.md)) peut être un *pointer_type* , ainsi qu’un *Value_type* ou un *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-135">In an unsafe context, a *type* ([Types](types.md)) may be a *pointer_type* as well as a *value_type* or a *reference_type*.</span></span> <span data-ttu-id="b95b9-136">Toutefois, un *pointer_type* peut également être utilisé dans une expression `typeof` ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) en dehors d’un contexte non sécurisé, car cette utilisation n’est pas fiable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-136">However, a *pointer_type* may also be used in a `typeof` expression ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) outside of an unsafe context as such usage is not unsafe.</span></span>

```antlr
type_unsafe
    : pointer_type
    ;
```

<span data-ttu-id="b95b9-137">Un *pointer_type* est écrit comme un *unmanaged_type* ou le mot clé `void`, suivi d’un jeton `*` :</span><span class="sxs-lookup"><span data-stu-id="b95b9-137">A *pointer_type* is written as an *unmanaged_type* or the keyword `void`, followed by a `*` token:</span></span>

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

<span data-ttu-id="b95b9-138">Le type spécifié avant le `*` dans un type pointeur est appelé ***type référent*** du type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-138">The type specified before the `*` in a pointer type is called the ***referent type*** of the pointer type.</span></span> <span data-ttu-id="b95b9-139">Il représente le type de la variable vers laquelle la valeur du type pointeur pointe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-139">It represents the type of the variable to which a value of the pointer type points.</span></span>

<span data-ttu-id="b95b9-140">Contrairement aux références (valeurs de types référence), les pointeurs ne sont pas suivis par le récupérateur de mémoire : le garbage collector n’a aucune connaissance des pointeurs et des données auxquelles ils pointent.</span><span class="sxs-lookup"><span data-stu-id="b95b9-140">Unlike references (values of reference types), pointers are not tracked by the garbage collector -- the garbage collector has no knowledge of pointers and the data to which they point.</span></span> <span data-ttu-id="b95b9-141">Pour cette raison, un pointeur n’est pas autorisé à pointer vers une référence ou un struct qui contient des références, et le type référent d’un pointeur doit être un *unmanaged_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-141">For this reason a pointer is not permitted to point to a reference or to a struct that contains references, and the referent type of a pointer must be an *unmanaged_type*.</span></span>

<span data-ttu-id="b95b9-142">Un *unmanaged_type* est un type qui n’est pas un type *reference_type* ou construit, et ne contient pas de champs de type *reference_type* ou construits à aucun niveau d’imbrication.</span><span class="sxs-lookup"><span data-stu-id="b95b9-142">An *unmanaged_type* is any type that isn't a *reference_type* or constructed type, and doesn't contain *reference_type* or constructed type fields at any level of nesting.</span></span> <span data-ttu-id="b95b9-143">En d’autres termes, un *unmanaged_type* est l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b95b9-143">In other words, an *unmanaged_type* is one of the following:</span></span>

*  <span data-ttu-id="b95b9-144">`sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, 0, 1 ou 2.</span><span class="sxs-lookup"><span data-stu-id="b95b9-144">`sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, or `bool`.</span></span>
*  <span data-ttu-id="b95b9-145">N’importe quel *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-145">Any *enum_type*.</span></span>
*  <span data-ttu-id="b95b9-146">N’importe quel *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-146">Any *pointer_type*.</span></span>
*  <span data-ttu-id="b95b9-147">Tout *struct_type* défini par l’utilisateur qui n’est pas un type construit et contient uniquement des champs de *unmanaged_type*s.</span><span class="sxs-lookup"><span data-stu-id="b95b9-147">Any user-defined *struct_type* that is not a constructed type and contains fields of *unmanaged_type*s only.</span></span>

<span data-ttu-id="b95b9-148">La règle intuitive pour mélanger des pointeurs et des références est que les référents de références (objets) sont autorisés à contenir des pointeurs, mais les référents de pointeurs ne sont pas autorisés à contenir des références.</span><span class="sxs-lookup"><span data-stu-id="b95b9-148">The intuitive rule for mixing of pointers and references is that referents of references (objects) are permitted to contain pointers, but referents of pointers are not permitted to contain references.</span></span>

<span data-ttu-id="b95b9-149">Des exemples de types pointeur sont fournis dans le tableau ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="b95b9-149">Some examples of pointer types are given in the table below:</span></span>

| <span data-ttu-id="b95b9-150">__Exemple__</span><span class="sxs-lookup"><span data-stu-id="b95b9-150">__Example__</span></span> | <span data-ttu-id="b95b9-151">__Description__</span><span class="sxs-lookup"><span data-stu-id="b95b9-151">__Description__</span></span>                               |
|-------------|-----------------------------------------------|
| `byte*`     | <span data-ttu-id="b95b9-152">Pointeur vers `byte`</span><span class="sxs-lookup"><span data-stu-id="b95b9-152">Pointer to `byte`</span></span>                             |
| `char*`     | <span data-ttu-id="b95b9-153">Pointeur vers `char`</span><span class="sxs-lookup"><span data-stu-id="b95b9-153">Pointer to `char`</span></span>                             |
| `int**`     | <span data-ttu-id="b95b9-154">Pointeur vers un pointeur vers `int`</span><span class="sxs-lookup"><span data-stu-id="b95b9-154">Pointer to pointer to `int`</span></span>                   |
| `int*[]`    | <span data-ttu-id="b95b9-155">Tableau unidimensionnel de pointeurs vers `int`</span><span class="sxs-lookup"><span data-stu-id="b95b9-155">Single-dimensional array of pointers to `int`</span></span> |
| `void*`     | <span data-ttu-id="b95b9-156">Pointeur vers un type inconnu</span><span class="sxs-lookup"><span data-stu-id="b95b9-156">Pointer to unknown type</span></span>                       |

<span data-ttu-id="b95b9-157">Pour une implémentation donnée, tous les types de pointeurs doivent avoir la même taille et la même représentation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-157">For a given implementation, all pointer types must have the same size and representation.</span></span>

<span data-ttu-id="b95b9-158">Contrairement à C C++et, lorsque plusieurs pointeurs sont déclarés dans la même déclaration C# , dans le `*` est écrit avec le type sous-jacent uniquement, et non comme un signe de ponctuation de préfixe sur chaque nom de pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-158">Unlike C and C++, when multiple pointers are declared in the same declaration, in C# the `*` is written along with the underlying type only, not as a prefix punctuator on each pointer name.</span></span> <span data-ttu-id="b95b9-159">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-159">For example</span></span>

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

<span data-ttu-id="b95b9-160">La valeur d’un pointeur de type `T*` représente l’adresse d’une variable de type `T`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-160">The value of a pointer having type `T*` represents the address of a variable of type `T`.</span></span> <span data-ttu-id="b95b9-161">L’opérateur d’indirection de pointeur `*` ([indirection de pointeur](unsafe-code.md#pointer-indirection)) peut être utilisé pour accéder à cette variable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-161">The pointer indirection operator `*` ([Pointer indirection](unsafe-code.md#pointer-indirection)) may be used to access this variable.</span></span> <span data-ttu-id="b95b9-162">Par exemple, à partir d’une variable `P` de type `int*`, l’expression `*P` indique la variable `int` trouvée à l’adresse contenue dans `P`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-162">For example, given a variable `P` of type `int*`, the expression `*P` denotes the `int` variable found at the address contained in `P`.</span></span>

<span data-ttu-id="b95b9-163">À l’instar d’une référence d’objet, un pointeur peut être `null`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-163">Like an object reference, a pointer may be `null`.</span></span> <span data-ttu-id="b95b9-164">L’application de l’opérateur d’indirection à un pointeur `null` entraîne un comportement défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-164">Applying the indirection operator to a `null` pointer results in implementation-defined behavior.</span></span> <span data-ttu-id="b95b9-165">Un pointeur avec la valeur `null` est représenté par All-bits-Zero.</span><span class="sxs-lookup"><span data-stu-id="b95b9-165">A pointer with value `null` is represented by all-bits-zero.</span></span>

<span data-ttu-id="b95b9-166">Le type `void*` représente un pointeur vers un type inconnu.</span><span class="sxs-lookup"><span data-stu-id="b95b9-166">The `void*` type represents a pointer to an unknown type.</span></span> <span data-ttu-id="b95b9-167">Étant donné que le type référent est inconnu, l’opérateur d’indirection ne peut pas être appliqué à un pointeur de type `void*`, et aucune arithmétique ne peut être exécutée sur ce pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-167">Because the referent type is unknown, the indirection operator cannot be applied to a pointer of type `void*`, nor can any arithmetic be performed on such a pointer.</span></span> <span data-ttu-id="b95b9-168">Toutefois, un pointeur de type `void*` peut être casté en n’importe quel autre type pointeur (et inversement).</span><span class="sxs-lookup"><span data-stu-id="b95b9-168">However, a pointer of type `void*` can be cast to any other pointer type (and vice versa).</span></span>

<span data-ttu-id="b95b9-169">Les types pointeur sont une catégorie distincte de types.</span><span class="sxs-lookup"><span data-stu-id="b95b9-169">Pointer types are a separate category of types.</span></span> <span data-ttu-id="b95b9-170">Contrairement aux types référence et aux types valeur, les types pointeur n’héritent pas de `object` et aucune conversion n’existe entre les types pointeur et `object`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-170">Unlike reference types and value types, pointer types do not inherit from `object` and no conversions exist between pointer types and `object`.</span></span> <span data-ttu-id="b95b9-171">En particulier, le boxing et l’unboxing ([boxing et unboxing](types.md#boxing-and-unboxing)) ne sont pas pris en charge pour les pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-171">In particular, boxing and unboxing ([Boxing and unboxing](types.md#boxing-and-unboxing)) are not supported for pointers.</span></span> <span data-ttu-id="b95b9-172">Toutefois, les conversions sont autorisées entre les différents types pointeur et entre les types pointeur et les types intégraux.</span><span class="sxs-lookup"><span data-stu-id="b95b9-172">However, conversions are permitted between different pointer types and between pointer types and the integral types.</span></span> <span data-ttu-id="b95b9-173">Cela est décrit dans [conversions de pointeurs](unsafe-code.md#pointer-conversions).</span><span class="sxs-lookup"><span data-stu-id="b95b9-173">This is described in [Pointer conversions](unsafe-code.md#pointer-conversions).</span></span>

<span data-ttu-id="b95b9-174">Un *pointer_type* ne peut pas être utilisé en tant qu’argument de type ([types construits](types.md#constructed-types)), et l’inférence de type ([inférence de type](expressions.md#type-inference)) échoue sur les appels de méthode génériques qui auraient pu déduire un argument de type comme étant un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-174">A *pointer_type* cannot be used as a type argument ([Constructed types](types.md#constructed-types)), and type inference ([Type inference](expressions.md#type-inference)) fails on generic method calls that would have inferred a type argument to be a pointer type.</span></span>

<span data-ttu-id="b95b9-175">Un *pointer_type* peut être utilisé comme type de champ volatile ([champs volatiles](classes.md#volatile-fields)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-175">A *pointer_type* may be used as the type of a volatile field ([Volatile fields](classes.md#volatile-fields)).</span></span>

<span data-ttu-id="b95b9-176">Bien que les pointeurs puissent être passés en tant que paramètres `ref` ou `out`, cela peut entraîner un comportement indéfini, puisque le pointeur peut être configuré pour pointer vers une variable locale qui n’existe plus quand la méthode appelée est retournée, ou l’objet fixe auquel elle est utilisée pour pointer , n’est plus résolu.</span><span class="sxs-lookup"><span data-stu-id="b95b9-176">Although pointers can be passed as `ref` or `out` parameters, doing so can cause undefined behavior, since the pointer may well be set to point to a local variable which no longer exists when the called method returns, or the fixed object to which it used to point, is no longer fixed.</span></span> <span data-ttu-id="b95b9-177">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-177">For example:</span></span>

```csharp
using System;

class Test
{
    static int value = 20;

    unsafe static void F(out int* pi1, ref int* pi2) {
        int i = 10;
        pi1 = &i;

        fixed (int* pj = &value) {
            // ...
            pi2 = pj;
        }
    }

    static void Main() {
        int i = 10;
        unsafe {
            int* px1;
            int* px2 = &i;

            F(out px1, ref px2);

            Console.WriteLine("*px1 = {0}, *px2 = {1}",
                *px1, *px2);    // undefined behavior
        }
    }
}
```

<span data-ttu-id="b95b9-178">Une méthode peut retourner une valeur d’un certain type et ce type peut être un pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-178">A method can return a value of some type, and that type can be a pointer.</span></span> <span data-ttu-id="b95b9-179">Par exemple, lorsqu’un pointeur désignant une séquence contiguë de `int`, le nombre d’éléments de cette séquence et une autre valeur `int`, la méthode suivante retourne l’adresse de cette valeur dans cette séquence, si une correspondance est trouvée ; dans le cas contraire, elle retourne `null` :</span><span class="sxs-lookup"><span data-stu-id="b95b9-179">For example, when given a pointer to a contiguous sequence of `int`s, that sequence's element count, and some other `int` value, the following method returns the address of that value in that sequence, if a match occurs; otherwise it returns `null`:</span></span>

```csharp
unsafe static int* Find(int* pi, int size, int value) {
    for (int i = 0; i < size; ++i) {
        if (*pi == value) 
            return pi;
        ++pi;
    }
    return null;
}
```

<span data-ttu-id="b95b9-180">Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour l’utilisation des pointeurs :</span><span class="sxs-lookup"><span data-stu-id="b95b9-180">In an unsafe context, several constructs are available for operating on pointers:</span></span>

*  <span data-ttu-id="b95b9-181">L’opérateur `*` peut être utilisé pour effectuer une indirection de pointeur ([indirection de pointeur](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-181">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
*  <span data-ttu-id="b95b9-182">L’opérateur `->` peut être utilisé pour accéder à un membre d’un struct via un pointeur ([accès aux membres du pointeur](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-182">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
*  <span data-ttu-id="b95b9-183">L’opérateur `[]` peut être utilisé pour indexer un pointeur ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-183">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
*  <span data-ttu-id="b95b9-184">L’opérateur `&` peut être utilisé pour obtenir l’adresse d’une variable ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-184">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
*  <span data-ttu-id="b95b9-185">Les opérateurs `++` et `--` peuvent être utilisés pour incrémenter et décrémenter des pointeurs ([incrémentation et décrémentation de pointeur](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-185">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
*  <span data-ttu-id="b95b9-186">Les opérateurs `+` et `-` peuvent être utilisés pour effectuer des opérations arithmétiques sur les pointeurs ([opérations arithmétiques](unsafe-code.md#pointer-arithmetic)sur les pointeurs).</span><span class="sxs-lookup"><span data-stu-id="b95b9-186">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
*  <span data-ttu-id="b95b9-187">Les opérateurs `==`, `!=`, `<`, `>`, `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-187">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
*  <span data-ttu-id="b95b9-188">L’opérateur `stackalloc` peut être utilisé pour allouer de la mémoire à partir de la pile des appels ([mémoires tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-188">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
*  <span data-ttu-id="b95b9-189">L’instruction `fixed` peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([l’instruction fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-189">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>

## <a name="fixed-and-moveable-variables"></a><span data-ttu-id="b95b9-190">Variables fixes et déplaçables</span><span class="sxs-lookup"><span data-stu-id="b95b9-190">Fixed and moveable variables</span></span>

<span data-ttu-id="b95b9-191">L’opérateur d’adresse ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)) et l’instruction `fixed` ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) divisent les variables en deux catégories : ***Variables fixes*** et ***variables déplaçables***.</span><span class="sxs-lookup"><span data-stu-id="b95b9-191">The address-of operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) and the `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) divide variables into two categories: ***Fixed variables*** and ***moveable variables***.</span></span>

<span data-ttu-id="b95b9-192">Les variables fixes résident dans des emplacements de stockage qui ne sont pas affectés par l’opération du garbage collector.</span><span class="sxs-lookup"><span data-stu-id="b95b9-192">Fixed variables reside in storage locations that are unaffected by operation of the garbage collector.</span></span> <span data-ttu-id="b95b9-193">(Les variables locales, les paramètres de valeur et les variables créées par des pointeurs de déréférencement sont des exemples de variables fixes.) En revanche, les variables déplaçables résident dans des emplacements de stockage qui peuvent faire l’objet d’un déplacement ou d’une suppression par le garbage collector.</span><span class="sxs-lookup"><span data-stu-id="b95b9-193">(Examples of fixed variables include local variables, value parameters, and variables created by dereferencing pointers.) On the other hand, moveable variables reside in storage locations that are subject to relocation or disposal by the garbage collector.</span></span> <span data-ttu-id="b95b9-194">(Les exemples de variables déplaçables incluent des champs dans des objets et des éléments de tableaux.)</span><span class="sxs-lookup"><span data-stu-id="b95b9-194">(Examples of moveable variables include fields in objects and elements of arrays.)</span></span>

<span data-ttu-id="b95b9-195">L’opérateur `&` ([l’opérateur d’adresse](unsafe-code.md#the-address-of-operator)) autorise l’obtention de l’adresse d’une variable fixe sans restrictions.</span><span class="sxs-lookup"><span data-stu-id="b95b9-195">The `&` operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) permits the address of a fixed variable to be obtained without restrictions.</span></span> <span data-ttu-id="b95b9-196">Toutefois, étant donné qu’une variable déplaçable est sujette à un déplacement ou une suppression par le garbage collector, l’adresse d’une variable déplaçable peut uniquement être obtenue à l’aide d’une instruction `fixed` ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) et cette adresse reste valide uniquement pour le durée de cette instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-196">However, because a moveable variable is subject to relocation or disposal by the garbage collector, the address of a moveable variable can only be obtained using a `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)), and that address remains valid only for the duration of that `fixed` statement.</span></span>

<span data-ttu-id="b95b9-197">En termes précis, une variable fixe est l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b95b9-197">In precise terms, a fixed variable is one of the following:</span></span>

*  <span data-ttu-id="b95b9-198">Variable résultant d’un *simple_name* ([noms simples](expressions.md#simple-names)) qui fait référence à une variable locale ou à un paramètre de valeur, sauf si la variable est capturée par une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b95b9-198">A variable resulting from a *simple_name* ([Simple names](expressions.md#simple-names)) that refers to a local variable or a value parameter, unless the variable is captured by an anonymous function.</span></span>
*  <span data-ttu-id="b95b9-199">Variable résultant d’un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `V.I`, où `V` est une variable fixe d’un *struct_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-199">A variable resulting from a *member_access* ([Member access](expressions.md#member-access)) of the form `V.I`, where `V` is a fixed variable of a *struct_type*.</span></span>
*  <span data-ttu-id="b95b9-200">Une variable résultant d’un *pointer_indirection_expression* ([indirection de pointeur](unsafe-code.md#pointer-indirection)) de la forme `*P`, un *pointer_member_access* ([accès au membre pointeur](unsafe-code.md#pointer-member-access)) de la forme `P->I` ou un *pointer_element_access* ( [Accès à l’élément pointeur](unsafe-code.md#pointer-element-access)) de la forme `P[E]`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-200">A variable resulting from a *pointer_indirection_expression* ([Pointer indirection](unsafe-code.md#pointer-indirection)) of the form `*P`, a *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) of the form `P->I`, or a *pointer_element_access* ([Pointer element access](unsafe-code.md#pointer-element-access)) of the form `P[E]`.</span></span>

<span data-ttu-id="b95b9-201">Toutes les autres variables sont classées comme variables déplaçables.</span><span class="sxs-lookup"><span data-stu-id="b95b9-201">All other variables are classified as moveable variables.</span></span>

<span data-ttu-id="b95b9-202">Notez qu’un champ statique est classé comme une variable déplaçable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-202">Note that a static field is classified as a moveable variable.</span></span> <span data-ttu-id="b95b9-203">Notez également qu’un paramètre `ref` ou `out` est classé en tant que variable déplaçable, même si l’argument donné pour le paramètre est une variable fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-203">Also note that a `ref` or `out` parameter is classified as a moveable variable, even if the argument given for the parameter is a fixed variable.</span></span> <span data-ttu-id="b95b9-204">Enfin, Notez qu’une variable produite par le déréférencement d’un pointeur est toujours classifiée comme une variable fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-204">Finally, note that a variable produced by dereferencing a pointer is always classified as a fixed variable.</span></span>

## <a name="pointer-conversions"></a><span data-ttu-id="b95b9-205">Conversions de pointeurs</span><span class="sxs-lookup"><span data-stu-id="b95b9-205">Pointer conversions</span></span>

<span data-ttu-id="b95b9-206">Dans un contexte non sécurisé, l’ensemble des conversions implicites disponibles ([conversions implicites](conversions.md#implicit-conversions)) est étendu pour inclure les conversions de pointeur implicites suivantes :</span><span class="sxs-lookup"><span data-stu-id="b95b9-206">In an unsafe context, the set of available implicit conversions ([Implicit conversions](conversions.md#implicit-conversions)) is extended to include the following implicit pointer conversions:</span></span>

*  <span data-ttu-id="b95b9-207">De n’importe quel *pointer_type* vers le type `void*`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-207">From any *pointer_type* to the type `void*`.</span></span>
*  <span data-ttu-id="b95b9-208">Du littéral `null` à n’importe quel *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-208">From the `null` literal to any *pointer_type*.</span></span>

<span data-ttu-id="b95b9-209">En outre, dans un contexte non sécurisé, l’ensemble des conversions explicites disponibles ([conversions explicites](conversions.md#explicit-conversions)) est étendu pour inclure les conversions de pointeur explicites suivantes :</span><span class="sxs-lookup"><span data-stu-id="b95b9-209">Additionally, in an unsafe context, the set of available explicit conversions ([Explicit conversions](conversions.md#explicit-conversions)) is extended to include the following explicit pointer conversions:</span></span>

*  <span data-ttu-id="b95b9-210">De n’importe quel *pointer_type* à n’importe quel autre *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-210">From any *pointer_type* to any other *pointer_type*.</span></span>
*  <span data-ttu-id="b95b9-211">À partir de `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong` à n’importe quel *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-211">From `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong` to any *pointer_type*.</span></span>
*  <span data-ttu-id="b95b9-212">De n’importe quel *pointer_type* à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-212">From any *pointer_type* to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="b95b9-213">Enfin, dans un contexte non sécurisé, l’ensemble de conversions implicites standard ([conversions implicites standard](conversions.md#standard-implicit-conversions)) comprend la conversion de pointeur suivante :</span><span class="sxs-lookup"><span data-stu-id="b95b9-213">Finally, in an unsafe context, the set of standard implicit conversions ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) includes the following pointer conversion:</span></span>

*  <span data-ttu-id="b95b9-214">De n’importe quel *pointer_type* vers le type `void*`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-214">From any *pointer_type* to the type `void*`.</span></span>

<span data-ttu-id="b95b9-215">Les conversions entre deux types pointeur ne modifient jamais la valeur du pointeur réel.</span><span class="sxs-lookup"><span data-stu-id="b95b9-215">Conversions between two pointer types never change the actual pointer value.</span></span> <span data-ttu-id="b95b9-216">En d’autres termes, une conversion d’un type pointeur vers un autre n’a aucun effet sur l’adresse sous-jacente donnée par le pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-216">In other words, a conversion from one pointer type to another has no effect on the underlying address given by the pointer.</span></span>

<span data-ttu-id="b95b9-217">Quand un type pointeur est converti en un autre, si le pointeur résultant n’est pas correctement aligné pour le type pointé, le comportement n’est pas défini si le résultat est déréférencé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-217">When one pointer type is converted to another, if the resulting pointer is not correctly aligned for the pointed-to type, the behavior is undefined if the result is dereferenced.</span></span> <span data-ttu-id="b95b9-218">En général, le concept « aligné correctement » est transitif : si un pointeur vers le type `A` est correctement aligné pour un pointeur vers le type `B` qui, à son tour, est correctement aligné pour un pointeur vers le type `C`, un pointeur vers le type `A` est correctement aligné pour un pointeur vers le type `C`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-218">In general, the concept "correctly aligned" is transitive: if a pointer to type `A` is correctly aligned for a pointer to type `B`, which, in turn, is correctly aligned for a pointer to type `C`, then a pointer to type `A` is correctly aligned for a pointer to type `C`.</span></span>

<span data-ttu-id="b95b9-219">Considérez le cas suivant dans lequel une variable ayant un type est accessible via un pointeur vers un type différent :</span><span class="sxs-lookup"><span data-stu-id="b95b9-219">Consider the following case in which a variable having one type is accessed via a pointer to a different type:</span></span>

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

<span data-ttu-id="b95b9-220">Quand un type pointeur est converti en un pointeur vers un octet, le résultat pointe vers l’octet adressé le plus bas de la variable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-220">When a pointer type is converted to a pointer to byte, the result points to the lowest addressed byte of the variable.</span></span> <span data-ttu-id="b95b9-221">Les incréments successifs du résultat, jusqu’à la taille de la variable, produisent des pointeurs vers les octets restants de cette variable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-221">Successive increments of the result, up to the size of the variable, yield pointers to the remaining bytes of that variable.</span></span> <span data-ttu-id="b95b9-222">Par exemple, la méthode suivante affiche chacun des huit octets d’une valeur double sous la forme d’une valeur hexadécimale :</span><span class="sxs-lookup"><span data-stu-id="b95b9-222">For example, the following method displays each of the eight bytes in a double as a hexadecimal value:</span></span>

```csharp
using System;

class Test
{
    unsafe static void Main() {
      double d = 123.456e23;
        unsafe {
           byte* pb = (byte*)&d;
            for (int i = 0; i < sizeof(double); ++i)
               Console.Write("{0:X2} ", *pb++);
            Console.WriteLine();
        }
    }
}
```

<span data-ttu-id="b95b9-223">Bien entendu, la sortie générée dépend de endianness.</span><span class="sxs-lookup"><span data-stu-id="b95b9-223">Of course, the output produced depends on endianness.</span></span>

<span data-ttu-id="b95b9-224">Les mappages entre les pointeurs et les entiers sont définis par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-224">Mappings between pointers and integers are implementation-defined.</span></span> <span data-ttu-id="b95b9-225">Toutefois, sur les architectures d’UC 32 \* et 64 bits avec un espace d’adressage linéaire, les conversions de pointeurs vers ou depuis les types intégraux se comportent généralement exactement comme des conversions de valeurs `uint` ou `ulong`, respectivement, vers ou à partir de ces types intégraux.</span><span class="sxs-lookup"><span data-stu-id="b95b9-225">However, on 32\* and 64-bit CPU architectures with a linear address space, conversions of pointers to or from integral types typically behave exactly like conversions of `uint` or `ulong` values, respectively, to or from those integral types.</span></span>

### <a name="pointer-arrays"></a><span data-ttu-id="b95b9-226">Tableaux de pointeurs</span><span class="sxs-lookup"><span data-stu-id="b95b9-226">Pointer arrays</span></span>

<span data-ttu-id="b95b9-227">Dans un contexte non sécurisé, des tableaux de pointeurs peuvent être construits.</span><span class="sxs-lookup"><span data-stu-id="b95b9-227">In an unsafe context, arrays of pointers can be constructed.</span></span> <span data-ttu-id="b95b9-228">Seules certaines des conversions qui s’appliquent à d’autres types de tableau sont autorisées sur les tableaux de pointeurs :</span><span class="sxs-lookup"><span data-stu-id="b95b9-228">Only some of the conversions that apply to other array types are allowed on pointer arrays:</span></span>

*  <span data-ttu-id="b95b9-229">La conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) de n’importe quel *array_type* en `System.Array` et les interfaces qu’elle implémente s’applique également aux tableaux de pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-229">The implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) from any *array_type* to `System.Array` and the interfaces it implements also applies to pointer arrays.</span></span> <span data-ttu-id="b95b9-230">Toutefois, toute tentative d’accès aux éléments du tableau par le biais de `System.Array` ou des interfaces qu’il implémente entraîne une exception au moment de l’exécution, car les types de pointeurs ne sont pas convertibles en `object`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-230">However, any attempt to access the array elements through `System.Array` or the interfaces it implements will result in an exception at run-time, as pointer types are not convertible to `object`.</span></span>
*  <span data-ttu-id="b95b9-231">Les conversions de référence implicite et explicite (conversions de[référence implicites](conversions.md#implicit-reference-conversions), [conversions de référence explicites](conversions.md#explicit-reference-conversions)) à partir d’un type de tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et ses interfaces de base génériques ne s’appliquent jamais aux tableaux de pointeurs. étant donné que les types pointeur ne peuvent pas être utilisés en tant qu’arguments de type, et qu’il n’y a pas de conversion de types pointeur en types non-pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-231">The implicit and explicit reference conversions ([Implicit reference conversions](conversions.md#implicit-reference-conversions), [Explicit reference conversions](conversions.md#explicit-reference-conversions)) from a single-dimensional array type `S[]` to `System.Collections.Generic.IList<T>` and its generic base interfaces never apply to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>
*  <span data-ttu-id="b95b9-232">La conversion de référence explicite ([conversions de référence explicites](conversions.md#explicit-reference-conversions)) de `System.Array` et les interfaces qu’elle implémente à n’importe quel *array_type* s’applique aux tableaux de pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-232">The explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Array` and the interfaces it implements to any *array_type* applies to pointer arrays.</span></span>
*  <span data-ttu-id="b95b9-233">Les conversions de référence explicites ([conversions de référence explicites](conversions.md#explicit-reference-conversions)) de `System.Collections.Generic.IList<S>` et ses interfaces de base en un type tableau unidimensionnel `T[]` ne s’appliquent jamais aux tableaux de pointeurs, car les types pointeur ne peuvent pas être utilisés en tant qu’arguments de type, et il y a aucune conversion des types pointeur en types non-pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-233">The explicit reference conversions ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Collections.Generic.IList<S>` and its base interfaces to a single-dimensional array type `T[]` never applies to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>

<span data-ttu-id="b95b9-234">Ces restrictions signifient que l’expansion de l’instruction `foreach` sur les tableaux décrits dans [l’instruction foreach](statements.md#the-foreach-statement) ne peut pas être appliquée aux tableaux de pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-234">These restrictions mean that the expansion for the `foreach` statement over arrays described in [The foreach statement](statements.md#the-foreach-statement) cannot be applied to pointer arrays.</span></span> <span data-ttu-id="b95b9-235">Au lieu de cela, une instruction foreach de la forme</span><span class="sxs-lookup"><span data-stu-id="b95b9-235">Instead, a foreach statement of the form</span></span>

```csharp
foreach (V v in x) embedded_statement
```

<span data-ttu-id="b95b9-236">où le type de `x` est un type de tableau de la forme `T[,,...,]`, `N` est le nombre de dimensions moins 1 et `T` ou `V` est un type pointeur, est développé à l’aide de boucles for imbriquées comme suit :</span><span class="sxs-lookup"><span data-stu-id="b95b9-236">where the type of `x` is an array type of the form `T[,,...,]`, `N` is the number of dimensions minus 1 and `T` or `V` is a pointer type, is expanded using nested for-loops as follows:</span></span>

```csharp
{
    T[,,...,] a = x;
    for (int i0 = a.GetLowerBound(0); i0 <= a.GetUpperBound(0); i0++)
    for (int i1 = a.GetLowerBound(1); i1 <= a.GetUpperBound(1); i1++)
    ...
    for (int iN = a.GetLowerBound(N); iN <= a.GetUpperBound(N); iN++) {
        V v = (V)a.GetValue(i0,i1,...,iN);
        embedded_statement
    }
}
```

<span data-ttu-id="b95b9-237">Les variables `a`, `i0`, `i1`,..., `iN` ne sont pas visibles ou accessibles à `x`, à l' *embedded_statement* ou à tout autre code source du programme.</span><span class="sxs-lookup"><span data-stu-id="b95b9-237">The variables `a`, `i0`, `i1`, ..., `iN` are not visible to or accessible to `x` or the *embedded_statement* or any other source code of the program.</span></span> <span data-ttu-id="b95b9-238">La variable `v` est en lecture seule dans l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-238">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="b95b9-239">S’il n’existe pas de conversion explicite ([conversions de pointeur](unsafe-code.md#pointer-conversions)) de `T` (type d’élément) en `V`, une erreur est générée et aucune autre étape n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-239">If there is not an explicit conversion ([Pointer conversions](unsafe-code.md#pointer-conversions)) from `T` (the element type) to `V`, an error is produced and no further steps are taken.</span></span> <span data-ttu-id="b95b9-240">Si `x` a la valeur `null`, une `System.NullReferenceException` est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b95b9-240">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

## <a name="pointers-in-expressions"></a><span data-ttu-id="b95b9-241">Pointeurs dans les expressions</span><span class="sxs-lookup"><span data-stu-id="b95b9-241">Pointers in expressions</span></span>

<span data-ttu-id="b95b9-242">Dans un contexte unsafe, une expression peut produire un résultat d’un type pointeur, mais en dehors d’un contexte unsafe, il s’agit d’une erreur de compilation pour qu’une expression soit d’un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-242">In an unsafe context, an expression may yield a result of a pointer type, but outside an unsafe context it is a compile-time error for an expression to be of a pointer type.</span></span> <span data-ttu-id="b95b9-243">En termes précis, en dehors d’un contexte non sécurisé, une erreur de compilation se produit si des *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([accès aux membres](expressions.md#member-access)), *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)) ou  *element_access* ([accès à l’élément](expressions.md#element-access)) est de type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-243">In precise terms, outside an unsafe context a compile-time error occurs if any *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)), or *element_access* ([Element access](expressions.md#element-access)) is of a pointer type.</span></span>

<span data-ttu-id="b95b9-244">Dans un contexte non sécurisé, les productions *primary_no_array_creation_expression* ([expressions primaires](expressions.md#primary-expressions)) et *unary_expression* ([opérateurs unaires](expressions.md#unary-operators)) autorisent les constructions supplémentaires suivantes :</span><span class="sxs-lookup"><span data-stu-id="b95b9-244">In an unsafe context, the *primary_no_array_creation_expression* ([Primary expressions](expressions.md#primary-expressions)) and *unary_expression* ([Unary operators](expressions.md#unary-operators)) productions permit the following additional constructs:</span></span>

```antlr
primary_no_array_creation_expression_unsafe
    : pointer_member_access
    | pointer_element_access
    | sizeof_expression
    ;

unary_expression_unsafe
    : pointer_indirection_expression
    | addressof_expression
    ;
```

<span data-ttu-id="b95b9-245">Ces constructions sont décrites dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="b95b9-245">These constructs are described in the following sections.</span></span> <span data-ttu-id="b95b9-246">La priorité et l’associativité des opérateurs non sécurisés sont implicites par la grammaire.</span><span class="sxs-lookup"><span data-stu-id="b95b9-246">The precedence and associativity of the unsafe operators is implied by the grammar.</span></span>

### <a name="pointer-indirection"></a><span data-ttu-id="b95b9-247">Indirection de pointeur</span><span class="sxs-lookup"><span data-stu-id="b95b9-247">Pointer indirection</span></span>

<span data-ttu-id="b95b9-248">Un *pointer_indirection_expression* se compose d’un astérisque (`*`) suivi d’un *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-248">A *pointer_indirection_expression* consists of an asterisk (`*`) followed by a *unary_expression*.</span></span>

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

<span data-ttu-id="b95b9-249">L’opérateur unaire `*` désigne l’indirection de pointeur et est utilisé pour obtenir la variable vers laquelle pointe un pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-249">The unary `*` operator denotes pointer indirection and is used to obtain the variable to which a pointer points.</span></span> <span data-ttu-id="b95b9-250">Le résultat de l’évaluation de `*P`, où `P` est une expression d’un type pointeur `T*`, est une variable de type `T`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-250">The result of evaluating `*P`, where `P` is an expression of a pointer type `T*`, is a variable of type `T`.</span></span> <span data-ttu-id="b95b9-251">Il s’agit d’une erreur au moment de la compilation pour appliquer l’opérateur unaire `*` à une expression de type `void*` ou à une expression qui n’est pas d’un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-251">It is a compile-time error to apply the unary `*` operator to an expression of type `void*` or to an expression that isn't of a pointer type.</span></span>

<span data-ttu-id="b95b9-252">L’effet de l’application de l’opérateur unaire `*` à un pointeur `null` est défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-252">The effect of applying the unary `*` operator to a `null` pointer is implementation-defined.</span></span> <span data-ttu-id="b95b9-253">En particulier, il n’y a aucune garantie que cette opération lève une `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-253">In particular, there is no guarantee that this operation throws a `System.NullReferenceException`.</span></span>

<span data-ttu-id="b95b9-254">Si une valeur non valide a été assignée au pointeur, le comportement de l’opérateur unaire `*` n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-254">If an invalid value has been assigned to the pointer, the behavior of the unary `*` operator is undefined.</span></span> <span data-ttu-id="b95b9-255">Parmi les valeurs non valides pour le déréférencement d’un pointeur par l’opérateur unaire `*`, on trouve une adresse alignée de manière inappropriée pour le type pointé (Voir l’exemple dans les [conversions de pointeur](unsafe-code.md#pointer-conversions)) et l’adresse d’une variable après la fin de sa durée de vie.</span><span class="sxs-lookup"><span data-stu-id="b95b9-255">Among the invalid values for dereferencing a pointer by the unary `*` operator are an address inappropriately aligned for the type pointed to (see example in [Pointer conversions](unsafe-code.md#pointer-conversions)), and the address of a variable after the end of its lifetime.</span></span>

<span data-ttu-id="b95b9-256">À des fins d’analyse d’assignation définie, une variable produite par l’évaluation d’une expression de la forme `*P` est considérée comme initialement assignée ([variables initialement affectées](variables.md#initially-assigned-variables)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-256">For purposes of definite assignment analysis, a variable produced by evaluating an expression of the form `*P` is considered initially assigned ([Initially assigned variables](variables.md#initially-assigned-variables)).</span></span>

### <a name="pointer-member-access"></a><span data-ttu-id="b95b9-257">Accès au membre pointeur</span><span class="sxs-lookup"><span data-stu-id="b95b9-257">Pointer member access</span></span>

<span data-ttu-id="b95b9-258">Un *pointer_member_access* se compose d’un *primary_expression*, suivi d’un jeton « `->` », suivi d’un *identificateur* et d’un *type_argument_list*facultatif.</span><span class="sxs-lookup"><span data-stu-id="b95b9-258">A *pointer_member_access* consists of a *primary_expression*, followed by a "`->`" token, followed by an *identifier* and an optional *type_argument_list*.</span></span>

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

<span data-ttu-id="b95b9-259">Dans un accès de membre de pointeur au format `P->I`, `P` doit être une expression d’un type pointeur autre que `void*`, et `I` doit désigner un membre accessible du type auquel `P` pointe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-259">In a pointer member access of the form `P->I`, `P` must be an expression of a pointer type other than `void*`, and `I` must denote an accessible member of the type to which `P` points.</span></span>

<span data-ttu-id="b95b9-260">Un accès de membre pointeur au format `P->I` est évalué exactement comme `(*P).I`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-260">A pointer member access of the form `P->I` is evaluated exactly as `(*P).I`.</span></span> <span data-ttu-id="b95b9-261">Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="b95b9-261">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="b95b9-262">Pour obtenir une description de l’opérateur d’accès aux membres (`.`), consultez [accès aux membres](expressions.md#member-access).</span><span class="sxs-lookup"><span data-stu-id="b95b9-262">For a description of the member access operator (`.`), see [Member access](expressions.md#member-access).</span></span>

<span data-ttu-id="b95b9-263">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-263">In the example</span></span>

```csharp
using System;

struct Point
{
    public int x;
    public int y;

    public override string ToString() {
        return "(" + x + "," + y + ")";
    }
}

class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            p->x = 10;
            p->y = 20;
            Console.WriteLine(p->ToString());
        }
    }
}
```

<span data-ttu-id="b95b9-264">l’opérateur `->` est utilisé pour accéder aux champs et appeler une méthode d’un struct via un pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-264">the `->` operator is used to access fields and invoke a method of a struct through a pointer.</span></span> <span data-ttu-id="b95b9-265">Étant donné que l’opération `P->I` équivaut précisément à `(*P).I`, la méthode `Main` peut également être écrite :</span><span class="sxs-lookup"><span data-stu-id="b95b9-265">Because the operation `P->I` is precisely equivalent to `(*P).I`, the `Main` method could equally well have been written:</span></span>

```csharp
class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            (*p).x = 10;
            (*p).y = 20;
            Console.WriteLine((*p).ToString());
        }
    }
}
```

### <a name="pointer-element-access"></a><span data-ttu-id="b95b9-266">Accès à l’élément pointeur</span><span class="sxs-lookup"><span data-stu-id="b95b9-266">Pointer element access</span></span>

<span data-ttu-id="b95b9-267">Un *pointer_element_access* se compose d’un *primary_no_array_creation_expression* suivi d’une expression placée dans « `[` » et « `]` ».</span><span class="sxs-lookup"><span data-stu-id="b95b9-267">A *pointer_element_access* consists of a *primary_no_array_creation_expression* followed by an expression enclosed in "`[`" and "`]`".</span></span>

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

<span data-ttu-id="b95b9-268">Dans un accès à un élément pointeur au format `P[E]`, `P` doit être une expression d’un type pointeur autre que `void*`, et `E` doit être une expression qui peut être convertie implicitement en `int`, `uint`, `long` ou `ulong`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-268">In a pointer element access of the form `P[E]`, `P` must be an expression of a pointer type other than `void*`, and `E` must be an expression that can be implicitly converted to `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="b95b9-269">Un accès à un élément pointeur au format `P[E]` est évalué exactement comme `*(P + E)`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-269">A pointer element access of the form `P[E]` is evaluated exactly as `*(P + E)`.</span></span> <span data-ttu-id="b95b9-270">Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="b95b9-270">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="b95b9-271">Pour obtenir une description de l’opérateur d’addition de pointeurs (`+`), consultez [opérations arithmétiques sur les pointeurs](unsafe-code.md#pointer-arithmetic).</span><span class="sxs-lookup"><span data-stu-id="b95b9-271">For a description of the pointer addition operator (`+`), see [Pointer arithmetic](unsafe-code.md#pointer-arithmetic).</span></span>

<span data-ttu-id="b95b9-272">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-272">In the example</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) p[i] = (char)i;
        }
    }
}
```

<span data-ttu-id="b95b9-273">un accès à un élément pointeur est utilisé pour initialiser la mémoire tampon de caractères dans une boucle `for`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-273">a pointer element access is used to initialize the character buffer in a `for` loop.</span></span> <span data-ttu-id="b95b9-274">Étant donné que l’opération `P[E]` équivaut précisément à `*(P + E)`, l’exemple peut également être écrit :</span><span class="sxs-lookup"><span data-stu-id="b95b9-274">Because the operation `P[E]` is precisely equivalent to `*(P + E)`, the example could equally well have been written:</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) *(p + i) = (char)i;
        }
    }
}
```

<span data-ttu-id="b95b9-275">L’opérateur d’accès à l’élément pointeur ne vérifie pas les erreurs de limites hors limites et le comportement lors de l’accès à un élément hors limites n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-275">The pointer element access operator does not check for out-of-bounds errors and the behavior when accessing an out-of-bounds element is undefined.</span></span> <span data-ttu-id="b95b9-276">Il s’agit du même que C C++et.</span><span class="sxs-lookup"><span data-stu-id="b95b9-276">This is the same as C and C++.</span></span>

### <a name="the-address-of-operator"></a><span data-ttu-id="b95b9-277">Opérateur d’adresse</span><span class="sxs-lookup"><span data-stu-id="b95b9-277">The address-of operator</span></span>

<span data-ttu-id="b95b9-278">Un *addressof_expression* se compose d’un et commercial (`&`) suivi d’un *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="b95b9-278">An *addressof_expression* consists of an ampersand (`&`) followed by a *unary_expression*.</span></span>

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

<span data-ttu-id="b95b9-279">Dans le cas d’une expression `E` qui est de type `T` et qui est classée en tant que variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), la construction `&E` calcule l’adresse de la variable donnée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-279">Given an expression `E` which is of a type `T` and is classified as a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), the construct `&E` computes the address of the variable given by `E`.</span></span> <span data-ttu-id="b95b9-280">Le type du résultat est `T*` et est classifié comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-280">The type of the result is `T*` and is classified as a value.</span></span> <span data-ttu-id="b95b9-281">Une erreur de compilation se produit si `E` n’est pas classée en tant que variable, si `E` est classée en tant que variable locale en lecture seule, ou si `E` désigne une variable déplaçable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-281">A compile-time error occurs if `E` is not classified as a variable, if `E` is classified as a read-only local variable, or if `E` denotes a moveable variable.</span></span> <span data-ttu-id="b95b9-282">Dans le dernier cas, une instruction fixed ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) peut être utilisée pour « corriger » temporairement la variable avant d’obtenir son adresse.</span><span class="sxs-lookup"><span data-stu-id="b95b9-282">In the last case, a fixed statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) can be used to temporarily "fix" the variable before obtaining its address.</span></span> <span data-ttu-id="b95b9-283">Comme indiqué dans l' [accès aux membres](expressions.md#member-access), en dehors d’un constructeur d’instance ou d’un constructeur statique pour un struct ou une classe qui définit un champ `readonly`, ce champ est considéré comme une valeur, et non comme une variable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-283">As stated in [Member access](expressions.md#member-access), outside an instance constructor or static constructor for a struct or class that defines a `readonly` field, that field is considered a value, not a variable.</span></span> <span data-ttu-id="b95b9-284">Par conséquent, son adresse ne peut pas être prise.</span><span class="sxs-lookup"><span data-stu-id="b95b9-284">As such, its address cannot be taken.</span></span> <span data-ttu-id="b95b9-285">De même, l’adresse d’une constante ne peut pas être prise.</span><span class="sxs-lookup"><span data-stu-id="b95b9-285">Similarly, the address of a constant cannot be taken.</span></span>

<span data-ttu-id="b95b9-286">L’opérateur `&` ne requiert pas l’assignation définitive de son argument, mais après une opération `&`, la variable à laquelle l’opérateur est appliqué est considérée comme assignée définitivement dans le chemin d’exécution dans lequel l’opération se produit.</span><span class="sxs-lookup"><span data-stu-id="b95b9-286">The `&` operator does not require its argument to be definitely assigned, but following an `&` operation, the variable to which the operator is applied is considered definitely assigned in the execution path in which the operation occurs.</span></span> <span data-ttu-id="b95b9-287">Il incombe au programmeur de s’assurer que l’initialisation correcte de la variable a réellement lieu dans cette situation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-287">It is the responsibility of the programmer to ensure that correct initialization of the variable actually does take place in this situation.</span></span>

<span data-ttu-id="b95b9-288">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-288">In the example</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        int i;
        unsafe {
            int* p = &i;
            *p = 123;
        }
        Console.WriteLine(i);
    }
}
```

<span data-ttu-id="b95b9-289">`i` est considéré comme définitivement affecté à la suite de l’opération `&i` utilisée pour initialiser `p`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-289">`i` is considered definitely assigned following the `&i` operation used to initialize `p`.</span></span> <span data-ttu-id="b95b9-290">L’assignation à `*p` en effet initialise `i`, mais l’inclusion de cette initialisation est la responsabilité du programmeur, et aucune erreur de compilation ne se produit si l’assignation a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-290">The assignment to `*p` in effect initializes `i`, but the inclusion of this initialization is the responsibility of the programmer, and no compile-time error would occur if the assignment was removed.</span></span>

<span data-ttu-id="b95b9-291">Les règles d’attribution définie pour l’opérateur `&` existent, de sorte que l’initialisation redondante de variables locales peut être évitée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-291">The rules of definite assignment for the `&` operator exist such that redundant initialization of local variables can be avoided.</span></span> <span data-ttu-id="b95b9-292">Par exemple, de nombreuses API externes prennent un pointeur vers une structure qui est remplie par l’API.</span><span class="sxs-lookup"><span data-stu-id="b95b9-292">For example, many external APIs take a pointer to a structure which is filled in by the API.</span></span> <span data-ttu-id="b95b9-293">Les appels à ces API passent généralement l’adresse d’une variable de struct locale et sans la règle, l’initialisation redondante de la variable de struct est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b95b9-293">Calls to such APIs typically pass the address of a local struct variable, and without the rule, redundant initialization of the struct variable would be required.</span></span>

### <a name="pointer-increment-and-decrement"></a><span data-ttu-id="b95b9-294">Incrémenter et décrémenter des pointeurs</span><span class="sxs-lookup"><span data-stu-id="b95b9-294">Pointer increment and decrement</span></span>

<span data-ttu-id="b95b9-295">Dans un contexte non sécurisé, les opérateurs `++` et `--` ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs d’incrémentation et de décrémentation de préfixe](expressions.md#prefix-increment-and-decrement-operators)) peuvent être appliqués aux variables de pointeur de tous les types, à l’exception de `void*`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-295">In an unsafe context, the `++` and `--` operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)) can be applied to pointer variables of all types except `void*`.</span></span> <span data-ttu-id="b95b9-296">Ainsi, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :</span><span class="sxs-lookup"><span data-stu-id="b95b9-296">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

<span data-ttu-id="b95b9-297">Les opérateurs produisent les mêmes résultats que `x + 1` et `x - 1`, respectivement ([arithmétique de pointeur](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-297">The operators produce the same results as `x + 1` and `x - 1`, respectively ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span> <span data-ttu-id="b95b9-298">En d’autres termes, pour une variable pointeur de type `T*`, l’opérateur `++` ajoute `sizeof(T)` à l’adresse contenue dans la variable, et l’opérateur `--` soustrait `sizeof(T)` de l’adresse contenue dans la variable.</span><span class="sxs-lookup"><span data-stu-id="b95b9-298">In other words, for a pointer variable of type `T*`, the `++` operator adds `sizeof(T)` to the address contained in the variable, and the `--` operator subtracts `sizeof(T)` from the address contained in the variable.</span></span>

<span data-ttu-id="b95b9-299">Si une opération d’incrémentation ou de décrémentation de pointeur dépasse le domaine du type pointeur, le résultat est défini par l’implémentation, mais aucune exception n’est générée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-299">If a pointer increment or decrement operation overflows the domain of the pointer type, the result is implementation-defined, but no exceptions are produced.</span></span>

### <a name="pointer-arithmetic"></a><span data-ttu-id="b95b9-300">Arithmétique des pointeurs</span><span class="sxs-lookup"><span data-stu-id="b95b9-300">Pointer arithmetic</span></span>

<span data-ttu-id="b95b9-301">Dans un contexte non sécurisé, les opérateurs `+` et `-` (opérateur d'[addition](expressions.md#addition-operator) et [opérateur de soustraction](expressions.md#subtraction-operator)) peuvent être appliqués aux valeurs de tous les types pointeur, à l’exception de `void*`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-301">In an unsafe context, the `+` and `-` operators ([Addition operator](expressions.md#addition-operator) and [Subtraction operator](expressions.md#subtraction-operator)) can be applied to values of all pointer types except `void*`.</span></span> <span data-ttu-id="b95b9-302">Ainsi, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :</span><span class="sxs-lookup"><span data-stu-id="b95b9-302">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

```csharp
T* operator +(T* x, int y);
T* operator +(T* x, uint y);
T* operator +(T* x, long y);
T* operator +(T* x, ulong y);

T* operator +(int x, T* y);
T* operator +(uint x, T* y);
T* operator +(long x, T* y);
T* operator +(ulong x, T* y);

T* operator -(T* x, int y);
T* operator -(T* x, uint y);
T* operator -(T* x, long y);
T* operator -(T* x, ulong y);

long operator -(T* x, T* y);
```

<span data-ttu-id="b95b9-303">À partir d’une expression `P` d’un type pointeur `T*` et d’une expression `N` de type `int`, `uint`, `long` ou `ulong`, les expressions `P + N` et `N + P` calculent la valeur de pointeur de type `T*` qui résulte de l’ajout de 0 à l’adresse fourni par 1.</span><span class="sxs-lookup"><span data-stu-id="b95b9-303">Given an expression `P` of a pointer type `T*` and an expression `N` of type `int`, `uint`, `long`, or `ulong`, the expressions `P + N` and `N + P` compute the pointer value of type `T*` that results from adding `N * sizeof(T)` to the address given by `P`.</span></span> <span data-ttu-id="b95b9-304">De même, l’expression `P - N` calcule la valeur de pointeur de type `T*` qui résulte de la soustraction de `N * sizeof(T)` de l’adresse donnée par `P`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-304">Likewise, the expression `P - N` computes the pointer value of type `T*` that results from subtracting `N * sizeof(T)` from the address given by `P`.</span></span>

<span data-ttu-id="b95b9-305">À partir de deux expressions, `P` et `Q`, d’un type pointeur `T*`, l’expression `P - Q` calcule la différence entre les adresses fournies par `P` et `Q`, puis divise cette différence par `sizeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-305">Given two expressions, `P` and `Q`, of a pointer type `T*`, the expression `P - Q` computes the difference between the addresses given by `P` and `Q` and then divides that difference by `sizeof(T)`.</span></span> <span data-ttu-id="b95b9-306">Le type du résultat est toujours `long`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-306">The type of the result is always `long`.</span></span> <span data-ttu-id="b95b9-307">En effet, `P - Q` est calculé comme `((long)(P) - (long)(Q)) / sizeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-307">In effect, `P - Q` is computed as `((long)(P) - (long)(Q)) / sizeof(T)`.</span></span>

<span data-ttu-id="b95b9-308">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-308">For example:</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        unsafe {
            int* values = stackalloc int[20];
            int* p = &values[1];
            int* q = &values[15];
            Console.WriteLine("p - q = {0}", p - q);
            Console.WriteLine("q - p = {0}", q - p);
        }
    }
}
```

<span data-ttu-id="b95b9-309">ce qui génère la sortie :</span><span class="sxs-lookup"><span data-stu-id="b95b9-309">which produces the output:</span></span>

```console
p - q = -14
q - p = 14
```

<span data-ttu-id="b95b9-310">Si une opération arithmétique de pointeur dépasse le domaine du type pointeur, le résultat est tronqué en mode d’implémentation défini, mais aucune exception n’est générée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-310">If a pointer arithmetic operation overflows the domain of the pointer type, the result is truncated in an implementation-defined fashion, but no exceptions are produced.</span></span>

### <a name="pointer-comparison"></a><span data-ttu-id="b95b9-311">Comparaison de pointeurs</span><span class="sxs-lookup"><span data-stu-id="b95b9-311">Pointer comparison</span></span>

<span data-ttu-id="b95b9-312">Dans un contexte non sécurisé, les opérateurs `==`, `!=`, `<`, `>`, `<=` et `=>` ([opérateurs relationnels et de test de type](expressions.md#relational-and-type-testing-operators)) peuvent être appliqués aux valeurs de tous les types pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-312">In an unsafe context, the `==`, `!=`, `<`, `>`, `<=`, and `=>` operators ([Relational and type-testing operators](expressions.md#relational-and-type-testing-operators)) can be applied to values of all pointer types.</span></span> <span data-ttu-id="b95b9-313">Les opérateurs de comparaison de pointeurs sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b95b9-313">The pointer comparison operators are:</span></span>

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

<span data-ttu-id="b95b9-314">Étant donné qu’une conversion implicite existe de tout type pointeur vers le type `void*`, les opérandes de tout type pointeur peuvent être comparés à l’aide de ces opérateurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-314">Because an implicit conversion exists from any pointer type to the `void*` type, operands of any pointer type can be compared using these operators.</span></span> <span data-ttu-id="b95b9-315">Les opérateurs de comparaison comparent les adresses données par les deux opérandes comme s’il s’agissait d’entiers non signés.</span><span class="sxs-lookup"><span data-stu-id="b95b9-315">The comparison operators compare the addresses given by the two operands as if they were unsigned integers.</span></span>

### <a name="the-sizeof-operator"></a><span data-ttu-id="b95b9-316">Opérateur sizeof</span><span class="sxs-lookup"><span data-stu-id="b95b9-316">The sizeof operator</span></span>

<span data-ttu-id="b95b9-317">L’opérateur `sizeof` retourne le nombre d’octets occupés par une variable d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="b95b9-317">The `sizeof` operator returns the number of bytes occupied by a variable of a given type.</span></span> <span data-ttu-id="b95b9-318">Le type spécifié comme opérande à `sizeof` doit être un *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-318">The type specified as an operand to `sizeof` must be an *unmanaged_type* ([Pointer types](unsafe-code.md#pointer-types)).</span></span>

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

<span data-ttu-id="b95b9-319">Le résultat de l’opérateur `sizeof` est une valeur de type `int`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-319">The result of the `sizeof` operator is a value of type `int`.</span></span> <span data-ttu-id="b95b9-320">Pour certains types prédéfinis, l’opérateur `sizeof` produit une valeur constante, comme indiqué dans le tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b95b9-320">For certain predefined types, the `sizeof` operator yields a constant value as shown in the table below.</span></span>


| <span data-ttu-id="b95b9-321">__Expression__</span><span class="sxs-lookup"><span data-stu-id="b95b9-321">__Expression__</span></span>   | <span data-ttu-id="b95b9-322">__Résultat__</span><span class="sxs-lookup"><span data-stu-id="b95b9-322">__Result__</span></span> |
|------------------|------------|
| `sizeof(sbyte)`  | `1`        |
| `sizeof(byte)`   | `1`        |
| `sizeof(short)`  | `2`        |
| `sizeof(ushort)` | `2`        |
| `sizeof(int)`    | `4`        |
| `sizeof(uint)`   | `4`        |
| `sizeof(long)`   | `8`        |
| `sizeof(ulong)`  | `8`        |
| `sizeof(char)`   | `2`        |
| `sizeof(float)`  | `4`        |
| `sizeof(double)` | `8`        |
| `sizeof(bool)`   | `1`        |

<span data-ttu-id="b95b9-323">Pour tous les autres types, le résultat de l’opérateur `sizeof` est défini par l’implémentation et est classifié comme une valeur, et non comme une constante.</span><span class="sxs-lookup"><span data-stu-id="b95b9-323">For all other types, the result of the `sizeof` operator is implementation-defined and is classified as a value, not a constant.</span></span>

<span data-ttu-id="b95b9-324">L’ordre dans lequel les membres sont empaquetés dans un struct n’est pas spécifié.</span><span class="sxs-lookup"><span data-stu-id="b95b9-324">The order in which members are packed into a struct is unspecified.</span></span>

<span data-ttu-id="b95b9-325">À des fins d’alignement, il peut y avoir un remplissage sans nom au début d’un struct, dans un struct et à la fin du struct.</span><span class="sxs-lookup"><span data-stu-id="b95b9-325">For alignment purposes, there may be unnamed padding at the beginning of a struct, within a struct, and at the end of the struct.</span></span> <span data-ttu-id="b95b9-326">Le contenu des bits utilisés comme remplissage est indéterminé.</span><span class="sxs-lookup"><span data-stu-id="b95b9-326">The contents of the bits used as padding are indeterminate.</span></span>

<span data-ttu-id="b95b9-327">En cas d’application à un opérande qui a le type struct, le résultat est le nombre total d’octets dans une variable de ce type, y compris tout remplissage.</span><span class="sxs-lookup"><span data-stu-id="b95b9-327">When applied to an operand that has struct type, the result is the total number of bytes in a variable of that type, including any padding.</span></span>

## <a name="the-fixed-statement"></a><span data-ttu-id="b95b9-328">Instruction fixed</span><span class="sxs-lookup"><span data-stu-id="b95b9-328">The fixed statement</span></span>

<span data-ttu-id="b95b9-329">Dans un contexte non sécurisé, la production *embedded_statement* ([instructions](statements.md)) autorise une construction supplémentaire, l’instruction `fixed`, qui est utilisée pour « corriger » une variable déplaçable de telle sorte que son adresse reste constante pendant toute la durée de l’instruction. .</span><span class="sxs-lookup"><span data-stu-id="b95b9-329">In an unsafe context, the *embedded_statement* ([Statements](statements.md)) production permits an additional construct, the `fixed` statement, which is used to "fix" a moveable variable such that its address remains constant for the duration of the statement.</span></span>

```antlr
fixed_statement
    : 'fixed' '(' pointer_type fixed_pointer_declarators ')' embedded_statement
    ;

fixed_pointer_declarators
    : fixed_pointer_declarator (','  fixed_pointer_declarator)*
    ;

fixed_pointer_declarator
    : identifier '=' fixed_pointer_initializer
    ;

fixed_pointer_initializer
    : '&' variable_reference
    | expression
    ;
```

<span data-ttu-id="b95b9-330">Chaque *fixed_pointer_declarator* déclare une variable locale du *pointer_type* donné et initialise cette variable locale avec l’adresse calculée par le *fixed_pointer_initializer*correspondant.</span><span class="sxs-lookup"><span data-stu-id="b95b9-330">Each *fixed_pointer_declarator* declares a local variable of the given *pointer_type* and initializes that local variable with the address computed by the corresponding *fixed_pointer_initializer*.</span></span> <span data-ttu-id="b95b9-331">Une variable locale déclarée dans une instruction `fixed` est accessible dans tout *fixed_pointer_initializer*s se trouvant à droite de la déclaration de cette variable et dans le *embedded_statement* de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-331">A local variable declared in a `fixed` statement is accessible in any *fixed_pointer_initializer*s occurring to the right of that variable's declaration, and in the *embedded_statement* of the `fixed` statement.</span></span> <span data-ttu-id="b95b9-332">Une variable locale déclarée par une instruction `fixed` est considérée comme étant en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="b95b9-332">A local variable declared by a `fixed` statement is considered read-only.</span></span> <span data-ttu-id="b95b9-333">Une erreur de compilation se produit si l’instruction incorporée tente de modifier cette variable locale (par le biais de l’assignation ou des opérateurs `++` et `--`) ou de la passer en tant que paramètre `ref` ou `out`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-333">A compile-time error occurs if the embedded statement attempts to modify this local variable (via assignment or the `++` and `--` operators) or pass it as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="b95b9-334">Un *fixed_pointer_initializer* peut être l’un des suivants :</span><span class="sxs-lookup"><span data-stu-id="b95b9-334">A *fixed_pointer_initializer* can be one of the following:</span></span>

*  <span data-ttu-id="b95b9-335">Le jeton « `&` » suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) à une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) d’un type non managé `T`, à condition que le type `T*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-335">The token "`&`" followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) to a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="b95b9-336">Dans ce cas, l’initialiseur calcule l’adresse de la variable donnée, et la variable est garantie à conserver à une adresse fixe pendant la durée de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-336">In this case, the initializer computes the address of the given variable, and the variable is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>
*  <span data-ttu-id="b95b9-337">Expression d’un *array_type* avec des éléments d’un type non managé `T`, à condition que le type `T*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-337">An expression of an *array_type* with elements of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="b95b9-338">Dans ce cas, l’initialiseur calcule l’adresse du premier élément du tableau, et le tableau entier est toujours conservé à une adresse fixe pendant toute la durée de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-338">In this case, the initializer computes the address of the first element in the array, and the entire array is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="b95b9-339">Si l’expression de tableau a la valeur null ou si le tableau n’a aucun élément, l’initialiseur calcule une adresse égale à zéro.</span><span class="sxs-lookup"><span data-stu-id="b95b9-339">If the array expression is null or if the array has zero elements, the initializer computes an address equal to zero.</span></span>
*  <span data-ttu-id="b95b9-340">Expression de type `string`, à condition que le type `char*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-340">An expression of type `string`, provided the type `char*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="b95b9-341">Dans ce cas, l’initialiseur calcule l’adresse du premier caractère de la chaîne, et la chaîne entière est garantie pour rester à une adresse fixe pendant la durée de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-341">In this case, the initializer computes the address of the first character in the string, and the entire string is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="b95b9-342">Le comportement de l’instruction `fixed` est défini par l’implémentation si l’expression de chaîne est null.</span><span class="sxs-lookup"><span data-stu-id="b95b9-342">The behavior of the `fixed` statement is implementation-defined if the string expression is null.</span></span>
*  <span data-ttu-id="b95b9-343">*Simple_name* ou *member_access* qui référence un membre de mémoire tampon de taille fixe d’une variable déplaçable, à condition que le type du membre de la mémoire tampon de taille fixe soit implicitement convertible en type pointeur donné dans l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-343">A *simple_name* or *member_access* that references a fixed size buffer member of a moveable variable, provided the type of the fixed size buffer member is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="b95b9-344">Dans ce cas, l’initialiseur calcule un pointeur vers le premier élément de la mémoire tampon de taille fixe ([mémoires tampons de taille fixe dans les expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), et la mémoire tampon de taille fixe est garantie pour rester à une adresse fixe pendant la durée de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-344">In this case, the initializer computes a pointer to the first element of the fixed size buffer ([Fixed size buffers in expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), and the fixed size buffer is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>

<span data-ttu-id="b95b9-345">Pour chaque adresse calculée par un *fixed_pointer_initializer* , l’instruction `fixed` garantit que la variable référencée par l’adresse n’est pas sujette à un déplacement ou une suppression par le garbage collector pendant la durée de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-345">For each address computed by a *fixed_pointer_initializer* the `fixed` statement ensures that the variable referenced by the address is not subject to relocation or disposal by the garbage collector for the duration of the `fixed` statement.</span></span> <span data-ttu-id="b95b9-346">Par exemple, si l’adresse calculée par un *fixed_pointer_initializer* fait référence à un champ d’un objet ou à un élément d’une instance de tableau, l’instruction `fixed` garantit que l’instance d’objet conteneur n’est pas déplacée ou supprimée pendant la durée de vie de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="b95b9-346">For example, if the address computed by a *fixed_pointer_initializer* references a field of an object or an element of an array instance, the `fixed` statement guarantees that the containing object instance is not relocated or disposed of during the lifetime of the statement.</span></span>

<span data-ttu-id="b95b9-347">Il incombe au programmeur de s’assurer que les pointeurs créés par les instructions `fixed` ne survivent pas au-delà de l’exécution de ces instructions.</span><span class="sxs-lookup"><span data-stu-id="b95b9-347">It is the programmer's responsibility to ensure that pointers created by `fixed` statements do not survive beyond execution of those statements.</span></span> <span data-ttu-id="b95b9-348">Par exemple, lorsque des pointeurs créés par des instructions `fixed` sont passés à des API externes, il incombe au programmeur de s’assurer que les API ne conservent aucune mémoire de ces pointeurs.</span><span class="sxs-lookup"><span data-stu-id="b95b9-348">For example, when pointers created by `fixed` statements are passed to external APIs, it is the programmer's responsibility to ensure that the APIs retain no memory of these pointers.</span></span>

<span data-ttu-id="b95b9-349">Les objets fixes peuvent entraîner la fragmentation du tas (car ils ne peuvent pas être déplacés).</span><span class="sxs-lookup"><span data-stu-id="b95b9-349">Fixed objects may cause fragmentation of the heap (because they can't be moved).</span></span> <span data-ttu-id="b95b9-350">Pour cette raison, les objets doivent être corrigés uniquement lorsque cela est absolument nécessaire, puis uniquement pour la durée la plus faible possible.</span><span class="sxs-lookup"><span data-stu-id="b95b9-350">For that reason, objects should be fixed only when absolutely necessary and then only for the shortest amount of time possible.</span></span>

<span data-ttu-id="b95b9-351">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-351">The example</span></span>

```csharp
class Test
{
    static int x;
    int y;

    unsafe static void F(int* p) {
        *p = 1;
    }

    static void Main() {
        Test t = new Test();
        int[] a = new int[10];
        unsafe {
            fixed (int* p = &x) F(p);
            fixed (int* p = &t.y) F(p);
            fixed (int* p = &a[0]) F(p);
            fixed (int* p = a) F(p);
        }
    }
}
```

<span data-ttu-id="b95b9-352">illustre plusieurs utilisations de l’instruction `fixed`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-352">demonstrates several uses of the `fixed` statement.</span></span> <span data-ttu-id="b95b9-353">La première instruction corrige et obtient l’adresse d’un champ statique, la deuxième instruction corrige et obtient l’adresse d’un champ d’instance, et la troisième instruction corrige et obtient l’adresse d’un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="b95b9-353">The first statement fixes and obtains the address of a static field, the second statement fixes and obtains the address of an instance field, and the third statement fixes and obtains the address of an array element.</span></span> <span data-ttu-id="b95b9-354">Dans chaque cas, l’utilisation de l’opérateur normal `&` était une erreur, car les variables sont toutes classées comme variables déplaçables.</span><span class="sxs-lookup"><span data-stu-id="b95b9-354">In each case it would have been an error to use the regular `&` operator since the variables are all classified as moveable variables.</span></span>

<span data-ttu-id="b95b9-355">La quatrième instruction `fixed` de l’exemple ci-dessus produit un résultat similaire à la troisième.</span><span class="sxs-lookup"><span data-stu-id="b95b9-355">The fourth `fixed` statement in the example above produces a similar result to the third.</span></span>

<span data-ttu-id="b95b9-356">Cet exemple de l’instruction `fixed` utilise `string` :</span><span class="sxs-lookup"><span data-stu-id="b95b9-356">This example of the `fixed` statement uses `string`:</span></span>

```csharp
class Test
{
    static string name = "xx";

    unsafe static void F(char* p) {
        for (int i = 0; p[i] != '\0'; ++i)
            Console.WriteLine(p[i]);
    }

    static void Main() {
        unsafe {
            fixed (char* p = name) F(p);
            fixed (char* p = "xx") F(p);
        }
    }
}
```

<span data-ttu-id="b95b9-357">Dans un tableau de contexte non sécurisé, les éléments des tableaux unidimensionnels sont stockés dans l’ordre d’index de plus en plus important, en commençant par l’index `0` et en terminant par l’index `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-357">In an unsafe context array elements of single-dimensional arrays are stored in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="b95b9-358">Pour les tableaux multidimensionnels, les éléments de tableau sont stockés de telle sorte que les index de la dimension la plus à droite soient augmentés en premier, puis la dimension gauche suivante, et ainsi de suite vers la gauche.</span><span class="sxs-lookup"><span data-stu-id="b95b9-358">For multi-dimensional arrays, array elements are stored such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span> <span data-ttu-id="b95b9-359">Dans une instruction `fixed` qui obtient un pointeur `p` vers une instance de tableau `a`, les valeurs de pointeur allant de `p` à `p + a.Length - 1` représentent les adresses des éléments dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="b95b9-359">Within a `fixed` statement that obtains a pointer `p` to an array instance `a`, the pointer values ranging from `p` to `p + a.Length - 1` represent addresses of the elements in the array.</span></span> <span data-ttu-id="b95b9-360">De même, les variables allant de `p[0]` à `p[a.Length - 1]` représentent les éléments de tableau réels.</span><span class="sxs-lookup"><span data-stu-id="b95b9-360">Likewise, the variables ranging from `p[0]` to `p[a.Length - 1]` represent the actual array elements.</span></span> <span data-ttu-id="b95b9-361">Étant donné la façon dont les tableaux sont stockés, nous pouvons traiter un tableau de toute dimension comme s’il était linéaire.</span><span class="sxs-lookup"><span data-stu-id="b95b9-361">Given the way in which arrays are stored, we can treat an array of any dimension as though it were linear.</span></span>

<span data-ttu-id="b95b9-362">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-362">For example:</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        int[,,] a = new int[2,3,4];
        unsafe {
            fixed (int* p = a) {
                for (int i = 0; i < a.Length; ++i)    // treat as linear
                    p[i] = i;
            }
        }

        for (int i = 0; i < 2; ++i)
            for (int j = 0; j < 3; ++j) {
                for (int k = 0; k < 4; ++k)
                    Console.Write("[{0},{1},{2}] = {3,2} ", i, j, k, a[i,j,k]);
                Console.WriteLine();
            }
    }
}
```

<span data-ttu-id="b95b9-363">ce qui génère la sortie :</span><span class="sxs-lookup"><span data-stu-id="b95b9-363">which produces the output:</span></span>

```console
[0,0,0] =  0 [0,0,1] =  1 [0,0,2] =  2 [0,0,3] =  3
[0,1,0] =  4 [0,1,1] =  5 [0,1,2] =  6 [0,1,3] =  7
[0,2,0] =  8 [0,2,1] =  9 [0,2,2] = 10 [0,2,3] = 11
[1,0,0] = 12 [1,0,1] = 13 [1,0,2] = 14 [1,0,3] = 15
[1,1,0] = 16 [1,1,1] = 17 [1,1,2] = 18 [1,1,3] = 19
[1,2,0] = 20 [1,2,1] = 21 [1,2,2] = 22 [1,2,3] = 23
```

<span data-ttu-id="b95b9-364">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-364">In the example</span></span>

```csharp
class Test
{
    unsafe static void Fill(int* p, int count, int value) {
        for (; count != 0; count--) *p++ = value;
    }

    static void Main() {
        int[] a = new int[100];
        unsafe {
            fixed (int* p = a) Fill(p, 100, -1);
        }
    }
}
```

<span data-ttu-id="b95b9-365">une instruction `fixed` est utilisée pour corriger un tableau afin que son adresse puisse être passée à une méthode qui prend un pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-365">a `fixed` statement is used to fix an array so its address can be passed to a method that takes a pointer.</span></span>

<span data-ttu-id="b95b9-366">Dans l'exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-366">In the example:</span></span>

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    Font f;

    unsafe static void Main()
    {
        Test test = new Test();
        test.f.size = 10;
        fixed (char* p = test.f.name) {
            PutString("Times New Roman", p, 32);
        }
    }
}
```

<span data-ttu-id="b95b9-367">une instruction fixed est utilisée pour corriger une mémoire tampon de taille fixe d’un struct afin que son adresse puisse être utilisée comme pointeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-367">a fixed statement is used to fix a fixed size buffer of a struct so its address can be used as a pointer.</span></span>

<span data-ttu-id="b95b9-368">Une valeur `char*` produite en résolvant une instance de chaîne pointe toujours vers une chaîne terminée par le caractère null.</span><span class="sxs-lookup"><span data-stu-id="b95b9-368">A `char*` value produced by fixing a string instance always points to a null-terminated string.</span></span> <span data-ttu-id="b95b9-369">Dans une instruction fixed qui obtient un pointeur `p` vers une instance de chaîne `s`, les valeurs de pointeur comprises entre `p` et `p + s.Length - 1` représentent les adresses des caractères de la chaîne, tandis que la valeur de pointeur `p + s.Length` pointe toujours sur un caractère null (le caractère avec la valeur `'\0'`).</span><span class="sxs-lookup"><span data-stu-id="b95b9-369">Within a fixed statement that obtains a pointer `p` to a string instance `s`, the pointer values ranging from `p` to `p + s.Length - 1` represent addresses of the characters in the string, and the pointer value `p + s.Length` always points to a null character (the character with value `'\0'`).</span></span>

<span data-ttu-id="b95b9-370">La modification d’objets de type managé par le biais de pointeurs fixes peut engendrer un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-370">Modifying objects of managed type through fixed pointers can results in undefined behavior.</span></span> <span data-ttu-id="b95b9-371">Par exemple, étant donné que les chaînes sont immuables, il incombe au programmeur de s’assurer que les caractères référencés par un pointeur vers une chaîne fixe ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="b95b9-371">For example, because strings are immutable, it is the programmer's responsibility to ensure that the characters referenced by a pointer to a fixed string are not modified.</span></span>

<span data-ttu-id="b95b9-372">L’arrêt automatique des chaînes de caractères est particulièrement pratique lors de l’appel d’API externes qui attendent des chaînes de style C.</span><span class="sxs-lookup"><span data-stu-id="b95b9-372">The automatic null-termination of strings is particularly convenient when calling external APIs that expect "C-style" strings.</span></span> <span data-ttu-id="b95b9-373">Notez, toutefois, qu’une instance de chaîne est autorisée à contenir des caractères null.</span><span class="sxs-lookup"><span data-stu-id="b95b9-373">Note, however, that a string instance is permitted to contain null characters.</span></span> <span data-ttu-id="b95b9-374">Si ces caractères Null sont présents, la chaîne apparaît tronquée lorsqu’elle est traitée comme un `char*` se terminant par un caractère null.</span><span class="sxs-lookup"><span data-stu-id="b95b9-374">If such null characters are present, the string will appear truncated when treated as a null-terminated `char*`.</span></span>

## <a name="fixed-size-buffers"></a><span data-ttu-id="b95b9-375">Mémoires tampons de taille fixe</span><span class="sxs-lookup"><span data-stu-id="b95b9-375">Fixed size buffers</span></span>

<span data-ttu-id="b95b9-376">Les mémoires tampons de taille fixe sont utilisées pour déclarer des tableaux en ligne « C style » comme membres de structs, et sont principalement utiles pour l’interfaçage avec les API non managées.</span><span class="sxs-lookup"><span data-stu-id="b95b9-376">Fixed size buffers are used to declare "C style" in-line arrays as members of structs, and are primarily useful for interfacing with unmanaged APIs.</span></span>

### <a name="fixed-size-buffer-declarations"></a><span data-ttu-id="b95b9-377">Déclarations de mémoire tampon de taille fixe</span><span class="sxs-lookup"><span data-stu-id="b95b9-377">Fixed size buffer declarations</span></span>

<span data-ttu-id="b95b9-378">Une ***mémoire tampon de taille fixe*** est un membre qui représente le stockage pour une mémoire tampon de longueur fixe des variables d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="b95b9-378">A ***fixed size buffer*** is a member that represents storage for a fixed length buffer of variables of a given type.</span></span> <span data-ttu-id="b95b9-379">Une déclaration de mémoire tampon de taille fixe introduit une ou plusieurs mémoires tampons de taille fixe d’un type d’élément donné.</span><span class="sxs-lookup"><span data-stu-id="b95b9-379">A fixed size buffer declaration introduces one or more fixed size buffers of a given element type.</span></span> <span data-ttu-id="b95b9-380">Les mémoires tampons de taille fixe sont autorisées uniquement dans les déclarations de struct et peuvent se produire uniquement dans des contextes non sécurisés ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-380">Fixed size buffers are only permitted in struct declarations and can only occur in unsafe contexts ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

```antlr
struct_member_declaration_unsafe
    : fixed_size_buffer_declaration
    ;

fixed_size_buffer_declaration
    : attributes? fixed_size_buffer_modifier* 'fixed' buffer_element_type fixed_size_buffer_declarator+ ';'
    ;

fixed_size_buffer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'unsafe'
    ;

buffer_element_type
    : type
    ;

fixed_size_buffer_declarator
    : identifier '[' constant_expression ']'
    ;
```

<span data-ttu-id="b95b9-381">Une déclaration de mémoire tampon de taille fixe peut inclure un ensemble d’attributs ([attributs](attributes.md)), un modificateur `new` ([modificateurs](classes.md#modifiers)), une combinaison valide des quatre modificateurs d’accès ([paramètres de type et contraintes](classes.md#type-parameters-and-constraints)) et un modificateur `unsafe` ([unsafe contextes](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-381">A fixed size buffer declaration may include a set of attributes ([Attributes](attributes.md)), a `new` modifier ([Modifiers](classes.md#modifiers)), a valid combination of the four access modifiers ([Type parameters and constraints](classes.md#type-parameters-and-constraints)) and an `unsafe` modifier ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="b95b9-382">Les attributs et les modificateurs s’appliquent à tous les membres déclarés par la déclaration de mémoire tampon de taille fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-382">The attributes and modifiers apply to all of the members declared by the fixed size buffer declaration.</span></span> <span data-ttu-id="b95b9-383">Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de mémoire tampon de taille fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-383">It is an error for the same modifier to appear multiple times in a fixed size buffer declaration.</span></span>

<span data-ttu-id="b95b9-384">Une déclaration de mémoire tampon de taille fixe n’est pas autorisée à inclure le modificateur `static`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-384">A fixed size buffer declaration is not permitted to include the `static` modifier.</span></span>

<span data-ttu-id="b95b9-385">Le type d’élément de mémoire tampon d’une déclaration de mémoire tampon de taille fixe spécifie le type d’élément de la ou des mémoires tampons introduites par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="b95b9-385">The buffer element type of a fixed size buffer declaration specifies the element type of the buffer(s) introduced by the declaration.</span></span> <span data-ttu-id="b95b9-386">Le type d’élément de la mémoire tampon doit être l’un des types prédéfinis `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, 0 ou 1.</span><span class="sxs-lookup"><span data-stu-id="b95b9-386">The buffer element type must be one of the predefined types `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, or `bool`.</span></span>

<span data-ttu-id="b95b9-387">Le type d’élément de mémoire tampon est suivi d’une liste de déclarateurs de mémoires tampons de taille fixe, chacun d’entre eux introduisant un nouveau membre.</span><span class="sxs-lookup"><span data-stu-id="b95b9-387">The buffer element type is followed by a list of fixed size buffer declarators, each of which introduces a new member.</span></span> <span data-ttu-id="b95b9-388">Un déclarateur de mémoire tampon de taille fixe se compose d’un identificateur qui nomme le membre, suivi d’une expression constante placée entre des jetons `[` et `]`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-388">A fixed size buffer declarator consists of an identifier that names the member, followed by a constant expression enclosed in `[` and `]` tokens.</span></span> <span data-ttu-id="b95b9-389">L’expression constante indique le nombre d’éléments dans le membre introduit par ce déclarateur de mémoire tampon de taille fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-389">The constant expression denotes the number of elements in the member introduced by that fixed size buffer declarator.</span></span> <span data-ttu-id="b95b9-390">Le type de l’expression constante doit être implicitement convertible en type `int`, et la valeur doit être un entier positif différent de zéro.</span><span class="sxs-lookup"><span data-stu-id="b95b9-390">The type of the constant expression must be implicitly convertible to type `int`, and the value must be a non-zero positive integer.</span></span>

<span data-ttu-id="b95b9-391">Il est garanti que les éléments d’une mémoire tampon de taille fixe sont disposés de façon séquentielle dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="b95b9-391">The elements of a fixed size buffer are guaranteed to be laid out sequentially in memory.</span></span>

<span data-ttu-id="b95b9-392">Une déclaration de mémoire tampon de taille fixe qui déclare plusieurs mémoires tampons de taille fixe équivaut à plusieurs déclarations d’une déclaration de mémoire tampon de taille fixe unique avec les mêmes attributs et types d’éléments.</span><span class="sxs-lookup"><span data-stu-id="b95b9-392">A fixed size buffer declaration that declares multiple fixed size buffers is equivalent to multiple declarations of a single fixed size buffer declaration with the same attributes, and element types.</span></span> <span data-ttu-id="b95b9-393">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b95b9-393">For example</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5], y[10], z[100];
}
```

<span data-ttu-id="b95b9-394">est équivalent à</span><span class="sxs-lookup"><span data-stu-id="b95b9-394">is equivalent to</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5];
   public fixed int y[10];
   public fixed int z[100];
}
```

### <a name="fixed-size-buffers-in-expressions"></a><span data-ttu-id="b95b9-395">Mémoires tampons de taille fixe dans les expressions</span><span class="sxs-lookup"><span data-stu-id="b95b9-395">Fixed size buffers in expressions</span></span>

<span data-ttu-id="b95b9-396">La recherche de membre ([opérateurs](expressions.md#operators)) d’un membre de mémoire tampon de taille fixe se déroule exactement comme la recherche de membres d’un champ.</span><span class="sxs-lookup"><span data-stu-id="b95b9-396">Member lookup ([Operators](expressions.md#operators)) of a fixed size buffer member proceeds exactly like member lookup of a field.</span></span>

<span data-ttu-id="b95b9-397">Une mémoire tampon de taille fixe peut être référencée dans une expression à l’aide d’un *simple_name* ([inférence de type](expressions.md#type-inference)) ou d’un *member_access* ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-397">A fixed size buffer can be referenced in an expression using a *simple_name* ([Type inference](expressions.md#type-inference)) or a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="b95b9-398">Lorsqu’un membre de la mémoire tampon de taille fixe est référencé comme un nom simple, l’effet est le même qu’un accès au membre de la forme `this.I`, où `I` est le membre de mémoire tampon de taille fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-398">When a fixed size buffer member is referenced as a simple name, the effect is the same as a member access of the form `this.I`, where `I` is the fixed size buffer member.</span></span>

<span data-ttu-id="b95b9-399">Dans un accès aux membres de la forme `E.I`, si `E` est de type struct et qu’une recherche de membre de `I` dans ce type struct identifie un membre de taille fixe, `E.I` est évaluée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b95b9-399">In a member access of the form `E.I`, if `E` is of a struct type and a member lookup of `I` in that struct type identifies a fixed size member, then `E.I` is evaluated an classified as follows:</span></span>

*  <span data-ttu-id="b95b9-400">Si l’expression `E.I` ne se produit pas dans un contexte unsafe, une erreur se produit au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-400">If the expression `E.I` does not occur in an unsafe context, a compile-time error occurs.</span></span>
*  <span data-ttu-id="b95b9-401">Si `E` est classée en tant que valeur, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b95b9-401">If `E` is classified as a value, a compile-time error occurs.</span></span>
*  <span data-ttu-id="b95b9-402">Sinon, si `E` est une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) et que l’expression `E.I` n’est pas un *fixed_pointer_initializer* ([l’instruction fixed](unsafe-code.md#the-fixed-statement)), une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b95b9-402">Otherwise, if `E` is a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) and the expression `E.I` is not a *fixed_pointer_initializer* ([The fixed statement](unsafe-code.md#the-fixed-statement)), a compile-time error occurs.</span></span>
*  <span data-ttu-id="b95b9-403">Dans le cas contraire, `E` fait référence à une variable fixe et le résultat de l’expression est un pointeur vers le premier élément du membre de la mémoire tampon de taille fixe `I` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-403">Otherwise, `E` references a fixed variable and the result of the expression is a pointer to the first element of the fixed size buffer member `I` in `E`.</span></span> <span data-ttu-id="b95b9-404">Le résultat est de type `S*`, où `S` est le type d’élément de `I` et est classifié comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b95b9-404">The result is of type `S*`, where `S` is the element type of `I`, and is classified as a value.</span></span>

<span data-ttu-id="b95b9-405">Les éléments suivants de la mémoire tampon de taille fixe sont accessibles à l’aide d’opérations de pointeur à partir du premier élément.</span><span class="sxs-lookup"><span data-stu-id="b95b9-405">The subsequent elements of the fixed size buffer can be accessed using pointer operations from the first element.</span></span> <span data-ttu-id="b95b9-406">Contrairement à l’accès aux tableaux, l’accès aux éléments d’une mémoire tampon de taille fixe est une opération risquée et l’option n’est pas cochée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-406">Unlike access to arrays, access to the elements of a fixed size buffer is an unsafe operation and is not range checked.</span></span>

<span data-ttu-id="b95b9-407">L’exemple suivant déclare et utilise un struct avec un membre de mémoire tampon de taille fixe.</span><span class="sxs-lookup"><span data-stu-id="b95b9-407">The following example declares and uses a struct with a fixed size buffer member.</span></span>

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    unsafe static void Main()
    {
        Font f;
        f.size = 10;
        PutString("Times New Roman", f.name, 32);
    }
}
```

### <a name="definite-assignment-checking"></a><span data-ttu-id="b95b9-408">Vérification de l’attribution définie</span><span class="sxs-lookup"><span data-stu-id="b95b9-408">Definite assignment checking</span></span>

<span data-ttu-id="b95b9-409">Les mémoires tampons de taille fixe ne sont pas soumises à la vérification de l’assignation définie ([assignation définie](variables.md#definite-assignment)) et les membres de la mémoire tampon de taille fixe sont ignorés à des fins de vérification de l’assignation définie des variables de type struct.</span><span class="sxs-lookup"><span data-stu-id="b95b9-409">Fixed size buffers are not subject to definite assignment checking ([Definite assignment](variables.md#definite-assignment)), and fixed size buffer members are ignored for purposes of definite assignment checking of struct type variables.</span></span>

<span data-ttu-id="b95b9-410">Lorsque la variable de struct conteneur la plus externe d’un membre de mémoire tampon de taille fixe est une variable statique, une variable d’instance d’une instance de classe ou un élément de tableau, les éléments de la mémoire tampon de taille fixe sont automatiquement initialisés à leurs valeurs par défaut ([par défaut valeurs](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-410">When the outermost containing struct variable of a fixed size buffer member is a static variable, an instance variable of a class instance, or an array element, the elements of the fixed size buffer are automatically initialized to their default values ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="b95b9-411">Dans tous les autres cas, le contenu initial d’une mémoire tampon de taille fixe n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-411">In all other cases, the initial content of a fixed size buffer is undefined.</span></span>

## <a name="stack-allocation"></a><span data-ttu-id="b95b9-412">Allocation de pile</span><span class="sxs-lookup"><span data-stu-id="b95b9-412">Stack allocation</span></span>

<span data-ttu-id="b95b9-413">Dans un contexte non sécurisé, une déclaration de variable locale ([déclarations de variable locale](statements.md#local-variable-declarations)) peut inclure un initialiseur d’allocation de pile qui alloue de la mémoire à partir de la pile des appels.</span><span class="sxs-lookup"><span data-stu-id="b95b9-413">In an unsafe context, a local variable declaration ([Local variable declarations](statements.md#local-variable-declarations)) may include a stack allocation initializer which allocates memory from the call stack.</span></span>

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

<span data-ttu-id="b95b9-414">*Unmanaged_type* indique le type des éléments qui seront stockés dans l’emplacement nouvellement alloué, et l' *expression* indique le nombre de ces éléments.</span><span class="sxs-lookup"><span data-stu-id="b95b9-414">The *unmanaged_type* indicates the type of the items that will be stored in the newly allocated location, and the *expression* indicates the number of these items.</span></span> <span data-ttu-id="b95b9-415">Prises ensemble, elles spécifient la taille d’allocation requise.</span><span class="sxs-lookup"><span data-stu-id="b95b9-415">Taken together, these specify the required allocation size.</span></span> <span data-ttu-id="b95b9-416">Étant donné que la taille d’une allocation de pile ne peut pas être négative, il s’agit d’une erreur au moment de la compilation pour spécifier le nombre d’éléments en tant que *constant_expression* qui prend une valeur négative.</span><span class="sxs-lookup"><span data-stu-id="b95b9-416">Since the size of a stack allocation cannot be negative, it is a compile-time error to specify the number of items as a *constant_expression* that evaluates to a negative value.</span></span>

<span data-ttu-id="b95b9-417">Un initialiseur d’allocation de pile de la forme `stackalloc T[E]` requiert que `T` soit un type non managé ([types pointeur](unsafe-code.md#pointer-types)) et `E` soit une expression de type `int`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-417">A stack allocation initializer of the form `stackalloc T[E]` requires `T` to be an unmanaged type ([Pointer types](unsafe-code.md#pointer-types)) and `E` to be an expression of type `int`.</span></span> <span data-ttu-id="b95b9-418">La construction alloue `E * sizeof(T)` octets à partir de la pile des appels et retourne un pointeur, de type `T*`, au bloc nouvellement alloué.</span><span class="sxs-lookup"><span data-stu-id="b95b9-418">The construct allocates `E * sizeof(T)` bytes from the call stack and returns a pointer, of type `T*`, to the newly allocated block.</span></span> <span data-ttu-id="b95b9-419">Si `E` est une valeur négative, le comportement n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-419">If `E` is a negative value, then the behavior is undefined.</span></span> <span data-ttu-id="b95b9-420">Si `E` est égal à zéro, aucune allocation n’est effectuée et le pointeur retourné est défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b95b9-420">If `E` is zero, then no allocation is made, and the pointer returned is implementation-defined.</span></span> <span data-ttu-id="b95b9-421">Si la mémoire disponible est insuffisante pour allouer un bloc de la taille donnée, une `System.StackOverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-421">If there is not enough memory available to allocate a block of the given size, a `System.StackOverflowException` is thrown.</span></span>

<span data-ttu-id="b95b9-422">Le contenu de la mémoire nouvellement allouée n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b95b9-422">The content of the newly allocated memory is undefined.</span></span>

<span data-ttu-id="b95b9-423">Les initialiseurs d’allocation de pile ne sont pas autorisés dans les blocs `catch` ou `finally` ([instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="b95b9-423">Stack allocation initializers are not permitted in `catch` or `finally` blocks ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="b95b9-424">Il n’existe aucun moyen de libérer explicitement la mémoire allouée à l’aide de `stackalloc`.</span><span class="sxs-lookup"><span data-stu-id="b95b9-424">There is no way to explicitly free memory allocated using `stackalloc`.</span></span> <span data-ttu-id="b95b9-425">Tous les blocs de mémoire alloués par la pile créés lors de l’exécution d’un membre de fonction sont automatiquement ignorés quand ce membre de fonction retourne.</span><span class="sxs-lookup"><span data-stu-id="b95b9-425">All stack allocated memory blocks created during the execution of a function member are automatically discarded when that function member returns.</span></span> <span data-ttu-id="b95b9-426">Cela correspond à la fonction `alloca`, une extension couramment trouvée dans C et C++ les implémentations.</span><span class="sxs-lookup"><span data-stu-id="b95b9-426">This corresponds to the `alloca` function, an extension commonly found in C and C++ implementations.</span></span>

<span data-ttu-id="b95b9-427">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b95b9-427">In the example</span></span>

```csharp
using System;

class Test
{
    static string IntToString(int value) {
        int n = value >= 0? value: -value;
        unsafe {
            char* buffer = stackalloc char[16];
            char* p = buffer + 16;
            do {
                *--p = (char)(n % 10 + '0');
                n /= 10;
            } while (n != 0);
            if (value < 0) *--p = '-';
            return new string(p, 0, (int)(buffer + 16 - p));
        }
    }

    static void Main() {
        Console.WriteLine(IntToString(12345));
        Console.WriteLine(IntToString(-999));
    }
}
```

<span data-ttu-id="b95b9-428">un initialiseur `stackalloc` est utilisé dans la méthode `IntToString` pour allouer une mémoire tampon de 16 caractères sur la pile.</span><span class="sxs-lookup"><span data-stu-id="b95b9-428">a `stackalloc` initializer is used in the `IntToString` method to allocate a buffer of 16 characters on the stack.</span></span> <span data-ttu-id="b95b9-429">La mémoire tampon est automatiquement supprimée lorsque la méthode est retournée.</span><span class="sxs-lookup"><span data-stu-id="b95b9-429">The buffer is automatically discarded when the method returns.</span></span>

## <a name="dynamic-memory-allocation"></a><span data-ttu-id="b95b9-430">Allocation de mémoire dynamique</span><span class="sxs-lookup"><span data-stu-id="b95b9-430">Dynamic memory allocation</span></span>

<span data-ttu-id="b95b9-431">À l’exception de l’opérateur @no__t- C# 0, ne fournit aucune construction prédéfinie pour la gestion de la mémoire qui n’est pas récupérée par le garbage collector.</span><span class="sxs-lookup"><span data-stu-id="b95b9-431">Except for the `stackalloc` operator, C# provides no predefined constructs for managing non-garbage collected memory.</span></span> <span data-ttu-id="b95b9-432">Ces services sont généralement fournis par la prise en charge des bibliothèques de classes ou importés directement depuis le système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="b95b9-432">Such services are typically provided by supporting class libraries or imported directly from the underlying operating system.</span></span> <span data-ttu-id="b95b9-433">Par exemple, la classe `Memory` ci-dessous illustre la façon dont les fonctions de tas d’un système d’exploitation C#sous-jacent sont accessibles à partir de :</span><span class="sxs-lookup"><span data-stu-id="b95b9-433">For example, the `Memory` class below illustrates how the heap functions of an underlying operating system might be accessed from C#:</span></span>

```csharp
using System;
using System.Runtime.InteropServices;

public unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    static int ph = GetProcessHeap();

    // Private instance constructor to prevent instantiation.
    private Memory() {}

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size) {
        void* result = HeapAlloc(ph, HEAP_ZERO_MEMORY, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count) {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd) {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd) {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block) {
        if (!HeapFree(ph, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size) {
        void* result = HeapReAlloc(ph, HEAP_ZERO_MEMORY, block, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block) {
        int result = HeapSize(ph, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    static extern int GetProcessHeap();

    [DllImport("kernel32")]
    static extern void* HeapAlloc(int hHeap, int flags, int size);

    [DllImport("kernel32")]
    static extern bool HeapFree(int hHeap, int flags, void* block);

    [DllImport("kernel32")]
    static extern void* HeapReAlloc(int hHeap, int flags, void* block, int size);

    [DllImport("kernel32")]
    static extern int HeapSize(int hHeap, int flags, void* block);
}
```

<span data-ttu-id="b95b9-434">Vous trouverez ci-dessous un exemple qui utilise la classe `Memory` :</span><span class="sxs-lookup"><span data-stu-id="b95b9-434">An example that uses the `Memory` class is given below:</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            byte* buffer = (byte*)Memory.Alloc(256);
            try {
                for (int i = 0; i < 256; i++) buffer[i] = (byte)i;
                byte[] array = new byte[256];
                fixed (byte* p = array) Memory.Copy(buffer, p, 256); 
            }
            finally {
                Memory.Free(buffer);
            }
            for (int i = 0; i < 256; i++) Console.WriteLine(array[i]);
        }
    }
}
```

<span data-ttu-id="b95b9-435">L’exemple alloue 256 octets de mémoire par le biais de `Memory.Alloc` et initialise le bloc de mémoire avec des valeurs qui s’incrémentent de 0 à 255.</span><span class="sxs-lookup"><span data-stu-id="b95b9-435">The example allocates 256 bytes of memory through `Memory.Alloc` and initializes the memory block with values increasing from 0 to 255.</span></span> <span data-ttu-id="b95b9-436">Il alloue ensuite un tableau d’octets d’éléments 256 et utilise `Memory.Copy` pour copier le contenu du bloc de mémoire dans le tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="b95b9-436">It then allocates a 256 element byte array and uses `Memory.Copy` to copy the contents of the memory block into the byte array.</span></span> <span data-ttu-id="b95b9-437">Enfin, le bloc de mémoire est libéré à l’aide de `Memory.Free` et le contenu du tableau d’octets est généré sur la console.</span><span class="sxs-lookup"><span data-stu-id="b95b9-437">Finally, the memory block is freed using `Memory.Free` and the contents of the byte array are output on the console.</span></span>
