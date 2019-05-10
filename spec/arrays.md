---
ms.openlocfilehash: 155c1beecddfdfcce2e7948bcb8d6b80428fbd7a
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488842"
---
# <a name="arrays"></a><span data-ttu-id="47ff2-101">Tableaux</span><span class="sxs-lookup"><span data-stu-id="47ff2-101">Arrays</span></span>

<span data-ttu-id="47ff2-102">Un tableau est une structure de données qui contient un nombre de variables qui sont accessibles par le biais des indices calculés.</span><span class="sxs-lookup"><span data-stu-id="47ff2-102">An array is a data structure that contains a number of variables which are accessed through computed indices.</span></span> <span data-ttu-id="47ff2-103">Les variables contenues dans un tableau, également appelé éléments du tableau, sont tous du même type, et ce type est appelé le type d’élément du tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-103">The variables contained in an array, also called the elements of the array, are all of the same type, and this type is called the element type of the array.</span></span>

<span data-ttu-id="47ff2-104">Un tableau a un rang qui détermine le nombre d’indices associés à chaque élément du tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-104">An array has a rank which determines the number of indices associated with each array element.</span></span> <span data-ttu-id="47ff2-105">Le rang d’un tableau est également appelé les dimensions du tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-105">The rank of an array is also referred to as the dimensions of the array.</span></span> <span data-ttu-id="47ff2-106">Un tableau avec un classement d’un est appelé un ***tableau unidimensionnel***.</span><span class="sxs-lookup"><span data-stu-id="47ff2-106">An array with a rank of one is called a ***single-dimensional array***.</span></span> <span data-ttu-id="47ff2-107">Un tableau de rang supérieur à un est appelé un ***tableau multidimensionnel***.</span><span class="sxs-lookup"><span data-stu-id="47ff2-107">An array with a rank greater than one is called a ***multi-dimensional array***.</span></span> <span data-ttu-id="47ff2-108">Tableaux multidimensionnels de taille spécifiques sont souvent appelées tableaux à deux dimensions, tableaux à trois dimensions et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="47ff2-108">Specific sized multi-dimensional arrays are often referred to as two-dimensional arrays, three-dimensional arrays, and so on.</span></span>

<span data-ttu-id="47ff2-109">Chaque dimension d’un tableau a une longueur, qui est un nombre entier supérieur ou égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="47ff2-109">Each dimension of an array has an associated length which is an integral number greater than or equal to zero.</span></span> <span data-ttu-id="47ff2-110">Les longueurs de dimensions ne font pas partie du type du tableau, mais elles sont établies lorsqu’une instance du type tableau est créée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="47ff2-110">The dimension lengths are not part of the type of the array, but rather are established when an instance of the array type is created at run-time.</span></span> <span data-ttu-id="47ff2-111">La longueur d’une dimension détermine la plage valide d’indices de cette dimension : Pour une dimension de longueur `N`, index peuvent être comprise entre `0` à `N - 1` inclus.</span><span class="sxs-lookup"><span data-stu-id="47ff2-111">The length of a dimension determines the valid range of indices for that dimension: For a dimension of length `N`, indices can range from `0` to `N - 1` inclusive.</span></span> <span data-ttu-id="47ff2-112">Le nombre total d’éléments dans un tableau est le produit des longueurs de chaque dimension du tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-112">The total number of elements in an array is the product of the lengths of each dimension in the array.</span></span> <span data-ttu-id="47ff2-113">Si un ou plusieurs des dimensions du tableau ont une longueur de zéro, le tableau est dit être vide.</span><span class="sxs-lookup"><span data-stu-id="47ff2-113">If one or more of the dimensions of an array have a length of zero, the array is said to be empty.</span></span>

<span data-ttu-id="47ff2-114">Le type d’élément d’un tableau peut être de n’importe quel type, y compris un type tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-114">The element type of an array can be any type, including an array type.</span></span>

