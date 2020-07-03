---
ms.openlocfilehash: 42d2b5f1c9bb58ed3d849f5a2e4d0d1e281c5bf4
ms.sourcegitcommit: ce26928a60a4b57e4174ec630c2dd6df99904feb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85914121"
---

# <a name="records"></a><span data-ttu-id="ef7c5-101">Enregistrements</span><span class="sxs-lookup"><span data-stu-id="ef7c5-101">Records</span></span>

<span data-ttu-id="ef7c5-102">Cette proposition effectue le suivi de la spécification de la fonctionnalité d’enregistrements C# 9, comme convenu par l’équipe de conception du langage C#.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="ef7c5-103">La syntaxe d’un enregistrement est la suivante :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="ef7c5-104">Les types d’enregistrements sont des types référence, similaires à une déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="ef7c5-105">Si un enregistrement `record_base` `argument_list` `record_declaration` ne contient pas de, il s’agit d’une erreur `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="inheritance"></a><span data-ttu-id="ef7c5-106">Héritage</span><span class="sxs-lookup"><span data-stu-id="ef7c5-106">Inheritance</span></span>

<span data-ttu-id="ef7c5-107">Les enregistrements ne peuvent pas hériter de classes, sauf si la classe est `object` , et les classes ne peuvent pas hériter d’enregistrements.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-107">Records cannot inherit from classes, unless the class is `object`, and classes cannot inherit from records.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="ef7c5-108">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="ef7c5-108">Members of a record type</span></span>

<span data-ttu-id="ef7c5-109">En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-109">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="ef7c5-110">Les membres sont synthétisés, sauf si un membre avec une signature « correspondante » est déclaré dans le corps de l’enregistrement ou si un membre non virtuel concret accessible avec une signature « correspondante » est hérité.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-110">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="ef7c5-111">Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-111">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="ef7c5-112">Les membres synthétisés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-112">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="ef7c5-113">Membres d’égalité</span><span class="sxs-lookup"><span data-stu-id="ef7c5-113">Equality members</span></span>

<span data-ttu-id="ef7c5-114">Si l’enregistrement est dérivé de `object` , le type d’enregistrement contient une propriété ReadOnly synthétisée</span><span class="sxs-lookup"><span data-stu-id="ef7c5-114">If the record is derived from `object`, the record type includes a synthesized readonly property</span></span>
```C#
protected virtual Type EqualityContract { get; };
```
<span data-ttu-id="ef7c5-115">La propriété peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-115">The property can be declared explicitly.</span></span> <span data-ttu-id="ef7c5-116">Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou l’accessibilité attendue, ou si la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-116">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>

<span data-ttu-id="ef7c5-117">Si le type d’enregistrement est dérivé d’un type d’enregistrement de base `Base` , le type d’enregistrement comprend une propriété ReadOnly synthétisée</span><span class="sxs-lookup"><span data-stu-id="ef7c5-117">If the record type is derived from a base record type `Base`, the record type includes a synthesized readonly property</span></span>
```C#
protected override Type EqualityContract { get; };
```

