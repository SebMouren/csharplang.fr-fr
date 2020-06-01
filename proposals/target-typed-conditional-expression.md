---
ms.openlocfilehash: bfdd44c81a40c49b26ac15a69d2327f795bd88e6
ms.sourcegitcommit: 85263bfff8512e3e6fa4da7dc75e892937076de8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84239034"
---
# <a name="target-typed-conditional-expression"></a>Expression conditionnelle typée cible

## <a name="conditional-expression-conversion"></a>Conversion d’expression conditionnelle

Pour une expression conditionnelle `c ? e1 : e2` , lorsque

1. Il n’existe aucun type commun pour `e1` et `e2` , ou
2. pour lequel un type commun existe, mais l’une des expressions `e1` ou n' `e2` a pas de conversion implicite vers ce type

Nous définissons une nouvelle *conversion d’expression conditionnelle* implicite qui autorise une conversion implicite de l’expression conditionnelle en n’importe quel type `T` pour lequel il existe une conversion de-from-expression de `e1` en `T` et également de à `e2` `T` .  Il s’agit d’une erreur si une expression conditionnelle n’a pas de type commun entre `e1` et ou `e2` n’est pas soumise à une *conversion d’expression conditionnelle*.

## <a name="better-conversion-from-expression"></a>Meilleure conversion à partir de l’expression

Nous changeons

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

## <a name="cast-expression"></a>Expression cast

La spécification du langage C# actuel indique

> *Cast_expression* de la forme `(T)E` , où `T` est un *type* et `E` est un *unary_expression*, effectue une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` en type `T` .

En présence de la *conversion d’expression conditionnelle* , il peut y avoir plusieurs conversions possibles de `E` à `T` . Avec l’ajout de la *conversion d’expression conditionnelle*, nous préférons toute conversion en une *conversion d’expression conditionnelle*et n’utilisez la *conversion d’expression conditionnelle* qu’en dernier recours.

## <a name="design-notes"></a>Notes de création

La raison de la modification pour une *meilleure conversion à partir de l’expression* consiste à gérer un cas de ce type :

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

Cette approche présente deux petits inconvénients.  Tout d’abord, il n’est pas tout à fait identique à l’expression de Switch :

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

La raison des notes sur l’expression de cast consiste à gérer un cas de ce type :

```csharp
_ = (short)(b ? 1 : 2);
```

Ce programme utilise actuellement la conversion explicite de `int` en `short` et nous souhaitons conserver le sens du langage actuel de ce programme.  La modification serait impossible à observer lors de l’exécution, mais avec le programme suivant, la modification serait observable :

```csharp
_ = (A)(b ? c : d);
```

où `c` est de type `C` , `d` est de type `D` , et il existe une conversion implicite définie par l’utilisateur de `C` en `D` , et une conversion implicite définie par l’utilisateur de `D` en `A` , et une conversion implicite définie par l’utilisateur de `C` en `A` . Si ce code est compilé avant C# 9,0, lorsque `b` a la valeur true, nous convertissons de `c` en en `D` `A` . Si nous utilisons la *conversion d’expression conditionnelle*, lorsque `b` a la valeur true, la conversion de `c` en est `A` exécutée directement, ce qui exécute une autre séquence de code utilisateur. Par conséquent, nous traitons la *conversion d’expression conditionnelle* en dernier recours dans un cast pour préserver le comportement existant.
