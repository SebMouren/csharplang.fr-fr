---
ms.openlocfilehash: e29f453eb33e06fb3c84fb542d8b74223af9b975
ms.sourcegitcommit: 7f0c8e4eac7afe75e4f312f54a554614384cd06b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80870980"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="46a63-101">Documents Travail en cours</span><span class="sxs-lookup"><span data-stu-id="46a63-101">Records Work-in-Progress</span></span>

<span data-ttu-id="46a63-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours visant à consigner les décisions de conception consensuelles pour la fonction d’enregistrements.</span><span class="sxs-lookup"><span data-stu-id="46a63-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="46a63-103">Les détails de spécification seront ajoutés au besoin pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="46a63-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="46a63-104">La syntaxe pour un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="46a63-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="46a63-105">Le `attributes` non-terminal permettra également un nouvel `data`attribut contextuel, .</span><span class="sxs-lookup"><span data-stu-id="46a63-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="46a63-106">Une classe (struct) déclarée `data` avec une liste de paramètres ou un modificateur est appelée classe record (struct d’enregistrement), dont l’un ou l’autre est un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="46a63-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="46a63-107">C’est une erreur de déclarer un type `data` d’enregistrement sans une liste de paramètres et le modificateur.</span><span class="sxs-lookup"><span data-stu-id="46a63-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="46a63-108">Membres d’un type de dossier</span><span class="sxs-lookup"><span data-stu-id="46a63-108">Members of a record type</span></span>

<span data-ttu-id="46a63-109">En plus des membres déclarés dans la classe ou l’organisme de struct, un type de dossier a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="46a63-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="46a63-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="46a63-110">Primary Constructor</span></span>

<span data-ttu-id="46a63-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="46a63-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="46a63-112">C’est ce qu’on appelle le constructeur principal pour le type, et provoque le constructeur implicitement déclaré classe par défaut, si présent, d’être supprimé.</span><span class="sxs-lookup"><span data-stu-id="46a63-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="46a63-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="46a63-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="46a63-114">Au moment de l’exécution, le principal constructeur</span><span class="sxs-lookup"><span data-stu-id="46a63-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="46a63-115">exécute les initialisateurs de champ d’instance apparaissant dans le corps de classe ; puis invoque le constructeur de la classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="46a63-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="46a63-116">initialise les champs de support générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur</span><span class="sxs-lookup"><span data-stu-id="46a63-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="46a63-117">Propriétés</span><span class="sxs-lookup"><span data-stu-id="46a63-117">Properties</span></span>

<span data-ttu-id="46a63-118">Pour chaque paramètre d’enregistrement d’une déclaration de type enregistrement, il y a un membre de la propriété publique correspondante dont le nom et le type sont tirés de la déclaration de paramètres de valeur.</span><span class="sxs-lookup"><span data-stu-id="46a63-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="46a63-119">Si aucune propriété concrète (c.-à-d. non abstraite) avec un accesseur d’accès et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="46a63-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="46a63-120">Pour une struction d’enregistrement ou une classe record :</span><span class="sxs-lookup"><span data-stu-id="46a63-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="46a63-121">Une autoproprié propriété publique est créée.</span><span class="sxs-lookup"><span data-stu-id="46a63-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="46a63-122">Sa valeur est parascée pendant la construction avec la valeur du paramètre constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="46a63-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="46a63-123">Chaque « appariement » hérité de la propriété abstraite obtenir accesseur est remplacé.</span><span class="sxs-lookup"><span data-stu-id="46a63-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="46a63-124">Membres de l’égalité</span><span class="sxs-lookup"><span data-stu-id="46a63-124">Equality members</span></span>

<span data-ttu-id="46a63-125">Les types d’enregistrement produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="46a63-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="46a63-126">`object.GetHashCode()`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="46a63-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="46a63-127">`object.Equals(object)`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="46a63-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="46a63-128">`T Equals(T)`méthode, `T` où est le type actuel</span><span class="sxs-lookup"><span data-stu-id="46a63-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="46a63-129">`T Equals(T)`est spécifié pour effectuer l’égalité de valeur, en comparant la propriété avec le même nom que chaque paramètre constructeur principal à la propriété correspondante de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="46a63-129">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="46a63-130">`object.Equals`effectue l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="46a63-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="46a63-131">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="46a63-131">`with` expression</span></span>

