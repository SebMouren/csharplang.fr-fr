---
ms.openlocfilehash: d4fc057e4bd56562d1b861200cf6e9f2b4e8d0ff
ms.sourcegitcommit: dd3261cfde7cfefc4e50fd79b21ec2d38b84e27e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85766252"
---

# <a name="records"></a><span data-ttu-id="1b68c-101">Enregistrements</span><span class="sxs-lookup"><span data-stu-id="1b68c-101">Records</span></span>

<span data-ttu-id="1b68c-102">Cette proposition effectue le suivi de la spécification de la fonctionnalité d’enregistrements C# 9, comme convenu par l’équipe de conception du langage C#.</span><span class="sxs-lookup"><span data-stu-id="1b68c-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="1b68c-103">La syntaxe d’un enregistrement est la suivante :</span><span class="sxs-lookup"><span data-stu-id="1b68c-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="1b68c-104">Les types d’enregistrements sont des types référence, similaires à une déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="1b68c-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="1b68c-105">Si un enregistrement `record_base` `argument_list` `record_declaration` ne contient pas de, il s’agit d’une erreur `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="1b68c-106">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="1b68c-106">Members of a record type</span></span>

<span data-ttu-id="1b68c-107">En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="1b68c-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="1b68c-108">Les membres sont synthétisés, sauf si un membre avec une signature « correspondante » est déclaré dans le corps de l’enregistrement ou si un membre non virtuel concret accessible avec une signature « correspondante » est hérité.</span><span class="sxs-lookup"><span data-stu-id="1b68c-108">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="1b68c-109">Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.</span><span class="sxs-lookup"><span data-stu-id="1b68c-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="1b68c-110">Les membres synthétisés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1b68c-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="1b68c-111">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="1b68c-111">Equality members</span></span>

<span data-ttu-id="1b68c-112">Le type d’enregistrement comprend une `EqualityContract` propriété virtuelle en lecture seule synthétisée.</span><span class="sxs-lookup"><span data-stu-id="1b68c-112">The record type includes a synthesized `EqualityContract` readonly virtual property.</span></span> <span data-ttu-id="1b68c-113">La propriété est substituée dans chaque type d’enregistrement dérivé.</span><span class="sxs-lookup"><span data-stu-id="1b68c-113">The property is overridden in each derived record type.</span></span>
<span data-ttu-id="1b68c-114">La propriété peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-114">The property can be declared explicitly.</span></span>
<span data-ttu-id="1b68c-115">Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou l’accessibilité attendue, ou si la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-115">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>
<span data-ttu-id="1b68c-116">La propriété synthétisée retourne `typeof(R)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-116">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>
```C#
protected virtual Type EqualityContract { get; };
```
<span data-ttu-id="1b68c-117">_Pouvez-vous omettre `EqualityContract` si le type d’enregistrement est `sealed` et dérive de `System.Object` ?_</span><span class="sxs-lookup"><span data-stu-id="1b68c-117">_Can we omit `EqualityContract` if the record type is `sealed` and derives from `System.Object`?_</span></span>

<span data-ttu-id="1b68c-118">Le type d’enregistrement implémente `System.IEquatable<R>` et comprend une surcharge fortement typée synthétisée de `Equals(R? other)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-118">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="1b68c-119">La méthode est `public` , et la méthode est, `virtual` sauf si le type d’enregistrement est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-119">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="1b68c-120">La méthode peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-120">The method can be declared explicitly.</span></span>
<span data-ttu-id="1b68c-121">Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou à l’accessibilité attendue, ou que la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-121">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>
```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="1b68c-122">La synthèse `Equals(R?)` retourne `true` si et seulement si chacun des éléments suivants est `true` :</span><span class="sxs-lookup"><span data-stu-id="1b68c-122">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="1b68c-123">`other`n’est pas `null` , et</span><span class="sxs-lookup"><span data-stu-id="1b68c-123">`other` is not `null`, and</span></span>
- <span data-ttu-id="1b68c-124">Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="1b68c-124">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="1b68c-125">S’il existe un type d’enregistrement de base, la valeur de `base.Equals(other)` (un appel non virtuel à `public virtual bool Equals(Base? other)` ); sinon, la valeur de `EqualityContract == other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-125">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="1b68c-126">Si le type d’enregistrement est dérivé d’un type d’enregistrement de base `Base` , le type d’enregistrement comprend une substitution synthétisée de l’entrée fortement typée `Equals(Base other)` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-126">If the record type is derived from a base record type `Base`, the record type includes a synthesized override of the strongly-typed `Equals(Base other)`.</span></span>
<span data-ttu-id="1b68c-127">La substitution synthétisée est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-127">The synthesized override is `sealed`.</span></span>
<span data-ttu-id="1b68c-128">Il s’agit d’une erreur si la substitution est déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-128">It is an error if the override is declared explicitly.</span></span>
<span data-ttu-id="1b68c-129">La valeur de substitution synthétisée est retournée `Equals((object?)other)` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-129">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="1b68c-130">Le type d’enregistrement comprend une substitution synthétisée de `object.Equals(object? obj)` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-130">The record type includes a synthesized override of `object.Equals(object? obj)`.</span></span>
<span data-ttu-id="1b68c-131">Il s’agit d’une erreur si la substitution est déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-131">It is an error if the override is declared explicitly.</span></span>
<span data-ttu-id="1b68c-132">La valeur de remplacement synthétisé retourne `Equals(other as R)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-132">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>
```C#
public override bool Equals(object? obj);
```

<span data-ttu-id="1b68c-133">Le type d’enregistrement comprend une substitution synthétisée de `object.GetHashCode()` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-133">The record type includes a synthesized override of `object.GetHashCode()`.</span></span>
<span data-ttu-id="1b68c-134">La méthode peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-134">The method can be declared explicitly.</span></span>
<span data-ttu-id="1b68c-135">Il s’agit d’une erreur si la déclaration explicite est `sealed` , sauf si le type d’enregistrement est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-135">It is an error if the explicit declaration is `sealed` unless the record type is `sealed`.</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="1b68c-136">Un avertissement est signalé si l’un des `Equals(R?)` et `GetHashCode()` est explicitement déclaré, mais que l’autre méthode n’est pas explicite.</span><span class="sxs-lookup"><span data-stu-id="1b68c-136">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="1b68c-137">La substitution synthétisée de `GetHashCode()` retourne un `int` résultat d’une fonction déterministe combinant les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="1b68c-137">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="1b68c-138">Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="1b68c-138">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="1b68c-139">S’il existe un type d’enregistrement de base, la valeur de `base.GetHashCode()` ; sinon, la valeur de `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-139">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="1b68c-140">Par exemple, considérez les types d’enregistrements suivants :</span><span class="sxs-lookup"><span data-stu-id="1b68c-140">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R2(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="1b68c-141">Pour ces types d’enregistrements, les membres synthétisés ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="1b68c-141">For those record types, the synthesized members would be something like:</span></span>
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; set; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; set; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="1b68c-142">Copier et cloner des membres</span><span class="sxs-lookup"><span data-stu-id="1b68c-142">Copy and Clone members</span></span>

