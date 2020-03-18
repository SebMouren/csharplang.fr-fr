---
ms.openlocfilehash: 2e4a3a32def6900797c151264c984378b09b4988
ms.sourcegitcommit: 5983461e05be62f39c77383cb7857539518cb04f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "79485125"
---

# <a name="discriminated-unions--enum-class"></a><span data-ttu-id="adaf6-101">Unions/`enum class` discriminées</span><span class="sxs-lookup"><span data-stu-id="adaf6-101">Discriminated unions / `enum class`</span></span>

<span data-ttu-id="adaf6-102">`enum class`es sont un nouveau genre de déclaration de type, parfois appelé unions discriminées, où chaque instance possible du type est répertoriée, et chaque instance ne se chevauche pas.</span><span class="sxs-lookup"><span data-stu-id="adaf6-102">`enum class`es are a new kind of type declaration, sometimes referred to as discriminated unions, where each every possible instance the type is listed, and each instance is non-overlapping.</span></span>

<span data-ttu-id="adaf6-103">Un `enum class` est défini à l’aide de la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="adaf6-103">An `enum class` is defined using the following syntax:</span></span>

```antlr
enum_class
    : 'partial'? 'enum class' identifier type_parameter_list? type_parameter_constraints_clause* 
      '{' enum_class_body '}'
    ;

enum_class_body
    : enum_class_cases?
    | enum_class_cases ','
    ;

enum_class_cases
    : enum_class_case
    | enum_class_case ',' enum_class_cases
    ;

enum_class_case
    : enum_class
    | class_declaration
    | identifier type_parameter_list? '(' formal_parameter_list? ')'
    | identifier
    ;

```

<span data-ttu-id="adaf6-104">Exemple de syntaxe :</span><span class="sxs-lookup"><span data-stu-id="adaf6-104">Sample syntax:</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a><span data-ttu-id="adaf6-105">Sémantique</span><span class="sxs-lookup"><span data-stu-id="adaf6-105">Semantics</span></span>

<span data-ttu-id="adaf6-106">Une définition de `enum class` définit un type racine, qui est une classe abstraite du même nom que la déclaration `enum class` et un ensemble de membres, chacun d’entre eux ayant un type qui est un sous-type du type racine.</span><span class="sxs-lookup"><span data-stu-id="adaf6-106">An `enum class` definition defines a root type, which is an abstract class of the same name as the `enum class` declaration, and a set of members, each of which has a type which is a subtype of the root type.</span></span> <span data-ttu-id="adaf6-107">S’il existe plusieurs définitions de `partial enum class`, tous les membres sont considérés comme membres de la définition de la classe Enum.</span><span class="sxs-lookup"><span data-stu-id="adaf6-107">If there are multiple `partial enum class` definitions, all members will be considered members of the enum class definition.</span></span> <span data-ttu-id="adaf6-108">Contrairement à une définition de classe abstraite définie par l’utilisateur, le type racine `enum class` est partiel par défaut et défini pour avoir un constructeur sans paramètre *privé* par défaut.</span><span class="sxs-lookup"><span data-stu-id="adaf6-108">Unlike a user-defined abstract class definition, the `enum class` root type is partial by default and defined to have a default *private* parameter-less constructor.</span></span>

<span data-ttu-id="adaf6-109">Notez que, étant donné que le type racine est défini comme une classe abstraite partielle, les définitions partielles du *type racine* peuvent également être ajoutées, où les formes de syntaxe standard pour un corps de classe sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="adaf6-109">Note that, since the root type is defined to be a partial abstract class, partial definitions of the *root type* may also be added, where standard syntax forms for a class body are allowed.</span></span>
<span data-ttu-id="adaf6-110">Toutefois, aucun type ne peut hériter directement du type racine dans une déclaration, à l’exception de ceux spécifiés en tant que membres de `enum class`.</span><span class="sxs-lookup"><span data-stu-id="adaf6-110">However, no types may directly inherit from the root type in any declaration, aside from those specified as `enum class` members.</span></span> <span data-ttu-id="adaf6-111">En outre, aucun constructeur défini par l’utilisateur n’est autorisé pour le type racine.</span><span class="sxs-lookup"><span data-stu-id="adaf6-111">In addition, no user-defined constructors are permitted for the root type.</span></span>

