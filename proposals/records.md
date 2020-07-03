---
ms.openlocfilehash: 42d2b5f1c9bb58ed3d849f5a2e4d0d1e281c5bf4
ms.sourcegitcommit: ce26928a60a4b57e4174ec630c2dd6df99904feb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85914121"
---

# <a name="records"></a>Enregistrements

Cette proposition effectue le suivi de la spécification de la fonctionnalité d’enregistrements C# 9, comme convenu par l’équipe de conception du langage C#.

La syntaxe d’un enregistrement est la suivante :

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

Les types d’enregistrements sont des types référence, similaires à une déclaration de classe. Si un enregistrement `record_base` `argument_list` `record_declaration` ne contient pas de, il s’agit d’une erreur `parameter_list` .

## <a name="inheritance"></a>Héritage

Les enregistrements ne peuvent pas hériter de classes, sauf si la classe est `object` , et les classes ne peuvent pas hériter d’enregistrements.

## <a name="members-of-a-record-type"></a>Membres d’un type d’enregistrement

En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.
Les membres sont synthétisés, sauf si un membre avec une signature « correspondante » est déclaré dans le corps de l’enregistrement ou si un membre non virtuel concret accessible avec une signature « correspondante » est hérité.
Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.

Les membres synthétisés sont les suivants :

### <a name="equality-members"></a>Membres d’égalité

Si l’enregistrement est dérivé de `object` , le type d’enregistrement contient une propriété ReadOnly synthétisée
```C#
protected virtual Type EqualityContract { get; };
```
La propriété peut être déclarée explicitement. Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou l’accessibilité attendue, ou si la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .

Si le type d’enregistrement est dérivé d’un type d’enregistrement de base `Base` , le type d’enregistrement comprend une propriété ReadOnly synthétisée
```C#
protected override Type EqualityContract { get; };
```

La propriété peut être déclarée explicitement. Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou l’accessibilité attendue, ou si la déclaration explicite est `sealed` et que le type d’enregistrement n’est pas `sealed` . Il s’agit d’une erreur si une propriété synthétisée ou explicitement déclarée ne peut pas substituer une propriété avec cette signature dans le type d’enregistrement `Base` (par exemple, si la propriété est manquante dans `Base` , ou sealed, ou non virtuelle, etc.).
La propriété synthétisée retourne `typeof(R)` où `R` est le type d’enregistrement.

_Pouvez-vous omettre `EqualityContract` si le type d’enregistrement est `sealed` et dérive de `System.Object` ?_

Le type d’enregistrement implémente `System.IEquatable<R>` et comprend une surcharge fortement typée synthétisée de `Equals(R? other)` où `R` est le type d’enregistrement.
La méthode est `public` , et la méthode est, `virtual` sauf si le type d’enregistrement est `sealed` .
La méthode peut être déclarée explicitement.
Il s’agit d’une erreur si la déclaration explicite ne correspond pas à la signature ou à l’accessibilité attendue, ou que la déclaration explicite n’est pas `virtual` et que le type d’enregistrement n’est pas `sealed` .
```C#
public virtual bool Equals(R? other);
```
La synthèse `Equals(R?)` retourne `true` si et seulement si chacun des éléments suivants est `true` :
- `other`n’est pas `null` , et
- Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` où `TN` est le type de champ, et
- S’il existe un type d’enregistrement de base, la valeur de `base.Equals(other)` (un appel non virtuel à `public virtual bool Equals(Base? other)` ); sinon, la valeur de `EqualityContract == other.EqualityContract` .

Si le type d’enregistrement est dérivé d’un type d’enregistrement de base `Base` , le type d’enregistrement contient une substitution synthétisée 
```C#
public sealed override bool Equals(Base? other);
```
Il s’agit d’une erreur si la substitution est déclarée explicitement. Il s’agit d’une erreur si la méthode ne peut pas substituer une méthode avec la même signature dans le type d’enregistrement `Base` (par exemple, si la méthode est manquante dans le `Base` , ou sealed, ou non virtuelle, etc.).
La valeur de substitution synthétisée est retournée `Equals((object?)other)` .

Le type d’enregistrement comprend une substitution synthétisée
```C#
public override bool Equals(object? obj);
```
Il s’agit d’une erreur si la substitution est déclarée explicitement. Il s’agit d’une erreur si la méthode n’est pas substituée `object.Equals(object? obj)` (par exemple, en raison de l’occultation dans les types de base intermédiaires, etc.).
La valeur de remplacement synthétisé retourne `Equals(other as R)` où `R` est le type d’enregistrement.

Le type d’enregistrement comprend une substitution synthétisée
```C#
public override int GetHashCode();
```
La méthode peut être déclarée explicitement.
Il s’agit d’une erreur si la déclaration explicite est `sealed` , sauf si le type d’enregistrement est `sealed` . Il s’agit d’une erreur si une méthode synthétisée ou explicitement déclarée ne se substitue pas `object.GetHashCode()` (par exemple, en raison de l’occultation dans les types de base intermédiaires, etc.).
 
Un avertissement est signalé si l’un des `Equals(R?)` et `GetHashCode()` est explicitement déclaré, mais que l’autre méthode n’est pas explicite.

La substitution synthétisée de `GetHashCode()` retourne un `int` résultat d’une fonction déterministe combinant les valeurs suivantes :
- Pour chaque champ d’instance `fieldN` dans le type d’enregistrement qui n’est pas hérité, la valeur de `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` où `TN` est le type de champ, et
- S’il existe un type d’enregistrement de base, la valeur de `base.GetHashCode()` ; sinon, la valeur de `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .

