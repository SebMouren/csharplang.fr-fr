---
ms.openlocfilehash: b0d0fa70d90f7493c6c23be576155a77cec36cf8
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485181"
---
# <a name="default-interface-methods"></a>Méthodes d’interface par défaut

* [x] proposé
* [] Prototype : [en cours](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)
* [] Implémentation : aucune
* [] Spécification : en cours, en dessous

## <a name="summary"></a>Résumé
[summary]: #summary

Ajoutez la prise en charge des _méthodes d’extension virtuelles_ (méthodes) dans les interfaces avec des implémentations concrètes. Une classe ou un struct qui implémente une telle interface doit avoir une implémentation unique _la plus spécifique_ pour la méthode d’interface, implémentée par la classe ou le struct, ou héritée de ses classes de base ou interfaces. Les méthodes d’extension virtuelles permettent à un auteur d’API d’ajouter des méthodes à une interface dans les futures versions sans altérer la compatibilité source ou binaire avec les implémentations existantes de cette interface.

Celles-ci sont similaires aux [« méthodes par défaut](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)» de Java.

(En fonction de la technique d’implémentation probable) cette fonctionnalité requiert la prise en charge correspondante dans l’interface CLI/CLR. Les programmes qui tirent parti de cette fonctionnalité ne peuvent pas s’exécuter sur des versions antérieures de la plateforme.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Les motivations principales de cette fonctionnalité sont les

