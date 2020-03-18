---
ms.openlocfilehash: b51f27b2f58fd19851c80beb9cedcbd32b80b165
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484586"
---
# <a name="fixed-sized-buffers"></a>Mémoires tampons de taille fixe

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Fournissez un mécanisme à usage général et sécurisé pour déclarer des mémoires tampons de taille C# fixe dans le langage.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Aujourd’hui, les utilisateurs ont la possibilité de créer des mémoires tampons de taille fixe dans un contexte non sécurisé. Toutefois, cela oblige l’utilisateur à traiter les pointeurs, à effectuer des vérifications manuelles et ne prend en charge qu’un ensemble limité de types (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`et `double`).

La réclamation la plus courante est que les mémoires tampons de taille fixe ne peuvent pas être indexées dans du code sécurisé. L’impossibilité d’utiliser d’autres types est la seconde.

Avec quelques ajustements mineurs, nous pourrions fournir des mémoires tampons de taille fixe à usage général qui prennent en charge tout type, peuvent être utilisées dans un contexte sécurisé et avoir une vérification de limites automatique effectuée.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Vous pouvez déclarer une mémoire tampon de taille fixe sécurisée à l’aide des éléments suivants :

```csharp
public fixed DXGI_RGB GammaCurve[1025];
```

La déclaration est traduite dans une représentation interne par le compilateur qui est semblable à ce qui suit :

```csharp
[FixedBuffer(typeof(DXGI_RGB), 1024)]
public ConsoleApp1.<Buffer>e__FixedBuffer_1024<DXGI_RGB> GammaCurve;

// Pack = 0 is the default packing and should result in indexable layout.
[CompilerGenerated, UnsafeValueType, StructLayout(LayoutKind.Sequential, Pack = 0)]
struct <Buffer>e__FixedBuffer_1024<T>
{
    private T _e0;
    private T _e1;
    // _e2 ... _e1023
    private T _e1024;

    public ref T this[int index] => ref (uint)index <= 1024u ?
                                         ref RefAdd<T>(ref _e0, index):
                                         throw new IndexOutOfRange();
}
```

Comme ces mémoires tampons de taille fixe ne nécessitent plus l’utilisation de `fixed`, il est logique d’autoriser tout type d’élément.  

> Remarque : `fixed` sera toujours pris en charge, mais uniquement si le type d’élément est `blittable`

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

* Il peut y avoir des difficultés avec la compatibilité descendante, mais étant donné que les mémoires tampons de taille fixe existantes fonctionnent uniquement avec une sélection de types primitifs, il est possible que le compilateur continue de fonctionner « juste-à-côte » si l’utilisateur traite la mémoire tampon fixe comme un dirigé.
* Les constructions incompatibles peuvent avoir besoin d’utiliser un encodage `v2` légèrement différent pour masquer les champs de l’ancien compilateur.
* La compression n’est pas bien définie dans la spécification IL pour les types génériques. Alors que l’approche devrait fonctionner, nous recevrons un comportement non documenté. Nous devons faire en sorte qu’il soit documenté et que d’autres JIT comme mono aient le même comportement.
* La spécification d’un type distinct pour chaque longueur (éventuellement un autre pour `readonly` champs, si pris en charge) aura un impact sur les métadonnées. Elle sera liée par le nombre de tableaux de tailles différentes dans l’application donnée.
* la `ref` mathématique n’est pas formellement vérifiable (car elle n’est pas sûre). Nous devons trouver un moyen de mettre à jour les règles de vérification pour savoir que notre utilisation est correcte.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Déclarez manuellement vos structures et utilisez du code unsafe pour construire des indexeurs.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- devons-nous autoriser `readonly`?  (avec l’indexeur ReadOnly)
- devons-nous autoriser les initialiseurs de tableau ?
- le mot clé `fixed` est-il nécessaire ?
- `foreach`?
- uniquement les champs d’instance dans les structs ?

## <a name="design-meetings"></a>Réunions de conception

Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.