---
ms.openlocfilehash: 922353d043653ddb651534a01f3fb98f85cd756e
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484481"
---
# <a name="readonly-locals-and-parameters"></a>paramètres et variables locales ReadOnly

* [x] proposé
* [] Prototype
* [] Implémentation
* [] Spécification

## <a name="summary"></a>Résumé
[summary]: #summary

Autorisez les variables locales et les paramètres à annoter en lecture seule afin d’éviter une mutation superficielle de ces variables locales et paramètres.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Aujourd’hui, le mot clé `readonly` peut être appliqué aux champs. Cela a pour effet de s’assurer qu’un champ ne peut être écrit que pendant la construction (construction statique dans le cas d’un champ statique, ou construction d’instance dans le cas d’un champ d’instance), ce qui permet aux développeurs d’éviter des erreurs en remplaçant accidentellement l’État qui ne doit pas être modifié. Toutefois, les champs ne sont pas les seuls à pouvoir s’assurer que les valeurs ne sont pas mutées. En particulier, il est courant de créer une variable locale pour stocker l’état temporaire, et la mise à jour accidentelle de cet État peut entraîner des calculs erronés et d’autres bogues, en particulier lorsque ces « variables locales » sont capturées dans des expressions lambda, auquel cas ils sont levés sur des champs, mais il n’existe aucun moyen de marquer ces champs augmentés comme `readonly`.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Les variables locales peuvent également être annotées en tant que `readonly`, le compilateur s’assurant qu’elles sont uniquement définies au moment de la déclaration ( C# certains variables locales dans sont déjà implicitement ReadOnly, telles que la variable d’itération dans une boucle « foreach » ou la variable utilisée dans un bloc « using », mais actuellement, un développeur ne peut pas marquer d’autres variables locales comme `readonly`) Ces `readonly` variables locales doivent avoir un initialiseur :

```csharp
readonly long maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

En guise de raccourci pour `readonly var`, le mot clé contextuel existant `let` peut être utilisé, par exemple

```csharp
let maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

Il n’existe aucune contrainte spéciale pour ce que l’initialiseur peut être, et peut être tout ce qui est actuellement valide en tant qu’initialiseur pour les variables locales, par exemple

```csharp
readonly T data = arg1 ?? arg2;
```

`readonly` sur les variables locales est particulièrement utile lorsque vous utilisez des expressions lambda et des fermetures. Quand une méthode anonyme ou une expression lambda accède à l’état local à partir de la portée englobante, cet État est capturé dans une fermeture par le compilateur, qui est représenté par une « classe d’affichage ».  Chaque « local » capturé est un champ de cette classe, mais étant donné que le compilateur génère ce champ en votre nom, vous n’avez pas la possibilité de l’annoter en tant que `readonly` pour empêcher l’expression lambda d’écrire de manière erronée dans le « local » (guillemets, car il ne s’agit pas vraiment d’un local, au moins dans le MSIL résultant). Avec `readonly` variables locales, le compilateur peut empêcher le lambda d’écrire dans un emplacement local, ce qui est particulièrement utile dans les scénarios impliquant le multithread, où une écriture erronée peut entraîner un bogue de concurrence risqué, mais rare et difficile à trouver.

```csharp
readonly long index = ...;
Parallel.ForEach(data, item => {
    T element = item[index];
    index = 0; // Error: can't assign to readonly locals outside of declaration
});
```

Comme une forme spéciale de locale, les paramètres sont également annotés comme `readonly`. Cela n’a aucun effet sur ce que l’appelant de la méthode est en mesure de passer au paramètre (tout comme il n’existe aucune contrainte sur les valeurs qui peuvent être stockées dans un champ `readonly`), mais comme avec n’importe quel `readonly` local, le compilateur empêche l’écriture du code dans le paramètre après la déclaration, ce qui signifie que le corps de la méthode est interdit d’écrire dans le paramètre

```csharp
public void Update(readonly int index = 0) // Default values are ok though not required
{
    ...
    index = 0; // Error: can't assign to readonly parameters
    ...
}
```

les paramètres de `readonly` n’affectent pas la signature/les métadonnées émises par le compilateur pour cette méthode et affectent simplement la façon dont le compilateur gère la compilation du corps de la méthode. Ainsi, par exemple, une méthode virtuelle de base peut avoir un paramètre `readonly`, et ce paramètre peut être accessible en écriture dans une substitution.

Comme pour les champs, les `readonly` pour les variables locales et les paramètres sont superficielles, ce qui affecte l’emplacement de stockage, mais n’affecte pas de manière transitive le graphique d’objets. Toutefois, comme avec les champs, l’appel d’une méthode sur une `readonly` structure locale/de paramètre effectue en fait une copie de la structure et appelle la méthode sur la copie, afin d’éviter la mutation interne des `this`.

`readonly` paramètres et variables locales ne peuvent pas être passés en tant qu’arguments `ref` ou `out`, sauf si/jusqu’à ce que `ref readonly` soit également pris en charge.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

- `val` peut être utilisé comme autre raccourci pour `let`.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- `readonly ref` / `ref readonly` / `readonly ref readonly`: J’ai laissé la question de savoir comment gérer les `ref readonly` séparément de cette offre.
- Cette proposition ne traite pas les structs/types immuables en lecture seule. Cela reste pour une proposition distincte.

## <a name="design-meetings"></a>Réunions de conception

- Brièvement abordé le 21 janvier 2015 (<https://github.com/dotnet/roslyn/issues/98>)
