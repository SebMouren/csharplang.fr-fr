---
ms.openlocfilehash: 833ea0469149cbd434e8950e844740a3adb4d386
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484593"
---
# <a name="improved-overload-candidates"></a>Candidats de surcharge améliorés

## <a name="summary"></a>Résumé
[summary]: #summary

Les règles de résolution de surcharge ont été mises à C# jour dans presque toutes les mises à jour de langage pour améliorer l’expérience des programmeurs, ce qui fait que les appels ambigus sélectionnent le choix « évident ». Cette opération doit être effectuée avec précaution pour préserver la compatibilité descendante, mais comme nous résolvons habituellement ce qui serait autrement le cas d’erreur, ces améliorations sont généralement apportées.

1. Lorsqu’un groupe de méthodes contient à la fois des membres d’instance et des membres statiques, nous ignorons les membres d’instance s’ils sont appelés sans un récepteur ou un contexte d’instance, et ignorons les membres statiques s’ils sont appelés avec un récepteur d’instance. En l’absence de récepteur, nous incluons uniquement des membres statiques dans un contexte statique, sinon des membres statiques et d’instance. Lorsque le destinataire est ambiguement une instance ou un type en raison d’une situation de couleur de couleur, nous incluons les deux. Un contexte statique, où il est impossible d’utiliser un récepteur d’instance implicite, comprend le corps des membres où aucun est défini, tel que les membres statiques, ainsi que les emplacements où il ne peut pas être utilisé, tels que les initialiseurs de champ et les initialiseurs de constructeur.
2. Lorsqu’un groupe de méthodes contient certaines méthodes génériques dont les arguments de type ne répondent pas à leurs contraintes, ces membres sont supprimés de l’ensemble de candidats.
3. Pour une conversion de groupe de méthodes, les méthodes candidates dont le type de retour ne correspond pas à celui du délégué sont supprimées de l’ensemble.
