---
ms.openlocfilehash: ac4c8760e3b6a0934f01ae634f666af60aa1c0fe
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484565"
---
# <a name="compiler-intrinsics"></a>compilateur, intrinsèques

## <a name="summary"></a>Résumé

Cette proposition fournit des constructions de langage qui exposent des opcodes IL de bas niveau qui ne sont pas actuellement accessibles efficacement, ou du tout : `ldftn`, `ldvirtftn`, `ldtoken` et `calli`. Ces opcodes de bas niveau peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.

## <a name="motivation"></a>Motivation

Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) : 

https://github.com/dotnet/csharplang/issues/191

Cette proposition de conception alternative vient après la révision d’une implémentation prototype de la proposition d’origine par @msjabby, ainsi que de l’utilisation dans une base de code importante. Cette conception a été effectuée avec une entrée significative de @mjsabby, @tmat et @jkotas.

## <a name="detailed-design"></a>Conception détaillée 

### <a name="allow-address-of-to-target-methods"></a>Autoriser l’adresse de aux méthodes cibles

Les groupes de méthodes sont désormais autorisés comme arguments d’une expression d’adresse. Le type d’une telle expression sera `void*`. 

``` csharp
class Util { 
    public static void Log() { } 
}

// ldftn Util.Log
void* ptr = &Util.Log; 
```

Étant donné qu’il n’y a pas de conversion de délégué ici, le seul mécanisme de filtrage des membres dans le groupe de méthodes est l’accès statique/d’instance. Si cela ne peut pas faire la distinction entre les membres, une erreur de compilation se produit.

``` csharp
class Util { 
    public void Log() { } 
    public void Log(string p1) { } 
    public static void Log(int i) { };
}

unsafe {
    // Error: Method group Log has more than one applicable candidate.
    void* ptr1 = &Log; 

    // Okay: only one static member to consider here.
    void* ptr2 = &Util.Log;
}
```

L’expression AddressOf dans ce contexte sera implémentée de la manière suivante :

- ldftn : lorsque la méthode n’est pas virtuelle.
- ldvirtftn : lorsque la méthode est virtuelle.

Restrictions de cette fonctionnalité :

- Les méthodes d’instance ne peuvent être spécifiées que lors de l’utilisation d’une expression d’appel sur une valeur
- Les fonctions locales ne peuvent pas être utilisées dans `&`. Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage. Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.

### <a name="handleof"></a>handleof

Le mot clé contextuel `handleof` traduira un champ, un membre ou un type en son équivalent `RuntimeHandle` type à l’aide de l’instruction `ldtoken`. Le type exact de l’expression dépend du type du nom dans `handleof`:

- champ : `RuntimeFieldHandle`
- Type : `RuntimeTypeHandle`
- méthode : `RuntimeMethodHandle`

Les arguments de `handleof` sont identiques à `nameof`. Il doit s’agir d’un nom simple, d’un nom qualifié, d’un accès aux membres, d’un accès de base avec un membre spécifié ou de cet accès avec un membre spécifié. L'expression d'argument identifie une définition de code, mais n'est jamais évaluée.

L’expression `handleof` est évaluée au moment de l’exécution et a un type de retour `RuntimeHandle`. Cela peut être exécuté dans du code sécurisé et non sécurisé. 

``` 
RuntimeHandle stringHandle = handleof(string);
```

Restrictions de cette fonctionnalité :

- Les propriétés ne peuvent pas être utilisées dans une expression `handleof`.
- L’expression `handleof` ne peut pas être utilisée lorsqu’un nom de `handleof` existe dans l’étendue. Par exemple, un type, un espace de noms, etc.

### <a name="calli"></a>calli

Le compilateur ajoute la prise en charge d’un nouveau type de `extern` fonction qui se traduit efficacement en une instruction `.calli`. L’attribut extern est marqué avec un attribut de la forme suivante :

``` csharp
[AttributeUsage(AttributeTargets.Method)]
public sealed class CallIndirectAttribute : Attribute
{
    public CallingConvention CallingConvention { get; }
    public CallIndirectAttribute(CallingConvention callingConvention)
    {
        CallingConvention = callingConvention;
    }
}
```

Cela permet aux développeurs de définir des méthodes sous la forme suivante :

