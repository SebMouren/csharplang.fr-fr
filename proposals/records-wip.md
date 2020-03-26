---
ms.openlocfilehash: 258ae6865c5b2c3103a0cdf7e1e5a2cdee11e740
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281955"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="15f42-101">Travaux en cours d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="15f42-101">Records Work-in-Progress</span></span>

<span data-ttu-id="15f42-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours destiné à enregistrer les décisions de conception de consensus pour la fonctionnalité d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="15f42-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="15f42-103">Les détails de la spécification seront ajoutés si nécessaire pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="15f42-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="15f42-104">La syntaxe d’un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="15f42-104">The syntax for a record is proposed to be added as follows:</span></span>

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      parameter_list? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

<span data-ttu-id="15f42-105">Le `attributes` non-terminal autorise également un nouvel attribut contextuel, `data`.</span><span class="sxs-lookup"><span data-stu-id="15f42-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="15f42-106">Une classe (struct) déclarée avec une liste de paramètres ou un modificateur de `data` est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="15f42-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="15f42-107">La déclaration d’un type d’enregistrement sans une liste de paramètres et le modificateur `data` est une erreur.</span><span class="sxs-lookup"><span data-stu-id="15f42-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="15f42-108">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="15f42-108">Members of a record type</span></span>

<span data-ttu-id="15f42-109">En plus des membres déclarés dans le corps de la classe, un type d’enregistrement a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="15f42-109">In addition to the members declared in the class-body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="15f42-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="15f42-110">Primary Constructor</span></span>

<span data-ttu-id="15f42-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="15f42-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="15f42-112">C’est ce que l’on appelle le constructeur principal pour le type et entraîne la suppression du constructeur par défaut déclaré implicitement.</span><span class="sxs-lookup"><span data-stu-id="15f42-112">This is called the primary constructor for the type, and causes the implicitly declared default constructor to be suppressed.</span></span> <span data-ttu-id="15f42-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="15f42-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>
<span data-ttu-id="15f42-114">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="15f42-114">At runtime the primary constructor</span></span> 