- Les méthodes d’interface par défaut permettent à un auteur d’API d’ajouter des méthodes à une interface dans les versions futures sans altérer la compatibilité source ou binaire avec les implémentations existantes de cette interface.
- Cette fonctionnalité permet C# à d’interagir avec les API ciblant [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) et [iOS (SWIFT)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), qui prennent en charge des fonctionnalités similaires.
- Il s’avère que l’ajout d’implémentations d’interface par défaut fournit les éléments de la fonctionnalité de langage « traits » (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>). Les caractéristiques ont prouvé qu’il s’agit d’une technique de programmation puissante (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

La syntaxe d’une interface est étendue pour permettre

- déclarations de membre qui déclarent des constantes, des opérateurs, des constructeurs statiques et des types imbriqués ;
- *corps* d’une méthode ou d’un indexeur, d’une propriété ou d’un accesseur d’événement (autrement dit, une implémentation « par défaut »);
- déclarations de membre qui déclarent des champs, des méthodes, des propriétés, des indexeurs et des événements statiques ;
- déclarations de membre à l’aide de la syntaxe d’implémentation d’interface explicite ; les
- Modificateurs d’accès explicites (l’accès par défaut est `public`).

Les membres avec des corps permettent à l’interface de fournir une implémentation « par défaut » pour la méthode dans les classes et les structs qui ne fournissent pas d’implémentation de substitution.

Les interfaces ne peuvent pas contenir l’état de l’instance. Alors que les champs statiques sont désormais autorisés, les champs d’instance ne sont pas autorisés dans les interfaces. Les propriétés auto des instances ne sont pas prises en charge dans les interfaces, car elles déclarent implicitement un champ masqué.

Les méthodes statiques et privées autorisent la refactorisation et l’organisation de code utiles utilisées pour implémenter l’API publique de l’interface.

Une substitution de méthode dans une interface doit utiliser la syntaxe d’implémentation d’interface explicite.

La déclaration d’un type de classe, d’un type struct ou d’un type enum dans la portée d’un paramètre de type qui a été déclaré avec un *variance_annotation*est une erreur.  Par exemple, la déclaration de `C` ci-dessous est une erreur.

```csharp
interface IOuter<out T>
{
    class C { } // error: class declaration within the scope of variant type parameter 'T'
}
```

### <a name="concrete-methods-in-interfaces"></a>Méthodes concrètes dans les interfaces

La forme la plus simple de cette fonctionnalité est la possibilité de déclarer une *méthode concrète* dans une interface, qui est une méthode avec un corps.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
```

Une classe qui implémente cette interface n’A pas besoin d’implémenter sa méthode concrète.

```csharp
class C : IA { } // OK

IA i = new C();
i.M(); // prints "IA.M"
```

La substitution finale pour `IA.M` dans la classe `C` est la méthode concrète `M` déclarée dans `IA`. Notez qu’une classe n’hérite pas des membres de ses interfaces ; Cela n’est pas modifié par cette fonctionnalité :

```csharp
new C().M(); // error: class 'C' does not contain a member 'M'
```

Dans un membre d’instance d’une interface, `this` a le type de l’interface englobante.

### <a name="modifiers-in-interfaces"></a>Modificateurs dans les interfaces

La syntaxe d’une interface est assouplie pour autoriser les modificateurs sur ses membres. Les éléments suivants sont autorisés : `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`et `partial`.

> ***TODO***: Vérifiez les autres modificateurs existants.

Un membre d’interface dont la déclaration comprend un corps est un membre `virtual`, sauf si le modificateur `sealed` ou `private` est utilisé. Le modificateur de `virtual` peut être utilisé sur un membre de fonction qui, autrement, serait implicitement `virtual`. De même, bien que `abstract` soit la valeur par défaut sur les membres d’interface sans corps, ce modificateur peut être fourni explicitement. Un membre non virtuel peut être déclaré à l’aide du mot clé `sealed`.

Un `private` ou `sealed` fonction membre d’une interface n’a pas de corps. Un membre de fonction `private` ne peut pas avoir le modificateur `sealed`.

Les modificateurs d’accès peuvent être utilisés sur les membres d’interface de tous les types de membres qui sont autorisés. Le niveau d’accès `public` est la valeur par défaut, mais il peut être fourni explicitement.

> ***Problème d’ouverture :*** Nous devons spécifier la signification précise des modificateurs d’accès, tels que `protected` et `internal`, et les déclarations qui ne se substituent pas à elles (dans une interface dérivée) ou qui les implémentent (dans une classe qui implémente l’interface).

Les interfaces peuvent déclarer des membres `static`, y compris les types imbriqués, les méthodes, les indexeurs, les propriétés, les événements et les constructeurs statiques. Le niveau d’accès par défaut pour tous les membres d’interface est `public`.

Les interfaces ne peuvent pas déclarer des constructeurs, des destructeurs ou des champs d’instance.

> ***Problème fermé :*** Les déclarations d’opérateur doivent-elles être autorisées dans une interface ? Les opérateurs de conversion ne sont probablement pas, mais qu’en est-il des autres ? ***Décision***: les opérateurs sont autorisés, *à l’exception* des opérateurs de conversion, d’égalité et d’inégalité.

> ***Problème fermé :*** Faut-`new` être autorisé sur les déclarations de membres d’interface qui masquent les membres des interfaces de base ? ***Décision***: Oui.

> ***Problème fermé :*** Actuellement, nous n’autorisons pas les `partial` sur une interface ou ses membres. Cela nécessiterait une proposition distincte. ***Décision***: Oui. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>

### <a name="overrides-in-interfaces"></a>Substitutions dans les interfaces

Les déclarations override (c’est-à-dire celles contenant le modificateur `override`) permettent au programmeur de fournir une implémentation la plus spécifique d’un membre virtuel dans une interface où le compilateur ou le runtime n’en trouvera pas un. Il permet également de transformer un membre abstrait d’une super interface en membre par défaut dans une interface dérivée. Une déclaration override est autorisée à substituer *explicitement* une méthode d’interface de base particulière en qualifiant la déclaration avec le nom de l’interface (aucun modificateur d’accès n’est autorisé dans ce cas). Les substitutions implicites ne sont pas autorisées.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); } // explicitly named
}
interface IC : IA
{
    override void M() { WriteLine("IC.M"); } // implicitly named
}
```

Les déclarations override dans les interfaces ne peuvent pas être déclarées `sealed`.

Les membres de fonction `virtual` publics dans une interface peuvent être substitués explicitement dans une interface dérivée (en qualifiant le nom dans la déclaration override avec le type d’interface qui a initialement déclaré la méthode et en omettant un modificateur d’accès).

`virtual` membres de fonction dans une interface ne peuvent être substitués explicitement (pas implicitement) dans les interfaces dérivées, et les membres qui ne sont pas `public` peuvent être implémentés explicitement dans une classe ou un struct explicitement (pas implicitement). Dans les deux cas, le membre substitué ou implémenté doit être *accessible* là où il est substitué.

### <a name="reabstraction"></a>Réabstraction

Une méthode virtuelle (concrète) déclarée dans une interface peut être substituée pour être abstraite dans une interface dérivée

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    abstract void IA.M();
}
class C : IB { } // error: class 'C' does not implement 'IA.M'.
```

Le modificateur `abstract` n’est pas requis dans la déclaration de `IB.M` (il s’agit de la valeur par défaut dans les interfaces), mais il est probablement conseillé d’être explicite dans une déclaration override.

Cela est utile dans les interfaces dérivées où l’implémentation par défaut d’une méthode est inappropriée et une implémentation plus appropriée doit être fournie par l’implémentation de classes.

> ***Problème d’ouverture :*** La réabstraction doit-elle être autorisée ?

### <a name="the-most-specific-override-rule"></a>Règle de remplacement la plus spécifique

Nous avons besoin que chaque interface et classe aient une *substitution la plus spécifique* pour chaque membre virtuel parmi les substitutions apparaissant dans le type ou ses interfaces directes et indirectes. La *substitution la plus spécifique* est un remplacement unique qui est plus spécifique que tous les autres remplacements. S’il n’y a pas de substitution, le membre lui-même est considéré comme la substitution la plus spécifique.

Une `M1` de remplacement est considérée comme *plus spécifique* qu’une autre substitution `M2` si `M1` est déclarée sur le type `T1`, `M2` est déclarée sur le type `T2`et soit

1. `T1` contient des `T2` parmi ses interfaces directes ou indirectes, ou
2. `T2` est un type d’interface, mais `T1` n’est pas un type d’interface.

Par exemple :

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    void IA.M() { WriteLine("IC.M"); }
}
interface ID : IB, IC { } // error: no most specific override for 'IA.M'
abstract class C : IB, IC { } // error: no most specific override for 'IA.M'
abstract class D : IA, IB, IC // ok
{
    public abstract void M();
}

```

