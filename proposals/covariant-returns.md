---
ms.openlocfilehash: 9cfc0758f16b2153d52faec1d19f0ecd817cde3b
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162085"
---
# <a name="covariant-return-types"></a>types de retour covariants

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [X] spécification : non démarrée

## <a name="summary"></a>Récapitulatif
[summary]: #summary

Prennent en charge les _types de retour covariants_. En particulier, autorisez la substitution d’une méthode à retourner un type de retour plus dérivé que la méthode qu’elle substitue, et de la même manière pour autoriser la substitution d’une propriété en lecture seule à retourner un type de retour plus dérivé. Les appelants de la méthode ou de la propriété recevaient statiquement le type de retour plus affiné à partir d’un appel, et les substitutions apparaissant dans des types plus dérivés seraient nécessaires pour fournir un type de retour au moins aussi spécifique que celui qui apparaît dans les substitutions dans ses types de base.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il s’agit d’un modèle courant dans le code selon lequel différents noms de méthode doivent être inventés pour contourner la contrainte de langage que les substitutions doivent retourner au même type que la méthode substituée.

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

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Il s’agit d’un brouillon de spécification proposée pour les [types de retour covariants](https://github.com/dotnet/csharplang/issues/49) en C#.  Notre intention est de permettre à la substitution d’une méthode de retourner un type de retour plus dérivé que la méthode qu’elle substitue, et de la même façon d’autoriser la substitution d’une propriété en lecture seule pour retourner un type de retour plus dérivé.  Les appelants de la méthode ou de la propriété recevaient statiquement le type de retour plus affiné à partir d’un appel, et les substitutions apparaissant dans des types plus dérivés seraient nécessaires pour fournir un type de retour au moins aussi spécifique que celui qui apparaît dans les substitutions dans ses types de base.

Il s’agit d’un premier brouillon. il a donc été entièrement inventé.  La plupart des idées introduites sont provisoires et peuvent être révisées ou éliminées lors de futures révisions.

--------------

### <a name="class-method-override"></a>Substitution de méthode de classe

[Contrainte existante sur les méthodes de substitution de classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods)

> - La méthode de substitution et la méthode de base substituée ont le même type de retour.

est modifié en

> - La méthode de substitution doit avoir un type de retour convertible par une identité ou une conversion de référence implicite en type de retour de la méthode de base substituée.

Et les conditions supplémentaires suivantes sont ajoutées à cette liste :

> - La méthode de substitution doit avoir un type de retour convertible par une identité ou une conversion de référence implicite en type de retour de chaque substitution de la méthode de base substituée qui est déclarée dans un type de base (direct ou indirect) de la méthode de substitution.
> - Le type de retour de la méthode de substitution doit être au moins aussi accessible que la méthode de substitution ([domaines d’accessibilité](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).

Cette contrainte permet à une méthode de substitution dans `private` une classe d’avoir `private` un type de retour.  Toutefois, `public` une méthode de substitution dans un `public` type doit avoir un `public` type de retour.

### <a name="class-property-and-indexer-override"></a>Substitution de la propriété de classe et de l’indexeur

[Contrainte existante sur les propriétés de substitution de classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors)

> Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité et nom que la propriété héritée, et il doit y avoir une conversion d’identité ~~entre le type de la substitution et la propriété héritée~~. Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur. Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs.

est modifié en

> Une déclaration de propriété de substitution doit spécifier exactement les mêmes modificateurs d’accessibilité et nom que la propriété héritée, et il doit y avoir une conversion d’identité **ou (si la propriété héritée est en lecture seule) une conversion de référence implicite du type de la propriété de substitution vers le type de la propriété héritée**. Si la propriété héritée n’a qu’un seul accesseur (autrement dit, si la propriété héritée est en lecture seule ou en écriture seule), la propriété de substitution doit inclure uniquement cet accesseur. Si la propriété héritée inclut des accesseurs (c’est-à-dire que si la propriété héritée est en lecture-écriture), la propriété de substitution peut inclure un seul accesseur ou les deux accesseurs. **Le type de la propriété de substitution doit être au moins aussi accessible que la propriété de substitution ([domaines d’accessibilité](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**

***Le reste de la spécification préliminaire ci-dessous propose une extension supplémentaire des retours covariants de méthodes d’interface à prendre en compte ultérieurement.***

### <a name="interface-method-property-and-indexer-override"></a>Substitution de la méthode d’interface, de la propriété et de l’indexeur

En ajoutant aux genres de membres autorisés dans une interface avec l’ajout de la fonctionnalité DIM en C# 8,0, nous ajoutons davantage de prise en `override` charge des membres avec les retours covariants.  Celles-ci suivent les `override` règles des membres telles qu’elles sont spécifiées pour les classes, avec les différences suivantes :

Le texte suivant dans les classes :

> La méthode substituée par une déclaration override est connue sous le nom de ***méthode de base substituée***. `M` Pour une méthode de substitution déclarée dans une `C`classe, la méthode de base substituée est déterminée en examinant chaque `C`classe de base de, en commençant par `C` la classe de base directe de et en continuant avec chaque classe de base directe, jusqu’à ce que dans un type de classe de base donné `M` , au moins une méthode accessible ait la même signature que après la substitution des arguments de type.

reçoit la spécification correspondante pour les interfaces :

> La méthode substituée par une déclaration override est connue sous le nom de ***méthode de base substituée***. Pour `M` une méthode de substitution déclarée dans une `I`interface, la méthode de base substituée est déterminée en examinant chaque interface de base `I`directe ou indirecte de, en collectant le jeu d’interfaces déclarant une méthode `M` accessible qui a la même signature qu’après la substitution des arguments de type.  Si cet ensemble d’interfaces a un *type le plus dérivé*, sur lequel il existe une conversion d’identité ou de référence implicite à partir de chaque type de cet ensemble, et que ce type contient une déclaration de méthode unique, il s’agit de la *méthode de base substituée*.

Nous autorisons `override` de manière similaire les propriétés et les indexeurs dans les interfaces comme spécifié pour les classes dans les *accesseurs 15.7.6 Virtual, sealed, override et abstract*.

### <a name="name-lookup"></a>Recherche de nom

La recherche de nom en présence de `override` déclarations de classe modifie actuellement le résultat de la recherche de nom en imposant sur les détails de membre `override` trouvés à partir de la déclaration la plus dérivée dans la hiérarchie de classes à partir du `this` type du qualificateur de l’identificateur (ou en l’absence de qualificateur).  Par exemple, dans *12.6.2.2 paramètres correspondants* , nous avons

> Pour les méthodes et les indexeurs virtuels définis dans les classes, la liste de paramètres est choisie à partir de la première déclaration ou substitution du membre de fonction trouvé lors du démarrage avec le type statique du récepteur, et la recherche dans ses classes de base.

pour cela, nous ajoutons

> Pour les méthodes et les indexeurs virtuels définis dans les interfaces, la liste de paramètres est prélevée à partir de la déclaration ou de la substitution du membre de fonction trouvé dans le type le plus dérivé parmi les types contenant la déclaration de substitution de la fonction membre.  Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.

Pour le type de résultat d’un accès à une propriété ou un indexeur, le texte existant

> - Si I identifie une propriété d’instance, le résultat est un accès à une propriété avec une expression d’instance associée de E et un type associé qui est le type de la propriété. Si T est un type de classe, le type associé est choisi dans la première déclaration ou la substitution de la propriété trouvée lors du démarrage de T, et la recherche dans ses classes de base.

est augmenté avec

> Si T est un type interface, le type associé est choisi dans la déclaration ou la substitution de la propriété trouvée dans le le plus dérivé de T ou ses interfaces de base directes ou indirectes.  Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.

Une modification similaire doit être apportée à l’accès à l' *indexeur 12.7.7.3*

Dans les *expressions d’appel 12.7.6* , nous enrichissons le texte existant

> - Sinon, le résultat est une valeur, avec un type associé du type de retour de la méthode ou du délégué. Si l’appel est d’une méthode d’instance et que le récepteur est d’un type de classe T, le type associé est choisi dans la première déclaration ou la substitution de la méthode trouvée lors du démarrage avec T et la recherche dans ses classes de base.

par

> Si l’appel est d’une méthode d’instance et que le récepteur est d’un type d’interface T, le type associé est choisi dans la déclaration ou la substitution de la méthode trouvée dans l’interface la plus dérivée parmi T et ses interfaces de base directes et indirectes.  Il s’agit d’une erreur au moment de la compilation si aucun type unique n’existe.

### <a name="implicit-interface-implementations"></a>Implémentations d’interface implicites

Cette section de la spécification

> Dans le cadre du mappage d’interface, un `A` membre de classe correspond `B` à un membre d’interface lorsque :
> 
> - `A`et `B` sont des méthodes, et le nom, le type et les listes de `A` paramètres `B` formels de et sont identiques.
> - `A`et `B` sont des propriétés, le nom et le `A` type `B` de et sont identiques `A` et ont les mêmes accesseurs `B` que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface explicite).
> - `A`et `B` sont des événements, et le nom et le `A` type `B` de et sont identiques.
> - `A`et `B` sont des indexeurs, les listes de paramètres de type `A` et `B` formels de et `A` sont identiques, et ont les `B` mêmes`A` accesseurs que (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface explicite).

est modifié comme suit :

> Dans le cadre du mappage d’interface, un `A` membre de classe correspond `B` à un membre d’interface lorsque :
> 
> - `A`et `B` sont des méthodes, et les listes de noms et de `A` paramètres `B` formels de et sont identiques, et `A` le type de retour de est convertible `B` en type de retour via une identité de conversion de référence implicite `B`vers le type de retour de.
> - `A`et `B` sont des propriétés, le nom `A` de `B` et sont identiques `A` , a les mêmes accesseurs `B` que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre d’interface `A` explicite) et le type de est `B` convertible en type de retour via une `A` conversion d’identité ou, si est une propriété ReadOnly, une conversion de référence implicite.
> - `A`et `B` sont des événements, et le nom et le `A` type `B` de et sont identiques.
> - `A`et `B` sont des indexeurs, les listes de paramètres `A` formels de `B` et `A` sont identiques, ont les mêmes `B` accesseurs que`A` (est autorisé à avoir des accesseurs supplémentaires s’il ne s’agit pas d’une implémentation de membre `A` d’interface explicite) et le type `B` de est convertible en type de retour `A` via une conversion d’identité ou, si est un indexeur ReadOnly, une conversion de référence implicite.

Il s’agit techniquement d’une modification avec rupture, car le programme ci-dessous affiche « C1 ». M « aujourd’hui, mais cela imprimerait «C2 ». M» sous la révision proposée.

``` c#
using System;

interface I1 { object M(); }
class C1 : I1 { public object M() { return "C1.M"; } }
class C2 : C1, I1 { public new string M() { return "C2.M"; } }
class Program
{
    static void Main()
    {
        I1 i = new C2();
        Console.WriteLine(i.M());
    }
}
```

En raison de cette modification avec rupture, nous pouvons envisager de ne pas prendre en charge les types de retour covariants sur les implémentations implicites.

### <a name="constraints-on-interface-implementation"></a>Contraintes sur l’implémentation de l’interface

**Nous aurons besoin d’une règle qui indique qu’une implémentation d’interface explicite doit déclarer un type de retour non moins dérivé que le type de retour déclaré dans une substitution de ses interfaces de base.**

### <a name="api-compatibility-implications"></a>Implications en matière de compatibilité des API

*TBD*

### <a name="open-issues"></a>Open Issues

La spécification n’indique pas comment l’appelant obtient le type de retour le plus affiné. Vraisemblablement, cela se fait de manière similaire à la façon dont les appelants obtiennent les spécifications de paramètre de substitution les plus dérivées.

--------------

Si nous disposons des interfaces suivantes :

```csharp
interface I1 { I1 M(); }
interface I2 { I2 M(); }
interface I3: I1, I2 { override I3 M(); }
```

Notez que dans `I3`, les méthodes `I1.M()` et `I2.M()` ont été fusionnées.  Lors de `I3`l’implémentation de, il est nécessaire de les implémenter ensemble.

En général, nous avons besoin d’une implémentation explicite pour faire référence à la méthode d’origine.  La question est, dans une classe

```csharp
class C : I1, I2, I3
{
    C IN.M();
}
```

Qu’est-ce que cela signifie ?  Que dois *-* je faire ?

Je vous suggère de permettre l' `I1.M` implémentation de `I2.M` ou (mais pas les deux), et de le considérer comme une implémentation des deux.

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

- une discussion sur <https://github.com/dotnet/roslyn/issues/357>.
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-01-08.md
- Discussion hors connexion vers une décision pour prendre en charge la substitution des méthodes de classe uniquement en C# 9,0.