<span data-ttu-id="46a63-132">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="46a63-132">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="46a63-133">Une `with` expression permet une «mutation non destructive», conçue pour produire une copie de `anonymous_object_initializer`l’expression du récepteur avec des modifications aux propriétés énumérées dans le .</span><span class="sxs-lookup"><span data-stu-id="46a63-133">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="46a63-134">Une `with` expression valide a un récepteur avec un type non vide.</span><span class="sxs-lookup"><span data-stu-id="46a63-134">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="46a63-135">Le type de récepteur doit `With` contenir une méthode d’instance accessible appelée avec les paramètres appropriés et le type de retour.</span><span class="sxs-lookup"><span data-stu-id="46a63-135">The receiver type must contain an accessible instance method called `With` with the appropriate parameters and return type.</span></span> <span data-ttu-id="46a63-136">C’est une erreur s’il `With` existe plusieurs méthodes de non-remplacement.</span><span class="sxs-lookup"><span data-stu-id="46a63-136">It is an error if there are multiple non-override `With` methods.</span></span> <span data-ttu-id="46a63-137">S’il `With` y a plusieurs remplacements, il `With` doit y avoir une méthode de non-remplacement, qui est la méthode cible.</span><span class="sxs-lookup"><span data-stu-id="46a63-137">If there are multiple `With` overrides, there must be a non-override `With` method, which is the target method.</span></span> <span data-ttu-id="46a63-138">Sinon, il doit `With` y avoir exactement une méthode.</span><span class="sxs-lookup"><span data-stu-id="46a63-138">Otherwise, there must be exactly one `With` method.</span></span>

<span data-ttu-id="46a63-139">Sur le côté droit `with` de `anonymous_object_initializer` l’expression est une séquence d’affectations avec un champ ou une propriété du récepteur sur le côté gauche de la mission, et une expression arbitraire sur le côté droit qui est implicitement convertible au type du côté gauche.</span><span class="sxs-lookup"><span data-stu-id="46a63-139">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a field or property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="46a63-140">Compte tenu `With` d’une méthode cible, le type de retour doit être le type d’expression du récepteur, ou un type de base de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="46a63-140">Given a target `With` method, the return type must be the type of the receiver expression type, or a base type thereof.</span></span> <span data-ttu-id="46a63-141">Pour chaque paramètre de la `With` méthode, il doit y avoir un champ d’instance correspondant accessible ou une propriété lisible sur le type de récepteur du même nom et du même type.</span><span class="sxs-lookup"><span data-stu-id="46a63-141">For each parameter of the `With` method, there must be an accessible corresponding instance field or readable property on the receiver type with the same name and the same type.</span></span> <span data-ttu-id="46a63-142">Chaque propriété ou champ dans le côté droit de l’expression Avec doit `With` également correspondre à un paramètre du même nom dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="46a63-142">Each property or field in the right-hand side of the With expression must also correspond to a parameter of the same name in the `With` method.</span></span>

<span data-ttu-id="46a63-143">Compte tenu `With` d’une méthode `with` valide, l’évaluation d’une expression équivaut à appeler la `With` méthode avec les expressions dans le `anonymous_object_initializer` substitué au paramètre du même nom que la propriété sur le côté gauche.</span><span class="sxs-lookup"><span data-stu-id="46a63-143">Given a valid `With` method, the evaluation of a `with` expression is equivalent to calling the `With` method with the expressions in the `anonymous_object_initializer` substituted for the parameter of the same name as the property on the left hand side.</span></span> <span data-ttu-id="46a63-144">S’il n’y a pas `anonymous_object_initializer`de propriété correspondante pour un paramètre donné dans le , l’argument est l’évaluation du champ ou de la propriété du même nom sur le récepteur.</span><span class="sxs-lookup"><span data-stu-id="46a63-144">If there is no matching property for a given parameter in the `anonymous_object_initializer`, the argument is the evaluation of the field or property of the same name on the receiver.</span></span>

<span data-ttu-id="46a63-145">L’ordre d’évaluation des effets secondaires est le suivant, chaque expression étant évaluée exactement une fois :</span><span class="sxs-lookup"><span data-stu-id="46a63-145">The order of evaluation of side effects is as follows, with each expression evaluated exactly once:</span></span>

1. <span data-ttu-id="46a63-146">Expression du récepteur</span><span class="sxs-lookup"><span data-stu-id="46a63-146">Receiver expression</span></span>

2. <span data-ttu-id="46a63-147">Expressions dans `anonymous_object_initializer`le , dans l’ordre lexical</span><span class="sxs-lookup"><span data-stu-id="46a63-147">Expressions in the `anonymous_object_initializer`, in lexical order</span></span>

3. <span data-ttu-id="46a63-148">L’évaluation de toutes `With` les propriétés correspondant aux `With` paramètres de la méthode, par ordre de définition des paramètres de la méthode.</span><span class="sxs-lookup"><span data-stu-id="46a63-148">The evaluation of any properties matching the `With` method parameters, in order of definition of the `With` method parameters.</span></span>
