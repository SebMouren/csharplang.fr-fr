---
ms.openlocfilehash: ecdad8c863d0695bc901e4d96d9ca3decbc248eb
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484614"
---
# <a name="nullable-reference-types-in-c"></a>Types référence Nullable dansC# #

L’objectif de cette fonctionnalité est de :

* Permet aux développeurs d’exprimer si une variable, un paramètre ou un résultat d’un type référence est destiné à être null ou non.
* Fournissez des avertissements lorsque ces variables, paramètres et résultats ne sont pas utilisés conformément à cet objectif.

## <a name="expression-of-intent"></a>Expression de l’intention

La langue contient déjà la syntaxe `T?` pour les types valeur. Il est facile d’étendre cette syntaxe aux types référence.

Il est supposé que l’objectif d’un type de référence sans ornement `T` est qu’il doit être non null.

## <a name="checking-of-nullable-references"></a>Vérification des références Nullable

Une analyse de fluide effectue le suivi des variables de référence Nullable. Lorsque l’analyse estime qu’elles ne sont pas nulles (par exemple, après une vérification ou une affectation), leur valeur est considérée comme une référence non null.

Une référence Nullable peut également être traitée explicitement comme non null avec l’opérateur de suffixe `x!` (opérateur « Dammit »), lorsque l’analyse du workflow ne peut pas établir une situation non null que le développeur connaît.

Dans le cas contraire, un avertissement est fourni si une référence Nullable est déréférencée ou est convertie en un type non null.

Un avertissement est fourni lors de la conversion de `S[]` en `T?[]` et de `S?[]` à `T[]`.

Un avertissement est fourni lors de la conversion de `C<S>` en `C<T?>` sauf lorsque le paramètre de type est covariant (`out`), et lors de la conversion de `C<S?>` en `C<T>`, sauf lorsque le paramètre de type est contravariant (`in`).

Un avertissement est fourni sur `C<T?>` si le paramètre de type a des contraintes non null. 

## <a name="checking-of-non-null-references"></a>Vérification des références non null

Un avertissement est fourni si un littéral null est assigné à une variable non null ou passé comme paramètre non null.

Un avertissement est également fourni si un constructeur n’initialise pas explicitement les champs de référence non null.

Nous ne pouvons pas suivre correctement l’initialisation de tous les éléments d’un tableau de références non null. Toutefois, nous pouvons émettre un avertissement si aucun élément d’un tableau nouvellement créé n’est assigné avant que le tableau soit lu ou passé. Cela peut gérer le cas courant sans trop bruyant.

Nous devons décider si `default(T)` génère un avertissement ou si elle est simplement traitée comme étant du type `T?`.

## <a name="metadata-representation"></a>Représentation des métadonnées

Les ornements de possibilité de valeur NULL doivent être représentés dans les métadonnées en tant qu’attributs. Cela signifie que les compilateurs de niveau inférieur les ignorent.

Nous devons décider si seules les annotations Nullable sont incluses, ou il existe également une indication précisant si la valeur non null était « on » dans l’assembly.

## <a name="generics"></a>Génériques

Si un paramètre de type `T` a des contraintes n’acceptant pas les valeurs NULL, il est traité comme n’acceptant pas les valeurs NULL dans sa portée.

Si un paramètre de type est sans contrainte ou n’a que des contraintes Nullable, la situation est un peu plus complexe : cela signifie que l’argument de type correspondant peut avoir la *valeur* null ou n’accepte pas les valeurs NULL. La seule chose à faire dans cette situation est de traiter le paramètre de type comme Nullable et non Nullable, en donnant *des* avertissements quand l’un ou l’autre est violé. 

Il est important de considérer si les contraintes de référence Nullable explicites doivent être autorisées. Notez, toutefois, que nous ne pouvons pas éviter que les types de référence Nullable soient *implicitement* des contraintes dans certains cas (contraintes héritées).

La contrainte de `class` est non null. Nous pouvons déterminer si `class?` doit être une contrainte Nullable valide qui désigne « type de référence Nullable ».

## <a name="type-inference"></a>Inférence de type

Dans l’inférence de type, si un type contributeur est un type référence Nullable, le type résultant doit être Nullable. En d’autres termes, la valeur null est propagée.

