---
ms.openlocfilehash: ae8b583d82f99135e2ba731604cd1febff4fade7
ms.sourcegitcommit: 125539b88d970bb2924d17a997c47cc0c7b806fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566751"
---
# <a name="relaxed-rules-for-auto-properties-in-structs"></a>Règles assouplies pour les propriétés automatiques dans les structs 

Avant C# 6,0, vous n’avez pas pu écrire du code tel que celui-ci : 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) 
    { 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

```none
error CS0188: The 'this' object cannot be used before all of its fields are assigned to
error CS0843: Backing field for automatically implemented property 'S.X' must be fully assigned before control is returned to the caller. Consider calling the default constructor from a constructor initializer. 
error CS0843: Backing field for automatically implemented property 'S.Y' must be fully assigned before control is returned to the caller. Consider calling the default constructor from a constructor initializer. 
```
 
Une solution de contournement consistait à appeler le constructeur par défaut : 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) : this() 
    { 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

ou pour initialiser la valeur par défaut : 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) 
    { 
        this = default(S); 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

Malheureusement, les deux méthodes ont effectivement désactivé l’analyse d’assignation définie pour les champs d’instance pour le constructeur donné. 

C# 6,0 assouplit ces règles en effectuant le suivi des assignations aux propriétés automatiques d’instance comme s’il s’agissait d’assignations à leurs champs de stockage, de sorte que le code au début de cette section est désormais valide. 
