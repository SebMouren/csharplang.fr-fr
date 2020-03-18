---
ms.openlocfilehash: 63dfdfee9ea6c16e162f483aa1298feed297daef
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484698"
---
# <a name="conditional-ref-expressions"></a>Expressions de référence conditionnelle

Le modèle de liaison d’une variable Ref à une ou une autre expression de manière conditionnelle n’est C#pas exprimable actuellement dans.

La solution de contournement classique consiste à introduire une méthode comme :

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

Notez qu’il ne s’agit pas d’un remplacement exact d’un ternaire, car tous les arguments doivent être évalués sur le site d’appel.

Les éléments suivants ne fonctionnent pas comme prévu :

```csharp
       // will crash with NRE because 'arr[0]' will be executed unconditionally
      ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

La syntaxe proposée devrait ressembler à ceci :

```csharp
     <condition> ? ref <consequence> : ref <alternative>;
```

La tentative ci-dessus avec « Choice » peut être écrite _correctement_ à l’aide de Ref ternaire en tant que :

```csharp
     ref var r = ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

La différence par rapport au choix est que la conséquence et les expressions de remplacement sont accessibles de manière _véritablement_ conditionnelle, donc aucun blocage ne s’affiche si ```arr == null```

La référence ternaire est juste un ternaire où les deux alternatives et les conséquences sont des références. Cela nécessitera naturellement que la conséquence/les opérandes alternatifs soient LValues. Elle nécessitera également que les types de conséquence et de remplacement soient convertibles les uns des autres.

Le type de l’expression sera calculé de la même manière que celui du ternaire standard. savoir dans un cas, si la conséquence et l’alternative ont une identité convertible, mais des types différents, les règles de fusion de type existantes s’appliquent.

La sécurité à retourner est considérée comme prudente par rapport aux opérandes conditionnels. Si l’un ou l’autre risque de retourner la totalité de l’élément, il est risqué de retourner.

La référence ternaire est une LValue et, par conséquent, elle peut être passée/assignée/retournée par référence ;

```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

Étant une LValue, elle peut également être assignée à. 

```csharp
    // assign to
    (arr != null ? ref arr[0]: ref otherArr[0]) = 1;
```

Ref ternaire peut également être utilisé dans un contexte standard (non Ref). Bien qu’il ne soit pas courant, il est possible d’utiliser un ternaire standard.

```csharp
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```


___

Remarques sur l’implémentation : 

La complexité de l’implémentation semble être la taille d’un correctif de bogue de taille moyenne à grande. -I. E pas très cher.
Je ne pense pas que les modifications apportées à la syntaxe ou à l’analyse ne sont pas nécessaires.
Il n’y a aucun effet sur les métadonnées ou l’interopérabilité. La fonctionnalité est entièrement basée sur une expression.
Aucun effet sur le débogage/PDB
