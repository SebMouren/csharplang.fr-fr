---
ms.openlocfilehash: 258ae6865c5b2c3103a0cdf7e1e5a2cdee11e740
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281955"
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

Le `attributes` non-terminal autorise également un nouvel attribut contextuel, `data`.

Une classe (struct) déclarée avec une liste de paramètres ou un modificateur de `data` est appelée classe d’enregistrement (struct d’enregistrement), soit un type d’enregistrement.

La déclaration d’un type d’enregistrement sans une liste de paramètres et le modificateur `data` est une erreur.

## <a name="members-of-a-record-type"></a>Membres d’un type d’enregistrement

En plus des membres déclarés dans le corps de la classe, un type d’enregistrement a les membres supplémentaires suivants :

### <a name="primary-constructor"></a>Constructeur principal

Un type d’enregistrement a un constructeur public dont la signature correspond aux paramètres de valeur de la déclaration de type. C’est ce que l’on appelle le constructeur principal pour le type et entraîne la suppression du constructeur par défaut déclaré implicitement. C’est une erreur d’avoir un constructeur principal et un constructeur avec la même signature déjà présente dans la classe.
Au moment de l’exécution, le constructeur principal 

1. exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la classe ; puis appelle le constructeur de classe de base sans arguments.

1. Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur ; consultez les [Propriétés synthétisées](#Synthesized Properties))


[] TODO : ajoutez la syntaxe et la spécification de l’appel de base concernant le choix du constructeur de base via la résolution de surcharge

### <a name="properties"></a>Propriétés

Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un membre de propriété public correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur. Si aucune propriété concrète (c’est-à-dire non abstraite) avec un accesseur get et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :

Pour un struct d’enregistrement ou une classe d’enregistrement :

* Une propriété automatique de récupération en tant que public est créée. Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant. Chaque accesseur Get de la propriété abstraite « correspondant » hérité est substitué.

### <a name="equality-members"></a>Membres d’égalité

Les types d’enregistrements produisent des implémentations synthétisées pour les méthodes suivantes :

* `object.GetHashCode()` remplacement, sauf s’il est scellé ou fourni par l’utilisateur
* `object.Equals(object)` remplacement, sauf s’il est scellé ou fourni par l’utilisateur
* méthode `T Equals(T)`, où `T` est le type actuel

`T Equals(T)` est spécifié pour effectuer l’égalité des valeurs, en comparant la propriété portant le même nom que chaque paramètre de constructeur principal à la propriété correspondante de l’autre type.
`object.Equals` effectue l’équivalent de

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a>Expression `with`

Une expression `with` est une nouvelle expression utilisant la syntaxe suivante.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

Une expression `with` permet la « mutation non destructrice », conçue pour produire une copie de l’expression du récepteur avec les modifications apportées aux propriétés figurant dans la `anonymous_object_initializer`.

Une expression `with` valide a un récepteur avec un type non void. Le type de récepteur doit contenir une méthode d’instance accessible appelée `With` avec les paramètres et le type de retour appropriés. C’est une erreur s’il existe plusieurs méthodes de `With` non substituées. S’il existe plusieurs remplacements `With`, il doit y avoir une méthode de `With` non substituée, qui est la méthode cible. Dans le cas contraire, il doit y avoir exactement une seule méthode `With`.

Sur le côté droit de l’expression `with` est une `anonymous_object_initializer` avec une séquence d’assignations avec un champ ou une propriété du récepteur sur le côté gauche de l’assignation, et une expression arbitraire sur le côté droit qui est implicitement convertible dans le type de la partie gauche.

Pour une méthode `With` cible donnée, le type de retour doit être le type du type d’expression de récepteur ou un type de base de ce type. Pour chaque paramètre de la méthode `With`, il doit y avoir un champ d’instance correspondant accessible ou une propriété lisible sur le type de récepteur avec le même nom et le même type. Chaque propriété ou champ du côté droit de l’expression with doit également correspondre à un paramètre du même nom dans la méthode `With`.

Étant donné une méthode de `With` valide, l’évaluation d’une expression `with` revient à appeler la méthode `With` avec les expressions du `anonymous_object_initializer` substituées pour le paramètre du même nom que la propriété de gauche. S’il n’existe aucune propriété correspondante pour un paramètre donné dans le `anonymous_object_initializer`, l’argument correspond à l’évaluation du champ ou de la propriété du même nom sur le récepteur.

L’ordre d’évaluation des effets secondaires est le suivant, chaque expression évaluée exactement une fois :

1. Expression Receiver

2. Expressions dans le `anonymous_object_initializer`, dans l’ordre lexical

3. L’évaluation de toutes les propriétés correspondant aux paramètres de méthode `With`, dans l’ordre de définition des paramètres de méthode `With`.