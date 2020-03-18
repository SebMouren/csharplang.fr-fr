---
ms.openlocfilehash: 25756c1811d5e6dc97512ce70f99ab7fefa91c4a
ms.sourcegitcommit: 2a6dffb60718065ece95df75e1cc7eb509e48a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2020
ms.locfileid: "79485237"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="e6651-101">Travaux en cours d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="e6651-101">Records Work-in-Progress</span></span>

<span data-ttu-id="e6651-102">Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours destiné à enregistrer les décisions de conception de consensus pour la fonctionnalité d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="e6651-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="e6651-103">Les détails de la spécification seront ajoutés si nécessaire pour résoudre les questions.</span><span class="sxs-lookup"><span data-stu-id="e6651-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="e6651-104">La syntaxe d’un enregistrement est proposée pour être ajoutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="e6651-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="e6651-105">Le `attributes` non-terminal autorise également un nouvel attribut contextuel, `data`.</span><span class="sxs-lookup"><span data-stu-id="e6651-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="e6651-106">Une classe (struct) déclarée avec une liste de paramètres ou un modificateur de `data` est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="e6651-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="e6651-107">La déclaration d’un type d’enregistrement sans une liste de paramètres et le modificateur `data` est une erreur.</span><span class="sxs-lookup"><span data-stu-id="e6651-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="e6651-108">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="e6651-108">Members of a record type</span></span>

<span data-ttu-id="e6651-109">En plus des membres déclarés dans le corps de la classe, un type d’enregistrement a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="e6651-109">In addition to the members declared in the class-body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="e6651-110">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="e6651-110">Primary Constructor</span></span>

<span data-ttu-id="e6651-111">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="e6651-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="e6651-112">C’est ce que l’on appelle le constructeur principal pour le type et entraîne la suppression du constructeur par défaut déclaré implicitement.</span><span class="sxs-lookup"><span data-stu-id="e6651-112">This is called the primary constructor for the type, and causes the implicitly declared default constructor to be suppressed.</span></span> <span data-ttu-id="e6651-113">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="e6651-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>
<span data-ttu-id="e6651-114">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="e6651-114">At runtime the primary constructor</span></span> 

1. <span data-ttu-id="e6651-115">exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.</span><span class="sxs-lookup"><span data-stu-id="e6651-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="e6651-116">Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur ; consultez les [Propriétés synthétisées](#Synthesized Properties))</span><span class="sxs-lookup"><span data-stu-id="e6651-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; see [Synthesized properties](#Synthesized Properties))</span></span>


<span data-ttu-id="e6651-117">[] TODO : ajoutez la syntaxe et la spécification de l’appel de base concernant le choix du constructeur de base via la résolution de surcharge</span><span class="sxs-lookup"><span data-stu-id="e6651-117">[ ] TODO: add base call syntax and specification about choosing base constructor through overload resolution</span></span>

### <a name="properties"></a><span data-ttu-id="e6651-118">Propriétés</span><span class="sxs-lookup"><span data-stu-id="e6651-118">Properties</span></span>

<span data-ttu-id="e6651-119">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="e6651-119">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="e6651-120">Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="e6651-120">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="e6651-121">Pour un struct d’enregistrement ou une classe d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="e6651-121">For a record struct or a record class:</span></span>

* <span data-ttu-id="e6651-122">Une propriété automatique de récupération en tant que public est créée.</span><span class="sxs-lookup"><span data-stu-id="e6651-122">A public get-only auto-property is created.</span></span> <span data-ttu-id="e6651-123">Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="e6651-123">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="e6651-124">Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.</span><span class="sxs-lookup"><span data-stu-id="e6651-124">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="e6651-125">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="e6651-125">Equality members</span></span>

<span data-ttu-id="e6651-126">Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e6651-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="e6651-127">`object.GetHashCode()` remplacement, sauf s’il est scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="e6651-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="e6651-128">`object.Equals(object)` remplacement, sauf s’il est scellé ou fourni par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="e6651-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="e6651-129">méthode `T Equals(T)`, où `T` est le type actuel</span><span class="sxs-lookup"><span data-stu-id="e6651-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="e6651-130">`T Equals(T)` est spécifié pour effectuer l’égalité des valeurs, en comparant la propriété portant le même nom que chaque paramètre de constructeur principal à la propriété correspondante de l’autre type.</span><span class="sxs-lookup"><span data-stu-id="e6651-130">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="e6651-131">`object.Equals` effectue l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="e6651-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```