<span data-ttu-id="adaf6-112">Il existe quatre types de déclarations de membres de `enum class` :</span><span class="sxs-lookup"><span data-stu-id="adaf6-112">There are four kinds of `enum class` member declarations:</span></span>

* <span data-ttu-id="adaf6-113">Membres de classe simples</span><span class="sxs-lookup"><span data-stu-id="adaf6-113">simple class members</span></span>

* <span data-ttu-id="adaf6-114">membres de classe complexe</span><span class="sxs-lookup"><span data-stu-id="adaf6-114">complex class members</span></span>

* <span data-ttu-id="adaf6-115">membres `enum class`</span><span class="sxs-lookup"><span data-stu-id="adaf6-115">`enum class` members</span></span>

* <span data-ttu-id="adaf6-116">membres de valeur.</span><span class="sxs-lookup"><span data-stu-id="adaf6-116">value members.</span></span>

### <a name="simple-class-members"></a><span data-ttu-id="adaf6-117">Membres de classe simples</span><span class="sxs-lookup"><span data-stu-id="adaf6-117">Simple class members</span></span>

<span data-ttu-id="adaf6-118">Une déclaration de membre de classe simple définit une nouvelle classe « record » imbriquée (intentionnellement laissée non définie dans ce document) portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="adaf6-118">A simple class member declaration defines a new nested "record" class (intentionally left undefined in this document) with the same name.</span></span> <span data-ttu-id="adaf6-119">La classe imbriquée hérite du type racine.</span><span class="sxs-lookup"><span data-stu-id="adaf6-119">The nested class inherits from the root type.</span></span>

<span data-ttu-id="adaf6-120">Compte tenu de l’exemple de code ci-dessus,</span><span class="sxs-lookup"><span data-stu-id="adaf6-120">Given the sample code above,</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

<span data-ttu-id="adaf6-121">la déclaration de `enum class` a une sémantique équivalente à la déclaration suivante</span><span class="sxs-lookup"><span data-stu-id="adaf6-121">the `enum class` declaration has semantics equivalent to the following declaration</span></span>

```C#
partial abstract class Shape
{
    public data class Rectangle(float Width, float Length) : Shape,
    public data class Circle(float Radius) : Shape
}
```

### <a name="complex-class-members"></a><span data-ttu-id="adaf6-122">Membres de classe complexe</span><span class="sxs-lookup"><span data-stu-id="adaf6-122">Complex class members</span></span>

<span data-ttu-id="adaf6-123">Vous pouvez également imbriquer une déclaration de classe entière sous une déclaration de `enum class`.</span><span class="sxs-lookup"><span data-stu-id="adaf6-123">You can also nest an entire class declaration below an `enum class` declaration.</span></span> <span data-ttu-id="adaf6-124">Il sera traité comme une classe imbriquée du type racine.</span><span class="sxs-lookup"><span data-stu-id="adaf6-124">It will be treated as a nested class of the root type.</span></span> <span data-ttu-id="adaf6-125">La syntaxe autorise toute déclaration de classe, mais elle est requise pour que le membre de classe complexe hérite de la déclaration de `enum class` contenant directement.</span><span class="sxs-lookup"><span data-stu-id="adaf6-125">The syntax allows any class declaration, but it is required for the complex class member to inherit from the direct containing `enum class` declaration.</span></span> 

### <a name="enum-class-members"></a><span data-ttu-id="adaf6-126">membres `enum class`</span><span class="sxs-lookup"><span data-stu-id="adaf6-126">`enum class` members</span></span>

<span data-ttu-id="adaf6-127">les `enum classes` peuvent être imbriqués les uns sous les autres, par exemple</span><span class="sxs-lookup"><span data-stu-id="adaf6-127">`enum classes` can be nested under each other, e.g.</span></span>

