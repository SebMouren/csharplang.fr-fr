---
ms.openlocfilehash: ae8b583d82f99135e2ba731604cd1febff4fade7
ms.sourcegitcommit: 125539b88d970bb2924d17a997c47cc0c7b806fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566751"
---
# <a name="relaxed-rules-for-auto-properties-in-structs"></a><span data-ttu-id="59581-101">Règles assouplies pour les propriétés automatiques dans les structs</span><span class="sxs-lookup"><span data-stu-id="59581-101">Relaxed rules for auto-properties in structs</span></span> 

<span data-ttu-id="59581-102">Avant C# 6,0, vous n’avez pas pu écrire du code tel que celui-ci :</span><span class="sxs-lookup"><span data-stu-id="59581-102">Before C# 6.0 you could not write code like this:</span></span> 

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
 
<span data-ttu-id="59581-103">Une solution de contournement consistait à appeler le constructeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="59581-103">A workaround was to invoke the default constructor:</span></span> 

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

<span data-ttu-id="59581-104">ou pour initialiser la valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="59581-104">or to initialize this to the default value:</span></span> 

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

<span data-ttu-id="59581-105">Malheureusement, les deux méthodes ont effectivement désactivé l’analyse d’assignation définie pour les champs d’instance pour le constructeur donné.</span><span class="sxs-lookup"><span data-stu-id="59581-105">Unfortunately, both methods effectively disabled definite assignment analysis for instance fields for the given constructor.</span></span> 

<span data-ttu-id="59581-106">C# 6,0 assouplit ces règles en effectuant le suivi des assignations aux propriétés automatiques d’instance comme s’il s’agissait d’assignations à leurs champs de stockage, de sorte que le code au début de cette section est désormais valide.</span><span class="sxs-lookup"><span data-stu-id="59581-106">C# 6.0 relaxes these rules by tracking assignments to instance auto-properties as if they were assignments to their backing fields, so the code in the beginning of this section is now valid.</span></span> 
