---
ms.openlocfilehash: e0def754174ab8646f9b849abe86d2c375c958b6
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703984"
---
# <a name="classes"></a><span data-ttu-id="0804f-101">Classes</span><span class="sxs-lookup"><span data-stu-id="0804f-101">Classes</span></span>

<span data-ttu-id="0804f-102">Une classe est une structure de données qui peut contenir des données membres (constantes et champs), des fonctions membres (méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et des types imbriqués.</span><span class="sxs-lookup"><span data-stu-id="0804f-102">A class is a data structure that may contain data members (constants and fields), function members (methods, properties, events, indexers, operators, instance constructors, destructors and static constructors), and nested types.</span></span> <span data-ttu-id="0804f-103">Les types de classe prennent en charge l’héritage, mécanisme par lequel une classe dérivée peut étendre et spécialiser une classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-103">Class types support inheritance, a mechanism whereby a derived class can extend and specialize a base class.</span></span>

## <a name="class-declarations"></a><span data-ttu-id="0804f-104">Déclarations de classe</span><span class="sxs-lookup"><span data-stu-id="0804f-104">Class declarations</span></span>

<span data-ttu-id="0804f-105">Un *class_declaration* est un *type_declaration* ([déclarations de type](namespaces.md#type-declarations)) qui déclare une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-105">A *class_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new class.</span></span>

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

<span data-ttu-id="0804f-106">Un *class_declaration* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), suivi d’un ensemble facultatif de *class_modifier*s ([modificateurs de classe](classes.md#class-modifiers)), suivi d’un modificateur facultatif `partial`, suivi du mot clé. `class` et un *identificateur* qui nomme la classe, suivis d’un *type_parameter_list* facultatif ([paramètres de type](classes.md#type-parameters)), suivi d’une spécification *Class_base* facultative (spécification de[base de classe](classes.md#class-base-specification)), suivie de un ensemble facultatif de *type_parameter_constraints_clause*s ([contraintes de paramètre de type](classes.md#type-parameter-constraints)), suivi d’un *class_body* ([corps de classe](classes.md#class-body)), éventuellement suivi d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-106">A *class_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *class_modifier*s ([Class modifiers](classes.md#class-modifiers)), followed by an optional `partial` modifier, followed by the keyword `class` and an *identifier* that names the class, followed by an optional *type_parameter_list* ([Type parameters](classes.md#type-parameters)), followed by an optional *class_base* specification ([Class base specification](classes.md#class-base-specification)) , followed by an optional set of *type_parameter_constraints_clause*s ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *class_body* ([Class body](classes.md#class-body)), optionally followed by a semicolon.</span></span>

<span data-ttu-id="0804f-107">Une déclaration de classe ne peut pas fournir *type_parameter_constraints_clause*, sauf si elle fournit également un *type_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="0804f-107">A class declaration cannot supply *type_parameter_constraints_clause*s unless it also supplies a *type_parameter_list*.</span></span>

<span data-ttu-id="0804f-108">Une déclaration de classe qui fournit un *type_parameter_list* est une ***déclaration de classe générique***.</span><span class="sxs-lookup"><span data-stu-id="0804f-108">A class declaration that supplies a *type_parameter_list* is a ***generic class declaration***.</span></span> <span data-ttu-id="0804f-109">En outre, toute classe imbriquée dans une déclaration de classe générique ou une déclaration de struct générique est elle-même une déclaration de classe générique, car les paramètres de type pour le type conteneur doivent être fournis pour créer un type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-109">Additionally, any class nested inside a generic class declaration or a generic struct declaration is itself a generic class declaration, since type parameters for the containing type must be supplied to create a constructed type.</span></span>

### <a name="class-modifiers"></a><span data-ttu-id="0804f-110">Modificateurs de classe</span><span class="sxs-lookup"><span data-stu-id="0804f-110">Class modifiers</span></span>

<span data-ttu-id="0804f-111">Un *class_declaration* peut éventuellement inclure une séquence de modificateurs de classe :</span><span class="sxs-lookup"><span data-stu-id="0804f-111">A *class_declaration* may optionally include a sequence of class modifiers:</span></span>

```antlr
class_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'abstract'
    | 'sealed'
    | 'static'
    | class_modifier_unsafe
    ;
```

<span data-ttu-id="0804f-112">Il s’agit d’une erreur de compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-112">It is a compile-time error for the same modifier to appear multiple times in a class declaration.</span></span>

<span data-ttu-id="0804f-113">Le `new` modificateur est autorisé sur les classes imbriquées.</span><span class="sxs-lookup"><span data-stu-id="0804f-113">The `new` modifier is permitted on nested classes.</span></span> <span data-ttu-id="0804f-114">Il spécifie que la classe masque un membre hérité du même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="0804f-114">It specifies that the class hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span> <span data-ttu-id="0804f-115">Il s’agit d’une erreur de compilation pour `new` que le modificateur apparaisse sur une déclaration de classe qui n’est pas une déclaration de classe imbriquée.</span><span class="sxs-lookup"><span data-stu-id="0804f-115">It is a compile-time error for the `new` modifier to appear on a class declaration that is not a nested class declaration.</span></span>

<span data-ttu-id="0804f-116">Les `public`modificateurs `internal`, `protected`, et`private` contrôlent l’accessibilité de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-116">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the class.</span></span> <span data-ttu-id="0804f-117">Selon le contexte dans lequel la déclaration de classe se produit, certains de ces modificateurs peuvent ne pas être autorisés ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="0804f-117">Depending on the context in which the class declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="0804f-118">Les `abstract`modificateurs `static` , `sealed` et sont décrits dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="0804f-118">The `abstract`, `sealed` and `static` modifiers are discussed in the following sections.</span></span>

#### <a name="abstract-classes"></a><span data-ttu-id="0804f-119">Classes abstraites</span><span class="sxs-lookup"><span data-stu-id="0804f-119">Abstract classes</span></span>

<span data-ttu-id="0804f-120">Le `abstract` modificateur est utilisé pour indiquer qu’une classe est incomplète et qu’elle est destinée à être utilisée uniquement comme classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-120">The `abstract` modifier is used to indicate that a class is incomplete and that it is intended to be used only as a base class.</span></span> <span data-ttu-id="0804f-121">Une classe abstraite diffère d’une classe non abstraite des façons suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-121">An abstract class differs from a non-abstract class in the following ways:</span></span>

*  <span data-ttu-id="0804f-122">Une classe abstraite ne peut pas être instanciée directement, et il s’agit d’une erreur au `new` moment de la compilation pour utiliser l’opérateur sur une classe abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-122">An abstract class cannot be instantiated directly, and it is a compile-time error to use the `new` operator on an abstract class.</span></span> <span data-ttu-id="0804f-123">Bien qu’il soit possible d’avoir des variables et des valeurs dont les types au moment de la compilation sont abstraits, ces `null` variables et valeurs doivent être ou contenir des références à des instances de classes non abstraites dérivées des types abstraits.</span><span class="sxs-lookup"><span data-stu-id="0804f-123">While it is possible to have variables and values whose compile-time types are abstract, such variables and values will necessarily either be `null` or contain references to instances of non-abstract classes derived from the abstract types.</span></span>
*  <span data-ttu-id="0804f-124">Une classe abstraite est autorisée (mais pas obligatoire) à contenir des membres abstraits.</span><span class="sxs-lookup"><span data-stu-id="0804f-124">An abstract class is permitted (but not required) to contain abstract members.</span></span>
*  <span data-ttu-id="0804f-125">Une classe abstraite ne peut pas être sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-125">An abstract class cannot be sealed.</span></span>

<span data-ttu-id="0804f-126">Lorsqu’une classe non abstraite est dérivée d’une classe abstraite, la classe non abstraite doit inclure des implémentations réelles de tous les membres abstraits hérités, remplaçant ainsi ces membres abstraits.</span><span class="sxs-lookup"><span data-stu-id="0804f-126">When a non-abstract class is derived from an abstract class, the non-abstract class must include actual implementations of all inherited abstract members, thereby overriding those abstract members.</span></span> <span data-ttu-id="0804f-127">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-127">In the example</span></span>
```csharp
abstract class A
{
    public abstract void F();
}

abstract class B: A
{
    public void G() {}
}

class C: B
{
    public override void F() {
        // actual implementation of F
    }
}
```
<span data-ttu-id="0804f-128">la classe `A` abstraite introduit une méthode `F`abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-128">the abstract class `A` introduces an abstract method `F`.</span></span> <span data-ttu-id="0804f-129">La `B` classe introduit une méthode `G`supplémentaire, mais puisqu’elle ne fournit pas d' `F`implémentation `B` de, doit également être déclarée comme abstract.</span><span class="sxs-lookup"><span data-stu-id="0804f-129">Class `B` introduces an additional method `G`, but since it doesn't provide an implementation of `F`, `B` must also be declared abstract.</span></span> <span data-ttu-id="0804f-130">La `C` classe`F` se substitue à et fournit une implémentation réelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-130">Class `C` overrides `F` and provides an actual implementation.</span></span> <span data-ttu-id="0804f-131">Étant donné qu’il n’y a `C`aucun `C` membre abstrait dans, est autorisé (mais pas obligatoire) à être non abstrait.</span><span class="sxs-lookup"><span data-stu-id="0804f-131">Since there are no abstract members in `C`, `C` is permitted (but not required) to be non-abstract.</span></span>

#### <a name="sealed-classes"></a><span data-ttu-id="0804f-132">Classes sealed</span><span class="sxs-lookup"><span data-stu-id="0804f-132">Sealed classes</span></span>

<span data-ttu-id="0804f-133">Le `sealed` modificateur est utilisé pour empêcher la dérivation à partir d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-133">The `sealed` modifier is used to prevent derivation from a class.</span></span> <span data-ttu-id="0804f-134">Une erreur de compilation se produit si une classe sealed est spécifiée en tant que classe de base d’une autre classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-134">A compile-time error occurs if a sealed class is specified as the base class of another class.</span></span>

<span data-ttu-id="0804f-135">Une classe sealed ne peut pas également être une classe abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-135">A sealed class cannot also be an abstract class.</span></span>

<span data-ttu-id="0804f-136">Le `sealed` modificateur est principalement utilisé pour empêcher la dérivation involontaire, mais il permet également certaines optimisations au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-136">The `sealed` modifier is primarily used to prevent unintended derivation, but it also enables certain run-time optimizations.</span></span> <span data-ttu-id="0804f-137">En particulier, étant donné qu’une classe sealed est connue pour ne jamais avoir de classes dérivées, il est possible de transformer des appels de membre de fonction virtuelle sur des instances de classe sealed en appels non virtuels.</span><span class="sxs-lookup"><span data-stu-id="0804f-137">In particular, because a sealed class is known to never have any derived classes, it is possible to transform virtual function member invocations on sealed class instances into non-virtual invocations.</span></span>

#### <a name="static-classes"></a><span data-ttu-id="0804f-138">Classes statiques</span><span class="sxs-lookup"><span data-stu-id="0804f-138">Static classes</span></span>

<span data-ttu-id="0804f-139">Le `static` modificateur est utilisé pour marquer la classe déclarée comme une ***classe statique***.</span><span class="sxs-lookup"><span data-stu-id="0804f-139">The `static` modifier is used to mark the class being declared as a ***static class***.</span></span> <span data-ttu-id="0804f-140">Une classe statique ne peut pas être instanciée, elle ne peut pas être utilisée en tant que type et ne peut contenir que des membres statiques.</span><span class="sxs-lookup"><span data-stu-id="0804f-140">A static class cannot be instantiated, cannot be used as a type and can contain only static members.</span></span> <span data-ttu-id="0804f-141">Seule une classe statique peut contenir des déclarations de méthodes d’extension ([méthodes d’extension](classes.md#extension-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-141">Only a static class can contain declarations of extension methods ([Extension methods](classes.md#extension-methods)).</span></span>

<span data-ttu-id="0804f-142">Une déclaration de classe statique est soumise aux restrictions suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-142">A static class declaration is subject to the following restrictions:</span></span>

*  <span data-ttu-id="0804f-143">Une classe statique ne peut pas inclure `sealed` un `abstract` modificateur ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-143">A static class may not include a `sealed` or `abstract` modifier.</span></span> <span data-ttu-id="0804f-144">Notez, toutefois, qu’étant donné qu’une classe statique ne peut pas être instanciée ou dérivée de, elle se comporte comme si elle était à la fois sealed et abstract.</span><span class="sxs-lookup"><span data-stu-id="0804f-144">Note, however, that since a static class cannot be instantiated or derived from, it behaves as if it was both sealed and abstract.</span></span>
*  <span data-ttu-id="0804f-145">Une classe statique ne peut pas inclure une spécification *class_base* ([spécification de base de classe](classes.md#class-base-specification)) et ne peut pas spécifier explicitement une classe de base ou une liste d’interfaces implémentées.</span><span class="sxs-lookup"><span data-stu-id="0804f-145">A static class may not include a *class_base* specification ([Class base specification](classes.md#class-base-specification)) and cannot explicitly specify a base class or a list of implemented interfaces.</span></span> <span data-ttu-id="0804f-146">Une classe statique hérite implicitement du type `object`.</span><span class="sxs-lookup"><span data-stu-id="0804f-146">A static class implicitly inherits from type `object`.</span></span>
*  <span data-ttu-id="0804f-147">Une classe statique ne peut contenir que des membres statiques ([membres statiques et d’instance](classes.md#static-and-instance-members)).</span><span class="sxs-lookup"><span data-stu-id="0804f-147">A static class can only contain static members ([Static and instance members](classes.md#static-and-instance-members)).</span></span> <span data-ttu-id="0804f-148">Notez que les constantes et les types imbriqués sont classés en tant que membres statiques.</span><span class="sxs-lookup"><span data-stu-id="0804f-148">Note that constants and nested types are classified as static members.</span></span>
*  <span data-ttu-id="0804f-149">Une classe statique ne peut pas avoir `protected` de `protected internal` membres avec ou d’accessibilité déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-149">A static class cannot have members with `protected` or `protected internal` declared accessibility.</span></span>

<span data-ttu-id="0804f-150">Il s’agit d’une erreur au moment de la compilation pour enfreindre l’une de ces restrictions.</span><span class="sxs-lookup"><span data-stu-id="0804f-150">It is a compile-time error to violate any of these restrictions.</span></span>

<span data-ttu-id="0804f-151">Une classe statique n’a pas de constructeurs d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-151">A static class has no instance constructors.</span></span> <span data-ttu-id="0804f-152">Il n’est pas possible de déclarer un constructeur d’instance dans une classe statique, et aucun constructeur d’instance par défaut ([constructeurs par défaut](classes.md#default-constructors)) n’est fourni pour une classe statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-152">It is not possible to declare an instance constructor in a static class, and no default instance constructor ([Default constructors](classes.md#default-constructors)) is provided for a static class.</span></span>

<span data-ttu-id="0804f-153">Les membres d’une classe statique ne sont pas automatiquement statiques, et les déclarations de membre doivent `static` inclure explicitement un modificateur (à l’exception des constantes et des types imbriqués).</span><span class="sxs-lookup"><span data-stu-id="0804f-153">The members of a static class are not automatically static, and the member declarations must explicitly include a `static` modifier (except for constants and nested types).</span></span> <span data-ttu-id="0804f-154">Quand une classe est imbriquée dans une classe externe statique, la classe imbriquée n’est pas une classe statique, sauf si elle `static` comprend explicitement un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-154">When a class is nested within a static outer class, the nested class is not a static class unless it explicitly includes a `static` modifier.</span></span>

<span data-ttu-id="0804f-155">__Référencement des types de classes statiques__</span><span class="sxs-lookup"><span data-stu-id="0804f-155">__Referencing static class types__</span></span>

<span data-ttu-id="0804f-156">Un *namespace_or_type_name* ([espace de noms et nom de type](basic-concepts.md#namespace-and-type-names)) est autorisé à faire référence à une classe statique si</span><span class="sxs-lookup"><span data-stu-id="0804f-156">A *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="0804f-157">*Namespace_or_type_name* est le `T` dans un *namespace_or_type_name* de la forme `T.I`, ou</span><span class="sxs-lookup"><span data-stu-id="0804f-157">The *namespace_or_type_name* is the `T` in a *namespace_or_type_name* of the form `T.I`, or</span></span>
*  <span data-ttu-id="0804f-158">*Namespace_or_type_name* est le `T` dans un *typeof_expression* ([liste d’arguments](expressions.md#argument-lists)1) de la forme `typeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="0804f-158">The *namespace_or_type_name* is the `T` in a *typeof_expression* ([Argument lists](expressions.md#argument-lists)1) of the form `typeof(T)`.</span></span>

<span data-ttu-id="0804f-159">Un *primary_expression* ([fonction members](expressions.md#function-members)) est autorisé à référencer une classe statique si</span><span class="sxs-lookup"><span data-stu-id="0804f-159">A *primary_expression* ([Function members](expressions.md#function-members)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="0804f-160">Le *primary_expression* est le `E` dans un *member_access* ([vérification de la résolution de surcharge dynamique au moment](expressions.md#compile-time-checking-of-dynamic-overload-resolution)de la compilation) sous la forme `E.I`.</span><span class="sxs-lookup"><span data-stu-id="0804f-160">The *primary_expression* is the `E` in a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) of the form `E.I`.</span></span>

<span data-ttu-id="0804f-161">Dans tout autre contexte, il s’agit d’une erreur de compilation pour référencer une classe statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-161">In any other context it is a compile-time error to reference a static class.</span></span> <span data-ttu-id="0804f-162">Par exemple, il s’agit d’une erreur pour une classe statique à utiliser comme classe de base, un type constitutif ([types imbriqués](classes.md#nested-types)) d’un membre, un argument de type générique ou une contrainte de paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-162">For example, it is an error for a static class to be used as a base class, a constituent type ([Nested types](classes.md#nested-types)) of a member, a generic type argument, or a type parameter constraint.</span></span> <span data-ttu-id="0804f-163">De même, une classe statique ne peut pas être utilisée dans un type de tableau, un `new` type pointeur, une expression, une `is` expression de Cast `as` , une expression `sizeof` , une expression, une expression ou une expression de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-163">Likewise, a static class cannot be used in an array type, a pointer type, a `new` expression, a cast expression, an `is` expression, an `as` expression, a `sizeof` expression, or a default value expression.</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="0804f-164">Modificateur partiel</span><span class="sxs-lookup"><span data-stu-id="0804f-164">Partial modifier</span></span>

<span data-ttu-id="0804f-165">Le modificateur `partial` est utilisé pour indiquer que ce *class_declaration* est une déclaration de type partielle.</span><span class="sxs-lookup"><span data-stu-id="0804f-165">The `partial` modifier is used to indicate that this *class_declaration* is a partial type declaration.</span></span> <span data-ttu-id="0804f-166">Plusieurs déclarations de type partiel portant le même nom dans un espace de noms englobant ou une déclaration de type sont combinées pour former une déclaration de type, suivant les règles spécifiées dans les [types partiels](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="0804f-166">Multiple partial type declarations with the same name within an enclosing namespace or type declaration combine to form one type declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

<span data-ttu-id="0804f-167">La déclaration d’une classe distribuée sur des segments distincts de texte de programme peut être utile si ces segments sont produits ou maintenus dans des contextes différents.</span><span class="sxs-lookup"><span data-stu-id="0804f-167">Having the declaration of a class distributed over separate segments of program text can be useful if these segments are produced or maintained in different contexts.</span></span> <span data-ttu-id="0804f-168">Par exemple, une partie d’une déclaration de classe peut être générée par l’ordinateur, tandis que l’autre est créée manuellement.</span><span class="sxs-lookup"><span data-stu-id="0804f-168">For instance, one part of a class declaration may be machine generated, whereas the other is manually authored.</span></span> <span data-ttu-id="0804f-169">La séparation du texte des deux empêche les mises à jour par l’une d’un conflit avec les mises à jour par l’autre.</span><span class="sxs-lookup"><span data-stu-id="0804f-169">Textual separation of the two prevents updates by one from conflicting with updates by the other.</span></span>

### <a name="type-parameters"></a><span data-ttu-id="0804f-170">Paramètres de type</span><span class="sxs-lookup"><span data-stu-id="0804f-170">Type parameters</span></span>

<span data-ttu-id="0804f-171">Un paramètre de type est un identificateur simple qui désigne un espace réservé pour un argument de type fourni pour créer un type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-171">A type parameter is a simple identifier that denotes a placeholder for a type argument supplied to create a constructed type.</span></span> <span data-ttu-id="0804f-172">Un paramètre de type est un espace réservé formel pour un type qui sera fourni ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="0804f-172">A type parameter is a formal placeholder for a type that will be supplied later.</span></span> <span data-ttu-id="0804f-173">En revanche, un argument de type ([arguments de type](types.md#type-arguments)) est le type réel qui remplace le paramètre de type lors de la création d’un type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-173">By contrast, a type argument ([Type arguments](types.md#type-arguments)) is the actual type that is substituted for the type parameter when a constructed type is created.</span></span>

```antlr
type_parameter_list
    : '<' type_parameters '>'
    ;

type_parameters
    : attributes? type_parameter
    | type_parameters ',' attributes? type_parameter
    ;

type_parameter
    : identifier
    ;
```

<span data-ttu-id="0804f-174">Chaque paramètre de type dans une déclaration de classe définit un nom dans l’espace de déclaration ([déclarations](basic-concepts.md#declarations)) de cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-174">Each type parameter in a class declaration defines a name in the declaration space ([Declarations](basic-concepts.md#declarations)) of that class.</span></span> <span data-ttu-id="0804f-175">Par conséquent, il ne peut pas avoir le même nom qu’un autre paramètre de type ou un membre déclaré dans cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-175">Thus, it cannot have the same name as another type parameter or a member declared in that class.</span></span> <span data-ttu-id="0804f-176">Un paramètre de type ne peut pas avoir le même nom que le type lui-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-176">A type parameter cannot have the same name as the type itself.</span></span>

### <a name="class-base-specification"></a><span data-ttu-id="0804f-177">Spécification de base de classe</span><span class="sxs-lookup"><span data-stu-id="0804f-177">Class base specification</span></span>

<span data-ttu-id="0804f-178">Une déclaration de classe peut inclure une spécification *class_base* , qui définit la classe de base directe de la classe et les interfaces ([interfaces](interfaces.md)) implémentées directement par la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-178">A class declaration may include a *class_base* specification, which defines the direct base class of the class and the interfaces ([Interfaces](interfaces.md)) directly implemented by the class.</span></span>

```antlr
class_base
    : ':' class_type
    | ':' interface_type_list
    | ':' class_type ',' interface_type_list
    ;

interface_type_list
    : interface_type (',' interface_type)*
    ;
```

<span data-ttu-id="0804f-179">La classe de base spécifiée dans une déclaration de classe peut être un type de classe construit ([types construits](types.md#constructed-types)).</span><span class="sxs-lookup"><span data-stu-id="0804f-179">The base class specified in a class declaration can be a constructed class type ([Constructed types](types.md#constructed-types)).</span></span> <span data-ttu-id="0804f-180">Une classe de base ne peut pas être un paramètre de type seul, bien qu’il puisse impliquer les paramètres de type qui se trouvent dans la portée.</span><span class="sxs-lookup"><span data-stu-id="0804f-180">A base class cannot be a type parameter on its own, though it can involve the type parameters that are in scope.</span></span>

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a><span data-ttu-id="0804f-181">Classes de base</span><span class="sxs-lookup"><span data-stu-id="0804f-181">Base classes</span></span>

<span data-ttu-id="0804f-182">Quand un *class_type* est inclus dans le *class_base*, il spécifie la classe de base directe de la classe déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-182">When a *class_type* is included in the *class_base*, it specifies the direct base class of the class being declared.</span></span> <span data-ttu-id="0804f-183">Si une déclaration de classe n’a pas de *class_base*, ou si le *class_base* répertorie uniquement les types d’interface, la classe de base directe est supposée être `object`.</span><span class="sxs-lookup"><span data-stu-id="0804f-183">If a class declaration has no *class_base*, or if the *class_base* lists only interface types, the direct base class is assumed to be `object`.</span></span> <span data-ttu-id="0804f-184">Une classe hérite des membres de sa classe de base directe, comme décrit dans [héritage](classes.md#inheritance).</span><span class="sxs-lookup"><span data-stu-id="0804f-184">A class inherits members from its direct base class, as described in [Inheritance](classes.md#inheritance).</span></span>

<span data-ttu-id="0804f-185">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-185">In the example</span></span>
```csharp
class A {}

class B: A {}
```
<span data-ttu-id="0804f-186">la `A` classe est considérée comme la classe de base directe `B`de et `B` est dite dérivée `A`de.</span><span class="sxs-lookup"><span data-stu-id="0804f-186">class `A` is said to be the direct base class of `B`, and `B` is said to be derived from `A`.</span></span> <span data-ttu-id="0804f-187">Étant `A` donné que ne spécifie pas explicitement une classe de base directe, sa classe de `object`base directe est implicitement.</span><span class="sxs-lookup"><span data-stu-id="0804f-187">Since `A` does not explicitly specify a direct base class, its direct base class is implicitly `object`.</span></span>

<span data-ttu-id="0804f-188">Pour un type de classe construit, si une classe de base est spécifiée dans la déclaration de classe générique, la classe de base du type construit est obtenue en substituant, pour chaque *type_parameter* dans la déclaration de la classe de base, la *type_argument correspondante* du type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-188">For a constructed class type, if a base class is specified in the generic class declaration, the base class of the constructed type is obtained by substituting, for each *type_parameter* in the base class declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="0804f-189">Compte tenu des déclarations de classes génériques</span><span class="sxs-lookup"><span data-stu-id="0804f-189">Given the generic class declarations</span></span>
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
<span data-ttu-id="0804f-190">la classe de base du type `G<int>` construit est. `B<string,int[]>`</span><span class="sxs-lookup"><span data-stu-id="0804f-190">the base class of the constructed type `G<int>` would be `B<string,int[]>`.</span></span>

<span data-ttu-id="0804f-191">La classe de base directe d’un type de classe doit être au moins aussi accessible que le type de classe lui-même ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)).</span><span class="sxs-lookup"><span data-stu-id="0804f-191">The direct base class of a class type must be at least as accessible as the class type itself ([Accessibility domains](basic-concepts.md#accessibility-domains)).</span></span> <span data-ttu-id="0804f-192">Par exemple, il s’agit d’une erreur de compilation pour `public` qu’une classe dérive `private` d' `internal` une classe ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-192">For example, it is a compile-time error for a `public` class to derive from a `private` or `internal` class.</span></span>

<span data-ttu-id="0804f-193">La classe de base directe d’un type de classe ne doit pas être de l’un `System.Array`des `System.Delegate`types suivants `System.Enum`:, `System.ValueType`, `System.MulticastDelegate`, ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-193">The direct base class of a class type must not be any of the following types: `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, or `System.ValueType`.</span></span> <span data-ttu-id="0804f-194">En outre, une déclaration de classe générique `System.Attribute` ne peut pas être utilisée en tant que classe de base directe ou indirecte.</span><span class="sxs-lookup"><span data-stu-id="0804f-194">Furthermore, a generic class declaration cannot use `System.Attribute` as a direct or indirect base class.</span></span>

<span data-ttu-id="0804f-195">Tout en déterminant la `A` signification de la spécification directe de la classe de base d’une classe `B`, la `B` classe de base directe de `object`est temporairement supposée être.</span><span class="sxs-lookup"><span data-stu-id="0804f-195">While determining the meaning of the direct base class specification `A` of a class `B`, the direct base class of `B` is temporarily assumed to be `object`.</span></span> <span data-ttu-id="0804f-196">Intuitivement, cela garantit que la signification d’une spécification de classe de base ne peut pas dépendre de lui-même de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="0804f-196">Intuitively this ensures that the meaning of a base class specification cannot recursively depend on itself.</span></span> <span data-ttu-id="0804f-197">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0804f-197">The example:</span></span>
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
<span data-ttu-id="0804f-198">`A<C.B>` est erroné `object`, car dans la spécification de la classe de base, la `C` classe de base directe de est considérée comme étant, et par conséquent (par les règles des [noms de type et d’espace de noms](basic-concepts.md#namespace-and-type-names)) `C` n’est pas considérée comme ayant un membre `B`.</span><span class="sxs-lookup"><span data-stu-id="0804f-198">is in error since in the base class specification `A<C.B>` the direct base class of `C` is considered to be `object`, and hence (by the rules of [Namespace and type names](basic-concepts.md#namespace-and-type-names))  `C` is not considered to have a member `B`.</span></span>

<span data-ttu-id="0804f-199">Les classes de base d’un type de classe sont la classe de base directe et ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-199">The base classes of a class type are the direct base class and its base classes.</span></span> <span data-ttu-id="0804f-200">En d’autres termes, l’ensemble de classes de base est la fermeture transitive de la relation de la classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-200">In other words, the set of base classes is the transitive closure of the direct base class relationship.</span></span> <span data-ttu-id="0804f-201">En vous référant à l’exemple ci-dessus, `B` les `A` classes `object`de base de sont et.</span><span class="sxs-lookup"><span data-stu-id="0804f-201">Referring to the example above, the base classes of `B` are `A` and `object`.</span></span> <span data-ttu-id="0804f-202">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-202">In the example</span></span>
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
<span data-ttu-id="0804f-203">les classes de base `D<int>` de `C<int[]>`sont `B<IComparable<int[]>>`, `A`, et `object`.</span><span class="sxs-lookup"><span data-stu-id="0804f-203">the base classes of `D<int>` are `C<int[]>`, `B<IComparable<int[]>>`, `A`, and `object`.</span></span>

<span data-ttu-id="0804f-204">À l’exception `object`de la classe, chaque type de classe a une seule classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-204">Except for class `object`, every class type has exactly one direct base class.</span></span> <span data-ttu-id="0804f-205">La `object` classe n’a pas de classe de base directe et est la classe de base fondamentale de toutes les autres classes.</span><span class="sxs-lookup"><span data-stu-id="0804f-205">The `object` class has no direct base class and is the ultimate base class of all other classes.</span></span>

<span data-ttu-id="0804f-206">Quand une classe `B` dérive d’une classe `A`, il s’agit d’une `B`erreur de compilation `A` pour que dépende de.</span><span class="sxs-lookup"><span data-stu-id="0804f-206">When a class `B` derives from a class `A`, it is a compile-time error for `A` to depend on `B`.</span></span> <span data-ttu-id="0804f-207">Une classe ***dépend directement de*** sa classe de base directe (le cas échéant) et ***dépend directement de*** la classe dans laquelle elle est immédiatement imbriquée (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="0804f-207">A class ***directly depends on*** its direct base class (if any) and ***directly depends on*** the class within which it is immediately nested (if any).</span></span> <span data-ttu-id="0804f-208">À partir de cette définition, l’ensemble complet des classes dont dépend une classe est la fermeture réflexive et transitive de la relation ***directement dépend*** de la relation.</span><span class="sxs-lookup"><span data-stu-id="0804f-208">Given this definition, the complete set of classes upon which a class depends is the reflexive and transitive closure of the ***directly depends on*** relationship.</span></span>

<span data-ttu-id="0804f-209">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-209">The example</span></span>
```csharp
class A: A {}
```
<span data-ttu-id="0804f-210">est erroné, car la classe dépend elle-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-210">is erroneous because the class depends on itself.</span></span> <span data-ttu-id="0804f-211">De même, l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-211">Likewise, the example</span></span>
```csharp
class A: B {}
class B: C {}
class C: A {}
```
<span data-ttu-id="0804f-212">est erroné, car les classes dépendent d’elles-mêmes de façon circulaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-212">is in error because the classes circularly depend on themselves.</span></span> <span data-ttu-id="0804f-213">Enfin, l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-213">Finally, the example</span></span>
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
<span data-ttu-id="0804f-214">génère une erreur au moment de la compilation `A` , car `B.C` dépend de (sa classe de base directe), `B` qui dépend de (sa `A`classe englobante immédiate), qui dépend de façon circulaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-214">results in a compile-time error because `A` depends on `B.C` (its direct base class), which depends on `B` (its immediately enclosing class), which circularly depends on `A`.</span></span>

<span data-ttu-id="0804f-215">Notez qu’une classe ne dépend pas des classes qui sont imbriquées dans celle-ci.</span><span class="sxs-lookup"><span data-stu-id="0804f-215">Note that a class does not depend on the classes that are nested within it.</span></span> <span data-ttu-id="0804f-216">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-216">In the example</span></span>
```csharp
class A
{
    class B: A {}
}
```
<span data-ttu-id="0804f-217">`B`dépend de `A` (car `A` est à la fois sa classe de base directe et sa classe englobante immédiate `A` ), `B` mais ne dépend pas `B` de (étant donné qu’il ne s’agit ni d’une classe de base, ni d’une classe englobante de `A`).</span><span class="sxs-lookup"><span data-stu-id="0804f-217">`B` depends on `A` (because `A` is both its direct base class and its immediately enclosing class), but `A` does not depend on `B` (since `B` is neither a base class nor an enclosing class of `A`).</span></span> <span data-ttu-id="0804f-218">Ainsi, l’exemple est valide.</span><span class="sxs-lookup"><span data-stu-id="0804f-218">Thus, the example is valid.</span></span>

<span data-ttu-id="0804f-219">Il n’est pas possible de dériver `sealed` d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-219">It is not possible to derive from a `sealed` class.</span></span> <span data-ttu-id="0804f-220">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-220">In the example</span></span>
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
<span data-ttu-id="0804f-221">la `B` classe est erronée, car elle tente de dériver `A`de la `sealed` classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-221">class `B` is in error because it attempts to derive from the `sealed` class `A`.</span></span>

#### <a name="interface-implementations"></a><span data-ttu-id="0804f-222">Implémentations d'interfaces</span><span class="sxs-lookup"><span data-stu-id="0804f-222">Interface implementations</span></span>

<span data-ttu-id="0804f-223">Une spécification *class_base* peut inclure une liste de types d’interfaces, auquel cas la classe est dite qui implémente directement les types d’interfaces donnés.</span><span class="sxs-lookup"><span data-stu-id="0804f-223">A *class_base* specification may include a list of interface types, in which case the class is said to directly implement the given interface types.</span></span> <span data-ttu-id="0804f-224">Les implémentations d’interface sont abordées plus en détail dans [implémentations d’interface](interfaces.md#interface-implementations).</span><span class="sxs-lookup"><span data-stu-id="0804f-224">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="type-parameter-constraints"></a><span data-ttu-id="0804f-225">Contraintes de paramètre de type</span><span class="sxs-lookup"><span data-stu-id="0804f-225">Type parameter constraints</span></span>

<span data-ttu-id="0804f-226">Les déclarations de type et de méthode génériques peuvent éventuellement spécifier des contraintes de paramètre de type en incluant *type_parameter_constraints_clause*s.</span><span class="sxs-lookup"><span data-stu-id="0804f-226">Generic type and method declarations can optionally specify type parameter constraints by including *type_parameter_constraints_clause*s.</span></span>

```antlr
type_parameter_constraints_clause
    : 'where' type_parameter ':' type_parameter_constraints
    ;

type_parameter_constraints
    : primary_constraint
    | secondary_constraints
    | constructor_constraint
    | primary_constraint ',' secondary_constraints
    | primary_constraint ',' constructor_constraint
    | secondary_constraints ',' constructor_constraint
    | primary_constraint ',' secondary_constraints ',' constructor_constraint
    ;

primary_constraint
    : class_type
    | 'class'
    | 'struct'
    ;

secondary_constraints
    : interface_type
    | type_parameter
    | secondary_constraints ',' interface_type
    | secondary_constraints ',' type_parameter
    ;

constructor_constraint
    : 'new' '(' ')'
    ;
```

<span data-ttu-id="0804f-227">Chaque *type_parameter_constraints_clause* se compose du jeton `where`, suivi du nom d’un paramètre de type, suivi d’un signe deux-points et de la liste de contraintes pour ce paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-227">Each *type_parameter_constraints_clause* consists of the token `where`, followed by the name of a type parameter, followed by a colon and the list of constraints for that type parameter.</span></span> <span data-ttu-id="0804f-228">Il peut y avoir au plus `where` une clause pour chaque paramètre de type, `where` et les clauses peuvent être listées dans n’importe quel ordre.</span><span class="sxs-lookup"><span data-stu-id="0804f-228">There can be at most one `where` clause for each type parameter, and the `where` clauses can be listed in any order.</span></span> <span data-ttu-id="0804f-229">À l' `get` instar des jetons et `set` dans un accesseur `where` de propriété, le jeton n’est pas un mot clé.</span><span class="sxs-lookup"><span data-stu-id="0804f-229">Like the `get` and `set` tokens in a property accessor, the `where` token is not a keyword.</span></span>

<span data-ttu-id="0804f-230">La liste de contraintes donnée dans une `where` clause peut inclure l’un des composants suivants, dans cet ordre : une seule contrainte primaire, une ou plusieurs contraintes secondaires et la contrainte de constructeur, `new()`.</span><span class="sxs-lookup"><span data-stu-id="0804f-230">The list of constraints given in a `where` clause can include any of the following components, in this order: a single primary constraint, one or more secondary constraints, and the constructor constraint, `new()`.</span></span>

<span data-ttu-id="0804f-231">Une contrainte principale peut être un type de classe ou la contrainte de type ***référence*** `class` ou la ***contrainte*** `struct`de type valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-231">A primary constraint can be a class type or the ***reference type constraint*** `class` or the ***value type constraint*** `struct`.</span></span> <span data-ttu-id="0804f-232">Une contrainte secondaire peut être *type_parameter* ou *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="0804f-232">A secondary constraint can be a *type_parameter* or *interface_type*.</span></span>

<span data-ttu-id="0804f-233">La contrainte de type référence spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type référence.</span><span class="sxs-lookup"><span data-stu-id="0804f-233">The reference type constraint specifies that a type argument used for the type parameter must be a reference type.</span></span> <span data-ttu-id="0804f-234">Tous les types de classe, d’interface, de délégué, de tableau et de type connu comme étant un type référence (tel que défini ci-dessous) répondent à cette contrainte.</span><span class="sxs-lookup"><span data-stu-id="0804f-234">All class types, interface types, delegate types, array types, and type parameters known to be a reference type (as defined below) satisfy this constraint.</span></span>

<span data-ttu-id="0804f-235">La contrainte de type valeur spécifie qu’un argument de type utilisé pour le paramètre de type doit être un type valeur n’acceptant pas les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="0804f-235">The value type constraint specifies that a type argument used for the type parameter must be a non-nullable value type.</span></span> <span data-ttu-id="0804f-236">Tous les types struct non Nullable, les types ENUM et les paramètres de type ayant la contrainte de type valeur satisfont cette contrainte.</span><span class="sxs-lookup"><span data-stu-id="0804f-236">All non-nullable struct types, enum types, and type parameters having the value type constraint satisfy this constraint.</span></span> <span data-ttu-id="0804f-237">Notez que, bien qu’il soit classifié comme un type valeur, un type Nullable ([types Nullable](types.md#nullable-types)) ne satisfait pas la contrainte de type valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-237">Note that although classified as a value type, a nullable type ([Nullable types](types.md#nullable-types)) does not satisfy the value type constraint.</span></span> <span data-ttu-id="0804f-238">Un paramètre de type ayant la contrainte de type valeur ne peut pas également avoir le *constructor_constraint*.</span><span class="sxs-lookup"><span data-stu-id="0804f-238">A type parameter having the value type constraint cannot also have the *constructor_constraint*.</span></span>

<span data-ttu-id="0804f-239">Les types de pointeurs ne sont jamais autorisés à être des arguments de type et ne sont pas considérés comme répondant aux contraintes de type référence ou de type valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-239">Pointer types are never allowed to be type arguments and are not considered to satisfy either the reference type or value type constraints.</span></span>

<span data-ttu-id="0804f-240">Si une contrainte est un type de classe, un type d’interface ou un paramètre de type, ce type spécifie un « type de base » minimal que chaque argument de type utilisé pour ce paramètre de type doit prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="0804f-240">If a constraint is a class type, an interface type, or a type parameter, that type specifies a minimal "base type" that every type argument used for that type parameter must support.</span></span> <span data-ttu-id="0804f-241">Chaque fois qu’un type construit ou une méthode générique est utilisé, l’argument de type est comparé aux contraintes sur le paramètre de type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-241">Whenever a constructed type or generic method is used, the type argument is checked against the constraints on the type parameter at compile-time.</span></span> <span data-ttu-id="0804f-242">L’argument de type fourni doit satisfaire aux conditions décrites dans [satisfaire les contraintes](types.md#satisfying-constraints).</span><span class="sxs-lookup"><span data-stu-id="0804f-242">The type argument supplied must satisfy the conditions described in [Satisfying constraints](types.md#satisfying-constraints).</span></span>

<span data-ttu-id="0804f-243">Une contrainte *class_type* doit respecter les règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-243">A *class_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="0804f-244">Le type doit être un type de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-244">The type must be a class type.</span></span>
*  <span data-ttu-id="0804f-245">Le type ne doit pas `sealed`être.</span><span class="sxs-lookup"><span data-stu-id="0804f-245">The type must not be `sealed`.</span></span>
*  <span data-ttu-id="0804f-246">Le type ne doit pas être l’un des types suivants `System.Array`: `System.Delegate`, `System.Enum`, ou `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="0804f-246">The type must not be one of the following types: `System.Array`, `System.Delegate`, `System.Enum`, or `System.ValueType`.</span></span>
*  <span data-ttu-id="0804f-247">Le type ne doit pas `object`être.</span><span class="sxs-lookup"><span data-stu-id="0804f-247">The type must not be `object`.</span></span> <span data-ttu-id="0804f-248">Étant donné que tous les `object`types dérivent de, une telle contrainte n’aura aucun effet si elle était autorisée.</span><span class="sxs-lookup"><span data-stu-id="0804f-248">Because all types derive from `object`, such a constraint would have no effect if it were permitted.</span></span>
*  <span data-ttu-id="0804f-249">Au plus une contrainte pour un paramètre de type donné peut être un type de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-249">At most one constraint for a given type parameter can be a class type.</span></span>

<span data-ttu-id="0804f-250">Un type spécifié en tant que contrainte *INTERFACE_TYPE* doit respecter les règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-250">A type specified as an *interface_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="0804f-251">Le type doit être un type d’interface.</span><span class="sxs-lookup"><span data-stu-id="0804f-251">The type must be an interface type.</span></span>
*  <span data-ttu-id="0804f-252">Un type ne doit pas être spécifié plus d’une fois dans `where` une clause donnée.</span><span class="sxs-lookup"><span data-stu-id="0804f-252">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="0804f-253">Dans les deux cas, la contrainte peut impliquer l’un des paramètres de type du type ou de la déclaration de méthode associés dans le cadre d’un type construit et peut impliquer le type déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-253">In either case, the constraint can involve any of the type parameters of the associated type or method declaration as part of a constructed type, and can involve the type being declared.</span></span>

<span data-ttu-id="0804f-254">Tout type de classe ou d’interface spécifié en tant que contrainte de paramètre de type doit être au moins aussi accessible ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)) que le type ou la méthode générique déclaré (e).</span><span class="sxs-lookup"><span data-stu-id="0804f-254">Any class or interface type specified as a type parameter constraint must be at least as accessible ([Accessibility constraints](basic-concepts.md#accessibility-constraints)) as the generic type or method being declared.</span></span>

<span data-ttu-id="0804f-255">Un type spécifié en tant que contrainte *type_parameter* doit respecter les règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-255">A type specified as a *type_parameter* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="0804f-256">Le type doit être un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-256">The type must be a type parameter.</span></span>
*  <span data-ttu-id="0804f-257">Un type ne doit pas être spécifié plus d’une fois dans `where` une clause donnée.</span><span class="sxs-lookup"><span data-stu-id="0804f-257">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="0804f-258">En outre, il ne doit y avoir aucun cycle dans le graphique de dépendance des paramètres de type, où la dépendance est une relation transitive définie par :</span><span class="sxs-lookup"><span data-stu-id="0804f-258">In addition there must be no cycles in the dependency graph of type parameters, where dependency is a transitive relation defined by:</span></span>

*  <span data-ttu-id="0804f-259">Si un `T` paramètre de type est utilisé en tant que contrainte pour le `S` paramètre `S` de type, ***dépend de*** `T`.</span><span class="sxs-lookup"><span data-stu-id="0804f-259">If a type parameter `T` is used as a constraint for type parameter `S` then `S` ***depends on*** `T`.</span></span>
*  <span data-ttu-id="0804f-260">Si un paramètre `S` de type dépend d’un paramètre `T` de `T` type et dépend d’un `U` paramètre `S` de type, ***dépend de*** `U`.</span><span class="sxs-lookup"><span data-stu-id="0804f-260">If a type parameter `S` depends on a type parameter `T` and `T` depends on a type parameter `U` then `S` ***depends on*** `U`.</span></span>

<span data-ttu-id="0804f-261">Compte tenu de cette relation, il s’agit d’une erreur de compilation pour qu’un paramètre de type dépende lui-même (directement ou indirectement).</span><span class="sxs-lookup"><span data-stu-id="0804f-261">Given this relation, it is a compile-time error for a type parameter to depend on itself (directly or indirectly).</span></span>

<span data-ttu-id="0804f-262">Toutes les contraintes doivent être cohérentes entre les paramètres de type dépendant.</span><span class="sxs-lookup"><span data-stu-id="0804f-262">Any constraints must be consistent among dependent type parameters.</span></span> <span data-ttu-id="0804f-263">Si le paramètre `S` de type dépend du `T` paramètre de type, alors :</span><span class="sxs-lookup"><span data-stu-id="0804f-263">If type parameter `S` depends on type parameter `T` then:</span></span>

*  <span data-ttu-id="0804f-264">`T`ne doit pas avoir la contrainte de type valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-264">`T` must not have the value type constraint.</span></span> <span data-ttu-id="0804f-265">Dans le `T` cas contraire, est `S` effectivement sealed, ce qui signifie qu’il `T`doit être du même type que, ce qui élimine le besoin de deux paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-265">Otherwise, `T` is effectively sealed so `S` would be forced to be the same type as `T`, eliminating the need for two type parameters.</span></span>
*  <span data-ttu-id="0804f-266">Si `S` a la contrainte de type valeur, `T` ne doit pas avoir de contrainte *class_type* .</span><span class="sxs-lookup"><span data-stu-id="0804f-266">If `S` has the value type constraint then `T` must not have a *class_type* constraint.</span></span>
*  <span data-ttu-id="0804f-267">Si `S` a une contrainte *class_type* `A` et `T` a une contrainte *class_type* `B`, il doit y avoir une conversion d’identité ou une conversion de référence implicite de `A` à `B` ou une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="0804f-267">If `S` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>
*  <span data-ttu-id="0804f-268">Si `S` dépend également du paramètre de type `U` et `U` a une contrainte *class_type* `A` et `T` a une contrainte *class_type* `B`, il doit y avoir une conversion d’identité ou une conversion de référence implicite de `A` pour `B` ou une conversion de référence implicite de 0 à 1.</span><span class="sxs-lookup"><span data-stu-id="0804f-268">If `S` also depends on type parameter `U` and `U` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>

<span data-ttu-id="0804f-269">La contrainte de type `S` valeur et `T` la contrainte de type référence sont valides.</span><span class="sxs-lookup"><span data-stu-id="0804f-269">It is valid for `S` to have the value type constraint and `T` to have the reference type constraint.</span></span> <span data-ttu-id="0804f-270">Cela limite `T` en fait les types `System.Object`, `System.ValueType` `System.Enum`, et n’importe quel type d’interface.</span><span class="sxs-lookup"><span data-stu-id="0804f-270">Effectively this limits `T` to the types `System.Object`, `System.ValueType`, `System.Enum`, and any interface type.</span></span>

<span data-ttu-id="0804f-271">Si la `where` clause d’un paramètre de type inclut une contrainte de constructeur (qui a `new()`la forme), il est possible d' `new` utiliser l’opérateur pour créer des instances du type ([expressions de création d’objet](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="0804f-271">If the `where` clause for a type parameter includes a constructor constraint (which has the form `new()`), it is possible to use the `new` operator to create instances of the type ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span> <span data-ttu-id="0804f-272">Tout argument de type utilisé pour un paramètre de type avec une contrainte de constructeur doit avoir un constructeur sans paramètre public (ce constructeur existe implicitement pour tout type valeur) ou être un paramètre de type ayant la contrainte de type valeur ou la contrainte de constructeur (consultez [Contraintes de paramètre de type](classes.md#type-parameter-constraints) pour plus d’informations).</span><span class="sxs-lookup"><span data-stu-id="0804f-272">Any type argument used for a type parameter with a constructor constraint must have a public parameterless constructor (this constructor implicitly exists for any value type) or be a type parameter having the value type constraint or constructor constraint (see [Type parameter constraints](classes.md#type-parameter-constraints) for details).</span></span>

<span data-ttu-id="0804f-273">Voici quelques exemples de contraintes :</span><span class="sxs-lookup"><span data-stu-id="0804f-273">The following are examples of constraints:</span></span>
```csharp
interface IPrintable
{
    void Print();
}

interface IComparable<T>
{
    int CompareTo(T value);
}

interface IKeyProvider<T>
{
    T GetKey();
}

class Printer<T> where T: IPrintable {...}

class SortedList<T> where T: IComparable<T> {...}

class Dictionary<K,V>
    where K: IComparable<K>
    where V: IPrintable, IKeyProvider<K>, new()
{
    ...
}
```

<span data-ttu-id="0804f-274">L’exemple suivant est une erreur, car elle provoque une circularité dans le graphique de dépendance des paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="0804f-274">The following example is in error because it causes a circularity in the dependency graph of the type parameters:</span></span>
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

<span data-ttu-id="0804f-275">Les exemples suivants illustrent des situations non valides supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="0804f-275">The following examples illustrate additional invalid situations:</span></span>
```csharp
class Sealed<S,T>
    where S: T
    where T: struct        // Error, T is sealed
{
    ...
}

class A {...}

class B {...}

class Incompat<S,T>
    where S: A, T
    where T: B                // Error, incompatible class-type constraints
{
    ...
}

class StructWithClass<S,T,U>
    where S: struct, T
    where T: U
    where U: A                // Error, A incompatible with struct
{
    ...
}
```

<span data-ttu-id="0804f-276">La ***classe de base effective*** d’un paramètre `T` de type est définie comme suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-276">The ***effective base class*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="0804f-277">Si `T` n’a pas de contraintes primaires ni de contraintes de paramètre de type, `object`sa classe de base effective est.</span><span class="sxs-lookup"><span data-stu-id="0804f-277">If `T` has no primary constraints or type parameter constraints, its effective base class is `object`.</span></span>
*  <span data-ttu-id="0804f-278">Si `T` a la contrainte de type valeur, sa classe de base `System.ValueType`effective est.</span><span class="sxs-lookup"><span data-stu-id="0804f-278">If `T` has the value type constraint, its effective base class is `System.ValueType`.</span></span>
*  <span data-ttu-id="0804f-279">Si `T` a une contrainte *class_type* `C` mais pas de contraintes *type_parameter* , sa classe de base effective est `C`.</span><span class="sxs-lookup"><span data-stu-id="0804f-279">If `T` has a *class_type* constraint `C` but no *type_parameter* constraints, its effective base class is `C`.</span></span>
*  <span data-ttu-id="0804f-280">Si `T` n’a pas de contrainte *class_type* mais possède une ou plusieurs contraintes *type_parameter* , sa classe de base effective est le type le plus englobé (les[opérateurs de conversion levés](conversions.md#lifted-conversion-operators)) dans l’ensemble des classes de base effectives de son *TYPE_* contraintes de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-280">If `T` has no *class_type* constraint but has one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set of effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="0804f-281">Les règles de cohérence garantissent qu’il existe un type le plus englobé.</span><span class="sxs-lookup"><span data-stu-id="0804f-281">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="0804f-282">Si `T` a à la fois une contrainte *class_type* et une ou plusieurs contraintes *type_parameter* , sa classe de base effective est le type le plus englobé (les[opérateurs de conversion levés](conversions.md#lifted-conversion-operators)) dans le jeu constitué du *class_type* contrainte de `T` et des classes de base effectives de ses contraintes *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="0804f-282">If `T` has both a *class_type* constraint and one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set consisting of the *class_type* constraint of `T` and the effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="0804f-283">Les règles de cohérence garantissent qu’il existe un type le plus englobé.</span><span class="sxs-lookup"><span data-stu-id="0804f-283">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="0804f-284">Si `T` a la contrainte de type référence, mais pas de contraintes *class_type* , sa classe de base effective est `object`.</span><span class="sxs-lookup"><span data-stu-id="0804f-284">If `T` has the reference type constraint but no *class_type* constraints, its effective base class is `object`.</span></span>

<span data-ttu-id="0804f-285">Dans le cadre de ces règles, si T a une contrainte `V` qui est un *Value_type*, utilisez à la place le type de base le plus spécifique de `V` qui est un *class_type*.</span><span class="sxs-lookup"><span data-stu-id="0804f-285">For the purpose of these rules, if T has a constraint `V` that is a *value_type*, use instead the most specific base type of `V` that is a *class_type*.</span></span> <span data-ttu-id="0804f-286">Cela ne peut jamais se produire dans une contrainte donnée explicitement, mais peut se produire lorsque les contraintes d’une méthode générique sont implicitement héritées par une déclaration de méthode de substitution ou une implémentation explicite d’une méthode d’interface.</span><span class="sxs-lookup"><span data-stu-id="0804f-286">This can never happen in an explicitly given constraint, but may occur when the constraints of a generic method are implicitly inherited by an overriding method declaration or an explicit implementation of an interface method.</span></span>

<span data-ttu-id="0804f-287">Ces règles garantissent que la classe de base effective est toujours un *class_type*.</span><span class="sxs-lookup"><span data-stu-id="0804f-287">These rules ensure that the effective base class is always a *class_type*.</span></span>

<span data-ttu-id="0804f-288">L' ***ensemble d’interfaces effectif*** d’un paramètre `T` de type est défini comme suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-288">The ***effective interface set*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="0804f-289">Si `T` n’a pas de *secondary_constraints*, son ensemble d’interfaces effectif est vide.</span><span class="sxs-lookup"><span data-stu-id="0804f-289">If `T` has no *secondary_constraints*, its effective interface set is empty.</span></span>
*  <span data-ttu-id="0804f-290">Si `T` a des contraintes *INTERFACE_TYPE* mais aucune contrainte *type_parameter* , son ensemble d’interfaces effectif est son ensemble de contraintes *INTERFACE_TYPE* .</span><span class="sxs-lookup"><span data-stu-id="0804f-290">If `T` has *interface_type* constraints but no *type_parameter* constraints, its effective interface set is its set of *interface_type* constraints.</span></span>
*  <span data-ttu-id="0804f-291">Si `T` n’a pas de contraintes *INTERFACE_TYPE* mais a des contraintes *type_parameter* , son ensemble d’interfaces effectif est l’Union des ensembles d’interfaces effectifs de ses contraintes *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="0804f-291">If `T` has no *interface_type* constraints but has *type_parameter* constraints, its effective interface set is the union of the effective interface sets of its *type_parameter* constraints.</span></span>
*  <span data-ttu-id="0804f-292">Si `T` a des contraintes *INTERFACE_TYPE* et *type_parameter* , son ensemble d’interfaces effectif est l’Union de son ensemble de contraintes *INTERFACE_TYPE* et des ensembles d’interfaces effectifs de ses *type_parameter* contraintes.</span><span class="sxs-lookup"><span data-stu-id="0804f-292">If `T` has both *interface_type* constraints and *type_parameter* constraints, its effective interface set is the union of its set of *interface_type* constraints and the effective interface sets of its *type_parameter* constraints.</span></span>

<span data-ttu-id="0804f-293">Un paramètre de type est ***connu comme un type référence*** s’il a la contrainte de type référence ou sa classe de base effective `object` n' `System.ValueType`est pas ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-293">A type parameter is ***known to be a reference type*** if it has the reference type constraint or its effective base class is not `object` or `System.ValueType`.</span></span>

<span data-ttu-id="0804f-294">Les valeurs d’un type de paramètre de type restreint peuvent être utilisées pour accéder aux membres de l’instance implicite par les contraintes.</span><span class="sxs-lookup"><span data-stu-id="0804f-294">Values of a constrained type parameter type can be used to access the instance members implied by the constraints.</span></span> <span data-ttu-id="0804f-295">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-295">In the example</span></span>
```csharp
interface IPrintable
{
    void Print();
}

class Printer<T> where T: IPrintable
{
    void PrintOne(T x) {
        x.Print();
    }
}
```
<span data-ttu-id="0804f-296">les méthodes de `IPrintable` peuvent être appelées directement sur `x` , `T` car est restreinte pour toujours implémenter `IPrintable`.</span><span class="sxs-lookup"><span data-stu-id="0804f-296">the methods of `IPrintable` can be invoked directly on `x` because `T` is constrained to always implement `IPrintable`.</span></span>

### <a name="class-body"></a><span data-ttu-id="0804f-297">Corps de la classe</span><span class="sxs-lookup"><span data-stu-id="0804f-297">Class body</span></span>

<span data-ttu-id="0804f-298">Le *class_body* d’une classe définit les membres de cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-298">The *class_body* of a class defines the members of that class.</span></span>

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a><span data-ttu-id="0804f-299">Types partiels</span><span class="sxs-lookup"><span data-stu-id="0804f-299">Partial types</span></span>

<span data-ttu-id="0804f-300">Une déclaration de type peut être fractionnée entre plusieurs ***déclarations de type partiel***.</span><span class="sxs-lookup"><span data-stu-id="0804f-300">A type declaration can be split across multiple ***partial type declarations***.</span></span> <span data-ttu-id="0804f-301">La déclaration de type est construite à partir de ses parties en suivant les règles de cette section, après quoi elle est traitée comme une déclaration unique pendant le reste du traitement au moment de la compilation et de l’exécution du programme.</span><span class="sxs-lookup"><span data-stu-id="0804f-301">The type declaration is constructed from its parts by following the rules in this section, whereupon it is treated as a single declaration during the remainder of the compile-time and run-time processing of the program.</span></span>

<span data-ttu-id="0804f-302">Un *class_declaration*, *struct_declaration* ou *interface_declaration* représente une déclaration de type partiel s’il comprend un modificateur `partial`.</span><span class="sxs-lookup"><span data-stu-id="0804f-302">A *class_declaration*, *struct_declaration* or *interface_declaration* represents a partial type declaration if it includes a `partial` modifier.</span></span> <span data-ttu-id="0804f-303">`partial`n’est pas un mot clé et agit uniquement comme un modificateur s’il apparaît immédiatement avant l’un des `class`mots `struct` clés `interface` , ou dans une déclaration de type, ou `void` avant le type dans une déclaration de méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-303">`partial` is not a keyword, and only acts as a modifier if it appears immediately before one of the keywords `class`, `struct` or `interface` in a type declaration, or before the type `void` in a method declaration.</span></span> <span data-ttu-id="0804f-304">Dans d’autres contextes, il peut être utilisé comme un identificateur normal.</span><span class="sxs-lookup"><span data-stu-id="0804f-304">In other contexts it can be used as a normal identifier.</span></span>

<span data-ttu-id="0804f-305">Chaque partie d’une déclaration de type partiel doit inclure `partial` un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-305">Each part of a partial type declaration must include a `partial` modifier.</span></span> <span data-ttu-id="0804f-306">Elle doit avoir le même nom et être déclarée dans le même espace de noms ou déclaration de type que les autres parties.</span><span class="sxs-lookup"><span data-stu-id="0804f-306">It must have the same name  and be declared in the same namespace or type declaration as the other parts.</span></span> <span data-ttu-id="0804f-307">Le `partial` modificateur indique que des parties supplémentaires de la déclaration de type peuvent exister ailleurs, mais que l’existence de ces parties supplémentaires n’est pas une exigence ; il est valide pour un type avec une déclaration `partial` unique pour inclure le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-307">The `partial` modifier indicates that additional parts of the type declaration may exist elsewhere, but the existence of such additional parts is not a requirement; it is valid for a type with a single declaration to include the `partial` modifier.</span></span>

<span data-ttu-id="0804f-308">Toutes les parties d’un type partiel doivent être compilées ensemble afin que les parties puissent être fusionnées au moment de la compilation dans une seule déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-308">All parts of a partial type must be compiled together such that the parts can be merged at compile-time into a single type declaration.</span></span> <span data-ttu-id="0804f-309">En particulier, les types partiels n’autorisent pas l’extension des types déjà compilés.</span><span class="sxs-lookup"><span data-stu-id="0804f-309">Partial types specifically do not allow already compiled types to be extended.</span></span>

<span data-ttu-id="0804f-310">Les types imbriqués peuvent être déclarés dans plusieurs parties à `partial` l’aide du modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-310">Nested types may be declared in multiple parts by using the `partial` modifier.</span></span> <span data-ttu-id="0804f-311">En règle générale, le type conteneur est `partial` déclaré à l’aide de également et chaque partie du type imbriqué est déclarée dans une autre partie du type conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-311">Typically, the containing type is declared using `partial` as well, and each part of the nested type is declared in a different part of the containing type.</span></span>

<span data-ttu-id="0804f-312">Le `partial` modificateur n’est pas autorisé sur les déclarations de délégué ou d’énumération.</span><span class="sxs-lookup"><span data-stu-id="0804f-312">The `partial` modifier is not permitted on delegate or enum declarations.</span></span>

### <a name="attributes"></a><span data-ttu-id="0804f-313">Attributs</span><span class="sxs-lookup"><span data-stu-id="0804f-313">Attributes</span></span>

<span data-ttu-id="0804f-314">Les attributs d’un type partiel sont déterminés en combinant, dans un ordre non spécifié, les attributs de chacune des parties.</span><span class="sxs-lookup"><span data-stu-id="0804f-314">The attributes of a partial type are determined by combining, in an unspecified order, the attributes of each of the parts.</span></span> <span data-ttu-id="0804f-315">Si un attribut est placé sur plusieurs parties, il équivaut à spécifier l’attribut plusieurs fois sur le type.</span><span class="sxs-lookup"><span data-stu-id="0804f-315">If an attribute is placed on multiple parts, it is equivalent to specifying the attribute multiple times on the type.</span></span> <span data-ttu-id="0804f-316">Par exemple, les deux parties :</span><span class="sxs-lookup"><span data-stu-id="0804f-316">For example, the two parts:</span></span>

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
<span data-ttu-id="0804f-317">équivalent à une déclaration telle que :</span><span class="sxs-lookup"><span data-stu-id="0804f-317">are equivalent to a declaration such as:</span></span>
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

<span data-ttu-id="0804f-318">Les attributs sur les paramètres de type sont combinés de manière similaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-318">Attributes on type parameters combine in a similar fashion.</span></span>

### <a name="modifiers"></a><span data-ttu-id="0804f-319">Modificateurs</span><span class="sxs-lookup"><span data-stu-id="0804f-319">Modifiers</span></span>

<span data-ttu-id="0804f-320">Lorsqu’une déclaration de type partiel inclut une spécification d’accessibilité `public`(les `internal`modificateurs `private` ,, et), `protected`elle doit accepter toutes les autres parties qui incluent une spécification d’accessibilité.</span><span class="sxs-lookup"><span data-stu-id="0804f-320">When a partial type declaration includes an accessibility specification (the `public`, `protected`, `internal`, and `private` modifiers) it must agree with all other parts that include an accessibility specification.</span></span> <span data-ttu-id="0804f-321">Si aucune partie d’un type partiel n’intègre une spécification d’accessibilité, le type reçoit l’accessibilité par défaut appropriée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="0804f-321">If no part of a partial type includes an accessibility specification, the type is given the appropriate default accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="0804f-322">Si une ou plusieurs déclarations partielles d’un type imbriqué incluent un `new` modificateur, aucun avertissement n’est signalé si le type imbriqué masque un membre hérité ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)).</span><span class="sxs-lookup"><span data-stu-id="0804f-322">If one or more partial declarations of a nested type include a `new` modifier, no warning is reported if the nested type hides an inherited member ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)).</span></span>

<span data-ttu-id="0804f-323">Si une ou plusieurs déclarations partielles d’une classe incluent `abstract` un modificateur, la classe est considérée comme abstraite ([classes abstraites](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="0804f-323">If one or more partial declarations of a class include an `abstract` modifier, the class is considered abstract ([Abstract classes](classes.md#abstract-classes)).</span></span> <span data-ttu-id="0804f-324">Dans le cas contraire, la classe est considérée comme non abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-324">Otherwise, the class is considered non-abstract.</span></span>

<span data-ttu-id="0804f-325">Si une ou plusieurs déclarations partielles d’une classe incluent `sealed` un modificateur, la classe est considérée comme sealed ([classes sealed](classes.md#sealed-classes)).</span><span class="sxs-lookup"><span data-stu-id="0804f-325">If one or more partial declarations of a class include a `sealed` modifier, the class is considered sealed ([Sealed classes](classes.md#sealed-classes)).</span></span> <span data-ttu-id="0804f-326">Dans le cas contraire, la classe est considérée comme non scellée.</span><span class="sxs-lookup"><span data-stu-id="0804f-326">Otherwise, the class is considered unsealed.</span></span>

<span data-ttu-id="0804f-327">Notez qu’une classe ne peut pas être à la fois abstract et sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-327">Note that a class cannot be both abstract and sealed.</span></span>

<span data-ttu-id="0804f-328">Quand le `unsafe` modificateur est utilisé sur une déclaration de type partiel, seul ce composant particulier est considéré comme un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="0804f-328">When the `unsafe` modifier is used on a partial type declaration, only that particular part is considered an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

### <a name="type-parameters-and-constraints"></a><span data-ttu-id="0804f-329">Paramètres de type et contraintes</span><span class="sxs-lookup"><span data-stu-id="0804f-329">Type parameters and constraints</span></span>

<span data-ttu-id="0804f-330">Si un type générique est déclaré dans plusieurs parties, chaque partie doit indiquer les paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-330">If a generic type is declared in multiple parts, each part must state the type parameters.</span></span> <span data-ttu-id="0804f-331">Chaque partie doit avoir le même nombre de paramètres de type et le même nom pour chaque paramètre de type, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="0804f-331">Each part must have the same number of type parameters, and the same name for each type parameter, in order.</span></span>

<span data-ttu-id="0804f-332">Lorsqu’une déclaration de type générique partielle inclut des`where` contraintes (clauses), les contraintes doivent être conformes à toutes les autres parties qui incluent des contraintes.</span><span class="sxs-lookup"><span data-stu-id="0804f-332">When a partial generic type declaration includes constraints (`where` clauses), the constraints must agree with all other parts that include constraints.</span></span> <span data-ttu-id="0804f-333">Plus précisément, chaque partie qui comprend des contraintes doit avoir des contraintes pour le même jeu de paramètres de type, et pour chaque paramètre de type, les ensembles de contraintes primaires, secondaires et de constructeurs doivent être équivalents.</span><span class="sxs-lookup"><span data-stu-id="0804f-333">Specifically, each part that includes constraints must have constraints for the same set of type parameters, and for each type parameter the sets of primary, secondary, and constructor constraints must be equivalent.</span></span> <span data-ttu-id="0804f-334">Deux ensembles de contraintes sont équivalents s’ils contiennent les mêmes membres.</span><span class="sxs-lookup"><span data-stu-id="0804f-334">Two sets of constraints are equivalent if they contain the same members.</span></span> <span data-ttu-id="0804f-335">Si aucune partie d’un type générique partiel ne spécifie des contraintes de paramètre de type, les paramètres de type sont considérés comme non restreints.</span><span class="sxs-lookup"><span data-stu-id="0804f-335">If no part of a partial generic type specifies type parameter constraints, the type parameters are considered unconstrained.</span></span>

<span data-ttu-id="0804f-336">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-336">The example</span></span>
```csharp
partial class Dictionary<K,V>
    where K: IComparable<K>
    where V: IKeyProvider<K>, IPersistable
{
    ...
}

partial class Dictionary<K,V>
    where V: IPersistable, IKeyProvider<K>
    where K: IComparable<K>
{
    ...
}

partial class Dictionary<K,V>
{
    ...
}
```
<span data-ttu-id="0804f-337">est correct, car les parties qui incluent des contraintes (les deux premières) spécifient effectivement le même jeu de contraintes primaires, secondaires et de constructeur pour le même jeu de paramètres de type, respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-337">is correct because those parts that include constraints (the first two) effectively specify the same set of primary, secondary, and constructor constraints for the same set of type parameters, respectively.</span></span>

### <a name="base-class"></a><span data-ttu-id="0804f-338">Classe de base</span><span class="sxs-lookup"><span data-stu-id="0804f-338">Base class</span></span>

<span data-ttu-id="0804f-339">Lorsqu’une déclaration de classe partielle inclut une spécification de classe de base, elle doit accepter toutes les autres parties qui incluent une spécification de classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-339">When a partial class declaration includes a base class specification it must agree with all other parts that include a base class specification.</span></span> <span data-ttu-id="0804f-340">Si aucune partie d’une classe partielle n’intègre une spécification de classe de base, la `System.Object` classe de base devient ([classes de base](classes.md#base-classes)).</span><span class="sxs-lookup"><span data-stu-id="0804f-340">If no part of a partial class includes a base class specification, the base class becomes `System.Object` ([Base classes](classes.md#base-classes)).</span></span>

### <a name="base-interfaces"></a><span data-ttu-id="0804f-341">Interfaces de base</span><span class="sxs-lookup"><span data-stu-id="0804f-341">Base interfaces</span></span>

<span data-ttu-id="0804f-342">L’ensemble d’interfaces de base pour un type déclaré en plusieurs parties est l’Union des interfaces de base spécifiées sur chaque composant.</span><span class="sxs-lookup"><span data-stu-id="0804f-342">The set of base interfaces for a type declared in multiple parts is the union of the base interfaces specified on each part.</span></span> <span data-ttu-id="0804f-343">Une interface de base particulière ne peut être nommée qu’une seule fois sur chaque partie, mais elle est autorisée pour plusieurs parties à nommer les mêmes interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-343">A particular base interface may only be named once on each part, but it is permitted for multiple parts to name the same base interface(s).</span></span> <span data-ttu-id="0804f-344">Il ne doit exister qu’une seule implémentation des membres d’une interface de base donnée.</span><span class="sxs-lookup"><span data-stu-id="0804f-344">There must only be one implementation of the members of any given base interface.</span></span>

<span data-ttu-id="0804f-345">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-345">In the example</span></span>
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
<span data-ttu-id="0804f-346">l’ensemble d’interfaces de base pour `C` la `IA`classe `IB`est, `IC`et.</span><span class="sxs-lookup"><span data-stu-id="0804f-346">the set of base interfaces for class `C` is `IA`, `IB`, and `IC`.</span></span>

<span data-ttu-id="0804f-347">En règle générale, chaque partie fournit une implémentation de l’interface déclarée sur cette partie ; Toutefois, cela n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="0804f-347">Typically, each part provides an implementation of the interface(s) declared on that part; however, this is not a requirement.</span></span> <span data-ttu-id="0804f-348">Un composant peut fournir l’implémentation d’une interface déclarée sur une autre partie :</span><span class="sxs-lookup"><span data-stu-id="0804f-348">A part may provide the implementation for an interface declared on a different part:</span></span>
```csharp
partial class X
{
    int IComparable.CompareTo(object o) {...}
}

partial class X: IComparable
{
    ...
}
```

### <a name="members"></a><span data-ttu-id="0804f-349">Membres</span><span class="sxs-lookup"><span data-stu-id="0804f-349">Members</span></span>

<span data-ttu-id="0804f-350">À l’exception des méthodes partielles ([méthodes partielles](classes.md#partial-methods)), le jeu de membres d’un type déclaré en plusieurs parties est simplement l’Union de l’ensemble des membres déclarés dans chaque partie.</span><span class="sxs-lookup"><span data-stu-id="0804f-350">With the exception of partial methods ([Partial methods](classes.md#partial-methods)), the set of members of a type declared in multiple parts is simply the union of the set of members declared in each part.</span></span> <span data-ttu-id="0804f-351">Les corps de toutes les parties de la déclaration de type partagent le même espace de déclaration ([déclarations](basic-concepts.md#declarations)) et l’étendue de chaque membre ([portées](basic-concepts.md#scopes)) s’étend aux corps de toutes les parties.</span><span class="sxs-lookup"><span data-stu-id="0804f-351">The bodies of all parts of the type declaration share the same declaration space ([Declarations](basic-concepts.md#declarations)), and the scope of each member ([Scopes](basic-concepts.md#scopes)) extends to the bodies of all the parts.</span></span> <span data-ttu-id="0804f-352">Le domaine d’accessibilité de n’importe quel membre comprend toujours toutes les parties du type englobant ; un `private` membre déclaré dans une partie est librement accessible à partir d’une autre partie.</span><span class="sxs-lookup"><span data-stu-id="0804f-352">The accessibility domain of any member always includes all the parts of the enclosing type; a `private` member declared in one part is freely accessible from another part.</span></span> <span data-ttu-id="0804f-353">Il s’agit d’une erreur au moment de la compilation pour déclarer le même membre dans plusieurs parties du type, sauf si ce membre est un type `partial` avec le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-353">It is a compile-time error to declare the same member in more than one part of the type, unless that member is a type with the `partial` modifier.</span></span>

```csharp
partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int y;
    }
}

partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int z;
    }
}
```

<span data-ttu-id="0804f-354">L’ordonnancement des membres dans un type est rarement significatif C# pour le code, mais peut être significatif lors de l’interfaçage avec d’autres langages et environnements.</span><span class="sxs-lookup"><span data-stu-id="0804f-354">The ordering of members within a type is rarely significant to C# code, but may be significant when interfacing with other languages and environments.</span></span> <span data-ttu-id="0804f-355">Dans ce cas, l’ordre des membres dans un type déclaré dans plusieurs parties n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="0804f-355">In these cases, the ordering of members within a type declared in multiple parts is undefined.</span></span>

### <a name="partial-methods"></a><span data-ttu-id="0804f-356">Méthodes partielles</span><span class="sxs-lookup"><span data-stu-id="0804f-356">Partial methods</span></span>

<span data-ttu-id="0804f-357">Les méthodes partielles peuvent être définies dans une partie d’une déclaration de type et implémentées dans une autre.</span><span class="sxs-lookup"><span data-stu-id="0804f-357">Partial methods can be defined in one part of a type declaration and implemented in another.</span></span> <span data-ttu-id="0804f-358">L’implémentation est facultative ; Si aucune partie n’implémente la méthode partielle, la déclaration de méthode partielle et tous les appels à celle-ci sont supprimés de la déclaration de type résultant de la combinaison des parties.</span><span class="sxs-lookup"><span data-stu-id="0804f-358">The implementation is optional; if no part implements the partial method, the partial method declaration and all calls to it are removed from the type declaration resulting from the combination of the parts.</span></span>

<span data-ttu-id="0804f-359">Les méthodes partielles ne peuvent pas définir de modificateurs d' `private`accès, mais sont implicitement.</span><span class="sxs-lookup"><span data-stu-id="0804f-359">Partial methods cannot define access modifiers, but are implicitly `private`.</span></span> <span data-ttu-id="0804f-360">Leur type de retour doit `void`être, et leurs paramètres ne peuvent `out` pas avoir le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-360">Their return type must be `void`, and their parameters cannot have the `out` modifier.</span></span> <span data-ttu-id="0804f-361">L’identificateur `partial` est reconnu comme un mot clé spécial dans une déclaration de méthode uniquement s’il apparaît juste avant `void` le type ; sinon, il peut être utilisé comme un identificateur normal.</span><span class="sxs-lookup"><span data-stu-id="0804f-361">The identifier `partial` is recognized as a special keyword in a method declaration only if it appears right before the `void` type; otherwise it can be used as a normal identifier.</span></span> <span data-ttu-id="0804f-362">Une méthode partielle ne peut pas implémenter explicitement des méthodes d’interface.</span><span class="sxs-lookup"><span data-stu-id="0804f-362">A partial method cannot explicitly implement interface methods.</span></span>

<span data-ttu-id="0804f-363">Il existe deux types de déclarations de méthode partielles : Si le corps de la déclaration de méthode est un point-virgule, la déclaration est dite « ***définition de déclaration de méthode partielle***».</span><span class="sxs-lookup"><span data-stu-id="0804f-363">There are two kinds of partial method declarations: If the body of the method declaration is a semicolon, the declaration is said to be a ***defining partial method declaration***.</span></span> <span data-ttu-id="0804f-364">Si le corps est fourni en tant que *bloc*, la déclaration est considérée comme une ***déclaration de méthode partielle d’implémentation***.</span><span class="sxs-lookup"><span data-stu-id="0804f-364">If the body is given as a *block*, the declaration is said to be an ***implementing partial method declaration***.</span></span> <span data-ttu-id="0804f-365">Dans les parties d’une déclaration de type, il ne peut y avoir qu’une seule déclaration de méthode partielle avec une signature donnée, et il ne peut y avoir qu’une seule déclaration de méthode partielle implémentée avec une signature donnée.</span><span class="sxs-lookup"><span data-stu-id="0804f-365">Across the parts of a type declaration there can be only one defining partial method declaration with a given signature, and there can be only one implementing partial method declaration with a given signature.</span></span> <span data-ttu-id="0804f-366">Si une déclaration de méthode partielle d’implémentation est fournie, une déclaration de méthode partielle de définition correspondante doit exister et les déclarations doivent correspondre comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0804f-366">If an implementing partial method declaration is given, a corresponding defining partial method declaration must exist, and the declarations must match as specified in the following:</span></span>

* <span data-ttu-id="0804f-367">Les déclarations doivent avoir les mêmes modificateurs (mais pas nécessairement dans le même ordre), le nom de la méthode, le nombre de paramètres de type et le nombre de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-367">The declarations must have the same modifiers (although not necessarily in the same order), method name, number of type parameters and number of parameters.</span></span>
* <span data-ttu-id="0804f-368">Les paramètres correspondants dans les déclarations doivent avoir les mêmes modificateurs (mais pas nécessairement dans le même ordre) et les mêmes types (différences modulo dans les noms de paramètres de type).</span><span class="sxs-lookup"><span data-stu-id="0804f-368">Corresponding parameters in the declarations must have the same modifiers (although not necessarily in the same order) and the same types (modulo differences in type parameter names).</span></span>
* <span data-ttu-id="0804f-369">Les paramètres de type correspondants dans les déclarations doivent avoir les mêmes contraintes (différences modulo dans les noms de paramètre de type).</span><span class="sxs-lookup"><span data-stu-id="0804f-369">Corresponding type parameters in the declarations must have the same constraints (modulo differences in type parameter names).</span></span>

<span data-ttu-id="0804f-370">Une déclaration de méthode partielle d’implémentation peut apparaître dans la même partie que la déclaration de méthode partielle de définition correspondante.</span><span class="sxs-lookup"><span data-stu-id="0804f-370">An implementing partial method declaration can appear in the same part as the corresponding defining partial method declaration.</span></span>

<span data-ttu-id="0804f-371">Seule une méthode partielle de définition participe à la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="0804f-371">Only a defining partial method participates in overload resolution.</span></span> <span data-ttu-id="0804f-372">Par conséquent, qu’une déclaration d’implémentation soit donnée ou non, les expressions d’appel peuvent être résolues en appels de la méthode partielle.</span><span class="sxs-lookup"><span data-stu-id="0804f-372">Thus, whether or not an implementing declaration is given, invocation expressions may resolve to invocations of the partial method.</span></span> <span data-ttu-id="0804f-373">Comme une méthode partielle retourne `void`toujours, ces expressions d’appel sont toujours des instructions d’expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-373">Because a partial method always returns `void`, such invocation expressions will always be expression statements.</span></span> <span data-ttu-id="0804f-374">En outre, étant donné qu’une méthode partielle `private`est implicitement, ces instructions sont toujours exécutées dans l’une des parties de la déclaration de type dans laquelle la méthode partielle est déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-374">Furthermore, because a partial method is implicitly `private`, such statements will always occur within one of the parts of the type declaration within which the partial method is declared.</span></span>

<span data-ttu-id="0804f-375">Si aucune partie d’une déclaration de type partiel ne contient une déclaration d’implémentation pour une méthode partielle donnée, toute instruction d’expression qui l’appelle est simplement supprimée de la déclaration de type combiné.</span><span class="sxs-lookup"><span data-stu-id="0804f-375">If no part of a partial type declaration contains an implementing declaration for a given partial method, any expression statement invoking it is simply removed from the combined type declaration.</span></span> <span data-ttu-id="0804f-376">Ainsi, l’expression d’appel, y compris toutes les expressions constitutives, n’a aucun effet au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-376">Thus the invocation expression, including any constituent expressions, has no effect at run-time.</span></span> <span data-ttu-id="0804f-377">La méthode partielle elle-même est également supprimée et ne sera pas membre de la déclaration de type combiné.</span><span class="sxs-lookup"><span data-stu-id="0804f-377">The partial method itself is also removed and will not be a member of the combined type declaration.</span></span>

<span data-ttu-id="0804f-378">Si une déclaration d’implémentation existe pour une méthode partielle donnée, les appels des méthodes partielles sont conservés.</span><span class="sxs-lookup"><span data-stu-id="0804f-378">If an implementing declaration exist for a given partial method, the invocations of the partial methods are retained.</span></span> <span data-ttu-id="0804f-379">La méthode partielle donne lieu à une déclaration de méthode similaire à la déclaration de méthode partielle d’implémentation, à l’exception des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="0804f-379">The partial method gives rise to a method declaration similar to the implementing partial method declaration except for the following:</span></span>

* <span data-ttu-id="0804f-380">Le `partial` modificateur n’est pas inclus</span><span class="sxs-lookup"><span data-stu-id="0804f-380">The `partial` modifier is not included</span></span>
* <span data-ttu-id="0804f-381">Les attributs de la déclaration de méthode résultante sont les attributs combinés de la définition et de la déclaration de méthode partielle d’implémentation dans un ordre non spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-381">The attributes in the resulting method declaration are the combined attributes of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="0804f-382">Les doublons ne sont pas supprimés.</span><span class="sxs-lookup"><span data-stu-id="0804f-382">Duplicates are not removed.</span></span>
* <span data-ttu-id="0804f-383">Les attributs sur les paramètres de la déclaration de méthode résultante sont les attributs combinés des paramètres correspondants de la définition et de la déclaration de méthode partielle d’implémentation dans un ordre non spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-383">The attributes on the parameters of the resulting method declaration are the combined attributes of the corresponding parameters of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="0804f-384">Les doublons ne sont pas supprimés.</span><span class="sxs-lookup"><span data-stu-id="0804f-384">Duplicates are not removed.</span></span>

<span data-ttu-id="0804f-385">Si une déclaration de définition, mais pas une déclaration d’implémentation, est fournie pour une méthode partielle M, les restrictions suivantes s’appliquent :</span><span class="sxs-lookup"><span data-stu-id="0804f-385">If a defining declaration but not an implementing declaration is given for a partial method M, the following restrictions apply:</span></span>

* <span data-ttu-id="0804f-386">Il s’agit d’une erreur au moment de la compilation pour créer un délégué à une méthode ([expressions de création de délégué](expressions.md#delegate-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="0804f-386">It is a compile-time error to create a delegate to method ([Delegate creation expressions](expressions.md#delegate-creation-expressions)).</span></span>
* <span data-ttu-id="0804f-387">Il s’agit d’une erreur de compilation qui se `M` réfère à l’intérieur d’une fonction anonyme convertie en un type d’arborescence d’expression ([évaluation de conversions de fonction anonyme en types d’arborescence d’expression](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="0804f-387">It is a compile-time error to refer to `M` inside an anonymous function that is converted to an expression tree type ([Evaluation of anonymous function conversions to expression tree types](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span></span>
* <span data-ttu-id="0804f-388">Les expressions qui se trouvent dans le cadre d' `M` un appel de n’affectent pas l’état d’assignation définie ([assignation définie](variables.md#definite-assignment)), ce qui peut entraîner des erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-388">Expressions occurring as part of an invocation of `M` do not affect the definite assignment state ([Definite assignment](variables.md#definite-assignment)), which can potentially lead to compile-time errors.</span></span>
* <span data-ttu-id="0804f-389">`M`ne peut pas être le point d’entrée d’une application (démarrage de l'[application](basic-concepts.md#application-startup)).</span><span class="sxs-lookup"><span data-stu-id="0804f-389">`M` cannot be the entry point for an application ([Application Startup](basic-concepts.md#application-startup)).</span></span>

<span data-ttu-id="0804f-390">Les méthodes partielles sont utiles pour permettre à une partie d’une déclaration de type de personnaliser le comportement d’une autre partie, par exemple, celle qui est générée par un outil.</span><span class="sxs-lookup"><span data-stu-id="0804f-390">Partial methods are useful for allowing one part of a type declaration to customize the behavior of another part, e.g., one that is generated by a tool.</span></span> <span data-ttu-id="0804f-391">Considérons la déclaration de classe partielle suivante :</span><span class="sxs-lookup"><span data-stu-id="0804f-391">Consider the following partial class declaration:</span></span>
```csharp
partial class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    partial void OnNameChanging(string newName);

    partial void OnNameChanged();
}
```

<span data-ttu-id="0804f-392">Si cette classe est compilée sans autre partie, les déclarations de méthode partielle de définition et leurs appels seront supprimés, et la déclaration de classe combinée résultante sera équivalente à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-392">If this class is compiled without any other parts, the defining partial method declarations and their invocations will be removed, and the resulting combined class declaration will be equivalent to the following:</span></span>
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set { name = value; }
    }
}
```

<span data-ttu-id="0804f-393">Supposons qu’une autre partie est fournie, toutefois, qui fournit des déclarations d’implémentation des méthodes partielles :</span><span class="sxs-lookup"><span data-stu-id="0804f-393">Assume that another part is given, however, which provides implementing declarations of the partial methods:</span></span>
```csharp
partial class Customer
{
    partial void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    partial void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

<span data-ttu-id="0804f-394">La déclaration de classe combinée résultante est alors équivalente à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-394">Then the resulting combined class declaration will be equivalent to the following:</span></span>
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

### <a name="name-binding"></a><span data-ttu-id="0804f-395">Liaison de nom</span><span class="sxs-lookup"><span data-stu-id="0804f-395">Name binding</span></span>

<span data-ttu-id="0804f-396">Bien que chaque partie d’un type extensible doive être déclarée dans le même espace de noms, les parties sont généralement écrites dans des déclarations d’espaces de noms différentes.</span><span class="sxs-lookup"><span data-stu-id="0804f-396">Although each part of an extensible type must be declared within the same namespace, the parts are typically written within different namespace declarations.</span></span> <span data-ttu-id="0804f-397">Ainsi, différentes `using` directives ([directives using](namespaces.md#using-directives)) peuvent être présentes pour chaque partie.</span><span class="sxs-lookup"><span data-stu-id="0804f-397">Thus, different `using` directives ([Using directives](namespaces.md#using-directives)) may be present for each part.</span></span> <span data-ttu-id="0804f-398">Lors de l’interprétation de noms simples ([inférence de type](expressions.md#type-inference)) dans une seule `using` partie, seules les directives de la ou des déclarations d’espace de noms englobant cette partie sont prises en compte.</span><span class="sxs-lookup"><span data-stu-id="0804f-398">When interpreting simple names ([Type inference](expressions.md#type-inference)) within one part, only the `using` directives of the namespace declaration(s) enclosing that part are considered.</span></span> <span data-ttu-id="0804f-399">Cela peut entraîner un même identificateur ayant des significations différentes dans différentes parties :</span><span class="sxs-lookup"><span data-stu-id="0804f-399">This may result in the same identifier having different meanings in different parts:</span></span>
```csharp
namespace N
{
    using List = System.Collections.ArrayList;

    partial class A
    {
        List x;                // x has type System.Collections.ArrayList
    }
}

namespace N
{
    using List = Widgets.LinkedList;

    partial class A
    {
        List y;                // y has type Widgets.LinkedList
    }
}
```

## <a name="class-members"></a><span data-ttu-id="0804f-400">Membres de classe</span><span class="sxs-lookup"><span data-stu-id="0804f-400">Class members</span></span>

<span data-ttu-id="0804f-401">Les membres d’une classe se composent des membres introduits par ses *class_member_declaration*s et des membres hérités de la classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-401">The members of a class consist of the members introduced by its *class_member_declaration*s and the members inherited from the direct base class.</span></span>

```antlr
class_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | destructor_declaration
    | static_constructor_declaration
    | type_declaration
    ;
```

<span data-ttu-id="0804f-402">Les membres d’un type de classe sont répartis dans les catégories suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-402">The members of a class type are divided into the following categories:</span></span>

*  <span data-ttu-id="0804f-403">Les constantes, qui représentent des valeurs constantes associées à la classe ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="0804f-403">Constants, which represent constant values associated with the class ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="0804f-404">Les champs, qui sont les variables de la classe ([champs](classes.md#fields)).</span><span class="sxs-lookup"><span data-stu-id="0804f-404">Fields, which are the variables of the class ([Fields](classes.md#fields)).</span></span>
*  <span data-ttu-id="0804f-405">Les méthodes qui implémentent les calculs et les actions qui peuvent être effectuées par la classe ([méthodes](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-405">Methods, which implement the computations and actions that can be performed by the class ([Methods](classes.md#methods)).</span></span>
*  <span data-ttu-id="0804f-406">Propriétés, qui définissent les caractéristiques nommées et les actions associées à la lecture et à l’écriture de ces caractéristiques ([Propriétés](classes.md#properties)).</span><span class="sxs-lookup"><span data-stu-id="0804f-406">Properties, which define named characteristics and the actions associated with reading and writing those characteristics ([Properties](classes.md#properties)).</span></span>
*  <span data-ttu-id="0804f-407">Les événements, qui définissent des notifications qui peuvent être générées par la classe ([événements](classes.md#events)).</span><span class="sxs-lookup"><span data-stu-id="0804f-407">Events, which define notifications that can be generated by the class ([Events](classes.md#events)).</span></span>
*  <span data-ttu-id="0804f-408">Les indexeurs, qui autorisent l’indexation des instances de la classe de la même façon (syntaxiquement) en tant que tableaux ([indexeurs](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-408">Indexers, which permit instances of the class to be indexed in the same way (syntactically) as arrays ([Indexers](classes.md#indexers)).</span></span>
*  <span data-ttu-id="0804f-409">Les opérateurs, qui définissent les opérateurs d’expression qui peuvent être appliqués aux instances de la classe ([opérateurs](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="0804f-409">Operators, which define the expression operators that can be applied to instances of the class ([Operators](classes.md#operators)).</span></span>
*  <span data-ttu-id="0804f-410">Constructeurs d’instance, qui implémentent les actions requises pour initialiser les instances de la classe ([constructeurs d’instance](classes.md#instance-constructors))</span><span class="sxs-lookup"><span data-stu-id="0804f-410">Instance constructors, which implement the actions required to initialize instances of the class ([Instance constructors](classes.md#instance-constructors))</span></span>
*  <span data-ttu-id="0804f-411">Les destructeurs, qui implémentent les actions à effectuer avant que les instances de la classe soient définitivement ignorées ([destructeurs](classes.md#destructors)).</span><span class="sxs-lookup"><span data-stu-id="0804f-411">Destructors, which implement the actions to be performed before instances of the class are permanently discarded ([Destructors](classes.md#destructors)).</span></span>
*  <span data-ttu-id="0804f-412">Constructeurs statiques, qui implémentent les actions requises pour initialiser la classe elle-même ([constructeurs statiques](classes.md#static-constructors)).</span><span class="sxs-lookup"><span data-stu-id="0804f-412">Static constructors, which implement the actions required to initialize the class itself ([Static constructors](classes.md#static-constructors)).</span></span>
*  <span data-ttu-id="0804f-413">Types, qui représentent les types qui sont locaux pour la classe ([types imbriqués](classes.md#nested-types)).</span><span class="sxs-lookup"><span data-stu-id="0804f-413">Types, which represent the types that are local to the class ([Nested types](classes.md#nested-types)).</span></span>

<span data-ttu-id="0804f-414">Les membres qui peuvent contenir du code exécutable sont collectivement appelés *membres de fonction* du type de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-414">Members that can contain executable code are collectively known as the *function members* of the class type.</span></span> <span data-ttu-id="0804f-415">Les fonctions membres d’un type de classe sont les méthodes, les propriétés, les événements, les indexeurs, les opérateurs, les constructeurs d’instance, les destructeurs et les constructeurs statiques de ce type de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-415">The function members of a class type are the methods, properties, events, indexers, operators, instance constructors,  destructors, and static constructors of that class type.</span></span>

<span data-ttu-id="0804f-416">Un *class_declaration* crée un nouvel espace de déclaration ([déclarations](basic-concepts.md#declarations)) et le *class_member_declaration*s contenu immédiatement dans le *class_declaration* introduit de nouveaux membres dans cet espace de déclaration.</span><span class="sxs-lookup"><span data-stu-id="0804f-416">A *class_declaration* creates a new declaration space ([Declarations](basic-concepts.md#declarations)), and the *class_member_declaration*s immediately contained by the *class_declaration* introduce new members into this declaration space.</span></span> <span data-ttu-id="0804f-417">Les règles suivantes s’appliquent à *class_member_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-417">The following rules apply to *class_member_declaration*s:</span></span>

*  <span data-ttu-id="0804f-418">Les constructeurs d’instance, les destructeurs et les constructeurs statiques doivent avoir le même nom que la classe immédiatement englobante.</span><span class="sxs-lookup"><span data-stu-id="0804f-418">Instance constructors, destructors and static constructors must have the same name as the immediately enclosing class.</span></span> <span data-ttu-id="0804f-419">Tous les autres membres doivent avoir des noms qui diffèrent du nom de la classe englobante immédiatement.</span><span class="sxs-lookup"><span data-stu-id="0804f-419">All other members must have names that differ from the name of the immediately enclosing class.</span></span>
*  <span data-ttu-id="0804f-420">Le nom d’une constante, d’un champ, d’une propriété, d’un événement ou d’un type doit différer des noms de tous les autres membres déclarés dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-420">The name of a constant, field, property, event, or type must differ from the names of all other members declared in the same class.</span></span>
*  <span data-ttu-id="0804f-421">Le nom d’une méthode doit être différent des noms de toutes les autres méthodes non déclarées dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-421">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="0804f-422">En outre, la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir de signatures qui `ref` diffèrent uniquement par et `out`.</span><span class="sxs-lookup"><span data-stu-id="0804f-422">In addition, the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="0804f-423">La signature d’un constructeur d’instance doit être différente des signatures de tous les autres constructeurs d’instance déclarés dans la même classe, et deux constructeurs déclarés dans la même classe ne peuvent pas avoir de `ref` signatures `out`qui diffèrent uniquement par et.</span><span class="sxs-lookup"><span data-stu-id="0804f-423">The signature of an instance constructor must differ from the signatures of all other instance constructors declared in the same class, and two constructors declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="0804f-424">La signature d’un indexeur doit être différente des signatures de tous les autres indexeurs déclarés dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-424">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>
*  <span data-ttu-id="0804f-425">La signature d’un opérateur doit être différente des signatures de tous les autres opérateurs déclarés dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-425">The signature of an operator must differ from the signatures of all other operators declared in the same class.</span></span>

<span data-ttu-id="0804f-426">Les membres hérités d’un type de classe ([héritage](classes.md#inheritance)) ne font pas partie de l’espace de déclaration d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-426">The inherited members of a class type ([Inheritance](classes.md#inheritance)) are not part of the declaration space of a class.</span></span> <span data-ttu-id="0804f-427">Par conséquent, une classe dérivée est autorisée à déclarer un membre avec le même nom ou la même signature qu’un membre hérité (qui, en effet, masque le membre hérité).</span><span class="sxs-lookup"><span data-stu-id="0804f-427">Thus, a derived class is allowed to declare a member with the same name or signature as an inherited member (which in effect hides the inherited member).</span></span>

### <a name="the-instance-type"></a><span data-ttu-id="0804f-428">Type d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-428">The instance type</span></span>

<span data-ttu-id="0804f-429">Chaque déclaration de classe a un type dépendant associé ([types liés et indépendants](types.md#bound-and-unbound-types)), le ***type d’instance***.</span><span class="sxs-lookup"><span data-stu-id="0804f-429">Each class declaration has an associated bound type ([Bound and unbound types](types.md#bound-and-unbound-types)), the ***instance type***.</span></span> <span data-ttu-id="0804f-430">Pour une déclaration de classe générique, le type d’instance est formé en créant un type construit ([types construits](types.md#constructed-types)) à partir de la déclaration de type, avec chacun des arguments de type fournis étant le paramètre de type correspondant.</span><span class="sxs-lookup"><span data-stu-id="0804f-430">For a generic class declaration, the instance type is formed by creating a constructed type ([Constructed types](types.md#constructed-types)) from the type declaration, with each of the supplied type arguments being the corresponding type parameter.</span></span> <span data-ttu-id="0804f-431">Étant donné que le type d’instance utilise les paramètres de type, il ne peut être utilisé que lorsque les paramètres de type sont dans la portée ; autrement dit, à l’intérieur de la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-431">Since the instance type uses the type parameters, it can only be used where the type parameters are in scope; that is, inside the class declaration.</span></span> <span data-ttu-id="0804f-432">Le type d’instance est le type `this` de pour le code écrit à l’intérieur de la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-432">The instance type is the type of `this` for code written inside the class declaration.</span></span> <span data-ttu-id="0804f-433">Pour les classes non génériques, le type d’instance est simplement la classe déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-433">For non-generic classes, the instance type is simply the declared class.</span></span> <span data-ttu-id="0804f-434">L’exemple suivant illustre plusieurs déclarations de classe avec leurs types d’instance :</span><span class="sxs-lookup"><span data-stu-id="0804f-434">The following shows several class declarations along with their instance types:</span></span> 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a><span data-ttu-id="0804f-435">Membres des types construits</span><span class="sxs-lookup"><span data-stu-id="0804f-435">Members of constructed types</span></span>

<span data-ttu-id="0804f-436">Les membres non hérités d’un type construit sont obtenus en substituant, pour chaque *type_parameter* dans la déclaration de membre, les *type_argument* correspondants du type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-436">The non-inherited members of a constructed type are obtained by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="0804f-437">Le processus de substitution est basé sur la signification sémantique des déclarations de type et n’est pas simplement une substitution textuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-437">The substitution process is based on the semantic meaning of type declarations, and is not simply textual substitution.</span></span>

<span data-ttu-id="0804f-438">Par exemple, à partir de la déclaration de classe générique</span><span class="sxs-lookup"><span data-stu-id="0804f-438">For example, given the generic class declaration</span></span>
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
<span data-ttu-id="0804f-439">le type `Gen<int[],IComparable<string>>` construit contient les membres suivants :</span><span class="sxs-lookup"><span data-stu-id="0804f-439">the constructed type `Gen<int[],IComparable<string>>` has the following members:</span></span>
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

<span data-ttu-id="0804f-440">`a` Le type du membre dans la Déclaration `Gen` de classe générique est « tableau à deux dimensions de `T`». par conséquent, le type du `a` membre dans le type construit ci-dessus est «tableau à deux dimensions d’un tableau unidimensionnel de `int`"ou `int[,][]`.</span><span class="sxs-lookup"><span data-stu-id="0804f-440">The type of the member `a` in the generic class declaration `Gen` is "two-dimensional array of `T`", so the type of the member `a` in the constructed type above is "two-dimensional array of one-dimensional array of `int`", or `int[,][]`.</span></span>

<span data-ttu-id="0804f-441">Dans les membres de fonction d’instance, `this` le type de est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la déclaration conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-441">Within instance function members, the type of `this` is the instance type ([The instance type](classes.md#the-instance-type)) of the containing declaration.</span></span>

<span data-ttu-id="0804f-442">Tous les membres d’une classe générique peuvent utiliser des paramètres de type de n’importe quelle classe englobante, directement ou dans le cadre d’un type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-442">All members of a generic class can use type parameters from any enclosing class, either directly or as part of a constructed type.</span></span> <span data-ttu-id="0804f-443">Lorsqu’un type construit fermé particulier ([types ouverts et fermés](types.md#open-and-closed-types)) est utilisé au moment de l’exécution, chaque utilisation d’un paramètre de type est remplacée par l’argument de type réel fourni au type construit.</span><span class="sxs-lookup"><span data-stu-id="0804f-443">When a particular closed constructed type ([Open and closed types](types.md#open-and-closed-types)) is used at run-time, each use of a type parameter is replaced with the actual type argument supplied to the constructed type.</span></span> <span data-ttu-id="0804f-444">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-444">For example:</span></span>
```csharp
class C<V>
{
    public V f1;
    public C<V> f2 = null;

    public C(V x) {
        this.f1 = x;
        this.f2 = this;
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>(1);
        Console.WriteLine(x1.f1);        // Prints 1

        C<double> x2 = new C<double>(3.1415);
        Console.WriteLine(x2.f1);        // Prints 3.1415
    }
}
```

### <a name="inheritance"></a><span data-ttu-id="0804f-445">Héritage</span><span class="sxs-lookup"><span data-stu-id="0804f-445">Inheritance</span></span>

<span data-ttu-id="0804f-446">Une classe ***hérite*** des membres de son type de classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-446">A class ***inherits*** the members of its direct base class type.</span></span> <span data-ttu-id="0804f-447">L’héritage signifie qu’une classe contient implicitement tous les membres de son type de classe de base directe, à l’exception des constructeurs d’instance, des destructeurs et des constructeurs statiques de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-447">Inheritance means that a class implicitly contains all members of its direct base class type, except for the instance constructors, destructors and static constructors of the base class.</span></span> <span data-ttu-id="0804f-448">Voici quelques aspects importants de l’héritage :</span><span class="sxs-lookup"><span data-stu-id="0804f-448">Some important aspects of inheritance are:</span></span>

*  <span data-ttu-id="0804f-449">L’héritage est transitif.</span><span class="sxs-lookup"><span data-stu-id="0804f-449">Inheritance is transitive.</span></span> <span data-ttu-id="0804f-450">Si `C` est dérivé de `B`, et `B` est dérivé de `A`, `C` hérite des membres déclarés dans `B` , ainsi que des membres déclarés `A`dans.</span><span class="sxs-lookup"><span data-stu-id="0804f-450">If `C` is derived from `B`, and `B` is derived from `A`, then `C` inherits the members declared in `B` as well as the members declared in `A`.</span></span>
*  <span data-ttu-id="0804f-451">Une classe dérivée étend sa classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-451">A derived class extends its direct base class.</span></span> <span data-ttu-id="0804f-452">Une classe dérivée peut ajouter des membres hérités, mais ne peut pas supprimer la définition d’un membre hérité.</span><span class="sxs-lookup"><span data-stu-id="0804f-452">A derived class can add new members to those it inherits, but it cannot remove the definition of an inherited member.</span></span>
*  <span data-ttu-id="0804f-453">Les constructeurs d’instance, les destructeurs et les constructeurs statiques ne sont pas hérités, mais tous les autres membres sont, indépendamment de leur accessibilité déclarée ([accès aux membres](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-453">Instance constructors, destructors, and static constructors are not inherited, but all other members are, regardless of their declared accessibility ([Member access](basic-concepts.md#member-access)).</span></span> <span data-ttu-id="0804f-454">Toutefois, selon l’accessibilité déclarée, les membres hérités peuvent ne pas être accessibles dans une classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="0804f-454">However, depending on their declared accessibility, inherited members might not be accessible in a derived class.</span></span>
*  <span data-ttu-id="0804f-455">Une classe dérivée peut ***Masquer*** les membres hérités ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)) en déclarant de nouveaux membres portant le même nom ou la même signature.</span><span class="sxs-lookup"><span data-stu-id="0804f-455">A derived class can ***hide*** ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)) inherited members by declaring new members with the same name or signature.</span></span> <span data-ttu-id="0804f-456">Notez cependant que le masquage d’un membre hérité ne supprime pas ce membre ; il rend simplement ce membre inaccessible directement via la classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="0804f-456">Note however that hiding an inherited member does not remove that member—it merely makes that member inaccessible directly through the derived class.</span></span>
*  <span data-ttu-id="0804f-457">Une instance d’une classe contient un ensemble de tous les champs d’instance déclarés dans la classe et ses classes de base, et une conversion implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe d’un type de classe dérivée vers l’un de ses types de classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-457">An instance of a class contains a set of all instance fields declared in the class and its base classes, and an implicit conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from a derived class type to any of its base class types.</span></span> <span data-ttu-id="0804f-458">Ainsi, une référence à une instance d’une classe dérivée peut être traitée comme une référence à une instance de l’une de ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-458">Thus, a reference to an instance of some derived class can be treated as a reference to an instance of any of its base classes.</span></span>
*  <span data-ttu-id="0804f-459">Une classe peut déclarer des méthodes, des propriétés et des indexeurs virtuels, et les classes dérivées peuvent substituer l’implémentation de ces membres de fonction.</span><span class="sxs-lookup"><span data-stu-id="0804f-459">A class can declare virtual methods, properties, and indexers, and derived classes can override the implementation of these function members.</span></span> <span data-ttu-id="0804f-460">Cela permet aux classes d’exposer un comportement polymorphe dans lequel les actions effectuées par un appel de membre de fonction varient en fonction du type d’exécution de l’instance par le biais duquel cette fonction membre est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-460">This enables classes to exhibit polymorphic behavior wherein the actions performed by a function member invocation varies depending on the run-time type of the instance through which that function member is invoked.</span></span>

<span data-ttu-id="0804f-461">Le membre hérité d’un type de classe construit sont les membres du type de classe de base immédiat ([classes de base](classes.md#base-classes)), qui est trouvé en substituant les arguments de type du type construit pour chaque occurrence des paramètres de type correspondants dans le  *spécification class_base* .</span><span class="sxs-lookup"><span data-stu-id="0804f-461">The inherited member of a constructed class type are the members of the immediate base class type ([Base classes](classes.md#base-classes)), which is found by substituting the type arguments of the constructed type for each occurrence of the corresponding type parameters in the *class_base* specification.</span></span> <span data-ttu-id="0804f-462">Ces membres, à leur tour, sont transformés en substituant, pour chaque *type_parameter* dans la déclaration de membre, les *type_argument* correspondants de la spécification *class_base* .</span><span class="sxs-lookup"><span data-stu-id="0804f-462">These members, in turn, are transformed by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the *class_base* specification.</span></span>

```csharp
class B<U>
{
    public U F(long index) {...}
}

class D<T>: B<T[]>
{
    public T G(string s) {...}
}
```

<span data-ttu-id="0804f-463">Dans l’exemple ci-dessus, le `D<int>` type construit a un membre `public int G(string s)` non hérité obtenu en substituant l’argument `int` de type pour le `T`paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-463">In the above example, the constructed type `D<int>` has a non-inherited member `public int G(string s)` obtained by substituting the type argument `int` for the type parameter `T`.</span></span> <span data-ttu-id="0804f-464">`D<int>`a également un membre hérité de la Déclaration `B`de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-464">`D<int>` also has an inherited member from the class declaration `B`.</span></span> <span data-ttu-id="0804f-465">Ce membre hérité est déterminé par la première `B<int[]>` détermination du type de classe de base de `int` `D<int>` en `T` remplaçant dans la spécification `B<T[]>`de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-465">This inherited member is determined by first determining the base class type `B<int[]>` of `D<int>` by substituting `int` for `T` in the base class specification `B<T[]>`.</span></span> <span data-ttu-id="0804f-466">Ensuite, en tant qu’argument de `B`type `int[]` pour, est substitué `U` à `public U F(long index)`dans, ce qui donne le `public int[] F(long index)`membre hérité.</span><span class="sxs-lookup"><span data-stu-id="0804f-466">Then, as a type argument to `B`, `int[]` is substituted for `U` in `public U F(long index)`, yielding the inherited member `public int[] F(long index)`.</span></span>

### <a name="the-new-modifier"></a><span data-ttu-id="0804f-467">Le nouveau modificateur</span><span class="sxs-lookup"><span data-stu-id="0804f-467">The new modifier</span></span>

<span data-ttu-id="0804f-468">Un *class_member_declaration* est autorisé à déclarer un membre avec le même nom ou la même signature qu’un membre hérité.</span><span class="sxs-lookup"><span data-stu-id="0804f-468">A *class_member_declaration* is permitted to declare a member with the same name or signature as an inherited member.</span></span> <span data-ttu-id="0804f-469">Dans ce cas, le membre de la classe dérivée est dit de ***Masquer*** le membre de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-469">When this occurs, the derived class member is said to ***hide*** the base class member.</span></span> <span data-ttu-id="0804f-470">Le masquage d’un membre hérité n’est pas considéré comme une erreur, mais il entraîne l’émission d’un avertissement par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-470">Hiding an inherited member is not considered an error, but it does cause the compiler to issue a warning.</span></span> <span data-ttu-id="0804f-471">Pour supprimer l’avertissement, la déclaration du membre de classe dérivée peut inclure `new` un modificateur pour indiquer que le membre dérivé est destiné à masquer le membre de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-471">To suppress the warning, the declaration of the derived class member can include a `new` modifier to indicate that the derived member is intended to hide the base member.</span></span> <span data-ttu-id="0804f-472">Cette rubrique est traitée plus en détail dans [Masquer par héritage](basic-concepts.md#hiding-through-inheritance).</span><span class="sxs-lookup"><span data-stu-id="0804f-472">This topic is discussed further in [Hiding through inheritance](basic-concepts.md#hiding-through-inheritance).</span></span>

<span data-ttu-id="0804f-473">Si un `new` modificateur est inclus dans une déclaration qui ne masque pas un membre hérité, un avertissement est émis à cet effet.</span><span class="sxs-lookup"><span data-stu-id="0804f-473">If a `new` modifier is included in a declaration that doesn't hide an inherited member, a warning to that effect is issued.</span></span> <span data-ttu-id="0804f-474">Cet avertissement est supprimé en supprimant le `new` modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-474">This warning is suppressed by removing the `new` modifier.</span></span>

### <a name="access-modifiers"></a><span data-ttu-id="0804f-475">Modificateurs d’accès</span><span class="sxs-lookup"><span data-stu-id="0804f-475">Access modifiers</span></span>

<span data-ttu-id="0804f-476">Un *class_member_declaration* peut avoir l’un des cinq types possibles d’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) : `public`, `protected internal`, `protected`, `internal` ou `private`.</span><span class="sxs-lookup"><span data-stu-id="0804f-476">A *class_member_declaration* can have any one of the five possible kinds of declared accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)): `public`, `protected internal`, `protected`, `internal`, or `private`.</span></span> <span data-ttu-id="0804f-477">À l’exception `protected internal` de la combinaison, il s’agit d’une erreur de compilation pour spécifier plusieurs modificateurs d’accès.</span><span class="sxs-lookup"><span data-stu-id="0804f-477">Except for the `protected internal` combination, it is a compile-time error to specify more than one access modifier.</span></span> <span data-ttu-id="0804f-478">Quand un *class_member_declaration* n’inclut pas de modificateur d’accès, `private` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0804f-478">When a *class_member_declaration* does not include any access modifiers, `private` is assumed.</span></span>

### <a name="constituent-types"></a><span data-ttu-id="0804f-479">Types constitutifs</span><span class="sxs-lookup"><span data-stu-id="0804f-479">Constituent types</span></span>

<span data-ttu-id="0804f-480">Les types qui sont utilisés dans la déclaration d’un membre sont appelés types constitutifs de ce membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-480">Types that are used in the declaration of a member are called the constituent types of that member.</span></span> <span data-ttu-id="0804f-481">Les types constitutifs possibles sont le type d’une constante, d’un champ, d’une propriété, d’un événement ou d’un indexeur, le type de retour d’une méthode ou d’un opérateur, ainsi que les types de paramètres d’une méthode, d’un indexeur, d’un opérateur ou d’un constructeur d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-481">Possible constituent types are the type of a constant, field, property, event, or indexer, the return type of a method or operator, and the parameter types of a method, indexer, operator, or instance constructor.</span></span> <span data-ttu-id="0804f-482">Les types constitutifs d’un membre doivent être au moins aussi accessibles que ce membre lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-482">The constituent types of a member must be at least as accessible as that member itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

### <a name="static-and-instance-members"></a><span data-ttu-id="0804f-483">Membres statiques et d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-483">Static and instance members</span></span>

<span data-ttu-id="0804f-484">Les membres d’une classe sont soit des ***membres statiques*** , soit des ***membres d’instance***.</span><span class="sxs-lookup"><span data-stu-id="0804f-484">Members of a class are either ***static members*** or ***instance members***.</span></span> <span data-ttu-id="0804f-485">En général, il est utile de considérer que les membres statiques appartiennent à des types de classe et des membres d’instance comme appartenant à des objets (instances de types de classe).</span><span class="sxs-lookup"><span data-stu-id="0804f-485">Generally speaking, it is useful to think of static members as belonging to class types and instance members as belonging to objects (instances of class types).</span></span>

<span data-ttu-id="0804f-486">Lorsqu’une déclaration de champ, de méthode, de propriété, d’événement, d’opérateur `static` ou de constructeur comprend un modificateur, elle déclare un membre statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-486">When a field, method, property, event, operator, or constructor declaration includes a `static` modifier, it declares a static member.</span></span> <span data-ttu-id="0804f-487">En outre, une déclaration de constante ou de type déclare implicitement un membre statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-487">In addition, a constant or type declaration implicitly declares a static member.</span></span> <span data-ttu-id="0804f-488">Les membres statiques présentent les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-488">Static members have the following characteristics:</span></span>

*  <span data-ttu-id="0804f-489">Lorsqu’un membre statique `M` est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, `E` doit désigner un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-489">When a static member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote a type containing `M`.</span></span> <span data-ttu-id="0804f-490">Il s’agit d’une erreur au moment `E` de la compilation pour qu’une instance soit dénotée.</span><span class="sxs-lookup"><span data-stu-id="0804f-490">It is a compile-time error for `E` to denote an instance.</span></span>
*  <span data-ttu-id="0804f-491">Un champ statique identifie exactement un emplacement de stockage à partager par toutes les instances d’un type de classe fermé donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-491">A static field identifies exactly one storage location to be shared by all instances of a given closed class type.</span></span> <span data-ttu-id="0804f-492">Quel que soit le nombre d’instances d’un type de classe fermé donné, il n’existe qu’une seule copie d’un champ statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-492">No matter how many instances of a given closed class type are created, there is only ever one copy of a static field.</span></span>
*  <span data-ttu-id="0804f-493">Un membre de fonction statique (méthode, propriété, événement, opérateur ou constructeur) ne fonctionne pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire `this` référence à dans ce type de membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="0804f-493">A static function member (method, property, event, operator, or constructor) does not operate on a specific instance, and it is a compile-time error to refer to `this` in such a function member.</span></span>

<span data-ttu-id="0804f-494">Quand un champ, une méthode, une propriété, un événement, un indexeur, un constructeur ou une déclaration de `static` destructeur n’inclut pas de modificateur, il déclare un membre d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-494">When a field, method, property, event, indexer, constructor, or destructor declaration does not include a `static` modifier, it declares an instance member.</span></span> <span data-ttu-id="0804f-495">(Un membre d’instance est parfois appelé membre non statique.) Les membres d’instance présentent les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-495">(An instance member is sometimes called a non-static member.) Instance members have the following characteristics:</span></span>

*  <span data-ttu-id="0804f-496">Lorsqu’un membre d’instance `M` est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, `E` doit désigner une instance d’un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-496">When an instance member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote an instance of a type containing `M`.</span></span> <span data-ttu-id="0804f-497">Il s’agit d’une erreur de liaison `E` au moment de la liaison pour indiquer un type.</span><span class="sxs-lookup"><span data-stu-id="0804f-497">It is a binding-time error for `E` to denote a type.</span></span>
*  <span data-ttu-id="0804f-498">Chaque instance d’une classe contient un ensemble distinct de tous les champs d’instance de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-498">Every instance of a class contains a separate set of all instance fields of the class.</span></span>
*  <span data-ttu-id="0804f-499">Un membre de fonction d’instance (méthode, propriété, indexeur, constructeur d’instance ou destructeur) opère sur une instance donnée de la classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-499">An instance function member (method, property, indexer, instance constructor, or destructor) operates on a given instance of the class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="0804f-500">L’exemple suivant illustre les règles d’accès aux membres statiques et d’instance :</span><span class="sxs-lookup"><span data-stu-id="0804f-500">The following example illustrates the rules for accessing static and instance members:</span></span>
```csharp
class Test
{
    int x;
    static int y;

    void F() {
        x = 1;            // Ok, same as this.x = 1
        y = 1;            // Ok, same as Test.y = 1
    }

    static void G() {
        x = 1;            // Error, cannot access this.x
        y = 1;            // Ok, same as Test.y = 1
    }

    static void Main() {
        Test t = new Test();
        t.x = 1;          // Ok
        t.y = 1;          // Error, cannot access static member through instance
        Test.x = 1;       // Error, cannot access instance member through type
        Test.y = 1;       // Ok
    }
}
```

<span data-ttu-id="0804f-501">La méthode `F` montre que dans une fonction membre d’instance, un *simple_name* ([noms simples](expressions.md#simple-names)) peut être utilisé pour accéder aux membres d’instance et aux membres statiques.</span><span class="sxs-lookup"><span data-stu-id="0804f-501">The `F` method shows that in an instance function member, a *simple_name* ([Simple names](expressions.md#simple-names)) can be used to access both instance members and static members.</span></span> <span data-ttu-id="0804f-502">La méthode `G` montre que dans une fonction membre statique, il s’agit d’une erreur de compilation pour accéder à un membre d’instance via un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="0804f-502">The `G` method shows that in a static function member, it is a compile-time error to access an instance member through a *simple_name*.</span></span> <span data-ttu-id="0804f-503">La méthode `Main` indique que dans un *member_access* ([accès aux membres](expressions.md#member-access)), les membres d’instance doivent être accessibles via des instances et les membres statiques doivent être accessibles par le biais de types.</span><span class="sxs-lookup"><span data-stu-id="0804f-503">The `Main` method shows that in a *member_access* ([Member access](expressions.md#member-access)), instance members must be accessed through instances, and static members must be accessed through types.</span></span>

### <a name="nested-types"></a><span data-ttu-id="0804f-504">Types imbriqués</span><span class="sxs-lookup"><span data-stu-id="0804f-504">Nested types</span></span>

<span data-ttu-id="0804f-505">Un type déclaré dans une déclaration de classe ou de struct est appelé un ***type imbriqué***.</span><span class="sxs-lookup"><span data-stu-id="0804f-505">A type declared within a class or struct declaration is called a ***nested type***.</span></span> <span data-ttu-id="0804f-506">Un type déclaré dans une unité de compilation ou un espace de noms est appelé ***type non imbriqué***.</span><span class="sxs-lookup"><span data-stu-id="0804f-506">A type that is declared within a compilation unit or namespace is called a ***non-nested type***.</span></span>

<span data-ttu-id="0804f-507">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-507">In the example</span></span>
```csharp
using System;

class A
{
    class B
    {
        static void F() {
            Console.WriteLine("A.B.F");
        }
    }
}
```
<span data-ttu-id="0804f-508">la `B` classe est un type imbriqué, car elle est déclarée `A`dans la classe `A` , et la classe est un type non imbriqué, car elle est déclarée dans une unité de compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-508">class `B` is a nested type because it is declared within class `A`, and class `A` is a non-nested type because it is declared within a compilation unit.</span></span>

#### <a name="fully-qualified-name"></a><span data-ttu-id="0804f-509">Nom complet</span><span class="sxs-lookup"><span data-stu-id="0804f-509">Fully qualified name</span></span>

<span data-ttu-id="0804f-510">Le nom qualifié complet ([noms qualifiés complets](basic-concepts.md#fully-qualified-names)) pour un type imbriqué est `S.N` où `S` est le nom qualifié complet du type dans lequel le type `N` est déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-510">The fully qualified name ([Fully qualified names](basic-concepts.md#fully-qualified-names)) for a nested type is `S.N` where `S` is the fully qualified name of the type in which type `N` is declared.</span></span>

#### <a name="declared-accessibility"></a><span data-ttu-id="0804f-511">Accessibilité déclarée</span><span class="sxs-lookup"><span data-stu-id="0804f-511">Declared accessibility</span></span>

<span data-ttu-id="0804f-512">Les types non imbriqués peuvent avoir `public` ou `internal` déclarés l’accessibilité `internal` et avoir déclaré l’accessibilité par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-512">Non-nested types can have `public` or `internal` declared accessibility and have `internal` declared accessibility by default.</span></span> <span data-ttu-id="0804f-513">Les types imbriqués peuvent également avoir ces formes d’accessibilité déclarée, plus une ou plusieurs formes supplémentaires d’accessibilité déclarée, selon que le type conteneur est une classe ou un struct :</span><span class="sxs-lookup"><span data-stu-id="0804f-513">Nested types can have these forms of declared accessibility too, plus one or more additional forms of declared accessibility, depending on whether the containing type is a class or struct:</span></span>

*  <span data-ttu-id="0804f-514">Un type imbriqué qui est déclaré dans une classe peut avoir l’une des cinq formes d’accessibilité déclarée`public`( `protected internal`, `protected`, `internal`, ou `private`) et, comme les autres membres de classe, la `private` valeur par défaut est déclarée aux.</span><span class="sxs-lookup"><span data-stu-id="0804f-514">A nested type that is declared in a class can have any of five forms of declared accessibility (`public`, `protected internal`, `protected`, `internal`, or `private`) and, like other class members, defaults to `private` declared accessibility.</span></span>
*  <span data-ttu-id="0804f-515">Un type imbriqué qui est déclaré dans un struct peut avoir l’une des trois formes d’accessibilité déclarée`public`( `internal`, ou `private`) et, `private` comme les autres membres de struct, a comme valeur par défaut l’accessibilité déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-515">A nested type that is declared in a struct can have any of three forms of declared accessibility (`public`, `internal`, or `private`) and, like other struct members, defaults to `private` declared accessibility.</span></span>

<span data-ttu-id="0804f-516">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-516">The example</span></span>
```csharp
public class List
{
    // Private data structure
    private class Node
    { 
        public object Data;
        public Node Next;

        public Node(object data, Node next) {
            this.Data = data;
            this.Next = next;
        }
    }

    private Node first = null;
    private Node last = null;

    // Public interface
    public void AddToFront(object o) {...}
    public void AddToBack(object o) {...}
    public object RemoveFromFront() {...}
    public object RemoveFromBack() {...}
    public int Count { get {...} }
}
```
<span data-ttu-id="0804f-517">déclare une classe `Node`imbriquée privée.</span><span class="sxs-lookup"><span data-stu-id="0804f-517">declares a private nested class `Node`.</span></span>

#### <a name="hiding"></a><span data-ttu-id="0804f-518">Masquage</span><span class="sxs-lookup"><span data-stu-id="0804f-518">Hiding</span></span>

<span data-ttu-id="0804f-519">Un type imbriqué peut masquer ([nom masquant](basic-concepts.md#name-hiding)) un membre de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-519">A nested type may hide ([Name hiding](basic-concepts.md#name-hiding)) a base member.</span></span> <span data-ttu-id="0804f-520">Le `new` modificateur est autorisé sur les déclarations de type imbriqué afin que le masquage puisse être exprimé explicitement.</span><span class="sxs-lookup"><span data-stu-id="0804f-520">The `new` modifier is permitted on nested type declarations so that hiding can be expressed explicitly.</span></span> <span data-ttu-id="0804f-521">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-521">The example</span></span>
```csharp
using System;

class Base
{
    public static void M() {
        Console.WriteLine("Base.M");
    }
}

class Derived: Base 
{
    new public class M 
    {
        public static void F() {
            Console.WriteLine("Derived.M.F");
        }
    }
}

class Test 
{
    static void Main() {
        Derived.M.F();
    }
}
```
<span data-ttu-id="0804f-522">affiche une classe `M` imbriquée qui masque la méthode `M` définie dans `Base`.</span><span class="sxs-lookup"><span data-stu-id="0804f-522">shows a nested class `M` that hides the method `M` defined in `Base`.</span></span>

#### <a name="this-access"></a><span data-ttu-id="0804f-523">Cet accès</span><span class="sxs-lookup"><span data-stu-id="0804f-523">this access</span></span>

<span data-ttu-id="0804f-524">Un type imbriqué et son type conteneur n’ont pas de relation spéciale en ce qui concerne *This_Access* ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-524">A nested type and its containing type do not have a special relationship with regard to *this_access* ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="0804f-525">En particulier `this` , dans un type imbriqué ne peut pas être utilisé pour faire référence aux membres d’instance du type conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-525">Specifically, `this` within a nested type cannot be used to refer to instance members of the containing type.</span></span> <span data-ttu-id="0804f-526">Dans les cas où un type imbriqué doit accéder aux membres de l’instance de son type conteneur, l’accès peut être fourni en `this` fournissant le pour l’instance du type conteneur comme argument de constructeur pour le type imbriqué.</span><span class="sxs-lookup"><span data-stu-id="0804f-526">In cases where a nested type needs access to the instance members of its containing type, access can be provided by providing the `this` for the instance of the containing type as a constructor argument for the nested type.</span></span> <span data-ttu-id="0804f-527">L’exemple suivant</span><span class="sxs-lookup"><span data-stu-id="0804f-527">The following example</span></span>
```csharp
using System;

class C
{
    int i = 123;

    public void F() {
        Nested n = new Nested(this);
        n.G();
    }

    public class Nested
    {
        C this_c;

        public Nested(C c) {
            this_c = c;
        }

        public void G() {
            Console.WriteLine(this_c.i);
        }
    }
}

class Test
{
    static void Main() {
        C c = new C();
        c.F();
    }
}
```
<span data-ttu-id="0804f-528">illustre cette technique.</span><span class="sxs-lookup"><span data-stu-id="0804f-528">shows this technique.</span></span> <span data-ttu-id="0804f-529">Une instance de `C` crée une instance de `Nested` et passe son propre `this` au `Nested`constructeur de pour fournir l’accès ultérieur aux `C`membres d’instance de.</span><span class="sxs-lookup"><span data-stu-id="0804f-529">An instance of `C` creates an instance of `Nested` and passes its own `this` to `Nested`'s constructor in order to provide subsequent access to `C`'s instance members.</span></span>

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a><span data-ttu-id="0804f-530">Accès aux membres privés et protégés du type conteneur</span><span class="sxs-lookup"><span data-stu-id="0804f-530">Access to private and protected members of the containing type</span></span>

<span data-ttu-id="0804f-531">Un type imbriqué a accès à tous les membres qui sont accessibles à son type conteneur, y compris les membres du type conteneur qui ont `private` et `protected` l’accessibilité déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-531">A nested type has access to all of the members that are accessible to its containing type, including members of the containing type that have `private` and `protected` declared accessibility.</span></span> <span data-ttu-id="0804f-532">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-532">The example</span></span>
```csharp
using System;

class C 
{
    private static void F() {
        Console.WriteLine("C.F");
    }

    public class Nested 
    {
        public static void G() {
            F();
        }
    }
}

class Test 
{
    static void Main() {
        C.Nested.G();
    }
}
```
<span data-ttu-id="0804f-533">affiche une classe `C` qui contient une classe `Nested`imbriquée.</span><span class="sxs-lookup"><span data-stu-id="0804f-533">shows a class `C` that contains a nested class `Nested`.</span></span> <span data-ttu-id="0804f-534">Dans `Nested`, la méthode `G` appelle la méthode `F` statique définie dans `C`et `F` a une accessibilité déclarée privée.</span><span class="sxs-lookup"><span data-stu-id="0804f-534">Within `Nested`, the method `G` calls the static method `F` defined in `C`, and `F` has private declared accessibility.</span></span>

<span data-ttu-id="0804f-535">Un type imbriqué peut également accéder à des membres protégés définis dans un type de base de son type conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-535">A nested type also may access protected members defined in a base type of its containing type.</span></span> <span data-ttu-id="0804f-536">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-536">In the example</span></span>
```csharp
using System;

class Base 
{
    protected void F() {
        Console.WriteLine("Base.F");
    }
}

class Derived: Base 
{
    public class Nested 
    {
        public void G() {
            Derived d = new Derived();
            d.F();        // ok
        }
    }
}

class Test 
{
    static void Main() {
        Derived.Nested n = new Derived.Nested();
        n.G();
    }
}
```
<span data-ttu-id="0804f-537">la `Derived.Nested` classe imbriquée accède à la méthode `Derived` `F` protégée définie `Base`dans la classe de base de,, en appelant par le `Derived`biais d’une instance de.</span><span class="sxs-lookup"><span data-stu-id="0804f-537">the nested class `Derived.Nested` accesses the protected method `F` defined in `Derived`'s base class, `Base`, by calling through an instance of `Derived`.</span></span>

#### <a name="nested-types-in-generic-classes"></a><span data-ttu-id="0804f-538">Types imbriqués dans les classes génériques</span><span class="sxs-lookup"><span data-stu-id="0804f-538">Nested types in generic classes</span></span>

<span data-ttu-id="0804f-539">Une déclaration de classe générique peut contenir des déclarations de type imbriqué.</span><span class="sxs-lookup"><span data-stu-id="0804f-539">A generic class declaration can contain nested type declarations.</span></span> <span data-ttu-id="0804f-540">Les paramètres de type de la classe englobante peuvent être utilisés dans les types imbriqués.</span><span class="sxs-lookup"><span data-stu-id="0804f-540">The type parameters of the enclosing class can be used within the nested types.</span></span> <span data-ttu-id="0804f-541">Une déclaration de type imbriquée peut contenir des paramètres de type supplémentaires qui s’appliquent uniquement au type imbriqué.</span><span class="sxs-lookup"><span data-stu-id="0804f-541">A nested type declaration can contain additional type parameters that apply only to the nested type.</span></span>

<span data-ttu-id="0804f-542">Chaque déclaration de type contenue dans une déclaration de classe générique est implicitement une déclaration de type générique.</span><span class="sxs-lookup"><span data-stu-id="0804f-542">Every type declaration contained within a generic class declaration is implicitly a generic type declaration.</span></span> <span data-ttu-id="0804f-543">Lors de l’écriture d’une référence à un type imbriqué dans un type générique, le type construit contenant, y compris ses arguments de type, doit être nommé.</span><span class="sxs-lookup"><span data-stu-id="0804f-543">When writing a reference to a type nested within a generic type, the containing constructed type, including its type arguments, must be named.</span></span> <span data-ttu-id="0804f-544">Toutefois, à partir de la classe externe, le type imbriqué peut être utilisé sans qualification ; le type d’instance de la classe externe peut être utilisé implicitement lors de la construction du type imbriqué.</span><span class="sxs-lookup"><span data-stu-id="0804f-544">However, from within the outer class, the nested type can be used without qualification; the instance type of the outer class can be implicitly used when constructing the nested type.</span></span> <span data-ttu-id="0804f-545">L’exemple suivant montre trois façons correctes de faire référence à un type construit créé `Inner`à partir de ; les deux premiers sont équivalents :</span><span class="sxs-lookup"><span data-stu-id="0804f-545">The following example shows three different correct ways to refer to a constructed type created from `Inner`; the first two are equivalent:</span></span>
```csharp
class Outer<T>
{
    class Inner<U>
    {
        public static void F(T t, U u) {...}
    }

    static void F(T t) {
        Outer<T>.Inner<string>.F(t, "abc");      // These two statements have
        Inner<string>.F(t, "abc");               // the same effect

        Outer<int>.Inner<string>.F(3, "abc");    // This type is different

        Outer.Inner<string>.F(t, "abc");         // Error, Outer needs type arg
    }
}
```

<span data-ttu-id="0804f-546">Bien qu’il s’agisse d’un style de programmation incorrect, un paramètre de type dans un type imbriqué peut masquer un membre ou un paramètre de type déclaré dans le type externe :</span><span class="sxs-lookup"><span data-stu-id="0804f-546">Although it is bad programming style, a type parameter in a nested type can hide a member or type parameter declared in the outer type:</span></span>
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a><span data-ttu-id="0804f-547">Noms de membres réservés</span><span class="sxs-lookup"><span data-stu-id="0804f-547">Reserved member names</span></span>

<span data-ttu-id="0804f-548">Pour faciliter l’implémentation C# sous-jacente au moment de l’exécution, pour chaque déclaration de membre source qui est une propriété, un événement ou un indexeur, l’implémentation doit réserver deux signatures de méthode selon le type de la déclaration de membre, son nom et son type.</span><span class="sxs-lookup"><span data-stu-id="0804f-548">To facilitate the underlying C# run-time implementation, for each source member declaration that is a property, event, or indexer, the implementation must reserve two method signatures based on the kind of the member declaration, its name, and its type.</span></span> <span data-ttu-id="0804f-549">Il s’agit d’une erreur de compilation pour qu’un programme déclare un membre dont la signature correspond à l’une de ces signatures réservées, même si l’implémentation au moment de l’exécution sous-jacente n’utilise pas ces réservations.</span><span class="sxs-lookup"><span data-stu-id="0804f-549">It is a compile-time error for a program to declare a member whose signature matches one of these reserved signatures, even if the underlying run-time implementation does not make use of these reservations.</span></span>

<span data-ttu-id="0804f-550">Les noms réservés n’introduisent pas de déclarations, donc ils ne participent pas à la recherche de membres.</span><span class="sxs-lookup"><span data-stu-id="0804f-550">The reserved names do not introduce declarations, thus they do not participate in member lookup.</span></span> <span data-ttu-id="0804f-551">Toutefois, les signatures de méthode réservée associées à une déclaration participent à l’héritage ([héritage](classes.md#inheritance)) et peuvent être masquées avec le `new` modificateur ([le nouveau modificateur](classes.md#the-new-modifier)).</span><span class="sxs-lookup"><span data-stu-id="0804f-551">However, a declaration's associated reserved method signatures do participate in inheritance ([Inheritance](classes.md#inheritance)), and can be hidden with the `new` modifier ([The new modifier](classes.md#the-new-modifier)).</span></span>

<span data-ttu-id="0804f-552">La réservation de ces noms remplit trois fonctions :</span><span class="sxs-lookup"><span data-stu-id="0804f-552">The reservation of these names serves three purposes:</span></span>

*  <span data-ttu-id="0804f-553">Pour permettre à l’implémentation sous-jacente d’utiliser un identificateur ordinaire comme nom de méthode pour obtenir ou définir l' C# accès à la fonctionnalité de langage.</span><span class="sxs-lookup"><span data-stu-id="0804f-553">To allow the underlying implementation to use an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="0804f-554">Pour permettre à d’autres langages d’interagir à l’aide d’un identificateur ordinaire comme nom de méthode pour obtenir ou C# définir l’accès à la fonctionnalité de langage.</span><span class="sxs-lookup"><span data-stu-id="0804f-554">To allow other languages to interoperate using an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="0804f-555">Pour garantir que la source acceptée par un compilateur conforme est acceptée par un autre, en rendant les détails des noms de membres réservés cohérents dans toutes les C# implémentations.</span><span class="sxs-lookup"><span data-stu-id="0804f-555">To help ensure that the source accepted by one conforming compiler is accepted by another, by making the specifics of reserved member names consistent across all C# implementations.</span></span>

<span data-ttu-id="0804f-556">La déclaration d’un destructeur ([destructeurs](classes.md#destructors)) entraîne également la réservation d’une signature ([noms de membres réservés aux destructeurs](classes.md#member-names-reserved-for-destructors)).</span><span class="sxs-lookup"><span data-stu-id="0804f-556">The declaration of a destructor ([Destructors](classes.md#destructors)) also causes a signature to be reserved ([Member names reserved for destructors](classes.md#member-names-reserved-for-destructors)).</span></span>

#### <a name="member-names-reserved-for-properties"></a><span data-ttu-id="0804f-557">Noms de membres réservés pour les propriétés</span><span class="sxs-lookup"><span data-stu-id="0804f-557">Member names reserved for properties</span></span>

<span data-ttu-id="0804f-558">Pour une propriété `P` ([Propriétés](classes.md#properties)) de type `T`, les signatures suivantes sont réservées :</span><span class="sxs-lookup"><span data-stu-id="0804f-558">For a property `P` ([Properties](classes.md#properties)) of type `T`, the following signatures are reserved:</span></span>

```csharp
T get_P();
void set_P(T value);
```

<span data-ttu-id="0804f-559">Les deux signatures sont réservées, même si la propriété est en lecture seule ou en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="0804f-559">Both signatures are reserved, even if the property is read-only or write-only.</span></span>

<span data-ttu-id="0804f-560">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-560">In the example</span></span>
```csharp
using System;

class A
{
    public int P {
        get { return 123; }
    }
}

class B: A
{
    new public int get_P() {
        return 456;
    }

    new public void set_P(int value) {
    }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        Console.WriteLine(a.P);
        Console.WriteLine(b.P);
        Console.WriteLine(b.get_P());
    }
}
```
<span data-ttu-id="0804f-561">une classe `A` définit une propriété `P`en lecture seule, en réservant des signatures `get_P` pour `set_P` les méthodes et.</span><span class="sxs-lookup"><span data-stu-id="0804f-561">a class `A` defines a read-only property `P`, thus reserving signatures for `get_P` and `set_P` methods.</span></span> <span data-ttu-id="0804f-562">Une classe `B` dérive `A` de et masque ces deux signatures réservées.</span><span class="sxs-lookup"><span data-stu-id="0804f-562">A class `B` derives from `A` and hides both of these reserved signatures.</span></span> <span data-ttu-id="0804f-563">L’exemple produit la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-563">The example produces the output:</span></span>
```console
123
123
456
```

#### <a name="member-names-reserved-for-events"></a><span data-ttu-id="0804f-564">Noms de membres réservés pour les événements</span><span class="sxs-lookup"><span data-stu-id="0804f-564">Member names reserved for events</span></span>

<span data-ttu-id="0804f-565">Pour un événement `E` ([événements](classes.md#events)) de type `T`délégué, les signatures suivantes sont réservées :</span><span class="sxs-lookup"><span data-stu-id="0804f-565">For an event `E` ([Events](classes.md#events)) of delegate type `T`, the following signatures are reserved:</span></span>
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a><span data-ttu-id="0804f-566">Noms de membres réservés pour les indexeurs</span><span class="sxs-lookup"><span data-stu-id="0804f-566">Member names reserved for indexers</span></span>

<span data-ttu-id="0804f-567">Pour un indexeur ([indexeurs](classes.md#indexers)) de type `T` avec Parameter-List `L`, les signatures suivantes sont réservées :</span><span class="sxs-lookup"><span data-stu-id="0804f-567">For an indexer ([Indexers](classes.md#indexers)) of type `T` with parameter-list `L`, the following signatures are reserved:</span></span>
```csharp
T get_Item(L);
void set_Item(L, T value);
```

<span data-ttu-id="0804f-568">Les deux signatures sont réservées, même si l’indexeur est en lecture seule ou en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="0804f-568">Both signatures are reserved, even if the indexer is read-only or write-only.</span></span>

<span data-ttu-id="0804f-569">En outre, le `Item` nom du membre est réservé.</span><span class="sxs-lookup"><span data-stu-id="0804f-569">Furthermore the member name `Item` is reserved.</span></span>

#### <a name="member-names-reserved-for-destructors"></a><span data-ttu-id="0804f-570">Noms de membres réservés pour les destructeurs</span><span class="sxs-lookup"><span data-stu-id="0804f-570">Member names reserved for destructors</span></span>

<span data-ttu-id="0804f-571">Pour une classe contenant un destructeur ([destructeurs](classes.md#destructors)), la signature suivante est réservée :</span><span class="sxs-lookup"><span data-stu-id="0804f-571">For a class containing a destructor ([Destructors](classes.md#destructors)), the following signature is reserved:</span></span>
```csharp
void Finalize();
```

## <a name="constants"></a><span data-ttu-id="0804f-572">Constantes</span><span class="sxs-lookup"><span data-stu-id="0804f-572">Constants</span></span>

<span data-ttu-id="0804f-573">Une ***constante*** est un membre de classe qui représente une valeur de constante : valeur qui peut être calculée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-573">A ***constant*** is a class member that represents a constant value: a value that can be computed at compile-time.</span></span> <span data-ttu-id="0804f-574">Un *constant_declaration* introduit une ou plusieurs constantes d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-574">A *constant_declaration* introduces one or more constants of a given type.</span></span>

```antlr
constant_declaration
    : attributes? constant_modifier* 'const' type constant_declarators ';'
    ;

constant_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

<span data-ttu-id="0804f-575">Un *constant_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), un modificateur `new` ([le nouveau modificateur](classes.md#the-new-modifier)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-575">A *constant_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span> <span data-ttu-id="0804f-576">Les attributs et les modificateurs s’appliquent à tous les membres déclarés par *constant_declaration*.</span><span class="sxs-lookup"><span data-stu-id="0804f-576">The attributes and modifiers apply to all of the members declared by the *constant_declaration*.</span></span> <span data-ttu-id="0804f-577">Bien que les constantes soient considérées comme des membres statiques, un *constant_declaration* ne requiert pas et n’autorise pas un modificateur `static`.</span><span class="sxs-lookup"><span data-stu-id="0804f-577">Even though constants are considered static members, a *constant_declaration* neither requires nor allows a `static` modifier.</span></span> <span data-ttu-id="0804f-578">Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de constante.</span><span class="sxs-lookup"><span data-stu-id="0804f-578">It is an error for the same modifier to appear multiple times in a constant declaration.</span></span>

<span data-ttu-id="0804f-579">Le *type* d’un *constant_declaration* spécifie le type des membres introduits par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="0804f-579">The *type* of a *constant_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="0804f-580">Le type est suivi d’une liste de *constant_declarator*s, chacun d’entre eux introduisant un nouveau membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-580">The type is followed by a list of *constant_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="0804f-581">Un *constant_declarator* se compose d’un *identificateur* qui nomme le membre, suivi d’un jeton « `=` », suivi d’un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) qui donne la valeur du membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-581">A *constant_declarator* consists of an *identifier* that names the member, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the member.</span></span>

<span data-ttu-id="0804f-582">Le *type* spécifié dans une déclaration de constante doit être `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3, 4, *enum_type*ou *reference_ tapez*.</span><span class="sxs-lookup"><span data-stu-id="0804f-582">The *type* specified in a constant declaration must be `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, an *enum_type*, or a *reference_type*.</span></span> <span data-ttu-id="0804f-583">Chaque *constant_expression* doit produire une valeur du type cible ou d’un type qui peut être converti vers le type cible par une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="0804f-583">Each *constant_expression* must yield a value of the target type or of a type that can be converted to the target type by an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>

<span data-ttu-id="0804f-584">Le *type* d’une constante doit être au moins aussi accessible que la constante elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-584">The *type* of a constant must be at least as accessible as the constant itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-585">La valeur d’une constante est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-585">The value of a constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span>

<span data-ttu-id="0804f-586">Une constante peut elle-même participer à un *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="0804f-586">A constant can itself participate in a *constant_expression*.</span></span> <span data-ttu-id="0804f-587">Par conséquent, une constante peut être utilisée dans n’importe quelle construction qui requiert un *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="0804f-587">Thus, a constant may be used in any construct that requires a *constant_expression*.</span></span> <span data-ttu-id="0804f-588">Les exemples de ces constructions incluent `case` les étiquettes `goto case` , les `enum` instructions, les déclarations de membre, les attributs et d’autres déclarations de constante.</span><span class="sxs-lookup"><span data-stu-id="0804f-588">Examples of such constructs include `case` labels, `goto case` statements, `enum` member declarations, attributes, and other constant declarations.</span></span>

<span data-ttu-id="0804f-589">Comme décrit dans [expressions constantes](expressions.md#constant-expressions), un *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-589">As described in [Constant expressions](expressions.md#constant-expressions), a *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span> <span data-ttu-id="0804f-590">Étant donné que la seule façon de créer une valeur non null d’un *reference_type* autre que `string` consiste à appliquer l’opérateur `new`, et puisque l’opérateur `new` n’est pas autorisé dans un *constant_expression*, la seule valeur possible pour les constantes de  *les reference_types*autres que `string` sont `null`.</span><span class="sxs-lookup"><span data-stu-id="0804f-590">Since the only way to create a non-null value of a *reference_type* other than `string` is to apply the `new` operator, and since the `new` operator is not permitted in a *constant_expression*, the only possible value for constants of *reference_type*s other than `string` is `null`.</span></span>

<span data-ttu-id="0804f-591">Lorsqu’un nom symbolique pour une valeur constante est souhaité, mais lorsque le type de cette valeur n’est pas autorisé dans une déclaration de constante, ou lorsque la valeur ne peut pas être calculée au moment de la compilation par un *constant_expression*, un champ `readonly` ([champs en lecture seule](classes.md#readonly-fields)) peut à utiliser à la place.</span><span class="sxs-lookup"><span data-stu-id="0804f-591">When a symbolic name for a constant value is desired, but when the type of that value is not permitted in a constant declaration, or when the value cannot be computed at compile-time by a *constant_expression*, a `readonly` field ([Readonly fields](classes.md#readonly-fields)) may be used instead.</span></span>

<span data-ttu-id="0804f-592">Une déclaration de constante qui déclare plusieurs constantes équivaut à plusieurs déclarations de constantes uniques avec les mêmes attributs, modificateurs et type.</span><span class="sxs-lookup"><span data-stu-id="0804f-592">A constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="0804f-593">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-593">For example</span></span>
```csharp
class A
{
    public const double X = 1.0, Y = 2.0, Z = 3.0;
}
```
<span data-ttu-id="0804f-594">est équivalent à</span><span class="sxs-lookup"><span data-stu-id="0804f-594">is equivalent to</span></span>
```csharp
class A
{
    public const double X = 1.0;
    public const double Y = 2.0;
    public const double Z = 3.0;
}
```

<span data-ttu-id="0804f-595">Les constantes sont autorisées à dépendre d’autres constantes au sein du même programme tant que les dépendances ne sont pas de nature circulaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-595">Constants are permitted to depend on other constants within the same program as long as the dependencies are not of a circular nature.</span></span> <span data-ttu-id="0804f-596">Le compilateur s’arrange automatiquement pour évaluer les déclarations de constantes dans l’ordre approprié.</span><span class="sxs-lookup"><span data-stu-id="0804f-596">The compiler automatically arranges to evaluate the constant declarations in the appropriate order.</span></span> <span data-ttu-id="0804f-597">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-597">In the example</span></span>
```csharp
class A
{
    public const int X = B.Z + 1;
    public const int Y = 10;
}

class B
{
    public const int Z = A.Y + 1;
}
```
<span data-ttu-id="0804f-598">le compilateur `A.Y`évalue, puis `B.Z`évalue et évalue `A.X`Enfin, en produisant les valeurs `10`, `11`et `12`.</span><span class="sxs-lookup"><span data-stu-id="0804f-598">the compiler first evaluates `A.Y`, then evaluates `B.Z`, and finally evaluates `A.X`, producing the values `10`, `11`, and `12`.</span></span> <span data-ttu-id="0804f-599">Les déclarations de constante peuvent dépendre de constantes d’autres programmes, mais ces dépendances ne sont possibles que dans une seule direction.</span><span class="sxs-lookup"><span data-stu-id="0804f-599">Constant declarations may depend on constants from other programs, but such dependencies are only possible in one direction.</span></span> <span data-ttu-id="0804f-600">En `A` `B` `B.Z` `B.Z` vousréférantàl'`A.Y`exemple ci-dessus, si et ont été déclarés dans des programmes distincts, il serait possible quedépendede,maisnedépendaitpassimultanémentde.`A.X`</span><span class="sxs-lookup"><span data-stu-id="0804f-600">Referring to the example above, if `A` and `B` were declared in separate programs, it would be possible for `A.X` to depend on `B.Z`, but `B.Z` could then not simultaneously depend on `A.Y`.</span></span>

## <a name="fields"></a><span data-ttu-id="0804f-601">Champs</span><span class="sxs-lookup"><span data-stu-id="0804f-601">Fields</span></span>

<span data-ttu-id="0804f-602">Un ***champ*** est un membre qui représente une variable associée à un objet ou une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-602">A ***field*** is a member that represents a variable associated with an object or class.</span></span> <span data-ttu-id="0804f-603">Un *field_declaration* introduit un ou plusieurs champs d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-603">A *field_declaration* introduces one or more fields of a given type.</span></span>

```antlr
field_declaration
    : attributes? field_modifier* type variable_declarators ';'
    ;

field_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'readonly'
    | 'volatile'
    | field_modifier_unsafe
    ;

variable_declarators
    : variable_declarator (',' variable_declarator)*
    ;

variable_declarator
    : identifier ('=' variable_initializer)?
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

<span data-ttu-id="0804f-604">Un *field_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), un modificateur `new` ([le nouveau modificateur](classes.md#the-new-modifier)), une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) et un modificateur `static` ([ Champs d’instance et statiques](classes.md#static-and-instance-fields)).</span><span class="sxs-lookup"><span data-stu-id="0804f-604">A *field_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and a `static` modifier ([Static and instance fields](classes.md#static-and-instance-fields)).</span></span> <span data-ttu-id="0804f-605">En outre, un *field_declaration* peut inclure un modificateur `readonly` ([champs ReadOnly](classes.md#readonly-fields)) ou un modificateur `volatile` ([champs volatiles](classes.md#volatile-fields)), mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="0804f-605">In addition, a *field_declaration* may include a `readonly` modifier ([Readonly fields](classes.md#readonly-fields)) or a `volatile` modifier ([Volatile fields](classes.md#volatile-fields)) but not both.</span></span> <span data-ttu-id="0804f-606">Les attributs et les modificateurs s’appliquent à tous les membres déclarés par *field_declaration*.</span><span class="sxs-lookup"><span data-stu-id="0804f-606">The attributes and modifiers apply to all of the members declared by the *field_declaration*.</span></span> <span data-ttu-id="0804f-607">Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-607">It is an error for the same modifier to appear multiple times in a field declaration.</span></span>

<span data-ttu-id="0804f-608">Le *type* d’un *field_declaration* spécifie le type des membres introduits par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="0804f-608">The *type* of a *field_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="0804f-609">Le type est suivi d’une liste de *variable_declarator*s, chacun d’entre eux introduisant un nouveau membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-609">The type is followed by a list of *variable_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="0804f-610">Un *variable_declarator* se compose d’un *identificateur* qui nomme ce membre, éventuellement suivi d’un jeton « `=` » et d’un *variable_initializer* ([initialiseurs de variable](classes.md#variable-initializers)) qui donne la valeur initiale de ce membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-610">A *variable_declarator* consists of an *identifier* that names that member, optionally followed by an "`=`" token and a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)) that gives the initial value of that member.</span></span>

<span data-ttu-id="0804f-611">Le *type* d’un champ doit être au moins aussi accessible que le champ lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-611">The *type* of a field must be at least as accessible as the field itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-612">La valeur d’un champ est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-612">The value of a field is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="0804f-613">La valeur d’un champ qui n’est pas en lecture seule est modifiée à l’aide d’une *assignation* ([opérateurs d’affectation](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="0804f-613">The value of a non-readonly field is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="0804f-614">La valeur d’un champ non readonly peut être obtenue et modifiée à l’aide des opérateurs suffixés d’incrémentation et de décrémentation ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators)) et des opérateurs de préfixe et d’incrémentation ([incrément et décrémentation de préfixe) opérateurs](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="0804f-614">The value of a non-readonly field can be both obtained and modified using postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)) and prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="0804f-615">Une déclaration de champ qui déclare plusieurs champs équivaut à plusieurs déclarations de champs uniques avec les mêmes attributs, modificateurs et type.</span><span class="sxs-lookup"><span data-stu-id="0804f-615">A field declaration that declares multiple fields is equivalent to multiple declarations of single fields with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="0804f-616">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-616">For example</span></span>
```csharp
class A
{
    public static int X = 1, Y, Z = 100;
}
```
<span data-ttu-id="0804f-617">est équivalent à</span><span class="sxs-lookup"><span data-stu-id="0804f-617">is equivalent to</span></span>
```csharp
class A
{
    public static int X = 1;
    public static int Y;
    public static int Z = 100;
}
```

### <a name="static-and-instance-fields"></a><span data-ttu-id="0804f-618">Champs d’instance et statiques</span><span class="sxs-lookup"><span data-stu-id="0804f-618">Static and instance fields</span></span>

<span data-ttu-id="0804f-619">Lorsqu’une déclaration de champ comprend `static` un modificateur, les champs introduits par la déclaration sont des ***champs statiques***.</span><span class="sxs-lookup"><span data-stu-id="0804f-619">When a field declaration includes a `static` modifier, the fields introduced by the declaration are ***static fields***.</span></span> <span data-ttu-id="0804f-620">Si aucun `static` modificateur n’est présent, les champs introduits par la déclaration sont des ***champs d’instance***.</span><span class="sxs-lookup"><span data-stu-id="0804f-620">When no `static` modifier is present, the fields introduced by the declaration are ***instance fields***.</span></span> <span data-ttu-id="0804f-621">Les champs statiques et les champs d’instance sont deux des différents types[de variables](variables.md) (variables C#) pris en charge par, et parfois appelés ***variables statiques*** et ***variables d’instance***, respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-621">Static fields and instance fields are two of the several kinds of variables ([Variables](variables.md)) supported by C#, and at times they are referred to as ***static variables*** and ***instance variables***, respectively.</span></span>

<span data-ttu-id="0804f-622">Un champ statique ne fait pas partie d’une instance spécifique ; au lieu de cela, il est partagé entre toutes les instances d’un type fermé ([types ouverts et fermés](types.md#open-and-closed-types)).</span><span class="sxs-lookup"><span data-stu-id="0804f-622">A static field is not part of a specific instance; instead, it is shared amongst all instances of a closed type ([Open and closed types](types.md#open-and-closed-types)).</span></span> <span data-ttu-id="0804f-623">Quel que soit le nombre d’instances d’un type de classe fermé, il n’existe qu’une seule copie d’un champ statique pour le domaine d’application associé.</span><span class="sxs-lookup"><span data-stu-id="0804f-623">No matter how many instances of a closed class type are created, there is only ever one copy of a static field for the associated application domain.</span></span>

<span data-ttu-id="0804f-624">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-624">For example:</span></span>
```csharp
class C<V>
{
    static int count = 0;

    public C() {
        count++;
    }

    public static int Count {
        get { return count; }
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<double> x2 = new C<double>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<int> x3 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 2
    }
}
```

<span data-ttu-id="0804f-625">Un champ d’instance appartient à une instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-625">An instance field belongs to an instance.</span></span> <span data-ttu-id="0804f-626">Plus précisément, chaque instance d’une classe contient un ensemble distinct de tous les champs d’instance de cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-626">Specifically, every instance of a class contains a separate set of all the instance fields of that class.</span></span>

<span data-ttu-id="0804f-627">Lorsqu’un champ est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est un champ statique, `E` doit désigner un type contenant `M`, et si `M` est un champ d’instance, E doit désigner une instance d’un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-627">When a field is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static field, `E` must denote a type containing `M`, and if `M` is an instance field, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="0804f-628">Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="0804f-628">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="readonly-fields"></a><span data-ttu-id="0804f-629">Champs en lecture seule</span><span class="sxs-lookup"><span data-stu-id="0804f-629">Readonly fields</span></span>

<span data-ttu-id="0804f-630">Lorsqu’un *field_declaration* comprend un modificateur `readonly`, les champs introduits par la déclaration sont des ***champs en lecture seule***.</span><span class="sxs-lookup"><span data-stu-id="0804f-630">When a *field_declaration* includes a `readonly` modifier, the fields introduced by the declaration are ***readonly fields***.</span></span> <span data-ttu-id="0804f-631">Les assignations directes aux champs ReadOnly ne peuvent se produire que dans le cadre de cette déclaration ou dans un constructeur d’instance ou un constructeur statique dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-631">Direct assignments to readonly fields can only occur as part of that declaration or in an instance constructor or static constructor in the same class.</span></span> <span data-ttu-id="0804f-632">(Un champ readonly peut être affecté à plusieurs fois dans ces contextes.) Plus précisément, les assignations directes à un `readonly` champ sont autorisées uniquement dans les contextes suivants :</span><span class="sxs-lookup"><span data-stu-id="0804f-632">(A readonly field can be assigned to multiple times in these contexts.) Specifically, direct assignments to a `readonly` field are permitted only in the following contexts:</span></span>

*  <span data-ttu-id="0804f-633">Dans le *variable_declarator* qui introduit le champ (en incluant un *variable_initializer* dans la déclaration).</span><span class="sxs-lookup"><span data-stu-id="0804f-633">In the *variable_declarator* that introduces the field (by including a *variable_initializer* in the declaration).</span></span>
*  <span data-ttu-id="0804f-634">Pour un champ d’instance, dans les constructeurs d’instance de la classe qui contient la déclaration de champ ; pour un champ statique, dans le constructeur statique de la classe qui contient la déclaration de champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-634">For an instance field, in the instance constructors of the class that contains the field declaration; for a static field, in the static constructor of the class that contains the field declaration.</span></span> <span data-ttu-id="0804f-635">Il s’agit également des seuls contextes dans lesquels il est possible de passer `readonly` un champ `out` en tant `ref` que paramètre ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-635">These are also the only contexts in which it is valid to pass a `readonly` field as an `out` or `ref` parameter.</span></span>

<span data-ttu-id="0804f-636">Toute tentative d’assignation à `readonly` un champ ou de transmission `out` en tant `ref` que paramètre ou dans un autre contexte est une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-636">Attempting to assign to a `readonly` field or pass it as an `out` or `ref` parameter in any other context is a compile-time error.</span></span>

#### <a name="using-static-readonly-fields-for-constants"></a><span data-ttu-id="0804f-637">Utilisation de champs ReadOnly statiques pour les constantes</span><span class="sxs-lookup"><span data-stu-id="0804f-637">Using static readonly fields for constants</span></span>

<span data-ttu-id="0804f-638">Un `static readonly` champ est utile lorsqu’un nom symbolique pour une valeur constante est souhaité, mais lorsque le type de la valeur n’est pas autorisé dans `const` une déclaration, ou lorsque la valeur ne peut pas être calculée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-638">A `static readonly` field is useful when a symbolic name for a constant value is desired, but when the type of the value is not permitted in a `const` declaration, or when the value cannot be computed at compile-time.</span></span> <span data-ttu-id="0804f-639">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-639">In the example</span></span>
```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);

    private byte red, green, blue;

    public Color(byte r, byte g, byte b) {
        red = r;
        green = g;
        blue = b;
    }
}
```
<span data-ttu-id="0804f-640">les `Black`membres `White`,, `Red`, `const` et ne`Blue` peuvent pas être déclarés comme membres, car leurs valeurs ne peuvent pas être calculées au moment de la compilation. `Green`</span><span class="sxs-lookup"><span data-stu-id="0804f-640">the `Black`, `White`, `Red`, `Green`, and `Blue` members cannot be declared as `const` members because their values cannot be computed at compile-time.</span></span> <span data-ttu-id="0804f-641">Toutefois, leur `static readonly` déclaration à la place a un effet très similaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-641">However, declaring them `static readonly` instead has much the same effect.</span></span>

#### <a name="versioning-of-constants-and-static-readonly-fields"></a><span data-ttu-id="0804f-642">Contrôle de version des constantes et des champs ReadOnly statiques</span><span class="sxs-lookup"><span data-stu-id="0804f-642">Versioning of constants and static readonly fields</span></span>

<span data-ttu-id="0804f-643">Les constantes et les champs ReadOnly ont une sémantique de version binaire différente.</span><span class="sxs-lookup"><span data-stu-id="0804f-643">Constants and readonly fields have different binary versioning semantics.</span></span> <span data-ttu-id="0804f-644">Lorsqu’une expression fait référence à une constante, la valeur de la constante est obtenue au moment de la compilation, mais lorsqu’une expression fait référence à un champ ReadOnly, la valeur du champ n’est obtenue qu’au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-644">When an expression references a constant, the value of the constant is obtained at compile-time, but when an expression references a readonly field, the value of the field is not obtained until run-time.</span></span> <span data-ttu-id="0804f-645">Prenons l’exemple d’une application qui se compose de deux programmes distincts :</span><span class="sxs-lookup"><span data-stu-id="0804f-645">Consider an application that consists of two separate programs:</span></span>
```csharp
using System;

namespace Program1
{
    public class Utils
    {
        public static readonly int X = 1;
    }
}

namespace Program2
{
    class Test
    {
        static void Main() {
            Console.WriteLine(Program1.Utils.X);
        }
    }
}
```

<span data-ttu-id="0804f-646">Les `Program1` espaces `Program2` de noms et désignent deux programmes qui sont compilés séparément.</span><span class="sxs-lookup"><span data-stu-id="0804f-646">The `Program1` and `Program2` namespaces denote two programs that are compiled separately.</span></span> <span data-ttu-id="0804f-647">Étant `Program1.Utils.X` donné que est déclaré en tant que champ readonly statique, la valeur `Console.WriteLine` générée par l’instruction n’est pas connue au moment de la compilation, mais est plutôt obtenue au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-647">Because `Program1.Utils.X` is declared as a static readonly field, the value output by the `Console.WriteLine` statement is not known at compile-time, but rather is obtained at run-time.</span></span> <span data-ttu-id="0804f-648">Ainsi, si la valeur de `X` est modifiée et `Program1` est recompilée, `Console.WriteLine` l’instruction génère la nouvelle valeur même si `Program2` n’est pas recompilé.</span><span class="sxs-lookup"><span data-stu-id="0804f-648">Thus, if the value of `X` is changed and `Program1` is recompiled, the `Console.WriteLine` statement will output the new value even if `Program2` isn't recompiled.</span></span> <span data-ttu-id="0804f-649">Toutefois, avait `X` été une constante, la valeur de `X` aurait été obtenue au moment `Program2` de la compilation `Program1` et ne serait pas affectée par les modifications apportées `Program2` à jusqu’à la recompilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-649">However, had `X` been a constant, the value of `X` would have been obtained at the time `Program2` was compiled, and would remain unaffected by changes in `Program1` until `Program2` is recompiled.</span></span>

### <a name="volatile-fields"></a><span data-ttu-id="0804f-650">Champs volatiles</span><span class="sxs-lookup"><span data-stu-id="0804f-650">Volatile fields</span></span>

<span data-ttu-id="0804f-651">Quand un *field_declaration* comprend un modificateur `volatile`, les champs introduits par cette déclaration sont des ***champs volatiles***.</span><span class="sxs-lookup"><span data-stu-id="0804f-651">When a *field_declaration* includes a `volatile` modifier, the fields introduced by that declaration are ***volatile fields***.</span></span>

<span data-ttu-id="0804f-652">Pour les champs non volatiles, les techniques d’optimisation qui réorganisent les instructions peuvent entraîner des résultats inattendus et imprévisibles dans les programmes multithread qui accèdent aux champs sans synchronisation, comme celui fourni par *lock_statement* ([le instruction lock](statements.md#the-lock-statement)).</span><span class="sxs-lookup"><span data-stu-id="0804f-652">For non-volatile fields, optimization techniques that reorder instructions can lead to unexpected and unpredictable results in multi-threaded programs that access fields without synchronization such as that provided by the *lock_statement* ([The lock statement](statements.md#the-lock-statement)).</span></span> <span data-ttu-id="0804f-653">Ces optimisations peuvent être effectuées par le compilateur, par le système d’exécution ou par le matériel.</span><span class="sxs-lookup"><span data-stu-id="0804f-653">These optimizations can be performed by the compiler, by the run-time system, or by hardware.</span></span> <span data-ttu-id="0804f-654">Pour les champs volatils, ces optimisations de réorganisation sont restreintes :</span><span class="sxs-lookup"><span data-stu-id="0804f-654">For volatile fields, such reordering optimizations are restricted:</span></span>

*  <span data-ttu-id="0804f-655">La lecture d’un champ volatile est appelée une ***lecture volatile***.</span><span class="sxs-lookup"><span data-stu-id="0804f-655">A read of a volatile field is called a ***volatile read***.</span></span> <span data-ttu-id="0804f-656">Une lecture volatile a une « sémantique d’acquisition »; autrement dit, il est garanti qu’il se produise avant toute référence à la mémoire qui se trouve après dans la séquence d’instructions.</span><span class="sxs-lookup"><span data-stu-id="0804f-656">A volatile read has "acquire semantics"; that is, it is guaranteed to occur prior to any references to memory that occur after it in the instruction sequence.</span></span>
*  <span data-ttu-id="0804f-657">L’écriture d’un champ volatile est appelée une ***écriture volatile***.</span><span class="sxs-lookup"><span data-stu-id="0804f-657">A write of a volatile field is called a ***volatile write***.</span></span> <span data-ttu-id="0804f-658">Une écriture volatile a une « sémantique de version »; autrement dit, il est garanti qu’il se produira après toute référence de mémoire avant l’instruction d’écriture dans la séquence d’instructions.</span><span class="sxs-lookup"><span data-stu-id="0804f-658">A volatile write has "release semantics"; that is, it is guaranteed to happen after any memory references prior to the write instruction in the instruction sequence.</span></span>

<span data-ttu-id="0804f-659">Ces restrictions garantissent que tous les threads observent les écritures volatiles effectuées par un autre thread dans l’ordre dans lequel elles ont été effectuées.</span><span class="sxs-lookup"><span data-stu-id="0804f-659">These restrictions ensure that all threads will observe volatile writes performed by any other thread in the order in which they were performed.</span></span> <span data-ttu-id="0804f-660">Une implémentation conforme n’est pas requise pour fournir un classement total unique des écritures volatiles, comme vu par tous les threads d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-660">A conforming implementation is not required to provide a single total ordering of volatile writes as seen from all threads of execution.</span></span> <span data-ttu-id="0804f-661">Le type d’un champ volatile doit être l’un des suivants :</span><span class="sxs-lookup"><span data-stu-id="0804f-661">The type of a volatile field must be one of the following:</span></span>

*  <span data-ttu-id="0804f-662">*Reference_type*.</span><span class="sxs-lookup"><span data-stu-id="0804f-662">A *reference_type*.</span></span>
*  <span data-ttu-id="0804f-663">`byte`Type ,`char` ,,`System.UIntPtr`,,,, ,`System.IntPtr`, ou. `bool` `float` `sbyte` `short` `ushort` `int` `uint`</span><span class="sxs-lookup"><span data-stu-id="0804f-663">The type `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, or `System.UIntPtr`.</span></span>
*  <span data-ttu-id="0804f-664">*Enum_type* dont le type de base enum est `byte`, `sbyte`, `short`, `ushort`, `int` ou `uint`.</span><span class="sxs-lookup"><span data-stu-id="0804f-664">An *enum_type* having an enum base type of `byte`, `sbyte`, `short`, `ushort`, `int`, or `uint`.</span></span>

<span data-ttu-id="0804f-665">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-665">The example</span></span>
```csharp
using System;
using System.Threading;

class Test
{
    public static int result;   
    public static volatile bool finished;

    static void Thread2() {
        result = 143;    
        finished = true; 
    }

    static void Main() {
        finished = false;

        // Run Thread2() in a new thread
        new Thread(new ThreadStart(Thread2)).Start();

        // Wait for Thread2 to signal that it has a result by setting
        // finished to true.
        for (;;) {
            if (finished) {
                Console.WriteLine("result = {0}", result);
                return;
            }
        }
    }
}
```
<span data-ttu-id="0804f-666">génère cette sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-666">produces the output:</span></span>
```console
result = 143
```

<span data-ttu-id="0804f-667">Dans cet exemple, la méthode `Main` démarre un nouveau thread qui exécute la méthode `Thread2`.</span><span class="sxs-lookup"><span data-stu-id="0804f-667">In this example, the method `Main` starts a new thread that runs the method `Thread2`.</span></span> <span data-ttu-id="0804f-668">Cette méthode stocke une valeur dans un champ non volatile appelé `result`, puis stocke `true` dans le champ `finished`volatile.</span><span class="sxs-lookup"><span data-stu-id="0804f-668">This method stores a value into a non-volatile field called `result`, then stores `true` in the volatile field `finished`.</span></span> <span data-ttu-id="0804f-669">Le thread principal attend que le champ `finished` soit défini sur `true`, puis lit le champ `result`.</span><span class="sxs-lookup"><span data-stu-id="0804f-669">The main thread waits for the field `finished` to be set to `true`, then reads the field `result`.</span></span> <span data-ttu-id="0804f-670">Étant `finished` donné que a `volatile`été déclaré, le thread principal doit lire `143` la valeur du `result`champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-670">Since `finished` has been declared `volatile`, the main thread must read the value `143` from the field `result`.</span></span> <span data-ttu-id="0804f-671">Si le champ `finished` n’a pas été `volatile`déclaré, il est possible que le magasin `result` soit visible par le thread principal après le stockage `finished`, et par conséquent, pour que le thread principal Lise la valeur `0` à partir de la champ `result`.</span><span class="sxs-lookup"><span data-stu-id="0804f-671">If the field `finished` had not been declared `volatile`, then it would be permissible for the store to `result` to be visible to the main thread after the store to `finished`, and hence for the main thread to read the value `0` from the field `result`.</span></span> <span data-ttu-id="0804f-672">La Déclaration `finished` `volatile` en tant que champ empêche toute incohérence.</span><span class="sxs-lookup"><span data-stu-id="0804f-672">Declaring `finished` as a `volatile` field prevents any such inconsistency.</span></span>

### <a name="field-initialization"></a><span data-ttu-id="0804f-673">Initialisation de champ</span><span class="sxs-lookup"><span data-stu-id="0804f-673">Field initialization</span></span>

<span data-ttu-id="0804f-674">La valeur initiale d’un champ, qu’il s’agisse d’un champ statique ou d’un champ d’instance, est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type du champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-674">The initial value of a field, whether it be a static field or an instance field, is the default value ([Default values](variables.md#default-values)) of the field's type.</span></span> <span data-ttu-id="0804f-675">Il n’est pas possible d’observer la valeur d’un champ avant l’initialisation par défaut, et un champ n’est donc jamais « non initialisé ».</span><span class="sxs-lookup"><span data-stu-id="0804f-675">It is not possible to observe the value of a field before this default initialization has occurred, and a field is thus never "uninitialized".</span></span> <span data-ttu-id="0804f-676">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-676">The example</span></span>
```csharp
using System;

class Test
{
    static bool b;
    int i;

    static void Main() {
        Test t = new Test();
        Console.WriteLine("b = {0}, i = {1}", b, t.i);
    }
}
```
<span data-ttu-id="0804f-677">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-677">produces the output</span></span>
```console
b = False, i = 0
```
<span data-ttu-id="0804f-678">étant `b` donné `i` que et sont initialisés automatiquement avec les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-678">because `b` and `i` are both automatically initialized to default values.</span></span>

### <a name="variable-initializers"></a><span data-ttu-id="0804f-679">Initialiseurs de variables</span><span class="sxs-lookup"><span data-stu-id="0804f-679">Variable initializers</span></span>

<span data-ttu-id="0804f-680">Les déclarations de champ peuvent inclure des *variable_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="0804f-680">Field declarations may include *variable_initializer*s.</span></span> <span data-ttu-id="0804f-681">Pour les champs statiques, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées pendant l’initialisation de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-681">For static fields, variable initializers correspond to assignment statements that are executed during class initialization.</span></span> <span data-ttu-id="0804f-682">Pour les champs d’instance, les initialiseurs de variable correspondent aux instructions d’assignation qui sont exécutées lors de la création d’une instance de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-682">For instance fields, variable initializers correspond to assignment statements that are executed when an instance of the class is created.</span></span>

<span data-ttu-id="0804f-683">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-683">The example</span></span>
```csharp
using System;

class Test
{
    static double x = Math.Sqrt(2.0);
    int i = 100;
    string s = "Hello";

    static void Main() {
        Test a = new Test();
        Console.WriteLine("x = {0}, i = {1}, s = {2}", x, a.i, a.s);
    }
}
```
<span data-ttu-id="0804f-684">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-684">produces the output</span></span>
```console
x = 1.4142135623731, i = 100, s = Hello
```
<span data-ttu-id="0804f-685">parce qu’une assignation `x` à se produit lorsque des initialiseurs de champs statiques `s` exécutent des assignations à et se produisent lorsque les initialiseurs de champ d' `i` instance s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="0804f-685">because an assignment to `x` occurs when static field initializers execute and assignments to `i` and `s` occur when the instance field initializers execute.</span></span>

<span data-ttu-id="0804f-686">L’initialisation de la valeur par défaut décrite dans [initialisation de champ](classes.md#field-initialization) se produit pour tous les champs, y compris les champs qui ont des initialiseurs de variable.</span><span class="sxs-lookup"><span data-stu-id="0804f-686">The default value initialization described in [Field initialization](classes.md#field-initialization) occurs for all fields, including fields that have variable initializers.</span></span> <span data-ttu-id="0804f-687">Ainsi, lorsqu’une classe est initialisée, tous les champs statiques de cette classe sont d’abord initialisés à leurs valeurs par défaut, puis les initialiseurs de champs statiques sont exécutés dans l’ordre textuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-687">Thus, when a class is initialized, all static fields in that class are first initialized to their default values, and then the static field initializers are executed in textual order.</span></span> <span data-ttu-id="0804f-688">De même, lorsqu’une instance d’une classe est créée, tous les champs d’instance dans cette instance sont d’abord initialisés à leurs valeurs par défaut, puis les initialiseurs de champ d’instance sont exécutés dans l’ordre textuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-688">Likewise, when an instance of a class is created, all instance fields in that instance are first initialized to their default values, and then the instance field initializers are executed in textual order.</span></span>

<span data-ttu-id="0804f-689">Il est possible que les champs statiques avec des initialiseurs de variable soient observés dans leur état de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-689">It is possible for static fields with variable initializers to be observed in their default value state.</span></span> <span data-ttu-id="0804f-690">Toutefois, cela est fortement déconseillé en tant que question de style.</span><span class="sxs-lookup"><span data-stu-id="0804f-690">However, this is strongly discouraged as a matter of style.</span></span> <span data-ttu-id="0804f-691">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-691">The example</span></span>
```csharp
using System;

class Test
{
    static int a = b + 1;
    static int b = a + 1;

    static void Main() {
        Console.WriteLine("a = {0}, b = {1}", a, b);
    }
}
```
<span data-ttu-id="0804f-692">présente ce comportement.</span><span class="sxs-lookup"><span data-stu-id="0804f-692">exhibits this behavior.</span></span> <span data-ttu-id="0804f-693">Malgré les définitions circulaires de a et b, le programme est valide.</span><span class="sxs-lookup"><span data-stu-id="0804f-693">Despite the circular definitions of a and b, the program is valid.</span></span> <span data-ttu-id="0804f-694">Elle génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-694">It results in the output</span></span>
```console
a = 1, b = 2
```
<span data-ttu-id="0804f-695">étant donné que les `a` champs `b` statiques et sont `0` initialisés à (valeur `int`par défaut pour) avant l’exécution de leurs initialiseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-695">because the static fields `a` and `b` are initialized to `0` (the default value for `int`) before their initializers are executed.</span></span> <span data-ttu-id="0804f-696">Lorsque l’initialiseur pour `a` s’exécute, la valeur `b` de est égale à zéro `a` , et par conséquent `1`, est initialisé à.</span><span class="sxs-lookup"><span data-stu-id="0804f-696">When the initializer for `a` runs, the value of `b` is zero, and so `a` is initialized to `1`.</span></span> <span data-ttu-id="0804f-697">Lorsque l’initialiseur pour `b` s’exécute, la valeur `a` de est `1`déjà, et `b` par conséquent, est `2`initialisé à.</span><span class="sxs-lookup"><span data-stu-id="0804f-697">When the initializer for `b` runs, the value of `a` is already `1`, and so `b` is initialized to `2`.</span></span>

#### <a name="static-field-initialization"></a><span data-ttu-id="0804f-698">Initialisation de champ statique</span><span class="sxs-lookup"><span data-stu-id="0804f-698">Static field initialization</span></span>

<span data-ttu-id="0804f-699">Les initialiseurs de variable de champ statique d’une classe correspondent à une séquence d’assignations qui sont exécutées dans l’ordre textuel dans lequel elles apparaissent dans la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-699">The static field variable initializers of a class correspond to a sequence of assignments that are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="0804f-700">Si un constructeur statique ([constructeurs statiques](classes.md#static-constructors)) existe dans la classe, l’exécution des initialiseurs de champ statiques se produit immédiatement avant l’exécution de ce constructeur statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-700">If a static constructor ([Static constructors](classes.md#static-constructors)) exists in the class, execution of the static field initializers occurs immediately prior to executing that static constructor.</span></span> <span data-ttu-id="0804f-701">Sinon, les initialiseurs de champs statiques sont exécutés à un moment dépendant de l’implémentation avant la première utilisation d’un champ statique de cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-701">Otherwise, the static field initializers are executed at an implementation-dependent time prior to the first use of a static field of that class.</span></span> <span data-ttu-id="0804f-702">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-702">The example</span></span>
```csharp
using System;

class Test 
{ 
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    public static int X = Test.F("Init A");
}

class B
{
    public static int Y = Test.F("Init B");
}
```
<span data-ttu-id="0804f-703">peut produire la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-703">might produce either the output:</span></span>
```console
Init A
Init B
1 1
```
<span data-ttu-id="0804f-704">ou la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-704">or the output:</span></span>
```console
Init B
Init A
1 1
```
<span data-ttu-id="0804f-705">étant donné que l' `X`exécution de l’initialiseur et `Y`de l’initialiseur de est susceptible de se produire dans l’un ou l’autre ordre, il ne doit se produire que pour les références à ces champs.</span><span class="sxs-lookup"><span data-stu-id="0804f-705">because the execution of `X`'s initializer and `Y`'s initializer could occur in either order; they are only constrained to occur before the references to those fields.</span></span> <span data-ttu-id="0804f-706">Toutefois, dans l’exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-706">However, in the example:</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    static A() {}

    public static int X = Test.F("Init A");
}

class B
{
    static B() {}

    public static int Y = Test.F("Init B");
}
```
<span data-ttu-id="0804f-707">la sortie doit être :</span><span class="sxs-lookup"><span data-stu-id="0804f-707">the output must be:</span></span>
```console
Init B
Init A
1 1
```
<span data-ttu-id="0804f-708">étant donné que les règles de moment où les constructeurs statiques s’exécutent (comme défini dans les [constructeurs statiques](classes.md#static-constructors)) fournissent `B`ce `B`constructeur statique (et par conséquent, les `A`initialiseurs de champ statiques) doivent s’exécuter avant la statique de initialiseurs de constructeur et de champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-708">because the rules for when static constructors execute (as defined in [Static constructors](classes.md#static-constructors)) provide that `B`'s static constructor (and hence `B`'s static field initializers) must run before `A`'s static constructor and field initializers.</span></span>

#### <a name="instance-field-initialization"></a><span data-ttu-id="0804f-709">Initialisation du champ d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-709">Instance field initialization</span></span>

<span data-ttu-id="0804f-710">Les initialiseurs de variable de champ d’instance d’une classe correspondent à une séquence d’assignations qui sont exécutées immédiatement après l’entrée de l’un des constructeurs d’instance ([initialiseurs de constructeur](classes.md#constructor-initializers)) de cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-710">The instance field variable initializers of a class correspond to a sequence of assignments that are executed immediately upon entry to any one of the instance constructors ([Constructor initializers](classes.md#constructor-initializers)) of that class.</span></span> <span data-ttu-id="0804f-711">Les initialiseurs de variable sont exécutés dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-711">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="0804f-712">Le processus de création et d’initialisation de l’instance de classe est décrit plus en détail dans les [constructeurs d’instance](classes.md#instance-constructors).</span><span class="sxs-lookup"><span data-stu-id="0804f-712">The class instance creation and initialization process is described further in [Instance constructors](classes.md#instance-constructors).</span></span>

<span data-ttu-id="0804f-713">Un initialiseur de variable pour un champ d’instance ne peut pas faire référence à l’instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="0804f-713">A variable initializer for an instance field cannot reference the instance being created.</span></span> <span data-ttu-id="0804f-714">Par conséquent, il s’agit d’une erreur au moment de la compilation pour référencer `this` dans un initialiseur de variable, car il s’agit d’une erreur de compilation pour qu’un initialiseur de variable référence un membre d’instance via un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="0804f-714">Thus, it is a compile-time error to reference `this` in a variable initializer, as it is a compile-time error for a variable initializer to reference any instance member through a *simple_name*.</span></span> <span data-ttu-id="0804f-715">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-715">In the example</span></span>
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
<span data-ttu-id="0804f-716">l’initialiseur de variable `y` pour génère une erreur de compilation, car il fait référence à un membre de l’instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="0804f-716">the variable initializer for `y` results in a compile-time error because it references a member of the instance being created.</span></span>

## <a name="methods"></a><span data-ttu-id="0804f-717">Méthodes</span><span class="sxs-lookup"><span data-stu-id="0804f-717">Methods</span></span>

<span data-ttu-id="0804f-718">Une ***méthode*** est un membre qui implémente un calcul ou une action qui peut être effectuée par un objet ou une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-718">A ***method*** is a member that implements a computation or action that can be performed by an object or class.</span></span> <span data-ttu-id="0804f-719">Les méthodes sont déclarées à l’aide de *method_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-719">Methods are declared using *method_declaration*s:</span></span>

```antlr
method_declaration
    : method_header method_body
    ;

method_header
    : attributes? method_modifier* 'partial'? return_type member_name type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause*
    ;

method_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | 'async'
    | method_modifier_unsafe
    ;

return_type
    : type
    | 'void'
    ;

member_name
    : identifier
    | interface_type '.' identifier
    ;

method_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

<span data-ttu-id="0804f-720">Un *method_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-720">A *method_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="0804f-721">Une déclaration a une combinaison valide de modificateurs si toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="0804f-721">A declaration has a valid combination of modifiers if all of the following are true:</span></span>

*  <span data-ttu-id="0804f-722">La déclaration comprend une combinaison valide de modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-722">The declaration includes a valid combination of access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span>
*  <span data-ttu-id="0804f-723">La déclaration n’inclut pas le même modificateur plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="0804f-723">The declaration does not include the same modifier multiple times.</span></span>
*  <span data-ttu-id="0804f-724">La déclaration comprend au plus l’un des modificateurs suivants : `static`, `virtual`et `override`.</span><span class="sxs-lookup"><span data-stu-id="0804f-724">The declaration includes at most one of the following modifiers: `static`, `virtual`, and `override`.</span></span>
*  <span data-ttu-id="0804f-725">La déclaration comprend au plus l’un des modificateurs suivants : `new` et `override`.</span><span class="sxs-lookup"><span data-stu-id="0804f-725">The declaration includes at most one of the following modifiers: `new` and `override`.</span></span>
*  <span data-ttu-id="0804f-726">Si la déclaration inclut le `abstract` modificateur, la déclaration n’inclut pas l’un des modificateurs suivants : `static`, `virtual` `sealed` ou `extern`.</span><span class="sxs-lookup"><span data-stu-id="0804f-726">If the declaration includes the `abstract` modifier, then the declaration does not include any of the following modifiers: `static`, `virtual`, `sealed` or `extern`.</span></span>
*  <span data-ttu-id="0804f-727">Si la déclaration inclut le `private` modificateur, la déclaration n’inclut pas l’un des modificateurs suivants : `virtual`, `override`ou `abstract`.</span><span class="sxs-lookup"><span data-stu-id="0804f-727">If the declaration includes the `private` modifier, then the declaration does not include any of the following modifiers: `virtual`, `override`, or `abstract`.</span></span>
*  <span data-ttu-id="0804f-728">Si la déclaration comprend le `sealed` modificateur, la déclaration comprend également le `override` modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-728">If the declaration includes the `sealed` modifier, then the declaration also includes the `override` modifier.</span></span>
*  <span data-ttu-id="0804f-729">Si la déclaration inclut le `partial` modificateur, elle n’inclut pas l’un des modificateurs suivants : `new`, `public`, `protected`, `internal`, `private`, `virtual`,, `override` `sealed` , `abstract`ou .`extern`</span><span class="sxs-lookup"><span data-stu-id="0804f-729">If the declaration includes the `partial` modifier, then it does not include any of the following modifiers: `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override`, `abstract`, or `extern`.</span></span>

<span data-ttu-id="0804f-730">Une méthode qui a le `async` modificateur est une fonction Async et suit les règles décrites dans [fonctions Async](classes.md#async-functions).</span><span class="sxs-lookup"><span data-stu-id="0804f-730">A method that has the `async` modifier is an async function and follows the rules described in [Async functions](classes.md#async-functions).</span></span>

<span data-ttu-id="0804f-731">L' *type_retour* d’une déclaration de méthode spécifie le type de la valeur calculée et retournée par la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-731">The *return_type* of a method declaration specifies the type of the value computed and returned by the method.</span></span> <span data-ttu-id="0804f-732">*Type_retour* est `void` si la méthode ne retourne pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-732">The *return_type* is `void` if the method does not return a value.</span></span> <span data-ttu-id="0804f-733">Si la déclaration comprend le `partial` modificateur, le type de retour doit être `void`.</span><span class="sxs-lookup"><span data-stu-id="0804f-733">If the declaration includes the `partial` modifier, then the return type must be `void`.</span></span>

<span data-ttu-id="0804f-734">*Member_Name* spécifie le nom de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-734">The *member_name* specifies the name of the method.</span></span> <span data-ttu-id="0804f-735">À moins que la méthode ne soit une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)), *Member_Name* est simplement un *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="0804f-735">Unless the method is an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="0804f-736">Pour une implémentation de membre d’interface explicite, *Member_Name* se compose d’un *INTERFACE_TYPE* suivi d’un « `.` » et d’un *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="0804f-736">For an explicit interface member implementation, the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="0804f-737">Le *type_parameter_list* facultatif spécifie les paramètres de type de la méthode ([paramètres de type](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="0804f-737">The optional *type_parameter_list* specifies the type parameters of the method ([Type parameters](classes.md#type-parameters)).</span></span> <span data-ttu-id="0804f-738">Si un *type_parameter_list* est spécifié, la méthode est une ***méthode générique***.</span><span class="sxs-lookup"><span data-stu-id="0804f-738">If a *type_parameter_list* is specified the method is a ***generic method***.</span></span> <span data-ttu-id="0804f-739">Si la méthode a un modificateur `extern`, un *type_parameter_list* ne peut pas être spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-739">If the method has an `extern` modifier, a *type_parameter_list* cannot be specified.</span></span>

<span data-ttu-id="0804f-740">Le *formal_parameter_list* facultatif spécifie les paramètres de la méthode ([paramètres de méthode](classes.md#method-parameters)).</span><span class="sxs-lookup"><span data-stu-id="0804f-740">The optional *formal_parameter_list* specifies the parameters of the method ([Method parameters](classes.md#method-parameters)).</span></span>

<span data-ttu-id="0804f-741">Les *type_parameter_constraints_clause*facultatifs spécifient des contraintes sur des paramètres de type individuels ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) et peuvent être spécifiés uniquement si un *type_parameter_list* est également fourni, et si la méthode n’a pas de modificateur `override`.</span><span class="sxs-lookup"><span data-stu-id="0804f-741">The optional *type_parameter_constraints_clause*s specify constraints on individual type parameters ([Type parameter constraints](classes.md#type-parameter-constraints)) and may only be specified if a *type_parameter_list* is also supplied, and the method does not have an `override` modifier.</span></span>

<span data-ttu-id="0804f-742">L' *type_retour* et chacun des types référencés dans le *formal_parameter_list* d’une méthode doivent être au moins aussi accessibles que la méthode elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-742">The *return_type* and each of the types referenced in the *formal_parameter_list* of a method must be at least as accessible as the method itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-743">*Method_body* est un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***.</span><span class="sxs-lookup"><span data-stu-id="0804f-743">The *method_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="0804f-744">Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-744">A statement body consists of a *block*, which specifies the statements to execute when the method is invoked.</span></span> <span data-ttu-id="0804f-745">Un corps d’expression se `=>` compose de suivis d’une *expression* et d’un point-virgule, et désigne une expression unique à exécuter lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-745">An expression body consists of `=>` followed by an *expression* and a semicolon, and denotes a single expression to perform when the method is invoked.</span></span> 

<span data-ttu-id="0804f-746">Pour les méthodes `abstract` et `extern`, *method_body* se compose simplement d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-746">For `abstract` and `extern` methods, the *method_body* consists simply of a semicolon.</span></span> <span data-ttu-id="0804f-747">Pour les méthodes `partial`, *method_body* peut être constitué soit d’un point-virgule, d’un corps de bloc ou d’un corps d’expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-747">For `partial` methods the *method_body* may consist of either a semicolon, a block body or an expression body.</span></span> <span data-ttu-id="0804f-748">Pour toutes les autres méthodes, *method_body* est soit un corps de bloc, soit un corps d’expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-748">For all other methods, the *method_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="0804f-749">Si le *method_body* se compose d’un point-virgule, la déclaration peut ne pas inclure le modificateur `async`.</span><span class="sxs-lookup"><span data-stu-id="0804f-749">If the *method_body* consists of a semicolon, then the declaration may not include the `async` modifier.</span></span>

<span data-ttu-id="0804f-750">Le nom, la liste des paramètres de type et la liste de paramètres formels d’une méthode définissent la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-750">The name, the type parameter list and the formal parameter list of a method define the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the method.</span></span> <span data-ttu-id="0804f-751">Plus précisément, la signature d’une méthode se compose de son nom, du nombre de paramètres de type et du nombre, des modificateurs et des types de ses paramètres formels.</span><span class="sxs-lookup"><span data-stu-id="0804f-751">Specifically, the signature of a method consists of its name, the number of type parameters and the number, modifiers, and types of its formal parameters.</span></span> <span data-ttu-id="0804f-752">Pour ces raisons, tout paramètre de type de la méthode qui se produit dans le type d’un paramètre formel est identifié par son nom, mais par sa position ordinale dans la liste d’arguments de type de la méthode. Le type de retour ne fait pas partie de la signature d’une méthode, ni les noms des paramètres de type ni les paramètres formels.</span><span class="sxs-lookup"><span data-stu-id="0804f-752">For these purposes, any type parameter of the method that occurs in the type of a formal parameter is identified not by its name, but by its ordinal position in the type argument list of the method.The return type is not part of a method's signature, nor are the names of the type parameters or the formal parameters.</span></span>

<span data-ttu-id="0804f-753">Le nom d’une méthode doit être différent des noms de toutes les autres méthodes non déclarées dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-753">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="0804f-754">En outre, la signature d’une méthode doit être différente des signatures de toutes les autres méthodes déclarées dans la même classe, et deux méthodes déclarées dans la même classe ne peuvent pas avoir de `ref` signatures `out`qui diffèrent uniquement par et.</span><span class="sxs-lookup"><span data-stu-id="0804f-754">In addition, the signature of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>

<span data-ttu-id="0804f-755">Le *type_parameter*de la méthode se trouve dans l’étendue dans tout le *method_declaration*, et peut être utilisé pour former des types dans cette portée dans *type_retour*, *method_body*et *type_parameter_constraints_clause*s, mais pas dans *attributs*.</span><span class="sxs-lookup"><span data-stu-id="0804f-755">The method's *type_parameter*s are in scope throughout the *method_declaration*, and can be used to form types throughout that scope in *return_type*, *method_body*, and *type_parameter_constraints_clause*s but not in *attributes*.</span></span>

<span data-ttu-id="0804f-756">Tous les paramètres formels et les paramètres de type doivent avoir des noms différents.</span><span class="sxs-lookup"><span data-stu-id="0804f-756">All formal parameters and type parameters must have different names.</span></span>

### <a name="method-parameters"></a><span data-ttu-id="0804f-757">Paramètres de méthode</span><span class="sxs-lookup"><span data-stu-id="0804f-757">Method parameters</span></span>

<span data-ttu-id="0804f-758">Les paramètres d’une méthode, le cas échéant, sont déclarés par le *formal_parameter_list*de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-758">The parameters of a method, if any, are declared by the method's *formal_parameter_list*.</span></span>

```antlr
formal_parameter_list
    : fixed_parameters
    | fixed_parameters ',' parameter_array
    | parameter_array
    ;

fixed_parameters
    : fixed_parameter (',' fixed_parameter)*
    ;

fixed_parameter
    : attributes? parameter_modifier? type identifier default_argument?
    ;

default_argument
    : '=' expression
    ;

parameter_modifier
    : 'ref'
    | 'out'
    | 'this'
    ;

parameter_array
    : attributes? 'params' array_type identifier
    ;
```

<span data-ttu-id="0804f-759">La liste de paramètres formels se compose d’un ou de plusieurs paramètres séparés par des virgules dont seule la dernière peut être un *parameter_array*.</span><span class="sxs-lookup"><span data-stu-id="0804f-759">The formal parameter list consists of one or more comma-separated parameters of which only the last may be a *parameter_array*.</span></span>

<span data-ttu-id="0804f-760">Un *fixed_parameter* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), d’un modificateur facultatif `ref`, `out` ou `this`, d’un *type*, d’un *identificateur* et d’un *default_argument*facultatif.</span><span class="sxs-lookup"><span data-stu-id="0804f-760">A *fixed_parameter* consists of an optional set of *attributes* ([Attributes](attributes.md)), an optional `ref`, `out` or `this` modifier, a *type*, an *identifier* and an optional *default_argument*.</span></span> <span data-ttu-id="0804f-761">Chaque *fixed_parameter* déclare un paramètre du type donné avec le nom donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-761">Each *fixed_parameter* declares a parameter of the given type with the given name.</span></span> <span data-ttu-id="0804f-762">Le `this` modificateur désigne la méthode en tant que méthode d’extension et est uniquement autorisé sur le premier paramètre d’une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-762">The `this` modifier designates the method as an extension method and is only allowed on the first parameter of a static method.</span></span> <span data-ttu-id="0804f-763">Les méthodes d’extension sont décrites plus en détail dans [méthodes d’extension](classes.md#extension-methods).</span><span class="sxs-lookup"><span data-stu-id="0804f-763">Extension methods are further described in [Extension methods](classes.md#extension-methods).</span></span>

<span data-ttu-id="0804f-764">Un *fixed_parameter* avec un *default_argument* est connu sous le nom de ***paramètre facultatif***, alors qu’un *fixed_parameter* sans *default_argument* est un ***paramètre obligatoire***.</span><span class="sxs-lookup"><span data-stu-id="0804f-764">A *fixed_parameter* with a *default_argument* is known as an ***optional parameter***, whereas a *fixed_parameter* without a *default_argument* is a ***required parameter***.</span></span> <span data-ttu-id="0804f-765">Un paramètre obligatoire ne peut pas apparaître après un paramètre facultatif dans un *formal_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="0804f-765">A required parameter may not appear after an optional parameter in a *formal_parameter_list*.</span></span>

<span data-ttu-id="0804f-766">Un paramètre `ref` ou `out` ne peut pas avoir de *default_argument*.</span><span class="sxs-lookup"><span data-stu-id="0804f-766">A `ref` or `out` parameter cannot have a *default_argument*.</span></span> <span data-ttu-id="0804f-767">L' *expression* d’un *default_argument* doit être l’une des suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-767">The *expression* in a *default_argument* must be one of the following:</span></span>

*  <span data-ttu-id="0804f-768">a *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="0804f-768">a *constant_expression*</span></span>
*  <span data-ttu-id="0804f-769">expression de la forme `new S()` où `S` est un type valeur</span><span class="sxs-lookup"><span data-stu-id="0804f-769">an expression of the form `new S()` where `S` is a value type</span></span>
*  <span data-ttu-id="0804f-770">expression de la forme `default(S)` où `S` est un type valeur</span><span class="sxs-lookup"><span data-stu-id="0804f-770">an expression of the form `default(S)` where `S` is a value type</span></span>

<span data-ttu-id="0804f-771">L' *expression* doit être implicitement convertible à l’aide d’une conversion d’identité ou de valeurs NULL dans le type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="0804f-771">The *expression* must be implicitly convertible by an identity or nullable conversion to the type of the parameter.</span></span>

<span data-ttu-id="0804f-772">Si des paramètres facultatifs se produisent dans une déclaration de méthode partielle d’implémentation ([méthodes partielles](classes.md#partial-methods)), une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)) ou dans une déclaration d’indexeur de paramètre unique ([ Indexeurs](classes.md#indexers)) le compilateur doit fournir un avertissement, car ces membres ne peuvent jamais être appelés d’une manière qui autorise l’omission des arguments.</span><span class="sxs-lookup"><span data-stu-id="0804f-772">If optional parameters occur in an implementing partial method declaration ([Partial methods](classes.md#partial-methods)) , an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)) or in a single-parameter indexer declaration ([Indexers](classes.md#indexers)) the compiler should give a warning, since these members can never be invoked in a way that permits arguments to be omitted.</span></span>

<span data-ttu-id="0804f-773">Un *parameter_array* se compose d’un ensemble facultatif d' *attributs* ([attributs](attributes.md)), d’un modificateur `params`, d’un *array_type*et d’un *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="0804f-773">A *parameter_array* consists of an optional set of *attributes* ([Attributes](attributes.md)), a `params` modifier, an *array_type*, and an *identifier*.</span></span> <span data-ttu-id="0804f-774">Un tableau de paramètres déclare un paramètre unique du type de tableau donné avec le nom donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-774">A parameter array declares a single parameter of the given array type with the given name.</span></span> <span data-ttu-id="0804f-775">Le *array_type* d’un tableau de paramètres doit être un type tableau unidimensionnel ([types tableau](arrays.md#array-types)).</span><span class="sxs-lookup"><span data-stu-id="0804f-775">The *array_type* of a parameter array must be a single-dimensional array type ([Array types](arrays.md#array-types)).</span></span> <span data-ttu-id="0804f-776">Dans un appel de méthode, un tableau de paramètres autorise la spécification d’un seul argument du type de tableau donné, ou elle permet de spécifier zéro ou plusieurs arguments du type d’élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="0804f-776">In a method invocation, a parameter array permits either a single argument of the given array type to be specified, or it permits zero or more arguments of the array element type to be specified.</span></span> <span data-ttu-id="0804f-777">Les tableaux de paramètres sont décrits plus en détail dans les [tableaux de paramètres](classes.md#parameter-arrays).</span><span class="sxs-lookup"><span data-stu-id="0804f-777">Parameter arrays are described further in [Parameter arrays](classes.md#parameter-arrays).</span></span>

<span data-ttu-id="0804f-778">Une *parameter_array* peut se produire après un paramètre facultatif, mais elle ne peut pas avoir de valeur par défaut ; l’omission des arguments pour un *parameter_array* entraînerait la création d’un tableau vide.</span><span class="sxs-lookup"><span data-stu-id="0804f-778">A *parameter_array* may occur after an optional parameter, but cannot have a default value -- the omission of arguments for a *parameter_array* would instead result in the creation of an empty array.</span></span>

<span data-ttu-id="0804f-779">L’exemple suivant illustre différents genres de paramètres :</span><span class="sxs-lookup"><span data-stu-id="0804f-779">The following example illustrates different kinds of parameters:</span></span>
```csharp
public void M(
    ref int      i,
    decimal      d,
    bool         b = false,
    bool?        n = false,
    string       s = "Hello",
    object       o = null,
    T            t = default(T),
    params int[] a
) { }
```

<span data-ttu-id="0804f-780">Dans le *formal_parameter_list* pour `M`, `i` est un paramètre ref obligatoire, `d` est un paramètre de valeur obligatoire, `b`, `s`, `o` et `t` sont des paramètres de valeur facultatifs et `a` est un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-780">In the *formal_parameter_list* for `M`, `i` is a required ref parameter, `d` is a required value parameter, `b`, `s`, `o` and `t` are optional value parameters and `a` is a parameter array.</span></span>

<span data-ttu-id="0804f-781">Une déclaration de méthode crée un espace de déclaration distinct pour les paramètres, les paramètres de type et les variables locales.</span><span class="sxs-lookup"><span data-stu-id="0804f-781">A method declaration creates a separate declaration space for parameters, type parameters and local variables.</span></span> <span data-ttu-id="0804f-782">Les noms sont introduits dans cet espace de déclaration par la liste de paramètres de type et la liste de paramètres formels de la méthode et par les déclarations de variable locale dans le *bloc* de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-782">Names are introduced into this declaration space by the type parameter list and the formal parameter list of the method and by local variable declarations in the *block* of the method.</span></span> <span data-ttu-id="0804f-783">Il y a une erreur pour que deux membres d’un espace de déclaration de méthode aient le même nom.</span><span class="sxs-lookup"><span data-stu-id="0804f-783">It is an error for two members of a method declaration space to have the same name.</span></span> <span data-ttu-id="0804f-784">L’espace de déclaration de méthode et l’espace de déclaration de variable locale d’un espace de déclaration imbriquée doivent contenir des éléments portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="0804f-784">It is an error for the method declaration space and the local variable declaration space of a nested declaration space to contain elements with the same name.</span></span>

<span data-ttu-id="0804f-785">Un appel de méthode ([appel de méthode](expressions.md#method-invocations)) crée une copie, spécifique à cet appel, des paramètres formels et des variables locales de la méthode, et la liste d’arguments de l’appel assigne des valeurs ou des références de variable à l’objet formel nouvellement créé. paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-785">A method invocation ([Method invocations](expressions.md#method-invocations)) creates a copy, specific to that invocation, of the formal parameters and local variables of the method, and the argument list of the invocation assigns values or variable references to the newly created formal parameters.</span></span> <span data-ttu-id="0804f-786">Dans le *bloc* d’une méthode, les paramètres formels peuvent être référencés par leurs identificateurs dans les expressions *Simple_name* ([noms simples](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="0804f-786">Within the *block* of a method, formal parameters can be referenced by their identifiers in *simple_name* expressions ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="0804f-787">Il existe quatre genres de paramètres formels :</span><span class="sxs-lookup"><span data-stu-id="0804f-787">There are four kinds of formal parameters:</span></span>

*  <span data-ttu-id="0804f-788">Les paramètres de valeur, qui sont déclarés sans modificateurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-788">Value parameters, which are declared without any modifiers.</span></span>
*  <span data-ttu-id="0804f-789">Les paramètres de référence, qui sont déclarés avec le `ref` modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-789">Reference parameters, which are declared with the `ref` modifier.</span></span>
*  <span data-ttu-id="0804f-790">Les paramètres de sortie, qui sont déclarés avec le `out` modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-790">Output parameters, which are declared with the `out` modifier.</span></span>
*  <span data-ttu-id="0804f-791">Tableaux de paramètres, qui sont déclarés avec `params` le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-791">Parameter arrays, which are declared with the `params` modifier.</span></span>

<span data-ttu-id="0804f-792">Comme décrit dans [signatures et surcharge](basic-concepts.md#signatures-and-overloading), les `ref` modificateurs et `out` font partie de la signature d’une méthode, mais le `params` modificateur n’est pas.</span><span class="sxs-lookup"><span data-stu-id="0804f-792">As described in [Signatures and overloading](basic-concepts.md#signatures-and-overloading), the `ref` and `out` modifiers are part of a method's signature, but the `params` modifier is not.</span></span>

#### <a name="value-parameters"></a><span data-ttu-id="0804f-793">Paramètres de valeur</span><span class="sxs-lookup"><span data-stu-id="0804f-793">Value parameters</span></span>

<span data-ttu-id="0804f-794">Un paramètre déclaré sans modificateurs est un paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-794">A parameter declared with no modifiers is a value parameter.</span></span> <span data-ttu-id="0804f-795">Un paramètre de valeur correspond à une variable locale qui obtient sa valeur initiale de l’argument correspondant fourni dans l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-795">A value parameter corresponds to a local variable that gets its initial value from the corresponding argument supplied in the method invocation.</span></span>

<span data-ttu-id="0804f-796">Quand un paramètre formel est un paramètre de valeur, l’argument correspondant dans un appel de méthode doit être une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type de paramètre formel.</span><span class="sxs-lookup"><span data-stu-id="0804f-796">When a formal parameter is a value parameter, the corresponding argument in a method invocation must be an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the formal parameter type.</span></span>

<span data-ttu-id="0804f-797">Une méthode est autorisée à assigner de nouvelles valeurs à un paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-797">A method is permitted to assign new values to a value parameter.</span></span> <span data-ttu-id="0804f-798">Ces affectations affectent uniquement l’emplacement de stockage local représenté par le paramètre de valeur, elles n’ont aucun effet sur l’argument réel fourni dans l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-798">Such assignments only affect the local storage location represented by the value parameter—they have no effect on the actual argument given in the method invocation.</span></span>

#### <a name="reference-parameters"></a><span data-ttu-id="0804f-799">Paramètres de référence</span><span class="sxs-lookup"><span data-stu-id="0804f-799">Reference parameters</span></span>

<span data-ttu-id="0804f-800">Un paramètre déclaré avec un `ref` modificateur est un paramètre de référence.</span><span class="sxs-lookup"><span data-stu-id="0804f-800">A parameter declared with a `ref` modifier is a reference parameter.</span></span> <span data-ttu-id="0804f-801">Contrairement à un paramètre de valeur, un paramètre de référence ne crée pas un nouvel emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-801">Unlike a value parameter, a reference parameter does not create a new storage location.</span></span> <span data-ttu-id="0804f-802">Au lieu de cela, un paramètre de référence représente le même emplacement de stockage que la variable donnée comme argument dans l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-802">Instead, a reference parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="0804f-803">Quand un paramètre formel est un paramètre de référence, l’argument correspondant dans un appel de méthode doit se composer du mot clé `ref` suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que paramètre formel.</span><span class="sxs-lookup"><span data-stu-id="0804f-803">When a formal parameter is a reference parameter, the corresponding argument in a method invocation must consist of the keyword `ref` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="0804f-804">Une variable doit être assignée définitivement pour pouvoir être passée en tant que paramètre de référence.</span><span class="sxs-lookup"><span data-stu-id="0804f-804">A variable must be definitely assigned before it can be passed as a reference parameter.</span></span>

<span data-ttu-id="0804f-805">Dans une méthode, un paramètre de référence est toujours considéré comme étant définitivement assigné.</span><span class="sxs-lookup"><span data-stu-id="0804f-805">Within a method, a reference parameter is always considered definitely assigned.</span></span>

<span data-ttu-id="0804f-806">Une méthode déclarée en tant qu’itérateur ([itérateurs](classes.md#iterators)) ne peut pas avoir de paramètres de référence.</span><span class="sxs-lookup"><span data-stu-id="0804f-806">A method declared as an iterator ([Iterators](classes.md#iterators)) cannot have reference parameters.</span></span>

<span data-ttu-id="0804f-807">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-807">The example</span></span>
```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("i = {0}, j = {1}", i, j);
    }
}
```
<span data-ttu-id="0804f-808">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-808">produces the output</span></span>
```console
i = 2, j = 1
```

<span data-ttu-id="0804f-809">Pour l’appel de `Swap` dans `Main`, `x` représente `i` et `y` représente. `j`</span><span class="sxs-lookup"><span data-stu-id="0804f-809">For the invocation of `Swap` in `Main`, `x` represents `i` and `y` represents `j`.</span></span> <span data-ttu-id="0804f-810">Ainsi, l’appel a pour effet de permuter les valeurs de `i` et `j`.</span><span class="sxs-lookup"><span data-stu-id="0804f-810">Thus, the invocation has the effect of swapping the values of `i` and `j`.</span></span>

<span data-ttu-id="0804f-811">Dans une méthode qui accepte des paramètres de référence, il est possible que plusieurs noms représentent le même emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-811">In a method that takes reference parameters it is possible for multiple names to represent the same storage location.</span></span> <span data-ttu-id="0804f-812">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-812">In the example</span></span>
```csharp
class A
{
    string s;

    void F(ref string a, ref string b) {
        s = "One";
        a = "Two";
        b = "Three";
    }

    void G() {
        F(ref s, ref s);
    }
}
```
<span data-ttu-id="0804f-813">l' `F` appel de `b`dans `G` `s` passe`a` une référence à pour et.</span><span class="sxs-lookup"><span data-stu-id="0804f-813">the invocation of `F` in `G` passes a reference to `s` for both `a` and `b`.</span></span> <span data-ttu-id="0804f-814">Ainsi, pour cet appel, les `s`noms, `a`et `b` font tous référence au même emplacement de stockage, et les trois assignations modifient toutes le `s`champ d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-814">Thus, for that invocation, the names `s`, `a`, and `b` all refer to the same storage location, and the three assignments all modify the instance field `s`.</span></span>

#### <a name="output-parameters"></a><span data-ttu-id="0804f-815">Paramètres de sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-815">Output parameters</span></span>

<span data-ttu-id="0804f-816">Un paramètre déclaré avec un `out` modificateur est un paramètre de sortie.</span><span class="sxs-lookup"><span data-stu-id="0804f-816">A parameter declared with an `out` modifier is an output parameter.</span></span> <span data-ttu-id="0804f-817">À l’instar d’un paramètre de référence, un paramètre de sortie ne crée pas un nouvel emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-817">Similar to a reference parameter, an output parameter does not create a new storage location.</span></span> <span data-ttu-id="0804f-818">Au lieu de cela, un paramètre de sortie représente le même emplacement de stockage que la variable donnée comme argument dans l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-818">Instead, an output parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="0804f-819">Quand un paramètre formel est un paramètre de sortie, l’argument correspondant dans un appel de méthode doit se composer du mot clé `out` suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) du même type que le paramètre formel.</span><span class="sxs-lookup"><span data-stu-id="0804f-819">When a formal parameter is an output parameter, the corresponding argument in a method invocation must consist of the keyword `out` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="0804f-820">Une variable n’a pas besoin d’être définitivement assignée avant de pouvoir être transmise en tant que paramètre de sortie, mais après un appel où une variable a été passée comme paramètre de sortie, la variable est considérée comme assignée de manière définitive.</span><span class="sxs-lookup"><span data-stu-id="0804f-820">A variable need not be definitely assigned before it can be passed as an output parameter, but following an invocation where a variable was passed as an output parameter, the variable is considered definitely assigned.</span></span>

<span data-ttu-id="0804f-821">Dans une méthode, tout comme une variable locale, un paramètre de sortie est considéré initialement comme non assigné et doit être définitivement assigné avant que sa valeur ne soit utilisée.</span><span class="sxs-lookup"><span data-stu-id="0804f-821">Within a method, just like a local variable, an output parameter is initially considered unassigned and must be definitely assigned before its value is used.</span></span>

<span data-ttu-id="0804f-822">Chaque paramètre de sortie d’une méthode doit être assigné définitivement avant le retour de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-822">Every output parameter of a method must be definitely assigned before the method returns.</span></span>

<span data-ttu-id="0804f-823">Une méthode déclarée en tant que méthode partielle ([méthodes partielles](classes.md#partial-methods)) ou itérateur ([itérateurs](classes.md#iterators)) ne peut pas avoir de paramètres de sortie.</span><span class="sxs-lookup"><span data-stu-id="0804f-823">A method declared as a partial method ([Partial methods](classes.md#partial-methods)) or an iterator ([Iterators](classes.md#iterators)) cannot have output parameters.</span></span>

<span data-ttu-id="0804f-824">Les paramètres de sortie sont généralement utilisés dans les méthodes qui produisent plusieurs valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0804f-824">Output parameters are typically used in methods that produce multiple return values.</span></span> <span data-ttu-id="0804f-825">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-825">For example:</span></span>
```csharp
using System;

class Test
{
    static void SplitPath(string path, out string dir, out string name) {
        int i = path.Length;
        while (i > 0) {
            char ch = path[i - 1];
            if (ch == '\\' || ch == '/' || ch == ':') break;
            i--;
        }
        dir = path.Substring(0, i);
        name = path.Substring(i);
    }

    static void Main() {
        string dir, name;
        SplitPath("c:\\Windows\\System\\hello.txt", out dir, out name);
        Console.WriteLine(dir);
        Console.WriteLine(name);
    }
}
```

<span data-ttu-id="0804f-826">L’exemple produit la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-826">The example produces the output:</span></span>
```console
c:\Windows\System\
hello.txt
```

<span data-ttu-id="0804f-827">Notez que les `dir` variables `name` et peuvent être non assignées avant d’être `SplitPath`passées à, et qu’elles sont considérées comme définitivement assignées après l’appel.</span><span class="sxs-lookup"><span data-stu-id="0804f-827">Note that the `dir` and `name` variables can be unassigned before they are passed to `SplitPath`, and that they are considered definitely assigned following the call.</span></span>

#### <a name="parameter-arrays"></a><span data-ttu-id="0804f-828">Tableaux de paramètres</span><span class="sxs-lookup"><span data-stu-id="0804f-828">Parameter arrays</span></span>

<span data-ttu-id="0804f-829">Un paramètre déclaré avec un `params` modificateur est un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-829">A parameter declared with a `params` modifier is a parameter array.</span></span> <span data-ttu-id="0804f-830">Si une liste de paramètres formels comprend un tableau de paramètres, elle doit être le dernier paramètre de la liste et doit être de type tableau unidimensionnel.</span><span class="sxs-lookup"><span data-stu-id="0804f-830">If a formal parameter list includes a parameter array, it must be the last parameter in the list and it must be of a single-dimensional array type.</span></span> <span data-ttu-id="0804f-831">Par exemple, les types `string[]` et `string[][]` peuvent être utilisés comme type d’un tableau de paramètres, mais le type `string[,]` ne peut pas.</span><span class="sxs-lookup"><span data-stu-id="0804f-831">For example, the types `string[]` and `string[][]` can be used as the type of a parameter array, but the type `string[,]` can not.</span></span> <span data-ttu-id="0804f-832">Il n’est pas possible de combiner `params` le modificateur avec les `ref` modificateurs `out`et.</span><span class="sxs-lookup"><span data-stu-id="0804f-832">It is not possible to combine the `params` modifier with the modifiers `ref` and `out`.</span></span>

<span data-ttu-id="0804f-833">Un tableau de paramètres permet de spécifier des arguments de l’une des deux manières suivantes dans un appel de méthode :</span><span class="sxs-lookup"><span data-stu-id="0804f-833">A parameter array permits arguments to be specified in one of two ways in a method invocation:</span></span>

*  <span data-ttu-id="0804f-834">L’argument donné pour un tableau de paramètres peut être une expression unique implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-834">The argument given for a parameter array can be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="0804f-835">Dans ce cas, le tableau de paramètres agit précisément comme un paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-835">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="0804f-836">L’appel peut également spécifier zéro ou plusieurs arguments pour le tableau de paramètres, où chaque argument est une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type d’élément du tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-836">Alternatively, the invocation can specify zero or more arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="0804f-837">Dans ce cas, l’appel crée une instance du type de tableau de paramètres avec une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument données et utilise l’instance de tableau nouvellement créée comme argument.</span><span class="sxs-lookup"><span data-stu-id="0804f-837">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="0804f-838">À l’exception de l’autorisation d’un nombre variable d’arguments dans un appel, un tableau de paramètres équivaut précisément à un paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)) du même type.</span><span class="sxs-lookup"><span data-stu-id="0804f-838">Except for allowing a variable number of arguments in an invocation, a parameter array is precisely equivalent to a value parameter ([Value parameters](classes.md#value-parameters)) of the same type.</span></span>

<span data-ttu-id="0804f-839">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-839">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params int[] args) {
        Console.Write("Array contains {0} elements:", args.Length);
        foreach (int i in args) 
            Console.Write(" {0}", i);
        Console.WriteLine();
    }

    static void Main() {
        int[] arr = {1, 2, 3};
        F(arr);
        F(10, 20, 30, 40);
        F();
    }
}
```
<span data-ttu-id="0804f-840">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-840">produces the output</span></span>
```console
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

<span data-ttu-id="0804f-841">La première invocation de `F` passe simplement le tableau `a` en tant que paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-841">The first invocation of `F` simply passes the array `a` as a value parameter.</span></span> <span data-ttu-id="0804f-842">Le deuxième appel de `F` crée automatiquement un élément à quatre éléments `int[]` avec les valeurs d’élément données et passe cette instance de tableau en tant que paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-842">The second invocation of `F` automatically creates a four-element `int[]` with the given element values and passes that array instance as a value parameter.</span></span> <span data-ttu-id="0804f-843">De même, le troisième appel de `F` crée un élément `int[]` de zéro et passe cette instance en tant que paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-843">Likewise, the third invocation of `F` creates a zero-element `int[]` and passes that instance as a value parameter.</span></span> <span data-ttu-id="0804f-844">Le deuxième et le troisième appel sont exactement équivalents à l’écriture :</span><span class="sxs-lookup"><span data-stu-id="0804f-844">The second and third invocations are precisely equivalent to writing:</span></span>
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

<span data-ttu-id="0804f-845">Lors de l’exécution de la résolution de surcharge, une méthode avec un tableau de paramètres peut s’appliquer soit dans sa forme normale, soit dans sa forme développée ([fonction membre applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="0804f-845">When performing overload resolution, a method with a parameter array may be applicable either in its normal form or in its expanded form ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="0804f-846">La forme développée d’une méthode est disponible uniquement si la forme normale de la méthode n’est pas applicable et uniquement si une méthode applicable avec la même signature que la forme développée n’est pas déjà déclarée dans le même type.</span><span class="sxs-lookup"><span data-stu-id="0804f-846">The expanded form of a method is available only if the normal form of the method is not applicable and only if an applicable method with the same signature as the expanded form is not already declared in the same type.</span></span>

<span data-ttu-id="0804f-847">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-847">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params object[] a) {
        Console.WriteLine("F(object[])");
    }

    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object a0, object a1) {
        Console.WriteLine("F(object,object)");
    }

    static void Main() {
        F();
        F(1);
        F(1, 2);
        F(1, 2, 3);
        F(1, 2, 3, 4);
    }
}
```
<span data-ttu-id="0804f-848">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-848">produces the output</span></span>
```console
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

<span data-ttu-id="0804f-849">Dans l’exemple, deux des formes étendues possibles de la méthode avec un tableau de paramètres sont déjà incluses dans la classe en tant que méthodes normales.</span><span class="sxs-lookup"><span data-stu-id="0804f-849">In the example, two of the possible expanded forms of the method with a parameter array are already included in the class as regular methods.</span></span> <span data-ttu-id="0804f-850">Ces formulaires développés ne sont donc pas pris en compte lors de l’exécution de la résolution de surcharge, et les premier et troisième appels de méthode sélectionnent donc les méthodes normales.</span><span class="sxs-lookup"><span data-stu-id="0804f-850">These expanded forms are therefore not considered when performing overload resolution, and the first and third method invocations thus select the regular methods.</span></span> <span data-ttu-id="0804f-851">Quand une classe déclare une méthode avec un tableau de paramètres, il n’est pas rare d’inclure également certains des formulaires développés comme méthodes normales.</span><span class="sxs-lookup"><span data-stu-id="0804f-851">When a class declares a method with a parameter array, it is not uncommon to also include some of the expanded forms as regular methods.</span></span> <span data-ttu-id="0804f-852">En procédant ainsi, il est possible d’éviter l’allocation d’une instance de tableau qui se produit lorsqu’une forme développée d’une méthode avec un tableau de paramètres est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-852">By doing so it is possible to avoid the allocation of an array instance that occurs when an expanded form of a method with a parameter array is invoked.</span></span>

<span data-ttu-id="0804f-853">Lorsque le type d’un tableau de paramètres `object[]`est, une ambiguïté potentielle se produit entre la forme normale de la méthode et la forme étendue pour un paramètre `object` unique.</span><span class="sxs-lookup"><span data-stu-id="0804f-853">When the type of a parameter array is `object[]`, a potential ambiguity arises between the normal form of the method and the expended form for a single `object` parameter.</span></span> <span data-ttu-id="0804f-854">L’ambiguïté est due au fait qu’un `object[]` est lui-même convertible implicitement en type. `object`</span><span class="sxs-lookup"><span data-stu-id="0804f-854">The reason for the ambiguity is that an `object[]` is itself implicitly convertible to type `object`.</span></span> <span data-ttu-id="0804f-855">Toutefois, l’ambiguïté ne présente aucun problème, car elle peut être résolue en insérant un cast si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-855">The ambiguity presents no problem, however, since it can be resolved by inserting a cast if needed.</span></span>

<span data-ttu-id="0804f-856">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-856">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params object[] args) {
        foreach (object o in args) {
            Console.Write(o.GetType().FullName);
            Console.Write(" ");
        }
        Console.WriteLine();
    }

    static void Main() {
        object[] a = {1, "Hello", 123.456};
        object o = a;
        F(a);
        F((object)a);
        F(o);
        F((object[])o);
    }
}
```
<span data-ttu-id="0804f-857">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-857">produces the output</span></span>
```console
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

<span data-ttu-id="0804f-858">Dans le premier et le dernier appel de `F`, la forme normale de `F` est applicable, car il existe une conversion implicite du type d’argument vers le type de paramètre (les `object[]`deux sont de type).</span><span class="sxs-lookup"><span data-stu-id="0804f-858">In the first and last invocations of `F`, the normal form of `F` is applicable because an implicit conversion exists from the argument type to the parameter type (both are of type `object[]`).</span></span> <span data-ttu-id="0804f-859">Ainsi, la résolution de surcharge sélectionne la forme `F`normale de et l’argument est passé en tant que paramètre de valeur régulière.</span><span class="sxs-lookup"><span data-stu-id="0804f-859">Thus, overload resolution selects the normal form of `F`, and the argument is passed as a regular value parameter.</span></span> <span data-ttu-id="0804f-860">Dans le deuxième et le troisième appel, la forme normale de `F` n’est pas applicable, car il n’existe aucune conversion implicite du type d’argument vers le type de paramètre `object` (le type ne `object[]`peut pas être converti implicitement en type).</span><span class="sxs-lookup"><span data-stu-id="0804f-860">In the second and third invocations, the normal form of `F` is not applicable because no implicit conversion exists from the argument type to the parameter type (type `object` cannot be implicitly converted to type `object[]`).</span></span> <span data-ttu-id="0804f-861">Toutefois, la forme développée de `F` est applicable, donc elle est sélectionnée par la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="0804f-861">However, the expanded form of `F` is applicable, so it is selected by overload resolution.</span></span> <span data-ttu-id="0804f-862">Par conséquent, un élément `object[]` unique est créé par l’appel, et l’élément unique du tableau est initialisé avec la valeur d’argument donnée (qui est elle-même une référence à un `object[]`).</span><span class="sxs-lookup"><span data-stu-id="0804f-862">As a result, a one-element `object[]` is created by the invocation, and the single element of the array is initialized with the given argument value (which itself is a reference to an `object[]`).</span></span>

### <a name="static-and-instance-methods"></a><span data-ttu-id="0804f-863">Méthodes statiques et d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-863">Static and instance methods</span></span>

<span data-ttu-id="0804f-864">Lorsqu’une déclaration de méthode comprend `static` un modificateur, cette méthode est dite statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-864">When a method declaration includes a `static` modifier, that method is said to be a static method.</span></span> <span data-ttu-id="0804f-865">Quand aucun `static` modificateur n’est présent, la méthode est considérée comme une méthode d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-865">When no `static` modifier is present, the method is said to be an instance method.</span></span>

<span data-ttu-id="0804f-866">Une méthode statique ne fonctionne pas sur une instance spécifique, et il s’agit d’une erreur de compilation pour faire `this` référence à dans une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-866">A static method does not operate on a specific instance, and it is a compile-time error to refer to `this` in a static method.</span></span>

<span data-ttu-id="0804f-867">Une méthode d’instance opère sur une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-867">An instance method operates on a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="0804f-868">Lorsqu’une méthode est référencée dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est une méthode statique, `E` doit désigner un type contenant `M`, et si `M` est une méthode d’instance, `E` doit désigner une instance d’un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-868">When a method is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static method, `E` must denote a type containing `M`, and if `M` is an instance method, `E` must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="0804f-869">Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="0804f-869">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-methods"></a><span data-ttu-id="0804f-870">Méthodes virtuelles</span><span class="sxs-lookup"><span data-stu-id="0804f-870">Virtual methods</span></span>

<span data-ttu-id="0804f-871">Lorsqu’une déclaration de méthode d’instance `virtual` comprend un modificateur, cette méthode est dite méthode virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-871">When an instance method declaration includes a `virtual` modifier, that method is said to be a virtual method.</span></span> <span data-ttu-id="0804f-872">Quand aucun `virtual` modificateur n’est présent, la méthode est dite non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-872">When no `virtual` modifier is present, the method is said to be a non-virtual method.</span></span>

<span data-ttu-id="0804f-873">L’implémentation d’une méthode non virtuelle est indifférente : L’implémentation est la même que la méthode soit appelée sur une instance de la classe dans laquelle elle est déclarée ou d’une instance d’une classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="0804f-873">The implementation of a non-virtual method is invariant: The implementation is the same whether the method is invoked on an instance of the class in which it is declared or an instance of a derived class.</span></span> <span data-ttu-id="0804f-874">En revanche, l’implémentation d’une méthode virtuelle peut être remplacée par des classes dérivées.</span><span class="sxs-lookup"><span data-stu-id="0804f-874">In contrast, the implementation of a virtual method can be superseded by derived classes.</span></span> <span data-ttu-id="0804f-875">Le processus de remplacement de l’implémentation d’une méthode virtuelle héritée est connu sous le nom de ***substitution*** de cette méthode ([méthodes override](classes.md#override-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-875">The process of superseding the implementation of an inherited virtual method is known as ***overriding*** that method ([Override methods](classes.md#override-methods)).</span></span>

<span data-ttu-id="0804f-876">Dans un appel de méthode virtuelle, le ***type au moment*** de l’exécution de l’instance pour laquelle cet appel se produit détermine l’implémentation de méthode réelle à appeler.</span><span class="sxs-lookup"><span data-stu-id="0804f-876">In a virtual method invocation, the ***run-time type*** of the instance for which that invocation takes place determines the actual method implementation to invoke.</span></span> <span data-ttu-id="0804f-877">Dans un appel de méthode non virtuel, le ***type au moment*** de la compilation de l’instance est le facteur déterminant.</span><span class="sxs-lookup"><span data-stu-id="0804f-877">In a non-virtual method invocation, the ***compile-time type*** of the instance is the determining factor.</span></span> <span data-ttu-id="0804f-878">En termes précis, quand une méthode nommée `N` est appelée avec une liste `A` d’arguments sur une instance avec un type `C` au moment de la compilation et un type `R` au moment de `R` l' `C` exécution (où est ou une classe dérivée à `C`partir de), l’appel est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-878">In precise terms, when a method named `N` is invoked with an argument list `A` on an instance with a compile-time type `C` and a run-time type `R` (where `R` is either `C` or a class derived from `C`), the invocation is processed as follows:</span></span>

*  <span data-ttu-id="0804f-879">Tout d’abord, la résolution de `C`surcharge `N`est appliquée `A`à, et, pour sélectionner `M` une méthode spécifique à partir de l’ensemble de méthodes `C`déclaré dans et héritée par.</span><span class="sxs-lookup"><span data-stu-id="0804f-879">First, overload resolution is applied to `C`, `N`, and `A`, to select a specific method `M` from the set of methods declared in and inherited by `C`.</span></span> <span data-ttu-id="0804f-880">Cela est décrit dans [appels de méthode](expressions.md#method-invocations).</span><span class="sxs-lookup"><span data-stu-id="0804f-880">This is described in [Method invocations](expressions.md#method-invocations).</span></span>
*  <span data-ttu-id="0804f-881">Ensuite, si `M` est une méthode non virtuelle, `M` est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-881">Then, if `M` is a non-virtual method, `M` is invoked.</span></span>
*  <span data-ttu-id="0804f-882">Sinon, `M` est une méthode virtuelle et l’implémentation la plus dérivée `M` de par rapport `R` à est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-882">Otherwise, `M` is a virtual method, and the most derived implementation of `M` with respect to `R` is invoked.</span></span>

<span data-ttu-id="0804f-883">Pour chaque méthode virtuelle déclarée dans ou héritée par une classe, il existe une ***implémentation la plus dérivée*** de la méthode par rapport à cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-883">For every virtual method declared in or inherited by a class, there exists a ***most derived implementation*** of the method with respect to that class.</span></span> <span data-ttu-id="0804f-884">L’implémentation la plus dérivée d’une `M` méthode virtuelle en ce qui `R` concerne une classe est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-884">The most derived implementation of a virtual method `M` with respect to a class `R` is determined as follows:</span></span>

*  <span data-ttu-id="0804f-885">Si `R` contient la Déclaration `virtual` d’introduction `M`de, il s’agit de l’implémentation la `M`plus dérivée de.</span><span class="sxs-lookup"><span data-stu-id="0804f-885">If `R` contains the introducing `virtual` declaration of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="0804f-886">Sinon, si `R` contient un `override` de `M`, il s’agit de l’implémentation la plus `M`dérivée de.</span><span class="sxs-lookup"><span data-stu-id="0804f-886">Otherwise, if `R` contains an `override` of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="0804f-887">Sinon, l' `M` implémentation la plus dérivée de par rapport à `R` est identique à l’implémentation la plus `M` dérivée de par rapport à la classe `R`de base directe de.</span><span class="sxs-lookup"><span data-stu-id="0804f-887">Otherwise, the most derived implementation of `M` with respect to `R` is the same as the most derived implementation of `M` with respect to the direct base class of `R`.</span></span>

<span data-ttu-id="0804f-888">L’exemple suivant illustre les différences entre les méthodes virtuelles et non virtuelles :</span><span class="sxs-lookup"><span data-stu-id="0804f-888">The following example illustrates the differences between virtual and non-virtual methods:</span></span>
```csharp
using System;

class A
{
    public void F() { Console.WriteLine("A.F"); }

    public virtual void G() { Console.WriteLine("A.G"); }
}

class B: A
{
    new public void F() { Console.WriteLine("B.F"); }

    public override void G() { Console.WriteLine("B.G"); }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        a.F();
        b.F();
        a.G();
        b.G();
    }
}
```

<span data-ttu-id="0804f-889">Dans l’exemple, `A` introduit une méthode `F` non virtuelle et une méthode `G`virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-889">In the example, `A` introduces a non-virtual method `F` and a virtual method `G`.</span></span> <span data-ttu-id="0804f-890">La classe `B` introduit une nouvelle méthode `F`non virtuelle, masquant ainsi la héritée `F`et substitue également la méthode `G`héritée.</span><span class="sxs-lookup"><span data-stu-id="0804f-890">The class `B` introduces a new non-virtual method `F`, thus hiding the inherited `F`, and also overrides the inherited method `G`.</span></span> <span data-ttu-id="0804f-891">L’exemple produit la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-891">The example produces the output:</span></span>
```console
A.F
B.F
B.G
B.G
```

<span data-ttu-id="0804f-892">Notez que l’instruction `a.G()` `B.G`appelle, et non `A.G`.</span><span class="sxs-lookup"><span data-stu-id="0804f-892">Notice that the statement `a.G()` invokes `B.G`, not `A.G`.</span></span> <span data-ttu-id="0804f-893">Cela est dû au fait que le type au moment de l’exécution de `B`l’instance (qui est), et non le type au moment de `A`la compilation de l’instance (c’est-à-dire), détermine l’implémentation réelle de la méthode à appeler.</span><span class="sxs-lookup"><span data-stu-id="0804f-893">This is because the run-time type of the instance (which is `B`), not the compile-time type of the instance (which is `A`), determines the actual method implementation to invoke.</span></span>

<span data-ttu-id="0804f-894">Étant donné que les méthodes sont autorisées à masquer les méthodes héritées, il est possible qu’une classe contienne plusieurs méthodes virtuelles avec la même signature.</span><span class="sxs-lookup"><span data-stu-id="0804f-894">Because methods are allowed to hide inherited methods, it is possible for a class to contain several virtual methods with the same signature.</span></span> <span data-ttu-id="0804f-895">Cela ne présente pas de problème d’ambiguïté, puisque toutes les méthodes sauf la plus dérivée sont masquées.</span><span class="sxs-lookup"><span data-stu-id="0804f-895">This does not present an ambiguity problem, since all but the most derived method are hidden.</span></span> <span data-ttu-id="0804f-896">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-896">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() { Console.WriteLine("A.F"); }
}

class B: A
{
    public override void F() { Console.WriteLine("B.F"); }
}

class C: B
{
    new public virtual void F() { Console.WriteLine("C.F"); }
}

class D: C
{
    public override void F() { Console.WriteLine("D.F"); }
}

class Test
{
    static void Main() {
        D d = new D();
        A a = d;
        B b = d;
        C c = d;
        a.F();
        b.F();
        c.F();
        d.F();
    }
}
```
<span data-ttu-id="0804f-897">les `C` classes `D` et contiennent deux méthodes virtuelles avec la même signature : Celui introduit par `A` et celui introduit par `C`.</span><span class="sxs-lookup"><span data-stu-id="0804f-897">the `C` and `D` classes contain two virtual methods with the same signature: The one introduced by `A` and the one introduced by `C`.</span></span> <span data-ttu-id="0804f-898">La méthode introduite `C` par masque la méthode héritée `A`de.</span><span class="sxs-lookup"><span data-stu-id="0804f-898">The method introduced by `C` hides the method inherited from `A`.</span></span> <span data-ttu-id="0804f-899">Ainsi, la `D` déclaration override de substitue la méthode introduite par `C`, et il n’est pas possible pour `D` de substituer la méthode introduite `A`par.</span><span class="sxs-lookup"><span data-stu-id="0804f-899">Thus, the override declaration in `D` overrides the method introduced by `C`, and it is not possible for `D` to override the method introduced by `A`.</span></span> <span data-ttu-id="0804f-900">L’exemple produit la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-900">The example produces the output:</span></span>
```console
B.F
B.F
D.F
D.F
```

<span data-ttu-id="0804f-901">Notez qu’il est possible d’appeler la méthode virtuelle masquée en accédant à une `D` instance de via un type moins dérivé dans lequel la méthode n’est pas masquée.</span><span class="sxs-lookup"><span data-stu-id="0804f-901">Note that it is possible to invoke the hidden virtual method by accessing an instance of `D` through a less derived type in which the method is not hidden.</span></span>

### <a name="override-methods"></a><span data-ttu-id="0804f-902">Substituer les méthodes</span><span class="sxs-lookup"><span data-stu-id="0804f-902">Override methods</span></span>

<span data-ttu-id="0804f-903">Lorsqu’une déclaration de méthode d’instance `override` comprend un modificateur, la méthode est considérée comme une ***méthode de substitution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-903">When an instance method declaration includes an `override` modifier, the method is said to be an ***override method***.</span></span> <span data-ttu-id="0804f-904">Une méthode override remplace une méthode virtuelle héritée avec la même signature.</span><span class="sxs-lookup"><span data-stu-id="0804f-904">An override method overrides an inherited virtual method with the same signature.</span></span> <span data-ttu-id="0804f-905">Là où une déclaration de méthode virtuelle présente une nouvelle méthode, une déclaration de méthode de substitution spécialise une méthode virtuelle héritée existante en fournissant une nouvelle implémentation de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-905">Whereas a virtual method declaration introduces a new method, an override method declaration specializes an existing inherited virtual method by providing a new implementation of that method.</span></span>

<span data-ttu-id="0804f-906">La méthode substituée par une `override` déclaration est connue sous le nom de ***méthode de base substituée***.</span><span class="sxs-lookup"><span data-stu-id="0804f-906">The method overridden by an `override` declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="0804f-907">Pour `M` une méthode de substitution déclarée dans une `C`classe, la méthode de base substituée est déterminée en examinant chaque type `C`de classe de base de, en commençant par `C` le type de classe de base directe de et en continuant avec chaque type de classe de base directe, jusqu’à ce que dans un type de classe de base donné, au moins une méthode accessible `M` soit localisée avec la même signature qu’après la substitution des arguments de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-907">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class type of `C`, starting with the direct base class type of `C` and continuing with each successive direct base class type, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span> <span data-ttu-id="0804f-908">Dans le cadre de la recherche de la méthode de base substituée, une méthode est considérée comme accessible `public`si `protected` `protected internal`c’est le cas, si c’est le cas, ou `internal` si elle est et est déclarée `C`dans le même programme que.</span><span class="sxs-lookup"><span data-stu-id="0804f-908">For the purposes of locating the overridden base method, a method is considered accessible if it is `public`, if it is `protected`, if it is `protected internal`, or if it is `internal` and declared in the same program as `C`.</span></span>

<span data-ttu-id="0804f-909">Une erreur de compilation se produit sauf si toutes les conditions suivantes sont vraies pour une déclaration override :</span><span class="sxs-lookup"><span data-stu-id="0804f-909">A compile-time error occurs unless all of the following are true for an override declaration:</span></span>

*  <span data-ttu-id="0804f-910">Une méthode de base substituée peut être localisée comme décrit ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0804f-910">An overridden base method can be located as described above.</span></span>
*  <span data-ttu-id="0804f-911">Il existe exactement une méthode de base substituée.</span><span class="sxs-lookup"><span data-stu-id="0804f-911">There is exactly one such overridden base method.</span></span> <span data-ttu-id="0804f-912">Cette restriction n’a d’effet que si le type de classe de base est un type construit où la substitution d’arguments de type rend la signature de deux méthodes identique.</span><span class="sxs-lookup"><span data-stu-id="0804f-912">This restriction has effect only if the base class type is a constructed type where the substitution of type arguments makes the signature of two methods the same.</span></span>
*  <span data-ttu-id="0804f-913">La méthode de base substituée est une méthode virtuelle, abstraite ou de substitution.</span><span class="sxs-lookup"><span data-stu-id="0804f-913">The overridden base method is a virtual, abstract, or override method.</span></span> <span data-ttu-id="0804f-914">En d’autres termes, la méthode de base substituée ne peut pas être statique ou non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-914">In other words, the overridden base method cannot be static or non-virtual.</span></span>
*  <span data-ttu-id="0804f-915">La méthode de base substituée n’est pas une méthode sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-915">The overridden base method is not a sealed method.</span></span>
*  <span data-ttu-id="0804f-916">La méthode de substitution et la méthode de base substituée ont le même type de retour.</span><span class="sxs-lookup"><span data-stu-id="0804f-916">The override method and the overridden base method have the same return type.</span></span>
*  <span data-ttu-id="0804f-917">La déclaration override et la méthode de base substituée ont la même accessibilité déclarée.</span><span class="sxs-lookup"><span data-stu-id="0804f-917">The override declaration and the overridden base method have the same declared accessibility.</span></span> <span data-ttu-id="0804f-918">En d’autres termes, une déclaration override ne peut pas modifier l’accessibilité de la méthode virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-918">In other words, an override declaration cannot change the accessibility of the virtual method.</span></span> <span data-ttu-id="0804f-919">Toutefois, si la méthode de base substituée est protégée interne et qu’elle est déclarée dans un assembly différent de l’assembly contenant la méthode de substitution, l’accessibilité déclarée de la méthode de substitution doit être protégée.</span><span class="sxs-lookup"><span data-stu-id="0804f-919">However, if the overridden base method is protected internal and it is declared in a different assembly than the assembly containing the override method then the override method's declared accessibility must be protected.</span></span>
*  <span data-ttu-id="0804f-920">La déclaration override ne spécifie pas de clauses de type-Parameter-Constraints.</span><span class="sxs-lookup"><span data-stu-id="0804f-920">The override declaration does not specify type-parameter-constraints-clauses.</span></span> <span data-ttu-id="0804f-921">Au lieu de cela, les contraintes sont héritées de la méthode de base substituée.</span><span class="sxs-lookup"><span data-stu-id="0804f-921">Instead the constraints are inherited from the overridden base method.</span></span> <span data-ttu-id="0804f-922">Notez que les contraintes qui sont des paramètres de type dans la méthode substituée peuvent être remplacées par des arguments de type dans la contrainte héritée.</span><span class="sxs-lookup"><span data-stu-id="0804f-922">Note that constraints that are type parameters in the overridden method may be replaced by type arguments in the inherited constraint.</span></span> <span data-ttu-id="0804f-923">Cela peut entraîner des contraintes qui ne sont pas autorisées quand elles sont explicitement spécifiées, telles que des types valeur ou des types sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-923">This can lead to constraints that are not legal when explicitly specified, such as value types or sealed types.</span></span>

<span data-ttu-id="0804f-924">L’exemple suivant montre comment les règles de substitution fonctionnent pour les classes génériques :</span><span class="sxs-lookup"><span data-stu-id="0804f-924">The following example demonstrates how the overriding rules work for generic classes:</span></span>
```csharp
abstract class C<T>
{
    public virtual T F() {...}
    public virtual C<T> G() {...}
    public virtual void H(C<T> x) {...}
}

class D: C<string>
{
    public override string F() {...}            // Ok
    public override C<string> G() {...}         // Ok
    public override void H(C<T> x) {...}        // Error, should be C<string>
}

class E<T,U>: C<U>
{
    public override U F() {...}                 // Ok
    public override C<U> G() {...}              // Ok
    public override void H(C<T> x) {...}        // Error, should be C<U>
}
```

<span data-ttu-id="0804f-925">Une déclaration override peut accéder à la méthode de base substituée à l’aide d’un *base_access* ([accès de base](expressions.md#base-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-925">An override declaration can access the overridden base method using a *base_access* ([Base access](expressions.md#base-access)).</span></span> <span data-ttu-id="0804f-926">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-926">In the example</span></span>
```csharp
class A
{
    int x;

    public virtual void PrintFields() {
        Console.WriteLine("x = {0}", x);
    }
}

class B: A
{
    int y;

    public override void PrintFields() {
        base.PrintFields();
        Console.WriteLine("y = {0}", y);
    }
}
```
<span data-ttu-id="0804f-927">l' `base.PrintFields()` appel dans `B` appelle la `PrintFields` méthode déclarée dans `A`.</span><span class="sxs-lookup"><span data-stu-id="0804f-927">the `base.PrintFields()` invocation in `B` invokes the `PrintFields` method declared in `A`.</span></span> <span data-ttu-id="0804f-928">Un *base_access* désactive le mécanisme d’appel virtuel et traite simplement la méthode de base comme une méthode non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-928">A *base_access* disables the virtual invocation mechanism and simply treats the base method as a non-virtual method.</span></span> <span data-ttu-id="0804f-929">Si l' `B` appel a été écrit `((A)this).PrintFields()`, il appellerait de manière récursive la `PrintFields` méthode déclarée dans `B`, pas celle qui est déclarée dans `PrintFields` `A`, car est virtuel et le type d’exécution de `((A)this)` est`B`de.</span><span class="sxs-lookup"><span data-stu-id="0804f-929">Had the invocation in `B` been written `((A)this).PrintFields()`, it would recursively invoke the `PrintFields` method declared in `B`, not the one declared in `A`, since `PrintFields` is virtual and the run-time type of `((A)this)` is `B`.</span></span>

<span data-ttu-id="0804f-930">Une méthode ne peut `override` se substituer à une autre méthode qu’en incluant un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-930">Only by including an `override` modifier can a method override another method.</span></span> <span data-ttu-id="0804f-931">Dans tous les autres cas, une méthode avec la même signature qu’une méthode héritée masque simplement la méthode héritée.</span><span class="sxs-lookup"><span data-stu-id="0804f-931">In all other cases, a method with the same signature as an inherited method simply hides the inherited method.</span></span> <span data-ttu-id="0804f-932">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-932">In the example</span></span>
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    public virtual void F() {}        // Warning, hiding inherited F()
}
```
<span data-ttu-id="0804f-933">la `F` méthode dans `B` n’inclut `override` pas de modificateur et, par conséquent, ne substitue pas `A`la `F` méthode dans.</span><span class="sxs-lookup"><span data-stu-id="0804f-933">the `F` method in `B` does not include an `override` modifier and therefore does not override the `F` method in `A`.</span></span> <span data-ttu-id="0804f-934">Au lieu de `F` cela, `B` la méthode dans masque la `A`méthode dans et un avertissement est signalé, car la déclaration n’inclut `new` pas de modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-934">Rather, the `F` method in `B` hides the method in `A`, and a warning is reported because the declaration does not include a `new` modifier.</span></span>

<span data-ttu-id="0804f-935">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-935">In the example</span></span>
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    new private void F() {}        // Hides A.F within body of B
}

class C: B
{
    public override void F() {}    // Ok, overrides A.F
}
```
<span data-ttu-id="0804f-936">la `F` méthode dans `B` masque la méthode virtuelle `F` héritée de `A`.</span><span class="sxs-lookup"><span data-stu-id="0804f-936">the `F` method in `B` hides the virtual `F` method inherited from `A`.</span></span> <span data-ttu-id="0804f-937">Étant donné que `F` le `B` nouveau dans a un accès privé, son étendue inclut uniquement le `B` corps de classe de et `C`n’étend pas à.</span><span class="sxs-lookup"><span data-stu-id="0804f-937">Since the new `F` in `B` has private access, its scope only includes the class body of `B` and does not extend to `C`.</span></span> <span data-ttu-id="0804f-938">Par conséquent, la Déclaration `F` de `C` dans est autorisée à substituer `F` l’héritage `A`de.</span><span class="sxs-lookup"><span data-stu-id="0804f-938">Therefore, the declaration of `F` in `C` is permitted to override the `F` inherited from `A`.</span></span>

### <a name="sealed-methods"></a><span data-ttu-id="0804f-939">Méthodes sealed</span><span class="sxs-lookup"><span data-stu-id="0804f-939">Sealed methods</span></span>

<span data-ttu-id="0804f-940">Lorsqu’une déclaration de méthode d’instance `sealed` comprend un modificateur, cette méthode est dite ***sealed***.</span><span class="sxs-lookup"><span data-stu-id="0804f-940">When an instance method declaration includes a `sealed` modifier, that method is said to be a ***sealed method***.</span></span> <span data-ttu-id="0804f-941">Si une déclaration de méthode d’instance `sealed` inclut le modificateur, elle doit également `override` inclure le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-941">If an instance method declaration includes the  `sealed` modifier, it must also include the `override` modifier.</span></span> <span data-ttu-id="0804f-942">L' `sealed` utilisation du modificateur empêche une classe dérivée de substituer davantage la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-942">Use of the `sealed` modifier prevents a derived class from further overriding the method.</span></span>

<span data-ttu-id="0804f-943">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-943">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }

    public virtual void G() {
        Console.WriteLine("A.G");
    }
}

class B: A
{
    sealed override public void F() {
        Console.WriteLine("B.F");
    } 

    override public void G() {
        Console.WriteLine("B.G");
    } 
}

class C: B
{
    override public void G() {
        Console.WriteLine("C.G");
    } 
}
```
<span data-ttu-id="0804f-944">la classe `B` fournit deux méthodes override : une `F` méthode qui a le modificateur et une `G` méthode qui ne le `sealed` fait pas.</span><span class="sxs-lookup"><span data-stu-id="0804f-944">the class `B` provides two override methods: an `F` method that has the `sealed` modifier and a `G` method that does not.</span></span> <span data-ttu-id="0804f-945">`B`l’utilisation du sealed `modifier` empêche `C` toute substitution `F`supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-945">`B`'s use of the sealed `modifier` prevents `C` from further overriding `F`.</span></span>

### <a name="abstract-methods"></a><span data-ttu-id="0804f-946">Méthodes abstraites</span><span class="sxs-lookup"><span data-stu-id="0804f-946">Abstract methods</span></span>

<span data-ttu-id="0804f-947">Lorsqu’une déclaration de méthode d’instance `abstract` comprend un modificateur, cette méthode est dite ***abstraite***.</span><span class="sxs-lookup"><span data-stu-id="0804f-947">When an instance method declaration includes an `abstract` modifier, that method is said to be an ***abstract method***.</span></span> <span data-ttu-id="0804f-948">Bien qu’une méthode abstraite soit implicitement également une méthode virtuelle, elle ne peut pas `virtual`avoir de modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-948">Although an abstract method is implicitly also a virtual method, it cannot have the modifier `virtual`.</span></span>

<span data-ttu-id="0804f-949">Une déclaration de méthode abstraite introduit une nouvelle méthode virtuelle, mais ne fournit pas d’implémentation de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-949">An abstract method declaration introduces a new virtual method but does not provide an implementation of that method.</span></span> <span data-ttu-id="0804f-950">Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation en substituant cette méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-950">Instead, non-abstract derived classes are required to provide their own implementation by overriding that method.</span></span> <span data-ttu-id="0804f-951">Étant donné qu’une méthode abstraite ne fournit pas d’implémentation réelle, le *method_body* d’une méthode abstraite se compose simplement d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-951">Because an abstract method provides no actual implementation, the *method_body* of an abstract method simply consists of a semicolon.</span></span>

<span data-ttu-id="0804f-952">Les déclarations de méthode abstraite sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="0804f-952">Abstract method declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="0804f-953">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-953">In the example</span></span>
```csharp
public abstract class Shape
{
    public abstract void Paint(Graphics g, Rectangle r);
}

public class Ellipse: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawEllipse(r);
    }
}

public class Box: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawRect(r);
    }
}
```
<span data-ttu-id="0804f-954">la `Shape` classe définit la notion abstraite d’un objet de forme géométrique qui peut se peindre lui-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-954">the `Shape` class defines the abstract notion of a geometrical shape object that can paint itself.</span></span> <span data-ttu-id="0804f-955">La `Paint` méthode est abstraite, car il n’y a pas d’implémentation significative par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-955">The `Paint` method is abstract because there is no meaningful default implementation.</span></span> <span data-ttu-id="0804f-956">Les `Ellipse` classes `Box` et sont des `Shape` implémentations concrètes.</span><span class="sxs-lookup"><span data-stu-id="0804f-956">The `Ellipse` and `Box` classes are concrete `Shape` implementations.</span></span> <span data-ttu-id="0804f-957">Étant donné que ces classes ne sont pas abstraites, elles doivent substituer `Paint` la méthode et fournir une implémentation réelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-957">Because these classes are non-abstract, they are required to override the `Paint` method and provide an actual implementation.</span></span>

<span data-ttu-id="0804f-958">Il s’agit d’une erreur au moment de la compilation pour qu’un *base_access* ([base Access](expressions.md#base-access)) référence une méthode abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-958">It is a compile-time error for a *base_access* ([Base access](expressions.md#base-access)) to reference an abstract method.</span></span> <span data-ttu-id="0804f-959">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-959">In the example</span></span>
```csharp
abstract class A
{
    public abstract void F();
}

class B: A
{
    public override void F() {
        base.F();                        // Error, base.F is abstract
    }
}
```
<span data-ttu-id="0804f-960">une erreur de compilation est signalée pour l' `base.F()` appel, car elle fait référence à une méthode abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-960">a compile-time error is reported for the `base.F()` invocation because it references an abstract method.</span></span>

<span data-ttu-id="0804f-961">Une déclaration de méthode abstraite est autorisée à substituer une méthode virtuelle.</span><span class="sxs-lookup"><span data-stu-id="0804f-961">An abstract method declaration is permitted to override a virtual method.</span></span> <span data-ttu-id="0804f-962">Cela permet à une classe abstraite de forcer la réimplémentation de la méthode dans les classes dérivées et rend l’implémentation d’origine de la méthode indisponible.</span><span class="sxs-lookup"><span data-stu-id="0804f-962">This allows an abstract class to force re-implementation of the method in derived classes, and makes the original implementation of the method unavailable.</span></span> <span data-ttu-id="0804f-963">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-963">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }
}

abstract class B: A
{
    public abstract override void F();
}

class C: B
{
    public override void F() {
        Console.WriteLine("C.F");
    }
}
```
<span data-ttu-id="0804f-964">la `A` classe déclare une méthode virtuelle, la `B` classe remplace cette méthode par une méthode abstraite, et la `C` classe substitue la méthode abstraite pour fournir sa propre implémentation.</span><span class="sxs-lookup"><span data-stu-id="0804f-964">class `A` declares a virtual method, class `B` overrides this method with an abstract method, and class `C` overrides the abstract method to provide its own implementation.</span></span>

### <a name="external-methods"></a><span data-ttu-id="0804f-965">Méthodes externes</span><span class="sxs-lookup"><span data-stu-id="0804f-965">External methods</span></span>

<span data-ttu-id="0804f-966">Lorsqu’une déclaration de méthode comprend `extern` un modificateur, cette méthode est dite ***externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-966">When a method declaration includes an `extern` modifier, that method is said to be an ***external method***.</span></span> <span data-ttu-id="0804f-967">Les méthodes externes sont implémentées en externe, généralement à l’aide C#d’un langage autre que.</span><span class="sxs-lookup"><span data-stu-id="0804f-967">External methods are implemented externally, typically using a language other than C#.</span></span> <span data-ttu-id="0804f-968">Étant donné qu’une déclaration de méthode externe ne fournit aucune implémentation réelle, le *method_body* d’une méthode externe se compose simplement d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-968">Because an external method declaration provides no actual implementation, the *method_body* of an external method simply consists of a semicolon.</span></span> <span data-ttu-id="0804f-969">Une méthode externe ne peut pas être générique.</span><span class="sxs-lookup"><span data-stu-id="0804f-969">An external method may not be generic.</span></span>

<span data-ttu-id="0804f-970">Le `extern` modificateur est généralement utilisé conjointement avec un `DllImport` attribut ([interopérabilité avec les composants COM et Win32](attributes.md#interoperation-with-com-and-win32-components)), ce qui permet d’implémenter des méthodes externes par des dll (bibliothèques de liens dynamiques).</span><span class="sxs-lookup"><span data-stu-id="0804f-970">The `extern` modifier is typically used in conjunction with a `DllImport` attribute ([Interoperation with COM and Win32 components](attributes.md#interoperation-with-com-and-win32-components)), allowing external methods to be implemented by DLLs (Dynamic Link Libraries).</span></span> <span data-ttu-id="0804f-971">L’environnement d’exécution peut prendre en charge d’autres mécanismes par lesquels des implémentations de méthodes externes peuvent être fournies.</span><span class="sxs-lookup"><span data-stu-id="0804f-971">The execution environment may support other mechanisms whereby implementations of external methods can be provided.</span></span>

<span data-ttu-id="0804f-972">Quand une méthode externe inclut un `DllImport` attribut, la déclaration de méthode doit également inclure `static` un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-972">When an external method includes a `DllImport` attribute, the method declaration must also include a `static` modifier.</span></span> <span data-ttu-id="0804f-973">Cet exemple illustre l’utilisation du `extern` modificateur et de l' `DllImport` attribut :</span><span class="sxs-lookup"><span data-stu-id="0804f-973">This example demonstrates the use of the `extern` modifier and the `DllImport` attribute:</span></span>
```csharp
using System.Text;
using System.Security.Permissions;
using System.Runtime.InteropServices;

class Path
{
    [DllImport("kernel32", SetLastError=true)]
    static extern bool CreateDirectory(string name, SecurityAttribute sa);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool RemoveDirectory(string name);

    [DllImport("kernel32", SetLastError=true)]
    static extern int GetCurrentDirectory(int bufSize, StringBuilder buf);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool SetCurrentDirectory(string name);
}
```

### <a name="partial-methods-recap"></a><span data-ttu-id="0804f-974">Méthodes partielles (récapitulatif)</span><span class="sxs-lookup"><span data-stu-id="0804f-974">Partial methods (recap)</span></span>

<span data-ttu-id="0804f-975">Lorsqu’une déclaration de méthode comprend `partial` un modificateur, cette méthode est dite ***partielle***.</span><span class="sxs-lookup"><span data-stu-id="0804f-975">When a method declaration includes a `partial` modifier, that method is said to be a ***partial method***.</span></span> <span data-ttu-id="0804f-976">Les méthodes partielles ne peuvent être déclarées que comme membres de types partiels ([types partiels](classes.md#partial-types)) et sont soumises à un certain nombre de restrictions.</span><span class="sxs-lookup"><span data-stu-id="0804f-976">Partial methods can only be declared as members of partial types ([Partial types](classes.md#partial-types)), and are subject to a number of restrictions.</span></span> <span data-ttu-id="0804f-977">Les méthodes partielles sont décrites plus en détail dans les [méthodes partielles](classes.md#partial-methods).</span><span class="sxs-lookup"><span data-stu-id="0804f-977">Partial methods are further described in [Partial methods](classes.md#partial-methods).</span></span>

### <a name="extension-methods"></a><span data-ttu-id="0804f-978">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="0804f-978">Extension methods</span></span>

<span data-ttu-id="0804f-979">Quand le premier paramètre d’une méthode comprend le `this` modificateur, cette méthode est considérée comme une ***méthode d’extension***.</span><span class="sxs-lookup"><span data-stu-id="0804f-979">When the first parameter of a method includes the `this` modifier, that method is said to be an ***extension method***.</span></span> <span data-ttu-id="0804f-980">Les méthodes d’extension ne peuvent être déclarées que dans des classes statiques non imbriquées et non génériques.</span><span class="sxs-lookup"><span data-stu-id="0804f-980">Extension methods can only be declared in non-generic, non-nested static classes.</span></span> <span data-ttu-id="0804f-981">Le premier paramètre d’une méthode d’extension ne peut pas avoir de modificateurs autres que `this`, et le type de paramètre ne peut pas être un type pointeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-981">The first parameter of an extension method can have no modifiers other than `this`, and the parameter type cannot be a pointer type.</span></span>

<span data-ttu-id="0804f-982">Voici un exemple de classe statique qui déclare deux méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="0804f-982">The following is an example of a static class that declares two extension methods:</span></span>
```csharp
public static class Extensions
{
    public static int ToInt32(this string s) {
        return Int32.Parse(s);
    }

    public static T[] Slice<T>(this T[] source, int index, int count) {
        if (index < 0 || count < 0 || source.Length - index < count)
            throw new ArgumentException();
        T[] result = new T[count];
        Array.Copy(source, index, result, 0, count);
        return result;
    }
}
```

<span data-ttu-id="0804f-983">Une méthode d’extension est une méthode statique normale.</span><span class="sxs-lookup"><span data-stu-id="0804f-983">An extension method is a regular static method.</span></span> <span data-ttu-id="0804f-984">En outre, lorsque sa classe statique englobante est dans la portée, une méthode d’extension peut être appelée à l’aide de la syntaxe d’appel de méthode d’instance ([appels de méthode d’extension](expressions.md#extension-method-invocations)), à l’aide de l’expression de récepteur comme premier argument.</span><span class="sxs-lookup"><span data-stu-id="0804f-984">In addition, where its enclosing static class is in scope, an extension method can be invoked using instance method invocation syntax ([Extension method invocations](expressions.md#extension-method-invocations)), using the receiver expression as the first argument.</span></span>

<span data-ttu-id="0804f-985">Le programme suivant utilise les méthodes d’extension déclarées ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="0804f-985">The following program uses the extension methods declared above:</span></span>
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in strings.Slice(1, 2)) {
            Console.WriteLine(s.ToInt32());
        }
    }
}
```

<span data-ttu-id="0804f-986">La `Slice` méthode est disponible sur le `string[]`, et la `ToInt32` méthode est disponible sur `string`, car elles ont été déclarées comme méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="0804f-986">The `Slice` method is available on the `string[]`, and the `ToInt32` method is available on `string`, because they have been declared as extension methods.</span></span> <span data-ttu-id="0804f-987">La signification du programme est la même que celle qui suit, à l’aide d’appels de méthode statique ordinaires :</span><span class="sxs-lookup"><span data-stu-id="0804f-987">The meaning of the program is the same as the following, using ordinary static method calls:</span></span>
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in Extensions.Slice(strings, 1, 2)) {
            Console.WriteLine(Extensions.ToInt32(s));
        }
    }
}
```

### <a name="method-body"></a><span data-ttu-id="0804f-988">Corps de la méthode</span><span class="sxs-lookup"><span data-stu-id="0804f-988">Method body</span></span>

<span data-ttu-id="0804f-989">Le *method_body* d’une déclaration de méthode se compose d’un corps de bloc, d’un corps d’expression ou d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-989">The *method_body* of a method declaration consists of either a block body, an expression body or a semicolon.</span></span>

<span data-ttu-id="0804f-990">Le ***type de résultat*** d’une méthode `void` est si le type de `void`retour est, ou si la méthode est asynchrone et que le `System.Threading.Tasks.Task`type de retour est.</span><span class="sxs-lookup"><span data-stu-id="0804f-990">The ***result type*** of a method is `void` if the return type is `void`, or if the method is async and the return type is `System.Threading.Tasks.Task`.</span></span> <span data-ttu-id="0804f-991">Sinon, le type de résultat d’une méthode non asynchrone est son type de retour et le type de résultat d’une méthode Async avec le `System.Threading.Tasks.Task<T>` type `T`de retour est.</span><span class="sxs-lookup"><span data-stu-id="0804f-991">Otherwise, the result type of a non-async method is its return type, and the result type of an async method with return type `System.Threading.Tasks.Task<T>` is `T`.</span></span>

<span data-ttu-id="0804f-992">Quand une méthode a un `void` type de résultat et un corps de `return` bloc, les instructions ([l’instruction return](statements.md#the-return-statement)) dans le bloc ne sont pas autorisées à spécifier une expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-992">When a method has a `void` result type and a block body, `return` statements ([The return statement](statements.md#the-return-statement)) in the block are not permitted to specify an expression.</span></span> <span data-ttu-id="0804f-993">Si l’exécution du bloc d’une méthode void se termine normalement (autrement dit, le contrôle circule à la fin du corps de la méthode), cette méthode retourne simplement à son appelant actuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-993">If execution of the block of a void method completes normally (that is, control flows off the end of the method body), that method simply returns to its current caller.</span></span>
    
<span data-ttu-id="0804f-994">Quand une méthode a un résultat `void` et un corps d’expression, l’expression `E` doit être un *statement_expression*et le corps est exactement équivalent à un corps de bloc de la forme `{ E; }`.</span><span class="sxs-lookup"><span data-stu-id="0804f-994">When a method has a `void` result and an expression body, the expression `E` must be a *statement_expression*, and the body is exactly equivalent to a block body of the form `{ E; }`.</span></span>
    
<span data-ttu-id="0804f-995">Quand une méthode a un type de résultat non void et un corps de bloc, `return` chaque instruction du bloc doit spécifier une expression qui est implicitement convertible en type de résultat.</span><span class="sxs-lookup"><span data-stu-id="0804f-995">When a method has a non-void result type and a block body, each `return` statement in the block must specify an expression that is implicitly convertible to the result type.</span></span> <span data-ttu-id="0804f-996">Le point de terminaison d’un corps de bloc d’une méthode retournant une valeur ne doit pas être accessible.</span><span class="sxs-lookup"><span data-stu-id="0804f-996">The endpoint of a block body of a value-returning method must not be reachable.</span></span> <span data-ttu-id="0804f-997">En d’autres termes, dans une méthode retournant une valeur avec un corps de bloc, le contrôle n’est pas autorisé à circuler à la fin du corps de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-997">In other words, in a value-returning method with a block body, control is not permitted to flow off the end of the method body.</span></span>
    
<span data-ttu-id="0804f-998">Quand une méthode a un type de résultat non void et un corps d’expression, l’expression doit être implicitement convertible en type de résultat, et le corps est exactement équivalent à un corps de bloc de `{ return E; }`la forme.</span><span class="sxs-lookup"><span data-stu-id="0804f-998">When a method has a non-void result type and an expression body, the expression must be implicitly convertible to the result type, and the body is exactly equivalent to a block body of the form `{ return E; }`.</span></span>
    
<span data-ttu-id="0804f-999">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-999">In the example</span></span>
```csharp
class A
{
    public int F() {}            // Error, return value required

    public int G() {
        return 1;
    }

    public int H(bool b) {
        if (b) {
            return 1;
        }
        else {
            return 0;
        }
    }

    public int I(bool b) => b ? 1 : 0;
}
```
<span data-ttu-id="0804f-1000">la méthode qui retourne `F` une valeur génère une erreur au moment de la compilation, car le contrôle peut circuler à la fin du corps de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-1000">the value-returning `F` method results in a compile-time error because control can flow off the end of the method body.</span></span> <span data-ttu-id="0804f-1001">Les `G` méthodes `H` et sont correctes, car tous les chemins d’exécution possibles se terminent par une instruction return qui spécifie une valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="0804f-1001">The `G` and `H` methods are correct because all possible execution paths end in a return statement that specifies a return value.</span></span> <span data-ttu-id="0804f-1002">La `I` méthode est correcte, car son corps est équivalent à un bloc d’instructions avec une seule instruction return.</span><span class="sxs-lookup"><span data-stu-id="0804f-1002">The `I` method is correct, because its body is equivalent to a statement block with just a single return statement in it.</span></span>

### <a name="method-overloading"></a><span data-ttu-id="0804f-1003">Surcharge de méthode</span><span class="sxs-lookup"><span data-stu-id="0804f-1003">Method overloading</span></span>

<span data-ttu-id="0804f-1004">Les règles de résolution de surcharge de méthode sont décrites dans [inférence de type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="0804f-1004">The method overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="properties"></a><span data-ttu-id="0804f-1005">Properties</span><span class="sxs-lookup"><span data-stu-id="0804f-1005">Properties</span></span>

<span data-ttu-id="0804f-1006">Une ***propriété*** est un membre qui fournit l’accès à une caractéristique d’un objet ou d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1006">A ***property*** is a member that provides access to a characteristic of an object or a class.</span></span> <span data-ttu-id="0804f-1007">Les exemples de propriétés incluent la longueur d’une chaîne, la taille d’une police, la légende d’une fenêtre, le nom d’un client, etc.</span><span class="sxs-lookup"><span data-stu-id="0804f-1007">Examples of properties include the length of a string, the size of a font, the caption of a window, the name of a customer, and so on.</span></span> <span data-ttu-id="0804f-1008">Les propriétés sont une extension naturelle des champs, les deux étant des membres nommés avec des types associés, et la syntaxe d’accès aux champs et aux propriétés est la même.</span><span class="sxs-lookup"><span data-stu-id="0804f-1008">Properties are a natural extension of fields—both are named members with associated types, and the syntax for accessing fields and properties is the same.</span></span> <span data-ttu-id="0804f-1009">Toutefois, contrairement aux champs, les propriétés ne désignent pas des emplacements de stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-1009">However, unlike fields, properties do not denote storage locations.</span></span> <span data-ttu-id="0804f-1010">Au lieu de cela, les propriétés ont des ***accesseurs*** qui spécifient les instructions à exécuter lorsque les valeurs sont lues ou écrites.</span><span class="sxs-lookup"><span data-stu-id="0804f-1010">Instead, properties have ***accessors*** that specify the statements to be executed when their values are read or written.</span></span> <span data-ttu-id="0804f-1011">Les propriétés fournissent donc un mécanisme permettant d’associer des actions à la lecture et à l’écriture des attributs d’un objet. en outre, ils permettent de calculer ces attributs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1011">Properties thus provide a mechanism for associating actions with the reading and writing of an object's attributes; furthermore, they permit such attributes to be computed.</span></span>

<span data-ttu-id="0804f-1012">Les propriétés sont déclarées à l’aide de *property_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1012">Properties are declared using *property_declaration*s:</span></span>

```antlr
property_declaration
    : attributes? property_modifier* type member_name property_body
    ;

property_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | property_modifier_unsafe
    ;

property_body
    : '{' accessor_declarations '}' property_initializer?
    | '=>' expression ';'
    ;

property_initializer
    : '=' variable_initializer ';'
    ;
```

<span data-ttu-id="0804f-1013">Un *property_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1013">A *property_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="0804f-1014">Les déclarations de propriété sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1014">Property declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="0804f-1015">Le *type* d’une déclaration de propriété spécifie le type de la propriété introduite par la déclaration, et *Member_Name* spécifie le nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1015">The *type* of a property declaration specifies the type of the property introduced by the declaration, and the *member_name* specifies the name of the property.</span></span> <span data-ttu-id="0804f-1016">À moins que la propriété ne soit une implémentation de membre d’interface explicite, *Member_Name* est simplement un *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1016">Unless the property is an explicit interface member implementation, the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="0804f-1017">Pour une implémentation de membre d’interface explicite ([implémentations de membres d’interface explicites](interfaces.md#explicit-interface-member-implementations)), *Member_Name* se compose d’un *interface_type* suivi d’un « `.` » et d’un *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1017">For an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="0804f-1018">Le *type* d’une propriété doit être au moins aussi accessible que la propriété elle-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1018">The *type* of a property must be at least as accessible as the property itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-1019">Un *property_body* peut être constitué d’un ***corps d’accesseur*** ou d’un ***corps d’expression***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1019">A *property_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="0804f-1020">Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre les jetons « `{` » et « `}` », déclare les accesseurs ([accesseurs](classes.md#accessors)) de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1020">In an accessor body,  *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="0804f-1021">Les accesseurs spécifient les instructions exécutables associées à la lecture et à l’écriture de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1021">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="0804f-1022">Un corps d’expression composé `=>` de suivis d’une *expression* `E` et d’un point-virgule est exactement `{ get { return E; } }`équivalent au corps de l’instruction et ne peut donc être utilisé que pour spécifier des propriétés Getter uniquement lorsque le résultat de l’accesseur Get est fourni par une expression unique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1022">An expression body consisting of `=>` followed by an *expression* `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only properties where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="0804f-1023">Un *property_initializer* peut uniquement être fourni pour une propriété implémentée automatiquement ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)) et provoque l’initialisation du champ sous-jacent de telles propriétés avec la valeur donnée par l' *expression* .</span><span class="sxs-lookup"><span data-stu-id="0804f-1023">A *property_initializer* may only be given for an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)), and causes the initialization of the underlying field of such properties with the value given by the *expression*.</span></span>

<span data-ttu-id="0804f-1024">Même si la syntaxe d’accès à une propriété est identique à celle d’un champ, une propriété n’est pas classée comme une variable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1024">Even though the syntax for accessing a property is the same as that for a field, a property is not classified as a variable.</span></span> <span data-ttu-id="0804f-1025">Par conséquent, il n’est pas possible de passer une propriété `ref` en `out` tant qu’argument ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-1025">Thus, it is not possible to pass a property as a `ref` or `out` argument.</span></span>

<span data-ttu-id="0804f-1026">Lorsqu’une déclaration de propriété comprend `extern` un modificateur, la propriété est considérée comme une ***propriété externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1026">When a property declaration includes an `extern` modifier, the property is said to be an ***external property***.</span></span> <span data-ttu-id="0804f-1027">Étant donné qu’une déclaration de propriété externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1027">Because an external property declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

### <a name="static-and-instance-properties"></a><span data-ttu-id="0804f-1028">Propriétés statiques et d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-1028">Static and instance properties</span></span>

<span data-ttu-id="0804f-1029">Lorsqu’une déclaration de propriété comprend `static` un modificateur, la propriété est considérée comme une ***propriété statique***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1029">When a property declaration includes a `static` modifier, the property is said to be a ***static property***.</span></span> <span data-ttu-id="0804f-1030">Quand aucun `static` modificateur n’est présent, la propriété est considérée comme une ***propriété d’instance***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1030">When no `static` modifier is present, the property is said to be an ***instance property***.</span></span>

<span data-ttu-id="0804f-1031">Une propriété statique n’est pas associée à une instance spécifique, et il s’agit d’une erreur de compilation pour `this` faire référence aux accesseurs d’une propriété statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1031">A static property is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static property.</span></span>

<span data-ttu-id="0804f-1032">Une propriété d’instance est associée à une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)) dans les accesseurs de cette propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1032">An instance property is associated with a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that property.</span></span>

<span data-ttu-id="0804f-1033">Lorsqu’une propriété est référencée dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est une propriété statique, `E` doit désigner un type contenant `M`, et si `M` est une propriété d’instance, E doit désigner une instance d’un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1033">When a property is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static property, `E` must denote a type containing `M`, and if `M` is an instance property, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="0804f-1034">Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="0804f-1034">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="accessors"></a><span data-ttu-id="0804f-1035">Accesseurs</span><span class="sxs-lookup"><span data-stu-id="0804f-1035">Accessors</span></span>

<span data-ttu-id="0804f-1036">Le *accessor_declarations* d’une propriété spécifie les instructions exécutables associées à la lecture et à l’écriture de cette propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1036">The *accessor_declarations* of a property specify the executable statements associated with reading and writing that property.</span></span>

```antlr
accessor_declarations
    : get_accessor_declaration set_accessor_declaration?
    | set_accessor_declaration get_accessor_declaration?
    ;

get_accessor_declaration
    : attributes? accessor_modifier? 'get' accessor_body
    ;

set_accessor_declaration
    : attributes? accessor_modifier? 'set' accessor_body
    ;

accessor_modifier
    : 'protected'
    | 'internal'
    | 'private'
    | 'protected' 'internal'
    | 'internal' 'protected'
    ;

accessor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="0804f-1037">Les déclarations d’accesseur se composent d’un *get_accessor_declaration*, d’un *set_accessor_declaration*ou des deux.</span><span class="sxs-lookup"><span data-stu-id="0804f-1037">The accessor declarations consist of a *get_accessor_declaration*, a *set_accessor_declaration*, or both.</span></span> <span data-ttu-id="0804f-1038">Chaque déclaration d’accesseur se compose du jeton `get` ou `set` suivi d’un *accessor_modifier* facultatif et d’un *accessor_body*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1038">Each accessor declaration consists of the token `get` or `set` followed by an optional *accessor_modifier* and an *accessor_body*.</span></span>

<span data-ttu-id="0804f-1039">L’utilisation de *accessor_modifier*s est régie par les restrictions suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-1039">The use of *accessor_modifier*s is governed by the following restrictions:</span></span>

*  <span data-ttu-id="0804f-1040">Un *accessor_modifier* ne peut pas être utilisé dans une interface ou dans une implémentation de membre d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="0804f-1040">An *accessor_modifier* may not be used in an interface or in an explicit interface member implementation.</span></span>
*  <span data-ttu-id="0804f-1041">Pour une propriété ou un indexeur qui n’a pas de modificateur `override`, un *accessor_modifier* est autorisé uniquement si la propriété ou l’indexeur a à la fois un accesseur `get` et `set`, et n’est autorisé que sur l’un de ces accesseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1041">For a property or indexer that has no `override` modifier, an *accessor_modifier* is permitted only if the property or indexer has both a `get` and `set` accessor, and then is permitted only on one of those accessors.</span></span>
*  <span data-ttu-id="0804f-1042">Pour une propriété ou un indexeur qui comprend un modificateur `override`, un accesseur doit correspondre au *accessor_modifier*, le cas échéant, de l’accesseur en cours de substitution.</span><span class="sxs-lookup"><span data-stu-id="0804f-1042">For a property or indexer that includes an `override` modifier, an accessor must match the *accessor_modifier*, if any, of the accessor being overridden.</span></span>
*  <span data-ttu-id="0804f-1043">Le *accessor_modifier* doit déclarer une accessibilité qui est strictement plus restrictive que l’accessibilité déclarée de la propriété ou de l’indexeur lui-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-1043">The *accessor_modifier* must declare an accessibility that is strictly more restrictive than the declared accessibility of the property or indexer itself.</span></span> <span data-ttu-id="0804f-1044">Pour être précis :</span><span class="sxs-lookup"><span data-stu-id="0804f-1044">To be precise:</span></span>
   * <span data-ttu-id="0804f-1045">Si la propriété ou l’indexeur a une accessibilité déclarée de `public`, *accessor_modifier* peut être `protected internal`, `internal`, `protected` ou `private`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1045">If the property or indexer has a declared accessibility of `public`, the *accessor_modifier* may be either `protected internal`, `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="0804f-1046">Si la propriété ou l’indexeur a une accessibilité déclarée de `protected internal`, la valeur de *accessor_modifier* peut être `internal`, `protected` ou `private`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1046">If the property or indexer has a declared accessibility of `protected internal`, the *accessor_modifier* may be either `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="0804f-1047">Si la propriété ou l’indexeur a une accessibilité déclarée de `internal` ou `protected`, *accessor_modifier* doit être `private`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1047">If the property or indexer has a declared accessibility of `internal` or `protected`, the *accessor_modifier* must be `private`.</span></span>
   * <span data-ttu-id="0804f-1048">Si la propriété ou l’indexeur a une accessibilité déclarée de `private`, aucun *accessor_modifier* ne peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1048">If the property or indexer has a declared accessibility of `private`, no *accessor_modifier* may be used.</span></span>

<span data-ttu-id="0804f-1049">Pour les propriétés `abstract` et `extern`, *accessor_body* pour chaque accesseur spécifié est simplement un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1049">For `abstract` and `extern` properties, the *accessor_body* for each accessor specified is simply a semicolon.</span></span> <span data-ttu-id="0804f-1050">Une propriété non abstraite et non-extern peut avoir chaque *accessor_body* un point-virgule, auquel cas il s’agit d’une ***propriété implémentée automatiquement*** ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1050">A non-abstract, non-extern property may have each *accessor_body* be a semicolon, in which case it is an ***automatically implemented property*** ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="0804f-1051">Une propriété implémentée automatiquement doit avoir au moins un accesseur Get.</span><span class="sxs-lookup"><span data-stu-id="0804f-1051">An automatically implemented property must have at least a get accessor.</span></span> <span data-ttu-id="0804f-1052">Pour les accesseurs de toute autre propriété non-abstract non-extern, *accessor_body* est un *bloc* qui spécifie les instructions à exécuter lorsque l’accesseur correspondant est appelé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1052">For the accessors of any other non-abstract, non-extern property, the *accessor_body* is a *block* which specifies the statements to be executed when the corresponding accessor is invoked.</span></span>

<span data-ttu-id="0804f-1053">Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1053">A `get` accessor corresponds to a parameterless method with a return value of the property type.</span></span> <span data-ttu-id="0804f-1054">À l’exception de la cible d’une assignation, lorsqu’une propriété est référencée dans une expression `get` , l’accesseur de la propriété est appelé pour calculer la valeur de la propriété ([valeurs des expressions](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1054">Except as the target of an assignment, when a property is referenced in an expression, the `get` accessor of the property is invoked to compute the value of the property ([Values of expressions](expressions.md#values-of-expressions)).</span></span> <span data-ttu-id="0804f-1055">Le corps d’un `get` accesseur doit se conformer aux règles pour les méthodes retournant des valeurs décrites dans le corps de la [méthode](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="0804f-1055">The body of a `get` accessor must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="0804f-1056">En particulier, toutes `return` les instructions dans le corps d' `get` un accesseur doivent spécifier une expression qui est implicitement convertible en type de propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1056">In particular, all `return` statements in the body of a `get` accessor must specify an expression that is implicitly convertible to the property type.</span></span> <span data-ttu-id="0804f-1057">En outre, le point de `get` terminaison d’un accesseur ne doit pas être accessible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1057">Furthermore, the endpoint of a `get` accessor must not be reachable.</span></span>

<span data-ttu-id="0804f-1058">Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété `void` et un type de retour.</span><span class="sxs-lookup"><span data-stu-id="0804f-1058">A `set` accessor corresponds to a method with a single value parameter of the property type and a `void` return type.</span></span> <span data-ttu-id="0804f-1059">Le paramètre implicite d' `set` un accesseur est `value`toujours nommé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1059">The implicit parameter of a `set` accessor is always named `value`.</span></span> <span data-ttu-id="0804f-1060">Lorsqu’une propriété est référencée en tant que cible d’une assignation[(opérateurs d’affectation](expressions.md#assignment-operators)), ou en tant `++` qu' `--` opérande de ou ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators), [opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), l’accesseur est appelé avec un argument (dont la valeur est celle du côté droit de l’assignation ou l’opérande de l' `++` opérateur or `--` ) qui fournit la nouvelle valeur ([assignation simple](expressions.md#simple-assignment)). `set`</span><span class="sxs-lookup"><span data-stu-id="0804f-1060">When a property is referenced as the target of an assignment ([Assignment operators](expressions.md#assignment-operators)), or as the operand of `++` or `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators), [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), the `set` accessor is invoked with an argument (whose value is that of the right-hand side of the assignment or the operand of the `++` or `--` operator) that provides the new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="0804f-1061">Le corps d’un `set` accesseur doit se conformer `void` aux règles pour les méthodes décrites dans le corps de la [méthode](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="0804f-1061">The body of a `set` accessor must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="0804f-1062">En particulier, `return` les instructions dans `set` le corps de l’accesseur ne sont pas autorisées à spécifier une expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-1062">In particular, `return` statements in the `set` accessor body are not permitted to specify an expression.</span></span> <span data-ttu-id="0804f-1063">Comme un `set` accesseur a implicitement un paramètre `value`nommé, il s’agit d’une erreur de compilation pour une variable locale ou une déclaration `set` de constante dans un accesseur portant ce nom.</span><span class="sxs-lookup"><span data-stu-id="0804f-1063">Since a `set` accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declaration in a `set` accessor to have that name.</span></span>

<span data-ttu-id="0804f-1064">En fonction de la présence ou de l' `get` absence `set` des accesseurs et, une propriété est classée comme suit :</span><span class="sxs-lookup"><span data-stu-id="0804f-1064">Based on the presence or absence of the `get` and `set` accessors, a property is classified as follows:</span></span>

*  <span data-ttu-id="0804f-1065">Une propriété qui comprend à la `get` fois un accesseur et un `set` accesseur est considérée comme une propriété ***en lecture-écriture*** .</span><span class="sxs-lookup"><span data-stu-id="0804f-1065">A property that includes both a `get` accessor and a `set` accessor is said to be a ***read-write*** property.</span></span>
*  <span data-ttu-id="0804f-1066">Une propriété qui a uniquement un `get` accesseur est considérée comme étant une propriété ***en lecture seule*** .</span><span class="sxs-lookup"><span data-stu-id="0804f-1066">A property that has only a `get` accessor is said to be a ***read-only*** property.</span></span> <span data-ttu-id="0804f-1067">Il s’agit d’une erreur de compilation pour qu’une propriété en lecture seule soit la cible d’une assignation.</span><span class="sxs-lookup"><span data-stu-id="0804f-1067">It is a compile-time error for a read-only property to be the target of an assignment.</span></span>
*  <span data-ttu-id="0804f-1068">Une propriété qui a uniquement un `set` accesseur est considérée comme étant une propriété en ***écriture seule*** .</span><span class="sxs-lookup"><span data-stu-id="0804f-1068">A property that has only a `set` accessor is said to be a ***write-only*** property.</span></span> <span data-ttu-id="0804f-1069">À l’exception de la cible d’une assignation, il s’agit d’une erreur au moment de la compilation pour référencer une propriété en écriture seule dans une expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-1069">Except as the target of an assignment, it is a compile-time error to reference a write-only property in an expression.</span></span>

<span data-ttu-id="0804f-1070">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1070">In the example</span></span>
```csharp
public class Button: Control
{
    private string caption;

    public string Caption {
        get {
            return caption;
        }
        set {
            if (caption != value) {
                caption = value;
                Repaint();
            }
        }
    }

    public override void Paint(Graphics g, Rectangle r) {
        // Painting code goes here
    }
}
```
<span data-ttu-id="0804f-1071">le `Button` contrôle déclare une propriété publique `Caption` .</span><span class="sxs-lookup"><span data-stu-id="0804f-1071">the `Button` control declares a public `Caption` property.</span></span> <span data-ttu-id="0804f-1072">L' `get` accesseur de `Caption` la propriété retourne la chaîne stockée dans `caption` le champ privé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1072">The `get` accessor of the `Caption` property returns the string stored in the private `caption` field.</span></span> <span data-ttu-id="0804f-1073">L' `set` accesseur vérifie si la nouvelle valeur est différente de la valeur actuelle, et si c’est le cas, elle stocke la nouvelle valeur et repeint le contrôle.</span><span class="sxs-lookup"><span data-stu-id="0804f-1073">The `set` accessor checks if the new value is different from the current value, and if so, it stores the new value and repaints the control.</span></span> <span data-ttu-id="0804f-1074">Les propriétés suivent souvent le modèle ci-dessus : L' `get` accesseur retourne simplement une valeur stockée dans un champ privé, `set` et l’accesseur modifie ce champ privé, puis exécute toutes les actions supplémentaires requises pour mettre à jour complètement l’état de l’objet.</span><span class="sxs-lookup"><span data-stu-id="0804f-1074">Properties often follow the pattern shown above: The `get` accessor simply returns a value stored in a private field, and the `set` accessor modifies that private field and then performs any additional actions required to fully update the state of the object.</span></span>

<span data-ttu-id="0804f-1075">À partir `Button` de la classe ci-dessus, voici un exemple d’utilisation `Caption` de la propriété :</span><span class="sxs-lookup"><span data-stu-id="0804f-1075">Given the `Button` class above, the following is an example of use of the `Caption` property:</span></span>
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

<span data-ttu-id="0804f-1076">Ici, l' `set` accesseur est appelé en affectant une valeur à la propriété, et l' `get` accesseur est appelé en référençant la propriété dans une expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-1076">Here, the `set` accessor is invoked by assigning a value to the property, and the `get` accessor is invoked by referencing the property in an expression.</span></span>

<span data-ttu-id="0804f-1077">Les `get` accesseurs et `set` d’une propriété ne sont pas des membres distincts et il n’est pas possible de déclarer les accesseurs d’une propriété séparément.</span><span class="sxs-lookup"><span data-stu-id="0804f-1077">The `get` and `set` accessors of a property are not distinct members, and it is not possible to declare the accessors of a property separately.</span></span> <span data-ttu-id="0804f-1078">Par conséquent, il n’est pas possible que les deux accesseurs d’une propriété en lecture-écriture aient une accessibilité différente.</span><span class="sxs-lookup"><span data-stu-id="0804f-1078">As such, it is not possible for the two accessors of a read-write property to have different accessibility.</span></span> <span data-ttu-id="0804f-1079">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1079">The example</span></span>
```csharp
class A
{
    private string name;

    public string Name {                // Error, duplicate member name
        get { return name; }
    }

    public string Name {                // Error, duplicate member name
        set { name = value; }
    }
}
```
<span data-ttu-id="0804f-1080">ne déclare pas une propriété en lecture-écriture unique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1080">does not declare a single read-write property.</span></span> <span data-ttu-id="0804f-1081">Au lieu de cela, elle déclare deux propriétés portant le même nom, une seule en lecture seule et une seule en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1081">Rather, it declares two properties with the same name, one read-only and one write-only.</span></span> <span data-ttu-id="0804f-1082">Dans la mesure où deux membres déclarés dans la même classe ne peuvent pas avoir le même nom, l’exemple provoque une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="0804f-1082">Since two members declared in the same class cannot have the same name, the example causes a compile-time error to occur.</span></span>

<span data-ttu-id="0804f-1083">Lorsqu’une classe dérivée déclare une propriété à l’aide du même nom qu’une propriété héritée, la propriété dérivée masque la propriété héritée en ce qui concerne la lecture et l’écriture.</span><span class="sxs-lookup"><span data-stu-id="0804f-1083">When a derived class declares a property by the same name as an inherited property, the derived property hides the inherited property with respect to both reading and writing.</span></span> <span data-ttu-id="0804f-1084">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1084">In the example</span></span>
```csharp
class A
{
    public int P {
        set {...}
    }
}

class B: A
{
    new public int P {
        get {...}
    }
}
```
<span data-ttu-id="0804f-1085">la `P` propriété dans `B` masque la propriété `P` dans en `A` ce qui concerne la lecture et l’écriture.</span><span class="sxs-lookup"><span data-stu-id="0804f-1085">the `P` property in `B` hides the `P` property in `A` with respect to both reading and writing.</span></span> <span data-ttu-id="0804f-1086">Par conséquent, dans les instructions</span><span class="sxs-lookup"><span data-stu-id="0804f-1086">Thus, in the statements</span></span>
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
<span data-ttu-id="0804f-1087">l’assignation à `b.P` provoque le signalement d’une erreur au moment de la compilation, puisque la `P` propriété en `B` lecture seule dans masque la propriété `P` en écriture `A`seule dans.</span><span class="sxs-lookup"><span data-stu-id="0804f-1087">the assignment to `b.P` causes a compile-time error to be reported, since the read-only `P` property in `B` hides the write-only `P` property in `A`.</span></span> <span data-ttu-id="0804f-1088">Notez, toutefois, qu’un cast peut être utilisé pour accéder à la `P` propriété Hidden.</span><span class="sxs-lookup"><span data-stu-id="0804f-1088">Note, however, that a cast can be used to access the hidden `P` property.</span></span>

<span data-ttu-id="0804f-1089">Contrairement aux champs publics, les propriétés fournissent une séparation entre l’état interne d’un objet et son interface publique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1089">Unlike public fields, properties provide a separation between an object's internal state and its public interface.</span></span> <span data-ttu-id="0804f-1090">Prenons l’exemple suivant:</span><span class="sxs-lookup"><span data-stu-id="0804f-1090">Consider the example:</span></span>
```csharp
class Label
{
    private int x, y;
    private string caption;

    public Label(int x, int y, string caption) {
        this.x = x;
        this.y = y;
        this.caption = caption;
    }

    public int X {
        get { return x; }
    }

    public int Y {
        get { return y; }
    }

    public Point Location {
        get { return new Point(x, y); }
    }

    public string Caption {
        get { return caption; }
    }
}
```

<span data-ttu-id="0804f-1091">Ici, la `Label` classe utilise deux `int` champs, `x` et `y`, pour stocker son emplacement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1091">Here, the `Label` class uses two `int` fields, `x` and `y`, to store its location.</span></span> <span data-ttu-id="0804f-1092">L’emplacement est exposé publiquement en tant `X` que `Y` et en tant que propriété `Location` et en tant `Point`que propriété de type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1092">The location is publicly exposed both as an `X` and a `Y` property and as a `Location` property of type `Point`.</span></span> <span data-ttu-id="0804f-1093">Si, dans une future version de `Label`, il devient plus pratique de stocker l’emplacement `Point` en interne, la modification peut être effectuée sans affecter l’interface publique de la classe :</span><span class="sxs-lookup"><span data-stu-id="0804f-1093">If, in a future version of `Label`, it becomes more convenient to store the location as a `Point` internally, the change can be made without affecting the public interface of the class:</span></span>
```csharp
class Label
{
    private Point location;
    private string caption;

    public Label(int x, int y, string caption) {
        this.location = new Point(x, y);
        this.caption = caption;
    }

    public int X {
        get { return location.x; }
    }

    public int Y {
        get { return location.y; }
    }

    public Point Location {
        get { return location; }
    }

    public string Caption {
        get { return caption; }
    }
}
```

<span data-ttu-id="0804f-1094">Avait `x` `public readonly` `Label` et `y` à la place des champs, il aurait été impossible d’apporter une telle modification à la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1094">Had `x` and `y` instead been `public readonly` fields, it would have been impossible to make such a change to the `Label` class.</span></span>

<span data-ttu-id="0804f-1095">L’exposition de l’état via des propriétés n’est pas nécessairement moins efficace que l’exposition directe de champs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1095">Exposing state through properties is not necessarily any less efficient than exposing fields directly.</span></span> <span data-ttu-id="0804f-1096">En particulier, lorsqu’une propriété est non virtuelle et ne contient qu’une petite quantité de code, l’environnement d’exécution peut remplacer les appels aux accesseurs par le code réel des accesseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1096">In particular, when a property is non-virtual and contains only a small amount of code, the execution environment may replace calls to accessors with the actual code of the accessors.</span></span> <span data-ttu-id="0804f-1097">Ce processus est appelé ***incorporation***, et il rend l’accès aux propriétés aussi efficace que l’accès au champ, tout en préservant la flexibilité accrue des propriétés.</span><span class="sxs-lookup"><span data-stu-id="0804f-1097">This process is known as ***inlining***, and it makes property access as efficient as field access, yet preserves the increased flexibility of properties.</span></span>

<span data-ttu-id="0804f-1098">Étant donné que l' `get` appel d’un accesseur est conceptuellement équivalent à la lecture de la valeur d’un champ, il `get` est considéré comme un style de programmation incorrect pour les accesseurs d’avoir des effets secondaires observables.</span><span class="sxs-lookup"><span data-stu-id="0804f-1098">Since invoking a `get` accessor is conceptually equivalent to reading the value of a field, it is considered bad programming style for `get` accessors to have observable side-effects.</span></span> <span data-ttu-id="0804f-1099">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1099">In the example</span></span>
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
<span data-ttu-id="0804f-1100">la valeur de la `Next` propriété dépend du nombre de fois où l’accès à la propriété a été effectué précédemment.</span><span class="sxs-lookup"><span data-stu-id="0804f-1100">the value of the `Next` property depends on the number of times the property has previously been accessed.</span></span> <span data-ttu-id="0804f-1101">Ainsi, l’accès à la propriété produit un effet secondaire observable, et la propriété doit être implémentée comme une méthode à la place.</span><span class="sxs-lookup"><span data-stu-id="0804f-1101">Thus, accessing the property produces an observable side-effect, and the property should be implemented as a method instead.</span></span>

<span data-ttu-id="0804f-1102">La Convention « sans effets secondaires » pour `get` les accesseurs ne signifie pas que `get` les accesseurs doivent toujours être écrits pour renvoyer simplement des valeurs stockées dans des champs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1102">The "no side-effects" convention for `get` accessors doesn't mean that `get` accessors should always be written to simply return values stored in fields.</span></span> <span data-ttu-id="0804f-1103">En effet `get` , les accesseurs calculent souvent la valeur d’une propriété en accédant à plusieurs champs ou en appelant des méthodes.</span><span class="sxs-lookup"><span data-stu-id="0804f-1103">Indeed, `get` accessors often compute the value of a property by accessing multiple fields or invoking methods.</span></span> <span data-ttu-id="0804f-1104">Toutefois, un accesseur `get` correctement conçu n’effectue aucune action qui entraîne des modifications observables de l’état de l’objet.</span><span class="sxs-lookup"><span data-stu-id="0804f-1104">However, a properly designed `get` accessor performs no actions that cause observable changes in the state of the object.</span></span>

<span data-ttu-id="0804f-1105">Les propriétés peuvent être utilisées pour différer l’initialisation d’une ressource jusqu’au moment où elle est référencée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="0804f-1105">Properties can be used to delay initialization of a resource until the moment it is first referenced.</span></span> <span data-ttu-id="0804f-1106">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-1106">For example:</span></span>
```csharp
using System.IO;

public class Console
{
    private static TextReader reader;
    private static TextWriter writer;
    private static TextWriter error;

    public static TextReader In {
        get {
            if (reader == null) {
                reader = new StreamReader(Console.OpenStandardInput());
            }
            return reader;
        }
    }

    public static TextWriter Out {
        get {
            if (writer == null) {
                writer = new StreamWriter(Console.OpenStandardOutput());
            }
            return writer;
        }
    }

    public static TextWriter Error {
        get {
            if (error == null) {
                error = new StreamWriter(Console.OpenStandardError());
            }
            return error;
        }
    }
}
```

<span data-ttu-id="0804f-1107">La `Console` classe contient trois propriétés, `In`, `Out`et `Error`, qui représentent respectivement les périphériques d’entrée, de sortie et d’erreur standard.</span><span class="sxs-lookup"><span data-stu-id="0804f-1107">The `Console` class contains three properties, `In`, `Out`, and `Error`, that represent the standard input, output, and error devices, respectively.</span></span> <span data-ttu-id="0804f-1108">En exposant ces membres en tant que propriétés, `Console` la classe peut différer leur initialisation jusqu’à ce qu’elles soient réellement utilisées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1108">By exposing these members as properties, the `Console` class can delay their initialization until they are actually used.</span></span> <span data-ttu-id="0804f-1109">Par exemple, lors de la première `Out` référence à la propriété, comme dans</span><span class="sxs-lookup"><span data-stu-id="0804f-1109">For example, upon first referencing the `Out` property, as in</span></span>
```csharp
Console.Out.WriteLine("hello, world");
```
<span data-ttu-id="0804f-1110">le sous `TextWriter` -jacent pour le périphérique de sortie est créé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1110">the underlying `TextWriter` for the output device is created.</span></span> <span data-ttu-id="0804f-1111">Toutefois, si l’application ne fait pas référence `In` aux `Error` propriétés et, aucun objet n’est créé pour ces appareils.</span><span class="sxs-lookup"><span data-stu-id="0804f-1111">But if the application makes no reference to the `In` and `Error` properties, then no objects are created for those devices.</span></span>

### <a name="automatically-implemented-properties"></a><span data-ttu-id="0804f-1112">Propriétés implémentées automatiquement</span><span class="sxs-lookup"><span data-stu-id="0804f-1112">Automatically implemented properties</span></span>

<span data-ttu-id="0804f-1113">Une propriété implémentée automatiquement (ou ***propriété automatique*** pour Short) est une propriété non-extern non abstraite avec des corps d’accesseur de point-virgule uniquement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1113">An automatically implemented property (or ***auto-property*** for short), is a non-abstract non-extern property with semicolon-only accessor bodies.</span></span> <span data-ttu-id="0804f-1114">Les propriétés automatiques doivent avoir un accesseur get et peuvent éventuellement avoir un accesseur Set.</span><span class="sxs-lookup"><span data-stu-id="0804f-1114">Auto-properties must have a get accessor and can optionally have a set accessor.</span></span>

<span data-ttu-id="0804f-1115">Quand une propriété est spécifiée en tant que propriété implémentée automatiquement, un champ de stockage masqué est automatiquement disponible pour la propriété, et les accesseurs sont implémentés pour lire et écrire dans ce champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-1115">When a property is specified as an automatically implemented property, a hidden backing field is automatically available for the property, and the accessors are implemented to read from and write to that backing field.</span></span> <span data-ttu-id="0804f-1116">Si la propriété automatique n’a pas d’accesseur Set, le champ de stockage `readonly` est pris en compte ([champs en lecture seule](classes.md#readonly-fields)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1116">If the auto-property has no set accessor, the backing field is considered `readonly` ([Readonly fields](classes.md#readonly-fields)).</span></span> <span data-ttu-id="0804f-1117">Tout comme un `readonly` champ, une propriété automatique Getter uniquement peut également être assignée à dans le corps d’un constructeur de la classe englobante.</span><span class="sxs-lookup"><span data-stu-id="0804f-1117">Just like a `readonly` field, a getter-only auto-property can also be assigned to in the body of a constructor of the enclosing class.</span></span> <span data-ttu-id="0804f-1118">Une assignation de ce type est directement assignée au champ de stockage en lecture seule de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1118">Such an assignment assigns directly to the readonly backing field of the property.</span></span>

<span data-ttu-id="0804f-1119">Une propriété automatique peut éventuellement avoir un *property_initializer*, qui est appliqué directement au champ de stockage en tant que *variable_initializer* ([initialiseurs de variable](classes.md#variable-initializers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1119">An auto-property may optionally have a *property_initializer*, which is applied directly to the backing field as a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)).</span></span>

<span data-ttu-id="0804f-1120">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0804f-1120">The following example:</span></span>
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
<span data-ttu-id="0804f-1121">équivaut à la déclaration suivante :</span><span class="sxs-lookup"><span data-stu-id="0804f-1121">is equivalent to the following declaration:</span></span>
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

<span data-ttu-id="0804f-1122">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0804f-1122">The following example:</span></span>
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
<span data-ttu-id="0804f-1123">équivaut à la déclaration suivante :</span><span class="sxs-lookup"><span data-stu-id="0804f-1123">is equivalent to the following declaration:</span></span>
```csharp
public class ReadOnlyPoint
{
    private readonly int __x;
    private readonly int __y;
    public int X { get { return __x; } }
    public int Y { get { return __y; } }
    public ReadOnlyPoint(int x, int y) { __x = x; __y = y; }
}
```

<span data-ttu-id="0804f-1124">Notez que les assignations au champ ReadOnly sont autorisées, car elles se produisent dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1124">Notice that the assignments to the readonly field are legal, because they occur within the constructor.</span></span>


### <a name="accessibility"></a><span data-ttu-id="0804f-1125">Accessibilité</span><span class="sxs-lookup"><span data-stu-id="0804f-1125">Accessibility</span></span>

<span data-ttu-id="0804f-1126">Si un accesseur a un *accessor_modifier*, le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) de l’accesseur est déterminé à l’aide de l’accessibilité déclarée de *accessor_modifier*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1126">If an accessor has an *accessor_modifier*, the accessibility domain ([Accessibility domains](basic-concepts.md#accessibility-domains)) of the accessor is determined using the declared accessibility of the *accessor_modifier*.</span></span> <span data-ttu-id="0804f-1127">Si un accesseur n’a pas de *accessor_modifier*, le domaine d’accessibilité de l’accesseur est déterminé à partir de l’accessibilité déclarée de la propriété ou de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1127">If an accessor does not have an *accessor_modifier*, the accessibility domain of the accessor is determined from the declared accessibility of the property or indexer.</span></span>

<span data-ttu-id="0804f-1128">La présence d’un *accessor_modifier* n’affecte jamais la recherche de membre ([opérateurs](expressions.md#operators)) ou la résolution de surcharge ([résolution de surcharge](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1128">The presence of an *accessor_modifier* never affects member lookup ([Operators](expressions.md#operators)) or overload resolution ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="0804f-1129">Les modificateurs de la propriété ou de l’indexeur déterminent toujours la propriété ou l’indexeur auquel il est lié, quel que soit le contexte de l’accès.</span><span class="sxs-lookup"><span data-stu-id="0804f-1129">The modifiers on the property or indexer always determine which property or indexer is bound to, regardless of the context of the access.</span></span>

<span data-ttu-id="0804f-1130">Une fois qu’une propriété ou un indexeur particulier a été sélectionné, les domaines d’accessibilité des accesseurs spécifiques impliqués sont utilisés pour déterminer si cette utilisation est valide :</span><span class="sxs-lookup"><span data-stu-id="0804f-1130">Once a particular property or indexer has been selected, the accessibility domains of the specific accessors involved are used to determine if that usage is valid:</span></span>

*  <span data-ttu-id="0804f-1131">Si l’utilisation est en tant que valeur ([valeurs d’expressions](expressions.md#values-of-expressions)), `get` l’accesseur doit exister et être accessible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1131">If the usage is as a value ([Values of expressions](expressions.md#values-of-expressions)), the `get` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="0804f-1132">Si l’utilisation est la cible d’une assignation simple ([assignation simple](expressions.md#simple-assignment)), l' `set` accesseur doit exister et être accessible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1132">If the usage is as the target of a simple assignment ([Simple assignment](expressions.md#simple-assignment)), the `set` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="0804f-1133">Si l’utilisation est la cible de l’assignation composée ([assignation composée](expressions.md#compound-assignment)), ou en tant que cible des `++` opérateurs `--` ou ([fonctions membres](expressions.md#function-members) `get` 0,9, [expressions d’appel](expressions.md#invocation-expressions)), les accesseurs et l' `set` accesseur doit exister et être accessible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1133">If the usage is as the target of compound assignment ([Compound assignment](expressions.md#compound-assignment)), or as the target of the `++` or `--` operators ([Function members](expressions.md#function-members).9, [Invocation expressions](expressions.md#invocation-expressions)), both the `get` accessors and the `set` accessor must exist and be accessible.</span></span>

<span data-ttu-id="0804f-1134">Dans l’exemple suivant, la propriété `A.Text` est masquée par la `B.Text`propriété, même dans les contextes où `set` seul l’accesseur est appelé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1134">In the following example, the property `A.Text` is hidden by the property `B.Text`, even in contexts where only the `set` accessor is called.</span></span> <span data-ttu-id="0804f-1135">En revanche, la propriété `B.Count` n’est pas accessible à `M`la classe, donc la `A.Count` propriété accessible est utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="0804f-1135">In contrast, the property `B.Count` is not accessible to class `M`, so the accessible property `A.Count` is used instead.</span></span>

```csharp
class A
{
    public string Text {
        get { return "hello"; }
        set { }
    }

    public int Count {
        get { return 5; }
        set { }
    }
}

class B: A
{
    private string text = "goodbye"; 
    private int count = 0;

    new public string Text {
        get { return text; }
        protected set { text = value; }
    }

    new protected int Count { 
        get { return count; }
        set { count = value; }
    }
}

class M
{
    static void Main() {
        B b = new B();
        b.Count = 12;             // Calls A.Count set accessor
        int i = b.Count;          // Calls A.Count get accessor
        b.Text = "howdy";         // Error, B.Text set accessor not accessible
        string s = b.Text;        // Calls B.Text get accessor
    }
}
```

<span data-ttu-id="0804f-1136">Un accesseur utilisé pour implémenter une interface peut ne pas avoir de *accessor_modifier*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1136">An accessor that is used to implement an interface may not have an *accessor_modifier*.</span></span> <span data-ttu-id="0804f-1137">Si un seul accesseur est utilisé pour implémenter une interface, l’autre accesseur peut être déclaré avec un *accessor_modifier*:</span><span class="sxs-lookup"><span data-stu-id="0804f-1137">If only one accessor is used to implement an interface, the other accessor may be declared with an *accessor_modifier*:</span></span>
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public string Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a><span data-ttu-id="0804f-1138">Accesseurs de propriété Virtual, sealed, override et abstract</span><span class="sxs-lookup"><span data-stu-id="0804f-1138">Virtual, sealed, override, and abstract property accessors</span></span>

<span data-ttu-id="0804f-1139">Une `virtual` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels.</span><span class="sxs-lookup"><span data-stu-id="0804f-1139">A `virtual` property declaration specifies that the accessors of the property are virtual.</span></span> <span data-ttu-id="0804f-1140">Le `virtual` modificateur s’applique aux deux accesseurs d’une propriété en lecture-écriture ; il n’est pas possible pour un seul accesseur d’une propriété en lecture-écriture d’être virtuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-1140">The `virtual` modifier applies to both accessors of a read-write property—it is not possible for only one accessor of a read-write property to be virtual.</span></span>

<span data-ttu-id="0804f-1141">Une `abstract` déclaration de propriété spécifie que les accesseurs de la propriété sont virtuels, mais ne fournissent pas d’implémentation réelle des accesseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1141">An `abstract` property declaration specifies that the accessors of the property are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="0804f-1142">Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation des accesseurs en substituant la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1142">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the property.</span></span> <span data-ttu-id="0804f-1143">Comme un accesseur pour une déclaration de propriété abstraite ne fournit pas d’implémentation réelle, son *accessor_body* se compose simplement d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1143">Because an accessor for an abstract property declaration provides no actual implementation, its *accessor_body* simply consists of a semicolon.</span></span>

<span data-ttu-id="0804f-1144">Une déclaration de propriété qui comprend à `abstract` la `override` fois les modificateurs et spécifie que la propriété est abstraite et substitue une propriété de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-1144">A property declaration that includes both the `abstract` and `override` modifiers specifies that the property is abstract and overrides a base property.</span></span> <span data-ttu-id="0804f-1145">Les accesseurs d’une telle propriété sont également abstraits.</span><span class="sxs-lookup"><span data-stu-id="0804f-1145">The accessors of such a property are also abstract.</span></span>

<span data-ttu-id="0804f-1146">Les déclarations de propriété abstraite sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)). Les accesseurs d’une propriété virtuelle héritée peuvent être substitués dans une classe dérivée en incluant une déclaration de propriété `override` qui spécifie une directive.</span><span class="sxs-lookup"><span data-stu-id="0804f-1146">Abstract property declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).The accessors of an inherited virtual property can be overridden in a derived class by including a property declaration that specifies an `override` directive.</span></span> <span data-ttu-id="0804f-1147">C’est ce qu’on appelle une ***déclaration de propriété de substitution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1147">This is known as an ***overriding property declaration***.</span></span> <span data-ttu-id="0804f-1148">Une déclaration de propriété de substitution ne déclare pas de nouvelle propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1148">An overriding property declaration does not declare a new property.</span></span> <span data-ttu-id="0804f-1149">Au lieu de cela, il spécialise simplement les implémentations des accesseurs d’une propriété virtuelle existante.</span><span class="sxs-lookup"><span data-stu-id="0804f-1149">Instead, it simply specializes the implementations of the accessors of an existing virtual property.</span></span>

<span data-ttu-id="0804f-1150">Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité, le même type et le même nom que la propriété héritée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1150">An overriding property declaration must specify the exact same accessibility modifiers, type, and name as the inherited property.</span></span> <span data-ttu-id="0804f-1151">Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1151">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property must include only that accessor.</span></span> <span data-ttu-id="0804f-1152">Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1152">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="0804f-1153">Une déclaration de propriété de substitution peut inclure `sealed` le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1153">An overriding property declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="0804f-1154">L’utilisation de ce modificateur empêche une classe dérivée de substituer davantage la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1154">Use of this modifier prevents a derived class from further overriding the property.</span></span> <span data-ttu-id="0804f-1155">Les accesseurs d’une propriété sealed sont également sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-1155">The accessors of a sealed property are also sealed.</span></span>

<span data-ttu-id="0804f-1156">À l’exception des différences dans la syntaxe de déclaration et d’appel, les accesseurs Virtual, sealed, override et abstract se comportent exactement comme des méthodes virtuelles, sealed, override et abstract.</span><span class="sxs-lookup"><span data-stu-id="0804f-1156">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="0804f-1157">Plus précisément, les règles décrites dans les [méthodes virtuelles](classes.md#virtual-methods), les [méthodes override](classes.md#override-methods), les [méthodes sealed](classes.md#sealed-methods)et les [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si les accesseurs étaient des méthodes d’un formulaire correspondant :</span><span class="sxs-lookup"><span data-stu-id="0804f-1157">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form:</span></span>

*  <span data-ttu-id="0804f-1158">Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété et les mêmes modificateurs que la propriété conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1158">A `get` accessor corresponds to a parameterless method with a return value of the property type and the same modifiers as the containing property.</span></span>
*  <span data-ttu-id="0804f-1159">Un `set` accesseur correspond à une méthode avec un paramètre de valeur unique du type de propriété `void` , un type de retour et les mêmes modificateurs que la propriété conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1159">A `set` accessor corresponds to a method with a single value parameter of the property type, a `void` return type, and the same modifiers as the containing property.</span></span>

<span data-ttu-id="0804f-1160">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1160">In the example</span></span>
```csharp
abstract class A
{
    int y;

    public virtual int X {
        get { return 0; }
    }

    public virtual int Y {
        get { return y; }
        set { y = value; }
    }

    public abstract int Z { get; set; }
}
```
<span data-ttu-id="0804f-1161">`X`est une propriété en lecture seule virtuelle, `Y` est une propriété en lecture-écriture virtuelle et `Z` est une propriété en lecture-écriture abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-1161">`X` is a virtual read-only property, `Y` is a virtual read-write property, and `Z` is an abstract read-write property.</span></span> <span data-ttu-id="0804f-1162">Étant `Z` donné que est abstrait, la `A` classe conteneur doit également être déclarée abstract.</span><span class="sxs-lookup"><span data-stu-id="0804f-1162">Because `Z` is abstract, the containing class `A` must also be declared abstract.</span></span>

<span data-ttu-id="0804f-1163">Une classe qui dérive de `A` est affichée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="0804f-1163">A class that derives from `A` is show below:</span></span>
```csharp
class B: A
{
    int z;

    public override int X {
        get { return base.X + 1; }
    }

    public override int Y {
        set { base.Y = value < 0? 0: value; }
    }

    public override int Z {
        get { return z; }
        set { z = value; }
    }
}
```

<span data-ttu-id="0804f-1164">Ici, les déclarations de `X`, `Y`et `Z` remplacent les déclarations de propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1164">Here, the declarations of `X`, `Y`, and `Z` are overriding property declarations.</span></span> <span data-ttu-id="0804f-1165">Chaque déclaration de propriété correspond exactement aux modificateurs d’accessibilité, au type et au nom de la propriété héritée correspondante.</span><span class="sxs-lookup"><span data-stu-id="0804f-1165">Each property declaration exactly matches the accessibility modifiers, type, and name of the corresponding inherited property.</span></span> <span data-ttu-id="0804f-1166">L' `get` accesseur `X` de et `set` l’accesseur de `base` `Y` utilisent le mot clé pour accéder aux accesseurs hérités.</span><span class="sxs-lookup"><span data-stu-id="0804f-1166">The `get` accessor of `X` and the `set` accessor of `Y` use the `base` keyword to access the inherited accessors.</span></span> <span data-ttu-id="0804f-1167">La déclaration de `Z` substitue les deux accesseurs abstraits. par conséquent, il n’existe aucun membre de `B`fonction abstraite en attente dans, et `B` il est autorisé à être une classe non abstraite.</span><span class="sxs-lookup"><span data-stu-id="0804f-1167">The declaration of `Z` overrides both abstract accessors—thus, there are no outstanding abstract function members in `B`, and `B` is permitted to be a non-abstract class.</span></span>

<span data-ttu-id="0804f-1168">Quand une propriété est déclarée en `override`tant que, tout accesseur substitué doit être accessible au code de substitution.</span><span class="sxs-lookup"><span data-stu-id="0804f-1168">When a property is declared as an `override`, any overridden accessors must be accessible to the overriding code.</span></span> <span data-ttu-id="0804f-1169">En outre, l’accessibilité déclarée de la propriété ou de l’indexeur lui-même, et des accesseurs, doit correspondre à celle du membre et des accesseurs substitués.</span><span class="sxs-lookup"><span data-stu-id="0804f-1169">In addition, the declared accessibility of both the property or indexer itself, and of the accessors, must match that of the overridden member and accessors.</span></span> <span data-ttu-id="0804f-1170">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-1170">For example:</span></span>
```csharp
public class B
{
    public virtual int P {
        protected set {...}
        get {...}
    }
}

public class D: B
{
    public override int P {
        protected set {...}            // Must specify protected here
        get {...}                      // Must not have a modifier here
    }
}
```

## <a name="events"></a><span data-ttu-id="0804f-1171">Events</span><span class="sxs-lookup"><span data-stu-id="0804f-1171">Events</span></span>

<span data-ttu-id="0804f-1172">Un ***événement*** est un membre qui permet à un objet ou à une classe de fournir des notifications.</span><span class="sxs-lookup"><span data-stu-id="0804f-1172">An ***event*** is a member that enables an object or class to provide notifications.</span></span> <span data-ttu-id="0804f-1173">Les clients peuvent joindre du code exécutable pour les événements en fournissant des ***gestionnaires d’événements***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1173">Clients can attach executable code for events by supplying ***event handlers***.</span></span>

<span data-ttu-id="0804f-1174">Les événements sont déclarés à l’aide de *event_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1174">Events are declared using *event_declaration*s:</span></span>

```antlr
event_declaration
    : attributes? event_modifier* 'event' type variable_declarators ';'
    | attributes? event_modifier* 'event' type member_name '{' event_accessor_declarations '}'
    ;

event_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | event_modifier_unsafe
    ;

event_accessor_declarations
    : add_accessor_declaration remove_accessor_declaration
    | remove_accessor_declaration add_accessor_declaration
    ;

add_accessor_declaration
    : attributes? 'add' block
    ;

remove_accessor_declaration
    : attributes? 'remove' block
    ;
```

<span data-ttu-id="0804f-1175">Un *event_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `static` ([static et instance Méthodes](classes.md#static-and-instance-methods)), `virtual` ([méthodes virtuelles](classes.md#virtual-methods)), 0 ([méthodes override](classes.md#override-methods)), 2 ([méthodes sealed](classes.md#sealed-methods)), 4 ([méthodes abstraites](classes.md#abstract-methods)) et 6 ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1175">An *event_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="0804f-1176">Les déclarations d’événements sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1176">Event declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="0804f-1177">Le *type* d’une déclaration d’événement doit être un *delegate_type* ([types référence](types.md#reference-types)) et *delegate_type* doit être au moins aussi accessible que l’événement lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1177">The *type* of an event declaration must be a *delegate_type* ([Reference types](types.md#reference-types)), and that *delegate_type* must be at least as accessible as the event itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-1178">Une déclaration d’événement peut inclure *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1178">An event declaration may include *event_accessor_declarations*.</span></span> <span data-ttu-id="0804f-1179">Toutefois, si ce n’est pas le cas pour les événements non-extern et non abstraits, le compilateur les fournit automatiquement ([événements de type champ](classes.md#field-like-events)); pour les événements extern, les accesseurs sont fournis en externe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1179">However, if it does not, for non-extern, non-abstract events, the compiler supplies them automatically ([Field-like events](classes.md#field-like-events)); for extern events, the accessors are provided externally.</span></span>

<span data-ttu-id="0804f-1180">Une déclaration d’événement qui omet *event_accessor_declarations* définit un ou plusieurs événements, un pour chacun des *variable_declarator*s.</span><span class="sxs-lookup"><span data-stu-id="0804f-1180">An event declaration that omits *event_accessor_declarations* defines one or more events—one for each of the *variable_declarator*s.</span></span> <span data-ttu-id="0804f-1181">Les attributs et les modificateurs s’appliquent à tous les membres déclarés par un *event_declaration*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1181">The attributes and modifiers apply to all of the members declared by such an *event_declaration*.</span></span>

<span data-ttu-id="0804f-1182">Il s’agit d’une erreur au moment de la compilation pour qu’un *event_declaration* inclue à la fois le modificateur `abstract` et les *event_accessor_declarations*délimités par des accolades.</span><span class="sxs-lookup"><span data-stu-id="0804f-1182">It is a compile-time error for an *event_declaration* to include both the `abstract` modifier and brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="0804f-1183">Lorsqu’une déclaration Event comprend un `extern` modificateur, l’événement est considéré comme un ***événement externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1183">When an event declaration includes an `extern` modifier, the event is said to be an ***external event***.</span></span> <span data-ttu-id="0804f-1184">Étant donné qu’une déclaration d’événement externe ne fournit aucune implémentation réelle, il y a une erreur pour qu’elle inclue à la fois le modificateur `extern` et *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1184">Because an external event declaration provides no actual implementation, it is an error for it to include both the `extern` modifier and *event_accessor_declarations*.</span></span>

<span data-ttu-id="0804f-1185">Il s’agit d’une erreur de compilation pour un *variable_declarator* d’une déclaration d’événement avec un modificateur `abstract` ou `external` pour inclure un *variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1185">It is a compile-time error for a *variable_declarator* of an event declaration with an `abstract` or `external` modifier to include a *variable_initializer*.</span></span>

<span data-ttu-id="0804f-1186">Un événement peut être utilisé comme opérande de gauche des opérateurs `+=` et `-=` (assignation d'[événement](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1186">An event can be used as the left-hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="0804f-1187">Ces opérateurs sont utilisés, respectivement, pour attacher des gestionnaires d’événements à ou pour supprimer des gestionnaires d’événements d’un événement, et les modificateurs d’accès de l’événement contrôlent les contextes dans lesquels ces opérations sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1187">These operators are used, respectively, to attach event handlers to or to remove event handlers from an event, and the access modifiers of the event control the contexts in which such operations are permitted.</span></span>

<span data-ttu-id="0804f-1188">Étant `+=` donné `-=` que et sont les seules opérations autorisées sur un événement à l’extérieur du type qui déclare l’événement, le code externe peut ajouter et supprimer des gestionnaires pour un événement, mais ne peut pas obtenir ou modifier la liste sous-jacente de l’événement. gestionnaires d'.</span><span class="sxs-lookup"><span data-stu-id="0804f-1188">Since `+=` and `-=` are the only operations that are permitted on an event outside the type that declares the event, external code can add and remove handlers for an event, but cannot in any other way obtain or modify the underlying list of event handlers.</span></span>

<span data-ttu-id="0804f-1189">Dans une opération de la forme `x += y` ou `x -= y`, quand `x` est un événement et que la référence a lieu à l’extérieur du type qui contient `x`la déclaration de, le résultat de l' `void` opération a le type (par opposition à having type de `x`, avec la valeur de `x` après l’assignation.</span><span class="sxs-lookup"><span data-stu-id="0804f-1189">In an operation of the form `x += y` or `x -= y`, when `x` is an event and the reference takes place outside the type that contains the declaration of `x`, the result of the operation has type `void` (as opposed to having the type of `x`, with the value of `x` after the assignment).</span></span> <span data-ttu-id="0804f-1190">Cette règle interdit au code externe d’examiner indirectement le délégué sous-jacent d’un événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1190">This rule prohibits external code from indirectly examining the underlying delegate of an event.</span></span>

<span data-ttu-id="0804f-1191">L’exemple suivant montre comment les gestionnaires d’événements sont attachés aux instances `Button` de la classe :</span><span class="sxs-lookup"><span data-stu-id="0804f-1191">The following example shows how event handlers are attached to instances of the `Button` class:</span></span>
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;
}

public class LoginDialog: Form
{
    Button OkButton;
    Button CancelButton;

    public LoginDialog() {
        OkButton = new Button(...);
        OkButton.Click += new EventHandler(OkButtonClick);
        CancelButton = new Button(...);
        CancelButton.Click += new EventHandler(CancelButtonClick);
    }

    void OkButtonClick(object sender, EventArgs e) {
        // Handle OkButton.Click event
    }

    void CancelButtonClick(object sender, EventArgs e) {
        // Handle CancelButton.Click event
    }
}
```

<span data-ttu-id="0804f-1192">Ici, le `LoginDialog` constructeur d’instance crée `Button` deux instances et attache des gestionnaires d' `Click` événements aux événements.</span><span class="sxs-lookup"><span data-stu-id="0804f-1192">Here, the `LoginDialog` instance constructor creates two `Button` instances and attaches event handlers to the `Click` events.</span></span>

### <a name="field-like-events"></a><span data-ttu-id="0804f-1193">Événements de type champ</span><span class="sxs-lookup"><span data-stu-id="0804f-1193">Field-like events</span></span>

<span data-ttu-id="0804f-1194">Dans le texte de programme de la classe ou du struct qui contient la déclaration d’un événement, certains événements peuvent être utilisés comme des champs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1194">Within the program text of the class or struct that contains the declaration of an event, certain events can be used like fields.</span></span> <span data-ttu-id="0804f-1195">Pour être utilisée de cette manière, un événement ne doit pas être `abstract` ou `extern`, et ne doit pas inclure explicitement *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1195">To be used in this way, an event must not be `abstract` or `extern`, and must not explicitly include *event_accessor_declarations*.</span></span> <span data-ttu-id="0804f-1196">Un tel événement peut être utilisé dans n’importe quel contexte qui autorise un champ.</span><span class="sxs-lookup"><span data-stu-id="0804f-1196">Such an event can be used in any context that permits a field.</span></span> <span data-ttu-id="0804f-1197">Le champ contient un délégué ([délégués](delegates.md)) qui fait référence à la liste des gestionnaires d’événements qui ont été ajoutés à l’événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1197">The field contains a delegate ([Delegates](delegates.md)) which refers to the list of event handlers that have been added to the event.</span></span> <span data-ttu-id="0804f-1198">Si aucun gestionnaire d’événements n’a été ajouté, le champ `null`contient.</span><span class="sxs-lookup"><span data-stu-id="0804f-1198">If no event handlers have been added, the field contains `null`.</span></span>

<span data-ttu-id="0804f-1199">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1199">In the example</span></span>
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;

    protected void OnClick(EventArgs e) {
        if (Click != null) Click(this, e);
    }

    public void Reset() {
        Click = null;
    }
}
```
<span data-ttu-id="0804f-1200">`Click`est utilisé en tant que champ dans `Button` la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1200">`Click` is used as a field within the `Button` class.</span></span> <span data-ttu-id="0804f-1201">Comme le montre l’exemple, le champ peut être examiné, modifié et utilisé dans les expressions d’appel de délégué.</span><span class="sxs-lookup"><span data-stu-id="0804f-1201">As the example demonstrates, the field can be examined, modified, and used in delegate invocation expressions.</span></span> <span data-ttu-id="0804f-1202">La `OnClick` méthode de la `Button` classe « déclenche » l' `Click` événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1202">The `OnClick` method in the `Button` class "raises" the `Click` event.</span></span> <span data-ttu-id="0804f-1203">La notion de déclenchement d’un événement est équivalente à l’appel de délégué représenté par l’événement. Par conséquent, il n’existe aucune construction de langage particulière pour déclencher des événements.</span><span class="sxs-lookup"><span data-stu-id="0804f-1203">The notion of raising an event is precisely equivalent to invoking the delegate represented by the event—thus, there are no special language constructs for raising events.</span></span> <span data-ttu-id="0804f-1204">Notez que l’appel de délégué est précédé d’une vérification qui garantit que le délégué est non null.</span><span class="sxs-lookup"><span data-stu-id="0804f-1204">Note that the delegate invocation is preceded by a check that ensures the delegate is non-null.</span></span>

<span data-ttu-id="0804f-1205">En dehors de la Déclaration `Button` de la classe `Click` , le membre peut uniquement être utilisé sur le côté gauche des `+=` opérateurs et `-=` , comme dans</span><span class="sxs-lookup"><span data-stu-id="0804f-1205">Outside the declaration of the `Button` class, the `Click` member can only be used on the left-hand side of the `+=` and `-=` operators, as in</span></span>
```csharp
b.Click += new EventHandler(...);
```
<span data-ttu-id="0804f-1206">qui ajoute un délégué à la liste d’appel de l' `Click` événement, et</span><span class="sxs-lookup"><span data-stu-id="0804f-1206">which appends a delegate to the invocation list of the `Click` event, and</span></span>
```csharp
b.Click -= new EventHandler(...);
```
<span data-ttu-id="0804f-1207">qui supprime un délégué de la liste d’appel de l' `Click` événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1207">which removes a delegate from the invocation list of the `Click` event.</span></span>

<span data-ttu-id="0804f-1208">Lors de la compilation d’un événement de type champ, le compilateur crée automatiquement le stockage pour contenir le délégué et crée des accesseurs pour l’événement qui ajoute ou supprime des gestionnaires d’événements dans le champ délégué.</span><span class="sxs-lookup"><span data-stu-id="0804f-1208">When compiling a field-like event, the compiler automatically creates storage to hold the delegate, and creates accessors for the event that add or remove event handlers to the delegate field.</span></span> <span data-ttu-id="0804f-1209">Les opérations d’ajout et de suppression sont thread-safe et peuvent (mais n’ont pas besoin d’être) effectuées tout en détenant le verrou ([l’instruction lock](statements.md#the-lock-statement)) sur l’objet conteneur d’un événement d’instance, ou le type Object ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) pour un événement statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1209">The addition and removal operations are thread safe, and may (but are not required to) be done while holding the lock ([The lock statement](statements.md#the-lock-statement)) on the containing object for an instance event, or the type object ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) for a static event.</span></span>

<span data-ttu-id="0804f-1210">Ainsi, une déclaration d’événement d’instance de la forme :</span><span class="sxs-lookup"><span data-stu-id="0804f-1210">Thus, an instance event declaration of the form:</span></span>
```csharp
class X
{
    public event D Ev;
}
```
<span data-ttu-id="0804f-1211">sera compilé en une valeur équivalente à :</span><span class="sxs-lookup"><span data-stu-id="0804f-1211">will be compiled to something equivalent to:</span></span>
```csharp
class X
{
    private D __Ev;  // field to hold the delegate

    public event D Ev {
        add {
            /* add the delegate in a thread safe way */
        }

        remove {
            /* remove the delegate in a thread safe way */
        }
    }
}
```
<span data-ttu-id="0804f-1212">Dans la classe `X`, les références `Ev` à sur le côté gauche des `+=` opérateurs et `-=` entraînent l’appel des accesseurs Add et Remove.</span><span class="sxs-lookup"><span data-stu-id="0804f-1212">Within the class `X`, references to `Ev` on the left-hand side of the `+=` and `-=` operators cause the add and remove accessors to be invoked.</span></span> <span data-ttu-id="0804f-1213">Toutes les autres références `Ev` à sont compilées pour référencer `__Ev` le champ masqué à la place ([accès aux membres](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1213">All other references to `Ev` are compiled to reference the hidden field `__Ev` instead ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="0804f-1214">Le nom «`__Ev`» est arbitraire ; le champ masqué peut avoir n’importe quel nom ou aucun nom.</span><span class="sxs-lookup"><span data-stu-id="0804f-1214">The name "`__Ev`" is arbitrary; the hidden field could have any name or no name at all.</span></span>

### <a name="event-accessors"></a><span data-ttu-id="0804f-1215">Accesseurs d’événement</span><span class="sxs-lookup"><span data-stu-id="0804f-1215">Event accessors</span></span>

<span data-ttu-id="0804f-1216">Les déclarations d’événements omettent généralement *event_accessor_declarations*, comme dans l’exemple `Button` ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0804f-1216">Event declarations typically omit *event_accessor_declarations*, as in the `Button` example above.</span></span> <span data-ttu-id="0804f-1217">Une situation pour cela implique le cas où le coût de stockage d’un champ par événement n’est pas acceptable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1217">One situation for doing so involves the case in which the storage cost of one field per event is not acceptable.</span></span> <span data-ttu-id="0804f-1218">Dans ce cas, une classe peut inclure des *event_accessor_declarations* et utiliser un mécanisme privé pour stocker la liste des gestionnaires d’événements.</span><span class="sxs-lookup"><span data-stu-id="0804f-1218">In such cases, a class can include *event_accessor_declarations* and use a private mechanism for storing the list of event handlers.</span></span>

<span data-ttu-id="0804f-1219">Le *event_accessor_declarations* d’un événement spécifie les instructions exécutables associées à l’ajout et à la suppression de gestionnaires d’événements.</span><span class="sxs-lookup"><span data-stu-id="0804f-1219">The *event_accessor_declarations* of an event specify the executable statements associated with adding and removing event handlers.</span></span>

<span data-ttu-id="0804f-1220">Les déclarations d’accesseur se composent d’un *add_accessor_declaration* et d’un *remove_accessor_declaration*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1220">The accessor declarations consist of an *add_accessor_declaration* and a *remove_accessor_declaration*.</span></span> <span data-ttu-id="0804f-1221">Chaque déclaration d’accesseur se compose `add` du `remove` jeton ou suivi d’un *bloc*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1221">Each accessor declaration consists of the token `add` or `remove` followed by a *block*.</span></span> <span data-ttu-id="0804f-1222">Le *bloc* associé à un *add_accessor_declaration* spécifie les instructions à exécuter lors de l’ajout d’un gestionnaire d’événements, et le *bloc* associé à un *remove_accessor_declaration* spécifie les instructions à exécuter. Lorsqu’un gestionnaire d’événements est supprimé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1222">The *block* associated with an *add_accessor_declaration* specifies the statements to execute when an event handler is added, and the *block* associated with a *remove_accessor_declaration* specifies the statements to execute when an event handler is removed.</span></span>

<span data-ttu-id="0804f-1223">Chaque *add_accessor_declaration* et *remove_accessor_declaration* correspond à une méthode avec un paramètre de valeur unique du type d’événement et un type de retour `void`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1223">Each *add_accessor_declaration* and *remove_accessor_declaration* corresponds to a method with a single value parameter of the event type and a `void` return type.</span></span> <span data-ttu-id="0804f-1224">Le paramètre implicite d’un accesseur d' `value`événement est nommé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1224">The implicit parameter of an event accessor is named `value`.</span></span> <span data-ttu-id="0804f-1225">Lorsqu’un événement est utilisé dans une assignation d’événement, l’accesseur d’événement approprié est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1225">When an event is used in an event assignment, the appropriate event accessor is used.</span></span> <span data-ttu-id="0804f-1226">Plus précisément, si l’opérateur d' `+=` assignation est, l’accesseur Add est utilisé, et si l' `-=` opérateur d’assignation est, l’accesseur remove est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1226">Specifically, if the assignment operator is `+=` then the add accessor is used, and if the assignment operator is `-=` then the remove accessor is used.</span></span> <span data-ttu-id="0804f-1227">Dans les deux cas, l’opérande droit de l’opérateur d’assignation est utilisé comme argument de l’accesseur d’événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1227">In either case, the right-hand operand of the assignment operator is used as the argument to the event accessor.</span></span> <span data-ttu-id="0804f-1228">Le bloc d’un *add_accessor_declaration* ou d’un *remove_accessor_declaration* doit respecter les règles pour les méthodes `void` décrites dans le corps de la [méthode](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="0804f-1228">The block of an *add_accessor_declaration* or a *remove_accessor_declaration* must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="0804f-1229">En particulier, `return` les instructions d’un tel bloc ne sont pas autorisées à spécifier une expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-1229">In particular, `return` statements in such a block are not permitted to specify an expression.</span></span>

<span data-ttu-id="0804f-1230">Comme un accesseur d’événement a implicitement un `value`paramètre nommé, il s’agit d’une erreur de compilation pour une variable locale ou une constante déclarée dans un accesseur d’événement pour avoir ce nom.</span><span class="sxs-lookup"><span data-stu-id="0804f-1230">Since an event accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declared in an event accessor to have that name.</span></span>

<span data-ttu-id="0804f-1231">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1231">In the example</span></span>
```csharp
class Control: Component
{
    // Unique keys for events
    static readonly object mouseDownEventKey = new object();
    static readonly object mouseUpEventKey = new object();

    // Return event handler associated with key
    protected Delegate GetEventHandler(object key) {...}

    // Add event handler associated with key
    protected void AddEventHandler(object key, Delegate handler) {...}

    // Remove event handler associated with key
    protected void RemoveEventHandler(object key, Delegate handler) {...}

    // MouseDown event
    public event MouseEventHandler MouseDown {
        add { AddEventHandler(mouseDownEventKey, value); }
        remove { RemoveEventHandler(mouseDownEventKey, value); }
    }

    // MouseUp event
    public event MouseEventHandler MouseUp {
        add { AddEventHandler(mouseUpEventKey, value); }
        remove { RemoveEventHandler(mouseUpEventKey, value); }
    }

    // Invoke the MouseUp event
    protected void OnMouseUp(MouseEventArgs args) {
        MouseEventHandler handler; 
        handler = (MouseEventHandler)GetEventHandler(mouseUpEventKey);
        if (handler != null)
            handler(this, args);
    }
}
```
<span data-ttu-id="0804f-1232">la `Control` classe implémente un mécanisme de stockage interne pour les événements.</span><span class="sxs-lookup"><span data-stu-id="0804f-1232">the `Control` class implements an internal storage mechanism for events.</span></span> <span data-ttu-id="0804f-1233">La `AddEventHandler` méthode associe une valeur de délégué à une clé `GetEventHandler` , la méthode retourne le délégué actuellement associé à une clé, `RemoveEventHandler` et la méthode supprime un délégué en tant que gestionnaire d’événements pour l’événement spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-1233">The `AddEventHandler` method associates a delegate value with a key, the `GetEventHandler` method returns the delegate currently associated with a key, and the `RemoveEventHandler` method removes a delegate as an event handler for the specified event.</span></span> <span data-ttu-id="0804f-1234">Vraisemblablement, le mécanisme de stockage sous-jacent est conçu de telle sorte qu’il n’y `null` a aucun coût d’association d’une valeur de délégué à une clé, et par conséquent, les événements non gérés ne consomment aucun stockage.</span><span class="sxs-lookup"><span data-stu-id="0804f-1234">Presumably, the underlying storage mechanism is designed such that there is no cost for associating a `null` delegate value with a key, and thus unhandled events consume no storage.</span></span>

### <a name="static-and-instance-events"></a><span data-ttu-id="0804f-1235">Événements statiques et d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-1235">Static and instance events</span></span>

<span data-ttu-id="0804f-1236">Lorsqu’une déclaration Event comprend un `static` modificateur, l’événement est considéré comme un ***événement statique***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1236">When an event declaration includes a `static` modifier, the event is said to be a ***static event***.</span></span> <span data-ttu-id="0804f-1237">Si aucun `static` modificateur n’est présent, l’événement est considéré comme un ***événement d’instance***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1237">When no `static` modifier is present, the event is said to be an ***instance event***.</span></span>

<span data-ttu-id="0804f-1238">Un événement statique n’est pas associé à une instance spécifique, et il s’agit d’une erreur de compilation pour `this` faire référence aux accesseurs d’un événement statique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1238">A static event is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static event.</span></span>

<span data-ttu-id="0804f-1239">Un événement d’instance est associé à une instance donnée d’une classe, et cette instance est accessible en `this` tant que ([cet accès](expressions.md#this-access)) dans les accesseurs de cet événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1239">An instance event is associated with a given instance of a class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that event.</span></span>

<span data-ttu-id="0804f-1240">Lorsqu’un événement est référencé dans un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `E.M`, si `M` est un événement statique, `E` doit désigner un type contenant `M`, et si `M` est un événement d’instance, E doit désigner une instance d’un type contenant `M`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1240">When an event is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static event, `E` must denote a type containing `M`, and if `M` is an instance event, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="0804f-1241">Les différences entre les membres statiques et les membres d’instance sont abordées plus en détail dans les [membres statiques et d’instance](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="0804f-1241">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a><span data-ttu-id="0804f-1242">Accesseurs d’événement virtuels, sealed, override et abstract</span><span class="sxs-lookup"><span data-stu-id="0804f-1242">Virtual, sealed, override, and abstract event accessors</span></span>

<span data-ttu-id="0804f-1243">Une `virtual` déclaration d’événement spécifie que les accesseurs de cet événement sont virtuels.</span><span class="sxs-lookup"><span data-stu-id="0804f-1243">A `virtual` event declaration specifies that the accessors of that event are virtual.</span></span> <span data-ttu-id="0804f-1244">Le `virtual` modificateur s’applique aux deux accesseurs d’un événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1244">The `virtual` modifier applies to both accessors of an event.</span></span>

<span data-ttu-id="0804f-1245">Une `abstract` déclaration d’événement spécifie que les accesseurs de l’événement sont virtuels, mais ne fournissent pas d’implémentation réelle des accesseurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1245">An `abstract` event declaration specifies that the accessors of the event are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="0804f-1246">Au lieu de cela, les classes dérivées non abstraites sont requises pour fournir leur propre implémentation des accesseurs en substituant l’événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1246">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the event.</span></span> <span data-ttu-id="0804f-1247">Étant donné qu’une déclaration d’événement abstraite ne fournit pas d’implémentation réelle, elle ne peut pas fournir des *event_accessor_declarations*délimités par des accolades.</span><span class="sxs-lookup"><span data-stu-id="0804f-1247">Because an abstract event declaration provides no actual implementation, it cannot provide brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="0804f-1248">Une déclaration d’événement qui comprend à `abstract` la `override` fois les modificateurs et spécifie que l’événement est abstrait et substitue un événement de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-1248">An event declaration that includes both the `abstract` and `override` modifiers specifies that the event is abstract and overrides a base event.</span></span> <span data-ttu-id="0804f-1249">Les accesseurs d’un tel événement sont également abstraits.</span><span class="sxs-lookup"><span data-stu-id="0804f-1249">The accessors of such an event are also abstract.</span></span>

<span data-ttu-id="0804f-1250">Les déclarations d’événements abstract sont autorisées uniquement dans les classes abstraites ([classes abstraites](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1250">Abstract event declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="0804f-1251">Les accesseurs d’un événement virtuel hérité peuvent être substitués dans une classe dérivée en incluant une déclaration d’événement qui `override` spécifie un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1251">The accessors of an inherited virtual event can be overridden in a derived class by including an event declaration that specifies an `override` modifier.</span></span> <span data-ttu-id="0804f-1252">C’est ce qu’on appelle une ***déclaration d’événement de substitution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1252">This is known as an ***overriding event declaration***.</span></span> <span data-ttu-id="0804f-1253">Une déclaration d’événement de substitution ne déclare pas un nouvel événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1253">An overriding event declaration does not declare a new event.</span></span> <span data-ttu-id="0804f-1254">Au lieu de cela, il spécialise simplement les implémentations des accesseurs d’un événement virtuel existant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1254">Instead, it simply specializes the implementations of the accessors of an existing virtual event.</span></span>

<span data-ttu-id="0804f-1255">Une déclaration d’événement de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité, le même type et le même nom que l’événement substitué.</span><span class="sxs-lookup"><span data-stu-id="0804f-1255">An overriding event declaration must specify the exact same accessibility modifiers, type, and name as the overridden event.</span></span>

<span data-ttu-id="0804f-1256">Une déclaration d’événement de substitution peut inclure `sealed` le modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1256">An overriding event declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="0804f-1257">L’utilisation de ce modificateur empêche une classe dérivée de substituer davantage l’événement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1257">Use of this modifier prevents a derived class from further overriding the event.</span></span> <span data-ttu-id="0804f-1258">Les accesseurs d’un événement sealed sont également sealed.</span><span class="sxs-lookup"><span data-stu-id="0804f-1258">The accessors of a sealed event are also sealed.</span></span>

<span data-ttu-id="0804f-1259">Il s’agit d’une erreur au moment de la compilation pour qu’une déclaration d' `new` événement de substitution inclue un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1259">It is a compile-time error for an overriding event declaration to include a `new` modifier.</span></span>

<span data-ttu-id="0804f-1260">À l’exception des différences dans la syntaxe de déclaration et d’appel, les accesseurs Virtual, sealed, override et abstract se comportent exactement comme des méthodes virtuelles, sealed, override et abstract.</span><span class="sxs-lookup"><span data-stu-id="0804f-1260">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="0804f-1261">Plus précisément, les règles décrites dans les [méthodes virtuelles](classes.md#virtual-methods), les [méthodes override](classes.md#override-methods), les [méthodes sealed](classes.md#sealed-methods)et les [méthodes abstraites](classes.md#abstract-methods) s’appliquent comme si les accesseurs étaient des méthodes d’un formulaire correspondant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1261">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form.</span></span> <span data-ttu-id="0804f-1262">Chaque accesseur correspond à une méthode avec un paramètre de valeur unique du type d’événement `void` , un type de retour et les mêmes modificateurs que l’événement conteneur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1262">Each accessor corresponds to a method with a single value parameter of the event type, a `void` return type, and the same modifiers as the containing event.</span></span>

## <a name="indexers"></a><span data-ttu-id="0804f-1263">Indexeurs</span><span class="sxs-lookup"><span data-stu-id="0804f-1263">Indexers</span></span>

<span data-ttu-id="0804f-1264">Un ***indexeur*** est un membre qui permet à un objet d’être indexé de la même manière qu’un tableau.</span><span class="sxs-lookup"><span data-stu-id="0804f-1264">An ***indexer*** is a member that enables an object to be indexed in the same way as an array.</span></span> <span data-ttu-id="0804f-1265">Les indexeurs sont déclarés à l’aide de *indexer_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1265">Indexers are declared using *indexer_declaration*s:</span></span>

```antlr
indexer_declaration
    : attributes? indexer_modifier* indexer_declarator indexer_body
    ;

indexer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | indexer_modifier_unsafe
    ;

indexer_declarator
    : type 'this' '[' formal_parameter_list ']'
    | type interface_type '.' 'this' '[' formal_parameter_list ']'
    ;

indexer_body
    : '{' accessor_declarations '}' 
    | '=>' expression ';'
    ;
```

<span data-ttu-id="0804f-1266">Un *indexer_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)), le `new` ([le nouveau modificateur](classes.md#the-new-modifier)), `virtual` ([méthodes virtuelles ](classes.md#virtual-methods)), `override` ([méthodes override](classes.md#override-methods)), 0 ([méthodes sealed](classes.md#sealed-methods)), 2 ([méthodes abstraites](classes.md#abstract-methods)) et les modificateurs 4 ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1266">An *indexer_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="0804f-1267">Les déclarations de l’indexeur sont soumises aux mêmes règles que les déclarations de méthode ([méthodes](classes.md#methods)) en ce qui concerne les combinaisons valides de modificateurs, à la seule exception que le modificateur static n’est pas autorisé sur une déclaration d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1267">Indexer declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers, with the one exception being that the static modifier is not permitted on an indexer declaration.</span></span>

<span data-ttu-id="0804f-1268">Les modificateurs `virtual`, `override`et `abstract` s’excluent mutuellement, sauf dans un cas.</span><span class="sxs-lookup"><span data-stu-id="0804f-1268">The modifiers `virtual`, `override`, and `abstract` are mutually exclusive except in one case.</span></span> <span data-ttu-id="0804f-1269">Les `abstract` modificateurs et `override` peuvent être utilisés ensemble afin qu’un indexeur abstrait puisse remplacer un indexeur virtuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-1269">The `abstract` and `override` modifiers may be used together so that an abstract indexer can override a virtual one.</span></span>

<span data-ttu-id="0804f-1270">Le *type* d’une déclaration d’indexeur spécifie le type d’élément de l’indexeur introduit par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="0804f-1270">The *type* of an indexer declaration specifies the element type of the indexer introduced by the declaration.</span></span> <span data-ttu-id="0804f-1271">À moins que l’indexeur ne soit une implémentation de membre d’interface explicite, le type `this`est suivi du mot clé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1271">Unless the indexer is an explicit interface member implementation, the *type* is followed by the keyword `this`.</span></span> <span data-ttu-id="0804f-1272">Pour une implémentation de membre d’interface explicite, le *type* est suivi d’un *INTERFACE_TYPE*, d’un « `.` » et du mot clé `this`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1272">For an explicit interface member implementation, the *type* is followed by an *interface_type*, a "`.`", and the keyword `this`.</span></span> <span data-ttu-id="0804f-1273">Contrairement aux autres membres, les indexeurs n’ont pas de noms définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1273">Unlike other members, indexers do not have user-defined names.</span></span>

<span data-ttu-id="0804f-1274">*Formal_parameter_list* spécifie les paramètres de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1274">The *formal_parameter_list* specifies the parameters of the indexer.</span></span> <span data-ttu-id="0804f-1275">La liste de paramètres formels d’un indexeur correspond à celle d’une méthode ([paramètres de méthode](classes.md#method-parameters)), à ceci près qu’au moins un paramètre doit être `ref` spécifié `out` et que les modificateurs de paramètres et ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="0804f-1275">The formal parameter list of an indexer corresponds to that of a method ([Method parameters](classes.md#method-parameters)), except that at least one parameter must be specified, and that the `ref` and `out` parameter modifiers are not permitted.</span></span>

<span data-ttu-id="0804f-1276">Le *type* d’un indexeur et chacun des types référencés dans le *formal_parameter_list* doivent être au moins aussi accessibles que l’indexeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1276">The *type* of an indexer and each of the types referenced in the *formal_parameter_list* must be at least as accessible as the indexer itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-1277">Un *indexer_body* peut être constitué d’un ***corps d’accesseur*** ou d’un ***corps d’expression***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1277">An *indexer_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="0804f-1278">Dans un corps d’accesseur, *accessor_declarations*, qui doit être placé entre les jetons « `{` » et « `}` », déclare les accesseurs ([accesseurs](classes.md#accessors)) de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1278">In an accessor body, *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="0804f-1279">Les accesseurs spécifient les instructions exécutables associées à la lecture et à l’écriture de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1279">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="0804f-1280">Un corps d’expression composé de`=>`«» suivi d’une `E` expression et d’un point-virgule est exactement équivalent `{ get { return E; } }`au corps de l’instruction et ne peut donc être utilisé que pour spécifier des indexeurs Getter uniquement où le résultat de l’accesseur Get est fourni par une expression unique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1280">An expression body consisting of "`=>`" followed by an expression `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only indexers where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="0804f-1281">Même si la syntaxe d’accès à un élément d’indexeur est identique à celle d’un élément de tableau, un élément d’indexeur n’est pas classifié comme une variable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1281">Even though the syntax for accessing an indexer element is the same as that for an array element, an indexer element is not classified as a variable.</span></span> <span data-ttu-id="0804f-1282">Par conséquent, il n’est pas possible de passer un élément d’indexeur `out` en tant qu' `ref` argument ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-1282">Thus, it is not possible to pass an indexer element as a `ref` or `out` argument.</span></span>

<span data-ttu-id="0804f-1283">La liste de paramètres formels d’un indexeur définit la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1283">The formal parameter list of an indexer defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the indexer.</span></span> <span data-ttu-id="0804f-1284">Plus précisément, la signature d’un indexeur se compose du nombre et des types de ses paramètres formels.</span><span class="sxs-lookup"><span data-stu-id="0804f-1284">Specifically, the signature of an indexer consists of the number and types of its formal parameters.</span></span> <span data-ttu-id="0804f-1285">Le type d’élément et les noms des paramètres formels ne font pas partie de la signature d’un indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1285">The element type and names of the formal parameters are not part of an indexer's signature.</span></span>

<span data-ttu-id="0804f-1286">La signature d’un indexeur doit être différente des signatures de tous les autres indexeurs déclarés dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1286">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>

<span data-ttu-id="0804f-1287">Les indexeurs et les propriétés sont très similaires, mais diffèrent des façons suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-1287">Indexers and properties are very similar in concept, but differ in the following ways:</span></span>

*  <span data-ttu-id="0804f-1288">Une propriété est identifiée par son nom, tandis qu’un indexeur est identifié par sa signature.</span><span class="sxs-lookup"><span data-stu-id="0804f-1288">A property is identified by its name, whereas an indexer is identified by its signature.</span></span>
*  <span data-ttu-id="0804f-1289">Une propriété est accessible par le biais d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)), tandis qu’un élément d’indexeur est accessible via un *element_access* (accès à l'[indexeur](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1289">A property is accessed through a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)), whereas an indexer element is accessed through an *element_access* ([Indexer access](expressions.md#indexer-access)).</span></span>
*  <span data-ttu-id="0804f-1290">Une propriété peut être un `static` membre, alors qu’un indexeur est toujours un membre d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1290">A property can be a `static` member, whereas an indexer is always an instance member.</span></span>
*  <span data-ttu-id="0804f-1291">Un `get` accesseur d’une propriété correspond à une méthode sans paramètres, alors qu' `get` un accesseur d’un indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1291">A `get` accessor of a property corresponds to a method with no parameters, whereas a `get` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer.</span></span>
*  <span data-ttu-id="0804f-1292">Un `set` accesseur d’une propriété correspond à une méthode avec un paramètre unique `value`nommé, alors `set` qu’un accesseur d’un indexeur correspond à une méthode avec la même liste de paramètres formels que l’indexeur, plus un paramètre supplémentaire nommé `value`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1292">A `set` accessor of a property corresponds to a method with a single parameter named `value`, whereas a `set` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer, plus an additional parameter named `value`.</span></span>
*  <span data-ttu-id="0804f-1293">Il s’agit d’une erreur de compilation permettant à un accesseur d’indexeur de déclarer une variable locale portant le même nom qu’un paramètre d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1293">It is a compile-time error for an indexer accessor to declare a local variable with the same name as an indexer parameter.</span></span>
*  <span data-ttu-id="0804f-1294">Dans une déclaration de propriété de substitution, la propriété héritée est accessible à `base.P`l’aide `P` de la syntaxe, où est le nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1294">In an overriding property declaration, the inherited property is accessed using the syntax `base.P`, where `P` is the property name.</span></span> <span data-ttu-id="0804f-1295">Dans une déclaration d’indexeur de substitution, l’indexeur hérité est accessible à l' `base[E]`aide de `E` la syntaxe, où est une liste d’expressions séparées par des virgules.</span><span class="sxs-lookup"><span data-stu-id="0804f-1295">In an overriding indexer declaration, the inherited indexer is accessed using the syntax `base[E]`, where `E` is a comma separated list of expressions.</span></span>
*  <span data-ttu-id="0804f-1296">Il n’existe aucun concept d’indexeur implémenté automatiquement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1296">There is no concept of an "automatically implemented indexer".</span></span> <span data-ttu-id="0804f-1297">Il s’agit d’une erreur d’avoir un indexeur non abstrait non externe avec des accesseurs de point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1297">It is an error to have a non-abstract, non-external indexer with semicolon accessors.</span></span>

<span data-ttu-id="0804f-1298">Hormis ces différences, toutes les règles définies dans les [accesseurs](classes.md#accessors) et les [Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties) s’appliquent aux accesseurs d’indexeur ainsi qu’aux accesseurs de propriété.</span><span class="sxs-lookup"><span data-stu-id="0804f-1298">Aside from these differences, all rules defined in [Accessors](classes.md#accessors) and [Automatically implemented properties](classes.md#automatically-implemented-properties) apply to indexer accessors as well as to property accessors.</span></span>

<span data-ttu-id="0804f-1299">Lorsqu’une déclaration d’indexeur comprend `extern` un modificateur, l’indexeur est considéré comme un ***indexeur externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1299">When an indexer declaration includes an `extern` modifier, the indexer is said to be an ***external indexer***.</span></span> <span data-ttu-id="0804f-1300">Étant donné qu’une déclaration d’indexeur externe ne fournit aucune implémentation réelle, chacun de ses *accessor_declarations* se compose d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1300">Because an external indexer declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

<span data-ttu-id="0804f-1301">L’exemple ci-dessous déclare `BitArray` une classe qui implémente un indexeur pour accéder aux bits individuels dans le tableau de bits.</span><span class="sxs-lookup"><span data-stu-id="0804f-1301">The example below declares a `BitArray` class that implements an indexer for accessing the individual bits in the bit array.</span></span>
```csharp
using System;

class BitArray
{
    int[] bits;
    int length;

    public BitArray(int length) {
        if (length < 0) throw new ArgumentException();
        bits = new int[((length - 1) >> 5) + 1];
        this.length = length;
    }

    public int Length {
        get { return length; }
    }

    public bool this[int index] {
        get {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            return (bits[index >> 5] & 1 << index) != 0;
        }
        set {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            if (value) {
                bits[index >> 5] |= 1 << index;
            }
            else {
                bits[index >> 5] &= ~(1 << index);
            }
        }
    }
}
```

<span data-ttu-id="0804f-1302">Une instance de la `BitArray` classe consomme sensiblement moins de mémoire qu’une `bool[]` correspondante (puisque chaque valeur du premier n’occupe qu’un seul bit au lieu du dernier octet), mais elle autorise les mêmes opérations qu’un `bool[]`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1302">An instance of the `BitArray` class consumes substantially less memory than a corresponding `bool[]` (since each value of the former occupies only one bit instead of the latter's one byte), but it permits the same operations as a `bool[]`.</span></span>

<span data-ttu-id="0804f-1303">La classe `CountPrimes` suivante utilise un `BitArray` et l’algorithme « crible » classique pour calculer le nombre de premiers compris entre 1 et un maximum donné :</span><span class="sxs-lookup"><span data-stu-id="0804f-1303">The following `CountPrimes` class uses a `BitArray` and the classical "sieve" algorithm to compute the number of primes between 1 and a given maximum:</span></span>
```csharp
class CountPrimes
{
    static int Count(int max) {
        BitArray flags = new BitArray(max + 1);
        int count = 1;
        for (int i = 2; i <= max; i++) {
            if (!flags[i]) {
                for (int j = i * 2; j <= max; j += i) flags[j] = true;
                count++;
            }
        }
        return count;
    }

    static void Main(string[] args) {
        int max = int.Parse(args[0]);
        int count = Count(max);
        Console.WriteLine("Found {0} primes between 1 and {1}", count, max);
    }
}
```

<span data-ttu-id="0804f-1304">Notez que la syntaxe pour accéder aux éléments de `BitArray` est exactement la même que pour un. `bool[]`</span><span class="sxs-lookup"><span data-stu-id="0804f-1304">Note that the syntax for accessing elements of the `BitArray` is precisely the same as for a `bool[]`.</span></span>

<span data-ttu-id="0804f-1305">L’exemple suivant montre une classe de grille 26 \* 10 qui a un indexeur avec deux paramètres.</span><span class="sxs-lookup"><span data-stu-id="0804f-1305">The following example shows a 26 \* 10 grid class that has an indexer with two parameters.</span></span> <span data-ttu-id="0804f-1306">Le premier paramètre doit être une lettre majuscule ou minuscule dans la plage A-Z, tandis que le second doit être un entier compris dans la plage 0-9.</span><span class="sxs-lookup"><span data-stu-id="0804f-1306">The first parameter is required to be an upper- or lowercase letter in the range A-Z, and the second is required to be an integer in the range 0-9.</span></span>

```csharp
using System;

class Grid
{
    const int NumRows = 26;
    const int NumCols = 10;

    int[,] cells = new int[NumRows, NumCols];

    public int this[char c, int col] {
        get {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            return cells[c - 'A', col];
        }

        set {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            cells[c - 'A', col] = value;
        }
    }
}
```

### <a name="indexer-overloading"></a><span data-ttu-id="0804f-1307">Surcharge de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="0804f-1307">Indexer overloading</span></span>

<span data-ttu-id="0804f-1308">Les règles de résolution de surcharge de l’indexeur sont décrites dans [inférence de type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="0804f-1308">The indexer overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="operators"></a><span data-ttu-id="0804f-1309">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="0804f-1309">Operators</span></span>

<span data-ttu-id="0804f-1310">Un ***opérateur*** est un membre qui définit la signification d’un opérateur d’expression qui peut être appliqué aux instances de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1310">An ***operator*** is a member that defines the meaning of an expression operator that can be applied to instances of the class.</span></span> <span data-ttu-id="0804f-1311">Les opérateurs sont déclarés à l’aide de *operator_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1311">Operators are declared using *operator_declaration*s:</span></span>

```antlr
operator_declaration
    : attributes? operator_modifier+ operator_declarator operator_body
    ;

operator_modifier
    : 'public'
    | 'static'
    | 'extern'
    | operator_modifier_unsafe
    ;

operator_declarator
    : unary_operator_declarator
    | binary_operator_declarator
    | conversion_operator_declarator
    ;

unary_operator_declarator
    : type 'operator' overloadable_unary_operator '(' type identifier ')'
    ;

overloadable_unary_operator
    : '+' | '-' | '!' | '~' | '++' | '--' | 'true' | 'false'
    ;

binary_operator_declarator
    : type 'operator' overloadable_binary_operator '(' type identifier ',' type identifier ')'
    ;

overloadable_binary_operator
    : '+'   | '-'   | '*'   | '/'   | '%'   | '&'   | '|'   | '^'   | '<<'
    | right_shift | '=='  | '!='  | '>'   | '<'   | '>='  | '<='
    ;

conversion_operator_declarator
    : 'implicit' 'operator' type '(' type identifier ')'
    | 'explicit' 'operator' type '(' type identifier ')'
    ;

operator_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

<span data-ttu-id="0804f-1312">Il existe trois catégories d’opérateurs surchargeables : Opérateurs unaires ([opérateurs unaires](classes.md#unary-operators)), opérateurs binaires ([opérateurs binaires](classes.md#binary-operators)) et opérateurs de conversion ([opérateurs de conversion](classes.md#conversion-operators)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1312">There are three categories of overloadable operators: Unary operators ([Unary operators](classes.md#unary-operators)), binary operators ([Binary operators](classes.md#binary-operators)), and conversion operators ([Conversion operators](classes.md#conversion-operators)).</span></span>

<span data-ttu-id="0804f-1313">*Operator_body* est un point-virgule, un ***corps d’instruction*** ou un ***corps d’expression***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1313">The *operator_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="0804f-1314">Un corps d’instruction se compose d’un *bloc*, qui spécifie les instructions à exécuter lorsque l’opérateur est appelé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1314">A statement body consists of a *block*, which specifies the statements to execute when the operator is invoked.</span></span> <span data-ttu-id="0804f-1315">Le *bloc* doit se conformer aux règles pour les méthodes qui retournent des valeurs décrites dans le corps de la [méthode](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="0804f-1315">The *block* must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="0804f-1316">Un corps d’expression se `=>` compose de suivis d’une expression et d’un point-virgule, et désigne une expression unique à exécuter lorsque l’opérateur est appelé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1316">An expression body consists of `=>` followed by an expression and a semicolon, and denotes a single expression to perform when the operator is invoked.</span></span>

<span data-ttu-id="0804f-1317">Pour les opérateurs `extern`, *operator_body* se compose simplement d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1317">For `extern` operators, the *operator_body* consists simply of a semicolon.</span></span> <span data-ttu-id="0804f-1318">Pour tous les autres opérateurs, *operator_body* est soit un corps de bloc, soit un corps d’expression.</span><span class="sxs-lookup"><span data-stu-id="0804f-1318">For all other operators, the *operator_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="0804f-1319">Les règles suivantes s’appliquent à toutes les déclarations d’opérateur :</span><span class="sxs-lookup"><span data-stu-id="0804f-1319">The following rules apply to all operator declarations:</span></span>

*  <span data-ttu-id="0804f-1320">Une déclaration d’opérateur doit inclure à `public` la fois `static` un et un modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1320">An operator declaration must include both a `public` and a `static` modifier.</span></span>
*  <span data-ttu-id="0804f-1321">Le (s) paramètre (s) d’un opérateur doivent être des paramètres de valeur ([paramètres de valeur](variables.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1321">The parameter(s) of an operator must be value parameters ([Value parameters](variables.md#value-parameters)).</span></span> <span data-ttu-id="0804f-1322">Il s’agit d’une erreur de compilation pour qu’une déclaration d' `ref` opérateur `out` spécifie des paramètres ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-1322">It is a compile-time error for an operator declaration to specify `ref` or `out` parameters.</span></span>
*  <span data-ttu-id="0804f-1323">La signature d’un opérateur ([opérateurs unaires](classes.md#unary-operators), [opérateurs binaires](classes.md#binary-operators), [opérateurs de conversion](classes.md#conversion-operators)) doit être différente des signatures de tous les autres opérateurs déclarés dans la même classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1323">The signature of an operator ([Unary operators](classes.md#unary-operators), [Binary operators](classes.md#binary-operators), [Conversion operators](classes.md#conversion-operators)) must differ from the signatures of all other operators declared in the same class.</span></span>
*  <span data-ttu-id="0804f-1324">Tous les types référencés dans une déclaration d’opérateur doivent être au moins aussi accessibles que l’opérateur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1324">All types referenced in an operator declaration must be at least as accessible as the operator itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>
*  <span data-ttu-id="0804f-1325">Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1325">It is an error for the same modifier to appear multiple times in an operator declaration.</span></span>

<span data-ttu-id="0804f-1326">Chaque catégorie d’opérateur impose des restrictions supplémentaires, comme décrit dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="0804f-1326">Each operator category imposes additional restrictions, as described in the following sections.</span></span>

<span data-ttu-id="0804f-1327">Comme les autres membres, les opérateurs déclarés dans une classe de base sont hérités par les classes dérivées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1327">Like other members, operators declared in a base class are inherited by derived classes.</span></span> <span data-ttu-id="0804f-1328">Étant donné que les déclarations d’opérateur requièrent toujours la classe ou le struct dans lequel l’opérateur est déclaré pour participer à la signature de l’opérateur, il n’est pas possible pour un opérateur déclaré dans une classe dérivée de masquer un opérateur déclaré dans une classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-1328">Because operator declarations always require the class or struct in which the operator is declared to participate in the signature of the operator, it is not possible for an operator declared in a derived class to hide an operator declared in a base class.</span></span> <span data-ttu-id="0804f-1329">Ainsi, le `new` modificateur n’est jamais requis et, par conséquent, n’est jamais autorisé dans une déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1329">Thus, the `new` modifier is never required, and therefore never permitted, in an operator declaration.</span></span>

<span data-ttu-id="0804f-1330">Vous trouverez des informations supplémentaires sur les opérateurs unaires et binaires dans [opérateurs](expressions.md#operators).</span><span class="sxs-lookup"><span data-stu-id="0804f-1330">Additional information on unary and binary operators can be found in [Operators](expressions.md#operators).</span></span>

<span data-ttu-id="0804f-1331">Vous trouverez des informations supplémentaires sur les opérateurs de conversion dans [les conversions définies par l’utilisateur](conversions.md#user-defined-conversions).</span><span class="sxs-lookup"><span data-stu-id="0804f-1331">Additional information on conversion operators can be found in [User-defined conversions](conversions.md#user-defined-conversions).</span></span>

### <a name="unary-operators"></a><span data-ttu-id="0804f-1332">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="0804f-1332">Unary operators</span></span>

<span data-ttu-id="0804f-1333">Les règles suivantes s’appliquent aux déclarations d’opérateur `T` unaires, où désigne le type d’instance de la classe ou du struct qui contient la déclaration d’opérateur :</span><span class="sxs-lookup"><span data-stu-id="0804f-1333">The following rules apply to unary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="0804f-1334">Un opérateur `+` `-`unaire `~` `T` ,, ou doit accepter un seul paramètre de type ou `T?` et peut retourner n’importe quel type. `!`</span><span class="sxs-lookup"><span data-stu-id="0804f-1334">A unary `+`, `-`, `!`, or `~` operator must take a single parameter of type `T` or `T?` and can return any type.</span></span>
*  <span data-ttu-id="0804f-1335">Un opérateur `++` or `--` unaire doit accepter un seul paramètre de `T` type `T?` ou et doit retourner ce même type ou un type dérivé de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="0804f-1335">A unary `++` or `--` operator must take a single parameter of type `T` or `T?` and must return that same type or a type derived from it.</span></span>
*  <span data-ttu-id="0804f-1336">Un opérateur `true` or `false` unaire doit accepter un seul paramètre de `T` type `T?` ou et doit retourner `bool`le type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1336">A unary `true` or `false` operator must take a single parameter of type `T` or `T?` and must return type `bool`.</span></span>

<span data-ttu-id="0804f-1337">La signature d’un opérateur unaire se compose du jeton d'`+`opérateur `-`( `!`, `~`, `++`, `--`, `true`,, `false`ou) et du type du paramètre formel unique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1337">The signature of a unary operator consists of the operator token (`+`, `-`, `!`, `~`, `++`, `--`, `true`, or `false`) and the type of the single formal parameter.</span></span> <span data-ttu-id="0804f-1338">Le type de retour ne fait pas partie de la signature d’un opérateur unaire, ni du nom du paramètre formel.</span><span class="sxs-lookup"><span data-stu-id="0804f-1338">The return type is not part of a unary operator's signature, nor is the name of the formal parameter.</span></span>

<span data-ttu-id="0804f-1339">Les `true` opérateurs `false` unaires et requièrent une déclaration de couple.</span><span class="sxs-lookup"><span data-stu-id="0804f-1339">The `true` and `false` unary operators require pair-wise declaration.</span></span> <span data-ttu-id="0804f-1340">Une erreur de compilation se produit si une classe déclare l’un de ces opérateurs sans également déclarer l’autre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1340">A compile-time error occurs if a class declares one of these operators without also declaring the other.</span></span> <span data-ttu-id="0804f-1341">Les `true` opérateurs `false` et sont décrits plus en détail dans les [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators) et les [expressions booléennes](expressions.md#boolean-expressions).</span><span class="sxs-lookup"><span data-stu-id="0804f-1341">The `true` and `false` operators are described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators) and [Boolean expressions](expressions.md#boolean-expressions).</span></span>

<span data-ttu-id="0804f-1342">L’exemple suivant illustre une implémentation et une utilisation ultérieure `operator ++` de pour une classe Vector entière :</span><span class="sxs-lookup"><span data-stu-id="0804f-1342">The following example shows an implementation and subsequent usage of `operator ++` for an integer vector class:</span></span>
```csharp
public class IntVector
{
    public IntVector(int length) {...}

    public int Length {...}                 // read-only property

    public int this[int index] {...}        // read-write indexer

    public static IntVector operator ++(IntVector iv) {
        IntVector temp = new IntVector(iv.Length);
        for (int i = 0; i < iv.Length; i++)
            temp[i] = iv[i] + 1;
        return temp;
    }
}

class Test
{
    static void Main() {
        IntVector iv1 = new IntVector(4);    // vector of 4 x 0
        IntVector iv2;

        iv2 = iv1++;    // iv2 contains 4 x 0, iv1 contains 4 x 1
        iv2 = ++iv1;    // iv2 contains 4 x 2, iv1 contains 4 x 2
    }
}
```

<span data-ttu-id="0804f-1343">Notez comment la méthode d’opérateur retourne la valeur produite par l’ajout de 1 à l’opérande, tout comme les opérateurs d’incrémentation et de décrémentation suffixés ([opérateurs d’incrémentation et de décrémentation suffixés](expressions.md#postfix-increment-and-decrement-operators)) et les opérateurs d’incrémentation et de décrémentation de préfixe ([préfixe opérateurs d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1343">Note how the operator method returns the value produced by adding 1 to the operand, just like the  postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)), and the prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="0804f-1344">Contrairement à C++, cette méthode n’a pas besoin de modifier directement la valeur de son opérande.</span><span class="sxs-lookup"><span data-stu-id="0804f-1344">Unlike in C++, this method need not modify the value of its operand directly.</span></span> <span data-ttu-id="0804f-1345">En fait, la modification de la valeur de l’opérande violerait la sémantique standard de l’opérateur d’incrémentation suffixée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1345">In fact, modifying the operand value would violate the standard semantics of the postfix increment operator.</span></span>

### <a name="binary-operators"></a><span data-ttu-id="0804f-1346">Opérateurs binaires</span><span class="sxs-lookup"><span data-stu-id="0804f-1346">Binary operators</span></span>

<span data-ttu-id="0804f-1347">Les règles suivantes s’appliquent aux déclarations d’opérateur `T` binaire, où dénote le type d’instance de la classe ou du struct qui contient la déclaration d’opérateur :</span><span class="sxs-lookup"><span data-stu-id="0804f-1347">The following rules apply to binary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="0804f-1348">Un opérateur binaire sans décalage doit accepter deux paramètres, au moins un qui doit avoir le type `T` ou `T?`, et peut retourner tout type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1348">A binary non-shift operator must take two parameters, at least one of which must have type `T` or `T?`, and can return any type.</span></span>
*  <span data-ttu-id="0804f-1349">Un binaire `<<` ou `>>` un opérateur doit accepter deux paramètres, le premier qui doit avoir le `T` type `T?` ou et le second, le type `int` ou `int?`, et peut retourner n’importe quel type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1349">A binary `<<` or `>>` operator must take two parameters, the first of which must have type `T` or `T?` and the second of which must have type `int` or `int?`, and can return any type.</span></span>

<span data-ttu-id="0804f-1350">La signature d’un opérateur binaire se compose du jeton d’opérateur`+`( `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`,, `==`, `!=`, ,,`>`ou )etlestypesdesdeux`<=`paramètres formels. `<` `>=`</span><span class="sxs-lookup"><span data-stu-id="0804f-1350">The signature of a binary operator consists of the operator token (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, or `<=`) and the types of the two formal parameters.</span></span> <span data-ttu-id="0804f-1351">Le type de retour et les noms des paramètres formels ne font pas partie de la signature d’un opérateur binaire.</span><span class="sxs-lookup"><span data-stu-id="0804f-1351">The return type and the names of the formal parameters are not part of a binary operator's signature.</span></span>

<span data-ttu-id="0804f-1352">Certains opérateurs binaires requièrent une déclaration de couple.</span><span class="sxs-lookup"><span data-stu-id="0804f-1352">Certain binary operators require pair-wise declaration.</span></span> <span data-ttu-id="0804f-1353">Pour chaque déclaration de l’un des opérateurs d’une paire, il doit y avoir une déclaration correspondante de l’autre opérateur de la paire.</span><span class="sxs-lookup"><span data-stu-id="0804f-1353">For every declaration of either operator of a pair, there must be a matching declaration of the other operator of the pair.</span></span> <span data-ttu-id="0804f-1354">Deux déclarations d’opérateur correspondent lorsqu’elles ont le même type de retour et le même type pour chaque paramètre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1354">Two operator declarations match when they have the same return type and the same type for each parameter.</span></span> <span data-ttu-id="0804f-1355">Les opérateurs suivants requièrent une déclaration de paires :</span><span class="sxs-lookup"><span data-stu-id="0804f-1355">The following operators require pair-wise declaration:</span></span>

*  <span data-ttu-id="0804f-1356">`operator ==` et `operator !=`</span><span class="sxs-lookup"><span data-stu-id="0804f-1356">`operator ==` and `operator !=`</span></span>
*  <span data-ttu-id="0804f-1357">`operator >` et `operator <`</span><span class="sxs-lookup"><span data-stu-id="0804f-1357">`operator >` and `operator <`</span></span>
*  <span data-ttu-id="0804f-1358">`operator >=` et `operator <=`</span><span class="sxs-lookup"><span data-stu-id="0804f-1358">`operator >=` and `operator <=`</span></span>

### <a name="conversion-operators"></a><span data-ttu-id="0804f-1359">Opérateurs de conversion</span><span class="sxs-lookup"><span data-stu-id="0804f-1359">Conversion operators</span></span>

<span data-ttu-id="0804f-1360">Une déclaration d’opérateur de conversion introduit une ***conversion définie par l’utilisateur*** ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) qui augmente les conversions implicites et explicites prédéfinies.</span><span class="sxs-lookup"><span data-stu-id="0804f-1360">A conversion operator declaration introduces a ***user-defined conversion*** ([User-defined conversions](conversions.md#user-defined-conversions)) which augments the pre-defined implicit and explicit conversions.</span></span>

<span data-ttu-id="0804f-1361">Une déclaration d’opérateur de conversion qui `implicit` inclut le mot clé introduit une conversion implicite définie par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1361">A conversion operator declaration that includes the `implicit` keyword introduces a user-defined implicit conversion.</span></span> <span data-ttu-id="0804f-1362">Les conversions implicites peuvent se produire dans diverses situations, y compris les appels de membres de fonction, les expressions de Cast et les assignations.</span><span class="sxs-lookup"><span data-stu-id="0804f-1362">Implicit conversions can occur in a variety of situations, including function member invocations, cast expressions, and assignments.</span></span> <span data-ttu-id="0804f-1363">Cela est décrit plus en détail dans [conversions implicites](conversions.md#implicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="0804f-1363">This is described further in [Implicit conversions](conversions.md#implicit-conversions).</span></span>

<span data-ttu-id="0804f-1364">Une déclaration d’opérateur de conversion qui `explicit` inclut le mot clé introduit une conversion explicite définie par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1364">A conversion operator declaration that includes the `explicit` keyword introduces a user-defined explicit conversion.</span></span> <span data-ttu-id="0804f-1365">Les conversions explicites peuvent se produire dans les expressions de Cast et sont décrites plus en détail dans les [conversions explicites](conversions.md#explicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="0804f-1365">Explicit conversions can occur in cast expressions, and are described further in [Explicit conversions](conversions.md#explicit-conversions).</span></span>

<span data-ttu-id="0804f-1366">Un opérateur de conversion convertit un type source, indiqué par le type de paramètre de l’opérateur de conversion, en un type cible, indiqué par le type de retour de l’opérateur de conversion.</span><span class="sxs-lookup"><span data-stu-id="0804f-1366">A conversion operator converts from a source type, indicated by the parameter type of the conversion operator, to a target type, indicated by the return type of the conversion operator.</span></span>

<span data-ttu-id="0804f-1367">Pour un `S` type de source et un type `T`cible donnés, `T` si `S` ou sont des types `S0` Nullable `T0` , Let et font référence à leurs `S0` types `T0` sous-jacents ; sinon, et sont égal à `S` et `T` , respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1367">For a given source type `S` and target type `T`, if `S` or `T` are nullable types, let `S0` and `T0` refer to their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span> <span data-ttu-id="0804f-1368">Une classe ou un struct est autorisé à déclarer une conversion d’un type `S` source en type `T` cible uniquement si toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="0804f-1368">A class or struct is permitted to declare a conversion from a source type `S` to a target type `T` only if all of the following are true:</span></span>

*  <span data-ttu-id="0804f-1369">`S0`et `T0` sont des types différents.</span><span class="sxs-lookup"><span data-stu-id="0804f-1369">`S0` and `T0` are different types.</span></span>
*  <span data-ttu-id="0804f-1370">`S0` Ou`T0` est le type de classe ou de struct dans lequel la déclaration d’opérateur a lieu.</span><span class="sxs-lookup"><span data-stu-id="0804f-1370">Either `S0` or `T0` is the class or struct type in which the operator declaration takes place.</span></span>
*  <span data-ttu-id="0804f-1371">Ni `S0`, ni `T0` n’est un *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1371">Neither `S0` nor `T0` is an *interface_type*.</span></span>
*  <span data-ttu-id="0804f-1372">En excluant les conversions définies par l’utilisateur, il n' `S` existe `T` pas de conversion `S`de vers ou de `T` à.</span><span class="sxs-lookup"><span data-stu-id="0804f-1372">Excluding user-defined conversions, a conversion does not exist from `S` to `T` or from `T` to `S`.</span></span>

<span data-ttu-id="0804f-1373">Dans le cadre de ces règles, tous les paramètres de type `S` associés `T` à ou sont considérés comme des types uniques qui n’ont aucune relation d’héritage avec d’autres types, et toutes les contraintes sur ces paramètres de type sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1373">For the purposes of these rules, any type parameters associated with `S` or `T` are considered to be unique types that have no inheritance relationship with other types, and any constraints on those type parameters are ignored.</span></span>

<span data-ttu-id="0804f-1374">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1374">In the example</span></span>
```csharp
class C<T> {...}

class D<T>: C<T>
{
    public static implicit operator C<int>(D<T> value) {...}      // Ok
    public static implicit operator C<string>(D<T> value) {...}   // Ok
    public static implicit operator C<T>(D<T> value) {...}        // Error
}
```
<span data-ttu-id="0804f-1375">les deux premières déclarations d’opérateur sont autorisées, car, à des fins d' [indexeurs](classes.md#indexers) `T` . 3 `int` et `string` et, respectivement, sont considérés comme des types uniques sans relation.</span><span class="sxs-lookup"><span data-stu-id="0804f-1375">the first two operator declarations are permitted because, for the purposes of [Indexers](classes.md#indexers).3, `T` and `int` and `string` respectively are considered unique types with no relationship.</span></span> <span data-ttu-id="0804f-1376">Toutefois, le troisième opérateur est une erreur, `C<T>` car est la classe de `D<T>`base de.</span><span class="sxs-lookup"><span data-stu-id="0804f-1376">However, the third operator is an error because `C<T>` is the base class of `D<T>`.</span></span>

<span data-ttu-id="0804f-1377">À partir de la deuxième règle, il suit qu’un opérateur de conversion doit effectuer une conversion vers ou à partir du type de classe ou de struct dans lequel l’opérateur est déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-1377">From the second rule it follows that a conversion operator must convert either to or from the class or struct type in which the operator is declared.</span></span> <span data-ttu-id="0804f-1378">Par exemple, il est possible pour `C` un type de classe ou de struct de définir une conversion de `C` vers `int` `int` `bool`et `C`de à, mais `int` pas de à.</span><span class="sxs-lookup"><span data-stu-id="0804f-1378">For example, it is possible for a class or struct type `C` to define a conversion from `C` to `int` and from `int` to `C`, but not from `int` to `bool`.</span></span>

<span data-ttu-id="0804f-1379">Il n’est pas possible de redéfinir directement une conversion prédéfinie.</span><span class="sxs-lookup"><span data-stu-id="0804f-1379">It is not possible to directly redefine a pre-defined conversion.</span></span> <span data-ttu-id="0804f-1380">Ainsi, les opérateurs de conversion ne sont pas autorisés à effectuer `object` une conversion depuis ou vers, car les conversions implicites et explicites existent déjà entre `object` et tous les autres types.</span><span class="sxs-lookup"><span data-stu-id="0804f-1380">Thus, conversion operators are not allowed to convert from or to `object` because implicit and explicit conversions already exist between `object` and all other types.</span></span> <span data-ttu-id="0804f-1381">De même, ni la source ni les types cibles d’une conversion ne peuvent être un type de base de l’autre, car une conversion existera déjà.</span><span class="sxs-lookup"><span data-stu-id="0804f-1381">Likewise, neither the source nor the target types of a conversion can be a base type of the other, since a conversion would then already exist.</span></span>

<span data-ttu-id="0804f-1382">Toutefois, il est possible de déclarer des opérateurs sur des types génériques qui, pour des arguments de type particuliers, spécifient des conversions qui existent déjà en tant que conversions prédéfinies.</span><span class="sxs-lookup"><span data-stu-id="0804f-1382">However, it is possible to declare operators on generic types that, for particular type arguments, specify conversions that already exist as pre-defined conversions.</span></span> <span data-ttu-id="0804f-1383">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1383">In the example</span></span>
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
<span data-ttu-id="0804f-1384">Quand le `object` type est spécifié en tant qu’argument `T`de type pour, le deuxième opérateur déclare une conversion qui existe déjà (implicite et, par conséquent, une conversion explicite existe de tout type `object`en type).</span><span class="sxs-lookup"><span data-stu-id="0804f-1384">when type `object` is specified as a type argument for `T`, the second operator declares a conversion that already exists (an implicit, and therefore also an explicit, conversion exists from any type to type `object`).</span></span>

<span data-ttu-id="0804f-1385">Dans les cas où une conversion prédéfinie existe entre deux types, toutes les conversions définies par l’utilisateur entre ces types sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1385">In cases where a pre-defined conversion exists between two types, any user-defined conversions between those types are ignored.</span></span> <span data-ttu-id="0804f-1386">Plus précisément :</span><span class="sxs-lookup"><span data-stu-id="0804f-1386">Specifically:</span></span>

*  <span data-ttu-id="0804f-1387">Si une conversion implicite prédéfinie ([conversions implicites](conversions.md#implicit-conversions)) existe `S` du type `T`en type, toutes les conversions définies par l’utilisateur (implicites `T` ou explicites) de `S` à sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1387">If a pre-defined implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from type `S` to type `T`, all user-defined conversions (implicit or explicit) from `S` to `T` are ignored.</span></span>
*  <span data-ttu-id="0804f-1388">Si une conversion explicite prédéfinie ([conversions explicites](conversions.md#explicit-conversions)) existe du `S` type en `T`type, toutes les conversions explicites définies `S` par `T` l’utilisateur de à sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1388">If a pre-defined explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) exists from type `S` to type `T`, any user-defined explicit conversions from `S` to `T` are ignored.</span></span> <span data-ttu-id="0804f-1389">Par</span><span class="sxs-lookup"><span data-stu-id="0804f-1389">Furthermore:</span></span>

<span data-ttu-id="0804f-1390">Si `T` est un type interface, les conversions implicites définies par `S` l' `T` utilisateur de à sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="0804f-1390">If `T` is an interface type, user-defined implicit conversions from `S` to `T` are ignored.</span></span>

<span data-ttu-id="0804f-1391">Sinon, les conversions implicites définies par `S` l' `T` utilisateur de à sont toujours prises en compte.</span><span class="sxs-lookup"><span data-stu-id="0804f-1391">Otherwise, user-defined implicit conversions from `S` to `T` are still considered.</span></span>

<span data-ttu-id="0804f-1392">Pour tous les types `object`, mais, les opérateurs déclarés par le `Convertible<T>` type ci-dessus ne sont pas en conflit avec les conversions prédéfinies.</span><span class="sxs-lookup"><span data-stu-id="0804f-1392">For all types but `object`, the operators declared by the `Convertible<T>` type above do not conflict with pre-defined conversions.</span></span> <span data-ttu-id="0804f-1393">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-1393">For example:</span></span>
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

<span data-ttu-id="0804f-1394">Toutefois, pour les `object`conversions de type, les conversions prédéfinies masquent les conversions définies par l’utilisateur dans tous les cas, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="0804f-1394">However, for type `object`, pre-defined conversions hide the user-defined conversions in all cases but one:</span></span>

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

<span data-ttu-id="0804f-1395">Les conversions définies par l’utilisateur ne sont pas autorisées à convertir à partir de ou en *INTERFACE_TYPE*s.</span><span class="sxs-lookup"><span data-stu-id="0804f-1395">User-defined conversions are not allowed to convert from or to *interface_type*s.</span></span> <span data-ttu-id="0804f-1396">En particulier, cette restriction garantit qu’aucune transformation définie par l’utilisateur ne se produit lors de la conversion vers un *INTERFACE_TYPE*, et qu’une conversion vers un *INTERFACE_TYPE* réussit uniquement si l’objet en cours de conversion implémente réellement le *INTERFACE_TYPE*spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-1396">In particular, this restriction ensures that no user-defined transformations occur when converting to an *interface_type*, and that a conversion to an *interface_type* succeeds only if the object being converted actually implements the specified *interface_type*.</span></span>

<span data-ttu-id="0804f-1397">La signature d’un opérateur de conversion se compose du type de source et du type de cible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1397">The signature of a conversion operator consists of the source type and the target type.</span></span> <span data-ttu-id="0804f-1398">(Notez qu’il s’agit de la seule forme de membre pour laquelle le type de retour participe à la signature.) La `implicit` classification `explicit` ou d’un opérateur de conversion ne fait pas partie de la signature de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1398">(Note that this is the only form of member for which the return type participates in the signature.) The `implicit` or `explicit` classification of a conversion operator is not part of the operator's signature.</span></span> <span data-ttu-id="0804f-1399">Ainsi, une classe ou un struct ne peut pas `implicit` déclarer à `explicit` la fois un opérateur de conversion et un opérateur de conversion avec les mêmes types source et cible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1399">Thus, a class or struct cannot declare both an `implicit` and an `explicit` conversion operator with the same source and target types.</span></span>

<span data-ttu-id="0804f-1400">En général, les conversions implicites définies par l’utilisateur doivent être conçues pour ne jamais lever d’exceptions et ne pas perdre d’informations.</span><span class="sxs-lookup"><span data-stu-id="0804f-1400">In general, user-defined implicit conversions should be designed to never throw exceptions and never lose information.</span></span> <span data-ttu-id="0804f-1401">Si une conversion définie par l’utilisateur peut donner lieu à des exceptions (par exemple, parce que l’argument source est hors limites) ou une perte d’informations (par exemple, en ignorant les bits de poids fort), cette conversion doit être définie comme une conversion explicite.</span><span class="sxs-lookup"><span data-stu-id="0804f-1401">If a user-defined conversion can give rise to exceptions (for example, because the source argument is out of range) or loss of information (such as discarding high-order bits), then that conversion should be defined as an explicit conversion.</span></span>

<span data-ttu-id="0804f-1402">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1402">In the example</span></span>
```csharp
using System;

public struct Digit
{
    byte value;

    public Digit(byte value) {
        if (value < 0 || value > 9) throw new ArgumentException();
        this.value = value;
    }

    public static implicit operator byte(Digit d) {
        return d.value;
    }

    public static explicit operator Digit(byte b) {
        return new Digit(b);
    }
}
```
<span data-ttu-id="0804f-1403">la conversion de `Digit` en `byte` est implicite, car elle ne lève jamais d’exception ou ne perd pas d' `byte` informations, mais la `Digit` conversion de en `Digit` est explicite, car peut uniquement représenter un sous-ensemble du possible valeurs d’un `byte`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1403">the conversion from `Digit` to `byte` is implicit because it never throws exceptions or loses information, but the conversion from `byte` to `Digit` is explicit since `Digit` can only represent a subset of the possible values of a `byte`.</span></span>

## <a name="instance-constructors"></a><span data-ttu-id="0804f-1404">Constructeurs d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-1404">Instance constructors</span></span>

<span data-ttu-id="0804f-1405">Un ***constructeur d’instance*** est un membre qui implémente les actions requises pour initialiser une instance d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1405">An ***instance constructor*** is a member that implements the actions required to initialize an instance of a class.</span></span> <span data-ttu-id="0804f-1406">Les constructeurs d’instance sont déclarés à l’aide de *constructor_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1406">Instance constructors are declared using *constructor_declaration*s:</span></span>

```antlr
constructor_declaration
    : attributes? constructor_modifier* constructor_declarator constructor_body
    ;

constructor_modifier
    : 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'extern'
    | constructor_modifier_unsafe
    ;

constructor_declarator
    : identifier '(' formal_parameter_list? ')' constructor_initializer?
    ;

constructor_initializer
    : ':' 'base' '(' argument_list? ')'
    | ':' 'this' '(' argument_list? ')'
    ;

constructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="0804f-1407">Un *constructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)), une combinaison valide des quatre modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) et un modificateur de `extern` ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1407">A *constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and an `extern` ([External methods](classes.md#external-methods)) modifier.</span></span> <span data-ttu-id="0804f-1408">Une déclaration de constructeur n’est pas autorisée à inclure le même modificateur plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="0804f-1408">A constructor declaration is not permitted to include the same modifier multiple times.</span></span>

<span data-ttu-id="0804f-1409">L' *identificateur* d’un *constructor_declarator* doit nommer la classe dans laquelle le constructeur d’instance est déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-1409">The *identifier* of a *constructor_declarator* must name the class in which the instance constructor is declared.</span></span> <span data-ttu-id="0804f-1410">Si un autre nom est spécifié, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1410">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="0804f-1411">Le *formal_parameter_list* facultatif d’un constructeur d’instance est soumis aux mêmes règles que les *formal_parameter_list* d’une méthode ([méthodes](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1411">The optional *formal_parameter_list* of an instance constructor is subject to the same rules as the *formal_parameter_list* of a method ([Methods](classes.md#methods)).</span></span> <span data-ttu-id="0804f-1412">La liste de paramètres formels définit la signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)) d’un constructeur d’instance et régit le processus par lequel la résolution de surcharge ([inférence de type](expressions.md#type-inference)) sélectionne un constructeur d’instance particulier dans un appel.</span><span class="sxs-lookup"><span data-stu-id="0804f-1412">The formal parameter list defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of an instance constructor and governs the process whereby overload resolution ([Type inference](expressions.md#type-inference)) selects a particular instance constructor in an invocation.</span></span>

<span data-ttu-id="0804f-1413">Chacun des types référencés dans le *formal_parameter_list* d’un constructeur d’instance doit être au moins aussi accessible que le constructeur lui-même ([contraintes d’accessibilité](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1413">Each of the types referenced in the *formal_parameter_list* of an instance constructor must be at least as accessible as the constructor itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="0804f-1414">Le *constructor_initializer* facultatif spécifie un autre constructeur d’instance à appeler avant d’exécuter les instructions fournies dans le *constructor_body* de ce constructeur d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1414">The optional *constructor_initializer* specifies another instance constructor to invoke before executing the statements given in the *constructor_body* of this instance constructor.</span></span> <span data-ttu-id="0804f-1415">Cela est décrit plus en détail dans les [initialiseurs de constructeur](classes.md#constructor-initializers).</span><span class="sxs-lookup"><span data-stu-id="0804f-1415">This is described further in [Constructor initializers](classes.md#constructor-initializers).</span></span>

<span data-ttu-id="0804f-1416">Lorsqu’une déclaration de constructeur comprend `extern` un modificateur, le constructeur est considéré comme un ***constructeur externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1416">When a constructor declaration includes an `extern` modifier, the constructor is said to be an ***external constructor***.</span></span> <span data-ttu-id="0804f-1417">Étant donné qu’une déclaration de constructeur externe ne fournit aucune implémentation réelle, son *constructor_body* se compose d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1417">Because an external constructor declaration provides no actual implementation, its *constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="0804f-1418">Pour tous les autres constructeurs, le *constructor_body* se compose d’un *bloc* qui spécifie les instructions pour initialiser une nouvelle instance de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1418">For all other constructors, the *constructor_body* consists of a *block* which specifies the statements to initialize a new instance of the class.</span></span> <span data-ttu-id="0804f-1419">Cela correspond exactement au *bloc* d’une méthode d’instance avec un `void` type de retour ([corps de méthode](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1419">This corresponds exactly to the *block* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="0804f-1420">Les constructeurs d’instance ne sont pas hérités.</span><span class="sxs-lookup"><span data-stu-id="0804f-1420">Instance constructors are not inherited.</span></span> <span data-ttu-id="0804f-1421">Par conséquent, une classe n’a pas de constructeurs d’instance autres que ceux réellement déclarés dans la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1421">Thus, a class has no instance constructors other than those actually declared in the class.</span></span> <span data-ttu-id="0804f-1422">Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est fourni automatiquement ([constructeurs par défaut](classes.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1422">If a class contains no instance constructor declarations, a default instance constructor is automatically provided ([Default constructors](classes.md#default-constructors)).</span></span>

<span data-ttu-id="0804f-1423">Les constructeurs d’instance sont appelés par *object_creation_expression*s ([expressions de création d’objet](expressions.md#object-creation-expressions)) et par le biais de *constructor_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="0804f-1423">Instance constructors are invoked by *object_creation_expression*s ([Object creation expressions](expressions.md#object-creation-expressions)) and through *constructor_initializer*s.</span></span>

### <a name="constructor-initializers"></a><span data-ttu-id="0804f-1424">Initialiseurs de constructeur</span><span class="sxs-lookup"><span data-stu-id="0804f-1424">Constructor initializers</span></span>

<span data-ttu-id="0804f-1425">Tous les constructeurs d’instance (sauf ceux de la classe `object`) incluent implicitement un appel d’un autre constructeur d’instance immédiatement avant le *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1425">All instance constructors (except those for class `object`) implicitly include an invocation of another instance constructor immediately before the *constructor_body*.</span></span> <span data-ttu-id="0804f-1426">Le constructeur pour appeler implicitement est déterminé par le *constructor_initializer*:</span><span class="sxs-lookup"><span data-stu-id="0804f-1426">The constructor to implicitly invoke is determined by the *constructor_initializer*:</span></span>

*  <span data-ttu-id="0804f-1427">Un initialiseur de constructeur d’instance du `base(argument_list)` formulaire `base()` ou provoque l’appel d’un constructeur d’instance de la classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1427">An instance constructor initializer of the form `base(argument_list)` or `base()` causes an instance constructor from the direct base class to be invoked.</span></span> <span data-ttu-id="0804f-1428">Ce constructeur est sélectionné à l’aide de *argument_list* s’il est présent et les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="0804f-1428">That constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="0804f-1429">L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles contenus dans la classe de base directe, ou du constructeur par défaut ([constructeurs par défaut](classes.md#default-constructors)), si aucun constructeur d’instance n’est déclaré dans la classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1429">The set of candidate instance constructors consists of all accessible instance constructors contained in the direct base class, or the default constructor ([Default constructors](classes.md#default-constructors)), if no instance constructors are declared in the direct base class.</span></span> <span data-ttu-id="0804f-1430">Si cet ensemble est vide ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1430">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span>
*  <span data-ttu-id="0804f-1431">Un initialiseur de constructeur d’instance du `this(argument-list)` formulaire `this()` ou provoque l’appel d’un constructeur d’instance de la classe elle-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-1431">An instance constructor initializer of the form `this(argument-list)` or `this()` causes an instance constructor from the class itself to be invoked.</span></span> <span data-ttu-id="0804f-1432">Le constructeur est sélectionné à l’aide de *argument_list* s’il est présent et les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="0804f-1432">The constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="0804f-1433">L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles déclarés dans la classe elle-même.</span><span class="sxs-lookup"><span data-stu-id="0804f-1433">The set of candidate instance constructors consists of all accessible instance constructors declared in the class itself.</span></span> <span data-ttu-id="0804f-1434">Si cet ensemble est vide ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1434">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span> <span data-ttu-id="0804f-1435">Si une déclaration de constructeur d’instance comprend un initialiseur de constructeur qui appelle le constructeur lui-même, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1435">If an instance constructor declaration includes a constructor initializer that invokes the constructor itself, a compile-time error occurs.</span></span>

<span data-ttu-id="0804f-1436">Si un constructeur d’instance n’a pas d’initialiseur de constructeur, un initialiseur `base()` de constructeur du formulaire est fourni implicitement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1436">If an instance constructor has no constructor initializer, a constructor initializer of the form `base()` is implicitly provided.</span></span> <span data-ttu-id="0804f-1437">Ainsi, une déclaration de constructeur d’instance de la forme</span><span class="sxs-lookup"><span data-stu-id="0804f-1437">Thus, an instance constructor declaration of the form</span></span>
```csharp
C(...) {...}
```
<span data-ttu-id="0804f-1438">est exactement équivalent à</span><span class="sxs-lookup"><span data-stu-id="0804f-1438">is exactly equivalent to</span></span>
```csharp
C(...): base() {...}
```

<span data-ttu-id="0804f-1439">La portée des paramètres fournis par le *formal_parameter_list* d’une déclaration de constructeur d’instance comprend l’initialiseur de constructeur de cette déclaration.</span><span class="sxs-lookup"><span data-stu-id="0804f-1439">The scope of the parameters given by the *formal_parameter_list* of an instance constructor declaration includes the constructor initializer of that declaration.</span></span> <span data-ttu-id="0804f-1440">Ainsi, un initialiseur de constructeur est autorisé à accéder aux paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1440">Thus, a constructor initializer is permitted to access the parameters of the constructor.</span></span> <span data-ttu-id="0804f-1441">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-1441">For example:</span></span>
```csharp
class A
{
    public A(int x, int y) {}
}

class B: A
{
    public B(int x, int y): base(x + y, x - y) {}
}
```

<span data-ttu-id="0804f-1442">Un initialiseur de constructeur d’instance ne peut pas accéder à l’instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="0804f-1442">An instance constructor initializer cannot access the instance being created.</span></span> <span data-ttu-id="0804f-1443">Par conséquent, il s’agit d’une erreur au moment de la compilation pour référencer `this` dans une expression d’argument de l’initialiseur de constructeur, comme c’est le cas pour une expression d’argument qui fait référence à un membre d’instance par l’intermédiaire d’un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1443">Therefore it is a compile-time error to reference `this` in an argument expression of the constructor initializer, as is it a compile-time error for an argument expression to reference any instance member through a *simple_name*.</span></span>

### <a name="instance-variable-initializers"></a><span data-ttu-id="0804f-1444">Initialiseurs de variable d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-1444">Instance variable initializers</span></span>

<span data-ttu-id="0804f-1445">Lorsqu’un constructeur d’instance n’a pas d’initialiseur de constructeur, ou qu’il a un initialiseur de constructeur de la forme `base(...)`, ce constructeur effectue implicitement les initialisations spécifiées par les *variable_initializer*des champs d’instance déclarés dans sa classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1445">When an instance constructor has no constructor initializer, or it has a constructor initializer of the form `base(...)`, that constructor implicitly performs the initializations specified by the *variable_initializer*s of the instance fields declared in its class.</span></span> <span data-ttu-id="0804f-1446">Cela correspond à une séquence d’assignations qui sont exécutées immédiatement après l’entrée dans le constructeur et avant l’appel implicite du constructeur de classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1446">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor and before the implicit invocation of the direct base class constructor.</span></span> <span data-ttu-id="0804f-1447">Les initialiseurs de variable sont exécutés dans l’ordre textuel dans lequel ils apparaissent dans la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1447">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span>

### <a name="constructor-execution"></a><span data-ttu-id="0804f-1448">Exécution du constructeur</span><span class="sxs-lookup"><span data-stu-id="0804f-1448">Constructor execution</span></span>

<span data-ttu-id="0804f-1449">Les initialiseurs de variable sont transformés en instructions d’assignation, et ces instructions d’assignation sont exécutées avant l’appel du constructeur d’instance de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-1449">Variable initializers are transformed into assignment statements, and these assignment statements are executed before the invocation of the base class instance constructor.</span></span> <span data-ttu-id="0804f-1450">Ce classement garantit que tous les champs d’instance sont initialisés par leurs initialiseurs de variable avant l’exécution des instructions qui ont accès à cette instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1450">This ordering ensures that all instance fields are initialized by their variable initializers before any statements that have access to that instance are executed.</span></span>

<span data-ttu-id="0804f-1451">À partir de l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1451">Given the example</span></span>
```csharp
using System;

class A
{
    public A() {
        PrintFields();
    }

    public virtual void PrintFields() {}
}

class B: A
{
    int x = 1;
    int y;

    public B() {
        y = -1;
    }

    public override void PrintFields() {
        Console.WriteLine("x = {0}, y = {1}", x, y);
    }
}
```
<span data-ttu-id="0804f-1452">Lorsque `new B()` est utilisé pour créer une instance de `B`, la sortie suivante est produite :</span><span class="sxs-lookup"><span data-stu-id="0804f-1452">when `new B()` is used to create an instance of `B`, the following output is produced:</span></span>
```console
x = 1, y = 0
```

<span data-ttu-id="0804f-1453">La valeur de `x` est 1, car l’initialiseur de variable est exécuté avant l’appel du constructeur d’instance de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0804f-1453">The value of `x` is 1 because the variable initializer is executed before the base class instance constructor is invoked.</span></span> <span data-ttu-id="0804f-1454">Toutefois, la valeur de `y` est 0 (la valeur par défaut d' `int`un), car l' `y` assignation à n’est pas exécutée tant que le constructeur de classe de base n’a pas été retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1454">However, the value of `y` is 0 (the default value of an `int`) because the assignment to `y` is not executed until after the base class constructor returns.</span></span>

<span data-ttu-id="0804f-1455">Il est utile de considérer les initialiseurs de variable d’instance et les initialiseurs de constructeur comme des instructions qui sont insérées automatiquement avant *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="0804f-1455">It is useful to think of instance variable initializers and constructor initializers as statements that are automatically inserted before the *constructor_body*.</span></span> <span data-ttu-id="0804f-1456">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1456">The example</span></span>
```csharp
using System;
using System.Collections;

class A
{
    int x = 1, y = -1, count;

    public A() {
        count = 0;
    }

    public A(int n) {
        count = n;
    }
}

class B: A
{
    double sqrt2 = Math.Sqrt(2.0);
    ArrayList items = new ArrayList(100);
    int max;

    public B(): this(100) {
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        max = n;
    }
}
```
<span data-ttu-id="0804f-1457">contient plusieurs initialiseurs de variable ; Il contient également des initialiseurs de constructeur des deux`base` formes `this`(et).</span><span class="sxs-lookup"><span data-stu-id="0804f-1457">contains several variable initializers; it also contains constructor initializers of both forms (`base` and `this`).</span></span> <span data-ttu-id="0804f-1458">L’exemple correspond au code indiqué ci-dessous, où chaque commentaire indique une instruction insérée automatiquement (la syntaxe utilisée pour les appels de constructeur insérés automatiquement n’est pas valide, mais sert simplement à illustrer le mécanisme).</span><span class="sxs-lookup"><span data-stu-id="0804f-1458">The example corresponds to the code shown below, where each comment indicates an automatically inserted statement (the syntax used for the automatically inserted constructor invocations isn't valid, but merely serves to illustrate the mechanism).</span></span>

```csharp
using System.Collections;

class A
{
    int x, y, count;

    public A() {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = 0;
    }

    public A(int n) {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = n;
    }
}

class B: A
{
    double sqrt2;
    ArrayList items;
    int max;

    public B(): this(100) {
        B(100);                      // Invoke B(int) constructor
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        sqrt2 = Math.Sqrt(2.0);      // Variable initializer
        items = new ArrayList(100);  // Variable initializer
        A(n - 1);                    // Invoke A(int) constructor
        max = n;
    }
}
```

### <a name="default-constructors"></a><span data-ttu-id="0804f-1459">Constructeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="0804f-1459">Default constructors</span></span>

<span data-ttu-id="0804f-1460">Si une classe ne contient aucune déclaration de constructeur d’instance, un constructeur d’instance par défaut est fourni automatiquement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1460">If a class contains no instance constructor declarations, a default instance constructor is automatically provided.</span></span> <span data-ttu-id="0804f-1461">Ce constructeur par défaut appelle simplement le constructeur sans paramètre de la classe de base directe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1461">That default constructor simply invokes the parameterless constructor of the direct base class.</span></span> <span data-ttu-id="0804f-1462">Si la classe est abstraite, l’accessibilité déclarée pour le constructeur par défaut est protégée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1462">If the class is abstract then the declared accessibility for the default constructor is protected.</span></span> <span data-ttu-id="0804f-1463">Dans le cas contraire, l’accessibilité déclarée pour le constructeur par défaut est public.</span><span class="sxs-lookup"><span data-stu-id="0804f-1463">Otherwise, the declared accessibility for the default constructor is public.</span></span> <span data-ttu-id="0804f-1464">Par conséquent, le constructeur par défaut est toujours de la forme</span><span class="sxs-lookup"><span data-stu-id="0804f-1464">Thus, the default constructor is always of the form</span></span>

```csharp
protected C(): base() {}
```
<span data-ttu-id="0804f-1465">ou</span><span class="sxs-lookup"><span data-stu-id="0804f-1465">or</span></span>
```csharp
public C(): base() {}
```
<span data-ttu-id="0804f-1466">où `C` est le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1466">where `C` is the name of the class.</span></span> <span data-ttu-id="0804f-1467">Si la résolution de surcharge ne peut pas déterminer un meilleur candidat unique pour l’initialiseur de constructeur de classe de base, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1467">If overload resolution is unable to determine a unique best candidate for the base class constructor initializer then a compile-time error occurs.</span></span>

<span data-ttu-id="0804f-1468">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1468">In the example</span></span>
```csharp
class Message
{
    object sender;
    string text;
}
```
<span data-ttu-id="0804f-1469">un constructeur par défaut est fourni, car la classe ne contient aucune déclaration de constructeur d’instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1469">a default constructor is provided because the class contains no instance constructor declarations.</span></span> <span data-ttu-id="0804f-1470">Ainsi, l’exemple est exactement équivalent à</span><span class="sxs-lookup"><span data-stu-id="0804f-1470">Thus, the example is precisely equivalent to</span></span>
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a><span data-ttu-id="0804f-1471">Constructeurs privés</span><span class="sxs-lookup"><span data-stu-id="0804f-1471">Private constructors</span></span>

<span data-ttu-id="0804f-1472">Quand une classe `T` déclare uniquement des constructeurs d’instance privés, il n’est pas possible que les classes situées en dehors `T` du texte de `T` programme de soient dérivées de `T`ou de pour créer directement des instances de.</span><span class="sxs-lookup"><span data-stu-id="0804f-1472">When a class `T` declares only private instance constructors, it is not possible for classes outside the program text of `T` to derive from `T` or to directly create instances of `T`.</span></span> <span data-ttu-id="0804f-1473">Ainsi, si une classe contient uniquement des membres statiques et n’est pas destinée à être instanciée, l’ajout d’un constructeur d’instance privée vide empêchera l’instanciation.</span><span class="sxs-lookup"><span data-stu-id="0804f-1473">Thus, if a class contains only static members and isn't intended to be instantiated, adding an empty private instance constructor will prevent instantiation.</span></span> <span data-ttu-id="0804f-1474">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0804f-1474">For example:</span></span>
```csharp
public class Trig
{
    private Trig() {}        // Prevent instantiation

    public const double PI = 3.14159265358979323846;

    public static double Sin(double x) {...}
    public static double Cos(double x) {...}
    public static double Tan(double x) {...}
}
```

<span data-ttu-id="0804f-1475">La `Trig` classe regroupe des constantes et des méthodes connexes, mais n’est pas destinée à être instanciée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1475">The `Trig` class groups related methods and constants, but is not intended to be instantiated.</span></span> <span data-ttu-id="0804f-1476">Par conséquent, il déclare un seul constructeur d’instance privée vide.</span><span class="sxs-lookup"><span data-stu-id="0804f-1476">Therefore it declares a single empty private instance constructor.</span></span> <span data-ttu-id="0804f-1477">Au moins un constructeur d’instance doit être déclaré pour supprimer la génération automatique d’un constructeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-1477">At least one instance constructor must be declared to suppress the automatic generation of a default constructor.</span></span>

### <a name="optional-instance-constructor-parameters"></a><span data-ttu-id="0804f-1478">Paramètres facultatifs du constructeur d’instance</span><span class="sxs-lookup"><span data-stu-id="0804f-1478">Optional instance constructor parameters</span></span>

<span data-ttu-id="0804f-1479">La `this(...)` forme de l’initialiseur de constructeur est couramment utilisée conjointement avec la surcharge pour implémenter des paramètres de constructeur d’instance facultatifs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1479">The `this(...)` form of constructor initializer is commonly used in conjunction with overloading to implement optional instance constructor parameters.</span></span> <span data-ttu-id="0804f-1480">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1480">In the example</span></span>
```csharp
class Text
{
    public Text(): this(0, 0, null) {}

    public Text(int x, int y): this(x, y, null) {}

    public Text(int x, int y, string s) {
        // Actual constructor implementation
    }
}
```
<span data-ttu-id="0804f-1481">les deux premiers constructeurs d’instance fournissent simplement les valeurs par défaut pour les arguments manquants.</span><span class="sxs-lookup"><span data-stu-id="0804f-1481">the first two instance constructors merely provide the default values for the missing arguments.</span></span> <span data-ttu-id="0804f-1482">Les deux utilisent `this(...)` un initialiseur de constructeur pour appeler le troisième constructeur d’instance, qui effectue en fait le travail d’initialisation de la nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1482">Both use a `this(...)` constructor initializer to invoke the third instance constructor, which actually does the work of initializing the new instance.</span></span> <span data-ttu-id="0804f-1483">L’effet est celui des paramètres de constructeur facultatifs :</span><span class="sxs-lookup"><span data-stu-id="0804f-1483">The effect is that of optional constructor parameters:</span></span>
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a><span data-ttu-id="0804f-1484">Constructeurs statiques</span><span class="sxs-lookup"><span data-stu-id="0804f-1484">Static constructors</span></span>

<span data-ttu-id="0804f-1485">Un ***constructeur statique*** est un membre qui implémente les actions requises pour initialiser un type de classe fermé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1485">A ***static constructor*** is a member that implements the actions required to initialize a closed class type.</span></span> <span data-ttu-id="0804f-1486">Les constructeurs statiques sont déclarés à l’aide de *static_constructor_declaration*s :</span><span class="sxs-lookup"><span data-stu-id="0804f-1486">Static constructors are declared using *static_constructor_declaration*s:</span></span>

```antlr
static_constructor_declaration
    : attributes? static_constructor_modifiers identifier '(' ')' static_constructor_body
    ;

static_constructor_modifiers
    : 'extern'? 'static'
    | 'static' 'extern'?
    | static_constructor_modifiers_unsafe
    ;

static_constructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="0804f-1487">Un *static_constructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)) et un modificateur `extern` ([méthodes externes](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1487">A *static_constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and an `extern` modifier ([External methods](classes.md#external-methods)).</span></span>

<span data-ttu-id="0804f-1488">L' *identificateur* d’un *static_constructor_declaration* doit nommer la classe dans laquelle le constructeur statique est déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-1488">The *identifier* of a *static_constructor_declaration* must name the class in which the static constructor is declared.</span></span> <span data-ttu-id="0804f-1489">Si un autre nom est spécifié, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1489">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="0804f-1490">Lorsqu’une déclaration de constructeur statique comprend `extern` un modificateur, le constructeur statique est dit comme un ***constructeur statique externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1490">When a static constructor declaration includes an `extern` modifier, the static constructor is said to be an ***external static constructor***.</span></span> <span data-ttu-id="0804f-1491">Étant donné qu’une déclaration de constructeur statique externe ne fournit aucune implémentation réelle, son *static_constructor_body* se compose d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1491">Because an external static constructor declaration provides no actual implementation, its *static_constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="0804f-1492">Pour toutes les autres déclarations de constructeur statiques, le *static_constructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter pour initialiser la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1492">For all other static constructor declarations, the *static_constructor_body* consists of a *block* which specifies the statements to execute in order to initialize the class.</span></span> <span data-ttu-id="0804f-1493">Cela correspond exactement au *method_body* d’une méthode statique avec un type de retour `void` ([corps de méthode](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1493">This corresponds exactly to the *method_body* of a static method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="0804f-1494">Les constructeurs statiques ne sont pas hérités et ne peuvent pas être appelés directement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1494">Static constructors are not inherited, and cannot be called directly.</span></span>

<span data-ttu-id="0804f-1495">Le constructeur statique d’un type de classe fermé s’exécute au plus une fois dans un domaine d’application donné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1495">The static constructor for a closed class type executes at most once in a given application domain.</span></span> <span data-ttu-id="0804f-1496">L’exécution d’un constructeur statique est déclenchée par le premier des événements suivants pour se produire au sein d’un domaine d’application :</span><span class="sxs-lookup"><span data-stu-id="0804f-1496">The execution of a static constructor is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="0804f-1497">Une instance du type de classe est créée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1497">An instance of the class type is created.</span></span>
*  <span data-ttu-id="0804f-1498">Tous les membres statiques du type de classe sont référencés.</span><span class="sxs-lookup"><span data-stu-id="0804f-1498">Any of the static members of the class type are referenced.</span></span>

<span data-ttu-id="0804f-1499">Si une classe contient la `Main` méthode ([démarrage](basic-concepts.md#application-startup)de l’application) dans laquelle l’exécution commence, le constructeur statique de cette classe s' `Main` exécute avant l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-1499">If a class contains the `Main` method ([Application Startup](basic-concepts.md#application-startup)) in which execution begins, the static constructor for that class executes before the `Main` method is called.</span></span>

<span data-ttu-id="0804f-1500">Pour initialiser un nouveau type de classe fermé, tout d’abord un nouvel ensemble de champs statiques ([champs d’instance et statiques](classes.md#static-and-instance-fields)) pour ce type fermé particulier est créé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1500">To initialize a new closed class type, first a new set of static fields ([Static and instance fields](classes.md#static-and-instance-fields)) for that particular closed type is created.</span></span> <span data-ttu-id="0804f-1501">Chacun des champs statiques est initialisé à sa valeur par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1501">Each of the static fields is initialized to its default value ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="0804f-1502">Ensuite, les initialiseurs de champ statique ([initialisation de champ statique](classes.md#static-field-initialization)) sont exécutés pour ces champs statiques.</span><span class="sxs-lookup"><span data-stu-id="0804f-1502">Next, the static field initializers ([Static field initialization](classes.md#static-field-initialization)) are executed for those static fields.</span></span> <span data-ttu-id="0804f-1503">Enfin, le constructeur statique est exécuté.</span><span class="sxs-lookup"><span data-stu-id="0804f-1503">Finally, the static constructor is executed.</span></span>

<span data-ttu-id="0804f-1504">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1504">The example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        A.F();
        B.F();
    }
}

class A
{
    static A() {
        Console.WriteLine("Init A");
    }
    public static void F() {
        Console.WriteLine("A.F");
    }
}

class B
{
    static B() {
        Console.WriteLine("Init B");
    }
    public static void F() {
        Console.WriteLine("B.F");
    }
}
```
<span data-ttu-id="0804f-1505">doit produire la sortie :</span><span class="sxs-lookup"><span data-stu-id="0804f-1505">must produce the output:</span></span>
```console
Init A
A.F
Init B
B.F
```
<span data-ttu-id="0804f-1506">étant donné que l' `A`exécution du constructeur statique de est déclenchée par l' `A.F`appel à, et que `B`l’exécution du constructeur statique de est déclenchée par `B.F`l’appel à.</span><span class="sxs-lookup"><span data-stu-id="0804f-1506">because the execution of `A`'s static constructor is triggered by the call to `A.F`, and the execution of `B`'s static constructor is triggered by the call to `B.F`.</span></span>

<span data-ttu-id="0804f-1507">Il est possible de construire des dépendances circulaires qui autorisent le respect des champs statiques avec des initialiseurs de variable dans leur état de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0804f-1507">It is possible to construct circular dependencies that allow static fields with variable initializers to be observed in their default value state.</span></span>

<span data-ttu-id="0804f-1508">L’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1508">The example</span></span>
```csharp
using System;

class A
{
    public static int X;

    static A() {
        X = B.Y + 1;
    }
}

class B
{
    public static int Y = A.X + 1;

    static B() {}

    static void Main() {
        Console.WriteLine("X = {0}, Y = {1}", A.X, B.Y);
    }
}
```
<span data-ttu-id="0804f-1509">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="0804f-1509">produces the output</span></span>
```console
X = 1, Y = 2
```

<span data-ttu-id="0804f-1510">Pour exécuter la `Main` méthode, le système exécute d’abord l’initialiseur `B.Y`pour, avant le `B`constructeur statique de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1510">To execute the `Main` method, the system first runs the initializer for `B.Y`, prior to class `B`'s static constructor.</span></span> <span data-ttu-id="0804f-1511">`Y`l’initialiseur de `A`entraîne l’exécution du constructeur statique de, car la `A.X` valeur de est référencée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1511">`Y`'s initializer causes `A`'s static constructor to be run because the value of `A.X` is referenced.</span></span> <span data-ttu-id="0804f-1512">Le constructeur statique de `A` à son tour poursuit le calcul de la valeur `X`de, ce qui permet d’extraire la valeur par défaut `Y`de, qui est égale à zéro.</span><span class="sxs-lookup"><span data-stu-id="0804f-1512">The static constructor of `A` in turn proceeds to compute the value of `X`, and in doing so fetches the default value of `Y`, which is zero.</span></span> <span data-ttu-id="0804f-1513">`A.X`est donc initialisé à 1.</span><span class="sxs-lookup"><span data-stu-id="0804f-1513">`A.X` is thus initialized to 1.</span></span> <span data-ttu-id="0804f-1514">Le processus d’exécution `A`des initialiseurs de champ statiques et du constructeur statique se termine alors, en retournant au calcul `Y`de la valeur initiale de, dont le résultat devient 2.</span><span class="sxs-lookup"><span data-stu-id="0804f-1514">The process of running `A`'s static field initializers and static constructor then completes, returning to the calculation of the initial value of `Y`, the result of which becomes 2.</span></span>

<span data-ttu-id="0804f-1515">Étant donné que le constructeur statique est exécuté une seule fois pour chaque type de classe construite fermée, il s’agit d’un emplacement pratique pour appliquer des contrôles au moment de l’exécution sur le paramètre de type qui ne peuvent pas être vérifiés au moment de la compilation via des contraintes ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) .</span><span class="sxs-lookup"><span data-stu-id="0804f-1515">Because the static constructor is executed exactly once for each closed constructed class type, it is a convenient place to enforce run-time checks on the type parameter that cannot be checked at compile-time via constraints ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="0804f-1516">Par exemple, le type suivant utilise un constructeur statique pour appliquer que l’argument de type est une énumération :</span><span class="sxs-lookup"><span data-stu-id="0804f-1516">For example, the following type uses a static constructor to enforce that the type argument is an enum:</span></span>
```csharp
class Gen<T> where T: struct
{
    static Gen() {
        if (!typeof(T).IsEnum) {
            throw new ArgumentException("T must be an enum");
        }
    }
}
```

## <a name="destructors"></a><span data-ttu-id="0804f-1517">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="0804f-1517">Destructors</span></span>

<span data-ttu-id="0804f-1518">Un ***destructeur*** est un membre qui implémente les actions requises pour détruire une instance d’une classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1518">A ***destructor*** is a member that implements the actions required to destruct an instance of a class.</span></span> <span data-ttu-id="0804f-1519">Un destructeur est déclaré à l’aide d’un *destructor_declaration*:</span><span class="sxs-lookup"><span data-stu-id="0804f-1519">A destructor is declared using a *destructor_declaration*:</span></span>

```antlr
destructor_declaration
    : attributes? 'extern'? '~' identifier '(' ')' destructor_body
    | destructor_declaration_unsafe
    ;

destructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="0804f-1520">Un *destructor_declaration* peut inclure un ensemble d' *attributs* ([attributs](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1520">A *destructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="0804f-1521">L' *identificateur* d’un *destructor_declaration* doit nommer la classe dans laquelle le destructeur est déclaré.</span><span class="sxs-lookup"><span data-stu-id="0804f-1521">The *identifier* of a *destructor_declaration* must name the class in which the destructor is declared.</span></span> <span data-ttu-id="0804f-1522">Si un autre nom est spécifié, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="0804f-1522">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="0804f-1523">Lorsqu’une déclaration de destructeur comprend un `extern` modificateur, le destructeur est considéré comme un ***destructeur externe***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1523">When a destructor declaration includes an `extern` modifier, the destructor is said to be an ***external destructor***.</span></span> <span data-ttu-id="0804f-1524">Étant donné qu’une déclaration de destructeur externe ne fournit aucune implémentation réelle, son *destructor_body* se compose d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="0804f-1524">Because an external destructor declaration provides no actual implementation, its *destructor_body* consists of a semicolon.</span></span> <span data-ttu-id="0804f-1525">Pour tous les autres destructeurs, le *destructor_body* se compose d’un *bloc* qui spécifie les instructions à exécuter afin de détruire une instance de la classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1525">For all other destructors, the *destructor_body* consists of a *block* which specifies the statements to execute in order to destruct an instance of the class.</span></span> <span data-ttu-id="0804f-1526">Un *destructor_body* correspond exactement au *method_body* d’une méthode d’instance avec un type de retour `void` ([corps de méthode](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1526">A *destructor_body* corresponds exactly to the *method_body* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="0804f-1527">Les destructeurs ne sont pas hérités.</span><span class="sxs-lookup"><span data-stu-id="0804f-1527">Destructors are not inherited.</span></span> <span data-ttu-id="0804f-1528">Par conséquent, une classe n’a pas de destructeurs autres que celui qui peut être déclaré dans cette classe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1528">Thus, a class has no destructors other than the one which may be declared in that class.</span></span>

<span data-ttu-id="0804f-1529">Étant donné qu’un destructeur ne doit pas avoir de paramètres, il ne peut pas être surchargé, donc une classe peut avoir, au plus, un destructeur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1529">Since a destructor is required to have no parameters, it cannot be overloaded, so a class can have, at most, one destructor.</span></span>

<span data-ttu-id="0804f-1530">Les destructeurs sont appelés automatiquement et ne peuvent pas être appelés explicitement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1530">Destructors are invoked automatically, and cannot be invoked explicitly.</span></span> <span data-ttu-id="0804f-1531">Une instance devient éligible pour la destruction lorsqu’il n’est plus possible pour un code d’utiliser cette instance.</span><span class="sxs-lookup"><span data-stu-id="0804f-1531">An instance becomes eligible for destruction when it is no longer possible for any code to use that instance.</span></span> <span data-ttu-id="0804f-1532">L’exécution du destructeur pour l’instance peut se produire à tout moment une fois que l’instance est éligible pour la destruction.</span><span class="sxs-lookup"><span data-stu-id="0804f-1532">Execution of the destructor for the instance may occur at any time after the instance becomes eligible for destruction.</span></span> <span data-ttu-id="0804f-1533">Lorsqu’une instance est détruite, les destructeurs dans la chaîne d’héritage de cette instance sont appelés, dans l’ordre, de la plus dérivée à la moins dérivée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1533">When an instance is destructed, the destructors in that instance's inheritance chain are called, in order, from most derived to least derived.</span></span> <span data-ttu-id="0804f-1534">Un destructeur peut être exécuté sur n’importe quel thread.</span><span class="sxs-lookup"><span data-stu-id="0804f-1534">A destructor may be executed on any thread.</span></span> <span data-ttu-id="0804f-1535">Pour plus d’informations sur les règles qui régissent le moment et le mode d’exécution d’un destructeur, consultez [gestion automatique](basic-concepts.md#automatic-memory-management)de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="0804f-1535">For further discussion of the rules that govern when and how a destructor is executed, see [Automatic memory management](basic-concepts.md#automatic-memory-management).</span></span>

<span data-ttu-id="0804f-1536">La sortie de l’exemple</span><span class="sxs-lookup"><span data-stu-id="0804f-1536">The output of the example</span></span>
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("A's destructor");
    }
}

class B: A
{
    ~B() {
        Console.WriteLine("B's destructor");
    }
}

class Test
{
   static void Main() {
        B b = new B();
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
   }
}
```
<span data-ttu-id="0804f-1537">est</span><span class="sxs-lookup"><span data-stu-id="0804f-1537">is</span></span>
```
B's destructor
A's destructor
```
<span data-ttu-id="0804f-1538">étant donné que les destructeurs dans une chaîne d’héritage sont appelés dans l’ordre, de la plus dérivée à la moins dérivée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1538">since destructors in an inheritance chain are called in order, from most derived to least derived.</span></span>

<span data-ttu-id="0804f-1539">Les destructeurs sont implémentés en substituant la `Finalize` méthode `System.Object`virtuelle sur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1539">Destructors are implemented by overriding the virtual method `Finalize` on `System.Object`.</span></span> <span data-ttu-id="0804f-1540">C#les programmes ne sont pas autorisés à substituer cette méthode ou à les appeler (ou à les remplacer) directement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1540">C# programs are not permitted to override this method or call it (or overrides of it) directly.</span></span> <span data-ttu-id="0804f-1541">Par exemple, le programme</span><span class="sxs-lookup"><span data-stu-id="0804f-1541">For instance, the program</span></span>
```csharp
class A 
{
    override protected void Finalize() {}    // error

    public void F() {
        this.Finalize();                     // error
    }
}
```
<span data-ttu-id="0804f-1542">contient deux erreurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1542">contains two errors.</span></span>

<span data-ttu-id="0804f-1543">Le compilateur se comporte comme si cette méthode, et ses substitutions, n’existent pas du tout.</span><span class="sxs-lookup"><span data-stu-id="0804f-1543">The compiler behaves as if this method, and overrides of it, do not exist at all.</span></span> <span data-ttu-id="0804f-1544">Ainsi, ce programme :</span><span class="sxs-lookup"><span data-stu-id="0804f-1544">Thus, this program:</span></span>
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
<span data-ttu-id="0804f-1545">est valide, et la méthode affichée masque `System.Object`la méthode de. `Finalize`</span><span class="sxs-lookup"><span data-stu-id="0804f-1545">is valid, and the method shown hides `System.Object`'s `Finalize` method.</span></span>

<span data-ttu-id="0804f-1546">Pour une description du comportement lorsqu’une exception est levée à partir d’un destructeur, consultez [la rubrique Comment les exceptions sont gérées](exceptions.md#how-exceptions-are-handled).</span><span class="sxs-lookup"><span data-stu-id="0804f-1546">For a discussion of the behavior when an exception is thrown from a destructor, see [How exceptions are handled](exceptions.md#how-exceptions-are-handled).</span></span>

## <a name="iterators"></a><span data-ttu-id="0804f-1547">Iterators</span><span class="sxs-lookup"><span data-stu-id="0804f-1547">Iterators</span></span>

<span data-ttu-id="0804f-1548">Une fonction membre ([fonction members](expressions.md#function-members)) implémentée à l’aide d’un bloc itérateur ([blocs](statements.md#blocks)) est appelée un ***itérateur***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1548">A function member ([Function members](expressions.md#function-members)) implemented using an iterator block ([Blocks](statements.md#blocks)) is called an ***iterator***.</span></span>

<span data-ttu-id="0804f-1549">Un bloc itérateur peut être utilisé comme corps d’un membre de fonction tant que le type de retour de la fonction membre correspondante est l’une des interfaces d’énumérateur ([interfaces d’énumérateur](classes.md#enumerator-interfaces)) ou l’une des interfaces énumérables ([interfaces énumérables](classes.md#enumerable-interfaces)) .</span><span class="sxs-lookup"><span data-stu-id="0804f-1549">An iterator block may be used as the body of a function member as long as the return type of the corresponding function member is one of the enumerator interfaces ([Enumerator interfaces](classes.md#enumerator-interfaces)) or one of the enumerable interfaces ([Enumerable interfaces](classes.md#enumerable-interfaces)).</span></span> <span data-ttu-id="0804f-1550">Elle peut se produire sous forme de *method_body*, *operator_body* ou *accessor_body*, alors que les événements, les constructeurs d’instance, les constructeurs statiques et les destructeurs ne peuvent pas être implémentés en tant qu’itérateurs.</span><span class="sxs-lookup"><span data-stu-id="0804f-1550">It can occur as a *method_body*, *operator_body* or *accessor_body*, whereas events, instance constructors, static constructors and destructors cannot be implemented as iterators.</span></span>

<span data-ttu-id="0804f-1551">Lorsqu’un membre de fonction est implémenté à l’aide d’un bloc itérateur, il s’agit d’une erreur de compilation pour la liste de paramètres formels `ref` de `out` la fonction membre qui spécifie les paramètres ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-1551">When a function member is implemented using an iterator block, it is a compile-time error for the formal parameter list of the function member to specify any `ref` or `out` parameters.</span></span>

### <a name="enumerator-interfaces"></a><span data-ttu-id="0804f-1552">Interfaces d’énumérateur</span><span class="sxs-lookup"><span data-stu-id="0804f-1552">Enumerator interfaces</span></span>

<span data-ttu-id="0804f-1553">Les ***interfaces d’énumérateur*** sont l’interface `System.Collections.IEnumerator` non générique et toutes les instanciations de l’interface `System.Collections.Generic.IEnumerator<T>`générique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1553">The ***enumerator interfaces*** are the non-generic interface `System.Collections.IEnumerator` and all instantiations of the generic interface `System.Collections.Generic.IEnumerator<T>`.</span></span> <span data-ttu-id="0804f-1554">Par souci de concision, dans ce chapitre, ces interfaces sont référencées en `IEnumerator` tant `IEnumerator<T>`que et, respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1554">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerator` and `IEnumerator<T>`, respectively.</span></span>

### <a name="enumerable-interfaces"></a><span data-ttu-id="0804f-1555">Interfaces énumérables</span><span class="sxs-lookup"><span data-stu-id="0804f-1555">Enumerable interfaces</span></span>

<span data-ttu-id="0804f-1556">Les ***interfaces énumérables*** sont l’interface `System.Collections.IEnumerable` non générique et toutes les instanciations de l' `System.Collections.Generic.IEnumerable<T>`interface générique.</span><span class="sxs-lookup"><span data-stu-id="0804f-1556">The ***enumerable interfaces*** are the non-generic interface `System.Collections.IEnumerable` and all instantiations of the generic interface `System.Collections.Generic.IEnumerable<T>`.</span></span> <span data-ttu-id="0804f-1557">Par souci de concision, dans ce chapitre, ces interfaces sont référencées en `IEnumerable` tant `IEnumerable<T>`que et, respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1557">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerable` and `IEnumerable<T>`, respectively.</span></span>

### <a name="yield-type"></a><span data-ttu-id="0804f-1558">Type yield</span><span class="sxs-lookup"><span data-stu-id="0804f-1558">Yield type</span></span>

<span data-ttu-id="0804f-1559">Un itérateur produit une séquence de valeurs, toutes du même type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1559">An iterator produces a sequence of values, all of the same type.</span></span> <span data-ttu-id="0804f-1560">Ce type est appelé le ***type yield*** de l’itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1560">This type is called the ***yield type*** of the iterator.</span></span>

*  <span data-ttu-id="0804f-1561">Type yield d’un itérateur qui retourne `IEnumerator` ou `IEnumerable` est `object`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1561">The yield type of an iterator that returns `IEnumerator` or `IEnumerable` is `object`.</span></span>
*  <span data-ttu-id="0804f-1562">Type yield d’un itérateur qui retourne `IEnumerator<T>` ou `IEnumerable<T>` est `T`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1562">The yield type of an iterator that returns `IEnumerator<T>` or `IEnumerable<T>` is `T`.</span></span>

### <a name="enumerator-objects"></a><span data-ttu-id="0804f-1563">Objets Enumerator</span><span class="sxs-lookup"><span data-stu-id="0804f-1563">Enumerator objects</span></span>

<span data-ttu-id="0804f-1564">Lorsqu’un membre de fonction qui retourne un type d’interface énumérateur est implémenté à l’aide d’un bloc itérateur, l’appel du membre de fonction n’exécute pas immédiatement le code dans le bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1564">When a function member returning an enumerator interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="0804f-1565">Au lieu de cela, un ***objet énumérateur*** est créé et retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1565">Instead, an ***enumerator object*** is created and returned.</span></span> <span data-ttu-id="0804f-1566">Cet objet encapsule le code spécifié dans le bloc Iterator, et l’exécution du code dans le bloc Iterator se produit lorsque la méthode de `MoveNext` l’objet énumérateur est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1566">This object encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="0804f-1567">Un objet énumérateur a les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-1567">An enumerator object has the following characteristics:</span></span>

*  <span data-ttu-id="0804f-1568">Il implémente `IEnumerator` et `IEnumerator<T>`, où `T` est le type de rendement de l’itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1568">It implements `IEnumerator` and `IEnumerator<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="0804f-1569">Elle implémente `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1569">It implements `System.IDisposable`.</span></span>
*  <span data-ttu-id="0804f-1570">Elle est initialisée à l’aide d’une copie des valeurs d’argument (le cas échéant) et de la valeur d’instance passée à la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1570">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>
*  <span data-ttu-id="0804f-1571">Il a quatre États potentiels, ***avant***, ***en cours d’exécution***, ***suspendu***et ***après***, et est initialement dans l’état ***avant*** .</span><span class="sxs-lookup"><span data-stu-id="0804f-1571">It has four potential states, ***before***, ***running***, ***suspended***, and ***after***, and is initially in the ***before*** state.</span></span>

<span data-ttu-id="0804f-1572">Un objet énumérateur est généralement une instance d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur et implémente les interfaces d’énumérateur, mais d’autres méthodes d’implémentation sont possibles.</span><span class="sxs-lookup"><span data-stu-id="0804f-1572">An enumerator object is typically an instance of a compiler-generated enumerator class that encapsulates the code in the iterator block and implements the enumerator interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="0804f-1573">Si une classe d’énumération est générée par le compilateur, cette classe sera imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, elle aura une accessibilité privée et un nom est réservé à l’utilisation du compilateur ([identificateurs](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1573">If an enumerator class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="0804f-1574">Un objet énumérateur peut implémenter plus d’interfaces que celles spécifiées ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0804f-1574">An enumerator object may implement more interfaces than those specified above.</span></span>

<span data-ttu-id="0804f-1575">Les sections suivantes décrivent le comportement exact des `MoveNext`membres `Current`, et `Dispose` des implémentations `IEnumerable` d' `IEnumerable<T>` interface et fournies par un objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1575">The following sections describe the exact behavior of the `MoveNext`, `Current`, and `Dispose` members of the `IEnumerable` and `IEnumerable<T>` interface implementations provided by an enumerator object.</span></span>

<span data-ttu-id="0804f-1576">Notez que les objets Enumerator ne prennent pas `IEnumerator.Reset` en charge la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-1576">Note that enumerator objects do not support the `IEnumerator.Reset` method.</span></span> <span data-ttu-id="0804f-1577">L’appel de cette méthode entraîne `System.NotSupportedException` la levée d’une exception.</span><span class="sxs-lookup"><span data-stu-id="0804f-1577">Invoking this method causes a `System.NotSupportedException` to be thrown.</span></span>

#### <a name="the-movenext-method"></a><span data-ttu-id="0804f-1578">Méthode MoveNext</span><span class="sxs-lookup"><span data-stu-id="0804f-1578">The MoveNext method</span></span>

<span data-ttu-id="0804f-1579">La `MoveNext` méthode d’un objet énumérateur encapsule le code d’un bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1579">The `MoveNext` method of an enumerator object encapsulates the code of an iterator block.</span></span> <span data-ttu-id="0804f-1580">L’appel de `MoveNext` la méthode exécute le code dans le bloc Iterator et définit `Current` la propriété de l’objet énumérateur comme il convient.</span><span class="sxs-lookup"><span data-stu-id="0804f-1580">Invoking the `MoveNext` method executes code in the iterator block and sets the `Current` property of the enumerator object as appropriate.</span></span> <span data-ttu-id="0804f-1581">L’action exacte effectuée par `MoveNext` dépend de l’état de l’objet énumérateur lorsque `MoveNext` est appelé :</span><span class="sxs-lookup"><span data-stu-id="0804f-1581">The precise action performed by `MoveNext` depends on the state of the enumerator object when `MoveNext` is invoked:</span></span>

*  <span data-ttu-id="0804f-1582">Si l’état de l’objet énumérateur est ***antérieur***, appelez `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="0804f-1582">If the state of the enumerator object is ***before***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="0804f-1583">Modifie l’État en ***en cours d’exécution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1583">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="0804f-1584">Initialise les paramètres (y compris `this`) du bloc itérateur aux valeurs d’argument et à la valeur d’instance enregistrées lorsque l’objet énumérateur a été initialisé.</span><span class="sxs-lookup"><span data-stu-id="0804f-1584">Initializes the parameters (including `this`) of the iterator block to the argument values and instance value saved when the enumerator object was initialized.</span></span>
   * <span data-ttu-id="0804f-1585">Exécute le bloc itérateur à partir du début jusqu’à ce que l’exécution soit interrompue (comme décrit ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="0804f-1585">Executes the iterator block from the beginning until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="0804f-1586">Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat `MoveNext` de l’appel de n’est pas spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-1586">If the state of the enumerator object is ***running***, the result of invoking `MoveNext` is unspecified.</span></span>
*  <span data-ttu-id="0804f-1587">Si l’état de l’objet énumérateur est ***suspendu***, appelez `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="0804f-1587">If the state of the enumerator object is ***suspended***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="0804f-1588">Modifie l’État en ***en cours d’exécution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1588">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="0804f-1589">Restaure les valeurs de tous les paramètres et variables locales (y compris celui-ci) aux valeurs enregistrées lors de la dernière suspension de l’exécution du bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1589">Restores the values of all local variables and parameters (including this) to the values saved when execution of the iterator block was last suspended.</span></span> <span data-ttu-id="0804f-1590">Notez que le contenu de tous les objets référencés par ces variables peut avoir changé depuis l’appel précédent à MoveNext.</span><span class="sxs-lookup"><span data-stu-id="0804f-1590">Note that the contents of any objects referenced by these variables may have changed since the previous call to MoveNext.</span></span>
   * <span data-ttu-id="0804f-1591">Reprend l’exécution du bloc itérateur immédiatement après l' `yield return` instruction qui a provoqué la suspension de l’exécution et continue jusqu’à ce que l’exécution soit interrompue (comme décrit ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="0804f-1591">Resumes execution of the iterator block immediately following the `yield return` statement that caused the suspension of execution and continues until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="0804f-1592">Si l’état de l’objet énumérateur est ***après***, l’appel `MoveNext` de `false`retourne.</span><span class="sxs-lookup"><span data-stu-id="0804f-1592">If the state of the enumerator object is ***after***, invoking `MoveNext` returns `false`.</span></span>


<span data-ttu-id="0804f-1593">Lorsque `MoveNext` exécute le bloc Iterator, l’exécution peut être interrompue de quatre façons : Par une `yield return` instruction, par une `yield break` instruction, en rencontrant la fin du bloc itérateur, et par une exception levée et propagée en dehors du bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1593">When `MoveNext` executes the iterator block, execution can be interrupted in four ways: By a `yield return` statement, by a `yield break` statement, by encountering the end of the iterator block, and by an exception being thrown and propagated out of the iterator block.</span></span>

*  <span data-ttu-id="0804f-1594">Quand une `yield return` instruction est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :</span><span class="sxs-lookup"><span data-stu-id="0804f-1594">When a `yield return` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="0804f-1595">L’expression donnée dans l’instruction est évaluée, implicitement convertie en type yield et assignée à la `Current` propriété de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1595">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
   * <span data-ttu-id="0804f-1596">L’exécution du corps de l’itérateur est suspendue.</span><span class="sxs-lookup"><span data-stu-id="0804f-1596">Execution of the iterator body is suspended.</span></span> <span data-ttu-id="0804f-1597">Les valeurs de tous les paramètres et variables locales ( `this`y compris) sont enregistrées, comme c’est l' `yield return` emplacement de cette instruction.</span><span class="sxs-lookup"><span data-stu-id="0804f-1597">The values of all local variables and parameters (including `this`) are saved, as is the location of this `yield return` statement.</span></span> <span data-ttu-id="0804f-1598">Si l' `yield return` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés ne sont pas exécutés pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1598">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
   * <span data-ttu-id="0804f-1599">L’état de l’objet énumérateur est modifié en ***suspendu***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1599">The state of the enumerator object is changed to ***suspended***.</span></span>
   * <span data-ttu-id="0804f-1600">La `MoveNext` méthode retourne `true` à son appelant, ce qui indique que l’itération a réussi à avancer jusqu’à la valeur suivante.</span><span class="sxs-lookup"><span data-stu-id="0804f-1600">The `MoveNext` method returns `true` to its caller, indicating that the iteration successfully advanced to the next value.</span></span>
*  <span data-ttu-id="0804f-1601">Quand une `yield break` instruction est rencontrée ([l’instruction yield](statements.md#the-yield-statement)) :</span><span class="sxs-lookup"><span data-stu-id="0804f-1601">When a `yield break` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="0804f-1602">Si l' `yield break` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés sont exécutés.</span><span class="sxs-lookup"><span data-stu-id="0804f-1602">If the `yield break` statement is within one or more `try` blocks, the associated `finally` blocks are executed.</span></span>
   * <span data-ttu-id="0804f-1603">L’état de l’objet énumérateur est remplacé par ***après***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1603">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="0804f-1604">La `MoveNext` méthode retourne `false` à son appelant, ce qui indique que l’itération est terminée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1604">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="0804f-1605">Quand la fin du corps de l’itérateur est rencontrée :</span><span class="sxs-lookup"><span data-stu-id="0804f-1605">When the end of the iterator body is encountered:</span></span>
   * <span data-ttu-id="0804f-1606">L’état de l’objet énumérateur est remplacé par ***après***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1606">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="0804f-1607">La `MoveNext` méthode retourne `false` à son appelant, ce qui indique que l’itération est terminée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1607">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="0804f-1608">Lorsqu’une exception est levée et propagée en dehors du bloc Iterator :</span><span class="sxs-lookup"><span data-stu-id="0804f-1608">When an exception is thrown and propagated out of the iterator block:</span></span>
   * <span data-ttu-id="0804f-1609">Les `finally` blocs appropriés dans le corps de l’itérateur ont été exécutés par la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="0804f-1609">Appropriate `finally` blocks in the iterator body will have been executed by the exception propagation.</span></span>
   * <span data-ttu-id="0804f-1610">L’état de l’objet énumérateur est remplacé par ***après***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1610">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="0804f-1611">La propagation de l’exception continue à l’appelant de `MoveNext` la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-1611">The exception propagation continues to the caller of the `MoveNext` method.</span></span>

#### <a name="the-current-property"></a><span data-ttu-id="0804f-1612">La propriété actuelle</span><span class="sxs-lookup"><span data-stu-id="0804f-1612">The Current property</span></span>

<span data-ttu-id="0804f-1613">La propriété d' `Current` un objet Enumerator est affectée `yield return` par les instructions du bloc Iterator.</span><span class="sxs-lookup"><span data-stu-id="0804f-1613">An enumerator object's `Current` property is affected by `yield return` statements in the iterator block.</span></span>

<span data-ttu-id="0804f-1614">Lorsqu’un objet énumérateur est dans l’état ***suspendu*** , la valeur de `Current` est la valeur définie par l’appel précédent à `MoveNext`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1614">When an enumerator object is in the ***suspended*** state, the value of `Current` is the value set by the previous call to `MoveNext`.</span></span> <span data-ttu-id="0804f-1615">Lorsqu’un objet énumérateur se trouve dans les États ***Before***, ***Running***ou ***after*** , `Current` le résultat de l’accès n’est pas spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-1615">When an enumerator object is in the ***before***, ***running***, or ***after*** states, the result of accessing `Current` is unspecified.</span></span>

<span data-ttu-id="0804f-1616">Pour un itérateur avec un type yield autre que `object`, le résultat de l' `Current` accès par le biais de l' `IEnumerable` implémentation de l’objet énumérateur `Current` correspond `IEnumerator<T>` à l’accès par le biais de l’objet énumérateur implémenter et effectuer un cast `object`du résultat vers.</span><span class="sxs-lookup"><span data-stu-id="0804f-1616">For an iterator with a yield type other than `object`, the result of accessing `Current` through the enumerator object's `IEnumerable` implementation corresponds to accessing `Current` through the enumerator object's `IEnumerator<T>` implementation and casting the result to `object`.</span></span>

#### <a name="the-dispose-method"></a><span data-ttu-id="0804f-1617">Méthode dispose</span><span class="sxs-lookup"><span data-stu-id="0804f-1617">The Dispose method</span></span>

<span data-ttu-id="0804f-1618">La `Dispose` méthode est utilisée pour nettoyer l’itération en plaçant l’objet énumérateur à l’état ***after*** .</span><span class="sxs-lookup"><span data-stu-id="0804f-1618">The `Dispose` method is used to clean up the iteration by bringing the enumerator object to the ***after*** state.</span></span>

*  <span data-ttu-id="0804f-1619">Si l’état de l’objet énumérateur est ***antérieur***, l’appel `Dispose` de passe l’État à ***après***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1619">If the state of the enumerator object is ***before***, invoking `Dispose` changes the state to ***after***.</span></span>
*  <span data-ttu-id="0804f-1620">Si l’état de l’objet énumérateur est ***en cours d’exécution***, le résultat `Dispose` de l’appel de n’est pas spécifié.</span><span class="sxs-lookup"><span data-stu-id="0804f-1620">If the state of the enumerator object is ***running***, the result of invoking `Dispose` is unspecified.</span></span>
*  <span data-ttu-id="0804f-1621">Si l’état de l’objet énumérateur est ***suspendu***, appelez `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="0804f-1621">If the state of the enumerator object is ***suspended***, invoking `Dispose`:</span></span>
   * <span data-ttu-id="0804f-1622">Modifie l’État en ***en cours d’exécution***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1622">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="0804f-1623">Exécute tous les blocs finally comme si la dernière `yield return` instruction exécutée `yield break` était une instruction.</span><span class="sxs-lookup"><span data-stu-id="0804f-1623">Executes any finally blocks as if the last executed `yield return` statement were a `yield break` statement.</span></span> <span data-ttu-id="0804f-1624">Si cela entraîne la levée d’une exception et sa propagation en dehors du corps de l’itérateur, l’état de l’objet énumérateur est défini sur ***après*** et l’exception est propagée à l’appelant `Dispose` de la méthode.</span><span class="sxs-lookup"><span data-stu-id="0804f-1624">If this causes an exception to be thrown and propagated out of the iterator body, the state of the enumerator object is set to ***after*** and the exception is propagated to the caller of the `Dispose` method.</span></span>
   * <span data-ttu-id="0804f-1625">Passe l’État à ***après***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1625">Changes the state to ***after***.</span></span>
*  <span data-ttu-id="0804f-1626">Si l’état de l’objet énumérateur est ***après***, l’appel `Dispose` de n’a aucun effet.</span><span class="sxs-lookup"><span data-stu-id="0804f-1626">If the state of the enumerator object is ***after***, invoking `Dispose` has no affect.</span></span>

### <a name="enumerable-objects"></a><span data-ttu-id="0804f-1627">Objets énumérables</span><span class="sxs-lookup"><span data-stu-id="0804f-1627">Enumerable objects</span></span>

<span data-ttu-id="0804f-1628">Lorsqu’un membre de fonction qui retourne un type d’interface énumérable est implémenté à l’aide d’un bloc itérateur, l’appel de la fonction member n’exécute pas immédiatement le code dans le bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1628">When a function member returning an enumerable interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="0804f-1629">Au lieu de cela, un ***objet énumérable*** est créé et retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1629">Instead, an ***enumerable object*** is created and returned.</span></span> <span data-ttu-id="0804f-1630">La méthode de `GetEnumerator` l’objet énumérable retourne un objet énumérateur qui encapsule le code spécifié dans le bloc Iterator, et l’exécution du code dans le bloc Iterator se produit lorsque la `MoveNext` méthode de l’objet énumérateur est appelée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1630">The enumerable object's `GetEnumerator` method returns an enumerator object that encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="0804f-1631">Un objet énumérable présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="0804f-1631">An enumerable object has the following characteristics:</span></span>

*  <span data-ttu-id="0804f-1632">Il implémente `IEnumerable` et `IEnumerable<T>`, où `T` est le type de rendement de l’itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1632">It implements `IEnumerable` and `IEnumerable<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="0804f-1633">Elle est initialisée à l’aide d’une copie des valeurs d’argument (le cas échéant) et de la valeur d’instance passée à la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1633">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>

<span data-ttu-id="0804f-1634">Un objet énumérable est généralement une instance d’une classe énumérable générée par le compilateur qui encapsule le code dans le bloc itérateur et implémente les interfaces énumérables, mais d’autres méthodes d’implémentation sont possibles.</span><span class="sxs-lookup"><span data-stu-id="0804f-1634">An enumerable object is typically an instance of a compiler-generated enumerable class that encapsulates the code in the iterator block and implements the enumerable interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="0804f-1635">Si une classe énumérable est générée par le compilateur, cette classe sera imbriquée, directement ou indirectement, dans la classe contenant la fonction membre, elle aura une accessibilité privée et un nom est réservé à l’utilisation du compilateur ([identificateurs](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1635">If an enumerable class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="0804f-1636">Un objet énumérable peut implémenter plus d’interfaces que celles spécifiées ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0804f-1636">An enumerable object may implement more interfaces than those specified above.</span></span> <span data-ttu-id="0804f-1637">En particulier, un objet énumérable peut également `IEnumerator` implémenter et `IEnumerator<T>`, ce qui lui permet de servir à la fois un énumérable et un énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1637">In particular, an enumerable object may also implement `IEnumerator` and `IEnumerator<T>`, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="0804f-1638">Dans ce type d’implémentation, la première fois qu’une méthode d' `GetEnumerator` un objet énumérable est appelée, l’objet énumérable lui-même est retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1638">In that type of implementation, the first time an enumerable object's `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="0804f-1639">Les appels suivants de l’objet `GetEnumerator`énumérable, le cas échéant, retournent une copie de l’objet énumérable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1639">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="0804f-1640">Ainsi, chaque énumérateur retourné a son propre État et les modifications apportées dans un énumérateur n’en affectent pas une autre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1640">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span>

#### <a name="the-getenumerator-method"></a><span data-ttu-id="0804f-1641">Méthode GetEnumerator</span><span class="sxs-lookup"><span data-stu-id="0804f-1641">The GetEnumerator method</span></span>

<span data-ttu-id="0804f-1642">Un objet énumérable fournit une implémentation des `GetEnumerator` méthodes `IEnumerable` des interfaces et `IEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="0804f-1642">An enumerable object provides an implementation of the `GetEnumerator` methods of the `IEnumerable` and `IEnumerable<T>` interfaces.</span></span> <span data-ttu-id="0804f-1643">Les deux `GetEnumerator` méthodes partagent une implémentation commune qui acquiert et retourne un objet énumérateur disponible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1643">The two `GetEnumerator` methods share a common implementation that acquires and returns an available enumerator object.</span></span> <span data-ttu-id="0804f-1644">L’objet énumérateur est initialisé avec les valeurs d’argument et la valeur d’instance enregistrées lorsque l’objet énumérable a été initialisé, mais dans le cas contraire, l’objet énumérateur fonctionne comme décrit dans [objets Enumerator](classes.md#enumerator-objects).</span><span class="sxs-lookup"><span data-stu-id="0804f-1644">The enumerator object is initialized with the argument values and instance value saved when the enumerable object was initialized, but otherwise the enumerator object functions as described in [Enumerator objects](classes.md#enumerator-objects).</span></span>

### <a name="implementation-example"></a><span data-ttu-id="0804f-1645">Exemple d’implémentation</span><span class="sxs-lookup"><span data-stu-id="0804f-1645">Implementation example</span></span>

<span data-ttu-id="0804f-1646">Cette section décrit une implémentation possible d’itérateurs en termes de constructions C# standard.</span><span class="sxs-lookup"><span data-stu-id="0804f-1646">This section describes a possible implementation of iterators in terms of standard C# constructs.</span></span> <span data-ttu-id="0804f-1647">L’implémentation décrite ici est basée sur les principes utilisés par le compilateur Microsoft C# , mais il ne s’agit pas d’une implémentation obligatoire ou du seul possible.</span><span class="sxs-lookup"><span data-stu-id="0804f-1647">The implementation described here is based on the same principles used by the Microsoft C# compiler, but it is by no means a mandated implementation or the only one possible.</span></span>

<span data-ttu-id="0804f-1648">La classe `Stack<T>` suivante implémente sa `GetEnumerator` méthode à l’aide d’un itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1648">The following `Stack<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="0804f-1649">L’itérateur énumère les éléments de la pile dans l’ordre de haut en bas.</span><span class="sxs-lookup"><span data-stu-id="0804f-1649">The iterator enumerates the elements of the stack in top to bottom order.</span></span>

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

class Stack<T>: IEnumerable<T>
{
    T[] items;
    int count;

    public void Push(T item) {
        if (items == null) {
            items = new T[4];
        }
        else if (items.Length == count) {
            T[] newItems = new T[count * 2];
            Array.Copy(items, 0, newItems, 0, count);
            items = newItems;
        }
        items[count++] = item;
    }

    public T Pop() {
        T result = items[--count];
        items[count] = default(T);
        return result;
    }

    public IEnumerator<T> GetEnumerator() {
        for (int i = count - 1; i >= 0; --i) yield return items[i];
    }
}
```

<span data-ttu-id="0804f-1650">La `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1650">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
class Stack<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1: IEnumerator<T>, IEnumerator
    {
        int __state;
        T __current;
        Stack<T> __this;
        int i;

        public __Enumerator1(Stack<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
                case 1: goto __state1;
                case 2: goto __state2;
            }
            i = __this.count - 1;
        __loop:
            if (i < 0) goto __state2;
            __current = __this.items[i];
            __state = 1;
            return true;
        __state1:
            --i;
            goto __loop;
        __state2:
            __state = 2;
            return false;
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="0804f-1651">Dans la traduction précédente, le code du bloc itérateur est converti en ordinateur d’État et placé dans la `MoveNext` méthode de la classe Enumerator.</span><span class="sxs-lookup"><span data-stu-id="0804f-1651">In the preceding translation, the code in the iterator block is turned into a state machine and placed in the `MoveNext` method of the enumerator class.</span></span> <span data-ttu-id="0804f-1652">En outre, la variable `i` locale est transformée en champ dans l’objet énumérateur afin de pouvoir continuer à exister entre les appels `MoveNext`de.</span><span class="sxs-lookup"><span data-stu-id="0804f-1652">Furthermore, the local variable `i` is turned into a field in the enumerator object so it can continue to exist across invocations of `MoveNext`.</span></span>

<span data-ttu-id="0804f-1653">L’exemple suivant imprime une table de multiplication simple des entiers 1 à 10.</span><span class="sxs-lookup"><span data-stu-id="0804f-1653">The following example prints a simple multiplication table of the integers 1 through 10.</span></span> <span data-ttu-id="0804f-1654">La `FromTo` méthode de l’exemple retourne un objet énumérable et est implémentée à l’aide d’un itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1654">The `FromTo` method in the example returns an enumerable object and is implemented using an iterator.</span></span>

```csharp
using System;
using System.Collections.Generic;

class Test
{
    static IEnumerable<int> FromTo(int from, int to) {
        while (from <= to) yield return from++;
    }

    static void Main() {
        IEnumerable<int> e = FromTo(1, 10);
        foreach (int x in e) {
            foreach (int y in e) {
                Console.Write("{0,3} ", x * y);
            }
            Console.WriteLine();
        }
    }
}
```

<span data-ttu-id="0804f-1655">La `FromTo` méthode peut être traduite en une instanciation d’une classe énumérable générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1655">The `FromTo` method can be translated into an instantiation of a compiler-generated enumerable class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
using System;
using System.Threading;
using System.Collections;
using System.Collections.Generic;

class Test
{
    ...

    static IEnumerable<int> FromTo(int from, int to) {
        return new __Enumerable1(from, to);
    }

    class __Enumerable1:
        IEnumerable<int>, IEnumerable,
        IEnumerator<int>, IEnumerator
    {
        int __state;
        int __current;
        int __from;
        int from;
        int to;
        int i;

        public __Enumerable1(int __from, int to) {
            this.__from = __from;
            this.to = to;
        }

        public IEnumerator<int> GetEnumerator() {
            __Enumerable1 result = this;
            if (Interlocked.CompareExchange(ref __state, 1, 0) != 0) {
                result = new __Enumerable1(__from, to);
                result.__state = 1;
            }
            result.from = result.__from;
            return result;
        }

        IEnumerator IEnumerable.GetEnumerator() {
            return (IEnumerator)GetEnumerator();
        }

        public int Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
            case 1:
                if (from > to) goto case 2;
                __current = from++;
                __state = 1;
                return true;
            case 2:
                __state = 2;
                return false;
            default:
                throw new InvalidOperationException();
            }
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="0804f-1656">La classe Enumerable implémente à la fois les interfaces Enumerable et les interfaces d’énumérateur, ce qui lui permet de servir à la fois un énumérable et un énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1656">The enumerable class implements both the enumerable interfaces and the enumerator interfaces, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="0804f-1657">La première fois que `GetEnumerator` la méthode est appelée, l’objet énumérable lui-même est retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1657">The first time the `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="0804f-1658">Les appels suivants de l’objet `GetEnumerator`énumérable, le cas échéant, retournent une copie de l’objet énumérable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1658">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="0804f-1659">Ainsi, chaque énumérateur retourné a son propre État et les modifications apportées dans un énumérateur n’en affectent pas une autre.</span><span class="sxs-lookup"><span data-stu-id="0804f-1659">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span> <span data-ttu-id="0804f-1660">La `Interlocked.CompareExchange` méthode est utilisée pour garantir l’opération thread-safe.</span><span class="sxs-lookup"><span data-stu-id="0804f-1660">The `Interlocked.CompareExchange` method is used to ensure thread-safe operation.</span></span>

<span data-ttu-id="0804f-1661">Les `from` paramètres `to` et sont convertis en champs dans la classe Enumerable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1661">The `from` and `to` parameters are turned into fields in the enumerable class.</span></span> <span data-ttu-id="0804f-1662">Étant `from` donné que est modifié dans le bloc Iterator, `__from` un champ supplémentaire est introduit pour contenir la valeur initiale `from` donnée à dans chaque énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1662">Because `from` is modified in the iterator block, an additional `__from` field is introduced to hold the initial value given to `from` in each enumerator.</span></span>

<span data-ttu-id="0804f-1663">La `MoveNext` méthode lève une `InvalidOperationException` si elle est appelée lorsque `__state` est `0`.</span><span class="sxs-lookup"><span data-stu-id="0804f-1663">The `MoveNext` method throws an `InvalidOperationException` if it is called when `__state` is `0`.</span></span> <span data-ttu-id="0804f-1664">Cela permet de protéger l’utilisation de l’objet énumérable en tant qu’objet `GetEnumerator`énumérateur sans appeler au préalable.</span><span class="sxs-lookup"><span data-stu-id="0804f-1664">This protects against use of the enumerable object as an enumerator object without first calling `GetEnumerator`.</span></span>

<span data-ttu-id="0804f-1665">L’exemple suivant montre une classe d’arborescence simple.</span><span class="sxs-lookup"><span data-stu-id="0804f-1665">The following example shows a simple tree class.</span></span> <span data-ttu-id="0804f-1666">La `Tree<T>` classe implémente sa `GetEnumerator` méthode à l’aide d’un itérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1666">The `Tree<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="0804f-1667">L’itérateur énumère les éléments de l’arborescence dans l’ordre des infixes.</span><span class="sxs-lookup"><span data-stu-id="0804f-1667">The iterator enumerates the elements of the tree in infix order.</span></span>

```csharp
using System;
using System.Collections.Generic;

class Tree<T>: IEnumerable<T>
{
    T value;
    Tree<T> left;
    Tree<T> right;

    public Tree(T value, Tree<T> left, Tree<T> right) {
        this.value = value;
        this.left = left;
        this.right = right;
    }

    public IEnumerator<T> GetEnumerator() {
        if (left != null) foreach (T x in left) yield x;
        yield value;
        if (right != null) foreach (T x in right) yield x;
    }
}

class Program
{
    static Tree<T> MakeTree<T>(T[] items, int left, int right) {
        if (left > right) return null;
        int i = (left + right) / 2;
        return new Tree<T>(items[i], 
            MakeTree(items, left, i - 1),
            MakeTree(items, i + 1, right));
    }

    static Tree<T> MakeTree<T>(params T[] items) {
        return MakeTree(items, 0, items.Length - 1);
    }

    // The output of the program is:
    // 1 2 3 4 5 6 7 8 9
    // Mon Tue Wed Thu Fri Sat Sun

    static void Main() {
        Tree<int> ints = MakeTree(1, 2, 3, 4, 5, 6, 7, 8, 9);
        foreach (int i in ints) Console.Write("{0} ", i);
        Console.WriteLine();

        Tree<string> strings = MakeTree(
            "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun");
        foreach (string s in strings) Console.Write("{0} ", s);
        Console.WriteLine();
    }
}
```

<span data-ttu-id="0804f-1668">La `GetEnumerator` méthode peut être traduite en une instanciation d’une classe d’énumérateur générée par le compilateur qui encapsule le code dans le bloc itérateur, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="0804f-1668">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
class Tree<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1 : IEnumerator<T>, IEnumerator
    {
        Node<T> __this;
        IEnumerator<T> __left, __right;
        int __state;
        T __current;

        public __Enumerator1(Node<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            try {
                switch (__state) {

                case 0:
                    __state = -1;
                    if (__this.left == null) goto __yield_value;
                    __left = __this.left.GetEnumerator();
                    goto case 1;

                case 1:
                    __state = -2;
                    if (!__left.MoveNext()) goto __left_dispose;
                    __current = __left.Current;
                    __state = 1;
                    return true;

                __left_dispose:
                    __state = -1;
                    __left.Dispose();

                __yield_value:
                    __current = __this.value;
                    __state = 2;
                    return true;

                case 2:
                    __state = -1;
                    if (__this.right == null) goto __end;
                    __right = __this.right.GetEnumerator();
                    goto case 3;

                case 3:
                    __state = -3;
                    if (!__right.MoveNext()) goto __right_dispose;
                    __current = __right.Current;
                    __state = 3;
                    return true;

                __right_dispose:
                    __state = -1;
                    __right.Dispose();

                __end:
                    __state = 4;
                    break;

                }
            }
            finally {
                if (__state < 0) Dispose();
            }
            return false;
        }

        public void Dispose() {
            try {
                switch (__state) {

                case 1:
                case -2:
                    __left.Dispose();
                    break;

                case 3:
                case -3:
                    __right.Dispose();
                    break;

                }
            }
            finally {
                __state = 4;
            }
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="0804f-1669">Les compilateurs temporaires générés par le `foreach` compilateur utilisés dans les instructions sont `__left` levés dans les champs et `__right` de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1669">The compiler generated temporaries used in the `foreach` statements are lifted into the `__left` and `__right` fields of the enumerator object.</span></span> <span data-ttu-id="0804f-1670">Le `__state` champ de l’objet énumérateur est mis à jour avec soin afin `Dispose()` que la méthode correcte soit appelée correctement si une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1670">The `__state` field of the enumerator object is carefully updated so that the correct `Dispose()` method will be called correctly if an exception is thrown.</span></span> <span data-ttu-id="0804f-1671">Notez qu’il n’est pas possible d’écrire le code traduit avec `foreach` des instructions simples.</span><span class="sxs-lookup"><span data-stu-id="0804f-1671">Note that it is not possible to write the translated code with simple `foreach` statements.</span></span>

## <a name="async-functions"></a><span data-ttu-id="0804f-1672">Fonctions Async</span><span class="sxs-lookup"><span data-stu-id="0804f-1672">Async functions</span></span>

<span data-ttu-id="0804f-1673">Une méthode ([méthodes](classes.md#methods)) ou une fonction anonyme ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) avec le `async` modificateur est appelée ***fonction Async***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1673">A method ([Methods](classes.md#methods)) or anonymous function ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) with the `async` modifier is called an ***async function***.</span></span> <span data-ttu-id="0804f-1674">En général, le terme ***Async*** est utilisé pour décrire tout type de fonction qui a le `async` modificateur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1674">In general, the term ***async*** is used to describe any kind of function that has the `async` modifier.</span></span>

<span data-ttu-id="0804f-1675">Il s’agit d’une erreur de compilation pour la liste de paramètres formels d’une fonction Async `ref` pour `out` spécifier des paramètres ou.</span><span class="sxs-lookup"><span data-stu-id="0804f-1675">It is a compile-time error for the formal parameter list of an async function to specify any `ref` or `out` parameters.</span></span>

<span data-ttu-id="0804f-1676">Le *type_retour* d’une méthode Async doit être `void` ou un ***type de tâche***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1676">The *return_type* of an async method must be either `void` or a ***task type***.</span></span> <span data-ttu-id="0804f-1677">Les types de tâches `System.Threading.Tasks.Task` sont et les types `System.Threading.Tasks.Task<T>`construits à partir de.</span><span class="sxs-lookup"><span data-stu-id="0804f-1677">The task types are `System.Threading.Tasks.Task` and types constructed from `System.Threading.Tasks.Task<T>`.</span></span> <span data-ttu-id="0804f-1678">Par souci de concision, dans ce chapitre, ces types sont référencés en `Task` tant `Task<T>`que et, respectivement.</span><span class="sxs-lookup"><span data-stu-id="0804f-1678">For the sake of brevity, in this chapter these types are referenced as `Task` and `Task<T>`, respectively.</span></span> <span data-ttu-id="0804f-1679">Une méthode Async qui retourne un type de tâche est dite retournant des tâches.</span><span class="sxs-lookup"><span data-stu-id="0804f-1679">An async method returning a task type is said to be task-returning.</span></span>

<span data-ttu-id="0804f-1680">La définition exacte des types de tâches est définie par l’implémentation, mais du point de vue du langage, un type de tâche se trouve dans l’un des États incomplet, SUCCEEDED ou Faulted.</span><span class="sxs-lookup"><span data-stu-id="0804f-1680">The exact definition of the task types is implementation defined, but from the language's point of view a task type is in one of the states incomplete, succeeded or faulted.</span></span> <span data-ttu-id="0804f-1681">Une tâche ayant généré une erreur enregistre une exception pertinente.</span><span class="sxs-lookup"><span data-stu-id="0804f-1681">A faulted task records a pertinent exception.</span></span> <span data-ttu-id="0804f-1682">Une opération `Task<T>` réussie enregistre un résultat de `T`type.</span><span class="sxs-lookup"><span data-stu-id="0804f-1682">A succeeded `Task<T>` records a result of type `T`.</span></span> <span data-ttu-id="0804f-1683">Les types de tâches sont await, et peuvent par conséquent être les opérandes des expressions await ([expression await](expressions.md#await-expressions)).</span><span class="sxs-lookup"><span data-stu-id="0804f-1683">Task types are awaitable, and can therefore be the operands of await expressions ([Await expressions](expressions.md#await-expressions)).</span></span>

<span data-ttu-id="0804f-1684">Un appel de fonction Async a la possibilité de suspendre l’évaluation à l’aide d’expressions await ([expression await](expressions.md#await-expressions)) dans son corps.</span><span class="sxs-lookup"><span data-stu-id="0804f-1684">An async function invocation has the ability to suspend evaluation by means of await expressions ([Await expressions](expressions.md#await-expressions)) in its body.</span></span> <span data-ttu-id="0804f-1685">L’évaluation peut être reprise ultérieurement au moment de l’interruption de l’expression await au moyen d’un ***délégué de reprise***.</span><span class="sxs-lookup"><span data-stu-id="0804f-1685">Evaluation may later be resumed at the point of the suspending await expression by means of a ***resumption delegate***.</span></span> <span data-ttu-id="0804f-1686">Le délégué de reprise est de `System.Action`type, et lorsqu’il est appelé, l’évaluation de l’appel de la fonction Async reprend à partir de l’expression await là où elle s’est arrêtée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1686">The resumption delegate is of type `System.Action`, and when it is invoked, evaluation of the async function invocation will resume from the await expression where it left off.</span></span> <span data-ttu-id="0804f-1687">L' ***appelant actuel*** d’un appel de fonction Async est l’appelant d’origine si l’appel de fonction n’a jamais été interrompu ou l’appelant le plus récent du délégué de reprise dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="0804f-1687">The ***current caller*** of an async function invocation is the original caller if the function invocation has never been suspended, or the most recent caller of the resumption delegate otherwise.</span></span>

### <a name="evaluation-of-a-task-returning-async-function"></a><span data-ttu-id="0804f-1688">Évaluation d’une fonction asynchrone retournant des tâches</span><span class="sxs-lookup"><span data-stu-id="0804f-1688">Evaluation of a task-returning async function</span></span>

<span data-ttu-id="0804f-1689">L’appel d’une fonction Async retournant des tâches provoque la génération d’une instance du type de tâche retourné.</span><span class="sxs-lookup"><span data-stu-id="0804f-1689">Invocation of a task-returning async function causes an instance of the returned task type to be generated.</span></span> <span data-ttu-id="0804f-1690">C’est ce que l’on appelle la ***tâche de retour*** de la fonction Async.</span><span class="sxs-lookup"><span data-stu-id="0804f-1690">This is called the ***return task*** of the async function.</span></span> <span data-ttu-id="0804f-1691">L’état initial de la tâche est incomplet.</span><span class="sxs-lookup"><span data-stu-id="0804f-1691">The task is initially in an incomplete state.</span></span>

<span data-ttu-id="0804f-1692">Le corps de la fonction Async est ensuite évalué jusqu’à ce qu’il soit suspendu (en atteignant une expression await) ou se termine, auquel point le contrôle est retourné à l’appelant, ainsi que la tâche de retour.</span><span class="sxs-lookup"><span data-stu-id="0804f-1692">The async function body is then evaluated until it is either suspended (by reaching an await expression) or terminates, at which point control is returned to the caller, along with the return task.</span></span>

<span data-ttu-id="0804f-1693">Lorsque le corps de la fonction Async se termine, la tâche de retour est déplacée de l’état incomplet :</span><span class="sxs-lookup"><span data-stu-id="0804f-1693">When the body of the async function terminates, the return task is moved out of the incomplete state:</span></span>

*  <span data-ttu-id="0804f-1694">Si le corps de la fonction se termine suite à l’atteinte d’une instruction return ou à la fin du corps, toute valeur de résultat est enregistrée dans la tâche de retour, qui est placée dans un état de réussite.</span><span class="sxs-lookup"><span data-stu-id="0804f-1694">If the function body terminates as the result of reaching a return statement or the end of the body, any result value is recorded in the return task, which is put into a succeeded state.</span></span>
*  <span data-ttu-id="0804f-1695">Si le corps de la fonction se termine à la suite d’une exception non interceptée ([instruction throw](statements.md#the-throw-statement)), l’exception est enregistrée dans la tâche de retour qui est placée dans un état d’erreur.</span><span class="sxs-lookup"><span data-stu-id="0804f-1695">If the function body terminates as the result of an uncaught exception ([The throw statement](statements.md#the-throw-statement)) the exception is recorded in the return task which is put into a faulted state.</span></span>

### <a name="evaluation-of-a-void-returning-async-function"></a><span data-ttu-id="0804f-1696">Évaluation d’une fonction Async qui retourne void</span><span class="sxs-lookup"><span data-stu-id="0804f-1696">Evaluation of a void-returning async function</span></span>

<span data-ttu-id="0804f-1697">Si le type de retour de la fonction Async `void`est, l’évaluation diffère de celle ci-dessus de la manière suivante : Étant donné qu’aucune tâche n’est retournée, la fonction communique à la place l’achèvement et les exceptions au ***contexte de synchronisation***du thread actuel.</span><span class="sxs-lookup"><span data-stu-id="0804f-1697">If the return type of the async function is `void`, evaluation differs from the above in the following way: Because no task is returned, the function instead communicates completion and exceptions to the current thread's ***synchronization context***.</span></span> <span data-ttu-id="0804f-1698">La définition exacte du contexte de synchronisation dépend de l’implémentation, mais est une représentation de « où » le thread actuel est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0804f-1698">The exact definition of synchronization context is implementation-dependent, but is a representation of "where" the current thread is running.</span></span> <span data-ttu-id="0804f-1699">Le contexte de synchronisation est notifié lorsque l’évaluation d’une fonction Async qui retourne une valeur void commence, se termine correctement ou provoque la levée d’une exception non interceptée.</span><span class="sxs-lookup"><span data-stu-id="0804f-1699">The synchronization context is notified when evaluation of a void-returning async function commences, completes successfully, or causes an uncaught exception to be thrown.</span></span>

<span data-ttu-id="0804f-1700">Cela permet au contexte d’effectuer le suivi du nombre de fonctions Async à retour void qui s’exécutent sous celui-ci et de décider comment propager les exceptions en provenance de ces dernières.</span><span class="sxs-lookup"><span data-stu-id="0804f-1700">This allows the context to keep track of how many void-returning async functions are running under it, and to decide how to propagate exceptions coming out of them.</span></span>