<span data-ttu-id="1b68c-143">Un type d’enregistrement contient deux membres de copie :</span><span class="sxs-lookup"><span data-stu-id="1b68c-143">A record type contains two copying members:</span></span>

* <span data-ttu-id="1b68c-144">Constructeur acceptant un seul argument du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-144">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="1b68c-145">Il est appelé « constructeur de copie ».</span><span class="sxs-lookup"><span data-stu-id="1b68c-145">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="1b68c-146">Méthode « Clone » d’instance virtuelle sans paramètre publique synthétisée avec un nom réservé par le compilateur</span><span class="sxs-lookup"><span data-stu-id="1b68c-146">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="1b68c-147">L’objectif du constructeur de copie est de copier l’état du paramètre vers la nouvelle instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="1b68c-147">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="1b68c-148">Ce constructeur n’exécute aucun initialiseur de champ/propriété d’instance présent dans la déclaration d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-148">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="1b68c-149">Si le constructeur n’est pas explicitement déclaré, un constructeur protégé est synthétisé par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="1b68c-149">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="1b68c-150">La première chose que le constructeur doit faire, est d’appeler un constructeur de copie de la base, ou un constructeur d’objet sans paramètre si l’enregistrement hérite de l’objet.</span><span class="sxs-lookup"><span data-stu-id="1b68c-150">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="1b68c-151">Une erreur est signalée si un constructeur de copie défini par l’utilisateur utilise un initialiseur de constructeur implicite ou explicite qui ne respecte pas cette exigence.</span><span class="sxs-lookup"><span data-stu-id="1b68c-151">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="1b68c-152">Après l’appel d’un constructeur de copie de base, un constructeur de copie synthétisé copie les valeurs de tous les champs d’instance implicitement ou explicitement déclarés dans le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-152">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="1b68c-153">La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie.</span><span class="sxs-lookup"><span data-stu-id="1b68c-153">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="1b68c-154">Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="1b68c-154">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="1b68c-155">Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="1b68c-155">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="1b68c-156">La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une.</span><span class="sxs-lookup"><span data-stu-id="1b68c-156">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="1b68c-157">Une erreur est générée si la méthode Clone du type de base est sealed.</span><span class="sxs-lookup"><span data-stu-id="1b68c-157">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="1b68c-158">Si l’enregistrement conteneur est abstrait, la méthode de clonage synthétisé est également abstraite.</span><span class="sxs-lookup"><span data-stu-id="1b68c-158">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="1b68c-159">Membres d’enregistrement positionnel</span><span class="sxs-lookup"><span data-stu-id="1b68c-159">Positional record members</span></span>