## <a name="array-types"></a><span data-ttu-id="47ff2-115">Types de tableaux</span><span class="sxs-lookup"><span data-stu-id="47ff2-115">Array types</span></span>

<span data-ttu-id="47ff2-116">Un type tableau est écrit comme un *non_array_type* suivie d’un ou plusieurs *rank_specifier*%s :</span><span class="sxs-lookup"><span data-stu-id="47ff2-116">An array type is written as a *non_array_type* followed by one or more *rank_specifier*s:</span></span>

```antlr
array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;
```

<span data-ttu-id="47ff2-117">Un *non_array_type* est any *type* qui est pas elle-même une *array_type*.</span><span class="sxs-lookup"><span data-stu-id="47ff2-117">A *non_array_type* is any *type* that is not itself an *array_type*.</span></span>

<span data-ttu-id="47ff2-118">Le rang d’un type tableau est fourni par le plus à gauche *rank_specifier* dans le *array_type*: Un *rank_specifier* indique que le tableau est un tableau de rang un plus le nombre de «`,`» des jetons dans le *rank_specifier*.</span><span class="sxs-lookup"><span data-stu-id="47ff2-118">The rank of an array type is given by the leftmost *rank_specifier* in the *array_type*: A *rank_specifier* indicates that the array is an array with a rank of one plus the number of "`,`" tokens in the *rank_specifier*.</span></span>

<span data-ttu-id="47ff2-119">Le type d’élément d’un type tableau est le type qui résulte de la suppression le plus à gauche *rank_specifier*:</span><span class="sxs-lookup"><span data-stu-id="47ff2-119">The element type of an array type is the type that results from deleting the leftmost *rank_specifier*:</span></span>

*  <span data-ttu-id="47ff2-120">Un type de tableau sous la forme `T[R]` est un tableau avec le rang `R` et un type d’élément de tableau non `T`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-120">An array type of the form `T[R]` is an array with rank `R` and a non-array element type `T`.</span></span>
*  <span data-ttu-id="47ff2-121">Un type de tableau sous la forme `T[R][R1]...[Rn]` est un tableau avec le rang `R` et un type d’élément `T[R1]...[Rn]`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-121">An array type of the form `T[R][R1]...[Rn]` is an array with rank `R` and an element type `T[R1]...[Rn]`.</span></span>

<span data-ttu-id="47ff2-122">En effet, le *rank_specifier*s sont lues de gauche à droite avant le type d’élément non-tableau final.</span><span class="sxs-lookup"><span data-stu-id="47ff2-122">In effect, the *rank_specifier*s are read from left to right before the final non-array element type.</span></span> <span data-ttu-id="47ff2-123">Le type `int[][,,][,]` est un tableau unidimensionnel de tableaux à trois dimensions de tableaux à deux dimensions de `int`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-123">The type `int[][,,][,]` is a single-dimensional array of three-dimensional arrays of two-dimensional arrays of `int`.</span></span>

<span data-ttu-id="47ff2-124">Au moment de l’exécution, une valeur d’un type tableau peut être `null` ou une référence à une instance de ce type de tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-124">At run-time, a value of an array type can be `null` or a reference to an instance of that array type.</span></span>

### <a name="the-systemarray-type"></a><span data-ttu-id="47ff2-125">Le type System.Array</span><span class="sxs-lookup"><span data-stu-id="47ff2-125">The System.Array type</span></span>

<span data-ttu-id="47ff2-126">Le type `System.Array` est le type de base abstrait de tous les types tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-126">The type `System.Array` is the abstract base type of all array types.</span></span> <span data-ttu-id="47ff2-127">Une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) existe à partir de n’importe quel type de tableau à `System.Array`et une conversion de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir de `System.Array` à n’importe quel type de tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-127">An implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from any array type to `System.Array`, and an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from `System.Array` to any array type.</span></span> <span data-ttu-id="47ff2-128">Notez que `System.Array` n’est pas lui-même un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="47ff2-128">Note that `System.Array` is not itself an *array_type*.</span></span> <span data-ttu-id="47ff2-129">Au lieu de cela, il est un *class_type* à partir de laquelle toutes les *array_type*s sont dérivés.</span><span class="sxs-lookup"><span data-stu-id="47ff2-129">Rather, it is a *class_type* from which all *array_type*s are derived.</span></span>

