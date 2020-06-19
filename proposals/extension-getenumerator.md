---
ms.openlocfilehash: a774e370a14373ccf308c2cba9944305c0053c05
ms.sourcegitcommit: aa2ff4d18a54d3999b7d014fcae38b2435e15510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84978645"
---
# <a name="extension-getenumerator-support-for-foreach-loops"></a>`GetEnumerator`Prise en charge des extensions pour les `foreach` boucles.

## <a name="summary"></a>Résumé
[summary]: #summary

Autoriser les boucles Foreach à reconnaître une méthode d’extension GetEnumerator qui, sinon, répond au modèle foreach, et effectue une boucle sur l’expression lorsqu’il s’agit d’une erreur.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Foreach sera incorporé avec la manière dont les autres fonctionnalités en C# sont implémentées, notamment Async et la déconstruction basée sur des modèles.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

La modification des spécifications est relativement simple. Nous modifions `The foreach statement` la section à ce texte :

>Le traitement au moment de la compilation d’une instruction foreach détermine d’abord le type de ***collection***, le type d' ***énumérateur*** et le ***type d’élément*** de l’expression. Cette détermination se déroule comme suit :
>
>*  Si le type `X` d' *expression* est un type tableau, il existe une conversion de référence implicite de `X` en `IEnumerable` interface (puisque `System.Array` implémente cette interface). Le ***type de collection*** est l' `IEnumerable` interface, le ***type d’énumérateur*** est l' `IEnumerator` interface et le ***type d’élément*** est le type d’élément du type tableau `X` .
>*  Si le type `X` d' *expression* est `dynamic` , il y a une conversion implicite d' *expression* en `IEnumerable` interface ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions)). Le ***type de collection*** est l' `IEnumerable` interface et le ***type d’énumérateur*** est l' `IEnumerator` interface. Si l' `var` identificateur est donné comme *local_variable_type* , le type d' ***élément*** est `dynamic` , sinon, `object` .
>*  Sinon, déterminez si le type `X` a une `GetEnumerator` méthode appropriée :
>    * Effectuez une recherche de membre sur le type `X` avec l’identificateur `GetEnumerator` et aucun argument de type. Si la recherche de membre ne produit pas de correspondance, si elle génère une ambiguïté ou si elle produit une correspondance qui n’est pas un groupe de méthodes, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé d’émettre un avertissement si la recherche de membres produit tout sauf un groupe de méthodes ou aucune correspondance.
>    * Exécutez la résolution de surcharge à l’aide du groupe de méthodes résultant et d’une liste d’arguments vide. Si la résolution de surcharge entraîne l’absence de méthodes applicables, provoque une ambiguïté ou produit une seule meilleure méthode, mais que cette méthode est statique ou non publique, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé d’émettre un avertissement si la résolution de surcharge produit tout sauf une méthode d’instance publique non ambiguë ou aucune méthode applicable.
>    * Si le type `E` de retour de la `GetEnumerator` méthode n’est pas un type de classe, de struct ou d’interface, une erreur est générée et aucune autre étape n’est prise.
>    * La recherche de membres est effectuée sur `E` avec l’identificateur `Current` et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf une propriété d’instance publique qui autorise la lecture, une erreur est générée et aucune autre étape n’est effectuée.
>    * La recherche de membres est effectuée sur `E` avec l’identificateur `MoveNext` et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf un groupe de méthodes, une erreur est générée et aucune autre étape n’est prise.
>    * La résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide. Si la résolution de surcharge entraîne l’absence de méthodes applicables, entraîne une ambiguïté ou produit une seule meilleure méthode, mais cette méthode est statique ou non publique, ou son type de retour n’est pas `bool` , une erreur est générée et aucune autre étape n’est prise.
>    * Le ***type de collection*** est `X` , le ***type d’énumérateur*** est `E` , et le type d' ***élément*** est le type de la `Current` propriété.
>
>*  Sinon, recherchez une interface énumérable :
>    * Si parmi tous les types `Ti` pour lesquels il existe une conversion implicite de `X` en `IEnumerable<Ti>` , il existe un type unique `T` , qui `T` n’est pas `dynamic` et pour tout l’autre, `Ti` il existe une conversion implicite de `IEnumerable<T>` en `IEnumerable<Ti>` , le type de ***collection*** est l’interface `IEnumerable<T>` , le type d' ***énumérateur*** est l’interface `IEnumerator<T>` et le type d' ***élément*** est `T` .
>    * Sinon, s’il existe plusieurs types de ce type `T` , une erreur est générée et aucune autre étape n’est effectuée.
>    * Sinon, s’il existe une conversion implicite de `X` en `System.Collections.IEnumerable` interface, le ***type de collection*** est cette interface, le ***type d’énumérateur*** est l’interface `System.Collections.IEnumerator` et le ***type d’élément*** est `object` .
>*  Sinon, déterminez si le type’X’a une `GetEnumerator` méthode d’extension appropriée :
>    * Effectue une recherche de méthode d’extension sur le type `X` avec l’identificateur `GetEnumerator` . Si la recherche de membre ne produit pas de correspondance, si elle génère une ambiguïté ou si elle produit une correspondance qui n’est pas un groupe de méthodes, une erreur est générée et aucune autre étape n’est effectuée. Il est recommandé qu’un avertissement soit émis si la recherche de membres produit n’importe quoi, sauf un groupe de méthodes ou aucune correspondance.
>    * Exécutez la résolution de surcharge à l’aide du groupe de méthodes résultant et d’un argument unique de type `X` . Si la résolution de surcharge ne produit aucune méthode applicable, provoque une ambiguïté ou produit une seule meilleure méthode mais que cette méthode n’est pas accessible, une erreur est générée. aucune autre étape n’est effectuée.
>        * Cette résolution permet au premier argument d’être passé par Ref si `X` est un type struct et le genre Ref est `in` .
>    * Si le type `E` de retour de la `GetEnumerator` méthode n’est pas un type de classe, de struct ou d’interface, une erreur est générée et aucune autre étape n’est prise.
>    * La recherche de membres est effectuée sur `E` avec l’identificateur `Current` et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf une propriété d’instance publique qui autorise la lecture, une erreur est générée et aucune autre étape n’est effectuée.
>    * La recherche de membres est effectuée sur `E` avec l’identificateur `MoveNext` et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf un groupe de méthodes, une erreur est générée et aucune autre étape n’est prise.
>    * La résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide. Si la résolution de surcharge entraîne l’absence de méthodes applicables, entraîne une ambiguïté ou produit une seule meilleure méthode, mais cette méthode est statique ou non publique, ou son type de retour n’est pas `bool` , une erreur est générée et aucune autre étape n’est prise.
>    * Le ***type de collection*** est `X` , le ***type d’énumérateur*** est `E` , et le type d' ***élément*** est le type de la `Current` propriété.
>*  Dans le cas contraire, une erreur est générée et aucune autre étape n’est prise.

Pour `await foreach` , les règles sont modifiées de façon similaire. La seule modification requise pour cette spécification consiste à supprimer la `Extension methods do not contribute.` ligne de la description, car le reste de cette spécification est basé sur les règles ci-dessus avec des noms différents substitués pour les méthodes de modèle.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Chaque modification ajoute une complexité supplémentaire à la langue, ce qui peut permettre d’effectuer des tâches qui n’ont pas été conçues pour être `foreach` `foreach` Ed, comme `Range` .

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Ne rien faire.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Aucun à ce stade.
