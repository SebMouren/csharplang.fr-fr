---
ms.openlocfilehash: 04dca01bad04d5c53aa1c7c876343fb7ef33d2fa
ms.sourcegitcommit: 5c7cc619214ade6a8f3a0caddfb4862635f5241d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82021929"
---
<a name="extending-partial-methods"></a>Extension des méthodes partielles
=====

## <a name="summary"></a>Résumé
Cette proposition vise à supprimer toutes `partial` les restrictions concernant les signatures de méthodes en C. L’objectif étant d’élargir l’ensemble de scénarios dans lesquels ces méthodes peuvent fonctionner avec les générateurs de sources ainsi que d’être un formulaire de déclaration plus générale pour les méthodes C.

## <a name="motivation"></a>Motivation
Le support limité des développeurs pour les méthodes de fractionnement en déclarations, définitions/implémentations. 

```cs 
partial class C
{
    // The declaration of C.M
    partial void M(string message);
}

partial class C
{
    // The definition of C.M
    partial void M(string message) => Console.WriteLine(message);
}
```

Un comportement `partial` des méthodes est que lorsque la définition est absente, alors la langue effacera simplement tous les appels à la `partial` méthode. Essentiellement, il se comporte `[Conditional]` comme un appel à une méthode où la condition a été évaluée à faux. 

```cs
partial class D
{
    partial void M(string message);

    void Example()
    {
        M(GetIt()); // Call to M and GetIt erased at compile time
    }

    string GetIt() => "Hello World";
}
```

La motivation originale pour cette fonctionnalité était la génération source sous la forme de code généré par le concepteur. Les utilisateurs éditaient constamment le code généré parce qu’ils voulaient brancher un aspect du code généré. Plus particulièrement des parties du processus de démarrage de Windows Forms, après l’initialisation des composants.

L’édition du code généré était sujette aux erreurs parce que toute action qui a amené le concepteur à régénérer le code entraînerait l’effacement de l’éditeur de l’utilisateur. La `partial` fonction de méthode a atténué cette tension car elle `partial` a permis aux concepteurs d’émettre des crochets sous forme de méthodes. 

Les concepteurs pourraient `partial void OnComponentInit()` émettre des crochets comme et les développeurs pourraient définir des déclarations pour eux ou ne pas les définir. Dans les deux cas, bien que le code généré serait compiler et les développeurs qui étaient intéressés par le processus pourrait s’accrocher au besoin. 

Cela signifie que les méthodes partielles ont plusieurs restrictions:

1. Doit avoir `void` un type de retour.
1. Impossible `ref` d’avoir ou `out` de paramètres. 
1. Impossible d’avoir d’accessibilité (implicitement `private`).

Ces restrictions existent parce que la langue doit être en mesure d’émettre du code lorsque le site d’appel est effacé. Étant donné qu’ils peuvent être effacés `private` est la seule accessibilité possible parce que le membre ne peut pas être exposé dans les métadonnées d’assemblage. Ces restrictions servent également à limiter l’ensemble de scénarios dans lesquels `partial` des méthodes peuvent être appliquées.

La proposition ici est de supprimer `partial` toutes les restrictions existantes concernant les méthodes. Essentiellement, laissez-les avoir, `out`les types de retour non nuls ou tout autre type d’accessibilité. De `partial` telles déclarations auraient alors l’exigence supplémentaire qu’une définition doit exister. Cela signifie que la langue n’a pas à tenir compte de l’impact de l’effacement des sites d’appels. 

Cela élargirait l’ensemble des `partial` scénarios de générateurs que les méthodes pourraient participer et donc relier bien avec notre fonction de générateurs sources. Par exemple, un regex peut être défini à l’aide du modèle suivant :

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

Cela donne à la fois au développeur une façon simple déclarative d’opter pour les générateurs ainsi que de donner aux générateurs un ensemble très facile de déclarations à regarder à travers dans le code source pour conduire leur sortie générée. 

Comparez cela avec la difficulté qu’un générateur aurait brancher l’extrait de code suivant. 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

