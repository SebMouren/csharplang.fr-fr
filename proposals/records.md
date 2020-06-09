---
ms.openlocfilehash: ffa34ad55752197bc2d8fb6cac7759602a2672c9
ms.sourcegitcommit: 5c9b8f27bd8299c70e2f4205a46079a10cffce76
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533344"
---

# <a name="records"></a><span data-ttu-id="82400-101">Enregistrements</span><span class="sxs-lookup"><span data-stu-id="82400-101">Records</span></span>

<span data-ttu-id="82400-102">Cette proposition effectue le suivi de la spécification de la fonctionnalité d’enregistrements C# 9, comme convenu par l’équipe de conception du langage C#.</span><span class="sxs-lookup"><span data-stu-id="82400-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="82400-103">La syntaxe d’un enregistrement est la suivante :</span><span class="sxs-lookup"><span data-stu-id="82400-103">The syntax for a record is as follows:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}'
    | ';'
    ;
```

<span data-ttu-id="82400-104">Les types d’enregistrements sont des types référence, similaires à une déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="82400-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="82400-105">Si un enregistrement `record_base` `argument_list` `record_declaration` ne contient pas de, il s’agit d’une erreur `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="82400-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="82400-106">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="82400-106">Members of a record type</span></span>

<span data-ttu-id="82400-107">En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="82400-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="82400-108">Les membres sont synthétisés, à moins qu’un membre concret (non abstrait) accessible avec une signature « correspondante » soit hérité ou déclaré dans le corps de l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="82400-108">Members are synthesized unless an accessible concrete (non-abstract) member with a "matching" signature is either inherited or declared in the record body.</span></span> <span data-ttu-id="82400-109">Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.</span><span class="sxs-lookup"><span data-stu-id="82400-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="82400-110">Les membres synthétisés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="82400-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="82400-111">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="82400-111">Equality members</span></span>

<span data-ttu-id="82400-112">Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes, où `T` est le type conteneur :</span><span class="sxs-lookup"><span data-stu-id="82400-112">Record types produce synthesized implementations for the following methods, where `T` is the containing type:</span></span>

* <span data-ttu-id="82400-113">`object.GetHashCode()`remplacer</span><span class="sxs-lookup"><span data-stu-id="82400-113">`object.GetHashCode()` override</span></span>
* <span data-ttu-id="82400-114">`object.Equals(object)`remplacer</span><span class="sxs-lookup"><span data-stu-id="82400-114">`object.Equals(object)` override</span></span>
* <span data-ttu-id="82400-115">`T Equals(T)`méthode, où `T` est le type actuel</span><span class="sxs-lookup"><span data-stu-id="82400-115">`T Equals(T)` method, where `T` is the current type</span></span>
* <span data-ttu-id="82400-116">`Type EqualityContract`propriété de récupération seule</span><span class="sxs-lookup"><span data-stu-id="82400-116">`Type EqualityContract` get-only property</span></span>

<span data-ttu-id="82400-117">Si `object.GetHashCode()` ou `object.Equals(object)` sont scellés, une erreur est générée.</span><span class="sxs-lookup"><span data-stu-id="82400-117">If either `object.GetHashCode()` or `object.Equals(object)` are sealed, an error is produced.</span></span>

<span data-ttu-id="82400-118">`EqualityContract`est une propriété d’instance virtuelle qui retourne `typeof(T)` .</span><span class="sxs-lookup"><span data-stu-id="82400-118">`EqualityContract` is a virtual instance property which returns `typeof(T)`.</span></span> <span data-ttu-id="82400-119">Si le type de base définit un `EqualityContract` , il est substitué dans l’enregistrement dérivé.</span><span class="sxs-lookup"><span data-stu-id="82400-119">If the base type defines an `EqualityContract` it is overridden in the derived record.</span></span> <span data-ttu-id="82400-120">Si la base `EqualityContract` est sealed ou non virtuelle, une erreur est générée.</span><span class="sxs-lookup"><span data-stu-id="82400-120">If the base `EqualityContract` is sealed or non-virtual, an error is produced.</span></span>

