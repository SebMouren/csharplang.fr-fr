---
ms.openlocfilehash: 4f66c0f60d05ed6509a1d0843318a71d1b36c351
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484705"
---
# <a name="pattern-matching-with-generics"></a>mise en correspondance de modèle avec des génériques

* [x] proposé
* [] Prototype :
* [] Implémentation :
* [] Spécification :

## <a name="summary"></a>Résumé
[summary]: #summary

La spécification de l' [opérateur C# As existant](../../spec/expressions.md#the-as-operator) permet de ne pas effectuer de conversion entre le type de l’opérande et le type spécifié lorsqu’il s’agit d’un type ouvert. Toutefois, dans C# 7, le modèle de `Type identifier` nécessite une conversion entre le type de l’entrée et le type donné.

Nous vous proposons de l’assouplir et de modifier `expression is Type identifier`, en plus d’être autorisé dans les conditions lorsqu’il C# est autorisé dans 7, d’être également autorisé lorsque `expression as Type` serait autorisé. Plus précisément, les nouveaux cas sont des cas où le type de l’expression ou le type spécifié est un type ouvert. 

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Les cas où la mise en correspondance des modèles doit être « évidemment » ne sont pas autorisés actuellement à compiler. Voir, par exemple, https://github.com/dotnet/roslyn/issues/16195.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Nous modifions le paragraphe dans la spécification de critères spéciaux (l’ajout proposé est affiché en gras) :

> Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation. Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T` **, ou si `E` ou `T` est un type ouvert**. Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Aucun.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Aucun.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Aucun.

## <a name="design-meetings"></a>Réunions de conception

LDM a considéré cette question et a pensé qu’il s’agissait d’une modification du niveau de correction des bogues. Nous le traitons comme une fonctionnalité de langage distincte, car l’apport de modifications après la publication du langage introduirait une incompatibilité de transfert. L’utilisation de la modification proposée requiert que le programmeur spécifie la version de langue 7,1.