La règle de remplacement la plus spécifique garantit qu’un conflit (c.-à-d. une ambiguïté résultant de l’héritage de diamant) est résolu explicitement par le programmeur au point où le conflit se produit.

Étant donné que nous prenons en charge les substitutions abstraites explicites dans les interfaces, nous pourrions le faire également dans les classes

```csharp
abstract class E : IA, IB, IC // ok
{
    abstract void IA.M();
}
```

> ***Problème d’ouverture***: devons-nous prendre en charge les remplacements abstraits d’interface explicites dans les classes ?

En outre, il s’agit d’une erreur si dans une déclaration de classe la substitution la plus spécifique d’une méthode d’interface est une substitution abstraite qui a été déclarée dans une interface. Il s’agit d’une règle existante retraitée à l’aide de la nouvelle terminologie.

```csharp
interface IF
{
    void M();
}
abstract class F : IF { } // error: 'F' does not implement 'IF.M'
```

Il est possible pour une propriété virtuelle déclarée dans une interface d’avoir une substitution la plus spécifique pour son accesseur `get` dans une interface et une substitution la plus spécifique pour son accesseur `set` dans une interface différente. Cela est considéré comme une violation de la règle de *remplacement la plus spécifique* .

### <a name="static-and-private-methods"></a>Méthodes `static` et `private`

Étant donné que les interfaces peuvent désormais contenir du code exécutable, il est utile d’extraire le code commun en méthodes privées et statiques. Nous les autorisons maintenant dans les interfaces.

> ***Problème fermé***: devons-nous prendre en charge les méthodes privées ? Devons-nous prendre en charge les méthodes statiques ? **Décision : Oui**

> ***Problème ouvert***: les méthodes d’interface doivent-elles être `protected` ou `internal` ou un autre accès ? Dans ce cas, quelle est la sémantique ? Sont-ils `virtual` par défaut ? Dans ce cas, existe-t-il un moyen de les rendre non virtuels ?

> ***Problème d’ouverture***: si nous prenons en charge les méthodes statiques, devons-nous prendre en charge les opérateurs (statiques) ?

### <a name="base-interface-invocations"></a>Appels de l’interface de base

Le code d’un type qui dérive d’une interface avec une méthode par défaut peut appeler explicitement l’implémentation de « base » de cette interface.

```csharp
interface I0
{
   void M() { Console.WriteLine("I0"); }
}
interface I1 : I0
{
   override void M() { Console.WriteLine("I1"); }
}
interface I2 : I0
{
   override void M() { Console.WriteLine("I2"); }
}
interface I3 : I1, I2
{
   // an explicit override that invoke's a base interface's default method
   void I0.M() { I2.base.M(); }
}

```

Une méthode d’instance (non statique) est autorisée à appeler l’implémentation d’une méthode d’instance accessible dans une interface de base directe de manière non virtuelle en la nommant à l’aide de la syntaxe `base(Type).M`. Cela est utile quand une substitution qui doit être fournie en raison de l’héritage Diamond est résolue par la délégation à une implémentation de base particulière.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    override void IA.M() { WriteLine("IC.M"); }
}

class D : IA, IB, IC
{
    void IA.M() { base(IB).M(); }
}
```

Lorsqu’un membre `virtual` ou `abstract` est accessible à l’aide de la syntaxe `base(Type).M`, `Type` contient une substitution unique *la plus spécifique* pour `M`.

### <a name="binding-base-clauses"></a>Liaison de clauses de base

Les interfaces contiennent désormais des types.  Ces types peuvent être utilisés dans la clause de base comme interfaces de base.  Lors de la liaison d’une clause de base, nous pouvons être amenés à connaître l’ensemble des interfaces de base pour lier ces types (par exemple, pour effectuer une recherche dans ceux-ci et pour résoudre l’accès protégé).  La signification de la clause de base d’une interface est donc définie de manière circulaire.  Pour rompre le cycle, nous ajoutons une nouvelle règle de langage correspondant à une règle similaire déjà en place pour les classes.

Tout en déterminant la signification de la *interface_base* d’une interface, les interfaces de base sont temporairement supposées être vides. Intuitivement, cela garantit que la signification d’une clause de base ne peut pas dépendre de lui-même de manière récursive. 

**Nous avons utilisé les règles suivantes :**

«Quand une classe B dérive d’une classe A, il s’agit d’une erreur de compilation qui dépend de B. Une classe **dépend directement de** sa classe de base directe (le cas échéant) et **dépend directement de** la ~~**classe**~~ dans laquelle elle est immédiatement imbriquée (le cas échéant). À partir de cette définition, l’ensemble complet des ~~**classes**~~ dont dépend une classe est la fermeture réflexive et transitive de la relation **directement dépend** de la relation.

Il s’agit d’une erreur au moment de la compilation pour qu’une interface hérite directement ou indirectement de lui-même.
Les **interfaces de base** d’une interface sont les interfaces de base explicites et leurs interfaces de base. En d’autres termes, l’ensemble des interfaces de base est la fermeture transitive complète des interfaces de base explicites, leurs interfaces de base explicites, et ainsi de suite.

**Nous les ajustons comme suit :**

Quand une classe B dérive d’une classe A, il s’agit d’une erreur au moment de la compilation pour qu’un dépende de B. Une classe **dépend directement de** sa classe de base directe (le cas échéant) et **dépend directement** du _**type**_ dans lequel elle est immédiatement imbriquée (le cas échéant).

Quand une interface IB étend une interface IA, il s’agit d’une erreur de compilation pour IA qui dépend de IB. Une interface **dépend directement de** ses interfaces de base directes (le cas échéant) et **dépend directement** du type dans lequel elle est immédiatement imbriquée (le cas échéant).

Étant donné ces définitions, le jeu complet de **types** dont dépend un type est la fermeture réflexive et transitive de la relation **directement dépend de** la relation.

### <a name="effect-on-existing-programs"></a>Effet sur les programmes existants

Les règles présentées ici sont destinées à n’avoir aucun effet sur la signification des programmes existants.

Exemple 1 :

```csharp
interface IA
{
    void M();
}
class C: IA // Error: IA.M has no concrete most specific override in C
{
    public static void M() { } // method unrelated to 'IA.M' because static
}
```

Exemple 2 :

```csharp
interface IA
{
    void M();
}
class Base: IA
{
    void IA.M() { }
}
class Derived: Base, IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

