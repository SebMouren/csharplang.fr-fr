---
ms.openlocfilehash: 25756c1811d5e6dc97512ce70f99ab7fefa91c4a
ms.sourcegitcommit: 2a6dffb60718065ece95df75e1cc7eb509e48a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2020
ms.locfileid: "79485237"
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