```C#
enum class Expr
{
    enum class Binary
    {
        Addition(Expr left, Expr right),
        Multiplication(Expr left, Expr right)
    }
}
```

<span data-ttu-id="adaf6-128">Cela est presque identique à la sémantique d’un `enum class`de niveau supérieur, sauf que la classe enum imbriquée définit un type racine imbriqué, et que tout ce qui se trouve sous la classe enum imbriquée est un sous-type du type racine imbriqué, au lieu du type racine de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="adaf6-128">This is almost identical to the semantics of a top-level `enum class`, except that the nested enum class defines a nested root type, and everything below the nested enum class is a subtype of the nested root type, instead of the top-level root type.</span></span>

```C#
partial abstract class Expr
{
    partial abstract class Binary : Expr
    {
        public data class Addition(Expr left, Expr right) : Binary,
        public data class Multiplication(Expr left, Expr right) : Binary
    }
}
```

### <a name="value-members"></a><span data-ttu-id="adaf6-129">Membres de valeur</span><span class="sxs-lookup"><span data-stu-id="adaf6-129">Value members</span></span>

<span data-ttu-id="adaf6-130">`enum classes` peut également contenir des membres de valeur.</span><span class="sxs-lookup"><span data-stu-id="adaf6-130">`enum classes` can also contain value members.</span></span> <span data-ttu-id="adaf6-131">Les membres de valeur définissent des propriétés statiques de type public en réception seule sur le type racine qui retournent également le type racine, par exemple</span><span class="sxs-lookup"><span data-stu-id="adaf6-131">Value members define public get-only static properties on the root type that also return the root type, e.g.</span></span>

```C#
enum class Color
{
    Red,
    Green
}
```

<span data-ttu-id="adaf6-132">a des propriétés équivalentes à</span><span class="sxs-lookup"><span data-stu-id="adaf6-132">has properties equivalent to</span></span>

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

<span data-ttu-id="adaf6-133">La sémantique complète est considérée comme un détail d’implémentation, mais il est garanti qu’une instance unique sera retournée pour chaque propriété, et la même instance sera retournée sur les appels répétés.</span><span class="sxs-lookup"><span data-stu-id="adaf6-133">The complete semantics are considered an implementation detail, but it is guaranteed that one unique instance will be returned for each property, and the same instance will be returned on repeated invocations.</span></span>


### <a name="switch-expression-and-patterns"></a><span data-ttu-id="adaf6-134">Expressions de commutateur et modèles</span><span class="sxs-lookup"><span data-stu-id="adaf6-134">Switch expression and patterns</span></span>

<span data-ttu-id="adaf6-135">Il existe des ajustements à apporter aux critères spéciaux et l’expression de commutateur pour gérer `enum classes`.</span><span class="sxs-lookup"><span data-stu-id="adaf6-135">There are some proposed adjustments to pattern matching and the switch expression to handle `enum classes`.</span></span> <span data-ttu-id="adaf6-136">Les expressions de commutateur peuvent déjà correspondre aux types via le modèle de variable, mais pour l’instant pour les types de référence, aucun ensemble d’accoudoirs de commutateur dans l’expression de commutateur n’est considéré comme complet, sauf pour une correspondance avec le type statique de l’argument ou un sous-type.</span><span class="sxs-lookup"><span data-stu-id="adaf6-136">Switch expressions can already match types through the variable pattern, but for currently for reference types, no set of switch arms in the switch expression are considered complete, except for matching against the static type of the argument, or a subtype.</span></span>

<span data-ttu-id="adaf6-137">Les expressions de commutateur sont modifiées de telle sorte que, si le type racine d’un `enum class` est le type statique de l’argument de l’expression de commutateur, et qu’il existe un ensemble de modèles correspondant à tous les membres de l’énumération, le commutateur est considéré comme exhaustif.</span><span class="sxs-lookup"><span data-stu-id="adaf6-137">Switch expressions would be changed such that, if the root type of an `enum class` is the static type of the argument to the switch expression, and there is a set of patterns matching all members of the enum, then the switch will be considered exhaustive.</span></span>