<span data-ttu-id="82400-121">`T Equals(T)`est spécifié pour effectuer une égalité de valeur, ce qui `Equals` a la valeur true si et seulement si tous les champs d’instance accessibles dans le récepteur sont égaux aux champs du paramètre et `this.EqualityContract` est égal à `other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="82400-121">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all accessible instance fields in the receiver are equal to the fields of the parameter and `this.EqualityContract` equals `other.EqualityContract`.</span></span>

<span data-ttu-id="82400-122">`object.Equals`exécute l’équivalent de</span><span class="sxs-lookup"><span data-stu-id="82400-122">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="82400-123">Copier et cloner des membres</span><span class="sxs-lookup"><span data-stu-id="82400-123">Copy and Clone members</span></span>

<span data-ttu-id="82400-124">Un type d’enregistrement contient deux membres de copie synthétisée :</span><span class="sxs-lookup"><span data-stu-id="82400-124">A record type contains two synthesized copying members:</span></span>

* <span data-ttu-id="82400-125">Constructeur protégé acceptant un seul argument du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="82400-125">A protected constructor taking a single argument of the record type.</span></span>
* <span data-ttu-id="82400-126">Méthode « Clone » d’instance virtuelle sans paramètre publique avec un nom réservé par le compilateur</span><span class="sxs-lookup"><span data-stu-id="82400-126">A public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="82400-127">Le constructeur protégé est appelé « constructeur de copie » et le corps synthétisé copie les valeurs de tous les champs d’instance accessibles dans le type d’entrée dans les champs correspondants de `this` .</span><span class="sxs-lookup"><span data-stu-id="82400-127">The protected constructor is referred to as the "copy constructor" and the synthesized body copies the values of all accessible instance fields in the input type to the corresponding fields of `this`.</span></span>

<span data-ttu-id="82400-128">La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie.</span><span class="sxs-lookup"><span data-stu-id="82400-128">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="82400-129">Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="82400-129">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="82400-130">Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="82400-130">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="82400-131">La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une.</span><span class="sxs-lookup"><span data-stu-id="82400-131">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="82400-132">Une erreur est générée si la méthode Clone du type de base est sealed.</span><span class="sxs-lookup"><span data-stu-id="82400-132">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="82400-133">Si l’enregistrement conteneur est abstrait, la méthode de clonage synthétisé est également abstraite.</span><span class="sxs-lookup"><span data-stu-id="82400-133">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="82400-134">Membres d’enregistrement positionnel</span><span class="sxs-lookup"><span data-stu-id="82400-134">Positional record members</span></span>

<span data-ttu-id="82400-135">Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="82400-135">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="82400-136">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="82400-136">Primary Constructor</span></span>

<span data-ttu-id="82400-137">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="82400-137">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="82400-138">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="82400-138">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="82400-139">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="82400-139">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="82400-140">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="82400-140">At runtime the primary constructor</span></span>

1. <span data-ttu-id="82400-141">exécute les initialiseurs d’instance qui apparaissent dans le corps de classe</span><span class="sxs-lookup"><span data-stu-id="82400-141">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="82400-142">appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="82400-142">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>


### <a name="properties"></a><span data-ttu-id="82400-143">Propriétés</span><span class="sxs-lookup"><span data-stu-id="82400-143">Properties</span></span>

<span data-ttu-id="82400-144">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="82400-144">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="82400-145">Pour un enregistrement :</span><span class="sxs-lookup"><span data-stu-id="82400-145">For a record:</span></span>

* <span data-ttu-id="82400-146">Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).</span><span class="sxs-lookup"><span data-stu-id="82400-146">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="82400-147">Chaque accesseur abstrait hérité « correspondant » est substitué.</span><span class="sxs-lookup"><span data-stu-id="82400-147">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="82400-148">La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="82400-148">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="82400-149">Déconstruire</span><span class="sxs-lookup"><span data-stu-id="82400-149">Deconstruct</span></span>

<span data-ttu-id="82400-150">Un enregistrement positionnel synthétise une méthode publique de Returning void appelée Deconstruct avec une déclaration de paramètre out pour chaque paramètre de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="82400-150">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="82400-151">Chaque paramètre de la méthode Deconstruct a le même type que le paramètre correspondant de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="82400-151">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="82400-152">Le corps de la méthode assigne à la valeur d’un membre d’instance un accès à un membre du même nom.</span><span class="sxs-lookup"><span data-stu-id="82400-152">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="82400-153">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="82400-153">`with` expression</span></span>

<span data-ttu-id="82400-154">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="82400-154">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="82400-155">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="82400-155">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="82400-156">Une `with` expression valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="82400-156">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="82400-157">Le type de récepteur doit contenir une méthode « Clone » d’enregistrement synthétisé accessible.</span><span class="sxs-lookup"><span data-stu-id="82400-157">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="82400-158">Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="82400-158">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="82400-159">Chaque `member_initializer` est traité de la même façon qu’une assignation à un champ ou à un accès à une propriété de la valeur de retour de la méthode Clone d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="82400-159">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="82400-160">La méthode Clone n’est exécutée qu’une seule fois et les affectations sont traitées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="82400-160">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
