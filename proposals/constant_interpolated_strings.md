---
ms.openlocfilehash: 4400cc56fc52fb36cdd19809ff7fb8f13706c161
ms.sourcegitcommit: eb00bb077e46c46807d804e9e1de3d794fb32405
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85070844"
---
# <a name="constant-interpolated-strings"></a>Chaînes interpolées constantes

* [x] proposé
* [] Prototype : [non démarré](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)
* [] Implémentation : [non démarrée](https://github.com/dotnet/roslyn/BRANCH_NAME)
* [] Spécification : [non démarrée](pr/1)

## <a name="summary"></a>Résumé
[summary]: #summary

Permet de générer des constantes à partir de chaînes interpolées de type chaîne constante.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Le code suivant est déjà conforme :
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
Toutefois, il y a eu de nombreuses demandes de la communauté pour que les conditions suivantes soient également valides :
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
Cette proposition représente l’étape logique suivante pour la génération de chaînes constantes, où la syntaxe de chaîne existante qui fonctionne dans d’autres situations est mise en œuvre pour les constantes.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Les éléments suivants représentent les spécifications mises à jour pour les expressions constantes sous cette nouvelle proposition. Les spécifications actuelles à partir desquelles ce est basé directement sont disponibles [ici](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).

### <a name="constant-expressions"></a>Expressions constantes

Une *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.

```antlr
constant_expression
    : expression
    ;
```

Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `bool` , `object` , `string` ou tout type énumération. Seules les constructions suivantes sont autorisées dans les expressions constantes :

*  Littéraux (y compris le `null` littéral).
*  Références aux `const` membres de types classe et struct.
*  Références aux membres de types énumération.
*  Références à des `const` paramètres ou à des variables locales
*  Sous-expressions entre parenthèses, qui sont elles-mêmes des expressions constantes.
*  Expressions de cast, à condition que le type cible soit l’un des types indiqués ci-dessus.
*  `checked``unchecked`expressions et
*  Expressions de valeur par défaut
*  Expressions Nameof
*  Opérateurs unaires prédéfinis, `+` `-` , `!` et `~` .
*  Les opérateurs prédéfinis,,,,,, `+` `-` ,,,, `*` `/` `%` `<<` `>>` `&` `|` `^` `&&` , `||` , `==` , `!=` , `<` , `>` , `<=` et `>=` binaires, à condition que chaque opérande soit d’un type indiqué ci-dessus.
*  `?:`Opérateur conditionnel.
*  *Chaînes interpolées `${}` , à condition que tous les composants soient des expressions constantes de type `string` et que tous les composants interpolés n’aient pas d’alignement et de spécificateurs de format.*

Les conversions suivantes sont autorisées dans les expressions constantes :

*  Conversions d’identité
*  Conversions numériques
*  Conversions d’énumération
*  Conversions d’expressions constantes
*  Conversions de référence implicites et explicites, à condition que la source des conversions soit une expression constante qui prend la valeur null.

Les autres conversions, y compris les conversions boxing, unboxing et implicites des valeurs non null, ne sont pas autorisées dans les expressions constantes. Par exemple :
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l’initialisation de i est une erreur, car une conversion boxing est requise. L’initialisation de str est une erreur, car une conversion de référence implicite à partir d’une valeur non null est requise.

Chaque fois qu’une expression répond aux spécifications mentionnées ci-dessus, l’expression est évaluée au moment de la compilation. Cela est vrai même si l’expression est une sous-expression d’une expression plus grande qui contient des constructions non constantes.

L’évaluation au moment de la compilation des expressions constantes utilise les mêmes règles que l’évaluation au moment de l’exécution des expressions non constantes, à la différence près que lorsque l’évaluation au moment de l’exécution a levé une exception, l’évaluation au moment de la compilation provoque une erreur au moment de la compilation.

À moins qu’une expression constante ne soit explicitement placée dans un `unchecked` contexte, les débordements qui se produisent dans les opérations arithmétiques de type intégral et les conversions pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation ([expressions constantes](expressions.md#constant-expressions)).

Les expressions constantes se trouvent dans les contextes listés ci-dessous. Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.

*  Déclarations de constantes ([constantes](classes.md#constants)).
*  Déclarations de membre d’énumération ([membres enum](enums.md#enum-members)).
*  Arguments par défaut des listes de paramètres formels ([paramètres de méthode](classes.md#method-parameters))
*  `case`étiquettes d’une `switch` instruction ([instruction switch](statements.md#the-switch-statement)).
*  `goto case`instructions ([instruction goto](statements.md#the-goto-statement)).
*  Longueurs de dimension dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.
*  Attributs ([attributs](attributes.md)).

Une conversion d’expression constante implicite ([conversions d’expressions constantes implicites](conversions.md#implicit-constant-expression-conversions)) permet de convertir une expression constante de type en `int` `sbyte` , `byte` ,, `short` `ushort` , `uint` ou `ulong` , à condition que la valeur de l’expression constante soit comprise dans la plage du type de destination.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Cette proposition ajoute une complexité supplémentaire au compilateur dans Exchange pour une applicabilité plus large des chaînes interpolées. Étant donné que ces chaînes sont entièrement évaluées au moment de la compilation, les fonctionnalités de mise en forme automatique importantes des chaînes interpolées sont moins nécessaires. La plupart des cas d’usage peuvent être répliqués en grande partie par le biais des alternatives ci-dessous.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

L' `+` opérateur actuel pour les concatnation de chaîne peut combiner des chaînes de manière similaire à la proposition actuelle.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

Quelles parties de la conception sont toujours en cours ?

## <a name="design-meetings"></a>Réunions de conception

Créez un lien vers les notes de conception qui affectent cette proposition, et décrivez-la en une phrase pour chaque modification apportée.


