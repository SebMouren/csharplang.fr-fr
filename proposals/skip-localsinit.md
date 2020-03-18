---
ms.openlocfilehash: 52b43abd2d8fb56088a68c7169289a63c43ce96f
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484488"
---
# <a name="suppress-emitting-of-localsinit-flag"></a>Supprime l’émission de `localsinit` indicateur.

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Autorise la suppression de l’émission d’un indicateur de `localsinit` via `SkipLocalsInitAttribute` attribut. 

## <a name="motivation"></a>Motivation
[motivation]: #motivation


### <a name="background"></a>Arrière-plan
Par spécification CLR, les variables locales qui ne contiennent pas de références ne sont pas initialisées à une valeur particulière par la machine virtuelle/JIT. La lecture de ces variables sans initialisation est de type sécurisé, mais dans le cas contraire, le comportement est non défini et spécifique à l’implémentation. En général, les variables locales non initialisées contiennent les valeurs laissées dans la mémoire qui est maintenant occupée par le frame de pile. Cela peut entraîner un comportement non déterministe et des bogues difficiles à reproduire. 

Il existe deux façons de « assigner » une variable locale : 
- en stockant une valeur ou 
- en spécifiant `localsinit` indicateur qui force tout ce qui est alloué à former le pool de mémoire local à être initialisé à zéro Remarque : cela inclut à la fois les variables locales et les données de `stackalloc`.    

L’utilisation de données non initialisées est déconseillée et n’est pas autorisée dans du code vérifiable. Bien qu’il soit possible de prouver que, par le biais de l’analyse des fluides, l’algorithme de vérification peut être conservateur et nécessite simplement que `localsinit` soit définie.

Le C# compilateur historique émet `localsinit` indicateur sur toutes les méthodes qui déclarent des variables locales.

Tandis que C# utilise une analyse d’assignation définie qui est plus stricte que celle requise par leC# CLR (il doit également prendre en compte l’étendue des variables locales), il n’est pas strictement garanti que le code résultant soit officiellement vérifiable :
- Le CLR C# et les règles peuvent ne pas savoir si le passage d’un argument local en tant qu’argument `out` est un `use`.
- Le CLR C# et les règles peuvent ne pas s’accorder sur le traitement des branches conditionnelles lorsque les conditions sont connues (propagation constante).
- Le CLR peut tout simplement nécessiter `localinits`, puisque cela est autorisé.  

### <a name="problem"></a>Problème
Dans une application hautes performances, le coût de l’initialisation forcée de zéro peut être perceptible. Cela est particulièrement perceptible lorsque `stackalloc` est utilisé.

Dans certains cas, le JIT peut Elide initialiser l’initialisation zéro des variables locales individuelles lorsque cette initialisation est « supprimée » par les assignations suivantes. Toutes les JIT ne le font pas et une telle optimisation a des limites. Elle ne permet pas de `stackalloc`.

Pour illustrer le problème réel, il existe un bogue connu dans lequel une méthode qui ne contient pas de `IL` variables locales n’aurait pas d’indicateur `localsinit`. Le bogue est déjà exploité par les utilisateurs en plaçant `stackalloc` dans ces méthodes, de manière intentionnelle, afin d’éviter les coûts d’initialisation. Cela est en dépit du fait que l’absence de `IL` variables locales est une mesure instable et peut varier en fonction des modifications apportées à la stratégie CodeGen. Le bogue doit être corrigé et les utilisateurs doivent disposer d’une méthode plus documentée et fiable pour supprimer l’indicateur. 

## <a name="detailed-design"></a>Conception détaillée

Autorisez la spécification d' `System.Runtime.CompilerServices.SkipLocalsInitAttribute` pour indiquer au compilateur de ne pas émettre `localsinit` indicateur.
 
Le résultat final est que les variables locales ne peuvent pas être initialisées à zéro par le JIT, qui est dans la plupart des cas non observables dans C#.  
En plus de cette `stackalloc` données ne seront pas initialisées à zéro. Cela est tout à fait observable, mais également le scénario le plus motivant.

Les cibles d’attribut autorisées et reconnues sont les suivantes : `Method`, `Property`, `Module`, `Class`, `Struct`, `Interface`, `Constructor`. Toutefois, le compilateur ne requiert pas que l’attribut soit défini avec les cibles listées, et il se chargera de déterminer l’assembly dans lequel l’attribut est défini. 

Lorsque l’attribut est spécifié sur un conteneur (`class`, `module`, qui contient la méthode pour une méthode imbriquée,...), l’indicateur affecte toutes les méthodes contenues dans le conteneur.

Les méthodes synthétisées « héritent » de l’indicateur du conteneur/propriétaire logique. 

L’indicateur affecte uniquement la stratégie CodeGen pour les corps de méthode réels. savoir l’indicateur n’a aucun effet sur les méthodes abstraites et n’est pas propagé aux méthodes de substitution/implémentation.

Il s’agit d’une **_fonctionnalité de compilateur_** qui n’est **_pas une fonctionnalité de langage_** .  
De la même façon que les commutateurs de ligne de commande du compilateur, la fonctionnalité contrôle les détails d’implémentation d’une stratégie CodeGen C# particulière et n’a pas besoin d’être requise par la spécification.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

- Les anciens/autres compilateurs ne peuvent pas honorer l’attribut.
Ignorer l’attribut est un comportement compatible. Seul peut entraîner une légère baisse des performances.

- Le code sans indicateur `localinits` peut déclencher des échecs de vérification.
Les utilisateurs qui demandent cette fonctionnalité sont généralement insoucieux de la vérifiabilité. 
 
- L’application de l’attribut à des niveaux supérieurs par rapport à une méthode individuelle a un effet non local, qui est observable quand `stackalloc` est utilisé. Pourtant, il s’agit du scénario le plus demandé.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

- omettez `localinits` indicateur lorsque la méthode est déclarée dans `unsafe` contexte. Cela peut entraîner un changement de comportement silencieux et dangereux de déterministe en non déterministe dans un cas de `stackalloc`.

- omettez l’indicateur `localinits` Always.
Pire encore que ci-dessus.

- omettez `localinits` indicateur, sauf si `stackalloc` est utilisé dans le corps de la méthode.
Ne traite pas le scénario le plus demandé et peut désactiver le code non vérifiable sans option pour revenir en arrière.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- L’attribut doit-il être émis aux métadonnées ? 

## <a name="design-meetings"></a>Réunions de conception

Aucun. 