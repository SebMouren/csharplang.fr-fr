---
ms.openlocfilehash: 54f9a372ca0329a284f06876f544e0b4936af02a
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82037400"
---
# <a name="nullable-reference-types-in-c"></a>Types de référence nuls en C # #

Le but de cette fonctionnalité est de :

* Permettre aux développeurs d’exprimer si une variable, un paramètre ou un résultat d’un type de référence est destiné à être nul ou non.
* Fournir des avertissements lorsque de telles variables, paramètres et résultats ne sont pas utilisés en fonction de cette intention.

## <a name="expression-of-intent"></a>Expression d’intention

La langue contient `T?` déjà la syntaxe pour les types de valeur. Il est simple d’étendre cette syntaxe aux types de référence.

On suppose que l’intention d’un `T` type de référence sans fioritures est qu’il soit non nul.

## <a name="checking-of-nullable-references"></a>Vérification des références annulées

Une analyse de flux suit les variables de référence annulables. Lorsque l’analyse estime qu’ils ne seraient pas nuls (p. ex., après une vérification ou une cession), leur valeur sera considérée comme une référence non nulle.

Une référence annulée peut également être traitée explicitement comme `x!` non nulle avec l’opérateur de poteaux (l’opérateur "damnit"), car lorsque l’analyse du débit ne peut pas établir une situation non nulle que le développeur sait être là.

Dans le cas contraire, un avertissement est donné si une référence annulée est déreférée ou est convertie en un type non nul.

Un avertissement est donné `S[]` lors `T?[]` de `S?[]` `T[]`la conversion de et de .

Un avertissement est donné `C<S>` lors `C<T?>` de la conversion à partir`out`de sauf lorsque `C<S?>` le `C<T>` paramètre de type est covariant ( ), et lors de la conversion à partir de sauf lorsque le paramètre de type est contravariant (`in`).

Un avertissement est `C<T?>` donné sur si le paramètre de type a des contraintes non nulles.

## <a name="checking-of-non-null-references"></a>Vérification des références non nulles

Un avertissement est donné si un littéral nul est attribué à une variable non nulle ou adopté comme paramètre non nul.

Un avertissement est également donné si un constructeur n’initialise pas explicitement les champs de référence non nuls.

Nous ne pouvons pas suivre adéquatement que tous les éléments d’un éventail de références non nulles sont initialisés. Cependant, nous pourrions émettre un avertissement si aucun élément d’un tableau nouvellement créé n’est attribué avant que le tableau ne soit lu ou transmis. Cela pourrait gérer le cas commun sans être trop bruyant.

Nous devons décider `default(T)` si génère un avertissement, ou est simplement `T?`traité comme étant du type .

## <a name="metadata-representation"></a>Représentation des métadonnées

Les ornements d’annulation doivent être représentés dans les métadonnées comme attributs. Cela signifie que les compilateurs à bas de niveau les ignoreront.

Nous devons décider si seules des annotations annulables sont incluses, ou il y a aussi une indication de si non-null était "sur" à l’Assemblée.

## <a name="generics"></a>Génériques

Si un `T` paramètre de type a des contraintes non annulables, il est considéré comme non annulable dans son champ d’application.

Si un paramètre de type n’est pas tendu ou n’a que des contraintes annulables, la situation est un peu plus complexe : cela signifie que *l’argument* de type correspondant peut être soit annulable, soit non annulable. La chose sûre à faire dans cette situation est de traiter le paramètre de type comme à la *fois* annulable et non-nullable, donnant des avertissements lorsque l’un ou l’autre est violé. 

Il convient d’examiner si des contraintes de référence nulles explicites devraient être autorisées. Notez toutefois que nous ne pouvons éviter que les types de référence nuls soient *implicitement* des contraintes dans certains cas (contraintes héritées).

La `class` contrainte n’est pas nulle. Nous pouvons `class?` examiner s’il devrait s’agir d’une contrainte nulle valide indiquant le « type de référence nul ».

## <a name="type-inference"></a>Inférence type

Dans l’inférence de type, si un type contributif est un type de référence nul, le type résultant doit être annulé. En d’autres termes, la nullité est propagée.

Nous devrions nous `null` demander si le littéral en tant qu’expression participante devrait contribuer à l’nullité. Il n’est pas aujourd’hui: pour les types de valeur, il conduit à une erreur, tandis que pour les types de référence les null convertis avec succès au type simple.

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="null-guard-guidance"></a>Guidage de garde nul

En tant que caractéristique, les types de référence inquérables permettent aux développeurs d’exprimer leur intention et de fournir des avertissements par l’analyse du débit si cette intention est contredite. Il y a une question commune quant à savoir si des gardes nuls sont nécessaires ou non.

### <a name="example-of-null-guard"></a>Exemple de garde nulle