Les mêmes règles donnent des résultats similaires à la situation analogue impliquant des méthodes d’interface par défaut :

```csharp
interface IA
{
    void M() { }
}
class Derived: IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

> ***Problème fermé***: confirmez qu’il s’agit d’une conséquence prévue de la spécification. **Décision : Oui**

### <a name="runtime-method-resolution"></a>Résolution de méthode d’exécution

> ***Problème fermé :*** La spécification doit décrire l’algorithme de résolution de la méthode d’exécution en face des méthodes par défaut de l’interface. Nous devons nous assurer que la sémantique est cohérente avec la sémantique de langue, par exemple les méthodes déclarées qui font et ne substituent pas ou n’implémentent pas une méthode `internal`.

### <a name="clr-support-api"></a>API de prise en charge CLR

Pour que les compilateurs détectent lorsqu’ils sont en cours de compilation pour un Runtime qui prend en charge cette fonctionnalité, les bibliothèques de ces runtimes sont modifiées pour annoncer ce fait via l’API décrite dans <https://github.com/dotnet/corefx/issues/17116>. Nous ajoutons

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeFeature
    {
        // Presence of the field indicates runtime support
        public const string DefaultInterfaceImplementation = nameof(DefaultInterfaceImplementation);
    }
}
```

> ***Problème ouvert***: est-ce que le meilleur nom pour la fonctionnalité *CLR* ? La fonctionnalité CLR fait bien plus que cela (par exemple, assouplit les contraintes de protection, prend en charge les remplacements dans les interfaces, etc.). Par exemple, elle doit être appelée « méthodes concrètes dans les interfaces » ou « caractéristiques » ?

### <a name="further-areas-to-be-specified"></a>Autres zones à spécifier

- [] Il serait utile de cataloguer les genres d’effets de compatibilité source et binaire dus à l’ajout de méthodes d’interface par défaut et de remplacements à des interfaces existantes.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Cette proposition requiert une mise à jour coordonnée de la spécification du CLR (pour prendre en charge les méthodes concrètes dans les interfaces et la résolution de méthode). Par conséquent, il est relativement « coûteux » et il peut être intéressant d’utiliser d’autres fonctionnalités que nous prévoyons d’exiger également des modifications du CLR.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Aucun.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- Les questions ouvertes sont dénommées tout au long de la proposition, ci-dessus.
- Voir aussi <https://github.com/dotnet/csharplang/issues/406> pour obtenir la liste des questions ouvertes.
- La spécification détaillée doit décrire le mécanisme de résolution utilisé lors de l’exécution pour sélectionner la méthode précise à appeler.
- L’interaction des métadonnées produites par les nouveaux compilateurs et consommées par les compilateurs plus anciens doit être traitée en détail. Par exemple, nous devons nous assurer que la représentation de métadonnées que nous utilisons n’entraîne pas l’ajout d’une implémentation par défaut dans une interface pour arrêter une classe existante qui implémente cette interface lorsqu’elle est compilée par un compilateur plus ancien. Cela peut affecter la représentation des métadonnées que nous pouvons utiliser.
- La conception doit prendre en compte l’interopérabilité avec d’autres langages et des compilateurs existants pour d’autres langages.

## <a name="resolved-questions"></a>Questions résolues

### <a name="abstract-override"></a>Remplacement abstrait

