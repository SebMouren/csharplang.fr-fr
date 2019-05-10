---
ms.openlocfilehash: a8ad8a8b3eda1d00fa745bd92e4371eacc36b79f
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488827"
---
# <a name="attributes"></a><span data-ttu-id="aaf04-101">Attributs</span><span class="sxs-lookup"><span data-stu-id="aaf04-101">Attributes</span></span>

<span data-ttu-id="aaf04-102">Une grande partie du langage c# permet au programmeur de spécifier des informations déclaratives sur les entités définies dans le programme.</span><span class="sxs-lookup"><span data-stu-id="aaf04-102">Much of the C# language enables the programmer to specify declarative information about the entities defined in the program.</span></span> <span data-ttu-id="aaf04-103">Par exemple, l’accessibilité d’une méthode dans une classe est spécifiée en la décorant avec le *method_modifier*s `public`, `protected`, `internal`, et `private`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-103">For example, the accessibility of a method in a class is specified by decorating it with the *method_modifier*s `public`, `protected`, `internal`, and `private`.</span></span>

<span data-ttu-id="aaf04-104">C# permet aux programmeurs d’inventer de nouveaux types d’informations déclaratives, appelé ***attributs***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-104">C# enables programmers to invent new kinds of declarative information, called ***attributes***.</span></span> <span data-ttu-id="aaf04-105">Les programmeurs peuvent ensuite attacher des attributs à plusieurs entités de programme et récupérer des informations d’attribut dans un environnement d’exécution.</span><span class="sxs-lookup"><span data-stu-id="aaf04-105">Programmers can then attach attributes to various program entities, and retrieve attribute information in a run-time environment.</span></span> <span data-ttu-id="aaf04-106">Par exemple, une infrastructure peut définir un `HelpAttribute` attribut qui peut être placé sur des éléments de programme (par exemple, les classes et méthodes) pour fournir un mappage entre ces éléments vers leur documentation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-106">For instance, a framework might define a `HelpAttribute` attribute that can be placed on certain program elements (such as classes and methods) to provide a mapping from those program elements to their documentation.</span></span>

