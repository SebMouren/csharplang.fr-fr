---
ms.openlocfilehash: 1fb1f3b9025d65cb39f675e60bae1cb6415fc184
ms.sourcegitcommit: 88202acd40ca04b6e513ea1e5993625ee26fac3f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81219626"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="29894-101">Documents Travail en cours</span><span class="sxs-lookup"><span data-stu-id="29894-101">Records Work-in-Progress</span></span>

<span data-ttu-id="29894-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours visant à consigner les décisions de conception consensuelles pour la fonction d’enregistrements.</span><span class="sxs-lookup"><span data-stu-id="29894-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="29894-103">Les détails de spécification seront ajoutés au besoin pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="29894-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="29894-104">La syntaxe pour un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="29894-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="29894-105">Le `attributes` non-terminal permettra également un nouvel `data`attribut contextuel, .</span><span class="sxs-lookup"><span data-stu-id="29894-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="29894-106">Une classe (struct) déclarée `data` avec une liste de paramètres ou un modificateur est appelée classe record (struct d’enregistrement), dont l’un ou l’autre est un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="29894-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="29894-107">C’est une erreur de déclarer un type `data` d’enregistrement sans une liste de paramètres et le modificateur.</span><span class="sxs-lookup"><span data-stu-id="29894-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="29894-108">Membres d’un type de dossier</span><span class="sxs-lookup"><span data-stu-id="29894-108">Members of a record type</span></span>

<span data-ttu-id="29894-109">En plus des membres déclarés dans la classe ou l’organisme de struct, un type de dossier a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="29894-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="29894-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="29894-110">Primary Constructor</span></span>

<span data-ttu-id="29894-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="29894-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="29894-112">C’est ce qu’on appelle le constructeur principal pour le type, et provoque le constructeur implicitement déclaré classe par défaut, si présent, d’être supprimé.</span><span class="sxs-lookup"><span data-stu-id="29894-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="29894-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="29894-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="29894-114">Au moment de l’exécution, le principal constructeur</span><span class="sxs-lookup"><span data-stu-id="29894-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="29894-115">exécute les initialisateurs de champ d’instance apparaissant dans le corps de classe ; puis invoque le constructeur de la classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="29894-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="29894-116">initialise les champs de support générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur</span><span class="sxs-lookup"><span data-stu-id="29894-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="29894-117">Propriétés</span><span class="sxs-lookup"><span data-stu-id="29894-117">Properties</span></span>

<span data-ttu-id="29894-118">Pour chaque paramètre d’enregistrement d’une déclaration de type enregistrement, il y a un membre de la propriété publique correspondante dont le nom et le type sont tirés de la déclaration de paramètres de valeur.</span><span class="sxs-lookup"><span data-stu-id="29894-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="29894-119">Si aucune propriété concrète (c.-à-d. non abstraite) avec un accesseur d’accès et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="29894-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="29894-120">Pour une struction d’enregistrement ou une classe record :</span><span class="sxs-lookup"><span data-stu-id="29894-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="29894-121">Une autoproprié propriété publique est créée.</span><span class="sxs-lookup"><span data-stu-id="29894-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="29894-122">Sa valeur est parascée pendant la construction avec la valeur du paramètre constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="29894-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="29894-123">Chaque « appariement » hérité de la propriété abstraite obtenir accesseur est remplacé.</span><span class="sxs-lookup"><span data-stu-id="29894-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="29894-124">Membres de l’égalité</span><span class="sxs-lookup"><span data-stu-id="29894-124">Equality members</span></span>

<span data-ttu-id="29894-125">Les types d’enregistrement produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="29894-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="29894-126">`object.GetHashCode()`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="29894-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="29894-127">`object.Equals(object)`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="29894-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="29894-128">`T Equals(T)`méthode, `T` où est le type actuel</span><span class="sxs-lookup"><span data-stu-id="29894-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="29894-129">`T Equals(T)`est spécifié pour effectuer l’égalité de valeur, en comparant la propriété avec le même nom que chaque paramètre constructeur principal à la propriété correspondante de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="29894-129">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="29894-130">`object.Equals`effectue l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="29894-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="29894-131">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="29894-131">`with` expression</span></span>

<span data-ttu-id="29894-132">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="29894-132">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="29894-133">Une `with` expression permet une «mutation non destructive», conçue pour produire une copie de `anonymous_object_initializer`l’expression du récepteur avec des modifications aux propriétés énumérées dans le .</span><span class="sxs-lookup"><span data-stu-id="29894-133">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="29894-134">Une `with` expression valide a un récepteur avec un type non vide.</span><span class="sxs-lookup"><span data-stu-id="29894-134">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="29894-135">Le type de récepteur doit contenir `Clone` une méthode d’instance sans paramètres accessible appelée dont le type de retour doit être le type express du récepteur, ou un type de base de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="29894-135">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the type of the receiver express type, or a base type thereof.</span></span>

<span data-ttu-id="29894-136">Sur le côté droit `with` de `anonymous_object_initializer` l’expression est une séquence d’affectations avec une propriété d’enregistrement générée par compilateur du récepteur sur le côté gauche de l’affectation, et une expression arbitraire sur le côté droit qui est implicitement convertible au type du côté gauche.</span><span class="sxs-lookup"><span data-stu-id="29894-136">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a compiler-generated record property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="29894-137">L’évaluation `with` d’une expression `Clone` équivaut à appeler la méthode exactement une fois, puis à définir le champ d’appui de `Clone` chaque propriété d’enregistrement dans la liste d’argumentation à son expression correspondante, dans l’ordre lexical, en utilisant le résultat de la méthode comme récepteur.</span><span class="sxs-lookup"><span data-stu-id="29894-137">The evaluation of a `with` expression is equivalent to calling the `Clone` method exactly once, and then setting the backing field of each record property in the argument list to its corresponding expression, in lexical order, using the result of the `Clone` method as the receiver.</span></span>