<span data-ttu-id="ef7c5-118">La propriété peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-118">The property can be declared explicitly.</span></span> <span data-ttu-id="ef7c5-119">Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou l’accessibilité attendue, ou si la déclaration explicite est `sealed` et que le type d’enregistrement n’est pas `sealed` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-119">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration is `sealed` and the record type is not `sealed`.</span></span> <span data-ttu-id="ef7c5-120">Il s’agit d’une erreur si une propriété synthétisée ou explicitement déclarée ne peut pas substituer une propriété avec cette signature dans le type d’enregistrement `Base` (par exemple, si la propriété est manquante dans `Base` , ou sealed, ou non virtuelle, etc.).</span><span class="sxs-lookup"><span data-stu-id="ef7c5-120">It is an error if either synthesized, or explicitly declared property cannot override a property with this signature in the record type `Base` (for example, if the property is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="ef7c5-121">La propriété synthétisée retourne `typeof(R)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-121">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>

<span data-ttu-id="ef7c5-122">_Pouvez-vous omettre `EqualityContract` si le type d’enregistrement est `sealed` et dérive de `System.Object` ?_</span><span class="sxs-lookup"><span data-stu-id="ef7c5-122">_Can we omit `EqualityContract` if the record type is `sealed` and derives from `System.Object`?_</span></span>

<span data-ttu-id="ef7c5-123">Le type d’enregistrement implémente `System.IEquatable<R>` et comprend une surcharge fortement typée synthétisée de `Equals(R? other)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-123">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="ef7c5-124">La méthode est `public` , et la méthode est, `virtual` sauf si le type d’enregistrement est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-124">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="ef7c5-125">La méthode peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-125">The method can be declared explicitly.</span></span>
<span data-ttu-id="ef7c5-126">Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou à l’accessibilité attendue, ou que la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-126">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>
```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="ef7c5-127">La synthèse `Equals(R?)` retourne `true` si et seulement si chacun des éléments suivants est `true` :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-127">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="ef7c5-128">`other`n’est pas `null` , et</span><span class="sxs-lookup"><span data-stu-id="ef7c5-128">`other` is not `null`, and</span></span>
- <span data-ttu-id="ef7c5-129">Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="ef7c5-129">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="ef7c5-130">S’il existe un type d’enregistrement de base, la valeur de `base.Equals(other)` (un appel non virtuel à `public virtual bool Equals(Base? other)` ); sinon, la valeur de `EqualityContract == other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-130">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="ef7c5-131">Si le type d’enregistrement est dérivé d’un type d’enregistrement de base `Base` , le type d’enregistrement contient une substitution synthétisée</span><span class="sxs-lookup"><span data-stu-id="ef7c5-131">If the record type is derived from a base record type `Base`, the record type includes a synthesized override</span></span> 
```C#
public sealed override bool Equals(Base? other);
```
<span data-ttu-id="ef7c5-132">Il s’agit d’une erreur si la substitution est déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-132">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="ef7c5-133">Il s’agit d’une erreur si la méthode ne peut pas substituer une méthode avec la même signature dans le type d’enregistrement `Base` (par exemple, si la méthode est manquante dans le `Base` , ou sealed, ou non virtuelle, etc.).</span><span class="sxs-lookup"><span data-stu-id="ef7c5-133">It is an error if the method cannot override a method with same signature in record type `Base` (for example, if the method is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="ef7c5-134">La valeur de substitution synthétisée est retournée `Equals((object?)other)` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-134">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="ef7c5-135">Le type d’enregistrement comprend une substitution synthétisée</span><span class="sxs-lookup"><span data-stu-id="ef7c5-135">The record type includes a synthesized override</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="ef7c5-136">Il s’agit d’une erreur si la substitution est déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-136">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="ef7c5-137">Il s’agit d’une erreur si la méthode n’est pas substituée `object.Equals(object? obj)` (par exemple, en raison de l’occultation dans les types de base intermédiaires, etc.).</span><span class="sxs-lookup"><span data-stu-id="ef7c5-137">It is an error if the method doesn't override `object.Equals(object? obj)` (for example, due to shadowing in intermediate base types, etc.).</span></span>
<span data-ttu-id="ef7c5-138">La valeur de remplacement synthétisé retourne `Equals(other as R)` où `R` est le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-138">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>

<span data-ttu-id="ef7c5-139">Le type d’enregistrement comprend une substitution synthétisée</span><span class="sxs-lookup"><span data-stu-id="ef7c5-139">The record type includes a synthesized override</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="ef7c5-140">La méthode peut être déclarée explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-140">The method can be declared explicitly.</span></span>
<span data-ttu-id="ef7c5-141">Il s’agit d’une erreur si la déclaration explicite est `sealed` , sauf si le type d’enregistrement est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-141">It is an error if the explicit declaration is `sealed` unless the record type is `sealed`.</span></span> <span data-ttu-id="ef7c5-142">Il s’agit d’une erreur si une méthode synthétisée ou explicitement déclarée ne se substitue pas `object.GetHashCode()` (par exemple, en raison de l’occultation dans les types de base intermédiaires, etc.).</span><span class="sxs-lookup"><span data-stu-id="ef7c5-142">It is an error if either synthesized, or explicitly declared method doesn't override `object.GetHashCode()` (for example, due to shadowing in intermediate base types, etc.).</span></span>
 
<span data-ttu-id="ef7c5-143">Un avertissement est signalé si l’un des `Equals(R?)` et `GetHashCode()` est explicitement déclaré, mais que l’autre méthode n’est pas explicite.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-143">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="ef7c5-144">La substitution synthétisée de `GetHashCode()` retourne un `int` résultat d’une fonction déterministe combinant les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-144">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="ef7c5-145">Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` où `TN` est le type de champ, et</span><span class="sxs-lookup"><span data-stu-id="ef7c5-145">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="ef7c5-146">S’il existe un type d’enregistrement de base, la valeur de `base.GetHashCode()` ; sinon, la valeur de `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-146">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="ef7c5-147">Par exemple, considérez les types d’enregistrements suivants :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-147">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R2(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="ef7c5-148">Pour ces types d’enregistrements, les membres synthétisés ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-148">For those record types, the synthesized members would be something like:</span></span>
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

### <a name="copy-and-clone-members"></a><span data-ttu-id="ef7c5-149">Copier et cloner des membres</span><span class="sxs-lookup"><span data-stu-id="ef7c5-149">Copy and Clone members</span></span>

<span data-ttu-id="ef7c5-150">Un type d’enregistrement contient deux membres de copie :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-150">A record type contains two copying members:</span></span>

* <span data-ttu-id="ef7c5-151">Constructeur acceptant un seul argument du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-151">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="ef7c5-152">Il est appelé « constructeur de copie ».</span><span class="sxs-lookup"><span data-stu-id="ef7c5-152">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="ef7c5-153">Méthode « Clone » d’instance virtuelle sans paramètre publique synthétisée avec un nom réservé par le compilateur</span><span class="sxs-lookup"><span data-stu-id="ef7c5-153">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="ef7c5-154">L’objectif du constructeur de copie est de copier l’état du paramètre vers la nouvelle instance en cours de création.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-154">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="ef7c5-155">Ce constructeur n’exécute aucun initialiseur de champ/propriété d’instance présent dans la déclaration d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-155">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="ef7c5-156">Si le constructeur n’est pas explicitement déclaré, un constructeur protégé est synthétisé par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-156">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="ef7c5-157">La première chose que le constructeur doit faire, est d’appeler un constructeur de copie de la base, ou un constructeur d’objet sans paramètre si l’enregistrement hérite de l’objet.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-157">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="ef7c5-158">Une erreur est signalée si un constructeur de copie défini par l’utilisateur utilise un initialiseur de constructeur implicite ou explicite qui ne respecte pas cette exigence.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-158">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="ef7c5-159">Après l’appel d’un constructeur de copie de base, un constructeur de copie synthétisé copie les valeurs de tous les champs d’instance implicitement ou explicitement déclarés dans le type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-159">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="ef7c5-160">La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-160">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="ef7c5-161">Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-161">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="ef7c5-162">Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-162">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="ef7c5-163">La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-163">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="ef7c5-164">Une erreur est générée si la méthode Clone du type de base est sealed.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-164">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="ef7c5-165">Si l’enregistrement conteneur est abstrait, la méthode de clonage synthétisé est également abstraite.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-165">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="ef7c5-166">Membres d’enregistrement positionnel</span><span class="sxs-lookup"><span data-stu-id="ef7c5-166">Positional record members</span></span>

<span data-ttu-id="ef7c5-167">Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-167">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="ef7c5-168">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="ef7c5-168">Primary Constructor</span></span>

<span data-ttu-id="ef7c5-169">Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-169">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="ef7c5-170">C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-170">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="ef7c5-171">C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-171">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="ef7c5-172">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="ef7c5-172">At runtime the primary constructor</span></span>

1. <span data-ttu-id="ef7c5-173">exécute les initialiseurs d’instance qui apparaissent dans le corps de classe</span><span class="sxs-lookup"><span data-stu-id="ef7c5-173">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="ef7c5-174">appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-174">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="ef7c5-175">Si un enregistrement a un constructeur principal, tout constructeur défini par l’utilisateur, à l’exception de « constructeur de copie », doit avoir un `this` initialiseur de constructeur explicite.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-175">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="ef7c5-176">Les paramètres du constructeur principal ainsi que les membres de l’enregistrement se trouvent dans la portée au sein `argument_list` de la `record_base` clause et dans les initialiseurs de champs d’instance ou de propriétés.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-176">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="ef7c5-177">Les membres d’instance seraient une erreur à ces emplacements (à l’instar de la façon dont les membres d’instance sont dans la portée dans les initialiseurs de constructeur standard aujourd’hui, mais une erreur à utiliser), mais les paramètres du constructeur principal seraient dans la portée et utilisables et créeraient un cliché instantané des membres.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-177">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="ef7c5-178">Les membres statiques seraient également utilisables, de la même façon que les appels de base et les initialiseurs fonctionnent aujourd’hui dans les constructeurs ordinaires.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-178">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="ef7c5-179">Les variables d’expression déclarées dans le `argument_list` sont dans la portée dans le `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-179">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="ef7c5-180">Les mêmes règles d’occultation qu’au sein d’une liste d’arguments d’un initialiseur de constructeur normal s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-180">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="ef7c5-181">Propriétés</span><span class="sxs-lookup"><span data-stu-id="ef7c5-181">Properties</span></span>

<span data-ttu-id="ef7c5-182">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-182">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="ef7c5-183">Pour un enregistrement :</span><span class="sxs-lookup"><span data-stu-id="ef7c5-183">For a record:</span></span>

* <span data-ttu-id="ef7c5-184">Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).</span><span class="sxs-lookup"><span data-stu-id="ef7c5-184">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="ef7c5-185">Une propriété héritée `abstract` avec le type correspondant est substituée.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-185">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="ef7c5-186">Il s’agit d’une erreur si la propriété héritée n’a pas `public` `get` de `init` accesseurs substituables.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-186">It is an error if the inherited property does not have `public` overridable `get` and `init` accessors.</span></span>
  <span data-ttu-id="ef7c5-187">La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-187">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="ef7c5-188">Déconstruire</span><span class="sxs-lookup"><span data-stu-id="ef7c5-188">Deconstruct</span></span>

<span data-ttu-id="ef7c5-189">Un enregistrement positionnel synthétise une méthode publique de Returning void appelée Deconstruct avec une déclaration de paramètre out pour chaque paramètre de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-189">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="ef7c5-190">Chaque paramètre de la méthode Deconstruct a le même type que le paramètre correspondant de la déclaration du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-190">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="ef7c5-191">Le corps de la méthode assigne à la valeur d’un membre d’instance un accès à un membre du même nom.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-191">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="ef7c5-192">Expression `with`</span><span class="sxs-lookup"><span data-stu-id="ef7c5-192">`with` expression</span></span>

<span data-ttu-id="ef7c5-193">Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-193">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="ef7c5-194">Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="ef7c5-194">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="ef7c5-195">Une `with` expression valide a un récepteur avec un type non void.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-195">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="ef7c5-196">Le type de récepteur doit contenir une méthode « Clone » d’enregistrement synthétisé accessible.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-196">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="ef7c5-197">Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-197">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="ef7c5-198">Chaque `member_initializer` est traité de la même façon qu’une assignation à un champ ou à un accès à une propriété de la valeur de retour de la méthode Clone d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-198">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="ef7c5-199">La méthode Clone n’est exécutée qu’une seule fois et les affectations sont traitées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="ef7c5-199">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
