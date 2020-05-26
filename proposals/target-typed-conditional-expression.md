---
ms.openlocfilehash: cd73a3d7289205f65f5144a98d32da06dfed3efc
ms.sourcegitcommit: e355841daad8c4672fae6a49c98653952d89a9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775423"
---
# <a name="target-typed-conditional-expression"></a>Expression conditionnelle typée cible

Pour une expression conditionnelle `c ? e1 : e2` , lorsque

1. Il n’existe aucun type commun pour `e1` et `e2` , ou
2. pour lequel un type commun existe, mais l’une des expressions `e1` ou n' `e2` a pas de conversion implicite vers ce type

Nous définissons une nouvelle *conversion d’expression conditionnelle* qui autorise une conversion implicite de l’expression conditionnelle en n’importe quel type `T` pour lequel il existe une conversion de-from-expression de `e1` en `T` et également de à `e2` `T` .  Il s’agit d’une erreur si une expression conditionnelle n’a pas de type commun entre `e1` et ou `e2` n’est pas soumise à une *conversion d’expression conditionnelle*.

### <a name="open-issues"></a>Problèmes ouverts

Nous souhaitons étendre ce type de cible aux cas dans lesquels l’expression conditionnelle a un type commun pour `e1` et, `e2` mais il n’existe aucune conversion de ce type commun vers le type cible. Cela entraînerait le typage de la cible de l’expression conditionnelle en alignement du typage de la cible de l’expression de commutateur. Toutefois, il s’agit d’une modification avec rupture :

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

Nous pourrions réduire l’étendue de la modification avec rupture en modifiant les règles pour *une meilleure conversion à partir de l’expression*: la conversion d’une expression conditionnelle en T1 est une meilleure conversion d’expression que la conversion en T2 si la conversion en T1 n’est pas une *conversion d’expression conditionnelle* et que la conversion en T2 est une *conversion d’expression conditionnelle*.  Cela résout la modification avec rupture dans le programme ci-dessus (il appelle `M(long)` avec ou sans cette fonctionnalité). Cette approche présente deux petits inconvénients.  Tout d’abord, il n’est pas tout à fait identique à l’expression de Switch :

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

Il s’agit toujours d’une modification avec rupture, mais son étendue est moins susceptible d’affecter des programmes réels :

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

Cela devient ambigu, car la conversion en `long` est préférable pour le premier argument (parce qu’il n’utilise pas la *conversion d’expression conditionnelle*), mais la conversion en `short` est préférable pour le deuxième argument (car `short` est une *meilleure cible de conversion* que `long` ). Cette modification avec rupture semble moins grave, car elle ne modifie pas le comportement d’un programme existant en mode silencieux.

Si nous choisissons d’apporter cette modification à la proposition, nous modifierons

> #### <a name="better-conversion-from-expression"></a>Meilleure conversion à partir de l’expression
> 
> Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :
> 
> * `E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))
> * `T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target))

to

> #### <a name="better-conversion-from-expression"></a>Meilleure conversion à partir de l’expression
> 
> Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en type `T1` , et d’une conversion implicite `C2` qui convertit une expression `E` en type `T2` , `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :
> 
> * `E`correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))
> * **`C1`n’est pas une *conversion d’expression conditionnelle* et `C2` est une * conversion d’expression conditionnelle * * *.
> * `T1`est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target)) * * et soit `C1` et `C2` sont des *conversions d’expressions conditionnelles* , soit aucune n’est une conversion d’expression conditionnelle * * *.
