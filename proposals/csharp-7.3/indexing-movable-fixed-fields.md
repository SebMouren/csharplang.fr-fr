---
ms.openlocfilehash: 36f3e6204d12c2569b3a55f3a47f58337e8a08e4
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484628"
---
# <a name="indexing-fixed-fields-should-not-require-pinning-regardless-of-the-movableunmovable-context"></a>L’indexation des champs de `fixed` ne doit pas nécessiter l’épinglage, quel que soit le contexte mobile/non déplaçable. #

La modification a la taille d’une résolution de bogue. Il peut être dans 7,3 et n’est pas en conflit avec la direction que nous prenons.
Cette modification concerne uniquement la possibilité de fonctionnement du scénario suivant, même si `s` est déplaçable. Elle est déjà valide lorsque `s` n’est pas déplaçable. 

Remarque : dans les deux cas, il requiert toujours `unsafe` contexte. Il est possible de lire des données non initialisées ou même hors limites. Cela ne change pas.

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int p = s.myFixedField[5]; // indexing fixed-size array fields would be ok
    }
}
```

Le « défi » principal que je vois ici est comment expliquer l’assouplissement dans la spécification. En particulier, étant donné que les éléments suivants doivent toujours être épinglés. (étant donné que `s` est déplaçable et que nous utilisons explicitement le champ comme pointeur)

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int* ptr = s.myFixedField; // taking a pointer explicitly still requires pinning.
        int p = ptr[5];
    }
}
```

L’un des motifs justifiant l’épinglage de la cible lorsqu’il est déplaçable est l’artefact de notre stratégie de génération de code,-nous convertissons toujours en pointeur non managé et forcez donc l’utilisateur à épingler via `fixed` instruction. Toutefois, la conversion en non managé n’est pas nécessaire lors de l’indexation. La même mathématique de pointeur non sécurisé est également applicable lorsque nous avons le récepteur sous la forme d’un pointeur managé. Dans ce cas, la référence intermédiaire est managée (suivi GC) et l’épinglage n’est pas nécessaire.

La modification https://github.com/dotnet/roslyn/pull/24966 est un prototype PR qui assouplit cette exigence.
