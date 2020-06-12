---
ms.openlocfilehash: 35f6836e20776450ce5f776e7fdb66ca634d04a0
ms.sourcegitcommit: 0f56445e250ddf82b88848b94c59870f13ab8ffc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84663265"
---

# <a name="records"></a><span data-ttu-id="0ca18-101">Enregistrements</span><span class="sxs-lookup"><span data-stu-id="0ca18-101">Records</span></span>

<span data-ttu-id="0ca18-102">Cette proposition effectue le suivi de la spécification de la fonctionnalité d’enregistrements C# 9, comme convenu par l’équipe de conception du langage C#.</span><span class="sxs-lookup"><span data-stu-id="0ca18-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="0ca18-103">La syntaxe d’un enregistrement est la suivante :</span><span class="sxs-lookup"><span data-stu-id="0ca18-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="0ca18-104">Les types d’enregistrements sont des types référence, similaires à une déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="0ca18-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="0ca18-105">Si un enregistrement `record_base` `argument_list` `record_declaration` ne contient pas de, il s’agit d’une erreur `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="0ca18-106">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="0ca18-106">Members of a record type</span></span>

<span data-ttu-id="0ca18-107">En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0ca18-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="0ca18-108">Les membres sont synthétisés, sauf si un membre avec une signature « correspondante » est déclaré dans le corps de l’enregistrement ou si un membre non virtuel concret accessible avec une signature « correspondante » est hérité.</span><span class="sxs-lookup"><span data-stu-id="0ca18-108">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="0ca18-109">Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.</span><span class="sxs-lookup"><span data-stu-id="0ca18-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="0ca18-110">Les membres synthétisés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="0ca18-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="0ca18-111">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="0ca18-111">Equality members</span></span>

<span data-ttu-id="0ca18-112">Les types d’enregistrements produisent des implémentations synthétisées des méthodes suivantes, où `T` est le type conteneur :</span><span class="sxs-lookup"><span data-stu-id="0ca18-112">Record types produce synthesized implementations of the following methods, where `T` is the containing type:</span></span>
```C#
public override int GetHashCode();
public override bool Equals(object other);
public virtual bool Equals(T other);
```
<span data-ttu-id="0ca18-113">`GetHashCode()`et `Equals(object other)` sont des substitutions des méthodes virtuelles dans `System.Object` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-113">`GetHashCode()` and `Equals(object other)` are overrides of the virtual methods in `System.Object`.</span></span>
<span data-ttu-id="0ca18-114">Toutes les méthodes sur les classes de base intermédiaires qui masquent ces méthodes sont ignorées lors de la substitution.</span><span class="sxs-lookup"><span data-stu-id="0ca18-114">Any methods on intermediate base classes that would hide those methods are ignored when overriding.</span></span>

<span data-ttu-id="0ca18-115">Les types d’enregistrements dérivés substituent également la `Equals(TBase other)` méthode à partir de chaque type d’enregistrement de base.</span><span class="sxs-lookup"><span data-stu-id="0ca18-115">Derived record types also override the `Equals(TBase other)` method from each base record type.</span></span>

<span data-ttu-id="0ca18-116">Le type d’enregistrement synthétise une implémentation de `System.IEquatable<T>` qui est implicitement implémentée par `Equals(T other)` où `T` est le type conteneur.</span><span class="sxs-lookup"><span data-stu-id="0ca18-116">The record type synthesizes an implementation of `System.IEquatable<T>` that is implicitly implemented by `Equals(T other)` where `T` is the containing type.</span></span>
<span data-ttu-id="0ca18-117">Les types d’enregistrements ne synthétisent pas les implémentations de `System.IEquatable<TBase>` pour tous les types de base `TBase` , même si ces interfaces sont implémentées par les types d’enregistrements de base.</span><span class="sxs-lookup"><span data-stu-id="0ca18-117">Record types do not synthesize implementations of `System.IEquatable<TBase>` for any base type `TBase`, even if those interfaces are implemented by the base record types.</span></span>

<span data-ttu-id="0ca18-118">La classe d’enregistrement de base synthétise une `EqualityContract` propriété.</span><span class="sxs-lookup"><span data-stu-id="0ca18-118">The base record class synthesizes an `EqualityContract` property.</span></span> <span data-ttu-id="0ca18-119">La propriété est substituée dans les classes d’enregistrement dérivées.</span><span class="sxs-lookup"><span data-stu-id="0ca18-119">The property is overridden in derived record classes.</span></span> <span data-ttu-id="0ca18-120">Les implémentations synthétisées retournent `typeof(T)` où `T` est un type contenant.</span><span class="sxs-lookup"><span data-stu-id="0ca18-120">The synthesized implementations return `typeof(T)` where `T` is containing type.</span></span>
```C#
protected virtual Type EqualityContract { get; }
```