<span data-ttu-id="adaf6-138">Étant donné que les membres de valeur ne sont pas des constantes et ne définissent pas de nouveaux types statiques, ils ne peuvent actuellement pas être mis en correspondance par le modèle.</span><span class="sxs-lookup"><span data-stu-id="adaf6-138">Since value members are not constants and do not define new static types, they currently cannot be matched by pattern.</span></span> <span data-ttu-id="adaf6-139">Pour ce faire, un nouveau modèle à l’aide de la syntaxe de modèle de constante sera ajouté pour permettre la correspondance avec les membres de la valeur `enum class`.</span><span class="sxs-lookup"><span data-stu-id="adaf6-139">To make this possible, a new pattern using the constant pattern syntax will be added to allow match against `enum class` value members.</span></span> <span data-ttu-id="adaf6-140">La correspondance est définie pour être réussie si et seulement si l’argument du modèle correspond à et que la valeur retournée par le membre de la valeur `enum class` serait égale, même si l’implémentation n’est pas requise pour effectuer cette vérification.</span><span class="sxs-lookup"><span data-stu-id="adaf6-140">The match is defined to succeed if and only if the argument to the pattern match and the value returned by the `enum class` value member would be reference equal, although the implementation is not required to perform this check.</span></span>


## <a name="open-questions"></a><span data-ttu-id="adaf6-141">Questions ouvertes</span><span class="sxs-lookup"><span data-stu-id="adaf6-141">Open questions</span></span>

- <span data-ttu-id="adaf6-142">[] Que disent l’algorithme de type commun sur `enum class` membres ?</span><span class="sxs-lookup"><span data-stu-id="adaf6-142">[ ] What does the common type algorithm say about `enum class` members?</span></span> <span data-ttu-id="adaf6-143">Ce code est-il valide ?</span><span class="sxs-lookup"><span data-stu-id="adaf6-143">Is this valid code?</span></span>
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- <span data-ttu-id="adaf6-144">[] L’ajout d’un nouveau modèle uniquement pour les membres de valeur semble lourd.</span><span class="sxs-lookup"><span data-stu-id="adaf6-144">[ ] Adding a new pattern just for value members seems heavy handed.</span></span> <span data-ttu-id="adaf6-145">Y a-t-il une construction de version plus générale qui est logique ?</span><span class="sxs-lookup"><span data-stu-id="adaf6-145">Is there a more general version construction that makes sense?</span></span>
    - <span data-ttu-id="adaf6-146">[] Les membres de valeur ne correspondent pas non plus bien à une construction de classe imbriquée parallèle</span><span class="sxs-lookup"><span data-stu-id="adaf6-146">[ ] Value members also do not map well to a parallel nested class construction because of this</span></span>

- <span data-ttu-id="adaf6-147">[] Bascule sur un argument avec un `enum class` type statique garanti comme étant à temps constant ?</span><span class="sxs-lookup"><span data-stu-id="adaf6-147">[ ] Is switching against an argument with an `enum class` static type guaranteed to be constant-time?</span></span>

- <span data-ttu-id="adaf6-148">[] Est-il possible de faire en sorte que les `enum class`ne soient pas considérés comme étant terminées dans l’expression de switch ?</span><span class="sxs-lookup"><span data-stu-id="adaf6-148">[ ] Should there be a way to make `enum class`es not be considered complete in the switch expression?</span></span> <span data-ttu-id="adaf6-149">Préfixe `virtual`?</span><span class="sxs-lookup"><span data-stu-id="adaf6-149">Prefix with `virtual`?</span></span>

- <span data-ttu-id="adaf6-150">[] Quels modificateurs doivent être autorisés sur `enum class`?</span><span class="sxs-lookup"><span data-stu-id="adaf6-150">[ ] What modifiers should be permitted on `enum class`?</span></span>