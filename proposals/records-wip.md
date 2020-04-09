---
ms.openlocfilehash: e29f453eb33e06fb3c84fb542d8b74223af9b975
ms.sourcegitcommit: 7f0c8e4eac7afe75e4f312f54a554614384cd06b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80870980"
---
# <a name="records-work-in-progress"></a>Documents Travail en cours

Contrairement aux autres propositions d’enregistrements, il ne s’agit pas d’une proposition en soi, mais d’un travail en cours visant à consigner les décisions de conception consensuelles pour la fonction d’enregistrements. Les détails de spécification seront ajoutés au besoin pour résoudre les questions.

La syntaxe pour un enregistrement est proposée pour être ajoutée comme suit :

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

Le `attributes` non-terminal permettra également un nouvel `data`attribut contextuel, .

Une classe (struct) déclarée `data` avec une liste de paramètres ou un modificateur est appelée classe record (struct d’enregistrement), dont l’un ou l’autre est un type d’enregistrement.

C’est une erreur de déclarer un type `data` d’enregistrement sans une liste de paramètres et le modificateur.

## <a name="members-of-a-record-type"></a>Membres d’un type de dossier

En plus des membres déclarés dans la classe ou l’organisme de struct, un type de dossier a les membres supplémentaires suivants :

### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce qu’on appelle le constructeur principal pour le type, et provoque le constructeur implicitement déclaré classe par défaut, si présent, d’être supprimé. C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.

Au moment de l’exécution, le principal constructeur

1. exécute les initialisateurs de champ d’instance apparaissant dans le corps de classe ; puis invoque le constructeur de la classe de base sans arguments.

1. initialise les champs de support générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur

### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type enregistrement, il y a un membre de la propriété publique correspondante dont le nom et le type sont tirés de la déclaration de paramètres de valeur. Si aucune propriété concrète (c.-à-d. non abstraite) avec un accesseur d’accès et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :

Pour une struction d’enregistrement ou une classe record :

* Une autoproprié propriété publique est créée. Sa valeur est parascée pendant la construction avec la valeur du paramètre constructeur principal correspondant. Chaque « appariement » hérité de la propriété abstraite obtenir accesseur est remplacé.

### <a name="equality-members"></a>Membres de l’égalité

Les types d’enregistrement produisent des implémentations synthétisées pour les méthodes suivantes :

* `object.GetHashCode()`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur
* `object.Equals(object)`l’emporter, à moins qu’il ne soit scellé ou fourni par l’utilisateur
* `T Equals(T)`méthode, `T` où est le type actuel

`T Equals(T)`est spécifié pour effectuer l’égalité de valeur, en comparant la propriété avec le même nom que chaque paramètre constructeur principal à la propriété correspondante de l’autre type.
`object.Equals`effectue l’équivalent de

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

Une `with` expression permet une «mutation non destructive», conçue pour produire une copie de `anonymous_object_initializer`l’expression du récepteur avec des modifications aux propriétés énumérées dans le .

Une `with` expression valide a un récepteur avec un type non vide. Le type de récepteur doit `With` contenir une méthode d’instance accessible appelée avec les paramètres appropriés et le type de retour. C’est une erreur s’il `With` existe plusieurs méthodes de non-remplacement. S’il `With` y a plusieurs remplacements, il `With` doit y avoir une méthode de non-remplacement, qui est la méthode cible. Sinon, il doit `With` y avoir exactement une méthode.

Sur le côté droit `with` de `anonymous_object_initializer` l’expression est une séquence d’affectations avec un champ ou une propriété du récepteur sur le côté gauche de la mission, et une expression arbitraire sur le côté droit qui est implicitement convertible au type du côté gauche.

Compte tenu `With` d’une méthode cible, le type de retour doit être le type d’expression du récepteur, ou un type de base de celui-ci. Pour chaque paramètre de la `With` méthode, il doit y avoir un champ d’instance correspondant accessible ou une propriété lisible sur le type de récepteur du même nom et du même type. Chaque propriété ou champ dans le côté droit de l’expression Avec doit `With` également correspondre à un paramètre du même nom dans la méthode.

Compte tenu `With` d’une méthode `with` valide, l’évaluation d’une expression équivaut à appeler la `With` méthode avec les expressions dans le `anonymous_object_initializer` substitué au paramètre du même nom que la propriété sur le côté gauche. S’il n’y a pas `anonymous_object_initializer`de propriété correspondante pour un paramètre donné dans le , l’argument est l’évaluation du champ ou de la propriété du même nom sur le récepteur.

L’ordre d’évaluation des effets secondaires est le suivant, chaque expression étant évaluée exactement une fois :

1. Expression du récepteur

2. Expressions dans `anonymous_object_initializer`le , dans l’ordre lexical

3. L’évaluation de toutes `With` les propriétés correspondant aux `With` paramètres de la méthode, par ordre de définition des paramètres de la méthode.