La spécification préliminaire précédente contenait la possibilité de « reabstract » une méthode héritée :

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { }
}
interface IC : IB
{
    override void M(); // make it abstract again
}
```

Mes notes pour 2017-03-20 ont montré que nous avons décidé de ne pas les autoriser. Toutefois, il existe au moins deux cas d’utilisation :

1. Les API Java, avec lesquelles certains utilisateurs de cette fonctionnalité espèrent interagir, dépendent de cette fonctionnalité.
2. La programmation avec les *caractéristiques* tire parti de cette. La réabstraction est l’un des éléments de la fonctionnalité de langage « traits » (https://en.wikipedia.org/wiki/Trait_(computer_programming)). Les éléments suivants sont autorisés avec les classes :

```csharp
public abstract class Base
{
    public abstract void M();
}
public abstract class A : Base
{
    public override void M() { }
}
public abstract class B : A
{
    public override abstract void M(); // reabstract Base.M
}
```

Malheureusement, ce code ne peut pas être refactorisé comme un ensemble d’interfaces (traits) à moins que cela ne soit autorisé. Le *principe Jared de cupidité*doit être autorisé.

> ***Problème fermé :*** La réabstraction doit-elle être autorisée ? Oui Mes notes sont incorrectes. Les [Notes LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) indiquent que la réabstraction est autorisée dans une interface. Pas dans une classe.

### <a name="virtual-modifier-vs-sealed-modifier"></a>Modificateur virtuel et modificateur sealed

À partir de [Aleksey Tsingauz](https://github.com/AlekseyTs):

> Nous avons décidé d’autoriser les modificateurs explicitement indiqués sur les membres d’interface, à moins qu’il y ait une raison d’en interdire certains. Cela pose une question intéressante concernant le modificateur virtuel. Doit-il être requis sur les membres avec l’implémentation par défaut ?
>
> Nous pouvons affirmer que :
>
> - s’il n’existe aucune implémentation et que ni Virtual, ni sealed ne sont spécifiés, nous supposons que le membre est abstrait.
> - s’il existe une implémentation et que ni abstract, ni sealed ne sont spécifiés, nous supposons que le membre est virtuel.
> - un modificateur sealed est requis pour rendre une méthode non virtuelle ou abstraite.
>
> Vous pouvez également indiquer que le modificateur virtuel est requis pour un membre virtuel. Par exemple, s’il existe un membre dont l’implémentation n’est pas explicitement marquée avec le modificateur Virtual, il n’est ni virtuel ni abstrait. Cette approche peut offrir une meilleure expérience quand une méthode est déplacée d’une classe vers une interface :
>
> - une méthode abstraite reste abstraite.
> - une méthode virtuelle reste virtuelle.
> - une méthode sans modificateur ne reste ni virtuelle, ni abstraite.
> - un modificateur sealed ne peut pas être appliqué à une méthode qui n’est pas une substitution.
>
> Qu'en penses-tu?

> ***Problème fermé :*** Une méthode concrète (avec implémentation) doit-elle être implicitement `virtual`? Oui

***Décisions :*** Créé dans le LDM 2017-04-05 :

1. les non virtuelles doivent être exprimées explicitement par `sealed` ou `private`.
2. `sealed` est le mot clé pour rendre les membres d’instance d’interface avec des corps non virtuels
3. Nous souhaitons autoriser tous les modificateurs dans les interfaces  
4. L’accessibilité par défaut pour les membres d’interface est publique, y compris les types imbriqués
5. les fonctions membres privées dans les interfaces sont implicitement sealed et `sealed` ne sont pas autorisées sur elles.
6. Les classes privées (dans les interfaces) sont autorisées et peuvent être sealed, ce qui signifie qu’elles sont scellées dans le sens de la classe de Sealed.
7. L’absence d’une bonne proposition, partielle n’est toujours pas autorisée sur les interfaces ou leurs membres.

### <a name="binary-compatibility-1"></a>Compatibilité binaire 1

Quand une bibliothèque fournit une implémentation par défaut

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
}
class C : I2
{
}
```

Nous savons que l’implémentation de `I1.M` dans `C` est `I1.M`. Que se passe-vous si l’assembly contenant `I2` est modifié comme suit et recompilé

```csharp
interface I2 : I1
{
    override void M() { Impl2 }
}
```

mais `C` n’est pas recompilée. Que se passe-t-il lorsque le programme est exécuté ? Un appel de `(C as I1).M()`

1. S’exécute `I1.M`
2. S’exécute `I2.M`
3. Lève un certain type d’erreur d’exécution

***Décision :*** Rendu 2017-04-11 : s’exécute `I2.M`, ce qui correspond à la substitution la plus spécifique non ambiguë au moment de l’exécution.

### <a name="event-accessors-closed"></a>Accesseurs d’événement (fermé)

> ***Problème fermé :*** Un événement peut-il être remplacé par « par morceaux » ?

Prenons le cas suivant :

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        // error: "remove" accessor missing
    }
}
```

Cette implémentation « partielle » de l’événement n’est pas autorisée, car, comme dans une classe, la syntaxe d’une déclaration d’événement n’autorise qu’un seul accesseur ; les deux (ou aucune) doivent être fournis. Vous pourriez accomplir la même chose en autorisant l’accesseur remove abstrait dans la syntaxe à être implicitement abstrait par l’absence d’un corps :

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        remove; // implicitly abstract
    }
}
```

Notez qu' *il s’agit d’une nouvelle syntaxe (proposée)* . Dans la grammaire actuelle, les accesseurs d’événement ont un corps obligatoire.

> ***Problème fermé :*** Un accesseur d’événement peut-il être (implicitement) abstrait par l’omission d’un corps, de la même façon que les méthodes des accesseurs d’interfaces et de propriétés sont abstraites (implicitement) par l’omission d’un corps ?

***Décision :*** (2017-04-18) non, les déclarations d’événements requièrent des accesseurs concrets (ou aucun).