Étant donné que le compilateur ne permet pas aux générateurs de modifier le code branchant ce modèle serait à peu près impossible pour les générateurs. Ils auraient besoin de recourir `IsMatch` à la réflexion dans la mise en œuvre, ou de demander aux utilisateurs de changer leurs sites d’appel à une nouvelle méthode - refactor le regex pour passer la chaîne littérale comme un argument. C’est assez désordonné.

## <a name="detailed-design"></a>Conception détaillée
Le langage changera `partial` pour permettre aux méthodes d’être annotées avec un modificateur explicite d’accessibilité. Cela signifie qu’ils `private`peuvent `public`être étiquetés comme , , etc ... 

Lorsqu’une `partial` méthode a un modificateur explicite de l’accessibilité, bien que `private`le libellé exige que la déclaration ait une définition correspondante même lorsque l’accessibilité est :

```cs
partial class C
{
    // Okay because no definition is required here
    partial void M1();

    // Okay because M2 has a definition
    private partial void M2();

    // Error: partial method M3 must have a definition
    private partial void M3();
}

partial class C
{
    private partial void M2() { }
}
```

En outre, le langage supprimera toutes `partial` les restrictions sur ce qui peut apparaître sur une méthode qui a une accessibilité explicite. Ces déclarations peuvent contenir des `ref` types `out` de `extern` retour non nuls, ou paramètres, modificateur, etc ... Ces signatures auront toute l’expressivité de la langue C.

```cs
partial class D
{
    // Okay
    internal partial bool TryParse(string s, out int i); 
}

partial class D
{
    internal partial bool TryParse(string s, out int i) { }
}
```

Cela permet `partial` explicitement des `overrides` méthodes `interface` de participation et de mise en œuvre :

```cs
interface IStudent
{
    string GetName();
}

partial class C : IStudent
{
    public virtual partial string GetName(); 
}

partial class C
{
    public virtual partial string GetName() => "Jarde";
}
```

Le compilateur va changer l’erreur `partial` qu’il émet quand une méthode contient un élément illégal pour dire essentiellement:

> Impossible `ref` d’utiliser sur une `partial` méthode qui manque d’accessibilité explicite 

Cela aidera les développeurs à pointer dans la bonne direction lors de l’utilisation de cette fonctionnalité.

Restrictions :
- `partial`déclarations avec accessibilité explicite doivent avoir une définition
- `partial`les déclarations et les signatures de définition doivent correspondre à toutes les méthodes et les modificateurs de paramètres. Les seuls aspects qui peuvent différer sont les noms de paramètres `partial` et les listes d’attributs (ce n’est pas nouveau, mais plutôt une exigence existante de méthodes).

## <a name="questions"></a>Questions

### <a name="partial-on-all-members"></a>partielle sur tous les membres
Étant donné que `partial` nous sommes en expansion pour être plus amical pour les générateurs source devrions-nous également l’étendre pour travailler sur tous les membres de la classe? Par exemple, devrions-nous `partial` être en mesure de déclarer les constructeurs, les opérateurs, etc ...

**Résolution** L’idée est saine, mais à ce stade de l’horaire C 9, nous essayons d’éviter le fluage des fonctionnalités inutiles. Vous voulez résoudre le problème immédiat de l’expansion de la fonctionnalité pour travailler avec les générateurs sources modernes. 

L’extension `partial` à l’appui d’autres membres sera envisagée pour la version C 10. Il semble probable que nous envisagerons cette prolongation.

### <a name="use-abstract-instead-of-partial"></a>Utiliser abstrait au lieu de partielle
Le nœud de cette proposition est essentiellement de s’assurer qu’une déclaration a une définition correspondante / mise en œuvre. Étant donné que `abstract` devrions-nous utiliser car c’est déjà un mot clé de langue qui oblige le développeur à penser à avoir une implémentation?

**Résolution** Il y a eu une discussion saine à ce sujet, mais finalement il a été décidé contre.
Oui, les exigences sont familières, mais les concepts sont très différents.
Pourrait facilement conduire le développeur à croire qu’ils étaient la création de fentes virtuelles quand ils ne le faisaient pas.
