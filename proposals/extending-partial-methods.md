---
ms.openlocfilehash: 6fbc866af9971d86a287b026013e235e5b25fc21
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162072"
---
<a name="extending-partial-methods"></a>Étendre des méthodes partielles
=====

## <a name="summary"></a>Récapitulatif
Cette proposition vise à supprimer toutes les restrictions concernant les signatures `partial` des méthodes en C#. L’objectif est de développer l’ensemble des scénarios dans lesquels ces méthodes peuvent fonctionner avec les générateurs de code source, ainsi qu’une forme de déclaration plus générale pour les méthodes C#.

Consultez également la [spécification des méthodes partielles d’origine](/spec/classes.md#partial-methods).

## <a name="motivation"></a>Motivation
C# offre une prise en charge limitée pour les développeurs qui fractionnent les méthodes en déclarations et définitions/implémentations. 

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

L’un des `partial` comportements des méthodes est que lorsque la définition est absente, le langage efface simplement `partial` tous les appels à la méthode. Il se comporte essentiellement comme un appel à une `[Conditional]` méthode dans laquelle la condition a été évaluée à false. 

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

La motivation initiale de cette fonctionnalité était la génération de la source sous la forme d’un code généré par le concepteur. Les utilisateurs modifient en permanence le code généré parce qu’ils souhaitaient raccorder certains aspects du code généré. Plus particulièrement les parties du processus de démarrage Windows Forms, après l’initialisation des composants.

La modification du code généré était sujette aux erreurs, car toute action ayant entraîné la régénération du code par le concepteur entraînerait l’effacement de la modification de l’utilisateur. La `partial` fonctionnalité de méthode a facilité la traction, car elle a permis aux concepteurs d' `partial` émettre des raccordements sous la forme de méthodes. 

Les concepteurs peuvent émettre `partial void OnComponentInit()` des points de raccordement comme et les développeurs peuvent définir des déclarations pour eux ou ne pas les définir. Dans les deux cas, même si le code généré est compilé et que les développeurs intéressés par le processus pouvaient se raccorder en fonction des besoins. 

Cela signifie que les méthodes partielles ont plusieurs restrictions :

1. Doit avoir un `void` type de retour.
1. Impossible d' `out` avoir des paramètres. 
1. Impossible d’avoir une accessibilité (implicitement `private`).

Ces restrictions existent, car la langue doit être en mesure d’émettre du code lorsque le site d’appel est effacé. Étant donné qu’ils peuvent être `private` effacés, la seule accessibilité possible est que le membre ne peut pas être exposé dans les métadonnées de l’assembly. Ces restrictions servent également à limiter l’ensemble des scénarios dans lesquels `partial` les méthodes peuvent être appliquées.

La proposition ici consiste à supprimer toutes les restrictions existantes autour `partial` des méthodes. Ils les laissent `out`, les types de retour non void ou n’importe quel type d’accessibilité. Ces `partial` déclarations auront alors la nécessité ajoutée qu’une définition doit exister. Cela signifie que le langage n’a pas à prendre en compte l’impact de l’effacement des sites d’appel. 

Cela développerait l’ensemble des scénarios de générateur `partial` auxquels les méthodes pourraient participer et, par conséquent, nous permettons de créer une liaison avec notre fonctionnalité de générateurs de source. Par exemple, une expression régulière peut être définie à l’aide du modèle suivant :

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

Cela donne aux développeurs un moyen déclaratif simple d’opter pour les générateurs et de donner aux générateurs un ensemble très facile de déclarations à examiner dans le code source pour piloter leur sortie générée. 

Comparez cela à la difficulté qu’un générateur aurait raccordé à l’extrait de code suivant. 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

Étant donné que le compilateur n’autorise pas les générateurs à modifier le raccordement de code, ce modèle serait beaucoup impossible pour les générateurs. Ils doivent recourir à la réflexion dans l' `IsMatch` implémentation ou demander aux utilisateurs de remplacer leurs sites d’appel par une nouvelle méthode + refactoriser l’expression régulière pour passer le littéral de chaîne en tant qu’argument. C’est assez compliqué.

## <a name="detailed-design"></a>Conception détaillée
Le langage change pour permettre `partial` l’annotation des méthodes avec un modificateur d’accessibilité explicite. Cela signifie qu’ils peuvent être `private`étiquetés `public`comme,, etc. 

Quand une `partial` méthode a un modificateur d’accessibilité explicite, bien que le langage exige que la déclaration ait une définition correspondante même lorsque l' `private`accessibilité est :

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

En outre, le langage supprimera toutes les restrictions sur ce qui `partial` peut apparaître sur une méthode qui a une accessibilité explicite. Ces déclarations peuvent contenir des types de retour non void `out` , des `extern` paramètres, des modificateurs, etc. Ces signatures auront l’intégralité de l’expression du langage C#.

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

Cela permet explicitement aux `partial` méthodes de participer aux `overrides` implémentations et `interface` :

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

Le compilateur modifie l’erreur qu’il émet lorsqu’une `partial` méthode contient un élément illégal pour dire :

> Impossible d' `ref` utiliser sur `partial` une méthode qui n’a pas d’accessibilité explicite 

Cela aidera les développeurs dans le sens adapté à l’utilisation de cette fonctionnalité.

Restrictions :
- `partial`les déclarations avec accessibilité explicite doivent avoir une définition
- `partial`les déclarations et les signatures de définition doivent correspondre sur tous les modificateurs de méthode et de paramètre. Les seuls aspects qui peuvent différer sont les noms de paramètres et les listes d’attributs (cela n’est pas nouveau `partial` , mais plutôt une exigence existante des méthodes).

## <a name="questions"></a>Questions

### <a name="partial-on-all-members"></a>partielle sur tous les membres
Étant donné que nous développons `partial` pour être plus conviviaux pour les générateurs de code source, devons-nous également le développer pour travailler sur tous les membres de classe ? Par exemple, vous devriez pouvoir déclarer `partial` des constructeurs, des opérateurs, etc.

**Résolution** L’idée est un son, mais à ce stade de la planification C# 9, nous essayons d’éviter la compensation des fonctionnalités inutiles. Vous souhaitez résoudre le problème immédiat lié au développement de la fonctionnalité afin qu’elle fonctionne avec les générateurs de source modernes. 

L' `partial` extension de pour prendre en charge d’autres membres sera prise en compte pour la version C# 10. Il semble que nous envisageons cette extension.

### <a name="use-abstract-instead-of-partial"></a>Utiliser abstract au lieu de partial
Le essentiel de cette proposition consiste essentiellement à s’assurer qu’une déclaration a une définition/implémentation correspondante. Étant donné que nous devons `abstract` utiliser, car il s’agit déjà d’un mot clé de langage qui oblige le développeur à réfléchir à la mise en œuvre d’une implémentation ?

**Résolution** Il y a eu une discussion saine sur ce sujet, mais il a finalement été décidé.
Oui, les spécifications sont familières, mais les concepts sont très différents.
Peut permettre au développeur de croire qu’il a créé des emplacements virtuels alors qu’il ne l’a pas fait.
