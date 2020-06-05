---
ms.openlocfilehash: 82f68e2e2703f1d78c191d1120781ab7306b327a
ms.sourcegitcommit: 100d3f7f04ba1e4f666c188fbd73762f2c3b8716
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84421867"
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

## <a name="members-of-a-record-type"></a>Membres d’un type d’enregistrement

En plus des membres déclarés dans le corps d’enregistrement, un type d’enregistrement a des membres synthétisés supplémentaires.
Les membres sont synthétisés, à moins qu’un membre concret (non abstrait) accessible avec une signature « correspondante » soit hérité ou déclaré dans le corps de l’enregistrement. Deux membres sont considérés comme correspondants s’ils ont la même signature ou sont considérés comme « masqués » dans un scénario d’héritage.

Les membres synthétisés sont les suivants :

### <a name="equality-members"></a>Membres d’égalité

Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes, où `T` est le type conteneur :

* `object.GetHashCode()`remplacer
* `object.Equals(object)`remplacer
* `T Equals(T)`méthode, où `T` est le type actuel
* `Type EqualityContract`propriété de récupération seule

Si `object.GetHashCode()` ou `object.Equals(object)` sont scellés, une erreur est générée.

`EqualityContract`est une propriété d’instance virtuelle qui retourne `typeof(T)` . Si le type de base définit un `EqualityContract` , il est substitué dans l’enregistrement dérivé. Si la base `EqualityContract` est sealed ou non virtuelle, une erreur est générée.

`T Equals(T)`est spécifié pour effectuer une égalité de valeur, ce qui `Equals` a la valeur true si et seulement si tous les champs d’instance accessibles dans le récepteur sont égaux aux champs du paramètre et `this.EqualityContract` est égal à `other.EqualityContract` .

`object.Equals`exécute l’équivalent de

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>Copier et cloner des membres

Un type d’enregistrement contient deux membres de copie synthétisée :

* Constructeur protégé acceptant un seul argument du type d’enregistrement.
* Méthode « Clone » d’instance virtuelle sans paramètre publique avec un nom réservé par le compilateur

Le constructeur protégé est appelé « constructeur de copie » et le corps synthétisé copie les valeurs de tous les champs d’instance accessibles dans le type d’entrée dans les champs correspondants de `this` .

La méthode « Clone » retourne le résultat d’un appel à un constructeur avec la même signature que le constructeur de copie. Le type de retour de la méthode Clone est le type conteneur, à moins qu’une méthode Clone virtuelle soit présente dans la classe de base. Dans ce cas, le type de retour est le type conteneur actuel si la fonctionnalité « covariant retourne » est prise en charge et le type de retour de substitution dans le cas contraire. La méthode de clonage synthétisé est une substitution de la méthode Clone du type de base s’il en existe une. Une erreur est générée si la méthode Clone du type de base est sealed.

## <a name="positional-record-members"></a>Membres d’enregistrement positionnel

Outre les membres ci-dessus, les enregistrements avec une liste de paramètres (« enregistrements positionnels ») synthétisent les membres supplémentaires avec les mêmes conditions que les membres ci-dessus.

### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé. C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.

Au moment de l’exécution, le constructeur principal

1. exécute les initialiseurs d’instance qui apparaissent dans le corps de classe

1. appelle le constructeur de classe de base avec les arguments fournis dans la `record_base` clause, le cas échéant.


### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.

Pour un enregistrement :

* Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte).
  Chaque accesseur abstrait hérité « correspondant » est substitué. La propriété automatique est initialisée à la valeur du paramètre de constructeur principal correspondant.

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
