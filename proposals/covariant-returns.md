---
ms.openlocfilehash: 392d932459ff0a4cb0d6d32c1606f73f9b913c68
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484453"
---
# <a name="covariant-return-types"></a>types de retour covariants

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Prennent en charge les _types de retour covariants_. En particulier, autorisez une méthode de substitution à avoir un type de référence plus dérivé que la méthode qu’elle substitue.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il s’agit d’un modèle courant dans le code selon lequel différents noms de méthode doivent être inventés pour contourner la contrainte de langage que les substitutions doivent retourner au même type que la méthode substituée. Voir ci-dessous un exemple de la base de code Roslyn.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Prennent en charge les _types de retour covariants_. En particulier, autorisez une méthode de substitution à avoir un type de référence plus dérivé que la méthode qu’elle substitue. Cela s’applique aux méthodes et aux propriétés, et est pris en charge dans les classes et les interfaces.

Cela peut être utile dans le modèle de fabrique. Par exemple, dans la base de code Roslyn, nous aurions

``` cs
class Compilation ...
{
    virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    override CSharpCompilation WithOptions(Options options)...
}
```

L’implémentation de cela serait que le compilateur émette la méthode de substitution comme une nouvelle méthode virtuelle qui masque la méthode de la classe de base, avec une _méthode de pont_ qui implémente la méthode de la classe de base avec un appel à la méthode de la classe dérivée.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

- [] Chaque modification de langue doit payer pour elle-même.
- [] Nous devons nous assurer que les performances sont raisonnables, même dans le cas des hiérarchies d’héritage profond
- [] Nous devons nous assurer que les artefacts de la stratégie de traduction n’affectent pas la sémantique de la langue, même lors de la consommation du nouveau langage intermédiaire à partir des compilateurs anciens.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Nous pourrions assouplir légèrement les règles de langage pour autoriser, dans la source,

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Comment les API qui ont été compilées pour utiliser cette fonctionnalité fonctionnent-elles dans des versions antérieures du langage ?

## <a name="design-meetings"></a>Réunions de conception

Aucun. Il y a eu une discussion sur <https://github.com/dotnet/roslyn/issues/357>.