``` csharp
[CallIndirect(CallingConvention.Cdecl)]
static extern int MapValue(string s, void *ptr);

unsafe {
    var i = MapValue("42", &int.Parse);
    Console.WriteLine(i);
}
```

Restrictions sur la méthode qui a l’attribut `CallIndirect` appliqué :

- Impossible d’avoir un attribut `DllImport`.
- Ne peut pas être générique.

## <a name="open-issues"></a>Problèmes ouverts

### <a name="callingconvention"></a>CallingConvention

La `CallIndirectAttribute` conçue utilise l’énumération `CallingConvention` qui n’a pas d’entrée pour les conventions d’appel managées. L’énumération doit être étendue pour inclure cette Convention d’appel ou l’attribut doit adopter une approche différente.

## <a name="considerations"></a>Considérations

### <a name="disambiguating-method-groups"></a>Groupes de méthodes disambiguating

Il y a eu une discussion sur les fonctionnalités qui facilitent la désambiguisation des groupes de méthodes transmises à une expression d’adresse. Par exemple, il est possible d’ajouter des éléments de signature à la syntaxe suivante :

``` csharp
class Util {
    public static void Log() { ... }
    public static void Log(string) { ... }
}

unsafe {
    // Error: ambiguous Log
    void *ptr1 = &Util.Log;

    // Use Util.Log();
    void *ptr2 = &Util.Log();
}
```

Cette erreur a été rejetée, car il n’a pas été possible de faire une syntaxe simple. En outre, il existe une solution de contournement relativement simple : définissez une autre méthode qui n’est pas C# ambiguë et qui utilise du code pour appeler la fonction souhaitée. 

``` csharp
class Workaround {
    public static void LocalLog() => Util.Log();
}
unsafe { 
    void* ptr = &Workaround.LocalLog;
}
```

Cela devient encore plus simple si `static` fonctions locales entrent dans la langue. La solution peut être définie dans la même fonction que celle qui utilisait l’opération d’adresse ambiguë :

``` csharp
unsafe { 
    static void LocalLog() => Util.Log();
    void* ptr = &Workaround.LocalLog;
}
```

### <a name="loadtypetokenint32"></a>LoadTypeTokenInt32

Proposition d’origine autorisée pour les jetons de métadonnées à charger en tant que valeurs de `int` au moment de la compilation. A essentiellement `tokenof` qui a les mêmes arguments que `handleof` mais est évalué au moment de la compilation sur une constante `int`. 

Cela a été rejeté, car cela provoque un problème important pour les réécritures de langage intermédiaire (dont .NET a beaucoup). De tels réécriturs manipulent souvent les tables de métadonnées d’une manière qui pourrait invalider ces valeurs. Il n’existe pas de moyen raisonnable pour ces réécritions de mettre à jour ces valeurs quand elles sont stockées en tant que valeurs de `int` simples.

L’idée sous-jacente de la présence d’un handle opaque pour les entrées de métadonnées continuera d’être explorée par l’équipe du Runtime. 

## <a name="future-considerations"></a>Éléments futurs à prendre en considération

### <a name="static-local-functions"></a>fonctions locales statiques

Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/1565) pour autoriser le modificateur `static` sur les fonctions locales. Une telle fonction serait toujours émise comme `static` et avec la signature exacte spécifiée dans le code source. Une telle fonction doit être un argument valide pour `&`, car elle ne contient aucun des problèmes liés aux fonctions locales.

### <a name="nativecallableattribute"></a>NativeCallableAttribute

Le CLR dispose d’une fonctionnalité qui permet aux méthodes managées d’être émises de sorte qu’elles puissent être appelées directement à partir du code natif. Pour ce faire, ajoutez les `NativeCallableAttribute` aux méthodes. Une telle méthode peut uniquement être appelée à partir du code natif et doit donc contenir uniquement des types blittables dans la signature. L’appel à partir du code managé génère une erreur d’exécution. 

Cette fonctionnalité est parfaitement adaptée à cette offre, comme cela serait le cas :

- Passage d’une fonction définie en code managé au code natif en tant que pointeur de fonction (via Address-of) sans surcharge en code managé ou natif. 
- Le runtime peut introduire des erreurs de site à l’aide de ces fonctions dans du code managé afin de les empêcher d’être appelées au moment de la compilation.




