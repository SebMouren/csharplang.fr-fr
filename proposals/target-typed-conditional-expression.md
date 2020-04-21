---
ms.openlocfilehash: 80ccdb75e2f5a022e367f3c023ea4464195deaaf
ms.sourcegitcommit: 95f5f86ba2e2a23cd4fb37bd9d1ff690c83d1191
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81647133"
---
# <a name="target-typed-conditional-expression"></a>Expression conditionnelle de type cible

Pour une `c ? e1 : e2`expression conditionnelle, quand

1. il n’existe `e1` pas `e2`de type commun pour et , ou
2. pour lequel un type commun n’existe qu’une seule des expressions `e1` ou `e2` n’a pas de conversion implicite à ce type

nous définissons une nouvelle *conversion d’expression conditionnelle* qui `T` permet une conversion implicite de `e1` l’expression conditionnelle à tout type pour lequel il y a une conversion de l’expression d’à partir `T` et aussi de `e2` . `T`  Il s’agit d’une erreur si `e1` une `e2` expression conditionnelle n’a pas de type commun entre et n’est pas soumise à une conversion d’expression *conditionnelle*.

### <a name="open-issues"></a>Open Issues

Nous aimerions étendre cette cible de dactylographie aux cas `e1` où l’expression conditionnelle a un type commun et `e2` mais il n’y a pas de conversion de ce type commun au type cible. Cela amènerait la dactylographie cible de l’expression conditionnelle dans l’alignement de la frappe cible de l’expression de commutateur. Cependant, nous craignons que ce serait un changement radical :

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

Nous pourrions réduire la portée du changement de rupture en modifiant les règles pour *une meilleure conversion de l’expression*: la conversion d’une expression conditionnelle à T1 est une meilleure conversion de l’expression que la conversion en T2 si la conversion en T1 n’est pas une conversion *d’expression conditionnelle* et la conversion en T2 est une conversion *d’expression conditionnelle.*  Cela résout le changement de rupture `M(long)` dans le programme ci-dessus (il appelle avec ou sans cette fonctionnalité). Cette approche a deux petits inconvénients.  Tout d’abord, il n’est pas tout à fait le même que l’expression de commutateur:

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

Il s’agit encore d’un changement radical, mais sa portée est moins susceptible d’affecter les programmes réels :

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

Cela devient ambigu parce `long` que la conversion est meilleure pour le premier argument (parce `short` qu’elle n’utilise `short` pas la conversion `long` *d’expression conditionnelle),* mais la conversion est meilleure pour le deuxième argument (parce que c’est une meilleure cible de *conversion* que ). Ce changement de rupture semble moins grave parce qu’il ne change pas silencieusement le comportement d’un programme existant.

Si nous décidons d’apporter ce changement à la proposition, nous

> #### <a name="better-conversion-from-expression"></a>Meilleure conversion de l’expression
> 
> Compte tenu `C1` d’une conversion `E` implicite qui `T1`se convertit `C2` d’une expression `E` à `T2`un `C1` type , et une conversion implicite qui se convertit d’une expression à un type , est une ***meilleure conversion*** `C2` que si `E` n’est pas exactement correspondre `T2` et au moins l’un des points suivants détient:
> 
> * `E`exactement `T1` correspond ([Exactement correspondant Expression](expressions.md#exactly-matching-expression))
> * `T1`est une meilleure `T2` cible de conversion que ([Meilleure cible de conversion](expressions.md#better-conversion-target))

to

> #### <a name="better-conversion-from-expression"></a>Meilleure conversion de l’expression
> 
> Compte tenu `C1` d’une conversion `E` implicite qui `T1`se convertit `C2` d’une expression `E` à `T2`un `C1` type , et une conversion implicite qui se convertit d’une expression à un type , est une ***meilleure conversion*** `C2` que si `E` n’est pas exactement correspondre `T2` et au moins l’un des points suivants détient:
> 
> * `E`exactement `T1` correspond ([Exactement correspondant Expression](expressions.md#exactly-matching-expression))
> * `T1`est une meilleure `T2` cible de conversion que[(meilleure cible de conversion](expressions.md#better-conversion-target)) et n’est `C1` pas une *conversion d’expression conditionnelle* ou `C2` est une conversion d’expression conditionnelle.