Par exemple, considérez les types d’enregistrements suivants :
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R2(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

Pour ces types d’enregistrements, les membres synthétisés ressemblent à ceci :
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

### <a name="copy-and-clone-members"></a>Copier et cloner des membres

Un type d’enregistrement contient deux membres de copie :

* Constructeur acceptant un seul argument du type d’enregistrement. Il est appelé « constructeur de copie ».
* Méthode « Clone » d’instance virtuelle sans paramètre publique synthétisée avec un nom réservé par le compilateur

L’objectif du constructeur de copie est de copier l’état du paramètre vers la nouvelle instance en cours de création. Ce constructeur n’exécute aucun initialiseur de champ/propriété d’instance présent dans la déclaration d’enregistrement. Si le constructeur n’est pas explicitement déclaré, un constructeur protégé est synthétisé par le compilateur.
La première chose que le constructeur doit faire, est d’appeler un constructeur de copie de la base, ou un constructeur d’objet sans paramètre si l’enregistrement hérite de l’objet. Une erreur est signalée si un constructeur de copie défini par l’utilisateur utilise un initialiseur de constructeur implicite ou explicite qui ne respecte pas cette exigence.
Après l’appel d’un constructeur de copie de base, un constructeur de copie synthétisé copie les valeurs de tous les champs d’instance implicitement ou explicitement déclarés dans le type d’enregistrement.

La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie. Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base. Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire. La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une. Une erreur est générée si la méthode Clone du type de base est sealed.

Si l’enregistrement conteneur est abstrait, la méthode de clonage synthétisé est également abstraite.

## <a name="positional-record-members"></a>Membres d’enregistrement positionnel

Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.

### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé. C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.

Au moment de l’exécution, le constructeur principal

1. exécute les initialiseurs d’instance qui apparaissent dans le corps de classe

1. appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.

Si un enregistrement a un constructeur principal, tout constructeur défini par l’utilisateur, à l’exception de « constructeur de copie », doit avoir un `this` initialiseur de constructeur explicite. 

Les paramètres du constructeur principal ainsi que les membres de l’enregistrement se trouvent dans la portée au sein `argument_list` de la `record_base` clause et dans les initialiseurs de champs d’instance ou de propriétés. Les membres d’instance seraient une erreur à ces emplacements (à l’instar de la façon dont les membres d’instance sont dans la portée dans les initialiseurs de constructeur standard aujourd’hui, mais une erreur à utiliser), mais les paramètres du constructeur principal seraient dans la portée et utilisables et créeraient un cliché instantané des membres. Les membres statiques seraient également utilisables, de la même façon que les appels de base et les initialiseurs fonctionnent aujourd’hui dans les constructeurs ordinaires. 

Les variables d’expression déclarées dans le `argument_list` sont dans la portée dans le `argument_list` . Les mêmes règles d’occultation qu’au sein d’une liste d’arguments d’un initialiseur de constructeur normal s’appliquent.

### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.

Pour un enregistrement :

* Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).
  Une propriété héritée `abstract` avec le type correspondant est substituée.
  Il s’agit d’une erreur si la propriété héritée n’a pas `public` `get` de `init` accesseurs substituables.
  La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.

### <a name="deconstruct"></a>Déconstruire

Un enregistrement positionnel synthétise une méthode publique de Returning void appelée Deconstruct avec une déclaration de paramètre out pour chaque paramètre de la déclaration du constructeur principal. Chaque paramètre de la méthode Deconstruct a le même type que le paramètre correspondant de la déclaration du constructeur principal. Le corps de la méthode assigne à la valeur d’un membre d’instance un accès à un membre du même nom.

## <a name="with-expression"></a>Expression `with`

Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.

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

Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications des assignations dans le `member_initializer_list` .

Une `with` expression valide a un récepteur avec un type non void. Le type de récepteur doit contenir une méthode « Clone » d’enregistrement synthétisé accessible.

Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.

Chaque `member_initializer` est traité de la même façon qu’une assignation à un champ ou à un accès à une propriété de la valeur de retour de la méthode Clone d’enregistrement. La méthode Clone n’est exécutée qu’une seule fois et les affectations sont traitées dans l’ordre lexical.
