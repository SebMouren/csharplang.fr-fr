---
ms.openlocfilehash: 2e054c629f71ae37b112300905c3106f9ce977e8
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484663"
---
# <a name="auto-implemented-property-field-targeted-attributes"></a>Champ de propriété implémentée automatiquement-attributs ciblés

## <a name="summary"></a>Résumé
[summary]: #summary

Cette fonctionnalité envisage d’autoriser les développeurs à appliquer des attributs directement aux champs de stockage des propriétés implémentées automatiquement.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Actuellement, il n’est pas possible d’appliquer des attributs aux champs de stockage des propriétés implémentées automatiquement.  Dans les cas où le développeur doit utiliser un attribut de ciblage de champ, il est contraint de déclarer le champ manuellement et d’utiliser la syntaxe de propriété plus détaillée.  Étant donné C# que a toujours pris en charge des attributs ciblés sur les champs sur le champ de stockage généré pour les événements, il est logique d’étendre les mêmes fonctionnalités à leur propriété comparses.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

En bref, les éléments suivants sont valides C# et ne génèrent pas d’avertissement :

```csharp
[Serializable]
public class Foo 
{
    [field: NonSerialized]
    public string MySecret { get; set; }
}
```

Cela entraînerait l’application des attributs ciblés sur les champs au champ de stockage généré par le compilateur :

```csharp
[Serializable]
public class Foo 
{
    [NonSerialized]
    private string _mySecretBackingField;
    
    public string MySecret
    {
        get { return _mySecretBackingField; }
        set { _mySecretBackingField = value; }
    }
}
```

Comme nous l’avons déjà mentionné, cela apporte la C# parité avec la syntaxe d’événement de 1,0, car les éléments suivants sont déjà légaux et se comportent comme prévu :

```csharp
[Serializable]
public class Foo
{
    [field: NonSerialized]
    public event EventHandler MyEvent;
}
```

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Il existe deux inconvénients potentiels à l’implémentation de cette modification :

1. Toute tentative d’application d’un attribut au champ d’une propriété implémentée automatiquement génère un avertissement du compilateur indiquant que les attributs de ce bloc seront ignorés.  Si le compilateur a été modifié pour prendre en charge ces attributs, ils seraient appliqués au champ de stockage lors d’une recompilation suivante, ce qui pourrait altérer le comportement du programme au moment de l’exécution.
1. Le compilateur ne valide pas actuellement les cibles AttributeUsage des attributs lorsque vous tentez de les appliquer au champ de la propriété implémentée automatiquement.  Si le compilateur a été modifié pour prendre en charge des attributs ciblés sur les champs et que l’attribut en question ne peut pas être appliqué à un champ, le compilateur émettra une erreur au lieu d’un avertissement, ce qui a pour effet de rompre la génération.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Réunions de conception