1. <span data-ttu-id="15f42-115">exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="15f42-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="15f42-116">Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur ; consultez les [Propriétés synthétisées](#Synthesized Properties))</span><span class="sxs-lookup"><span data-stu-id="15f42-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; see [Synthesized properties](#Synthesized Properties))</span></span>


<span data-ttu-id="15f42-117">[] TODO : ajoutez la syntaxe et la spécification de l’appel de base concernant le choix du constructeur de base via la résolution de surcharge</span><span class="sxs-lookup"><span data-stu-id="15f42-117">[ ] TODO: add base call syntax and specification about choosing base constructor through overload resolution</span></span>

### <a name="properties"></a><span data-ttu-id="15f42-118">Propriétés</span><span class="sxs-lookup"><span data-stu-id="15f42-118">Properties</span></span>

<span data-ttu-id="15f42-119">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="15f42-119">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="15f42-120">Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="15f42-120">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="15f42-121">Pour un struct d’enregistrement ou une classe d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="15f42-121">For a record struct or a record class:</span></span>

* <span data-ttu-id="15f42-122">Une propriété automatique de récupération en tant que public est créée.</span><span class="sxs-lookup"><span data-stu-id="15f42-122">A public get-only auto-property is created.</span></span> <span data-ttu-id="15f42-123">Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="15f42-123">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="15f42-124">Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.</span><span class="sxs-lookup"><span data-stu-id="15f42-124">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="15f42-125">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="15f42-125">Equality members</span></span>

<span data-ttu-id="15f42-126">Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="15f42-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="15f42-127">`object.GetHashCode()` remplacement, sauf s’il est scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="15f42-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="15f42-128">`object.Equals(object)` remplacement, sauf s’il est scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="15f42-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="15f42-129">méthode `T Equals(T)`, où `T` est le type actuel</span><span class="sxs-lookup"><span data-stu-id="15f42-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="15f42-130">`T Equals(T)` est spécifié pour effectuer l’égalité des valeurs, en comparant la propriété portant le même nom que chaque paramètre de constructeur principal à la propriété correspondante de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="15f42-130">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="15f42-131">`object.Equals` effectue l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="15f42-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="15f42-132">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="15f42-132">`with` expression</span></span>

<span data-ttu-id="15f42-133">Une expression `with` est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="15f42-133">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="15f42-134">Une expression `with` permet la « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications apportées aux propriétés figurant dans la `anonymous_object_initializer`.</span><span class="sxs-lookup"><span data-stu-id="15f42-134">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="15f42-135">Une expression `with` valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="15f42-135">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="15f42-136">Le type de récepteur doit contenir une méthode d’instance accessible appelée `With` avec les paramètres et le type de retour appropriés.</span><span class="sxs-lookup"><span data-stu-id="15f42-136">The receiver type must contain an accessible instance method called `With` with the appropriate parameters and return type.</span></span> <span data-ttu-id="15f42-137">C’est une erreur s’il existe plusieurs méthodes de `With` non substituées.</span><span class="sxs-lookup"><span data-stu-id="15f42-137">It is an error if there are multiple non-override `With` methods.</span></span> <span data-ttu-id="15f42-138">S’il existe plusieurs remplacements `With`, il doit y avoir une méthode de `With` non substituée, qui est la méthode cible.</span><span class="sxs-lookup"><span data-stu-id="15f42-138">If there are multiple `With` overrides, there must be a non-override `With` method, which is the target method.</span></span> <span data-ttu-id="15f42-139">Dans le cas contraire, il doit y avoir exactement une seule méthode `With`.</span><span class="sxs-lookup"><span data-stu-id="15f42-139">Otherwise, there must be exactly one `With` method.</span></span>

<span data-ttu-id="15f42-140">Sur le côté droit de l’expression `with` est une `anonymous_object_initializer` avec une séquence d’assignations avec un champ ou une propriété du récepteur sur le côté gauche de l’assignation, et une expression arbitraire sur le côté droit qui est implicitement convertible dans le type de la partie gauche.</span><span class="sxs-lookup"><span data-stu-id="15f42-140">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a field or property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="15f42-141">Pour une méthode `With` cible donnée, le type de retour doit être le type du type d’expression de récepteur ou un type de base de ce type.</span><span class="sxs-lookup"><span data-stu-id="15f42-141">Given a target `With` method, the return type must be the type of the receiver expression type, or a base type thereof.</span></span> <span data-ttu-id="15f42-142">Pour chaque paramètre de la méthode `With`, il doit y avoir un champ d’instance correspondant accessible ou une propriété lisible sur le type de récepteur avec le même nom et le même type.</span><span class="sxs-lookup"><span data-stu-id="15f42-142">For each parameter of the `With` method, there must be an accessible corresponding instance field or readable property on the receiver type with the same name and the same type.</span></span> <span data-ttu-id="15f42-143">Chaque propriété ou champ du côté droit de l’expression with doit également correspondre à un paramètre du même nom dans la méthode `With`.</span><span class="sxs-lookup"><span data-stu-id="15f42-143">Each property or field in the right-hand side of the With expression must also correspond to a parameter of the same name in the `With` method.</span></span>

<span data-ttu-id="15f42-144">Étant donné une méthode de `With` valide, l’évaluation d’une expression `with` revient à appeler la méthode `With` avec les expressions du `anonymous_object_initializer` substituées pour le paramètre du même nom que la propriété de gauche.</span><span class="sxs-lookup"><span data-stu-id="15f42-144">Given a valid `With` method, the evaluation of a `with` expression is equivalent to calling the `With` method with the expressions in the `anonymous_object_initializer` substituted for the parameter of the same name as the property on the left hand side.</span></span> <span data-ttu-id="15f42-145">S’il n’existe aucune propriété correspondante pour un paramètre donné dans le `anonymous_object_initializer`, l’argument correspond à l’évaluation du champ ou de la propriété du même nom sur le récepteur.</span><span class="sxs-lookup"><span data-stu-id="15f42-145">If there is no matching property for a given parameter in the `anonymous_object_initializer`, the argument is the evaluation of the field or property of the same name on the receiver.</span></span>

<span data-ttu-id="15f42-146">L’ordre d’évaluation des effets secondaires est le suivant, chaque expression évaluée exactement une fois :</span><span class="sxs-lookup"><span data-stu-id="15f42-146">The order of evaluation of side effects is as follows, with each expression evaluated exactly once:</span></span>

1. <span data-ttu-id="15f42-147">Expression Receiver</span><span class="sxs-lookup"><span data-stu-id="15f42-147">Receiver expression</span></span>

2. <span data-ttu-id="15f42-148">Expressions dans le `anonymous_object_initializer`, dans l’ordre lexical</span><span class="sxs-lookup"><span data-stu-id="15f42-148">Expressions in the `anonymous_object_initializer`, in lexical order</span></span>

3. <span data-ttu-id="15f42-149">L’évaluation de toutes les propriétés correspondant aux paramètres de méthode `With`, dans l’ordre de définition des paramètres de méthode `With`.</span><span class="sxs-lookup"><span data-stu-id="15f42-149">The evaluation of any properties matching the `With` method parameters, in order of definition of the `With` method parameters.</span></span>