<span data-ttu-id="0ca18-121">Il s’agit d’une erreur si les implémentations de base de l’un des membres substitués sont sealed ou non virtuelles, ou ne correspondent pas à la signature et à l’accessibilité attendues.</span><span class="sxs-lookup"><span data-stu-id="0ca18-121">It is an error if the base implementations of any of the overridden members is sealed or non-virtual, or do not match the expected signature and accessibility.</span></span>

<span data-ttu-id="0ca18-122">`Equals(T other)`retourne la valeur true si et seulement si chacune des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="0ca18-122">`Equals(T other)` returns true if and only if each of the following terms are true:</span></span>
- <span data-ttu-id="0ca18-123">`other`n’est pas `null` , et</span><span class="sxs-lookup"><span data-stu-id="0ca18-123">`other` is not `null`, and</span></span>
- <span data-ttu-id="0ca18-124">Pour chaque champ déclaré dans le type d’enregistrement, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="0ca18-124">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="0ca18-125">S’il existe un type d’enregistrement de base, la valeur de `base.Equals(other)` ; sinon, la valeur de `EqualityContract.Equals(other.EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-125">If there is a base record type, the value of `base.Equals(other)`; otherwise the value of `EqualityContract.Equals(other.EqualityContract)`.</span></span>

<span data-ttu-id="0ca18-126">Les substitutions de `Equals(T other)` pour les méthodes de base, y compris `object.Equals(object other)` , effectuent l’équivalent de :</span><span class="sxs-lookup"><span data-stu-id="0ca18-126">The overrides of `Equals(T other)` for the base methods, including `object.Equals(object other)`, perform the equivalent of:</span></span>
```C#
public override bool Equals(object other) => Equals(other as T);
```

<span data-ttu-id="0ca18-127">`GetHashCode()`retourne le `int` résultat d’une fonction déterministe acceptant les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="0ca18-127">`GetHashCode()` returns the `int` result of a deterministic function taking the following values:</span></span>
- <span data-ttu-id="0ca18-128">Pour chaque champ déclaré dans le type d’enregistrement, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="0ca18-128">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="0ca18-129">S’il existe un type d’enregistrement de base, la valeur de `base.GetHashCode()` ; sinon, la valeur de `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-129">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

### <a name="copy-and-clone-members"></a><span data-ttu-id="0ca18-130">Copier et cloner des membres</span><span class="sxs-lookup"><span data-stu-id="0ca18-130">Copy and Clone members</span></span>

<span data-ttu-id="0ca18-131">Un type d’enregistrement contient deux membres de copie :</span><span class="sxs-lookup"><span data-stu-id="0ca18-131">A record type contains two copying members:</span></span>

* <span data-ttu-id="0ca18-132">Constructeur acceptant un seul argument du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0ca18-132">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="0ca18-133">Il est appelé « constructeur de copie ».</span><span class="sxs-lookup"><span data-stu-id="0ca18-133">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="0ca18-134">Méthode « Clone » d’instance virtuelle sans paramètre publique synthétisée avec un nom réservé par le compilateur</span><span class="sxs-lookup"><span data-stu-id="0ca18-134">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="0ca18-135">L’objectif du constructeur de copie est de copier l’état du paramètre vers la nouvelle instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="0ca18-135">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="0ca18-136">Ce constructeur n’exécute aucun initialiseur de champ/propriété d’instance présent dans la déclaration d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0ca18-136">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="0ca18-137">Si le constructeur n’est pas explicitement déclaré, un constructeur protégé est synthétisé par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="0ca18-137">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="0ca18-138">La première chose que le constructeur doit faire, est d’appeler un constructeur de copie de la base, ou un constructeur d’objet sans paramètre si l’enregistrement hérite de l’objet.</span><span class="sxs-lookup"><span data-stu-id="0ca18-138">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="0ca18-139">Une erreur est signalée si un constructeur de copie défini par l’utilisateur utilise un initialiseur de constructeur implicite ou explicite qui ne respecte pas cette exigence.</span><span class="sxs-lookup"><span data-stu-id="0ca18-139">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="0ca18-140">Après l’appel d’un constructeur de copie de base, un constructeur de copie synthétisé copie les valeurs de tous les champs d’instance implicitement ou explicitement déclarés dans le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0ca18-140">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="0ca18-141">La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie.</span><span class="sxs-lookup"><span data-stu-id="0ca18-141">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="0ca18-142">Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="0ca18-142">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="0ca18-143">Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="0ca18-143">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="0ca18-144">La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une.</span><span class="sxs-lookup"><span data-stu-id="0ca18-144">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="0ca18-145">Une erreur est générée si la méthode Clone du type de base est sealed.</span><span class="sxs-lookup"><span data-stu-id="0ca18-145">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="0ca18-146">Si l’enregistrement conteneur est abstrait, la méthode de clonage synthétisé est également abstraite.</span><span class="sxs-lookup"><span data-stu-id="0ca18-146">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="0ca18-147">Membres d’enregistrement positionnel</span><span class="sxs-lookup"><span data-stu-id="0ca18-147">Positional record members</span></span>

