---
ms.openlocfilehash: 6d8f0512d478d3d82cb262466e49a22a9e3cd0ee
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84227977"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="fcf15-101">Travaux en cours d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="fcf15-101">Records Work-in-Progress</span></span>

<span data-ttu-id="fcf15-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours destiné à enregistrer les décisions de conception de consensus pour la fonctionnalité d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="fcf15-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="fcf15-103">Les détails de la spécification seront ajoutés si nécessaire pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="fcf15-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="fcf15-104">La syntaxe d’un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="fcf15-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="fcf15-105">Le `attributes` Terminal non-terminal autorise également un nouvel attribut contextuel, `data` .</span><span class="sxs-lookup"><span data-stu-id="fcf15-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="fcf15-106">Une classe (struct) déclarée avec un modificateur ou une liste `data` de paramètres est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="fcf15-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="fcf15-107">La déclaration d’un type d’enregistrement sans une liste de paramètres et le modificateur est une erreur `data` .</span><span class="sxs-lookup"><span data-stu-id="fcf15-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="fcf15-108">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="fcf15-108">Members of a record type</span></span>

<span data-ttu-id="fcf15-109">En plus des membres déclarés dans le corps de la classe ou du struct, un type d’enregistrement a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="fcf15-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="fcf15-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="fcf15-110">Primary Constructor</span></span>

<span data-ttu-id="fcf15-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="fcf15-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="fcf15-112">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="fcf15-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="fcf15-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="fcf15-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="fcf15-114">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="fcf15-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="fcf15-115">exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="fcf15-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="fcf15-116">Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur</span><span class="sxs-lookup"><span data-stu-id="fcf15-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="fcf15-117">Propriétés</span><span class="sxs-lookup"><span data-stu-id="fcf15-117">Properties</span></span>

<span data-ttu-id="fcf15-118">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="fcf15-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="fcf15-119">Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="fcf15-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="fcf15-120">Pour un struct d’enregistrement ou une classe d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="fcf15-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="fcf15-121">Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).</span><span class="sxs-lookup"><span data-stu-id="fcf15-121">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span> <span data-ttu-id="fcf15-122">Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="fcf15-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="fcf15-123">Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.</span><span class="sxs-lookup"><span data-stu-id="fcf15-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="fcf15-124">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="fcf15-124">Equality members</span></span>

<span data-ttu-id="fcf15-125">Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fcf15-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="fcf15-126">`object.GetHashCode()`override, sauf s’il est sealed ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="fcf15-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="fcf15-127">`object.Equals(object)`override, sauf s’il est sealed ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="fcf15-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="fcf15-128">`T Equals(T)`méthode, où `T` est le type actuel</span><span class="sxs-lookup"><span data-stu-id="fcf15-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="fcf15-129">`T Equals(T)`est spécifié pour effectuer une égalité de valeur, ce qui `Equals` a la valeur true si et seulement si tous les champs d’instance déclarés dans le type de récepteur sont égaux aux champs de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="fcf15-129">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all the instance fields declared in the receiver type are equal to the fields of the other type.</span></span>

<span data-ttu-id="fcf15-130">`object.Equals`exécute l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="fcf15-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="fcf15-131">Copier et cloner des membres</span><span class="sxs-lookup"><span data-stu-id="fcf15-131">Copy and Clone members</span></span>

<span data-ttu-id="fcf15-132">Un type d’enregistrement contient deux membres de copie synthétisée si les méthodes ayant la même signature ne sont pas déjà déclarées dans le type d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="fcf15-132">A record type contains two synthesized copying members if methods with the same signature are not already declared within the record type:</span></span>

* <span data-ttu-id="fcf15-133">Constructeur protégé acceptant un seul argument du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="fcf15-133">A protected constructor taking a single argument of the record type.</span></span>
* <span data-ttu-id="fcf15-134">Une méthode d’instance sans paramètre publique appelée `Clone` qui retourne le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="fcf15-134">A public parameterless instance method called `Clone` which returns the record type.</span></span>

<span data-ttu-id="fcf15-135">Le constructeur protégé est appelé « constructeur de copie » et le corps synthétisé copie les valeurs de tous les champs d’instance déclarés dans le type d’entrée dans les champs correspondants de `this` .</span><span class="sxs-lookup"><span data-stu-id="fcf15-135">The protected constructor is referred to as the "copy constructor" and the synthesized body copies the values of all instance fields declared in the input type to the corresponding fields of `this`.</span></span>

<span data-ttu-id="fcf15-136">La `Clone` méthode retourne le résultat d’un appel à un constructeur ayant la même signature que le constructeur de copie.</span><span class="sxs-lookup"><span data-stu-id="fcf15-136">The `Clone` method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span>
## <a name="with-expression"></a><span data-ttu-id="fcf15-137">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="fcf15-137">`with` expression</span></span>

<span data-ttu-id="fcf15-138">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="fcf15-138">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' '{' member_initializer_list? '}'
    ;
    
member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : identifier '=' expression
    ;
```

<span data-ttu-id="fcf15-139">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="fcf15-139">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="fcf15-140">Une `with` expression valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="fcf15-140">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="fcf15-141">Le type de récepteur doit contenir une méthode d’instance sans paramètre accessible appelée `Clone` dont le type de retour doit être le type de récepteur ou un type de base de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="fcf15-141">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the receiver type, or a base type thereof.</span></span>

<span data-ttu-id="fcf15-142">Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="fcf15-142">On the right hand side of the `with` expression is an `member_initializer_list` with a sequence of assignments to *identifier*, which must an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="fcf15-143">Chaque `member_initializer` est traité de la même façon qu’une assignation au champ ou à la cible de propriété sur la valeur de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="fcf15-143">Each `member_initializer` is processed the same way as an assignment to the field or property target on the return value of the `Clone()` method.</span></span> <span data-ttu-id="fcf15-144">La `Clone()` méthode n’est exécutée qu’une seule fois et les assignations sont traitées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="fcf15-144">The `Clone()` method is executed only once and the assignments are processed in lexical order.</span></span>