### <a name="reabstraction-in-a-class-closed"></a>Réabstraction dans une classe (fermée)

***Problème fermé :*** Nous devons confirmer que cela est autorisé (sinon, l’ajout d’une implémentation par défaut serait une modification avec rupture) :

```csharp
interface I1
{
    void M() { }
}
abstract class C : I1
{
    public abstract void M(); // implement I1.M with an abstract method in C
}
```

***Décision :*** (2017-04-18) Oui, l’ajout d’un corps à une déclaration de membre d’interface ne doit pas rompre la valeur C.

### <a name="sealed-override-closed"></a>Remplacement sealed (fermé)

La question précédente suppose implicitement que le modificateur `sealed` peut être appliqué à un `override` dans une interface. Cela contredit la spécification brouillon. Voulez-vous autoriser le scellement d’un remplacement ? Les effets de la compatibilité source et binaire de la fermeture doivent être pris en compte.

> ***Problème fermé :*** Faut-il autoriser le scellement d’un remplacement ?

***Décision :*** (2017-04-18) n’est pas autorisé `sealed` sur les remplacements dans les interfaces. La seule utilisation de `sealed` sur les membres d’interface consiste à les rendre non virtuelles dans leur déclaration initiale.

### <a name="diamond-inheritance-and-classes-closed"></a>Héritage et classes Diamond (fermé)

Le brouillon de la proposition préfère les remplacements de classe aux remplacements d’interface dans les scénarios d’héritage Diamond :

> Nous avons besoin que chaque interface et classe aient une *substitution la plus spécifique* pour chaque méthode d’interface parmi les substitutions apparaissant dans le type ou ses interfaces directes et indirectes. La *substitution la plus spécifique* est un remplacement unique qui est plus spécifique que tous les autres remplacements. S’il n’y a pas de substitution, la méthode elle-même est considérée comme la substitution la plus spécifique.
>
> Une `M1` de remplacement est considérée comme *plus spécifique* qu’une autre substitution `M2` si `M1` est déclarée sur le type `T1`, `M2` est déclarée sur le type `T2`et soit
>
> 1. `T1` contient des `T2` parmi ses interfaces directes ou indirectes, ou
> 2. `T2` est un type d’interface, mais `T1` n’est pas un type d’interface.

Le scénario est le

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { WriteLine("IB"); }
}
class Base : IA
{
    void IA.M() { WriteLine("Base"); }
}
class Derived : Base, IB // allowed?
{
    static void Main()
    {
        Ia a = new Derived();
        a.M();           // what does it do?
    }
}
```

Nous devons confirmer ce comportement (ou décider autrement)

> ***Problème fermé :*** Confirmez la spécification préliminaire, ci-dessus, pour *la substitution la plus spécifique* , telle qu’elle s’applique aux classes et interfaces mixtes (une classe est prioritaire sur une interface). Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.

### <a name="interface-methods-vs-structs-closed"></a>Méthodes d’interface et structs (fermé)

Il existe des interactions fâcheuses entre les méthodes et structs de l’interface par défaut.

```csharp
interface IA
{
    public void M() { }
}
struct S : IA
{
}
```

Notez que les membres d’interface ne sont pas hérités :

```csharp
var s = default(S);
s.M(); // error: 'S' does not contain a member 'M'
```

Par conséquent, le client doit Box le struct pour appeler des méthodes d’interface

```csharp
IA s = default(S); // an S, boxed
s.M(); // ok
```

De cette façon, le boxing est à l’encontre des principaux avantages d’un type de `struct`. En outre, toutes les méthodes de mutation n’auront aucun effet apparent, car elles fonctionnent sur une *copie boxed* du struct :

```csharp
interface IB
{
    public void Increment() { P += 1; }
    public int P { get; set; }
}
struct T : IB
{
    public int P { get; set; } // auto-property
}

