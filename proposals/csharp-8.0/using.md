---
ms.openlocfilehash: 91afbc3e3412049cd183c36c8035f1862c520413
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485055"
---
# <a name="pattern-based-using-and-using-declarations"></a>« utilisation basée sur des modèles » et « utilisation des déclarations »

## <a name="summary"></a>Résumé

Le langage ajoutera deux nouvelles fonctionnalités autour de l’instruction `using` afin de simplifier la gestion des ressources : `using` doit reconnaître un modèle jetable en plus de `IDisposable` et ajouter une déclaration de `using` à la langue.

## <a name="motivation"></a>Motivation

L’instruction `using` est un outil efficace pour la gestion des ressources, mais elle nécessite un peu de cérémonie. Les méthodes qui ont un certain nombre de ressources à gérer peuvent être facilement coincées dans une série d’instructions `using`. Cette charge de syntaxe est suffisante pour que la plupart des règles de style de codage aient explicitement une exception autour des accolades pour ce scénario. 

La déclaration de `using` supprime la plus grande partie de la C# cérémonie ici et s’obtient en même temps que d’autres langages qui incluent des blocs de gestion des ressources. En outre, le `using` basé sur des modèles permet aux développeurs de développer l’ensemble des types qui peuvent participer ici. Dans de nombreux cas, il est inutile de créer des types de wrappers qui n’existent que pour permettre l’utilisation d’une valeur dans une instruction `using`. 

Ensemble, ces fonctionnalités permettent aux développeurs de simplifier et d’étendre les scénarios dans lesquels les `using` peuvent être appliquées.

## <a name="detailed-design"></a>Conception détaillée 

### <a name="using-declaration"></a>déclaration using

Le langage permet d’ajouter des `using` à une déclaration de variable locale. Une telle déclaration aura le même effet que la déclaration de la variable dans une instruction `using` au même emplacement.

```csharp
if (...) 
{ 
   using FileStream f = new FileStream(@"C:\users\jaredpar\using.md");
   // statements
}

// Equivalent to 
if (...) 
{ 
   using (FileStream f = new FileStream(@"C:\users\jaredpar\using.md")) 
   {
    // statements
   }
}
```

La durée de vie d’un `using` local s’étend jusqu’à la fin de l’étendue dans laquelle il est déclaré. Les variables locales `using` sont ensuite supprimées dans l’ordre inverse dans lequel elles sont déclarées. 

```csharp
{ 
    using var f1 = new FileStream("...");
    using var f2 = new FileStream("..."), f3 = new FileStream("...");
    ...
    // Dispose f3
    // Dispose f2 
    // Dispose f1
}
```

Il n’existe aucune restriction concernant `goto`, ou toute autre construction de workflow de contrôle en face d’une déclaration `using`. Au lieu de cela, le code agit comme pour l’instruction `using` équivalente :

```csharp
{
    using var f1 = new FileStream("...");
  target:
    using var f2 = new FileStream("...");
    if (someCondition) 
    {
        // Causes f2 to be disposed but has no effect on f1
        goto target;
    }
}
```

Un local déclaré dans une déclaration locale `using` est implicitement en lecture seule. Cela correspond au comportement des variables locales déclarées dans une instruction `using`. 

La grammaire du langage pour les déclarations de `using` est la suivante :

```antlr
local-using-declaration:
  using type using-declarators

using-declarators:
  using-declarator
  using-declarators , using-declarator
  
using-declarator:
  identifier = expression
```

Restrictions relatives à la déclaration de `using` :

- Peut ne pas apparaître directement à l’intérieur d’une étiquette de `case`, mais au lieu d’être dans un bloc à l’intérieur de l’étiquette `case`.
- Peut ne pas apparaître dans le cadre d’une déclaration de variable `out`. 
- Doit avoir un initialiseur pour chaque déclarateur.
- Le type local doit être implicitement convertible en `IDisposable` ou respecter le modèle de `using`.

### <a name="pattern-based-using"></a>utilisation basée sur des modèles

Le langage ajoutera la notion d’un modèle jetable : il s’agit d’un type qui a une méthode d’instance accessible `Dispose`. Les types qui correspondent au modèle jetable peuvent participer à une instruction `using` ou à une déclaration sans qu’il soit nécessaire d’implémenter `IDisposable`. 

```csharp
class Resource
{ 
    public void Dispose() { ... }
}

using (var r = new Resource())
{
    // statements
}
```

Cela permet aux développeurs de tirer parti de `using` dans un certain nombre de nouveaux scénarios :

- `ref struct`: ces types ne peuvent pas implémenter d’interfaces aujourd’hui et ne peuvent donc pas participer aux instructions `using`.
- Les méthodes d’extension permettent aux développeurs d’ajouter des types dans d’autres assemblys pour participer à `using` instructions.

Dans le cas où un type peut être implicitement converti en `IDisposable` et s’adapte également au modèle jetable, `IDisposable` sera préféré. Bien que cela prenne l’approche opposée de `foreach` (modèle préféré par rapport à l’interface), elle est nécessaire à des fins de compatibilité descendante.

Les mêmes restrictions qu’une instruction de `using` traditionnelle s’appliquent également ici : les variables locales déclarées dans le `using` sont en lecture seule, une valeur de `null` n’entraîne pas la levée d’une exception, etc. La génération de code sera différente uniquement en ce qu’il n’y aura pas de cast en `IDisposable` avant d’appeler dispose :

```csharp
{
      Resource r = new Resource();
      try {
            // statements
      }
      finally {
            if (resource != null) resource.Dispose();
      }
}
```

Pour s’adapter au modèle jetable, la méthode `Dispose` doit être accessible, sans paramètre et avoir un type de retour `void`. Il n’existe aucune autre restriction. Cela signifie explicitement que les méthodes d’extension peuvent être utilisées ici.

## <a name="considerations"></a>Considérations

### <a name="case-labels-without-blocks"></a>étiquettes case sans blocs

Une `using declaration` est directement illégale à l’intérieur d’une étiquette `case` en raison de complications autour de sa durée de vie réelle. Une solution possible consiste simplement à lui attribuer la même durée de vie qu’une `out var` au même emplacement. Il a été considéré comme la complexité supplémentaire de l’implémentation des fonctionnalités et de la facilité de travail (juste ajouter un bloc à l’étiquette `case`) n’a pas justifié la réalisation de cet itinéraire.

## <a name="future-expansions"></a>Expansions futures

### <a name="fixed-locals"></a>variables locales fixes

Une instruction `fixed` possède toutes les propriétés des instructions `using` qui ont motivé la capacité à avoir `using` variables locales. Vous devez tenir compte de l’extension de cette fonctionnalité à `fixed` les variables locales. Les règles de durée de vie et de classement doivent s’appliquer aussi bien pour `using` et `fixed` ici.