<span data-ttu-id="aaf04-107">Les attributs sont définis via la déclaration de classes d’attributs ([classes d’attributs](attributes.md#attribute-classes)), qui peuvent avoir des paramètres positionnels et nommés ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-107">Attributes are defined through the declaration of attribute classes ([Attribute classes](attributes.md#attribute-classes)), which may have positional and named parameters ([Positional and named parameters](attributes.md#positional-and-named-parameters)).</span></span> <span data-ttu-id="aaf04-108">Les attributs sont associés aux entités d’un programme c# à l’aide des spécifications d’attribut ([spécification des attributs](attributes.md#attribute-specification)) et peuvent être récupérés au moment de l’exécution sous la forme d’instances d’attribut ([des instances d’attribut](attributes.md#attribute-instances)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-108">Attributes are attached to entities in a C# program using attribute specifications ([Attribute specification](attributes.md#attribute-specification)), and can be retrieved at run-time as attribute instances ([Attribute instances](attributes.md#attribute-instances)).</span></span>

## <a name="attribute-classes"></a><span data-ttu-id="aaf04-109">Classes d’attributs</span><span class="sxs-lookup"><span data-stu-id="aaf04-109">Attribute classes</span></span>

<span data-ttu-id="aaf04-110">Une classe qui dérive de la classe abstraite `System.Attribute`, directement ou indirectement, est un ***classe d’attributs***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-110">A class that derives from the abstract class `System.Attribute`, whether directly or indirectly, is an ***attribute class***.</span></span> <span data-ttu-id="aaf04-111">La déclaration d’une classe d’attributs définit un nouveau type de ***attribut*** qui peut être placé dans une déclaration.</span><span class="sxs-lookup"><span data-stu-id="aaf04-111">The declaration of an attribute class defines a new kind of ***attribute*** that can be placed on a declaration.</span></span> <span data-ttu-id="aaf04-112">Par convention, les classes d’attributs sont nommés avec un suffixe de `Attribute`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-112">By convention, attribute classes are named with a suffix of `Attribute`.</span></span> <span data-ttu-id="aaf04-113">Utilisations d’un attribut peuvent inclure ou omettre ce suffixe.</span><span class="sxs-lookup"><span data-stu-id="aaf04-113">Uses of an attribute may either include or omit this suffix.</span></span>

### <a name="attribute-usage"></a><span data-ttu-id="aaf04-114">Utilisation d’attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-114">Attribute usage</span></span>

<span data-ttu-id="aaf04-115">L’attribut `AttributeUsage` ([attribut AttributeUsage le](attributes.md#the-attributeusage-attribute)) est utilisé pour décrire la façon dont une classe d’attributs peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="aaf04-115">The attribute `AttributeUsage` ([The AttributeUsage attribute](attributes.md#the-attributeusage-attribute)) is used to describe how an attribute class can be used.</span></span>

<span data-ttu-id="aaf04-116">`AttributeUsage` a un paramètre positionnel ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)) qui permet à une classe d’attributs spécifier les types de déclarations sur lequel il peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="aaf04-116">`AttributeUsage` has a positional parameter ([Positional and named parameters](attributes.md#positional-and-named-parameters)) that enables an attribute class to specify the kinds of declarations on which it can be used.</span></span> <span data-ttu-id="aaf04-117">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-117">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface)]
public class SimpleAttribute: Attribute 
{
    ...
}
```

<span data-ttu-id="aaf04-118">définit une classe d’attributs nommée `SimpleAttribute` qui peut être placé sur *class_declaration*s et *interface_declaration*s uniquement.</span><span class="sxs-lookup"><span data-stu-id="aaf04-118">defines an attribute class named `SimpleAttribute` that can be placed on *class_declaration*s and *interface_declaration*s only.</span></span> <span data-ttu-id="aaf04-119">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-119">The example</span></span>

```csharp
[Simple] class Class1 {...}

[Simple] interface Interface1 {...}
```

<span data-ttu-id="aaf04-120">illustre plusieurs utilisations de la `Simple` attribut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-120">shows several uses of the `Simple` attribute.</span></span> <span data-ttu-id="aaf04-121">Bien que cet attribut est défini avec le nom `SimpleAttribute`, lorsque cet attribut est utilisé, le `Attribute` suffixe peut être omis, ce qui entraîne le nom court `Simple`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-121">Although this attribute is defined with the name `SimpleAttribute`, when this attribute is used, the `Attribute` suffix may be omitted, resulting in the short name `Simple`.</span></span> <span data-ttu-id="aaf04-122">Par conséquent, l’exemple ci-dessus est sémantiquement équivalente à la suivante :</span><span class="sxs-lookup"><span data-stu-id="aaf04-122">Thus, the example above is semantically equivalent to the following:</span></span>

```csharp
[SimpleAttribute] class Class1 {...}

[SimpleAttribute] interface Interface1 {...}
```

<span data-ttu-id="aaf04-123">`AttributeUsage` a un paramètre nommé ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)) appelé `AllowMultiple`, ce qui indique si l’attribut peut être spécifié plusieurs fois pour une entité donnée.</span><span class="sxs-lookup"><span data-stu-id="aaf04-123">`AttributeUsage` has a named parameter ([Positional and named parameters](attributes.md#positional-and-named-parameters)) called `AllowMultiple`, which indicates whether the attribute can be specified more than once for a given entity.</span></span> <span data-ttu-id="aaf04-124">Si `AllowMultiple` d’un attribut de classe a la valeur true, cette classe d’attributs est un ***classe d’attributs à usages multiples***et peut être spécifié plusieurs fois sur une entité.</span><span class="sxs-lookup"><span data-stu-id="aaf04-124">If `AllowMultiple` for an attribute class is true, then that attribute class is a ***multi-use attribute class***, and can be specified more than once on an entity.</span></span> <span data-ttu-id="aaf04-125">Si `AllowMultiple` d’un attribut de classe a la valeur false ou il n’est pas spécifié, cette classe d’attributs est un ***classe d’attributs à usage unique***et peut être spécifié au maximum une fois sur une entité.</span><span class="sxs-lookup"><span data-stu-id="aaf04-125">If `AllowMultiple` for an attribute class is false or it is unspecified, then that attribute class is a ***single-use attribute class***, and can be specified at most once on an entity.</span></span>

<span data-ttu-id="aaf04-126">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-126">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class AuthorAttribute: Attribute
{
    private string name;

    public AuthorAttribute(string name) {
        this.name = name;
    }

    public string Name {
        get { return name; }
    }
}
```

<span data-ttu-id="aaf04-127">définit une classe d’attributs à usages multiples nommée `AuthorAttribute`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-127">defines a multi-use attribute class named `AuthorAttribute`.</span></span> <span data-ttu-id="aaf04-128">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-128">The example</span></span>

```csharp
[Author("Brian Kernighan"), Author("Dennis Ritchie")] 
class Class1
{
    ...
}
```

<span data-ttu-id="aaf04-129">illustre une déclaration de classe avec deux utilisations de la `Author` attribut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-129">shows a class declaration with two uses of the `Author` attribute.</span></span>

<span data-ttu-id="aaf04-130">`AttributeUsage` a un paramètre nommé appelé `Inherited`, ce qui indique si l’attribut, lorsqu’il est spécifié sur une classe de base, est également hérité par les classes qui dérivent de cette classe de base.</span><span class="sxs-lookup"><span data-stu-id="aaf04-130">`AttributeUsage` has another named parameter called `Inherited`, which indicates whether the attribute, when specified on a base class, is also inherited by classes that derive from that base class.</span></span> <span data-ttu-id="aaf04-131">Si `Inherited` d’un attribut de classe est vrai, alors cet attribut est hérité.</span><span class="sxs-lookup"><span data-stu-id="aaf04-131">If `Inherited` for an attribute class is true, then that attribute is inherited.</span></span> <span data-ttu-id="aaf04-132">Si `Inherited` d’un attribut de classe a la valeur false, alors cet attribut n’est pas hérité.</span><span class="sxs-lookup"><span data-stu-id="aaf04-132">If `Inherited` for an attribute class is false then that attribute is not inherited.</span></span> <span data-ttu-id="aaf04-133">Si elle n’est pas spécifié, sa valeur par défaut est true.</span><span class="sxs-lookup"><span data-stu-id="aaf04-133">If it is unspecified, its default value is true.</span></span>

<span data-ttu-id="aaf04-134">Une classe d’attributs `X` n’ayant ne pas un `AttributeUsage` attribut joint, comme dans</span><span class="sxs-lookup"><span data-stu-id="aaf04-134">An attribute class `X` not having an `AttributeUsage` attribute attached to it, as in</span></span>

```csharp
using System;

class X: Attribute {...}
```

<span data-ttu-id="aaf04-135">Est équivalente à la suivante :</span><span class="sxs-lookup"><span data-stu-id="aaf04-135">is equivalent to the following:</span></span>

```csharp
using System;

[AttributeUsage(
    AttributeTargets.All,
    AllowMultiple = false,
    Inherited = true)
]
class X: Attribute {...}
```

### <a name="positional-and-named-parameters"></a><span data-ttu-id="aaf04-136">Paramètres positionnels et nommés</span><span class="sxs-lookup"><span data-stu-id="aaf04-136">Positional and named parameters</span></span>

<span data-ttu-id="aaf04-137">Classes d’attributs peuvent avoir ***paramètres positionnels*** et ***des paramètres nommés***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-137">Attribute classes can have ***positional parameters*** and ***named parameters***.</span></span> <span data-ttu-id="aaf04-138">Chaque constructeur d’instance public pour une classe d’attributs définit une séquence valide de paramètres positionnels pour cette classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-138">Each public instance constructor for an attribute class defines a valid sequence of positional parameters for that attribute class.</span></span> <span data-ttu-id="aaf04-139">Chaque champ de lecture-écriture non statique publique et de la propriété pour une classe d’attributs définissent un paramètre nommé pour la classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-139">Each non-static public read-write field and property for an attribute class defines a named parameter for the attribute class.</span></span>

<span data-ttu-id="aaf04-140">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-140">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpAttribute: Attribute
{
    public HelpAttribute(string url) {        // Positional parameter
        ...
    }

    public string Topic {                     // Named parameter
        get {...}
        set {...}
    }

    public string Url {
        get {...}
    }
}
```

<span data-ttu-id="aaf04-141">définit une classe d’attributs nommée `HelpAttribute` qui a un paramètre positionnel, `url`et un seul paramètre nommé, `Topic`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-141">defines an attribute class named `HelpAttribute` that has one positional parameter, `url`, and one named parameter, `Topic`.</span></span> <span data-ttu-id="aaf04-142">Bien qu’il soit non static et public, la propriété `Url` ne définit pas un paramètre nommé, dans la mesure où il n’est pas en lecture-écriture.</span><span class="sxs-lookup"><span data-stu-id="aaf04-142">Although it is non-static and public, the property `Url` does not define a named parameter, since it is not read-write.</span></span>

<span data-ttu-id="aaf04-143">Cette classe d’attributs peut être utilisée comme suit :</span><span class="sxs-lookup"><span data-stu-id="aaf04-143">This attribute class might be used as follows:</span></span>

```csharp
[Help("http://www.mycompany.com/.../Class1.htm")]
class Class1
{
    ...
}

[Help("http://www.mycompany.com/.../Misc.htm", Topic = "Class2")]
class Class2
{
    ...
}
```

### <a name="attribute-parameter-types"></a><span data-ttu-id="aaf04-144">Types de paramètre d’attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-144">Attribute parameter types</span></span>

<span data-ttu-id="aaf04-145">Les types des paramètres positionnels et nommés pour une classe d’attributs sont limités à la ***les types de paramètre d’attribut***, qui sont :</span><span class="sxs-lookup"><span data-stu-id="aaf04-145">The types of positional and named parameters for an attribute class are limited to the ***attribute parameter types***, which are:</span></span>

*  <span data-ttu-id="aaf04-146">Un des types suivants : `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-146">One of the following types: `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.</span></span>
*  <span data-ttu-id="aaf04-147">Le type `object`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-147">The type `object`.</span></span>
*  <span data-ttu-id="aaf04-148">Le type `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-148">The type `System.Type`.</span></span>
*  <span data-ttu-id="aaf04-149">Un type enum, fournie il a une accessibilité publique et les types dans lequel elle est imbriquée (le cas échéant) ont également une accessibilité publique ([spécification des attributs](attributes.md#attribute-specification)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-149">An enum type, provided it has public accessibility and the types in which it is nested (if any) also have public accessibility ([Attribute specification](attributes.md#attribute-specification)).</span></span>
*  <span data-ttu-id="aaf04-150">Tableaux unidimensionnels de types ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="aaf04-150">Single-dimensional arrays of the above types.</span></span>
*  <span data-ttu-id="aaf04-151">Un argument de constructeur ou un champ public qui n’a pas un de ces types, ne peut pas être utilisé comme un paramètre positionnel ou nommé dans une spécification d’attribut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-151">A constructor argument or public field which does not have one of these types, cannot be used as a positional or named parameter in an attribute specification.</span></span>

## <a name="attribute-specification"></a><span data-ttu-id="aaf04-152">Spécification d’attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-152">Attribute specification</span></span>

<span data-ttu-id="aaf04-153">***Spécification des attributs*** est l’application d’un attribut défini précédemment à une déclaration.</span><span class="sxs-lookup"><span data-stu-id="aaf04-153">***Attribute specification*** is the application of a previously defined attribute to a declaration.</span></span> <span data-ttu-id="aaf04-154">Un attribut est une partie des informations déclaratives supplémentaires qui sont spécifiées pour une déclaration.</span><span class="sxs-lookup"><span data-stu-id="aaf04-154">An attribute is a piece of additional declarative information that is specified for a declaration.</span></span> <span data-ttu-id="aaf04-155">Attributs peuvent être spécifiés dans une portée globale (pour spécifier des attributs sur l’assembly ou le module conteneur) et pour *type_declaration*s ([les déclarations de Type](namespaces.md#type-declarations)), *class_member_declaration* s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([membres de l’Interface](interfaces.md#interface-members)), *struct_member _declaration*s ([les membres de Struct](structs.md#struct-members)), *enum_member_declaration*s ([membres Enum](enums.md#enum-members)), *accessor_declarations*  ([Accesseurs](classes.md#accessors)), *event_accessor_declarations* ([les événements de type champ](classes.md#field-like-events)), et *formal_parameter_list*s ([paramètres de méthode](classes.md#method-parameters)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-155">Attributes can be specified at global scope (to specify attributes on the containing assembly or module) and for *type_declaration*s ([Type declarations](namespaces.md#type-declarations)), *class_member_declaration*s ([Type parameter constraints](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([Interface members](interfaces.md#interface-members)), *struct_member_declaration*s ([Struct members](structs.md#struct-members)), *enum_member_declaration*s ([Enum members](enums.md#enum-members)), *accessor_declarations* ([Accessors](classes.md#accessors)), *event_accessor_declarations* ([Field-like events](classes.md#field-like-events)), and *formal_parameter_list*s ([Method parameters](classes.md#method-parameters)).</span></span>

<span data-ttu-id="aaf04-156">Les attributs sont spécifiés dans ***attribut sections***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-156">Attributes are specified in ***attribute sections***.</span></span> <span data-ttu-id="aaf04-157">Une section d’attributs se compose d’une paire de crochets qui entourent une liste séparée par des virgules d’un ou plusieurs attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-157">An attribute section consists of a pair of square brackets, which surround a comma-separated list of one or more attributes.</span></span> <span data-ttu-id="aaf04-158">L’ordre dans lequel les attributs sont spécifiés dans une telle liste et l’ordre dans lequel les sections attachées à la même entité de programme sont organisés, n’est pas significatif.</span><span class="sxs-lookup"><span data-stu-id="aaf04-158">The order in which attributes are specified in such a list, and the order in which sections attached to the same program entity are arranged, is not significant.</span></span> <span data-ttu-id="aaf04-159">Par exemple, les spécifications de l’attribut `[A][B]`, `[B][A]`, `[A,B]`, et `[B,A]` sont équivalentes.</span><span class="sxs-lookup"><span data-stu-id="aaf04-159">For instance, the attribute specifications `[A][B]`, `[B][A]`, `[A,B]`, and `[B,A]` are equivalent.</span></span>

```antlr
global_attributes
    : global_attribute_section+
    ;

global_attribute_section
    : '[' global_attribute_target_specifier attribute_list ']'
    | '[' global_attribute_target_specifier attribute_list ',' ']'
    ;

global_attribute_target_specifier
    : global_attribute_target ':'
    ;

global_attribute_target
    : 'assembly'
    | 'module'
    ;

attributes
    : attribute_section+
    ;

attribute_section
    : '[' attribute_target_specifier? attribute_list ']'
    | '[' attribute_target_specifier? attribute_list ',' ']'
    ;

attribute_target_specifier
    : attribute_target ':'
    ;

attribute_target
    : 'field'
    | 'event'
    | 'method'
    | 'param'
    | 'property'
    | 'return'
    | 'type'
    ;

attribute_list
    : attribute (',' attribute)*
    ;

attribute
    : attribute_name attribute_arguments?
    ;

attribute_name
    : type_name
    ;

attribute_arguments
    : '(' positional_argument_list? ')'
    | '(' positional_argument_list ',' named_argument_list ')'
    | '(' named_argument_list ')'
    ;

positional_argument_list
    : positional_argument (',' positional_argument)*
    ;

positional_argument
    : attribute_argument_expression
    ;

named_argument_list
    : named_argument (','  named_argument)*
    ;

named_argument
    : identifier '=' attribute_argument_expression
    ;

attribute_argument_expression
    : expression
    ;
```

<span data-ttu-id="aaf04-160">Un attribut se compose d’un *attribute_name* et une liste facultative d’arguments positionnels et nommés.</span><span class="sxs-lookup"><span data-stu-id="aaf04-160">An attribute consists of an *attribute_name* and an optional list of positional and named arguments.</span></span> <span data-ttu-id="aaf04-161">Les arguments de position (le cas échéant) faites précéder les arguments nommés.</span><span class="sxs-lookup"><span data-stu-id="aaf04-161">The positional arguments (if any) precede the named arguments.</span></span> <span data-ttu-id="aaf04-162">Un argument positionnel se compose d’un *attribute_argument_expression*; un argument nommé se compose d’un nom, suivi par un signe égal, suivi par un *attribute_argument_expression*, qui, ensemble , sont limitées par les mêmes règles que l’assignation simple.</span><span class="sxs-lookup"><span data-stu-id="aaf04-162">A positional argument consists of an *attribute_argument_expression*; a named argument consists of a name, followed by an equal sign, followed by an *attribute_argument_expression*, which, together, are constrained by the same rules as simple assignment.</span></span> <span data-ttu-id="aaf04-163">L’ordre des arguments nommés n’est pas significatif.</span><span class="sxs-lookup"><span data-stu-id="aaf04-163">The order of named arguments is not significant.</span></span>

<span data-ttu-id="aaf04-164">Le *attribute_name* identifie une classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-164">The *attribute_name* identifies an attribute class.</span></span> <span data-ttu-id="aaf04-165">Si la forme de *attribute_name* est *type_name* ensuite ce nom doit faire référence à une classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-165">If the form of *attribute_name* is *type_name* then this name must refer to an attribute class.</span></span> <span data-ttu-id="aaf04-166">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-166">Otherwise, a compile-time error occurs.</span></span> <span data-ttu-id="aaf04-167">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-167">The example</span></span>

```csharp
class Class1 {}

[Class1] class Class2 {}    // Error
```

<span data-ttu-id="aaf04-168">génère une erreur de compilation, car il tente d’utiliser `Class1` en tant qu’attribut de classe lorsque `Class1` n’est pas une classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-168">results in a compile-time error because it attempts to use `Class1` as an attribute class when `Class1` is not an attribute class.</span></span>

<span data-ttu-id="aaf04-169">Certains contextes autorisent la spécification d’un attribut sur plusieurs cibles.</span><span class="sxs-lookup"><span data-stu-id="aaf04-169">Certain contexts permit the specification of an attribute on more than one target.</span></span> <span data-ttu-id="aaf04-170">Un programme peut spécifier explicitement la cible en incluant un *attribute_target_specifier*.</span><span class="sxs-lookup"><span data-stu-id="aaf04-170">A program can explicitly specify the target by including an *attribute_target_specifier*.</span></span> <span data-ttu-id="aaf04-171">Lorsqu’un attribut est placé au niveau global, un *global_attribute_target_specifier* est requis.</span><span class="sxs-lookup"><span data-stu-id="aaf04-171">When an attribute is placed at the global level, a *global_attribute_target_specifier* is required.</span></span> <span data-ttu-id="aaf04-172">Dans tous les autres emplacements, par défaut raisonnable est appliquée, mais un *attribute_target_specifier* peut être utilisé pour confirmer ou remplacer la valeur par défaut dans certains cas ambigus (ou pour simplement confirmer la valeur par défaut dans les cas non ambigus).</span><span class="sxs-lookup"><span data-stu-id="aaf04-172">In all other locations, a reasonable default is applied, but an *attribute_target_specifier* can be used to affirm or override the default in certain ambiguous cases (or to just affirm the default in non-ambiguous cases).</span></span> <span data-ttu-id="aaf04-173">Par conséquent, en règle générale, *attribute_target_specifier*s peut être omis, sauf au niveau global.</span><span class="sxs-lookup"><span data-stu-id="aaf04-173">Thus, typically, *attribute_target_specifier*s can be omitted except at the global level.</span></span> <span data-ttu-id="aaf04-174">Les contextes potentiellement ambigus sont résolus comme suit :</span><span class="sxs-lookup"><span data-stu-id="aaf04-174">The potentially ambiguous contexts are resolved as follows:</span></span>

*  <span data-ttu-id="aaf04-175">Un attribut spécifié avec une portée globale peut s’appliquer à l’assembly cible ou le module cible.</span><span class="sxs-lookup"><span data-stu-id="aaf04-175">An attribute specified at global scope can apply either to the target assembly or the target module.</span></span> <span data-ttu-id="aaf04-176">Sans valeur par défaut existe pour ce contexte, par conséquent, un *attribute_target_specifier* est toujours requis dans ce contexte.</span><span class="sxs-lookup"><span data-stu-id="aaf04-176">No default exists for this context, so an *attribute_target_specifier* is always required in this context.</span></span> <span data-ttu-id="aaf04-177">La présence de la `assembly` *attribute_target_specifier* indique que l’attribut s’applique à la cible assembly ; la présence de la `module` *attribute_target_specifier* Indique que l’attribut s’applique au module cible.</span><span class="sxs-lookup"><span data-stu-id="aaf04-177">The presence of the `assembly` *attribute_target_specifier* indicates that the attribute applies to the target assembly; the presence of the `module` *attribute_target_specifier* indicates that the attribute applies to the target module.</span></span>
*  <span data-ttu-id="aaf04-178">Un attribut spécifié dans une déclaration delegate peut s’appliquer au délégué déclaré ou à sa valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-178">An attribute specified on a delegate declaration can apply either to the delegate being declared or to its return value.</span></span> <span data-ttu-id="aaf04-179">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique au délégué.</span><span class="sxs-lookup"><span data-stu-id="aaf04-179">In the absence of an *attribute_target_specifier*, the attribute applies to the delegate.</span></span> <span data-ttu-id="aaf04-180">La présence de la `type` *attribute_target_specifier* indique que l’attribut s’applique au délégué ; la présence de la `return` *attribute_target_specifier* Indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-180">The presence of the `type` *attribute_target_specifier* indicates that the attribute applies to the delegate; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="aaf04-181">Un attribut spécifié dans une déclaration de méthode peut s’appliquer à la méthode déclarée ou à sa valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-181">An attribute specified on a method declaration can apply either to the method being declared or to its return value.</span></span> <span data-ttu-id="aaf04-182">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode.</span><span class="sxs-lookup"><span data-stu-id="aaf04-182">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="aaf04-183">La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-183">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="aaf04-184">Un attribut spécifié dans une déclaration d’opérateur peut s’appliquer à l’opérateur déclaré ou à sa valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-184">An attribute specified on an operator declaration can apply either to the operator being declared or to its return value.</span></span> <span data-ttu-id="aaf04-185">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="aaf04-185">In the absence of an *attribute_target_specifier*, the attribute applies to the operator.</span></span> <span data-ttu-id="aaf04-186">La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à l’opérateur ; la présence de la `return` *attribute_target_specifier* Indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-186">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the operator; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="aaf04-187">Un attribut spécifié dans une déclaration event qui omet les accesseurs d’événement peut s’appliquent à l’événement déclaré, au champ associé (si l’événement n’est pas abstrait) ou pour l’ajouter associé et supprimer des méthodes.</span><span class="sxs-lookup"><span data-stu-id="aaf04-187">An attribute specified on an event declaration that omits event accessors can apply to the event being declared, to the associated field (if the event is not abstract), or to the associated add and remove methods.</span></span> <span data-ttu-id="aaf04-188">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à l’événement.</span><span class="sxs-lookup"><span data-stu-id="aaf04-188">In the absence of an *attribute_target_specifier*, the attribute applies to the event.</span></span> <span data-ttu-id="aaf04-189">La présence de la `event` *attribute_target_specifier* indique que l’attribut s’applique à l’événement ; la présence de la `field` *attribute_target_specifier* indique que l’attribut s’applique au champ ; et la présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="aaf04-189">The presence of the `event` *attribute_target_specifier* indicates that the attribute applies to the event; the presence of the `field` *attribute_target_specifier* indicates that the attribute applies to the field; and the presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the methods.</span></span>
*  <span data-ttu-id="aaf04-190">Un attribut spécifié dans une déclaration d’accesseur get pour une déclaration de propriété ou un indexeur peut s’appliquer à la méthode associée ou à sa valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-190">An attribute specified on a get accessor declaration for a property or indexer declaration can apply either to the associated method or to its return value.</span></span> <span data-ttu-id="aaf04-191">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode.</span><span class="sxs-lookup"><span data-stu-id="aaf04-191">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="aaf04-192">La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-192">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="aaf04-193">Un attribut spécifié sur un accesseur set pour une déclaration de propriété ou l’indexeur peut s’appliquer à la méthode associée ou à son paramètre unique implicite.</span><span class="sxs-lookup"><span data-stu-id="aaf04-193">An attribute specified on a set accessor for a property or indexer declaration can apply either to the associated method or to its lone implicit parameter.</span></span> <span data-ttu-id="aaf04-194">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode.</span><span class="sxs-lookup"><span data-stu-id="aaf04-194">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="aaf04-195">La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `param` *attribute_target_specifier* indique que l’attribut s’applique au paramètre ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-195">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `param` *attribute_target_specifier* indicates that the attribute applies to the parameter; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="aaf04-196">Un attribut spécifié dans une déclaration d’accesseur add ou remove pour une déclaration d’événement peut s’appliquent à la méthode associée ou à son paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="aaf04-196">An attribute specified on an add or remove accessor declaration for an event declaration can apply either to the associated method or to its lone parameter.</span></span> <span data-ttu-id="aaf04-197">En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode.</span><span class="sxs-lookup"><span data-stu-id="aaf04-197">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="aaf04-198">La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `param` *attribute_target_specifier* indique que l’attribut s’applique au paramètre ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="aaf04-198">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `param` *attribute_target_specifier* indicates that the attribute applies to the parameter; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>

<span data-ttu-id="aaf04-199">Dans d’autres contextes, l’inclusion d’un *attribute_target_specifier* est autorisée mais inutile.</span><span class="sxs-lookup"><span data-stu-id="aaf04-199">In other contexts, inclusion of an *attribute_target_specifier* is permitted but unnecessary.</span></span> <span data-ttu-id="aaf04-200">Par exemple, une déclaration de classe peut inclure ou omettre le spécificateur `type`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-200">For instance, a class declaration may either include or omit the specifier `type`:</span></span>

```csharp
[type: Author("Brian Kernighan")]
class Class1 {}

[Author("Dennis Ritchie")]
class Class2 {}
```

<span data-ttu-id="aaf04-201">C’est une erreur pour spécifier un non valide *attribute_target_specifier*.</span><span class="sxs-lookup"><span data-stu-id="aaf04-201">It is an error to specify an invalid *attribute_target_specifier*.</span></span> <span data-ttu-id="aaf04-202">Par exemple, le spécificateur de `param` ne peut pas être utilisé sur une déclaration de classe :</span><span class="sxs-lookup"><span data-stu-id="aaf04-202">For instance, the specifier `param` cannot be used on a class declaration:</span></span>

```csharp
[param: Author("Brian Kernighan")]        // Error
class Class1 {}
```

<span data-ttu-id="aaf04-203">Par convention, les classes d’attributs sont nommés avec un suffixe de `Attribute`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-203">By convention, attribute classes are named with a suffix of `Attribute`.</span></span> <span data-ttu-id="aaf04-204">Un *attribute_name* du formulaire *type_name* peut inclure ou omettre ce suffixe.</span><span class="sxs-lookup"><span data-stu-id="aaf04-204">An *attribute_name* of the form *type_name* may either include or omit this suffix.</span></span> <span data-ttu-id="aaf04-205">Si une classe d’attributs est trouvée avec et sans ce suffixe, une ambiguïté est présente, et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-205">If an attribute class is found both with and without this suffix, an ambiguity is present, and a compile-time error results.</span></span> <span data-ttu-id="aaf04-206">Si le *attribute_name* est orthographié telles que son plus à droite *identificateur* est un identificateur de chaîne textuelle ([identificateurs](lexical-structure.md#identifiers)), puis uniquement un attribut sans suffixe est mis en correspondance, ce qui permet une ambiguïté de ce type à résoudre.</span><span class="sxs-lookup"><span data-stu-id="aaf04-206">If the *attribute_name* is spelled such that its right-most *identifier* is a verbatim identifier ([Identifiers](lexical-structure.md#identifiers)), then only an attribute without a suffix is matched, thus enabling such an ambiguity to be resolved.</span></span> <span data-ttu-id="aaf04-207">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-207">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class X: Attribute
{}

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Error: ambiguity
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Refers to X
class Class3 {}

[@XAttribute]           // Refers to XAttribute
class Class4 {}
```

<span data-ttu-id="aaf04-208">montre deux classes d’attributs appelées `X` et `XAttribute`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-208">shows two attribute classes named `X` and `XAttribute`.</span></span> <span data-ttu-id="aaf04-209">L’attribut `[X]` est ambigu, car elle peut désigner soit `X` ou `XAttribute`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-209">The attribute `[X]` is ambiguous, since it could refer to either `X` or `XAttribute`.</span></span> <span data-ttu-id="aaf04-210">À l’aide d’un identificateur textuel permet l’intention exacte doit être spécifié dans ce cas rares.</span><span class="sxs-lookup"><span data-stu-id="aaf04-210">Using a verbatim identifier allows the exact intent to be specified in such rare cases.</span></span> <span data-ttu-id="aaf04-211">L’attribut `[XAttribute]` n’est pas ambigu (bien qu’il serait si il y a une classe d’attributs nommée `XAttributeAttribute`!).</span><span class="sxs-lookup"><span data-stu-id="aaf04-211">The attribute `[XAttribute]` is not ambiguous (although it would be if there was an attribute class named `XAttributeAttribute`!).</span></span> <span data-ttu-id="aaf04-212">Si la déclaration de classe `X` est supprimé, les deux attributs font référence à la classe d’attributs nommée `XAttribute`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="aaf04-212">If the declaration for class `X` is removed, then both attributes refer to the attribute class named `XAttribute`, as follows:</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Refers to XAttribute
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Error: no attribute named "X"
class Class3 {}
```

<span data-ttu-id="aaf04-213">Il s’agit d’une erreur de compilation à utiliser une classe d’attribut à usage unique plusieurs fois sur la même entité.</span><span class="sxs-lookup"><span data-stu-id="aaf04-213">It is a compile-time error to use a single-use attribute class more than once on the same entity.</span></span> <span data-ttu-id="aaf04-214">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-214">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpStringAttribute: Attribute
{
    string value;

    public HelpStringAttribute(string value) {
        this.value = value;
    }

    public string Value {
        get {...}
    }
}

[HelpString("Description of Class1")]
[HelpString("Another description of Class1")]
public class Class1 {}
```

<span data-ttu-id="aaf04-215">génère une erreur de compilation, car il tente d’utiliser `HelpString`, qui est une classe d’attribut à usage unique, plusieurs fois sur la déclaration de `Class1`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-215">results in a compile-time error because it attempts to use `HelpString`, which is a single-use attribute class, more than once on the declaration of `Class1`.</span></span>

<span data-ttu-id="aaf04-216">Une expression `E` est un *attribute_argument_expression* si toutes les instructions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="aaf04-216">An expression `E` is an *attribute_argument_expression* if all of the following statements are true:</span></span>

*  <span data-ttu-id="aaf04-217">Le type de `E` est un type de paramètre d’attribut ([les types de paramètre d’attribut](attributes.md#attribute-parameter-types)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-217">The type of `E` is an attribute parameter type ([Attribute parameter types](attributes.md#attribute-parameter-types)).</span></span>
*  <span data-ttu-id="aaf04-218">Au moment de la compilation, la valeur de `E` peut être résolu à une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaf04-218">At compile-time, the value of `E` can be resolved to one of the following:</span></span>
   * <span data-ttu-id="aaf04-219">Une valeur constante.</span><span class="sxs-lookup"><span data-stu-id="aaf04-219">A constant value.</span></span>
   * <span data-ttu-id="aaf04-220">Objet `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-220">A `System.Type` object.</span></span>
   * <span data-ttu-id="aaf04-221">Un tableau unidimensionnel de *attribute_argument_expression*s.</span><span class="sxs-lookup"><span data-stu-id="aaf04-221">A one-dimensional array of *attribute_argument_expression*s.</span></span>

<span data-ttu-id="aaf04-222">Exemple :</span><span class="sxs-lookup"><span data-stu-id="aaf04-222">For example:</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class TestAttribute: Attribute
{
    public int P1 {
        get {...}
        set {...}
    }

    public Type P2 {
        get {...}
        set {...}
    }

    public object P3 {
        get {...}
        set {...}
    }
}

[Test(P1 = 1234, P3 = new int[] {1, 3, 5}, P2 = typeof(float))]
class MyClass {}
```

<span data-ttu-id="aaf04-223">Un *typeof_expression* ([l’opérateur typeof](expressions.md#the-typeof-operator)) utilisée comme une expression d’argument attribut peut référencer un type non générique, un type construit fermé ou un type générique indépendant, mais il ne peut pas référencer un Ouvrez le type.</span><span class="sxs-lookup"><span data-stu-id="aaf04-223">A *typeof_expression* ([The typeof operator](expressions.md#the-typeof-operator)) used as an attribute argument expression can reference a non-generic type, a closed constructed type, or an unbound generic type, but it cannot reference an open type.</span></span> <span data-ttu-id="aaf04-224">Il s’agit pour vous assurer que l’expression peut être résolue au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-224">This is to ensure that the expression can be resolved at compile-time.</span></span>

```csharp
class A: Attribute
{
    public A(Type t) {...}
}

class G<T>
{
    [A(typeof(T))] T t;                  // Error, open type in attribute
}

class X
{
    [A(typeof(List<int>))] int x;        // Ok, closed constructed type
    [A(typeof(List<>))] int y;           // Ok, unbound generic type
}
```

## <a name="attribute-instances"></a><span data-ttu-id="aaf04-225">Instances d’attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-225">Attribute instances</span></span>

<span data-ttu-id="aaf04-226">Un ***instance de l’attribut*** est une instance qui représente un attribut au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="aaf04-226">An ***attribute instance*** is an instance that represents an attribute at run-time.</span></span> <span data-ttu-id="aaf04-227">Un attribut est défini avec une classe d’attributs, des arguments positionnels et des arguments nommé.</span><span class="sxs-lookup"><span data-stu-id="aaf04-227">An attribute is defined with an attribute class, positional arguments, and named arguments.</span></span> <span data-ttu-id="aaf04-228">Une instance d’attribut est une instance de la classe d’attributs qui est initialisée avec les arguments nommés et positionnels.</span><span class="sxs-lookup"><span data-stu-id="aaf04-228">An attribute instance is an instance of the attribute class that is initialized with the positional and named arguments.</span></span>

<span data-ttu-id="aaf04-229">Récupération d’une instance d’attribut implique un traitement de compilation et d’exécution, comme décrit dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="aaf04-229">Retrieval of an attribute instance involves both compile-time and run-time processing, as described in the following sections.</span></span>

### <a name="compilation-of-an-attribute"></a><span data-ttu-id="aaf04-230">Compilation d’un attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-230">Compilation of an attribute</span></span>

<span data-ttu-id="aaf04-231">La compilation d’un *attribut* avec la classe d’attributs `T`, *positional_argument_list* `P` et *named_argument_list* `N`, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaf04-231">The compilation of an *attribute* with attribute class `T`, *positional_argument_list* `P` and *named_argument_list* `N`, consists of the following steps:</span></span>

*  <span data-ttu-id="aaf04-232">Suivez les étapes de traitement de compilation pour compiler une *object_creation_expression* du formulaire `new T(P)`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-232">Follow the compile-time processing steps for compiling an *object_creation_expression* of the form `new T(P)`.</span></span> <span data-ttu-id="aaf04-233">Ces étapes entraînent une erreur de compilation, ou déterminer un constructeur d’instance `C` sur `T` qui peut être appelé au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="aaf04-233">These steps either result in a compile-time error, or determine an instance constructor `C` on `T` that can be invoked at run-time.</span></span>
*  <span data-ttu-id="aaf04-234">Si `C` n’a pas d’une accessibilité publique, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-234">If `C` does not have public accessibility, then a compile-time error occurs.</span></span>
*  <span data-ttu-id="aaf04-235">Pour chaque *argument_nommé* `Arg` dans `N`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-235">For each *named_argument* `Arg` in `N`:</span></span>
   * <span data-ttu-id="aaf04-236">Laisser `Name` être le *identificateur* de la *argument_nommé* `Arg`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-236">Let `Name` be the *identifier* of the *named_argument* `Arg`.</span></span>
   * <span data-ttu-id="aaf04-237">`Name` doit identifier un champ de public en lecture-écriture non statique ou une propriété sur `T`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-237">`Name` must identify a non-static read-write public field or property on `T`.</span></span> <span data-ttu-id="aaf04-238">Si `T` a pas de champ ou une propriété, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-238">If `T` has no such field or property, then a compile-time error occurs.</span></span>
*  <span data-ttu-id="aaf04-239">Gardez les informations suivantes pour l’instanciation de l’exécution de l’attribut : la classe d’attributs `T`, le constructeur d’instance `C` sur `T`, le *positional_argument_list* `P` et le *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-239">Keep the following information for run-time instantiation of the attribute: the attribute class `T`, the instance constructor `C` on `T`, the *positional_argument_list* `P` and the *named_argument_list* `N`.</span></span>

### <a name="run-time-retrieval-of-an-attribute-instance"></a><span data-ttu-id="aaf04-240">Récupération de l’exécution d’une instance d’attribut</span><span class="sxs-lookup"><span data-stu-id="aaf04-240">Run-time retrieval of an attribute instance</span></span>

<span data-ttu-id="aaf04-241">Compilation d’un *attribut* génère une classe d’attributs `T`, un constructeur d’instance `C` sur `T`, un *positional_argument_list* `P`et un *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-241">Compilation of an *attribute* yields an attribute class `T`, an instance constructor `C` on `T`, a *positional_argument_list* `P`, and a *named_argument_list* `N`.</span></span> <span data-ttu-id="aaf04-242">Par conséquent, une instance d’attribut peut être récupérée au moment de l’exécution en procédant comme suit :</span><span class="sxs-lookup"><span data-stu-id="aaf04-242">Given this information, an attribute instance can be retrieved at run-time using the following steps:</span></span>

*  <span data-ttu-id="aaf04-243">Suivez les étapes de traitement de l’exécution pour exécuter un *object_creation_expression* du formulaire `new T(P)`, en utilisant le constructeur d’instance `C` comme déterminé au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-243">Follow the run-time processing steps for executing an *object_creation_expression* of the form `new T(P)`, using the instance constructor `C` as determined at compile-time.</span></span> <span data-ttu-id="aaf04-244">Ces étapes génèrent une exception, ou produisent une instance `O` de `T`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-244">These steps either result in an exception, or produce an instance `O` of `T`.</span></span>
*  <span data-ttu-id="aaf04-245">Pour chaque *argument_nommé* `Arg` dans `N`, dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="aaf04-245">For each *named_argument* `Arg` in `N`, in order:</span></span>
   * <span data-ttu-id="aaf04-246">Laisser `Name` être le *identificateur* de la *argument_nommé* `Arg`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-246">Let `Name` be the *identifier* of the *named_argument* `Arg`.</span></span> <span data-ttu-id="aaf04-247">Si `Name` n’identifie pas un champ de lecture-écriture non statique publique ou une propriété sur `O`, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="aaf04-247">If `Name` does not identify a non-static public read-write field or property on `O`, then an exception is thrown.</span></span>
   * <span data-ttu-id="aaf04-248">Laisser `Value` être le résultat de l’évaluation de la *attribute_argument_expression* de `Arg`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-248">Let `Value` be the result of evaluating the *attribute_argument_expression* of `Arg`.</span></span>
   * <span data-ttu-id="aaf04-249">Si `Name` identifie un champ sur `O`, puis définissez ce champ sur `Value`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-249">If `Name` identifies a field on `O`, then set this field to `Value`.</span></span>
   * <span data-ttu-id="aaf04-250">Sinon, `Name` identifie une propriété sur `O`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-250">Otherwise, `Name` identifies a property on `O`.</span></span> <span data-ttu-id="aaf04-251">Définissez cette propriété sur `Value`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-251">Set this property to `Value`.</span></span>
   * <span data-ttu-id="aaf04-252">Le résultat est `O`, une instance de la classe d’attributs `T` qui a été initialisée avec le *positional_argument_list* `P` et *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-252">The result is `O`, an instance of the attribute class `T` that has been initialized with the *positional_argument_list* `P` and the *named_argument_list* `N`.</span></span>

## <a name="reserved-attributes"></a><span data-ttu-id="aaf04-253">Attributs réservés</span><span class="sxs-lookup"><span data-stu-id="aaf04-253">Reserved attributes</span></span>

<span data-ttu-id="aaf04-254">Un petit nombre d’attributs affecte le langage d’une certaine façon.</span><span class="sxs-lookup"><span data-stu-id="aaf04-254">A small number of attributes affect the language in some way.</span></span> <span data-ttu-id="aaf04-255">Ces attributs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaf04-255">These attributes include:</span></span>

*  <span data-ttu-id="aaf04-256">`System.AttributeUsageAttribute` ([Attribut AttributeUsage le](attributes.md#the-attributeusage-attribute)), qui est utilisé pour décrire les méthodes dans lesquels une classe d’attributs peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="aaf04-256">`System.AttributeUsageAttribute` ([The AttributeUsage attribute](attributes.md#the-attributeusage-attribute)), which is used to describe the ways in which an attribute class can be used.</span></span>
*  <span data-ttu-id="aaf04-257">`System.Diagnostics.ConditionalAttribute` ([L’attribut Conditional](attributes.md#the-conditional-attribute)), qui est utilisée pour définir des méthodes conditionnelles.</span><span class="sxs-lookup"><span data-stu-id="aaf04-257">`System.Diagnostics.ConditionalAttribute` ([The Conditional attribute](attributes.md#the-conditional-attribute)), which is used to define conditional methods.</span></span>
*  <span data-ttu-id="aaf04-258">`System.ObsoleteAttribute` ([Attribut obsolète le](attributes.md#the-obsolete-attribute)), qui est utilisé pour marquer un membre comme étant obsolète.</span><span class="sxs-lookup"><span data-stu-id="aaf04-258">`System.ObsoleteAttribute` ([The Obsolete attribute](attributes.md#the-obsolete-attribute)), which is used to mark a member as obsolete.</span></span>
*  <span data-ttu-id="aaf04-259">`System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` et `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([attributs d’informations appelant](attributes.md#caller-info-attributes)), qui sont utilisées pour fournir des informations sur le contexte d’appel aux paramètres facultatifs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-259">`System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` and `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([Caller info attributes](attributes.md#caller-info-attributes)), which are used to supply information about the calling context to optional parameters.</span></span>

### <a name="the-attributeusage-attribute"></a><span data-ttu-id="aaf04-260">L’attribut AttributeUsage</span><span class="sxs-lookup"><span data-stu-id="aaf04-260">The AttributeUsage attribute</span></span>

<span data-ttu-id="aaf04-261">L’attribut `AttributeUsage` est utilisé pour décrire le mode dans lequel la classe d’attributs peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="aaf04-261">The attribute `AttributeUsage` is used to describe the manner in which the attribute class can be used.</span></span>

<span data-ttu-id="aaf04-262">Une classe est décorée avec le `AttributeUsage` attribut doit dériver de `System.Attribute`, directement ou indirectement.</span><span class="sxs-lookup"><span data-stu-id="aaf04-262">A class that is decorated with the `AttributeUsage` attribute must derive from `System.Attribute`, either directly or indirectly.</span></span> <span data-ttu-id="aaf04-263">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-263">Otherwise, a compile-time error occurs.</span></span>

```csharp
namespace System
{
    [AttributeUsage(AttributeTargets.Class)]
    public class AttributeUsageAttribute: Attribute
    {
        public AttributeUsageAttribute(AttributeTargets validOn) {...}
        public virtual bool AllowMultiple { get {...} set {...} }
        public virtual bool Inherited { get {...} set {...} }
        public virtual AttributeTargets ValidOn { get {...} }
    }

    public enum AttributeTargets
    {
        Assembly     = 0x0001,
        Module       = 0x0002,
        Class        = 0x0004,
        Struct       = 0x0008,
        Enum         = 0x0010,
        Constructor  = 0x0020,
        Method       = 0x0040,
        Property     = 0x0080,
        Field        = 0x0100,
        Event        = 0x0200,
        Interface    = 0x0400,
        Parameter    = 0x0800,
        Delegate     = 0x1000,
        ReturnValue  = 0x2000,

        All = Assembly | Module | Class | Struct | Enum | Constructor | 
            Method | Property | Field | Event | Interface | Parameter | 
            Delegate | ReturnValue
    }
}
```

### <a name="the-conditional-attribute"></a><span data-ttu-id="aaf04-264">L’attribut Conditional</span><span class="sxs-lookup"><span data-stu-id="aaf04-264">The Conditional attribute</span></span>

<span data-ttu-id="aaf04-265">L’attribut `Conditional` permet la définition de ***des méthodes conditionnelles*** et ***classes d’attributs conditionnel***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-265">The attribute `Conditional` enables the definition of ***conditional methods*** and ***conditional attribute classes***.</span></span>

```csharp
namespace System.Diagnostics
{
    [AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, AllowMultiple = true)]
    public class ConditionalAttribute: Attribute
    {
        public ConditionalAttribute(string conditionString) {...}
        public string ConditionString { get {...} }
    }
}
```

#### <a name="conditional-methods"></a><span data-ttu-id="aaf04-266">Méthodes conditionnelles</span><span class="sxs-lookup"><span data-stu-id="aaf04-266">Conditional methods</span></span>

<span data-ttu-id="aaf04-267">Une méthode décorée avec le `Conditional` attribut est une méthode conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="aaf04-267">A method decorated with the `Conditional` attribute is a conditional method.</span></span> <span data-ttu-id="aaf04-268">Le `Conditional` attribut indique une condition en testant un symbole de compilation conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="aaf04-268">The `Conditional` attribute indicates a condition by testing a conditional compilation symbol.</span></span> <span data-ttu-id="aaf04-269">Les appels à une méthode conditionnelle sont inclus ou omis selon que ce symbole est défini au moment de l’appel.</span><span class="sxs-lookup"><span data-stu-id="aaf04-269">Calls to a conditional method are either included or omitted depending on whether this symbol is defined at the point of the call.</span></span> <span data-ttu-id="aaf04-270">Si le symbole est défini, l’appel est inclus ; Sinon, l’appel (y compris l’évaluation du récepteur et les paramètres de l’appel) est omis.</span><span class="sxs-lookup"><span data-stu-id="aaf04-270">If the symbol is defined, the call is included; otherwise, the call (including evaluation of the receiver and parameters of the call) is omitted.</span></span>

<span data-ttu-id="aaf04-271">Une méthode conditionnelle est soumise aux restrictions suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaf04-271">A conditional method is subject to the following restrictions:</span></span>

*  <span data-ttu-id="aaf04-272">La méthode conditionnelle doit être une méthode dans un *class_declaration* ou *struct_declaration*.</span><span class="sxs-lookup"><span data-stu-id="aaf04-272">The conditional method must be a method in a *class_declaration* or *struct_declaration*.</span></span> <span data-ttu-id="aaf04-273">Une erreur de compilation se produit si le `Conditional` attribut est spécifié sur une méthode dans une déclaration d’interface.</span><span class="sxs-lookup"><span data-stu-id="aaf04-273">A compile-time error occurs if the `Conditional` attribute is specified on a method in an interface declaration.</span></span>
*  <span data-ttu-id="aaf04-274">La méthode conditionnelle doit avoir un type de retour `void`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-274">The conditional method must have a return type of `void`.</span></span>
*  <span data-ttu-id="aaf04-275">La méthode conditionnelle ne doit pas être marquée avec le `override` modificateur.</span><span class="sxs-lookup"><span data-stu-id="aaf04-275">The conditional method must not be marked with the `override` modifier.</span></span> <span data-ttu-id="aaf04-276">Une méthode conditionnelle peut être marquée avec le `virtual` modificateur, toutefois.</span><span class="sxs-lookup"><span data-stu-id="aaf04-276">A conditional method may be marked with the `virtual` modifier, however.</span></span> <span data-ttu-id="aaf04-277">Remplacements d’une telle méthode sont implicitement conditionnelles et ne doivent pas être marqués explicitement avec une `Conditional` attribut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-277">Overrides of such a method are implicitly conditional, and must not be explicitly marked with a `Conditional` attribute.</span></span>
*  <span data-ttu-id="aaf04-278">La méthode conditionnelle ne doit pas être une implémentation d’une méthode d’interface.</span><span class="sxs-lookup"><span data-stu-id="aaf04-278">The conditional method must not be an implementation of an interface method.</span></span> <span data-ttu-id="aaf04-279">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="aaf04-279">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="aaf04-280">En outre, une erreur de compilation se produit si une méthode conditionnelle est utilisée dans un *delegate_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="aaf04-280">In addition, a compile-time error occurs if a conditional method is used in a *delegate_creation_expression*.</span></span> <span data-ttu-id="aaf04-281">L’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-281">The example</span></span>

```csharp
#define DEBUG

using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void M() {
        Console.WriteLine("Executed Class1.M");
    }
}

class Class2
{
    public static void Test() {
        Class1.M();
    }
}
```

<span data-ttu-id="aaf04-282">déclare `Class1.M` comme une méthode conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="aaf04-282">declares `Class1.M` as a conditional method.</span></span> <span data-ttu-id="aaf04-283">`Class2`de `Test` méthode appelle cette méthode.</span><span class="sxs-lookup"><span data-stu-id="aaf04-283">`Class2`'s `Test` method calls this method.</span></span> <span data-ttu-id="aaf04-284">Depuis le symbole de compilation conditionnelle `DEBUG` est défini, si `Class2.Test` est appelé, il appellera `M`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-284">Since the conditional compilation symbol `DEBUG` is defined, if `Class2.Test` is called, it will call `M`.</span></span> <span data-ttu-id="aaf04-285">Si le symbole `DEBUG` n’était pas défini, puis `Class2.Test` n’appelez pas `Class1.M`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-285">If the symbol `DEBUG` had not been defined, then `Class2.Test` would not call `Class1.M`.</span></span>

<span data-ttu-id="aaf04-286">Il est important de noter que l’inclusion ou exclusion d’un appel à une méthode conditionnelle est contrôlée par les symboles de compilation conditionnelle au moment de l’appel.</span><span class="sxs-lookup"><span data-stu-id="aaf04-286">It is important to note that the inclusion or exclusion of a call to a conditional method is controlled by the conditional compilation symbols at the point of the call.</span></span> <span data-ttu-id="aaf04-287">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-287">In the example</span></span>

<span data-ttu-id="aaf04-288">Fichier `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-288">File `class1.cs`:</span></span>

```csharp
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void F() {
        Console.WriteLine("Executed Class1.F");
    }
}
```

<span data-ttu-id="aaf04-289">Fichier `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-289">File `class2.cs`:</span></span>

```csharp
#define DEBUG

class Class2
{
    public static void G() {
        Class1.F();                // F is called
    }
}
```

<span data-ttu-id="aaf04-290">Fichier `class3.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-290">File `class3.cs`:</span></span>

```csharp
#undef DEBUG

class Class3
{
    public static void H() {
        Class1.F();                // F is not called
    }
}
```

<span data-ttu-id="aaf04-291">les classes `Class2` et `Class3` contiennent chacune des appels à la méthode conditionnelle `Class1.F`, qui est conditionnelle selon s’il faut ou non `DEBUG` est défini.</span><span class="sxs-lookup"><span data-stu-id="aaf04-291">the classes `Class2` and `Class3` each contain calls to the conditional method `Class1.F`, which is conditional based on whether or not `DEBUG` is defined.</span></span> <span data-ttu-id="aaf04-292">Dans la mesure où ce symbole est défini dans le contexte de `Class2` mais pas `Class3`, l’appel à `F` dans `Class2` est inclus, tandis que l’appel à `F` dans `Class3` est omis.</span><span class="sxs-lookup"><span data-stu-id="aaf04-292">Since this symbol is defined in the context of `Class2` but not `Class3`, the call to `F` in `Class2` is included, while the call to `F` in `Class3` is omitted.</span></span>

<span data-ttu-id="aaf04-293">L’utilisation des méthodes conditionnelles dans une chaîne d’héritage peut prêter à confuse.</span><span class="sxs-lookup"><span data-stu-id="aaf04-293">The use of conditional methods in an inheritance chain can be confusing.</span></span> <span data-ttu-id="aaf04-294">Appels à une méthode conditionnelle via `base`, sous la forme `base.M`, sont soumis aux règles d’appel de méthode conditionnelle normale.</span><span class="sxs-lookup"><span data-stu-id="aaf04-294">Calls made to a conditional method through `base`, of the form `base.M`, are subject to the normal conditional method call rules.</span></span> <span data-ttu-id="aaf04-295">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-295">In the example</span></span>

<span data-ttu-id="aaf04-296">Fichier `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-296">File `class1.cs`:</span></span>

```csharp
using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public virtual void M() {
        Console.WriteLine("Class1.M executed");
    }
}
```

<span data-ttu-id="aaf04-297">Fichier `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-297">File `class2.cs`:</span></span>

```csharp
using System;

class Class2: Class1
{
    public override void M() {
        Console.WriteLine("Class2.M executed");
        base.M();                        // base.M is not called!
    }
}
```

<span data-ttu-id="aaf04-298">Fichier `class3.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-298">File `class3.cs`:</span></span>

```csharp
#define DEBUG

using System;

class Class3
{
    public static void Test() {
        Class2 c = new Class2();
        c.M();                            // M is called
    }
}
```

<span data-ttu-id="aaf04-299">`Class2` inclut un appel à la `M` défini dans sa classe de base.</span><span class="sxs-lookup"><span data-stu-id="aaf04-299">`Class2` includes a call to the `M` defined in its base class.</span></span> <span data-ttu-id="aaf04-300">Cet appel est omis, car la méthode de base est conditionnelle basée sur la présence du symbole `DEBUG`, ce qui n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="aaf04-300">This call is omitted because the base method is conditional based on the presence of the symbol `DEBUG`, which is undefined.</span></span> <span data-ttu-id="aaf04-301">Par conséquent, la méthode écrit dans la console «`Class2.M executed`» uniquement.</span><span class="sxs-lookup"><span data-stu-id="aaf04-301">Thus, the method writes to the console "`Class2.M executed`" only.</span></span> <span data-ttu-id="aaf04-302">Utilisation judicieuse de *pp_declaration*s peut éliminer ces problèmes.</span><span class="sxs-lookup"><span data-stu-id="aaf04-302">Judicious use of *pp_declaration*s can eliminate such problems.</span></span>

#### <a name="conditional-attribute-classes"></a><span data-ttu-id="aaf04-303">Classes de l’attribut Conditional</span><span class="sxs-lookup"><span data-stu-id="aaf04-303">Conditional attribute classes</span></span>

<span data-ttu-id="aaf04-304">Une classe d’attributs ([classes d’attributs](attributes.md#attribute-classes)) décoré avec un ou plusieurs `Conditional` attributs est un ***classe de l’attribut conditional***.</span><span class="sxs-lookup"><span data-stu-id="aaf04-304">An attribute class ([Attribute classes](attributes.md#attribute-classes)) decorated with one or more `Conditional` attributes is a ***conditional attribute class***.</span></span> <span data-ttu-id="aaf04-305">Une classe de l’attribut conditional est ainsi associée avec les symboles de compilation conditionnelle déclarés dans son `Conditional` attributs.</span><span class="sxs-lookup"><span data-stu-id="aaf04-305">A conditional attribute class is thus associated with the conditional compilation symbols declared in its `Conditional` attributes.</span></span> <span data-ttu-id="aaf04-306">Cet exemple :</span><span class="sxs-lookup"><span data-stu-id="aaf04-306">This example:</span></span>

```csharp
using System;
using System.Diagnostics;
[Conditional("ALPHA")]
[Conditional("BETA")]
public class TestAttribute : Attribute {}
```

<span data-ttu-id="aaf04-307">déclare `TestAttribute` comme une classe d’attribut conditional associée les symboles de compilations conditionnelles `ALPHA` et `BETA`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-307">declares `TestAttribute` as a conditional attribute class associated with the conditional compilations symbols `ALPHA` and `BETA`.</span></span>

<span data-ttu-id="aaf04-308">Spécifications d’attribut ([spécification des attributs](attributes.md#attribute-specification)) d’un attribut conditionnel sont incluses si un ou plusieurs de ses symboles de compilation conditionnelle associé sont défini au moment de la spécification, sinon l’attribut spécification est omise.</span><span class="sxs-lookup"><span data-stu-id="aaf04-308">Attribute specifications ([Attribute specification](attributes.md#attribute-specification)) of a conditional attribute are included if one or more of its associated conditional compilation symbols is defined at the point of specification, otherwise the attribute specification is omitted.</span></span>

<span data-ttu-id="aaf04-309">Il est important de noter que l’inclusion ou l’exclusion d’une spécification d’attribut d’une classe de l’attribut conditional est contrôlée par les symboles de compilation conditionnelle au moment de la spécification.</span><span class="sxs-lookup"><span data-stu-id="aaf04-309">It is important to note that the inclusion or exclusion of an attribute specification of a conditional attribute class is controlled by the conditional compilation symbols at the point of the specification.</span></span> <span data-ttu-id="aaf04-310">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-310">In the example</span></span>

<span data-ttu-id="aaf04-311">Fichier `test.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-311">File `test.cs`:</span></span>

```csharp
using System;
using System.Diagnostics;

[Conditional("DEBUG")]

public class TestAttribute : Attribute {}
```

<span data-ttu-id="aaf04-312">Fichier `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-312">File `class1.cs`:</span></span>

```csharp
#define DEBUG

[Test]                // TestAttribute is specified

class Class1 {}
```

<span data-ttu-id="aaf04-313">Fichier `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="aaf04-313">File `class2.cs`:</span></span>

```csharp
#undef DEBUG

[Test]                 // TestAttribute is not specified

class Class2 {}
```

<span data-ttu-id="aaf04-314">les classes `Class1` et `Class2` sont chacun décorée avec l’attribut `Test`, qui est conditionnelle selon s’il faut ou non `DEBUG` est défini.</span><span class="sxs-lookup"><span data-stu-id="aaf04-314">the classes `Class1` and `Class2` are each decorated with attribute `Test`, which is conditional based on whether or not `DEBUG` is defined.</span></span> <span data-ttu-id="aaf04-315">Dans la mesure où ce symbole est défini dans le contexte de `Class1` mais pas `Class2`, la spécification de la `Test` attribut sur `Class1` est inclus lors de la spécification de la `Test` attribut sur `Class2` est omis.</span><span class="sxs-lookup"><span data-stu-id="aaf04-315">Since this symbol is defined in the context of `Class1` but not `Class2`, the specification of the `Test` attribute on `Class1` is included, while the specification of the `Test` attribute on `Class2` is omitted.</span></span>

### <a name="the-obsolete-attribute"></a><span data-ttu-id="aaf04-316">L’attribut Obsolete</span><span class="sxs-lookup"><span data-stu-id="aaf04-316">The Obsolete attribute</span></span>

<span data-ttu-id="aaf04-317">L’attribut `Obsolete` est utilisé pour marquer des types et membres de types qui ne doivent plus être utilisés.</span><span class="sxs-lookup"><span data-stu-id="aaf04-317">The attribute `Obsolete` is used to mark types and members of types that should no longer be used.</span></span>

```csharp
namespace System
{
    [AttributeUsage(
        AttributeTargets.Class | 
        AttributeTargets.Struct |
        AttributeTargets.Enum | 
        AttributeTargets.Interface | 
        AttributeTargets.Delegate |
        AttributeTargets.Method | 
        AttributeTargets.Constructor |
        AttributeTargets.Property | 
        AttributeTargets.Field |
        AttributeTargets.Event,
        Inherited = false)
    ]
    public class ObsoleteAttribute: Attribute
    {
        public ObsoleteAttribute() {...}
        public ObsoleteAttribute(string message) {...}
        public ObsoleteAttribute(string message, bool error) {...}
        public string Message { get {...} }
        public bool IsError { get {...} }
    }
}
```

<span data-ttu-id="aaf04-318">Si un programme utilise un type ou membre qui est décorée avec le `Obsolete` attribut, le compilateur émet un avertissement ou une erreur.</span><span class="sxs-lookup"><span data-stu-id="aaf04-318">If a program uses a type or member that is decorated with the `Obsolete` attribute, the compiler issues a warning or an error.</span></span> <span data-ttu-id="aaf04-319">Plus précisément, le compilateur émet un avertissement si aucun paramètre d’erreur n’est fournie, ou si le paramètre d’erreur est fourni et qu’il a la valeur `false`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-319">Specifically, the compiler issues a warning if no error parameter is provided, or if the error parameter is provided and has the value `false`.</span></span> <span data-ttu-id="aaf04-320">Le compilateur émet une erreur si le paramètre d’erreur est spécifié et a la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-320">The compiler issues an error if the error parameter is specified and has the value `true`.</span></span>

<span data-ttu-id="aaf04-321">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="aaf04-321">In the example</span></span>

```csharp
[Obsolete("This class is obsolete; use class B instead")]
class A
{
    public void F() {}
}

class B
{
    public void F() {}
}

class Test
{
    static void Main() {
        A a = new A();         // Warning
        a.F();
    }
}
```

<span data-ttu-id="aaf04-322">la classe `A` est décorée avec le `Obsolete` attribut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-322">the class `A` is decorated with the `Obsolete` attribute.</span></span> <span data-ttu-id="aaf04-323">L’utilisation de `A` dans `Main` entraîne un avertissement qui inclut le message spécifié, « cette classe est obsolète ; Utilisez la classe B à la place. »</span><span class="sxs-lookup"><span data-stu-id="aaf04-323">Each use of `A` in `Main` results in a warning that includes the specified message, "This class is obsolete; use class B instead."</span></span>

### <a name="caller-info-attributes"></a><span data-ttu-id="aaf04-324">Attributs d’informations de l’appelant</span><span class="sxs-lookup"><span data-stu-id="aaf04-324">Caller info attributes</span></span>

<span data-ttu-id="aaf04-325">À des fins telles que la journalisation et création de rapports, il est parfois utile pour un membre de fonction obtenir certaines informations de compilation sur le code appelant.</span><span class="sxs-lookup"><span data-stu-id="aaf04-325">For purposes such as logging and reporting, it is sometimes useful for a function member to obtain certain compile-time information about the calling code.</span></span> <span data-ttu-id="aaf04-326">Les attributs d’informations appelant fournissent un moyen de passer ces informations en toute transparence.</span><span class="sxs-lookup"><span data-stu-id="aaf04-326">The caller info attributes provide a way to pass such information transparently.</span></span>

<span data-ttu-id="aaf04-327">Lorsqu’un paramètre facultatif est annoté avec l’un des attributs d’informations de l’appelant, en omettant l’argument correspondant dans un appel ne provoquant pas forcément la valeur du paramètre par défaut à substituer.</span><span class="sxs-lookup"><span data-stu-id="aaf04-327">When an optional parameter is annotated with one of the caller info attributes, omitting the corresponding argument in a call does not necessarily cause the default parameter value to be substituted.</span></span> <span data-ttu-id="aaf04-328">Au lieu de cela, si l’information spécifiée concernant le contexte d’appel est disponible, ces informations seront transmies en tant que la valeur d’argument.</span><span class="sxs-lookup"><span data-stu-id="aaf04-328">Instead, if the specified information about the calling context is available, that information will be passed as the argument value.</span></span>

<span data-ttu-id="aaf04-329">Exemple :</span><span class="sxs-lookup"><span data-stu-id="aaf04-329">For example:</span></span>

```csharp
using System.Runtime.CompilerServices

...

public void Log(
    [CallerLineNumber] int line = -1,
    [CallerFilePath]   string path = null,
    [CallerMemberName] string name = null
)
{
    Console.WriteLine((line < 0) ? "No line" : "Line "+ line);
    Console.WriteLine((path == null) ? "No file path" : path);
    Console.WriteLine((name == null) ? "No member name" : name);
}
```

<span data-ttu-id="aaf04-330">Un appel à `Log()` sans arguments affiche le chemin de nombre et le fichier de ligne de l’appel, ainsi que le nom du membre dans lequel l’appel s’est produite.</span><span class="sxs-lookup"><span data-stu-id="aaf04-330">A call to `Log()` with no arguments would print the line number and file path of the call, as well as the name of the member within which the call occurred.</span></span>

<span data-ttu-id="aaf04-331">Attributs d’informations de l’appelant peuvent se produire sur n’importe où, les paramètres facultatifs, y compris dans les déclarations de délégué.</span><span class="sxs-lookup"><span data-stu-id="aaf04-331">Caller info attributes can occur on optional parameters anywhere, including in delegate declarations.</span></span> <span data-ttu-id="aaf04-332">Toutefois, les attributs d’informations appelant spécifique ont les restrictions sur les types des paramètres, qu'ils peuvent l’attribut, afin qu’il y aura toujours une conversion implicite d’une valeur substituée au type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="aaf04-332">However, the specific caller info attributes have restrictions on the types of the parameters they can attribute, so that there will always be an implicit conversion from a substituted value to the parameter type.</span></span>

<span data-ttu-id="aaf04-333">Il est une erreur d’avoir le même attribut d’informations de l’appelant sur un paramètre de la définition et de mise en œuvre de la partie d’une déclaration de méthode partielle.</span><span class="sxs-lookup"><span data-stu-id="aaf04-333">It is an error to have the same caller info attribute on a parameter of both the defining and implementing part of a partial method declaration.</span></span> <span data-ttu-id="aaf04-334">Uniquement les attributs d’informations de l’appelant dans la partie de définition sont appliquées, tandis que les attributs des informations de l’appelant qui se produisent uniquement dans la partie implémentation sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="aaf04-334">Only caller info attributes in the defining part are applied, whereas caller info attributes occurring only in the implementing part are ignored.</span></span>

<span data-ttu-id="aaf04-335">Informations de l’appelant n’affectent pas la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="aaf04-335">Caller information does not affect overload resolution.</span></span> <span data-ttu-id="aaf04-336">Comme les paramètres facultatifs avec attributs sont toujours omises dans le code source de l’appelant, la résolution de surcharge ignore ces paramètres de la même façon qu’il ignore les autres paramètres facultatifs omis ([la résolution de surcharge](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-336">As the attributed optional parameters are still omitted from the source code of the caller, overload resolution ignores those parameters in the same way it ignores other omitted optional parameters ([Overload resolution](expressions.md#overload-resolution)).</span></span>

<span data-ttu-id="aaf04-337">Informations de l’appelant sont remplacées uniquement lorsqu’une fonction est appelée explicitement dans le code source.</span><span class="sxs-lookup"><span data-stu-id="aaf04-337">Caller information is only substituted when a function is explicitly invoked in source code.</span></span> <span data-ttu-id="aaf04-338">Appels implicites tels que les appels de constructeur implicite parent n’ont pas d’un emplacement source et ne remplacera pas les informations de l’appelant.</span><span class="sxs-lookup"><span data-stu-id="aaf04-338">Implicit invocations such as implicit parent constructor calls do not have a source location and will not substitute caller information.</span></span> <span data-ttu-id="aaf04-339">En outre, les appels qui sont liées dynamiquement remplacera pas les informations de l’appelant.</span><span class="sxs-lookup"><span data-stu-id="aaf04-339">Also, calls that are dynamically bound will not substitute caller information.</span></span> <span data-ttu-id="aaf04-340">Quand une info appelant paramètre avec attributs est omis dans ce cas, la valeur du paramètre est utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="aaf04-340">When a caller info attributed parameter is omitted in such cases, the specified default value of the parameter is used instead.</span></span>

<span data-ttu-id="aaf04-341">Seule exception concerne les expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="aaf04-341">One exception is query-expressions.</span></span> <span data-ttu-id="aaf04-342">Celles-ci sont considérées comme des expansions syntaxiques, et si les appels qui se développent pour omettre les paramètres facultatifs avec attributs d’informations de l’appelant, les informations de l’appelant seront remplacées.</span><span class="sxs-lookup"><span data-stu-id="aaf04-342">These are considered syntactic expansions, and if the calls they expand to omit optional parameters with caller info attributes, caller information will be substituted.</span></span> <span data-ttu-id="aaf04-343">L’emplacement utilisé est l’emplacement de la clause de requête généré à partir de l’appel.</span><span class="sxs-lookup"><span data-stu-id="aaf04-343">The location used is the location of the query clause which the call was generated from.</span></span>

<span data-ttu-id="aaf04-344">Si plusieurs attributs d’informations sur l’appelant est spécifié sur un paramètre donné, ils sont préférables dans l’ordre suivant : `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.</span><span class="sxs-lookup"><span data-stu-id="aaf04-344">If more than one caller info attribute is specified on a given parameter, they are preferred in the following order: `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.</span></span>

#### <a name="the-callerlinenumber-attribute"></a><span data-ttu-id="aaf04-345">L’attribut CallerLineNumber</span><span class="sxs-lookup"><span data-stu-id="aaf04-345">The CallerLineNumber attribute</span></span>

<span data-ttu-id="aaf04-346">Le `System.Runtime.CompilerServices.CallerLineNumberAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de la valeur de constante `int.MaxValue` pour le type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="aaf04-346">The `System.Runtime.CompilerServices.CallerLineNumberAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from the constant value `int.MaxValue` to the parameter's type.</span></span> <span data-ttu-id="aaf04-347">Cela garantit que tout numéro de ligne de non négatif jusqu'à cette valeur peut être passé sans erreur.</span><span class="sxs-lookup"><span data-stu-id="aaf04-347">This ensures that any non-negative line number up to that value can be passed without error.</span></span>

<span data-ttu-id="aaf04-348">Si un appel de fonction à partir d’un emplacement dans le code source omet un paramètre facultatif avec la `CallerLineNumberAttribute`, un littéral numérique représentant le numéro de ligne de cet emplacement est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-348">If a function invocation from a location in source code omits an optional parameter with the `CallerLineNumberAttribute`, then a numeric literal representing that location's line number is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="aaf04-349">Si l’appel s’étend sur plusieurs lignes, la ligne choisie est dépend de l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-349">If the invocation spans multiple lines, the line chosen is implementation-dependent.</span></span>

<span data-ttu-id="aaf04-350">Notez que le numéro de ligne peut être affecté par `#line` directives ([directives de ligne](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-350">Note that the line number may be affected by `#line` directives ([Line directives](lexical-structure.md#line-directives)).</span></span>

#### <a name="the-callerfilepath-attribute"></a><span data-ttu-id="aaf04-351">L’attribut CallerFilePath</span><span class="sxs-lookup"><span data-stu-id="aaf04-351">The CallerFilePath attribute</span></span>

<span data-ttu-id="aaf04-352">Le `System.Runtime.CompilerServices.CallerFilePathAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de `string` pour le type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="aaf04-352">The `System.Runtime.CompilerServices.CallerFilePathAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from `string` to the parameter's type.</span></span>

<span data-ttu-id="aaf04-353">Si un appel de fonction à partir d’un emplacement dans le code source omet un paramètre facultatif avec la `CallerFilePathAttribute`, un littéral de chaîne représentant le chemin d’accès de cet emplacement fichier est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-353">If a function invocation from a location in source code omits an optional parameter with the `CallerFilePathAttribute`, then a string literal representing that location's file path is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="aaf04-354">Le format du chemin du fichier est dépend de l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-354">The format of the file path is implementation-dependent.</span></span>

<span data-ttu-id="aaf04-355">Notez que le chemin d’accès de fichier peut être affectée par `#line` directives ([directives de ligne](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="aaf04-355">Note that the file path may be affected by `#line` directives ([Line directives](lexical-structure.md#line-directives)).</span></span>

#### <a name="the-callermembername-attribute"></a><span data-ttu-id="aaf04-356">L’attribut CallerMemberName</span><span class="sxs-lookup"><span data-stu-id="aaf04-356">The CallerMemberName attribute</span></span>

<span data-ttu-id="aaf04-357">Le `System.Runtime.CompilerServices.CallerMemberNameAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de `string` pour le type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="aaf04-357">The `System.Runtime.CompilerServices.CallerMemberNameAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from `string` to the parameter's type.</span></span>

<span data-ttu-id="aaf04-358">Si un appel de fonction à partir d’un emplacement dans le corps d’une fonction membre ou au sein d’un attribut appliqué à la fonction membre lui-même ou son type de retour, paramètres ou des paramètres de type dans le code source omet un paramètre facultatif avec la `CallerMemberNameAttribute`, puis un littéral de chaîne représentant le nom de ce membre est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-358">If a function invocation from a location within the body of a function member or within an attribute applied to the function member itself or its return type, parameters or type parameters in source code omits an optional parameter with the `CallerMemberNameAttribute`, then a string literal representing the name of that member is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="aaf04-359">Pour les appels qui se produisent dans les méthodes génériques, uniquement le nom de méthode est utilisé, sans la liste de paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="aaf04-359">For invocations that occur within generic methods, only the method name itself is used, without the type parameter list.</span></span>

<span data-ttu-id="aaf04-360">Pour les appels qui se produisent dans les implémentations de membres d’interface explicite, uniquement le nom de méthode est utilisé, sans la qualification d’interface précédent.</span><span class="sxs-lookup"><span data-stu-id="aaf04-360">For invocations that occur within explicit interface member implementations, only the method name itself is used, without the preceding interface qualification.</span></span>

<span data-ttu-id="aaf04-361">Pour les appels qui se produisent dans les accesseurs de propriété ou un événement, le nom de membre utilisé est celui de la propriété ou l’événement lui-même.</span><span class="sxs-lookup"><span data-stu-id="aaf04-361">For invocations that occur within property or event accessors, the member name used is that of the property or event itself.</span></span>

<span data-ttu-id="aaf04-362">Pour les appels qui se produisent dans les accesseurs des indexeurs, le nom de membre utilisé est celui fourni par un `IndexerNameAttribute` ([l’attribut IndexerName](attributes.md#the-indexername-attribute)) sur le membre d’indexeur, le cas échéant, ou le nom par défaut `Item` dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="aaf04-362">For invocations that occur within indexer accessors, the member name used is that supplied by an `IndexerNameAttribute` ([The IndexerName attribute](attributes.md#the-indexername-attribute)) on the indexer member, if present, or the default name `Item` otherwise.</span></span>

<span data-ttu-id="aaf04-363">Pour les appels qui se produisent dans les déclarations de constructeurs d’instance, des constructeurs statiques, des destructeurs et des opérateurs du membre nom utilisé est dépend de l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="aaf04-363">For invocations that occur within declarations of instance constructors, static constructors, destructors and operators the member name used is implementation-dependent.</span></span>

## <a name="attributes-for-interoperation"></a><span data-ttu-id="aaf04-364">Attributs d’interopérabilité</span><span class="sxs-lookup"><span data-stu-id="aaf04-364">Attributes for Interoperation</span></span>

<span data-ttu-id="aaf04-365">Remarque : Cette section s’applique uniquement à l’implémentation de Microsoft .NET de C#.</span><span class="sxs-lookup"><span data-stu-id="aaf04-365">Note: This section is applicable only to the Microsoft .NET implementation of C#.</span></span>

### <a name="interoperation-with-com-and-win32-components"></a><span data-ttu-id="aaf04-366">Interopérabilité avec les composants COM et Win32</span><span class="sxs-lookup"><span data-stu-id="aaf04-366">Interoperation with COM and Win32 components</span></span>

<span data-ttu-id="aaf04-367">La durée d’exécution .NET fournit un grand nombre d’attributs qui permettent aux programmes c# interagir avec les composants écrits à l’aide de COM et les DLL Win32.</span><span class="sxs-lookup"><span data-stu-id="aaf04-367">The .NET run-time provides a large number of attributes that enable C# programs to interoperate with components written using COM and Win32 DLLs.</span></span> <span data-ttu-id="aaf04-368">Par exemple, le `DllImport` attribut peut être utilisé sur un `static extern` méthode pour indiquer que l’implémentation de la méthode doit être trouvé dans une DLL Win32.</span><span class="sxs-lookup"><span data-stu-id="aaf04-368">For example, the `DllImport` attribute can be used on a `static extern` method to indicate that the implementation of the method is to be found in a Win32 DLL.</span></span> <span data-ttu-id="aaf04-369">Ces attributs sont trouvent dans le `System.Runtime.InteropServices` espace de noms et une documentation détaillée pour ces attributs se trouve dans la documentation du runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="aaf04-369">These attributes are found in the `System.Runtime.InteropServices` namespace, and detailed documentation for these attributes is found in the .NET runtime documentation.</span></span>

### <a name="interoperation-with-other-net-languages"></a><span data-ttu-id="aaf04-370">Interopérabilité avec d’autres langages .NET</span><span class="sxs-lookup"><span data-stu-id="aaf04-370">Interoperation with other .NET languages</span></span>

#### <a name="the-indexername-attribute"></a><span data-ttu-id="aaf04-371">L’attribut IndexerName</span><span class="sxs-lookup"><span data-stu-id="aaf04-371">The IndexerName attribute</span></span>

<span data-ttu-id="aaf04-372">Les indexeurs sont implémentés dans .NET à l’aide de propriétés indexées et ont un nom dans les métadonnées de .NET.</span><span class="sxs-lookup"><span data-stu-id="aaf04-372">Indexers are implemented in .NET using indexed properties, and have a name in the .NET metadata.</span></span> <span data-ttu-id="aaf04-373">Si aucun `IndexerName` attribut n’est présent pour un indexeur, puis le nom `Item` est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aaf04-373">If no `IndexerName` attribute is present for an indexer, then the name `Item` is used by default.</span></span> <span data-ttu-id="aaf04-374">Le `IndexerName` attribut permet à un développeur remplacer cette valeur par défaut et spécifiez un autre nom.</span><span class="sxs-lookup"><span data-stu-id="aaf04-374">The `IndexerName` attribute enables a developer to override this default and specify a different name.</span></span>

```csharp
namespace System.Runtime.CompilerServices.CSharp
{
    [AttributeUsage(AttributeTargets.Property)]
    public class IndexerNameAttribute: Attribute
    {
        public IndexerNameAttribute(string indexerName) {...}
        public string Value { get {...} } 
    }
}
```
