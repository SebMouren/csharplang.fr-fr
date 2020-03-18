---
ms.openlocfilehash: 2e054c629f71ae37b112300905c3106f9ce977e8
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484663"
---
# <a name="auto-implemented-property-field-targeted-attributes"></a><span data-ttu-id="78214-101">Champ de propriété implémentée automatiquement-attributs ciblés</span><span class="sxs-lookup"><span data-stu-id="78214-101">Auto-Implemented Property Field-Targeted Attributes</span></span>

## <a name="summary"></a><span data-ttu-id="78214-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="78214-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="78214-103">Cette fonctionnalité envisage d’autoriser les développeurs à appliquer des attributs directement aux champs de stockage des propriétés implémentées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="78214-103">This feature intends to allow developers to apply attributes directly to the backing fields of auto-implemented properties.</span></span>

## <a name="motivation"></a><span data-ttu-id="78214-104">Motivation</span><span class="sxs-lookup"><span data-stu-id="78214-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="78214-105">Actuellement, il n’est pas possible d’appliquer des attributs aux champs de stockage des propriétés implémentées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="78214-105">Currently it is not possible to apply attributes to the backing fields of auto-implemented properties.</span></span>  <span data-ttu-id="78214-106">Dans les cas où le développeur doit utiliser un attribut de ciblage de champ, il est contraint de déclarer le champ manuellement et d’utiliser la syntaxe de propriété plus détaillée.</span><span class="sxs-lookup"><span data-stu-id="78214-106">In those cases where the developer must use a field-targeting attribute they are forced to declare the field manually and use the more verbose property syntax.</span></span>  <span data-ttu-id="78214-107">Étant donné C# que a toujours pris en charge des attributs ciblés sur les champs sur le champ de stockage généré pour les événements, il est logique d’étendre les mêmes fonctionnalités à leur propriété comparses.</span><span class="sxs-lookup"><span data-stu-id="78214-107">Given that C# has always supported field-targeted attributes on the generated backing field for events it makes sense to extend the same functionality to their property kin.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="78214-108">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="78214-108">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="78214-109">En bref, les éléments suivants sont valides C# et ne génèrent pas d’avertissement :</span><span class="sxs-lookup"><span data-stu-id="78214-109">In short, the following would be legal C# and not produce a warning:</span></span>

```csharp
[Serializable]
public class Foo 
{
    [field: NonSerialized]
    public string MySecret { get; set; }
}
```

<span data-ttu-id="78214-110">Cela entraînerait l’application des attributs ciblés sur les champs au champ de stockage généré par le compilateur :</span><span class="sxs-lookup"><span data-stu-id="78214-110">This would result in the field-targeted attributes being applied to the compiler-generated backing field:</span></span>

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

<span data-ttu-id="78214-111">Comme nous l’avons déjà mentionné, cela apporte la C# parité avec la syntaxe d’événement de 1,0, car les éléments suivants sont déjà légaux et se comportent comme prévu :</span><span class="sxs-lookup"><span data-stu-id="78214-111">As mentioned, this brings parity with event syntax from C# 1.0 as the following is already legal and behaves as expected:</span></span>

```csharp
[Serializable]
public class Foo
{
    [field: NonSerialized]
    public event EventHandler MyEvent;
}
```

## <a name="drawbacks"></a><span data-ttu-id="78214-112">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="78214-112">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="78214-113">Il existe deux inconvénients potentiels à l’implémentation de cette modification :</span><span class="sxs-lookup"><span data-stu-id="78214-113">There are two potential drawbacks to implementing this change:</span></span>

1. <span data-ttu-id="78214-114">Toute tentative d’application d’un attribut au champ d’une propriété implémentée automatiquement génère un avertissement du compilateur indiquant que les attributs de ce bloc seront ignorés.</span><span class="sxs-lookup"><span data-stu-id="78214-114">Attempting to apply an attribute to the field of an auto-implemented property produces a compiler warning that the attributes in that block will be ignored.</span></span>  <span data-ttu-id="78214-115">Si le compilateur a été modifié pour prendre en charge ces attributs, ils seraient appliqués au champ de stockage lors d’une recompilation suivante, ce qui pourrait altérer le comportement du programme au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="78214-115">If the compiler were changed to support those attributes they would be applied to the backing field on a subsequent recompilation which could alter the behavior of the program at runtime.</span></span>
1. <span data-ttu-id="78214-116">Le compilateur ne valide pas actuellement les cibles AttributeUsage des attributs lorsque vous tentez de les appliquer au champ de la propriété implémentée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="78214-116">The compiler does not currently validate the AttributeUsage targets of the attributes when attempting to apply them to the field of the auto-implemented property.</span></span>  <span data-ttu-id="78214-117">Si le compilateur a été modifié pour prendre en charge des attributs ciblés sur les champs et que l’attribut en question ne peut pas être appliqué à un champ, le compilateur émettra une erreur au lieu d’un avertissement, ce qui a pour effet de rompre la génération.</span><span class="sxs-lookup"><span data-stu-id="78214-117">If the compiler were changed to support field-targeted attributes and the attribute in question cannot be applied to a field the compiler would emit an error instead of a warning, breaking the build.</span></span>

## <a name="alternatives"></a><span data-ttu-id="78214-118">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="78214-118">Alternatives</span></span>
[alternatives]: #alternatives

## <a name="unresolved-questions"></a><span data-ttu-id="78214-119">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="78214-119">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="78214-120">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="78214-120">Design meetings</span></span>
