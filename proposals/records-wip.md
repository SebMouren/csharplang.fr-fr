---
ms.openlocfilehash: 32682010c79073aabbc71ef9de433a7a76138c3f
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82596755"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="cdd05-101">Travaux en cours d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="cdd05-101">Records Work-in-Progress</span></span>

<span data-ttu-id="cdd05-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours destiné à enregistrer les décisions de conception de consensus pour la fonctionnalité d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="cdd05-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="cdd05-103">Les détails de la spécification seront ajoutés si nécessaire pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="cdd05-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="cdd05-104">La syntaxe d’un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="cdd05-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="cdd05-105">Le `attributes` terminal non-terminal autorise également un nouvel attribut contextuel `data`,.</span><span class="sxs-lookup"><span data-stu-id="cdd05-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="cdd05-106">Une classe (struct) déclarée avec un modificateur `data` ou une liste de paramètres est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="cdd05-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="cdd05-107">La déclaration d’un type d’enregistrement sans une liste de paramètres et le `data` modificateur est une erreur.</span><span class="sxs-lookup"><span data-stu-id="cdd05-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="cdd05-108">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="cdd05-108">Members of a record type</span></span>

<span data-ttu-id="cdd05-109">En plus des membres déclarés dans le corps de la classe ou du struct, un type d’enregistrement a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd05-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="cdd05-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="cdd05-110">Primary Constructor</span></span>

<span data-ttu-id="cdd05-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="cdd05-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="cdd05-112">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="cdd05-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="cdd05-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="cdd05-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="cdd05-114">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="cdd05-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="cdd05-115">exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="cdd05-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="cdd05-116">Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur</span><span class="sxs-lookup"><span data-stu-id="cdd05-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="cdd05-117">Propriétés</span><span class="sxs-lookup"><span data-stu-id="cdd05-117">Properties</span></span>

<span data-ttu-id="cdd05-118">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="cdd05-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="cdd05-119">Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="cdd05-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="cdd05-120">Pour un struct d’enregistrement ou une classe d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="cdd05-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="cdd05-121">Une propriété automatique de récupération en tant que public est créée.</span><span class="sxs-lookup"><span data-stu-id="cdd05-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="cdd05-122">Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="cdd05-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="cdd05-123">Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.</span><span class="sxs-lookup"><span data-stu-id="cdd05-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

  * <span data-ttu-id="cdd05-124">Cette propriété est également `initonly`, ce qui signifie que le champ de stockage peut être modifié dans l’expression [with](#With) ci- `get` dessous, si l’accesseur correspondant est accessible</span><span class="sxs-lookup"><span data-stu-id="cdd05-124">This property is also `initonly`, meaning the backing field can be modified in the [with](#With) expression below, if the corresponding `get` accessor is accessible</span></span>

### <a name="equality-members"></a><span data-ttu-id="cdd05-125">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="cdd05-125">Equality members</span></span>

<span data-ttu-id="cdd05-126">Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd05-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="cdd05-127">`object.GetHashCode()`override, sauf s’il est sealed ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="cdd05-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="cdd05-128">`object.Equals(object)`override, sauf s’il est sealed ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="cdd05-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="cdd05-129">`T Equals(T)`méthode, où `T` est le type actuel</span><span class="sxs-lookup"><span data-stu-id="cdd05-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="cdd05-130">`T Equals(T)`est spécifié pour effectuer l’égalité des valeurs, en comparant la propriété portant le même nom que chaque paramètre de constructeur principal à la propriété correspondante de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="cdd05-130">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="cdd05-131">`object.Equals`exécute l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="cdd05-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="cdd05-132">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="cdd05-132">`with` expression</span></span>

<span data-ttu-id="cdd05-133">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="cdd05-133">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="cdd05-134">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications apportées aux `anonymous_object_initializer`propriétés figurant dans le.</span><span class="sxs-lookup"><span data-stu-id="cdd05-134">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="cdd05-135">Une expression `with` valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="cdd05-135">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="cdd05-136">Le type de récepteur doit contenir une méthode d’instance sans paramètre `Clone` accessible appelée dont le type de retour doit être le type de récepteur ou un type de base de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="cdd05-136">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the receiver type, or a base type thereof.</span></span>

<span data-ttu-id="cdd05-137">Sur le côté `with` droit de l’expression se trouve un `anonymous_object_initializer` avec une séquence d’assignations, chacune `initonly` avec une propriété (consultez [Propriétés](#Properties)) `Clone` du type de retour sur le côté gauche de l’assignation (en tant qu’appel de propriété) et une expression arbitraire sur le côté droit qui est implicitement convertible en type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="cdd05-137">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments, each with an `initonly` property (see [Properties](#Properties)) of the `Clone` return type on the left-hand side of the assignment (as a property invocation), and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the property.</span></span>

<span data-ttu-id="cdd05-138">L’évaluation d’une `with` expression appelle la `Clone` méthode exactement une fois, puis définit le champ de stockage de chaque `initonly` propriété dans la liste d’arguments sur la valeur de son expression correspondante, dans l’ordre lexical, à l’aide `Clone` du résultat de l’appel de la méthode comme récepteur.</span><span class="sxs-lookup"><span data-stu-id="cdd05-138">The evaluation of a `with` expression calls the `Clone` method exactly once, and then sets the backing field of each `initonly` property in the argument list to the value of its corresponding expression, in lexical order, using the result of the `Clone` method invocation as the receiver.</span></span> <span data-ttu-id="cdd05-139">Le type de l' `with` expression est le même que le type de retour de `Clone` la méthode.</span><span class="sxs-lookup"><span data-stu-id="cdd05-139">The type of the `with` expression is the same as the return type of the `Clone` method.</span></span>

