---
ms.openlocfilehash: a8822137c85f449444ed675c6f2912315c041691
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484474"
---
# <a name="static-delegates"></a>Délégués statiques

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Fournissez une fonction de rappel à usage général et légère C# au langage.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Aujourd’hui, les utilisateurs ont la possibilité de créer des rappels à l’aide du type de `System.Delegate`. Toutefois, celles-ci sont assez lourdes (par exemple, une allocation de tas et une gestion systématique pour le chaînage des rappels).

En outre, `System.Delegate` ne fournit pas la meilleure interopérabilité avec les pointeurs de fonction non managés, à savoir la non-blittable et la nécessité d’un marshaling à chaque fois qu’il franchit les limites managées/non managées.

Avec quelques ajustements mineurs, nous pourrions fournir un nouveau type de délégué léger, à usage général et des interopérabilités bien avec le code natif.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Vous pouvez déclarer un délégué statique à l’aide de ce qui suit :

```C#
static delegate int Func()
```

Il est également possible d’attribuer à la déclaration une valeur similaire à `System.Runtime.InteropServices.UnmanagedFunctionPointer` afin que la Convention d’appel, le marshaling de chaînes et le comportement de la définition de la dernière erreur puissent être contrôlés. Remarque : l’utilisation de `System.Runtime.InteropServices.UnmanagedFunctionPointer` elle-même ne fonctionne pas, car elle est utilisable uniquement sur les délégués.

La déclaration est traduite dans une représentation interne par le compilateur qui est semblable à ce qui suit :

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

Autrement dit, il est représenté en interne par un struct qui a un seul membre de type `IntPtr` (ce type de struct est blittable et n’entraîne pas d’allocation de tas) :
* Le membre contient l’adresse de la fonction qui doit être le rappel.
* Le type déclare une méthode qui correspond à la signature de méthode du rappel.
* Le nom du struct ne doit pas être constructible par l’utilisateur (comme nous le faisons avec d’autres structures de stockage générées en interne).
 * Par exemple : les mémoires tampons de taille fixe génèrent un struct avec un nom au format `<name>e__FixedBuffer` (`<` et `>` font partie de l’identificateur et rendent l’identificateur non constructible C#, mais toujours utilisable dans il).
* Le nom de la déclaration de méthode doit être un nom connu utilisé dans tous les types délégués statiques (cela permet au compilateur de connaître le nom à rechercher lors de la détermination de la signature).

La valeur du délégué statique ne peut être liée qu’à une méthode statique qui correspond à la signature du rappel.

Le chaînage des rappels n’est pas pris en charge.

L’appel du rappel est implémenté par l’instruction `calli`.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Les délégués statiques ne fonctionneront pas avec les API existantes qui utilisent des délégués réguliers (vous devez encapsuler ce délégué statique dans un délégué normal de la même signature).
* Étant donné que `System.Delegate` est représenté en interne sous la forme d’un ensemble de champs `object` et `IntPtr` (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), il est possible d’autoriser la conversion implicite d’un délégué statique en `System.Delegate` qui a une signature de méthode correspondante. Il serait également possible de fournir une conversion explicite dans le sens inverse, à condition que la `System.Delegate` conforme à toutes les exigences d’un délégué statique.

Un travail supplémentaire est nécessaire pour rendre le délégué statique facilement utilisable dans l’infrastructure de base.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

TBD

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Quelles sont les parties de la conception toujours TBD ?

## <a name="design-meetings"></a>Réunions de conception

Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.


