---
ms.openlocfilehash: a78567594d39fc4e204e12c4f2f247b8d6995c38
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484922"
---
# <a name="expression-variables-in-initializers"></a>Variables d’expression dans les initialiseurs

## <a name="summary"></a>Résumé
[summary]: #summary

Nous étendons les fonctionnalités introduites dans C# 7 pour autoriser des expressions contenant des variables d’expression (des déclarations de variables et des modèles de déclaration) dans les initialiseurs de champ, les initialiseurs de propriété, les initialiseurs de constructeurs et les clauses de requête.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Cela termine quelques bords à gauche du C# langage en raison d’un manque de temps.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Nous supprimons la restriction empêchant la déclaration des variables d’expression (les déclarations de variables et les modèles de déclaration) dans un constructeur ctor-initializer. Une telle variable déclarée est dans la portée dans le corps du constructeur.

Nous supprimons la restriction empêchant la déclaration de variables d’expression (les déclarations de variable et les modèles de déclaration) dans un initialiseur de champ ou de propriété. Une telle variable déclarée est dans la portée tout au long de l’expression d’initialisation.

Nous supprimons la restriction empêchant la déclaration des variables d’expression (les déclarations de variables et les modèles de déclaration) dans une clause d’expression de requête qui est traduite dans le corps d’une expression lambda. Une telle variable déclarée est dans la portée tout au long de cette expression de la clause de requête.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Aucun.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

La portée appropriée pour les variables d’expression déclarées dans ces contextes n’est pas évidente et mérite une discussion de LDM.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Quelle est la portée appropriée pour ces variables ?

## <a name="design-meetings"></a>Réunions de conception

Aucun.
