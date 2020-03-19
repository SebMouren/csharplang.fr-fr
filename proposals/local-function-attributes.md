---
ms.openlocfilehash: 0c8bc2b5072ea7f86189b41a1cdbf2a449661b05
ms.sourcegitcommit: 33a60a1db1d42d21d959acfeb127e647150173aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79509506"
---
# <a name="attributes-on-local-functions"></a>Attributs sur des fonctions locales

## <a name="attributes"></a>Attributs

Les déclarations de fonction locale sont désormais autorisées à avoir des [attributs](../spec/attributes.md). Les paramètres et les paramètres de type sur les fonctions locales sont également autorisés à avoir des attributs.

Les attributs ayant une signification spécifiée lorsqu’ils sont appliqués à une méthode, à ses paramètres ou à ses paramètres de type ont la même signification lorsqu’ils sont appliqués à une fonction locale, à ses paramètres ou à ses paramètres de type, respectivement.

Une fonction locale peut être rendue conditionnelle dans le même sens qu’une [méthode conditionnelle](../spec/attributes.md#the-conditional-attribute) en la décorant avec un `[ConditionalAttribute]`. Une fonction locale conditionnelle doit également être `static`. Toutes les restrictions sur les méthodes conditionnelles s’appliquent également aux fonctions locales conditionnelles, y compris que le type de retour doit être `void`.

## <a name="extern"></a>Externes

Le modificateur `extern` est désormais autorisé sur les fonctions locales. Cela rend la fonction locale externe dans le même sens qu’une [méthode externe](../spec/classes.md#external-methods).

À l’instar d’une méthode externe, le *corps de fonction locale* d’une fonction locale externe doit être un point-virgule. Un *corps de fonction locale* de point-virgule est uniquement autorisé sur une fonction locale externe. 

Une fonction locale externe doit également être `static`.

## <a name="syntax"></a>Syntaxe

La [grammaire Functions locale](csharp-7.0/local-functions.md#syntax-grammar) est modifiée comme suit :
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```
