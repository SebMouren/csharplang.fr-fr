---
ms.openlocfilehash: aa0c233e7739ae7a0a7752251aae6f89df18ba93
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484516"
---
# <a name="c-language-proposals"></a>C#Propositions de langage

Les propositions linguistiques sont des documents actifs qui décrivent l’approche actuelle d’une fonctionnalité de langage donnée.

Les propositions peuvent être *actives*, *inactives*, *rejetées* ou *terminées*. Les propositions *actives* sont stockées directement dans le dossier propositions, les propositions *inactive* et *rejetées* sont stockées dans les sous-dossiers [inactif](proposals/inactive) et [rejeté](proposals/rejected) , et les propositions *terminées* sont archivées dans un dossier correspondant à la version linguistique à laquelle elles appartiennent.

## <a name="lifetime-of-a-proposal"></a>Durée de vie d’une proposition

Une proposition commence sa vie lorsque l’équipe de conception de langage décide qu’elle peut apporter une bonne adjonction à la langue. En règle générale, elle commence à être *active*, mais si nous souhaitons capturer une idée sans avoir à travailler dessus pour le moment, une proposition peut également commencer dans le sous-dossier *inactif* . Les propositions peuvent même commencer directement à l’état *rejeté* , si nous voulons créer un enregistrement de ce que nous n’envisageons pas de faire. Par exemple, si une demande populaire et récurrente n’est pas possible à implémenter, nous pouvons la capturer comme une proposition rejetée.

La proposition peut commencer par une idée dans un [problème de discussion](https://github.com/dotnet/csharplang/labels/Discussion)ou peut provenir de discussions dans le cadre de la réunion de conception de langage ou d’un grand nombre d’autres frontaux. Le principal est que l’équipe de conception pense qu’elle doit être exécutée, et qu’il y a une personne qui souhaite la rédiger. En général, un membre de l’équipe de conception de langage s’attribuera comme champion de la fonctionnalité, suivi par un [problème de champion](https://github.com/dotnet/csharplang/labels/Proposal%20champion). Le champion est responsable du déplacement de la proposition dans le processus de conception.

Une proposition est *active* si elle progresse dans le cadre de la conception et de l’implémentation vers une prochaine version. Une fois *l’opération terminée, c’est-à-dire*qu’une implémentation a été fusionnée dans une version et que la fonctionnalité a été spécifiée, elle est déplacée dans un sous-répertoire correspondant à sa version.

Si une fonctionnalité n’est pas susceptible de la transformer en langue, par exemple parce qu’elle s’avère irréalisable, ne semble pas ajouter suffisamment de valeur ou n’est pas adaptée à la langue, elle est [rejetée](proposals/rejected). Si une fonctionnalité a une promesse raisonnable mais n’est pas actuellement hiérarchisée pour fonctionner, elle peut être déclarée [inactive](proposals/inactive) pour éviter d’encombrer le dossier principal. Il est parfaitement parfait de travailler sur des propositions inactives ou rejetées, et de les réactiver ultérieurement. Les catégories sont là pour refléter l’intention de conception actuelle.

## <a name="nature-of-a-proposal"></a>Nature d’une proposition

Une proposition doit suivre le [modèle de proposition](proposal-template.md). Une proposition correcte doit :

- Ajustez l’esprit général et l’esthétique du langage.
- N’introduisez pas de syntaxe alternative subtile pour les fonctionnalités existantes.
- Ajoutez un grand nombre de valeurs pour un ensemble clair d’utilisateurs.
- N’ajoutez pas de manière significative à la complexité du langage, en particulier pour les nouveaux utilisateurs.  

## <a name="discussion-of-proposals"></a>Discussion sur les propositions

Les commentaires et les discussions se produisent dans les [problèmes de discussion](https://github.com/dotnet/csharplang/labels/Discussion). Quand une nouvelle proposition est ajoutée au dossier propositions, elle doit être annoncée dans un problème de discussion de la part du champion ou de l’auteur de la proposition. 

 