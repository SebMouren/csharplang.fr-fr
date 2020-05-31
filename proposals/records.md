---
ms.openlocfilehash: 6d8f0512d478d3d82cb262466e49a22a9e3cd0ee
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84227977"
---
# <a name="records-work-in-progress"></a>Travaux en cours d’enregistrement

Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours destiné à enregistrer les décisions de conception de consensus pour la fonctionnalité d’enregistrement. Les détails de la spécification seront ajoutés si nécessaire pour résoudre les questions.

La syntaxe d’un enregistrement est proposée pour être ajoutée comme suit :

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

Le `attributes` Terminal non-terminal autorise également un nouvel attribut contextuel, `data` .

Une classe (struct) déclarée avec un modificateur ou une liste `data` de paramètres est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.

La déclaration d’un type d’enregistrement sans une liste de paramètres et le modificateur est une erreur `data` .

## <a name="members-of-a-record-type"></a>Membres d’un type d’enregistrement

En plus des membres déclarés dans le corps de la classe ou du struct, un type d’enregistrement a les membres supplémentaires suivants :

### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce que l’on appelle le constructeur principal pour le type, et le constructeur de classe par défaut déclaré implicitement, s’il est présent, doit être supprimé. C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.

Au moment de l’exécution, le constructeur principal

1. exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.

1. Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur

### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur. Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :

Pour un struct d’enregistrement ou une classe d’enregistrement :

* Une `get` propriété publique et une `init` propriété automatique sont créées (voir `init` spécification d’accesseur distincte). Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant. Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.

### <a name="equality-members"></a>Membres d’égalité

Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :

* `object.GetHashCode()`override, sauf s’il est sealed ou fourni par l’utilisateur
* `object.Equals(object)`override, sauf s’il est sealed ou fourni par l’utilisateur
* `T Equals(T)`méthode, où `T` est le type actuel

`T Equals(T)`est spécifié pour effectuer une égalité de valeur, ce qui `Equals` a la valeur true si et seulement si tous les champs d’instance déclarés dans le type de récepteur sont égaux aux champs de l’autre type.

`object.Equals`exécute l’équivalent de

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>Copier et cloner des membres

Un type d’enregistrement contient deux membres de copie synthétisée si les méthodes ayant la même signature ne sont pas déjà déclarées dans le type d’enregistrement :

* Constructeur protégé acceptant un seul argument du type d’enregistrement.
* Une méthode d’instance sans paramètre publique appelée `Clone` qui retourne le type d’enregistrement.

Le constructeur protégé est appelé « constructeur de copie » et le corps synthétisé copie les valeurs de tous les champs d’instance déclarés dans le type d’entrée dans les champs correspondants de `this` .

La `Clone` méthode retourne le résultat d’un appel à un constructeur ayant la même signature que le constructeur de copie.
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

Une `with` expression valide a un récepteur avec un type non void. Le type de récepteur doit contenir une méthode d’instance sans paramètre accessible appelée `Clone` dont le type de retour doit être le type de récepteur ou un type de base de celui-ci.

Sur le côté droit de l' `with` expression se trouve un `member_initializer_list` avec une séquence d’assignations à *identifier*, qui doit être un champ d’instance accessible ou une propriété du type de retour de la `Clone()` méthode.

Chaque `member_initializer` est traité de la même façon qu’une assignation au champ ou à la cible de propriété sur la valeur de retour de la `Clone()` méthode. La `Clone()` méthode n’est exécutée qu’une seule fois et les assignations sont traitées dans l’ordre lexical.
