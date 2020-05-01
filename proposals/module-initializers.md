---
ms.openlocfilehash: 7522bee6ac14d205aaf2a8491c13a321d2c18d62
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598580"
---
# <a name="module-initializers"></a>Initialiseurs de module

* [x] proposé
* [] Prototype : [en cours](https://github.com/jnm2/roslyn/tree/module_initializer)
* [] Implémentation : [en cours](https://github.com/dotnet/roslyn/tree/features/module-initializers)
* [] Spécification : [non démarrée]()

## <a name="summary"></a>Résumé
[summary]: #summary

Bien que la plateforme .NET dispose d’une fonctionnalité qui prend directement en charge l’écriture du code d’initialisation de l’assembly (techniquement, le module), elle n’est pas exposée en C#.  Il s’agit d’un scénario plutôt que des niches, mais une fois que vous y êtes dans, les solutions semblent assez pénibles.  Il existe des rapports d' [un certain nombre de clients](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (à l’intérieur et à l’extérieur de Microsoft) qui éprouvent le problème et il n’y a aucun doute sur les cas non documentés.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

- Permettez aux bibliothèques d’effectuer des initialisations opportunes et ponctuelles lorsqu’elles sont chargées, avec une charge minimale et sans que l’utilisateur ait besoin d’appeler explicitement quoi que ce soit
- Un point délicat particulier des approches `static` actuelles du constructeur est que le runtime doit effectuer des vérifications supplémentaires sur l’utilisation d’un type avec un constructeur statique, afin de déterminer si le constructeur statique doit être exécuté ou non. Cela ajoute une surcharge mesurable.
- Activer les générateurs de source pour exécuter une logique d’initialisation globale sans que l’utilisateur doive appeler explicitement quoi que ce soit

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Le compilateur C# reconnaît l’attribut suivant :

``` c#
namespace System.Runtime.CompilerServices
{
    // Note: an Obsolete attribute is not needed here,
    // because only C# 9 compilers will have access to attributes added in .NET 5.
    // LangVersion checks will still be necessary.
    [AttributeUsage(AttributeTargets.Module, AllowMultiple = false)]
    public class ModuleInitializerAttribute : Attribute
    {
        public ModuleInitializerAttribute(Type type) { }
    }
}
```

Vous pouvez l’utiliser comme suit

``` c#
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyModuleInitializer))]

internal static class MyModuleInitializer
{
    static MyModuleInitializer()
    {
        // put your module initializer here
    }
}
```

et le compilateur C# émettra alors un constructeur de module qui provoque le déclenchement du constructeur statique du type identifié :

``` c#
void .cctor()
{
    // synthesize and call a dummy method with an unspeakable name,
    // which will cause the runtime to call the static constructor
    MyModuleInitializer.<TriggerClassConstructor>();
}
```

Notez que la surcharge liée à la vérification de l’exécution du constructeur statique sur un type d’initialiseur de module (`MyModuleInitializer` dans cet exemple) peut être réduite au minimum en référençant le `[module: ModuleInitializer(typeof(MyModuleInitializer))]` type uniquement dans l’attribut.

Cette proposition utilise `AttributeUsage(AllowMultiple = false)` pour interdire à l’utilisateur de déclarer plusieurs classes d’initialiseurs sans ajouter de règles de langage spéciales pour y parvenir. Il est très léger en ce qu’il utilise la syntaxe et les règles sémantiques existantes pour les attributs. En revanche, il peut être plus difficile pour les utilisateurs de se rendre compte que le constructeur statique qu’ils examinent est l’initialiseur de module, car l’attribut peut être éloigné de la classe qui contient réellement l’initialiseur de module, et il est également nécessaire de décider ce qui se passe si l’attribut est utilisé pour référencer un type d’un autre assembly.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

#### <a name="should-we-permit-multiple-types-to-be-decorated-with-moduleinitializerattribute-in-a-compilation-if-so-in-what-order-should-the-static-constructors-be-invoked"></a>Devons-nous autoriser le décor de plusieurs types avec ModuleInitializerAttribute dans une compilation ? Dans ce cas, dans quel ordre les constructeurs statiques doivent-ils être appelés ?

Une option : effectuez la même opération pour les initialiseurs statiques dans les classes partielles. Ordonnez-les en fonction de l’ordre des fichiers et de la position source.

#### <a name="should-we-permit-using-a-type-from-another-assembly-as-the-module-initializer"></a>Faut-il autoriser l’utilisation d’un type d’un autre assembly comme initialiseur de module ?

destin`[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`

Cela peut compliquer l’appel du constructeur de classe en appelant simplement une méthode factice, car nous ne pouvons pas synthétiser une telle méthode sur le type. Nous pourrions envisager de revenir à `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` dans ce cas, mais il introduit une dépendance sur la pile de réflexion.

Ce cas d’usage implique que la classe d’initialiseur n’est pas l’initialiseur de module pour l’assembly dans lequel il est déclaré. Il semble que ce cas d’usage doit plutôt être géré en exposant une méthode que le consommateur externe doit appeler à partir de son initialiseur de module.

```cs
// Assembly 1
public class MyLibInit
{
    public static void Init() { }
}

// Assembly 2
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyInit))]

class MyInit
{
    static
    {
        MyLibInit.Init();
    }
}
```

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Pourquoi n’est-ce *pas* ?

- Les outils tiers existants pour « l’injection » d’initialiseurs de module sont peut-être suffisants pour les utilisateurs qui ont demandé cette fonctionnalité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Quelles autres conceptions ont été prises en compte ? Quel est l’impact de cette action ?

Il existe plusieurs façons d’exposer cette fonctionnalité dans le langage :

### <a name="1-special-global-method-declaration"></a>1. Déclaration de méthode globale spéciale

Un initialiseur de module est fourni en écrivant un genre spécial de méthode dans la portée globale :

```csharp
internal void operator init() ...
```

Cela donne à la nouvelle construction de langage sa propre syntaxe. Toutefois, étant donné la rare et la niche du scénario, il s’agit probablement beaucoup trop lourd d’une approche.

### <a name="2-attribute-on-the-type-to-be-initialized"></a>2. attribut sur le type à initialiser

Au lieu d’un attribut de niveau module, peut-être que l’attribut est placé sur le type à initialiser

```csharp
[ModuleInitializer]
class ToInitialize
{
    static ToInitialize() ...
}
```

Avec cette approche, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois. Dans les deux cas, il est plus complexe que la proposition d’origine ci-dessus.

#### <a name="3-attribute-on-the-static-constructor-to-be-initialized"></a>3. attribut sur le constructeur statique à initialiser

Au lieu d’un attribut au niveau du module, peut-être que l’attribut serait placé sur le constructeur statique à initialiser

```csharp
class ToInitialize
{
    [ModuleInitializer]
    static ToInitialize() ...
}
```

Avec cette approche, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois. Dans les deux cas, il est plus complexe que la proposition d’origine ci-dessus.

#### <a name="4-attribute-on-a-static-method-to-be-called"></a>4. attribut sur une méthode statique à appeler

Au lieu d’un attribut au niveau du module, peut-être que l’attribut serait placé sur la méthode à appeler pour effectuer l’initialisation

```csharp
class Any
{
    [ModuleInitializer]
    static void Initializer() ...
}
```

Comme dans l’approche précédente, nous aurions besoin de rejeter un programme qui contient plus d’une application de cet attribut, ou de fournir une stratégie pour définir le classement au cas où il serait utilisé plusieurs fois. Dans les deux cas, il est plus complexe que la proposition d’origine. Nous aurions également probablement besoin de rejeter toute méthode avec cet attribut si elle a des paramètres ou un type`void` de retour non-.

#### <a name="5-do-nothing"></a>5. ne rien faire
Si nous n’implémentons pas cette fonctionnalité, procédez comme suit :
- Les utilisateurs qui ont vraiment besoin d’initialiseurs de module continuent de s’appuyer sur des outils tiers pour les injecter dans leurs assemblys.
- Les générateurs de code source doivent s’appuyer sur des constructeurs statiques qui ajoutent de la surcharge ou sur les méthodes Init () que les utilisateurs doivent appeler explicitement.

## <a name="design-meetings"></a>Réunions de conception

### <a name="april-8th-2020"></a>[8 avril 2020](/meetings/2020/LDM-2020-04-08.md#module-initializers)
Permettons à toute méthode statique d’être un initialiseur de module (option 4 dans la proposition ci-dessus) et de marquer cette méthode à l’aide d’un attribut connu. Nous allons également autoriser plusieurs méthodes d’initialiseur de module, et elles seront appelées dans un ordre réservé, mais déterministe.
