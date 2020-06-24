---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: aa2d828c824c5d81c5aa20ba4d3c6880633088ed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85279067"
---
# <a name="module-initializers"></a>Initialiseurs de module

* [x] proposé
* [] Prototype : [en cours](https://github.com/jnm2/roslyn/tree/module_initializer)
* [] Implémentation : [en cours](https://github.com/dotnet/roslyn/tree/features/module-initializers)
* [] Spécification : [non démarrée]()

## <a name="summary"></a>Résumé
[summary]: #summary

Bien que la plateforme .NET dispose d’une [fonctionnalité](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) qui prend directement en charge l’écriture du code d’initialisation de l’assembly (techniquement, le module), elle n’est pas exposée en C#.  Il s’agit d’un scénario plutôt que des niches, mais une fois que vous y êtes dans, les solutions semblent assez pénibles.  Il existe des rapports d' [un certain nombre de clients](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (à l’intérieur et à l’extérieur de Microsoft) qui éprouvent le problème et il n’y a aucun doute sur les cas non documentés.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

- Permettez aux bibliothèques d’effectuer des initialisations opportunes et ponctuelles lorsqu’elles sont chargées, avec une charge minimale et sans que l’utilisateur ait besoin d’appeler explicitement quoi que ce soit
- Un point délicat particulier des approches actuelles du `static` constructeur est que le runtime doit effectuer des vérifications supplémentaires sur l’utilisation d’un type avec un constructeur statique, afin de déterminer si le constructeur statique doit être exécuté ou non. Cela ajoute une surcharge mesurable.
- Activer les générateurs de source pour exécuter une logique d’initialisation globale sans que l’utilisateur doive appeler explicitement quoi que ce soit

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Une méthode peut être désignée en tant qu’initialiseur de module en la décorant avec un `[ModuleInitializer]` attribut.

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

L’attribut peut être utilisé comme suit :

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

Certaines exigences sont imposées sur la méthode ciblée avec cet attribut :
1. La méthode doit être `static` .
1. La méthode doit être sans paramètre.
1. La méthode doit retourner `void`.
1. La méthode ne doit pas être générique ou être contenue dans un type générique.
1. La méthode doit être accessible à partir du module conteneur.
    - Cela signifie que l’accessibilité effective de la méthode doit être `internal` ou `public` .
    - Cela signifie également que la méthode ne peut pas être une fonction locale.
    
Quand une ou plusieurs méthodes valides avec cet attribut sont trouvées dans une compilation, le compilateur émet un initialiseur de module qui appelle chacune des méthodes attribuées. Les appels seront émis dans un ordre réservé, mais déterministe.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Pourquoi n’est-ce *pas* ?

- Les outils tiers existants pour « l’injection » d’initialiseurs de module sont peut-être suffisants pour les utilisateurs qui ont demandé cette fonctionnalité.

## <a name="design-meetings"></a>Réunions de conception

### <a name="april-8th-2020"></a>[8 avril 2020](/meetings/2020/LDM-2020-04-08.md#module-initializers)