<span data-ttu-id="1b68c-160">Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1b68c-160">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="1b68c-161">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="1b68c-161">Primary Constructor</span></span>

<span data-ttu-id="1b68c-162">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="1b68c-162">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="1b68c-163">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="1b68c-163">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="1b68c-164">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="1b68c-164">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="1b68c-165">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="1b68c-165">At runtime the primary constructor</span></span>

1. <span data-ttu-id="1b68c-166">exécute les initialiseurs d’instance qui apparaissent dans le corps de classe</span><span class="sxs-lookup"><span data-stu-id="1b68c-166">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="1b68c-167">appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="1b68c-167">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="1b68c-168">Si un enregistrement a un constructeur principal, tout constructeur défini par l’utilisateur, à l’exception de « constructeur de copie », doit avoir un `this` initialiseur de constructeur explicite.</span><span class="sxs-lookup"><span data-stu-id="1b68c-168">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="1b68c-169">Les paramètres du constructeur principal ainsi que les membres de l’enregistrement se trouvent dans la portée au sein `argument_list` de la `record_base` clause et dans les initialiseurs de champs d’instance ou de propriétés.</span><span class="sxs-lookup"><span data-stu-id="1b68c-169">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="1b68c-170">Les membres d’instance seraient une erreur à ces emplacements (à l’instar de la façon dont les membres d’instance sont dans la portée dans les initialiseurs de constructeur standard aujourd’hui, mais une erreur à utiliser), mais les paramètres du constructeur principal seraient dans la portée et utilisables et créeraient un cliché instantané des membres.</span><span class="sxs-lookup"><span data-stu-id="1b68c-170">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="1b68c-171">Les membres statiques seraient également utilisables, de la même façon que les appels de base et les initialiseurs fonctionnent aujourd’hui dans les constructeurs ordinaires.</span><span class="sxs-lookup"><span data-stu-id="1b68c-171">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="1b68c-172">Les variables d’expression déclarées dans le `argument_list` sont dans la portée dans le `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-172">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="1b68c-173">Les mêmes règles d’occultation qu’au sein d’une liste d’arguments d’un initialiseur de constructeur normal s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="1b68c-173">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="1b68c-174">Propriétés</span><span class="sxs-lookup"><span data-stu-id="1b68c-174">Properties</span></span>

<span data-ttu-id="1b68c-175">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="1b68c-175">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="1b68c-176">Pour un enregistrement :</span><span class="sxs-lookup"><span data-stu-id="1b68c-176">For a record:</span></span>

* <span data-ttu-id="1b68c-177">Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).</span><span class="sxs-lookup"><span data-stu-id="1b68c-177">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="1b68c-178">Une propriété héritée `abstract` avec le type correspondant est substituée.</span><span class="sxs-lookup"><span data-stu-id="1b68c-178">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="1b68c-179">Il s’agit d’une erreur si la propriété héritée n’a pas `public` `get` de `init` accesseurs substituables.</span><span class="sxs-lookup"><span data-stu-id="1b68c-179">It is an error if the inherited property does not have `public` overridable `get` and `init` accessors.</span></span>
  <span data-ttu-id="1b68c-180">La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="1b68c-180">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="1b68c-181">Déconstruire</span><span class="sxs-lookup"><span data-stu-id="1b68c-181">Deconstruct</span></span>

<span data-ttu-id="1b68c-182">Un enregistrement positionnel synthétise une méthode publique de Returning void appelée Deconstruct avec une déclaration de paramètre out pour chaque paramètre de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="1b68c-182">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="1b68c-183">Chaque paramètre de la méthode Deconstruct a le même type que le paramètre correspondant de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="1b68c-183">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="1b68c-184">Le corps de la méthode assigne à la valeur d’un membre d’instance un accès à un membre du même nom.</span><span class="sxs-lookup"><span data-stu-id="1b68c-184">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="1b68c-185">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="1b68c-185">`with` expression</span></span>

<span data-ttu-id="1b68c-186">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="1b68c-186">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="1b68c-187">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="1b68c-187">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="1b68c-188">Une `with` expression valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="1b68c-188">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="1b68c-189">Le type de récepteur doit contenir une méthode « Clone » d’enregistrement synthétisé accessible.</span><span class="sxs-lookup"><span data-stu-id="1b68c-189">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="1b68c-190">Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="1b68c-190">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="1b68c-191">Chaque `member_initializer` est traité de la même façon qu’une assignation à un champ ou à un accès à une propriété de la valeur de retour de la méthode Clone d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1b68c-191">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="1b68c-192">La méthode Clone n’est exécutée qu’une seule fois et les affectations sont traitées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="1b68c-192">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
