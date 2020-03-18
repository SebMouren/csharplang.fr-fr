---
ms.openlocfilehash: 6695664c3d5ca73f66e792b7ce2ec9993aceea05
ms.sourcegitcommit: 42ef673ecc883009c865f8384249881a546df216
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2019
ms.locfileid: "79485153"
---
# <a name="lambda-discard-parameters"></a>Paramètres d’abandon lambda

## <a name="summary"></a>Résumé

Autorisez l’utilisation des éléments ignorés (`_`) comme paramètres des expressions lambda et des méthodes anonymes.
Par exemple :
- expressions lambda : `(_, _) => 0`, `(int _, int _) => 0`
- méthodes anonymes : `delegate(int _, int _) { return 0; }`

## <a name="motivation"></a>Motivation

Les paramètres inutilisés n’ont pas besoin d’être nommés. L’objectif des éléments ignorés est clair, c’est-à-dire qu’ils sont inutilisés/ignorés.

## <a name="detailed-design"></a>Conception détaillée

[Paramètres de méthode](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) Dans la liste des paramètres d’une méthode lambda ou anonyme avec plusieurs paramètres nommés `_`, ces paramètres sont ignorés.
Remarque : si un seul paramètre est nommé `_`, il s’agit d’un paramètre régulier pour des raisons de compatibilité descendante.

Les paramètres d’abandon n’introduisent aucun nom dans les étendues.
Notez que cela signifie qu’ils n’entraînent pas le masquage des noms de `_` (trait de soulignement).

[Noms simples](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) Si `K` est égal à zéro et que le *simple_name* apparaît dans un *bloc* et que l’espace de déclaration de variable locale du *bloc*(ou du *bloc*englobant[) contient](basic-concepts.md#declarations)une variable locale, un paramètre (à l’exception de paramètres ignorés) ou une constante portant le nom `I`, le *simple_name* fait référence à cette variable locale, à ce paramètre ou à cette constante

[Étendues](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) À l’exception des paramètres d’abandon, la portée d’un paramètre déclaré dans *une lambda_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est la *anonymous_function_body* de cette *lambda_expression* à l’exception des paramètres d’abandon, la portée d’un paramètre déclaré dans une *anonymous_method_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est le *bloc* de cette *anonymous_method_expression*.

## <a name="related-spec-sections"></a>Sections relatives aux spécifications
- [Paramètres correspondants](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#corresponding-parameters)