<span data-ttu-id="47ff2-130">Au moment de l’exécution, une valeur de type `System.Array` peut être `null` ou une référence à une instance de n’importe quel type de tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-130">At run-time, a value of type `System.Array` can be `null` or a reference to an instance of any array type.</span></span>

### <a name="arrays-and-the-generic-ilist-interface"></a><span data-ttu-id="47ff2-131">Tableaux et l’interface IList générique</span><span class="sxs-lookup"><span data-stu-id="47ff2-131">Arrays and the generic IList interface</span></span>

<span data-ttu-id="47ff2-132">Un tableau unidimensionnel `T[]` implémente l’interface `System.Collections.Generic.IList<T>` (`IList<T>` en abrégé) et ses interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="47ff2-132">A one-dimensional array `T[]` implements the interface `System.Collections.Generic.IList<T>` (`IList<T>` for short) and its base interfaces.</span></span> <span data-ttu-id="47ff2-133">En conséquence, il existe une conversion implicite de `T[]` à `IList<T>` et ses interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="47ff2-133">Accordingly, there is an implicit conversion from `T[]` to `IList<T>` and its base interfaces.</span></span> <span data-ttu-id="47ff2-134">En outre, s’il existe une conversion de référence implicite à partir de `S` à `T` puis `S[]` implémente `IList<T>` et il existe une conversion de référence implicite à partir de `S[]` à `IList<T>` et sa base d’interfaces () [Conversions de référence implicite](conversions.md#implicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-134">In addition, if there is an implicit reference conversion from `S` to `T` then `S[]` implements `IList<T>` and there is an implicit reference conversion from `S[]` to `IList<T>` and its base interfaces ([Implicit reference conversions](conversions.md#implicit-reference-conversions)).</span></span> <span data-ttu-id="47ff2-135">S’il existe une conversion de référence explicite à partir de `S` à `T` , il y a une conversion de référence explicite à partir de `S[]` à `IList<T>` et ses interfaces de base ([conversions de référence explicite](conversions.md#explicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-135">If there is an explicit reference conversion from `S` to `T` then there is an explicit reference conversion from `S[]` to `IList<T>` and its base interfaces ([Explicit reference conversions](conversions.md#explicit-reference-conversions)).</span></span> <span data-ttu-id="47ff2-136">Exemple :</span><span class="sxs-lookup"><span data-stu-id="47ff2-136">For example:</span></span>
```csharp
using System.Collections.Generic;

class Test
{
    static void Main() {
        string[] sa = new string[5];
        object[] oa1 = new object[5];
        object[] oa2 = sa;

        IList<string> lst1 = sa;                    // Ok
        IList<string> lst2 = oa1;                   // Error, cast needed
        IList<object> lst3 = sa;                    // Ok
        IList<object> lst4 = oa1;                   // Ok

        IList<string> lst5 = (IList<string>)oa1;    // Exception
        IList<string> lst6 = (IList<string>)oa2;    // Ok
    }
}
```

<span data-ttu-id="47ff2-137">L’attribution de `lst2 = oa1` génère une erreur de compilation depuis la conversion de `object[]` à `IList<string>` est une conversion explicite, implicite pas.</span><span class="sxs-lookup"><span data-stu-id="47ff2-137">The assignment `lst2 = oa1` generates a compile-time error since the conversion from `object[]` to `IList<string>` is an explicit conversion, not implicit.</span></span> <span data-ttu-id="47ff2-138">Le cast `(IList<string>)oa1` provoquera une exception levée au moment de l’exécution depuis `oa1` références un `object[]` et non un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-138">The cast `(IList<string>)oa1` will cause an exception to be thrown at run-time since `oa1` references an `object[]` and not a `string[]`.</span></span> <span data-ttu-id="47ff2-139">Toutefois le cast `(IList<string>)oa2` n’entraîne pas une exception levée depuis `oa2` références un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-139">However the cast `(IList<string>)oa2` will not cause an exception to be thrown since `oa2` references a `string[]`.</span></span>

<span data-ttu-id="47ff2-140">Chaque fois qu’il existe une conversion de référence implicite ou explicite de `S[]` à `IList<T>`, il existe également une conversion de référence explicite à partir de `IList<T>` et les interfaces à sa base `S[]` ([référence explicite conversions](conversions.md#explicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-140">Whenever there is an implicit or explicit reference conversion from `S[]` to `IList<T>`, there is also an explicit reference conversion from `IList<T>` and its base interfaces to `S[]` ([Explicit reference conversions](conversions.md#explicit-reference-conversions)).</span></span>

<span data-ttu-id="47ff2-141">Lorsqu’un type tableau `S[]` implémente `IList<T>`, certains membres de l’interface implémentée peuvent lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="47ff2-141">When an array type `S[]` implements `IList<T>`, some of the members of the implemented interface may throw exceptions.</span></span> <span data-ttu-id="47ff2-142">Le comportement précis de l’implémentation de l’interface n’entre pas dans le cadre de cette spécification.</span><span class="sxs-lookup"><span data-stu-id="47ff2-142">The precise behavior of the implementation of the interface is beyond the scope of this specification.</span></span>

## <a name="array-creation"></a><span data-ttu-id="47ff2-143">Création de tableau</span><span class="sxs-lookup"><span data-stu-id="47ff2-143">Array creation</span></span>

<span data-ttu-id="47ff2-144">Les instances de tableau sont créées par *array_creation_expression*s ([expressions de création de tableau](expressions.md#array-creation-expressions)) ou par champ ou des déclarations de variables locales qui incluent un *array_initializer*([Les initialiseurs de tableaux](arrays.md#array-initializers)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-144">Array instances are created by *array_creation_expression*s ([Array creation expressions](expressions.md#array-creation-expressions)) or by field or local variable declarations that include an *array_initializer* ([Array initializers](arrays.md#array-initializers)).</span></span>

<span data-ttu-id="47ff2-145">Lorsqu’une instance de tableau est créée, le rang et la longueur de chaque dimension sont établis, puis il restera constante pour toute la durée de vie de l’instance.</span><span class="sxs-lookup"><span data-stu-id="47ff2-145">When an array instance is created, the rank and length of each dimension are established and then remain constant for the entire lifetime of the instance.</span></span> <span data-ttu-id="47ff2-146">En d’autres termes, il n’est pas possible de modifier le rang d’une instance de tableau existante, ni est-il possible de redimensionner ses dimensions.</span><span class="sxs-lookup"><span data-stu-id="47ff2-146">In other words, it is not possible to change the rank of an existing array instance, nor is it possible to resize its dimensions.</span></span>

<span data-ttu-id="47ff2-147">Une instance de tableau est toujours d’un type tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-147">An array instance is always of an array type.</span></span> <span data-ttu-id="47ff2-148">Le `System.Array` type est un type abstrait qui ne peut pas être instancié.</span><span class="sxs-lookup"><span data-stu-id="47ff2-148">The `System.Array` type is an abstract type that cannot be instantiated.</span></span>

<span data-ttu-id="47ff2-149">Éléments des tableaux créés par *array_creation_expression*s sont toujours initialisés à leur valeur par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-149">Elements of arrays created by *array_creation_expression*s are always initialized to their default value ([Default values](variables.md#default-values)).</span></span>

## <a name="array-element-access"></a><span data-ttu-id="47ff2-150">Accès aux éléments de tableau</span><span class="sxs-lookup"><span data-stu-id="47ff2-150">Array element access</span></span>

<span data-ttu-id="47ff2-151">Éléments de tableau sont accessibles à l’aide de *element_access* expressions ([tableau accès](expressions.md#array-access)) sous la forme `A[I1, I2, ..., In]`, où `A` est une expression de type tableau et chaque `Ix` est un expression de type `int`, `uint`, `long`, `ulong`, ou peuvent être converties implicitement à un ou plusieurs de ces types.</span><span class="sxs-lookup"><span data-stu-id="47ff2-151">Array elements are accessed using *element_access* expressions ([Array access](expressions.md#array-access)) of the form `A[I1, I2, ..., In]`, where `A` is an expression of an array type and each `Ix` is an expression of type `int`, `uint`, `long`, `ulong`, or can be implicitly converted to one or more of these types.</span></span> <span data-ttu-id="47ff2-152">Le résultat d’un accès aux éléments de tableau est une variable, à savoir l’élément de tableau sélectionné par l’index.</span><span class="sxs-lookup"><span data-stu-id="47ff2-152">The result of an array element access is a variable, namely the array element selected by the indices.</span></span>

<span data-ttu-id="47ff2-153">Les éléments d’un tableau peuvent être énumérés à l’aide un `foreach` instruction ([l’instruction foreach](statements.md#the-foreach-statement)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-153">The elements of an array can be enumerated using a `foreach` statement ([The foreach statement](statements.md#the-foreach-statement)).</span></span>

## <a name="array-members"></a><span data-ttu-id="47ff2-154">Membres de tableau</span><span class="sxs-lookup"><span data-stu-id="47ff2-154">Array members</span></span>

<span data-ttu-id="47ff2-155">Le type de chaque tableau hérite des membres déclarés par le `System.Array` type.</span><span class="sxs-lookup"><span data-stu-id="47ff2-155">Every array type inherits the members declared by the `System.Array` type.</span></span>

## <a name="array-covariance"></a><span data-ttu-id="47ff2-156">Covariance de tableau</span><span class="sxs-lookup"><span data-stu-id="47ff2-156">Array covariance</span></span>

<span data-ttu-id="47ff2-157">Pour les deux *reference_type*s `A` et `B`, si une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) ou conversion de référence explicite ([ Conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir de `A` à `B`, puis la même conversion de référence existe également du type de tableau `A[R]` pour le type de tableau `B[R]`, où `R` est any donné *rank_specifier* (mais le même pour les deux types de tableaux).</span><span class="sxs-lookup"><span data-stu-id="47ff2-157">For any two *reference_type*s `A` and `B`, if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) or explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from `A` to `B`, then the same reference conversion also exists from the array type `A[R]` to the array type `B[R]`, where `R` is any given *rank_specifier* (but the same for both array types).</span></span> <span data-ttu-id="47ff2-158">Cette relation est appelée ***covariance de tableau***.</span><span class="sxs-lookup"><span data-stu-id="47ff2-158">This relationship is known as ***array covariance***.</span></span> <span data-ttu-id="47ff2-159">Covariance de tableau signifie en particulier qu’une valeur d’un type tableau `A[R]` peut être une référence à une instance d’un type tableau `B[R]`, à condition qu’existe une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-159">Array covariance in particular means that a value of an array type `A[R]` may actually be a reference to an instance of an array type `B[R]`, provided an implicit reference conversion exists from `B` to `A`.</span></span>

<span data-ttu-id="47ff2-160">En raison de la covariance de tableau, les assignations aux éléments de tableaux de type référence incluent une vérification de l’exécution qui permet de s’assurer que la valeur assignée à l’élément de tableau est réellement de type autorisé ([assignation Simple](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="47ff2-160">Because of array covariance, assignments to elements of reference type arrays include a run-time check which ensures that the value being assigned to the array element is actually of a permitted type ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="47ff2-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="47ff2-161">For example:</span></span>
```csharp
class Test
{
    static void Fill(object[] array, int index, int count, object value) {
        for (int i = index; i < index + count; i++) array[i] = value;
    }

    static void Main() {
        string[] strings = new string[100];
        Fill(strings, 0, 100, "Undefined");
        Fill(strings, 0, 10, null);
        Fill(strings, 90, 10, 0);
    }
}
```

<span data-ttu-id="47ff2-162">L’assignation à `array[i]` dans le `Fill` méthode inclut implicitement une vérification de l’exécution qui garantit que l’objet référencé par `value` est soit `null` ou une instance qui est compatible avec le type d’élément réel de `array`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-162">The assignment to `array[i]` in the `Fill` method implicitly includes a run-time check which ensures that the object referenced by `value` is either `null` or an instance that is compatible with the actual element type of `array`.</span></span> <span data-ttu-id="47ff2-163">Dans `Main`, les deux premiers appels de `Fill` réussissent, mais les causes d’appel troisième un `System.ArrayTypeMismatchException` levée lors de l’exécution de la première assignation à `array[i]`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-163">In `Main`, the first two invocations of `Fill` succeed, but the third invocation causes a `System.ArrayTypeMismatchException` to be thrown upon executing the first assignment to `array[i]`.</span></span> <span data-ttu-id="47ff2-164">L’exception se produit, car un boxed `int` ne peut pas être stocké dans un `string` tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-164">The exception occurs because a boxed `int` cannot be stored in a `string` array.</span></span>

<span data-ttu-id="47ff2-165">Covariance de tableau en particulier ne s’étend pas aux tableaux de *value_type*s.</span><span class="sxs-lookup"><span data-stu-id="47ff2-165">Array covariance specifically does not extend to arrays of *value_type*s.</span></span> <span data-ttu-id="47ff2-166">Par exemple, aucune conversion n’existe qui permet à un `int[]` doit être traité comme un `object[]`.</span><span class="sxs-lookup"><span data-stu-id="47ff2-166">For example, no conversion exists that permits an `int[]` to be treated as an `object[]`.</span></span>

## <a name="array-initializers"></a><span data-ttu-id="47ff2-167">Initialiseurs de tableau</span><span class="sxs-lookup"><span data-stu-id="47ff2-167">Array initializers</span></span>

<span data-ttu-id="47ff2-168">Initialiseurs de tableau ne peuvent être spécifiées dans les déclarations de champ ([champs](classes.md#fields)), déclarations de variables locales ([déclarations de variables locales](statements.md#local-variable-declarations)) et les expressions de création de tableau ([création de tableau expressions](expressions.md#array-creation-expressions)) :</span><span class="sxs-lookup"><span data-stu-id="47ff2-168">Array initializers may be specified in field declarations ([Fields](classes.md#fields)), local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), and array creation expressions ([Array creation expressions](expressions.md#array-creation-expressions)):</span></span>

```antlr
array_initializer
    : '{' variable_initializer_list? '}'
    | '{' variable_initializer_list ',' '}'
    ;

variable_initializer_list
    : variable_initializer (',' variable_initializer)*
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

<span data-ttu-id="47ff2-169">Un initialiseur de tableau se compose d’une séquence d’initialiseurs de variable, délimitée par «`{`« et »`}`« jetons et séparés par »`,`« jetons.</span><span class="sxs-lookup"><span data-stu-id="47ff2-169">An array initializer consists of a sequence of variable initializers, enclosed by "`{`" and "`}`" tokens and separated by "`,`" tokens.</span></span> <span data-ttu-id="47ff2-170">Chaque initialiseur de variable est une expression ou, dans le cas d’un tableau multidimensionnel, un initialiseur de tableau imbriqué.</span><span class="sxs-lookup"><span data-stu-id="47ff2-170">Each variable initializer is an expression or, in the case of a multi-dimensional array, a nested array initializer.</span></span>

<span data-ttu-id="47ff2-171">Le contexte dans lequel un initialiseur de tableau est utilisé détermine le type du tableau en cours d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="47ff2-171">The context in which an array initializer is used determines the type of the array being initialized.</span></span> <span data-ttu-id="47ff2-172">Dans une expression de création de tableau, le type de tableau immédiatement précède l’initialiseur ou est déduit à partir d’expressions dans l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-172">In an array creation expression, the array type immediately precedes the initializer, or is inferred from the expressions in the array initializer.</span></span> <span data-ttu-id="47ff2-173">Dans un champ ou une déclaration de variable, le type de tableau est le type du champ ou de variable déclarée.</span><span class="sxs-lookup"><span data-stu-id="47ff2-173">In a field or variable declaration, the array type is the type of the field or variable being declared.</span></span> <span data-ttu-id="47ff2-174">Quand un initialiseur de tableau est utilisé dans un champ ou une déclaration de variable, tel que :</span><span class="sxs-lookup"><span data-stu-id="47ff2-174">When an array initializer is used in a field or variable declaration, such as:</span></span>
```csharp
int[] a = {0, 2, 4, 6, 8};
```
<span data-ttu-id="47ff2-175">Il est simplement d’un raccourci pour une expression de création de tableau équivalente :</span><span class="sxs-lookup"><span data-stu-id="47ff2-175">it is simply shorthand for an equivalent array creation expression:</span></span>
```csharp
int[] a = new int[] {0, 2, 4, 6, 8};
```

<span data-ttu-id="47ff2-176">Pour un tableau unidimensionnel, l’initialiseur de tableau doit se composer d’une séquence d’expressions qui sont compatibles avec le type d’élément du tableau de l’assignation.</span><span class="sxs-lookup"><span data-stu-id="47ff2-176">For a single-dimensional array, the array initializer must consist of a sequence of expressions that are assignment compatible with the element type of the array.</span></span> <span data-ttu-id="47ff2-177">Les expressions initialisent les éléments du tableau dans l’ordre croissant, en commençant par l’élément à l’index zéro.</span><span class="sxs-lookup"><span data-stu-id="47ff2-177">The expressions initialize array elements in increasing order, starting with the element at index zero.</span></span> <span data-ttu-id="47ff2-178">Le nombre d’expressions dans l’initialiseur de tableau détermine la longueur de l’instance de tableau en cours de création.</span><span class="sxs-lookup"><span data-stu-id="47ff2-178">The number of expressions in the array initializer determines the length of the array instance being created.</span></span> <span data-ttu-id="47ff2-179">Par exemple, l’initialiseur de tableau ci-dessus crée un `int[]` instance de longueur 5, puis initialise l’instance avec les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="47ff2-179">For example, the array initializer above creates an `int[]` instance of length 5 and then initializes the instance with the following values:</span></span>
```csharp
a[0] = 0; a[1] = 2; a[2] = 4; a[3] = 6; a[4] = 8;
```

<span data-ttu-id="47ff2-180">Pour un tableau multidimensionnel, l’initialiseur de tableau doit avoir autant de niveaux d’imbrication qu’il existe des dimensions dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-180">For a multi-dimensional array, the array initializer must have as many levels of nesting as there are dimensions in the array.</span></span> <span data-ttu-id="47ff2-181">Le niveau d’imbrication le plus externe correspond à la dimension la plus à gauche et le niveau d’imbrication plus profond correspond à la dimension la plus à droite.</span><span class="sxs-lookup"><span data-stu-id="47ff2-181">The outermost nesting level corresponds to the leftmost dimension and the innermost nesting level corresponds to the rightmost dimension.</span></span> <span data-ttu-id="47ff2-182">La longueur de chaque dimension du tableau est déterminée par le nombre d’éléments au niveau d’imbrication correspondant dans l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-182">The length of each dimension of the array is determined by the number of elements at the corresponding nesting level in the array initializer.</span></span> <span data-ttu-id="47ff2-183">Pour chaque initialiseur de tableau imbriqué, le nombre d’éléments doit être le même que les initialiseurs de tableau au même niveau.</span><span class="sxs-lookup"><span data-stu-id="47ff2-183">For each nested array initializer, the number of elements must be the same as the other array initializers at the same level.</span></span> <span data-ttu-id="47ff2-184">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="47ff2-184">The example:</span></span>
```csharp
int[,] b = {{0, 1}, {2, 3}, {4, 5}, {6, 7}, {8, 9}};
```
<span data-ttu-id="47ff2-185">Crée un tableau à deux dimensions avec une longueur de cinq pour la dimension la plus à gauche et une longueur de deux pour la dimension la plus à droite :</span><span class="sxs-lookup"><span data-stu-id="47ff2-185">creates a two-dimensional array with a length of five for the leftmost dimension and a length of two for the rightmost dimension:</span></span>
```csharp
int[,] b = new int[5, 2];
```
<span data-ttu-id="47ff2-186">et puis initialise l’instance de tableau avec les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="47ff2-186">and then initializes the array instance with the following values:</span></span>
```csharp
b[0, 0] = 0; b[0, 1] = 1;
b[1, 0] = 2; b[1, 1] = 3;
b[2, 0] = 4; b[2, 1] = 5;
b[3, 0] = 6; b[3, 1] = 7;
b[4, 0] = 8; b[4, 1] = 9;
```

<span data-ttu-id="47ff2-187">Si une dimension autre que le plus à droite est fournie avec une longueur égale à zéro, les dimensions suivantes sont supposées pour être également de longueur zéro.</span><span class="sxs-lookup"><span data-stu-id="47ff2-187">If a dimension other than the rightmost is given with length zero, the subsequent dimensions are assumed to also have length zero.</span></span> <span data-ttu-id="47ff2-188">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="47ff2-188">The example:</span></span>
```csharp
int[,] c = {};
```
<span data-ttu-id="47ff2-189">Crée un tableau à deux dimensions avec une longueur de zéro pour le plus à gauche et la dimension la plus à droite :</span><span class="sxs-lookup"><span data-stu-id="47ff2-189">creates a two-dimensional array with a length of zero for both the leftmost and the rightmost dimension:</span></span>
```csharp
int[,] c = new int[0, 0];
```

<span data-ttu-id="47ff2-190">Lorsqu’une expression de création de tableau comprend des longueurs de dimensions explicites et un initialiseur de tableau, les longueurs doivent être des expressions constantes et le nombre d’éléments à chaque niveau d’imbrication doit correspondre à la longueur de la dimension correspondante.</span><span class="sxs-lookup"><span data-stu-id="47ff2-190">When an array creation expression includes both explicit dimension lengths and an array initializer, the lengths must be constant expressions and the number of elements at each nesting level must match the corresponding dimension length.</span></span> <span data-ttu-id="47ff2-191">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="47ff2-191">Here are some examples:</span></span>
```csharp
int i = 3;
int[] x = new int[3] {0, 1, 2};        // OK
int[] y = new int[i] {0, 1, 2};        // Error, i not a constant
int[] z = new int[3] {0, 1, 2, 3};     // Error, length/initializer mismatch
```

<span data-ttu-id="47ff2-192">Ici, l’initialiseur pour `y` entraîne une erreur de compilation, car l’expression de longueur de dimension n’est pas une constante et l’initialiseur pour `z` génère une erreur de compilation, car la longueur et le nombre d’éléments dans le initialiseur ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="47ff2-192">Here, the initializer for `y` results in a compile-time error because the dimension length expression is not a constant, and the initializer for `z` results in a compile-time error because the length and the number of elements in the initializer do not agree.</span></span>
