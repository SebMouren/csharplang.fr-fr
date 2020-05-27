---
ms.openlocfilehash: 758bc3fddf577b118fc50866a683fa96324e5dc3
ms.sourcegitcommit: 300226c009490828d615e86c50df7ac8c836f945
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83843597"
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

* Une propriété automatique de récupération en tant que public est créée. Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant. Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.

  * Cette propriété est également `initonly` , ce qui signifie que le champ de stockage peut être modifié dans l’expression [with](#With) ci-dessous, si l' `get` accesseur correspondant est accessible

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

## <a name="with-expression"></a>Expression `with`

Une `with` expression est une nouvelle expression utilisant la syntaxe suivante.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

Une `with` expression permet une « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications apportées aux propriétés figurant dans le `anonymous_object_initializer` .

Une `with` expression valide a un récepteur avec un type non void. Le type de récepteur doit contenir une méthode d’instance sans paramètre accessible appelée `Clone` dont le type de retour doit être le type de récepteur ou un type de base de celui-ci.

Sur le côté droit de l' `with` expression se trouve un `anonymous_object_initializer` avec une séquence d’assignations, chacune avec une `initonly` propriété (consultez [Propriétés](#Properties)) du `Clone` type de retour sur le côté gauche de l’assignation (en tant qu’appel de propriété) et une expression arbitraire sur le côté droit qui est implicitement convertible en type de la propriété.

L’évaluation d’une `with` expression appelle la `Clone` méthode exactement une fois, puis définit le champ de stockage de chaque `initonly` propriété dans la liste d’arguments sur la valeur de son expression correspondante, dans l’ordre lexical, à l’aide du résultat de l’appel de la `Clone` méthode comme récepteur. Le type de l' `with` expression est le même que le type de retour de la `Clone` méthode.