```csharp
public void DoWork(Worker worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

Dans l’exemple `DoWork` précédent, la `Worker` fonction accepte un `null`et se garde contre elle potentiellement être . Si `worker` l’argument est `null`, la `DoWork` fonction sera `throw`. Avec les types de référence nuls, le code `Worker` dans l’exemple précédent fait l’intention que le paramètre *ne* serait `null`pas . Si `DoWork` la fonction était une API publique, comme un paquet NuGet ou une bibliothèque partagée - comme des conseils, vous devriez laisser les gardes nuls en place. En tant qu’API public, la seule garantie `null` qu’un appelant ne passe pas est de se prémunir contre elle.

### <a name="express-intent"></a>Intention expresse

Une utilisation plus convaincante de l’exemple `Worker` précédent `null`est d’exprimer que le paramètre pourrait être, ce qui rend la garde nulle plus appropriée. Si vous supprimez la garde nulle dans l’exemple suivant, le compilateur avertit que vous pouvez être différenciation nulle. Quoi qu’il en soit, les deux gardes nuls sont toujours valides.

```csharp
public void DoWork(Worker? worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

Pour les API non publiques, telles que le code source entièrement sous contrôle par un développeur ou une équipe de développement - les types de référence nuls pourraient permettre la suppression en toute sécurité des gardes nuls lorsque les développeurs peuvent garantir qu’il n’est pas nécessaire. La fonctionnalité peut aider avec des avertissements, mais il ne `NullReferenceException`peut pas garantir qu’à l’exécution du code runtime pourrait entraîner un .

## <a name="breaking-changes"></a>Changements cassants

Les avertissements non nuls sont une modification évidente de rupture sur le code existant, et doivent être accompagnés d’un mécanisme d’opt-in.

De toute évidence, les avertissements de types nuls (comme décrit ci-dessus) sont une modification de rupture sur le code existant dans certains scénarios où l’annulation est implicite :

* Les paramètres de type non tendus seront traités comme implicitement `object` in annulables, `ToString` de sorte que les attribuer ou y accéder, par exemple, donnera des avertissements.
* si l’inférence de type `null` déduit l’annulation des expressions, alors le code existant produira parfois des types nuls plutôt que non annulables, ce qui peut conduire à de nouveaux avertissements.

Les avertissements nuls doivent donc également être facultatifs

Enfin, l’ajout d’annotations à une API existante sera un changement de rupture pour les utilisateurs qui ont opté pour les avertissements, quand ils mettent à niveau la bibliothèque. Cela, aussi, mérite la possibilité d’opter pour ou hors. "Je veux que les corrections des insectes, mais je ne suis pas prêt à faire face à leurs nouvelles annotations"

En résumé, vous devez être en mesure d’opter pour/hors de:
* Avertissements nuls
* Avertissements non nuls
* Avertissements d’annotations dans d’autres fichiers

La granularité de l’opt-in suggère un modèle de type analyseur, où des bandes de code peuvent opter avec des pragmas et des niveaux de gravité peuvent être choisis par l’utilisateur. En outre, les options par bibliothèque ("ignorer les annotations de JSON.NET jusqu’à ce que je suis prêt à faire face à la chute") peut être expressible dans le code comme attributs.

La conception de l’expérience opt-in/transition est cruciale pour le succès et l’utilité de cette fonctionnalité. Nous devons nous assurer que :

* Les utilisateurs peuvent adopter la vérification de la nullabilité progressivement comme ils le souhaitent
* Les auteurs de la bibliothèque peuvent ajouter des annotations de nullabilité sans crainte de briser les clients
* Malgré cela, il n’y a pas un sentiment de «cauchemar de configuration»

## <a name="tweaks"></a>Réglages

Nous pourrions envisager `?` de ne pas utiliser les annotations sur les habitants, mais simplement d’observer si elles sont utilisées conformément à ce qui leur est assigné. Je ne suis pas en faveur de cela; Je pense que nous devrions uniformément laisser les gens exprimer leur intention.

Nous pourrions envisager `T! x` un raccourci sur les paramètres, qui génère automatiquement une vérification nulle durée de course.

Certains modèles sur les `FirstOrDefault` types `TryGet`génériques, tels que ou , ont un comportement légèrement bizarre avec des arguments de type non-nullable, parce qu’ils donnent explicitement des valeurs par défaut dans certaines situations. Nous pourrions essayer de nuancer le système de type pour mieux les accueillir. Par exemple, nous `?` pourrions autoriser sur les paramètres de type non tendu, même si l’argument de type pourrait déjà être annulé. Je doute que cela en vaut la peine, et il conduit à l’étrangeté liée à l’interaction avec les types de *valeur* nulle. 

## <a name="nullable-value-types"></a>Types valeur Nullable

Nous pourrions envisager d’adopter certaines des sémantiques ci-dessus pour les types de valeur nulle ainsi.

Nous avons déjà mentionné l’inférence `int?` `(7, null)`de type, où nous pourrions déduire de , au lieu de simplement donner une erreur.

Une autre occasion est d’appliquer l’analyse du débit aux types de valeur nuls. Lorsqu’ils sont jugés non nuls, nous pourrions en fait permettre d’utiliser comme type non annulable de certaines façons (p. ex. l’accès des membres). Nous devons juste faire attention que les choses que vous pouvez *déjà* faire sur un type de valeur nulle sera préférée, pour des raisons de compat arrière.