T t = default(T);
Console.WriteLine(t.P); // prints 0
(t as IB).Increment();
Console.WriteLine(t.P); // prints 0
```

> ***Problème fermé :*** Que pouvons-nous faire pour cela :
>
> 1. Interdire à un `struct` d’hériter d’une implémentation par défaut. Toutes les méthodes d’interface seraient traitées comme abstraites dans une `struct`. Ensuite, nous pouvons prendre du temps ultérieurement pour décider comment améliorer son fonctionnement.
> 2. Trouvez un type de stratégie de génération de code qui évite la conversion boxing. À l’intérieur d’une méthode comme `IB.Increment`, le type de `this` serait peut-être similaire à un paramètre de type restreint à `IB`. En conjonction avec cela, pour éviter le boxing dans l’appelant, les méthodes non abstraites sont héritées des interfaces. Cela peut augmenter considérablement le fonctionnement du compilateur et de l’implémentation du CLR.
> 3. Ne vous inquiétez pas et ne vous contentez pas de gênant.
> 4. Autres idées ?

***Décision :*** Ne vous inquiétez pas et ne vous contentez pas de gênant. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.

### <a name="base-interface-invocations-closed"></a>Appels de l’interface de base (fermé)

Le brouillon de spécifications suggère une syntaxe pour les appels de l’interface de base inspirée par Java : `Interface.base.M()`. Nous devons sélectionner une syntaxe, au moins pour le prototype initial. Mon favori est `base<Interface>.M()`.

> ***Problème fermé :*** Quelle est la syntaxe d’un appel de membre de base ?

***Décision :*** La syntaxe est `base(Interface).M()`. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>. L’interface ainsi nommée doit être une interface de base, mais elle n’a pas besoin d’être une interface de base directe.

> ***Problème d’ouverture :*** Les appels d’interface de base doivent-ils être autorisés dans les membres de classe ?

***Décision***: Oui. <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>

### <a name="overriding-non-public-interface-members-closed"></a>Substitution de membres d’interface non publics (fermés)

Dans une interface, les membres non publics des interfaces de base sont substitués à l’aide du modificateur `override`. S’il s’agit d’une substitution « explicite » qui nomme l’interface contenant le membre, le modificateur d’accès est omis.

> ***Problème fermé :*** S’il s’agit d’une substitution « implicite » qui ne nomme pas l’interface, le modificateur d’accès doit-il correspondre ?

***Décision :*** Seuls les membres publics peuvent être substitués implicitement, et l’accès doit correspondre. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.

> ***Problème d’ouverture :*** Le modificateur d’accès est-il requis, facultatif ou omis sur une substitution explicite, telle que `override void IB.M() {}`?

> ***Problème d’ouverture :*** La `override` est-elle obligatoire, facultative ou omise sur un remplacement explicite, tel que `void IB.M() {}`?

Comment l’un implémente-t-il un membre d’interface non public dans une classe ? Peut-être devez-vous le faire explicitement ?

```csharp
interface IA
{
    internal void MI();
    protected void MP();
}
class C : IA
{
    // are these implementations?
    internal void MI() {}
    protected void MP() {}
}
```

> ***Problème fermé :*** Comment l’un implémente-t-il un membre d’interface non public dans une classe ?

***Décision :*** Vous pouvez uniquement implémenter des membres d’interface non publics explicitement. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.

***Décision***: aucun mot clé `override` autorisé sur les membres d’interface. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>

### <a name="binary-compatibility-2-closed"></a>Compatibilité binaire 2 (fermée)

Considérez le code suivant dans lequel chaque type se trouve dans un assembly distinct.

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
    override void M() { Impl2 }
}
interface I3 : I1
{
}
class C : I2, I3
{
}
```

Nous savons que l’implémentation de `I1.M` dans `C` est `I2.M`. Que se passe-vous si l’assembly contenant `I3` est modifié comme suit et recompilé

```csharp
interface I3 : I1
{
    override void M() { Impl3 }
}
```

mais `C` n’est pas recompilée. Que se passe-t-il lorsque le programme est exécuté ? Un appel de `(C as I1).M()`

1. S’exécute `I1.M`
2. S’exécute `I2.M`
3. S’exécute `I3.M`
4. 2 ou 3, déterministe
5. Lève un type d’exception d’exécution

***Décision***: levez une exception (5). Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.

### <a name="permit-partial-in-interface-closed"></a>Autoriser `partial` dans l’interface ? légendes

Étant donné que les interfaces peuvent être utilisées de manière analogue à la façon dont les classes abstraites sont utilisées, il peut être utile de les déclarer `partial`. Cela serait particulièrement utile pour les générateurs.

> ***Proposition :*** Supprimez la restriction de langue pour laquelle les interfaces et les membres d’interfaces ne peuvent pas être déclarés `partial`.

***Décision***: Oui. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.

### <a name="main-in-an-interface-closed"></a>`Main` dans une interface ? légendes

> ***Problème d’ouverture :*** Une méthode `static Main` dans une interface peut-elle être le point d’entrée du programme ?

***Décision***: Oui. Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.

### <a name="confirm-intent-to-support-public-non-virtual-methods-closed"></a>Confirmer l’intention de prendre en charge les méthodes publiques non virtuelles (fermées)

Pouvons-nous confirmer (ou inverser) notre décision d’autoriser les méthodes publiques non virtuelles dans une interface ?

```csharp
interface IA
{
    public sealed void M() { }
}
```

> ***Problème semi-fermé :*** (2017-04-18) nous pensons qu’il va être utile, mais y revenir. Il s’agit d’un bloc de déclenchement de modèle mental.

***Décision***: Oui. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.

### <a name="does-an-override-in-an-interface-introduce-a-new-member-closed"></a>Un `override` dans une interface présente-t-il un nouveau membre ? légendes

Il existe plusieurs façons d’observer si une déclaration de remplacement introduit un nouveau membre ou non.

```csharp
interface IA
{
    void M(int x) { }
}
interface IB : IA
{
    override void M(int y) { }
}
interface IC : IB
{
    static void M2()
    {
        M(y: 3); // permitted?
    }
    override void IB.M(int z) { } // permitted? What does it override?
}
```

> ***Problème d’ouverture :*** Une déclaration override dans une interface présente-t-elle un nouveau membre ? légendes

Dans une classe, une méthode de substitution est « visible » dans certains sens. Par exemple, les noms de ses paramètres ont la priorité sur les noms des paramètres dans la méthode substituée. Il peut être possible de dupliquer ce comportement dans les interfaces, car il y a toujours une substitution la plus spécifique. Mais voulez-vous dupliquer ce comportement ?