Nous devons déterminer si le littéral `null` en tant qu’expression participante doit contribuer à la valeur null. Ce n’est pas aujourd’hui le cas : pour les types de valeur, il génère une erreur, tandis que pour les types de référence, la valeur null est convertie avec succès en type brut. 

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="breaking-changes"></a>Changements cassants

Les avertissements non NULL sont une modification avec rupture évidente sur le code existant et doivent être accompagnés d’un mécanisme d’abonnement.

Moins évidemment, les avertissements des types Nullable (comme décrit ci-dessus) sont une modification avec rupture sur le code existant dans certains scénarios où la possibilité de valeur null est implicite :

* Les paramètres de type sans contrainte sont traités comme des valeurs NULL implicites, de sorte que leur affectation à `object` ou l’accès à, par exemple, `ToString` généreront des avertissements.
* Si l’inférence de type déduit la valeur null des expressions `null`, le code existant peut parfois générer des valeurs NULL plutôt que des types non Nullable, ce qui peut entraîner de nouveaux avertissements.

Les avertissements Nullable doivent également être facultatifs

Enfin, l’ajout d’annotations à une API existante sera une modification avec rupture pour les utilisateurs qui ont opté pour les avertissements lorsqu’ils effectuent la mise à niveau de la bibliothèque. Cela vous permet également d’accepter ou de refuser l’inscription. « Je veux les correctifs de bogues, mais je ne suis pas prêt à gérer leurs nouvelles annotations »

En résumé, vous devez être en mesure d’accepter/de refuser :
* Avertissements Nullable
* Avertissements non null
* Avertissements d’annotations dans d’autres fichiers

La granularité de l’abonnement suggère un modèle de type analyseur, où les quantités de code peuvent s’inscrire et sortir avec des pragmas et des niveaux de gravité peuvent être choisis par l’utilisateur. En outre, les options par bibliothèque (« ignorer les annotations de JSON.NET jusqu’à ce que je suis prêt à gérer le passage ») peuvent être exprimables dans le code en tant qu’attributs.

La conception de l’expérience d’abonnement/transition est cruciale pour la réussite et l’utilité de cette fonctionnalité. Nous devons nous assurer que :

* Les utilisateurs peuvent adopter une vérification de la possibilité de valeur NULL comme ils le souhaitent
* Les auteurs de bibliothèques peuvent ajouter des annotations de possibilité de valeur null sans craindre de perturber les clients
* En dépit de ceux-ci, il n’y a pas de sens de « cauchemar de la configuration »

## <a name="tweaks"></a>Ajustements

Nous pourrions envisager de ne pas utiliser les annotations `?` sur les variables locales, mais simplement d’observer s’ils sont utilisés conformément à ce qui leur est assigné. Je ne préfère pas cela. Je pense que nous devrions permettre aux utilisateurs d’exprimer leur intention de manière uniforme.

Nous pourrions envisager une `T! x` abrégée sur les paramètres, qui génère automatiquement une vérification de la valeur null au moment de l’exécution.

Certains modèles sur des types génériques, tels que `FirstOrDefault` ou `TryGet`, ont un comportement légèrement étrange avec les arguments de type non Nullable, car ils génèrent explicitement des valeurs par défaut dans certaines situations. Nous pourrions essayer de nuancer le système de type pour s’adapter à ces améliorations. Par exemple, nous pourrions autoriser `?` sur des paramètres de type sans contrainte, même si l’argument de type peut déjà être Nullable. Je n’ai pas de doute qu’il en mérite, et cela a pour conséquence l’étrange liée à l’interaction avec les types *valeur* Nullable. 

## <a name="nullable-value-types"></a>Types valeur Nullable

Nous pourrions envisager d’adopter également une partie de la sémantique ci-dessus pour les types valeur Nullable.

Nous avons déjà mentionné l’inférence de type, où nous pourrions déduire `int?` à partir de `(7, null)`, au lieu de donner simplement une erreur.

Une autre possibilité consiste à appliquer l’analyse du workflow à des types valeur Nullable. Lorsqu’ils sont considérés comme non null, nous pourrions réellement autoriser l’utilisation de comme type non Nullable de certaines façons (par exemple, l’accès aux membres). Nous devons simplement veiller à ce que les choses que vous puissiez *déjà* faire sur un type de valeur Nullable soient préférées, pour des raisons de compatibilité de retour.
