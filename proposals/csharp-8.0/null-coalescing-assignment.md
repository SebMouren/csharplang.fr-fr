---
ms.openlocfilehash: 962236dd30365b2e9c33bb1f6ec5328fdf22e90a
ms.sourcegitcommit: c2fe8f1d150ac6ac171072d1c6f9df883adcbb40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83203257"
---
# <a name="null-coalescing-assignment"></a>Attribution de fusion Null

* [x] proposé
* [x] prototype : terminé
* [x] implémentation : terminée
* [x] spécification : ci-dessous

## <a name="summary"></a>Récapitulatif
[summary]: #summary

Simplifie un modèle de codage commun dans lequel une valeur est assignée à une variable si elle est null.

Dans le cadre de cette proposition, nous relâcherons également les exigences de type sur `??` pour permettre l’utilisation d’une expression dont le type est un paramètre de type sans contrainte à utiliser sur le côté gauche.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il est courant de voir le code du formulaire

```csharp
if (variable == null)
{
    variable = expression;
}
```

Cette proposition ajoute un opérateur binaire non surchargeable dans le langage qui exécute cette fonction.

Il y a eu au moins huit demandes de communauté distinctes pour cette fonctionnalité.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Nous ajoutons une nouvelle forme d’opérateur d’assignation

``` antlr
assignment_operator
    : '??='
    ;
```

Qui suit les [règles sémantiques existantes pour les opérateurs d’assignation composée](../../spec/expressions.md#compound-assignment), à l’exception du fait que nous Elide l’assignation si le côté gauche n’est pas null. Les règles de cette fonctionnalité sont les suivantes.

Donné `a ??= b` , où `A` est le type de `a` , `B` est le type de `b` , et `A0` est le type sous-jacent de `A` si `A` est un type valeur Nullable :

1. Si `A` n’existe pas ou s’il s’agit d’un type valeur qui n’autorise pas les valeurs NULL, une erreur de compilation se produit.
2. Si `B` n’est pas implicitement convertible en `A` ou `A0` (s' `A0` il existe), une erreur de compilation se produit.
3. Si `A0` existe et qu’il `B` est implicitement convertible en `A0` et `B` n’est pas dynamique, le type de `a ??= b` est `A0` . `a ??= b`est évaluée au moment de l’exécution comme suit :
   ```C#
   var tmp = a.GetValueOrDefault();
   if (!a.HasValue) { tmp = b; a = tmp; }
   tmp
   ```
   Sauf que `a` n’est évalué qu’une seule fois.
4. Sinon, le type de `a ??= b` est `A` . `a ??= b`est évaluée au moment de l’exécution en tant `a ?? (a = b)` que, sauf que `a` n’est évalué qu’une seule fois.


Pour l’assouplissement des exigences de type de `??` , nous mettons à jour la spécification là où elle déclare actuellement, étant donné que `a ?? b` `A` est le type de `a` :

> 1. Si un existe et n’est pas un type Nullable ou un type référence, une erreur de compilation se produit.

Nous détendons cette exigence pour :

1. Si un existe et qu’il s’agit d’un type valeur n’acceptant pas les valeurs NULL, une erreur de compilation se produit.

Cela permet à l’opérateur de fusion Null de fonctionner sur des paramètres de type sans contrainte, étant donné que le paramètre de type sans contrainte T existe, qu’il n’est pas un type Nullable et qu’il n’est pas un type référence.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le corps des programmes C# qui tireraient parti de la fonctionnalité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Le programmeur peut écrire `(x = x ?? y)` , `if (x == null) x = y;` ou `x ?? (x = y)` à la main.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Requiert la revue LDM
- [] Devons-nous également prendre en charge `&&=` les `||=` opérateurs et ?

## <a name="design-meetings"></a>Réunions de conception

Aucun.