Il est également possible de « remplacer » une méthode override ? Discutable

***Décision***: aucun mot clé `override` autorisé sur les membres d’interface. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.

### <a name="properties-with-a-private-accessor-closed"></a>Propriétés avec un accesseur private (fermé)

Nous disons que les membres privés ne sont pas virtuels et que la combinaison de Virtual et Private est interdite. Mais qu’en est-il d’une propriété avec un accesseur private ?

```csharp
interface IA
{
    public virtual int P
    {
        get => 3;
        private set => { }
    }
}
```

Est-ce autorisé ? L’accesseur `set` est-il `virtual` ou non ? Peut-il être substitué là où il est accessible ? Les éléments suivants implémentent-ils implicitement uniquement l’accesseur `get` ?

```csharp
class C : IA
{
    public int P
    {
        get => 4;
        set { }
    }
}
```

Est probablement une erreur due à l’IA. P. Set n’est pas virtuel et aussi parce qu’il n’est pas accessible ?

```csharp
class C : IA
{
    int IA.P
    {
        get => 4;
        set { }
    }
}
```

***Décision***: le premier exemple semble valide, tandis que le dernier ne le fait pas. Cela est résolu de façon similaire à la façon dont il C#fonctionne déjà dans. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#properties-with-a-private-accessor>

### <a name="base-interface-invocations-round-2-closed"></a>Appels de l’interface de base, arrondi 2 (fermé)

Notre « résolution » précédente à la manière de gérer les appels de base n’offre pas en fait suffisamment d’expressivité. Il s’avère que dans C# et le CLR, contrairement à Java, vous devez spécifier à la fois l’interface contenant la déclaration de méthode et l’emplacement de l’implémentation que vous souhaitez appeler.

Je propose la syntaxe suivante pour les appels de base dans les interfaces. Je n’aime pas, mais il illustre ce que toute syntaxe doit pouvoir exprimer :

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I4 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>(I1).M(); // calls I3's implementation of I1.M
        base<I4>(I1).M(); // calls I4's implementation of I1.M
    }
    void I2.M()
    {
        base<I3>(I2).M(); // calls I3's implementation of I2.M
        base<I4>(I2).M(); // calls I4's implementation of I2.M
    }
}
```

S’il n’y a pas d’ambiguïté, vous pouvez l’écrire plus simplement

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I4 : I1 { void I1.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>.M(); // calls I3's implementation of I1.M
        base<I4>.M(); // calls I4's implementation of I1.M
    }
}
```

ou

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base(I1).M(); // calls I3's implementation of I1.M
    }
    void I2.M()
    {
        base(I2).M(); // calls I3's implementation of I2.M
    }
}
```

ou

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base.M(); // calls I3's implementation of I1.M
    }
}
```

***Décision***: décidée sur `base(N.I1<T>).M(s)`, en concédant que si nous avons une liaison d’appel, il peut y avoir un problème plus tard. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md#default-interface-implementations>

### <a name="warning-for-struct-not-implementing-default-method-closed"></a>Avertissement indiquant que la structure n’implémente pas la méthode par défaut ? légendes

@vancem déclare que nous devrions sérieusement envisager de générer un avertissement si une déclaration de type de valeur ne peut pas substituer une méthode d’interface, même si elle hérite d’une implémentation de cette méthode à partir d’une interface. Dans la mesure où elle provoque des appels de conversion boxing et de sous-mines.

***Décision***: cela semble plus adapté à un analyseur. Il semble également que cet avertissement soit bruyant, car il se déclenche même si la méthode d’interface par défaut n’est jamais appelée et qu’il n’y aura jamais de boxing. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#warning-for-struct-not-implementing-default-method>

### <a name="interface-static-constructors-closed"></a>Constructeurs statiques d’interface (fermés)

Quand les constructeurs statiques d’interface s’exécutent-ils ?  La version actuelle de l’interface CLI suggère qu’elle se produit lors de l’accès à la première méthode ou au champ statique. S’il n’y en a pas, il n’est peut-être jamais exécuté ?

[2018-10-09 l’équipe CLR propose « va mettre en miroir ce que nous faisons pour les points de contrôle de l’accès à chaque méthode d’instance) »]

***Décision***: les constructeurs statiques sont également exécutés à l’entrée dans des méthodes d’instance, si le constructeur statique n’a pas été `beforefieldinit`, auquel cas les constructeurs statiques sont exécutés avant l’accès au premier champ statique. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#when-are-interface-static-constructors-run>

## <a name="design-meetings"></a>Réunions de conception

[2017-03-08 notes de réunion ldm](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md)
[2017-03-21 notes de réunion LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 Réunion "comportement CLR pour les méthodes d’interface par défaut"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[notes de réunion LDM 2017-04-05](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md) de réunion LDM
2017-04-18 notes de [réunion LDM
](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md) 2017-04-19 [notes de réunion](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md) du ldm
2017-05-17 [Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md) de réunion LDM
2017-05-31 [Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md) de réunion LDM
[2017-06-14 LDM Notes de réunion
](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md) [2018-10-17 notes de réunion ldm](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 Notes de réunion LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)
