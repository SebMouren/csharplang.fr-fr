---
ms.openlocfilehash: a0d80afc47e9f0073237db9b8d7a4f0b045c1b0b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484509"
---
# <a name="out-variable-declarations"></a>Déclarations de variables out

La fonctionnalité de *déclaration de variable out* permet à une variable d’être déclarée à l’emplacement où elle est passée en tant qu’argument `out`.

```antlr
argument_value
    : 'out' type identifier
    | ...
    ;
```

Une variable déclarée de cette façon est appelée une *variable out*. Vous pouvez utiliser le mot clé contextuel `var` pour le type de la variable. L’étendue est la même que pour une *variable de modèle* introduite via la mise en correspondance de modèle.

Conformément à la spécification du langage (section 7.6.7 Element Access), la liste d’arguments d’un élément d’accès (expression d’indexation) ne contient pas d’arguments ref ou out. Toutefois, ils sont autorisés par le compilateur pour différents scénarios, par exemple les indexeurs déclarés dans les métadonnées qui acceptent `out`.

Dans l’étendue d’une variable locale introduite par un argument_value, il s’agit d’une erreur au moment de la compilation pour faire référence à cette variable locale dans une position textuelle qui précède sa déclaration.

Il s’agit également d’une erreur pour référencer une variable de sortie implicitement typée (§ 8.5.1) dans la même liste d’arguments qui contient immédiatement sa déclaration.

La résolution de surcharge est modifiée comme suit :

Nous ajoutons une nouvelle conversion :

> Il y a une *conversion de l’expression* d’une déclaration de variable de type implicitement typée à chaque type.

Même

> Le type d’un argument de variable out explicitement typé est le type déclaré.

and

> Un argument de variable out implicitement typé n’a aucun type.

La *conversion d’une expression* à partir d’une déclaration de variable de sortie implicitement typée n’est pas considérée comme meilleure que toute autre *conversion à partir d’une expression*.

Le type d’une variable de sortie implicitement typée est le type du paramètre correspondant dans la signature de la méthode sélectionnée par la résolution de surcharge.

Le nouveau nœud de syntaxe `DeclarationExpressionSyntax` est ajouté pour représenter la déclaration dans un argument var.