<span data-ttu-id="0ca18-148">Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0ca18-148">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="0ca18-149">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="0ca18-149">Primary Constructor</span></span>

<span data-ttu-id="0ca18-150">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="0ca18-150">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="0ca18-151">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="0ca18-151">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="0ca18-152">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="0ca18-152">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="0ca18-153">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="0ca18-153">At runtime the primary constructor</span></span>

1. <span data-ttu-id="0ca18-154">exécute les initialiseurs d’instance qui apparaissent dans le corps de classe</span><span class="sxs-lookup"><span data-stu-id="0ca18-154">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="0ca18-155">appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="0ca18-155">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="0ca18-156">Si un enregistrement a un constructeur principal, tout constructeur défini par l’utilisateur, à l’exception de « constructeur de copie », doit avoir un `this` initialiseur de constructeur explicite.</span><span class="sxs-lookup"><span data-stu-id="0ca18-156">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="0ca18-157">Les paramètres du constructeur principal ainsi que les membres de l’enregistrement se trouvent dans la portée au sein `argument_list` de la `record_base` clause et dans les initialiseurs de champs d’instance ou de propriétés.</span><span class="sxs-lookup"><span data-stu-id="0ca18-157">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="0ca18-158">Les membres d’instance seraient une erreur à ces emplacements (à l’instar de la façon dont les membres d’instance sont dans la portée dans les initialiseurs de constructeur standard aujourd’hui, mais une erreur à utiliser), mais les paramètres du constructeur principal seraient dans la portée et utilisables et créeraient un cliché instantané des membres.</span><span class="sxs-lookup"><span data-stu-id="0ca18-158">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="0ca18-159">Les membres statiques seraient également utilisables, de la même façon que les appels de base et les initialiseurs fonctionnent aujourd’hui dans les constructeurs ordinaires.</span><span class="sxs-lookup"><span data-stu-id="0ca18-159">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="0ca18-160">Les variables d’expression déclarées dans le `argument_list` sont dans la portée dans le `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-160">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="0ca18-161">Les mêmes règles d’occultation qu’au sein d’une liste d’arguments d’un initialiseur de constructeur normal s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="0ca18-161">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="0ca18-162">Propriétés</span><span class="sxs-lookup"><span data-stu-id="0ca18-162">Properties</span></span>

<span data-ttu-id="0ca18-163">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="0ca18-163">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="0ca18-164">Pour un enregistrement :</span><span class="sxs-lookup"><span data-stu-id="0ca18-164">For a record:</span></span>

* <span data-ttu-id="0ca18-165">Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).</span><span class="sxs-lookup"><span data-stu-id="0ca18-165">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="0ca18-166">Chaque accesseur abstrait hérité « correspondant » est substitué.</span><span class="sxs-lookup"><span data-stu-id="0ca18-166">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="0ca18-167">La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="0ca18-167">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="0ca18-168">Déconstruire</span><span class="sxs-lookup"><span data-stu-id="0ca18-168">Deconstruct</span></span>

<span data-ttu-id="0ca18-169">Un enregistrement positionnel synthétise une méthode publique de Returning void appelée Deconstruct avec une déclaration de paramètre out pour chaque paramètre de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="0ca18-169">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="0ca18-170">Chaque paramètre de la méthode Deconstruct a le même type que le paramètre correspondant de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="0ca18-170">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="0ca18-171">Le corps de la méthode assigne à la valeur d’un membre d’instance un accès à un membre du même nom.</span><span class="sxs-lookup"><span data-stu-id="0ca18-171">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="0ca18-172">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="0ca18-172">`with` expression</span></span>

<span data-ttu-id="0ca18-173">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="0ca18-173">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="0ca18-174">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="0ca18-174">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="0ca18-175">Une `with` expression valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="0ca18-175">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="0ca18-176">Le type de récepteur doit contenir une méthode « Clone » d’enregistrement synthétisé accessible.</span><span class="sxs-lookup"><span data-stu-id="0ca18-176">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="0ca18-177">Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="0ca18-177">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="0ca18-178">Chaque `member_initializer` est traité de la même façon qu’une assignation à un champ ou à un accès à une propriété de la valeur de retour de la méthode Clone d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0ca18-178">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="0ca18-179">La méthode Clone n’est exécutée qu’une seule fois et les affectations sont traitées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="0ca18-179">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
