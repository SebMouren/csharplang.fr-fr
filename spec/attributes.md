---
ms.openlocfilehash: a8ad8a8b3eda1d00fa745bd92e4371eacc36b79f
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229610"
---
# <a name="attributes"></a>Attributs

Une grande partie du langage c# permet au programmeur de spécifier des informations déclaratives sur les entités définies dans le programme. Par exemple, l’accessibilité d’une méthode dans une classe est spécifiée en la décorant avec le *method_modifier*s `public`, `protected`, `internal`, et `private`.

C# permet aux programmeurs d’inventer de nouveaux types d’informations déclaratives, appelé ***attributs***. Les programmeurs peuvent ensuite attacher des attributs à plusieurs entités de programme et récupérer des informations d’attribut dans un environnement d’exécution. Par exemple, une infrastructure peut définir un `HelpAttribute` attribut qui peut être placé sur des éléments de programme (par exemple, les classes et méthodes) pour fournir un mappage entre ces éléments vers leur documentation.

Les attributs sont définis via la déclaration de classes d’attributs ([classes d’attributs](attributes.md#attribute-classes)), qui peuvent avoir des paramètres positionnels et nommés ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)). Les attributs sont associés aux entités d’un programme c# à l’aide des spécifications d’attribut ([spécification des attributs](attributes.md#attribute-specification)) et peuvent être récupérés au moment de l’exécution sous la forme d’instances d’attribut ([des instances d’attribut](attributes.md#attribute-instances)).

## <a name="attribute-classes"></a>Classes d’attributs

Une classe qui dérive de la classe abstraite `System.Attribute`, directement ou indirectement, est un ***classe d’attributs***. La déclaration d’une classe d’attributs définit un nouveau type de ***attribut*** qui peut être placé dans une déclaration. Par convention, les classes d’attributs sont nommés avec un suffixe de `Attribute`. Utilisations d’un attribut peuvent inclure ou omettre ce suffixe.

### <a name="attribute-usage"></a>Utilisation d’attribut

L’attribut `AttributeUsage` ([attribut AttributeUsage le](attributes.md#the-attributeusage-attribute)) est utilisé pour décrire la façon dont une classe d’attributs peut être utilisée.

`AttributeUsage` a un paramètre positionnel ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)) qui permet à une classe d’attributs spécifier les types de déclarations sur lequel il peut être utilisé. L’exemple

```csharp
using System;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface)]
public class SimpleAttribute: Attribute 
{
    ...
}
```

définit une classe d’attributs nommée `SimpleAttribute` qui peut être placé sur *class_declaration*s et *interface_declaration*s uniquement. L’exemple

```csharp
[Simple] class Class1 {...}

[Simple] interface Interface1 {...}
```

illustre plusieurs utilisations de la `Simple` attribut. Bien que cet attribut est défini avec le nom `SimpleAttribute`, lorsque cet attribut est utilisé, le `Attribute` suffixe peut être omis, ce qui entraîne le nom court `Simple`. Par conséquent, l’exemple ci-dessus est sémantiquement équivalente à la suivante :

```csharp
[SimpleAttribute] class Class1 {...}

[SimpleAttribute] interface Interface1 {...}
```

`AttributeUsage` a un paramètre nommé ([paramètres nommés et positionnels](attributes.md#positional-and-named-parameters)) appelé `AllowMultiple`, ce qui indique si l’attribut peut être spécifié plusieurs fois pour une entité donnée. Si `AllowMultiple` d’un attribut de classe a la valeur true, cette classe d’attributs est un ***classe d’attributs à usages multiples***et peut être spécifié plusieurs fois sur une entité. Si `AllowMultiple` d’un attribut de classe a la valeur false ou il n’est pas spécifié, cette classe d’attributs est un ***classe d’attributs à usage unique***et peut être spécifié au maximum une fois sur une entité.

L’exemple

```csharp
using System;

[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class AuthorAttribute: Attribute
{
    private string name;

    public AuthorAttribute(string name) {
        this.name = name;
    }

    public string Name {
        get { return name; }
    }
}
```

définit une classe d’attributs à usages multiples nommée `AuthorAttribute`. L’exemple

```csharp
[Author("Brian Kernighan"), Author("Dennis Ritchie")] 
class Class1
{
    ...
}
```

illustre une déclaration de classe avec deux utilisations de la `Author` attribut.

`AttributeUsage` a un paramètre nommé appelé `Inherited`, ce qui indique si l’attribut, lorsqu’il est spécifié sur une classe de base, est également hérité par les classes qui dérivent de cette classe de base. Si `Inherited` d’un attribut de classe est vrai, alors cet attribut est hérité. Si `Inherited` d’un attribut de classe a la valeur false, alors cet attribut n’est pas hérité. Si elle n’est pas spécifié, sa valeur par défaut est true.

Une classe d’attributs `X` n’ayant ne pas un `AttributeUsage` attribut joint, comme dans

```csharp
using System;

class X: Attribute {...}
```

Est équivalente à la suivante :

```csharp
using System;

[AttributeUsage(
    AttributeTargets.All,
    AllowMultiple = false,
    Inherited = true)
]
class X: Attribute {...}
```

### <a name="positional-and-named-parameters"></a>Paramètres positionnels et nommés

Classes d’attributs peuvent avoir ***paramètres positionnels*** et ***des paramètres nommés***. Chaque constructeur d’instance public pour une classe d’attributs définit une séquence valide de paramètres positionnels pour cette classe d’attributs. Chaque champ de lecture-écriture non statique publique et de la propriété pour une classe d’attributs définissent un paramètre nommé pour la classe d’attributs.

L’exemple

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpAttribute: Attribute
{
    public HelpAttribute(string url) {        // Positional parameter
        ...
    }

    public string Topic {                     // Named parameter
        get {...}
        set {...}
    }

    public string Url {
        get {...}
    }
}
```

définit une classe d’attributs nommée `HelpAttribute` qui a un paramètre positionnel, `url`et un seul paramètre nommé, `Topic`. Bien qu’il soit non static et public, la propriété `Url` ne définit pas un paramètre nommé, dans la mesure où il n’est pas en lecture-écriture.

Cette classe d’attributs peut être utilisée comme suit :

```csharp
[Help("http://www.mycompany.com/.../Class1.htm")]
class Class1
{
    ...
}

[Help("http://www.mycompany.com/.../Misc.htm", Topic = "Class2")]
class Class2
{
    ...
}
```

### <a name="attribute-parameter-types"></a>Types de paramètre d’attribut

Les types des paramètres positionnels et nommés pour une classe d’attributs sont limités à la ***les types de paramètre d’attribut***, qui sont :

*  Un des types suivants : `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.
*  Le type `object`.
*  Le type `System.Type`.
*  Un type enum, fournie il a une accessibilité publique et les types dans lequel elle est imbriquée (le cas échéant) ont également une accessibilité publique ([spécification des attributs](attributes.md#attribute-specification)).
*  Tableaux unidimensionnels de types ci-dessus.
*  Un argument de constructeur ou un champ public qui n’a pas un de ces types, ne peut pas être utilisé comme un paramètre positionnel ou nommé dans une spécification d’attribut.

## <a name="attribute-specification"></a>Spécification d’attribut

***Spécification des attributs*** est l’application d’un attribut défini précédemment à une déclaration. Un attribut est une partie des informations déclaratives supplémentaires qui sont spécifiées pour une déclaration. Attributs peuvent être spécifiés dans une portée globale (pour spécifier des attributs sur l’assembly ou le module conteneur) et pour *type_declaration*s ([les déclarations de Type](namespaces.md#type-declarations)), *class_member_declaration* s ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([membres de l’Interface](interfaces.md#interface-members)), *struct_member _declaration*s ([les membres de Struct](structs.md#struct-members)), *enum_member_declaration*s ([membres Enum](enums.md#enum-members)), *accessor_declarations*  ([Accesseurs](classes.md#accessors)), *event_accessor_declarations* ([les événements de type champ](classes.md#field-like-events)), et *formal_parameter_list*s ([paramètres de méthode](classes.md#method-parameters)).

Les attributs sont spécifiés dans ***attribut sections***. Une section d’attributs se compose d’une paire de crochets qui entourent une liste séparée par des virgules d’un ou plusieurs attributs. L’ordre dans lequel les attributs sont spécifiés dans une telle liste et l’ordre dans lequel les sections attachées à la même entité de programme sont organisés, n’est pas significatif. Par exemple, les spécifications de l’attribut `[A][B]`, `[B][A]`, `[A,B]`, et `[B,A]` sont équivalentes.

```antlr
global_attributes
    : global_attribute_section+
    ;

global_attribute_section
    : '[' global_attribute_target_specifier attribute_list ']'
    | '[' global_attribute_target_specifier attribute_list ',' ']'
    ;

global_attribute_target_specifier
    : global_attribute_target ':'
    ;

global_attribute_target
    : 'assembly'
    | 'module'
    ;

attributes
    : attribute_section+
    ;

attribute_section
    : '[' attribute_target_specifier? attribute_list ']'
    | '[' attribute_target_specifier? attribute_list ',' ']'
    ;

attribute_target_specifier
    : attribute_target ':'
    ;

attribute_target
    : 'field'
    | 'event'
    | 'method'
    | 'param'
    | 'property'
    | 'return'
    | 'type'
    ;

attribute_list
    : attribute (',' attribute)*
    ;

attribute
    : attribute_name attribute_arguments?
    ;

attribute_name
    : type_name
    ;

attribute_arguments
    : '(' positional_argument_list? ')'
    | '(' positional_argument_list ',' named_argument_list ')'
    | '(' named_argument_list ')'
    ;

positional_argument_list
    : positional_argument (',' positional_argument)*
    ;

positional_argument
    : attribute_argument_expression
    ;

named_argument_list
    : named_argument (','  named_argument)*
    ;

named_argument
    : identifier '=' attribute_argument_expression
    ;

attribute_argument_expression
    : expression
    ;
```

Un attribut se compose d’un *attribute_name* et une liste facultative d’arguments positionnels et nommés. Les arguments de position (le cas échéant) faites précéder les arguments nommés. Un argument positionnel se compose d’un *attribute_argument_expression*; un argument nommé se compose d’un nom, suivi par un signe égal, suivi par un *attribute_argument_expression*, qui, ensemble , sont limitées par les mêmes règles que l’assignation simple. L’ordre des arguments nommés n’est pas significatif.

Le *attribute_name* identifie une classe d’attributs. Si la forme de *attribute_name* est *type_name* ensuite ce nom doit faire référence à une classe d’attributs. Sinon, une erreur de compilation se produit. L’exemple

```csharp
class Class1 {}

[Class1] class Class2 {}    // Error
```

génère une erreur de compilation, car il tente d’utiliser `Class1` en tant qu’attribut de classe lorsque `Class1` n’est pas une classe d’attributs.

Certains contextes autorisent la spécification d’un attribut sur plusieurs cibles. Un programme peut spécifier explicitement la cible en incluant un *attribute_target_specifier*. Lorsqu’un attribut est placé au niveau global, un *global_attribute_target_specifier* est requis. Dans tous les autres emplacements, par défaut raisonnable est appliquée, mais un *attribute_target_specifier* peut être utilisé pour confirmer ou remplacer la valeur par défaut dans certains cas ambigus (ou pour simplement confirmer la valeur par défaut dans les cas non ambigus). Par conséquent, en règle générale, *attribute_target_specifier*s peut être omis, sauf au niveau global. Les contextes potentiellement ambigus sont résolus comme suit :

*  Un attribut spécifié avec une portée globale peut s’appliquer à l’assembly cible ou le module cible. Sans valeur par défaut existe pour ce contexte, par conséquent, un *attribute_target_specifier* est toujours requis dans ce contexte. La présence de la `assembly` *attribute_target_specifier* indique que l’attribut s’applique à la cible assembly ; la présence de la `module` *attribute_target_specifier* Indique que l’attribut s’applique au module cible.
*  Un attribut spécifié dans une déclaration delegate peut s’appliquer au délégué déclaré ou à sa valeur de retour. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique au délégué. La présence de la `type` *attribute_target_specifier* indique que l’attribut s’applique au délégué ; la présence de la `return` *attribute_target_specifier* Indique que l’attribut s’applique à la valeur de retour.
*  Un attribut spécifié dans une déclaration de méthode peut s’appliquer à la méthode déclarée ou à sa valeur de retour. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode. La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.
*  Un attribut spécifié dans une déclaration d’opérateur peut s’appliquer à l’opérateur déclaré ou à sa valeur de retour. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à l’opérateur. La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à l’opérateur ; la présence de la `return` *attribute_target_specifier* Indique que l’attribut s’applique à la valeur de retour.
*  Un attribut spécifié dans une déclaration event qui omet les accesseurs d’événement peut s’appliquent à l’événement déclaré, au champ associé (si l’événement n’est pas abstrait) ou pour l’ajouter associé et supprimer des méthodes. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à l’événement. La présence de la `event` *attribute_target_specifier* indique que l’attribut s’applique à l’événement ; la présence de la `field` *attribute_target_specifier* indique que l’attribut s’applique au champ ; et la présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique aux méthodes.
*  Un attribut spécifié dans une déclaration d’accesseur get pour une déclaration de propriété ou un indexeur peut s’appliquer à la méthode associée ou à sa valeur de retour. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode. La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.
*  Un attribut spécifié sur un accesseur set pour une déclaration de propriété ou l’indexeur peut s’appliquer à la méthode associée ou à son paramètre unique implicite. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode. La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `param` *attribute_target_specifier* indique que l’attribut s’applique au paramètre ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.
*  Un attribut spécifié dans une déclaration d’accesseur add ou remove pour une déclaration d’événement peut s’appliquent à la méthode associée ou à son paramètre unique. En l’absence d’un *attribute_target_specifier*, l’attribut s’applique à la méthode. La présence de la `method` *attribute_target_specifier* indique que l’attribut s’applique à la méthode ; la présence de la `param` *attribute_target_specifier* indique que l’attribut s’applique au paramètre ; la présence de la `return` *attribute_target_specifier* indique que l’attribut s’applique à la valeur de retour.

Dans d’autres contextes, l’inclusion d’un *attribute_target_specifier* est autorisée mais inutile. Par exemple, une déclaration de classe peut inclure ou omettre le spécificateur `type`:

```csharp
[type: Author("Brian Kernighan")]
class Class1 {}

[Author("Dennis Ritchie")]
class Class2 {}
```

C’est une erreur pour spécifier un non valide *attribute_target_specifier*. Par exemple, le spécificateur de `param` ne peut pas être utilisé sur une déclaration de classe :

```csharp
[param: Author("Brian Kernighan")]        // Error
class Class1 {}
```

Par convention, les classes d’attributs sont nommés avec un suffixe de `Attribute`. Un *attribute_name* du formulaire *type_name* peut inclure ou omettre ce suffixe. Si une classe d’attributs est trouvée avec et sans ce suffixe, une ambiguïté est présente, et une erreur de compilation se produit. Si le *attribute_name* est orthographié telles que son plus à droite *identificateur* est un identificateur de chaîne textuelle ([identificateurs](lexical-structure.md#identifiers)), puis uniquement un attribut sans suffixe est mis en correspondance, ce qui permet une ambiguïté de ce type à résoudre. L’exemple

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class X: Attribute
{}

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Error: ambiguity
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Refers to X
class Class3 {}

[@XAttribute]           // Refers to XAttribute
class Class4 {}
```

montre deux classes d’attributs appelées `X` et `XAttribute`. L’attribut `[X]` est ambigu, car elle peut désigner soit `X` ou `XAttribute`. À l’aide d’un identificateur textuel permet l’intention exacte doit être spécifié dans ce cas rares. L’attribut `[XAttribute]` n’est pas ambigu (bien qu’il serait si il y a une classe d’attributs nommée `XAttributeAttribute`!). Si la déclaration de classe `X` est supprimé, les deux attributs font référence à la classe d’attributs nommée `XAttribute`, comme suit :

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Refers to XAttribute
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Error: no attribute named "X"
class Class3 {}
```

Il s’agit d’une erreur de compilation à utiliser une classe d’attribut à usage unique plusieurs fois sur la même entité. L’exemple

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpStringAttribute: Attribute
{
    string value;

    public HelpStringAttribute(string value) {
        this.value = value;
    }

    public string Value {
        get {...}
    }
}

[HelpString("Description of Class1")]
[HelpString("Another description of Class1")]
public class Class1 {}
```

génère une erreur de compilation, car il tente d’utiliser `HelpString`, qui est une classe d’attribut à usage unique, plusieurs fois sur la déclaration de `Class1`.

Une expression `E` est un *attribute_argument_expression* si toutes les instructions suivantes sont remplies :

*  Le type de `E` est un type de paramètre d’attribut ([les types de paramètre d’attribut](attributes.md#attribute-parameter-types)).
*  Au moment de la compilation, la valeur de `E` peut être résolu à une des opérations suivantes :
   * Une valeur constante.
   * Objet `System.Type`.
   * Un tableau unidimensionnel de *attribute_argument_expression*s.

Exemple :

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class TestAttribute: Attribute
{
    public int P1 {
        get {...}
        set {...}
    }

    public Type P2 {
        get {...}
        set {...}
    }

    public object P3 {
        get {...}
        set {...}
    }
}

[Test(P1 = 1234, P3 = new int[] {1, 3, 5}, P2 = typeof(float))]
class MyClass {}
```

Un *typeof_expression* ([l’opérateur typeof](expressions.md#the-typeof-operator)) utilisée comme une expression d’argument attribut peut référencer un type non générique, un type construit fermé ou un type générique indépendant, mais il ne peut pas référencer un Ouvrez le type. Il s’agit pour vous assurer que l’expression peut être résolue au moment de la compilation.

```csharp
class A: Attribute
{
    public A(Type t) {...}
}

class G<T>
{
    [A(typeof(T))] T t;                  // Error, open type in attribute
}

class X
{
    [A(typeof(List<int>))] int x;        // Ok, closed constructed type
    [A(typeof(List<>))] int y;           // Ok, unbound generic type
}
```

## <a name="attribute-instances"></a>Instances d’attribut

Un ***instance de l’attribut*** est une instance qui représente un attribut au moment de l’exécution. Un attribut est défini avec une classe d’attributs, des arguments positionnels et des arguments nommé. Une instance d’attribut est une instance de la classe d’attributs qui est initialisée avec les arguments nommés et positionnels.

Récupération d’une instance d’attribut implique un traitement de compilation et d’exécution, comme décrit dans les sections suivantes.

### <a name="compilation-of-an-attribute"></a>Compilation d’un attribut

La compilation d’un *attribut* avec la classe d’attributs `T`, *positional_argument_list* `P` et *named_argument_list* `N`, se compose des étapes suivantes :

*  Suivez les étapes de traitement de compilation pour compiler une *object_creation_expression* du formulaire `new T(P)`. Ces étapes entraînent une erreur de compilation, ou déterminer un constructeur d’instance `C` sur `T` qui peut être appelé au moment de l’exécution.
*  Si `C` n’a pas d’une accessibilité publique, une erreur de compilation se produit.
*  Pour chaque *argument_nommé* `Arg` dans `N`:
   * Laisser `Name` être le *identificateur* de la *argument_nommé* `Arg`.
   * `Name` doit identifier un champ de public en lecture-écriture non statique ou une propriété sur `T`. Si `T` a pas de champ ou une propriété, une erreur de compilation se produit.
*  Gardez les informations suivantes pour l’instanciation de l’exécution de l’attribut : la classe d’attributs `T`, le constructeur d’instance `C` sur `T`, le *positional_argument_list* `P` et le *named_argument_list* `N`.

### <a name="run-time-retrieval-of-an-attribute-instance"></a>Récupération de l’exécution d’une instance d’attribut

Compilation d’un *attribut* génère une classe d’attributs `T`, un constructeur d’instance `C` sur `T`, un *positional_argument_list* `P`et un *named_argument_list* `N`. Par conséquent, une instance d’attribut peut être récupérée au moment de l’exécution en procédant comme suit :

*  Suivez les étapes de traitement de l’exécution pour exécuter un *object_creation_expression* du formulaire `new T(P)`, en utilisant le constructeur d’instance `C` comme déterminé au moment de la compilation. Ces étapes génèrent une exception, ou produisent une instance `O` de `T`.
*  Pour chaque *argument_nommé* `Arg` dans `N`, dans l’ordre :
   * Laisser `Name` être le *identificateur* de la *argument_nommé* `Arg`. Si `Name` n’identifie pas un champ de lecture-écriture non statique publique ou une propriété sur `O`, une exception est levée.
   * Laisser `Value` être le résultat de l’évaluation de la *attribute_argument_expression* de `Arg`.
   * Si `Name` identifie un champ sur `O`, puis définissez ce champ sur `Value`.
   * Sinon, `Name` identifie une propriété sur `O`. Définissez cette propriété sur `Value`.
   * Le résultat est `O`, une instance de la classe d’attributs `T` qui a été initialisée avec le *positional_argument_list* `P` et *named_argument_list* `N`.

## <a name="reserved-attributes"></a>Attributs réservés

Un petit nombre d’attributs affecte le langage d’une certaine façon. Ces attributs sont les suivantes :

*  `System.AttributeUsageAttribute` ([Attribut AttributeUsage le](attributes.md#the-attributeusage-attribute)), qui est utilisé pour décrire les méthodes dans lesquels une classe d’attributs peut être utilisée.
*  `System.Diagnostics.ConditionalAttribute` ([L’attribut Conditional](attributes.md#the-conditional-attribute)), qui est utilisée pour définir des méthodes conditionnelles.
*  `System.ObsoleteAttribute` ([Attribut obsolète le](attributes.md#the-obsolete-attribute)), qui est utilisé pour marquer un membre comme étant obsolète.
*  `System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` et `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([attributs d’informations appelant](attributes.md#caller-info-attributes)), qui sont utilisées pour fournir des informations sur le contexte d’appel aux paramètres facultatifs.

### <a name="the-attributeusage-attribute"></a>L’attribut AttributeUsage

L’attribut `AttributeUsage` est utilisé pour décrire le mode dans lequel la classe d’attributs peut être utilisée.

Une classe est décorée avec le `AttributeUsage` attribut doit dériver de `System.Attribute`, directement ou indirectement. Sinon, une erreur de compilation se produit.

```csharp
namespace System
{
    [AttributeUsage(AttributeTargets.Class)]
    public class AttributeUsageAttribute: Attribute
    {
        public AttributeUsageAttribute(AttributeTargets validOn) {...}
        public virtual bool AllowMultiple { get {...} set {...} }
        public virtual bool Inherited { get {...} set {...} }
        public virtual AttributeTargets ValidOn { get {...} }
    }

    public enum AttributeTargets
    {
        Assembly     = 0x0001,
        Module       = 0x0002,
        Class        = 0x0004,
        Struct       = 0x0008,
        Enum         = 0x0010,
        Constructor  = 0x0020,
        Method       = 0x0040,
        Property     = 0x0080,
        Field        = 0x0100,
        Event        = 0x0200,
        Interface    = 0x0400,
        Parameter    = 0x0800,
        Delegate     = 0x1000,
        ReturnValue  = 0x2000,

        All = Assembly | Module | Class | Struct | Enum | Constructor | 
            Method | Property | Field | Event | Interface | Parameter | 
            Delegate | ReturnValue
    }
}
```

### <a name="the-conditional-attribute"></a>L’attribut Conditional

L’attribut `Conditional` permet la définition de ***des méthodes conditionnelles*** et ***classes d’attributs conditionnel***.

```csharp
namespace System.Diagnostics
{
    [AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, AllowMultiple = true)]
    public class ConditionalAttribute: Attribute
    {
        public ConditionalAttribute(string conditionString) {...}
        public string ConditionString { get {...} }
    }
}
```

#### <a name="conditional-methods"></a>Méthodes conditionnelles

Une méthode décorée avec le `Conditional` attribut est une méthode conditionnelle. Le `Conditional` attribut indique une condition en testant un symbole de compilation conditionnelle. Les appels à une méthode conditionnelle sont inclus ou omis selon que ce symbole est défini au moment de l’appel. Si le symbole est défini, l’appel est inclus ; Sinon, l’appel (y compris l’évaluation du récepteur et les paramètres de l’appel) est omis.

Une méthode conditionnelle est soumise aux restrictions suivantes :

*  La méthode conditionnelle doit être une méthode dans un *class_declaration* ou *struct_declaration*. Une erreur de compilation se produit si le `Conditional` attribut est spécifié sur une méthode dans une déclaration d’interface.
*  La méthode conditionnelle doit avoir un type de retour `void`.
*  La méthode conditionnelle ne doit pas être marquée avec le `override` modificateur. Une méthode conditionnelle peut être marquée avec le `virtual` modificateur, toutefois. Remplacements d’une telle méthode sont implicitement conditionnelles et ne doivent pas être marqués explicitement avec une `Conditional` attribut.
*  La méthode conditionnelle ne doit pas être une implémentation d’une méthode d’interface. Sinon, une erreur de compilation se produit.

En outre, une erreur de compilation se produit si une méthode conditionnelle est utilisée dans un *delegate_creation_expression*. L’exemple

```csharp
#define DEBUG

using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void M() {
        Console.WriteLine("Executed Class1.M");
    }
}

class Class2
{
    public static void Test() {
        Class1.M();
    }
}
```

déclare `Class1.M` comme une méthode conditionnelle. `Class2`de `Test` méthode appelle cette méthode. Depuis le symbole de compilation conditionnelle `DEBUG` est défini, si `Class2.Test` est appelé, il appellera `M`. Si le symbole `DEBUG` n’était pas défini, puis `Class2.Test` n’appelez pas `Class1.M`.

Il est important de noter que l’inclusion ou exclusion d’un appel à une méthode conditionnelle est contrôlée par les symboles de compilation conditionnelle au moment de l’appel. Dans l’exemple

Fichier `class1.cs`:

```csharp
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void F() {
        Console.WriteLine("Executed Class1.F");
    }
}
```

Fichier `class2.cs`:

```csharp
#define DEBUG

class Class2
{
    public static void G() {
        Class1.F();                // F is called
    }
}
```

Fichier `class3.cs`:

```csharp
#undef DEBUG

class Class3
{
    public static void H() {
        Class1.F();                // F is not called
    }
}
```

les classes `Class2` et `Class3` contiennent chacune des appels à la méthode conditionnelle `Class1.F`, qui est conditionnelle selon s’il faut ou non `DEBUG` est défini. Dans la mesure où ce symbole est défini dans le contexte de `Class2` mais pas `Class3`, l’appel à `F` dans `Class2` est inclus, tandis que l’appel à `F` dans `Class3` est omis.

L’utilisation des méthodes conditionnelles dans une chaîne d’héritage peut prêter à confuse. Appels à une méthode conditionnelle via `base`, sous la forme `base.M`, sont soumis aux règles d’appel de méthode conditionnelle normale. Dans l’exemple

Fichier `class1.cs`:

```csharp
using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public virtual void M() {
        Console.WriteLine("Class1.M executed");
    }
}
```

Fichier `class2.cs`:

```csharp
using System;

class Class2: Class1
{
    public override void M() {
        Console.WriteLine("Class2.M executed");
        base.M();                        // base.M is not called!
    }
}
```

Fichier `class3.cs`:

```csharp
#define DEBUG

using System;

class Class3
{
    public static void Test() {
        Class2 c = new Class2();
        c.M();                            // M is called
    }
}
```

`Class2` inclut un appel à la `M` défini dans sa classe de base. Cet appel est omis, car la méthode de base est conditionnelle basée sur la présence du symbole `DEBUG`, ce qui n’est pas défini. Par conséquent, la méthode écrit dans la console «`Class2.M executed`» uniquement. Utilisation judicieuse de *pp_declaration*s peut éliminer ces problèmes.

#### <a name="conditional-attribute-classes"></a>Classes de l’attribut Conditional

Une classe d’attributs ([classes d’attributs](attributes.md#attribute-classes)) décoré avec un ou plusieurs `Conditional` attributs est un ***classe de l’attribut conditional***. Une classe de l’attribut conditional est ainsi associée avec les symboles de compilation conditionnelle déclarés dans son `Conditional` attributs. Cet exemple :

```csharp
using System;
using System.Diagnostics;
[Conditional("ALPHA")]
[Conditional("BETA")]
public class TestAttribute : Attribute {}
```

déclare `TestAttribute` comme une classe d’attribut conditional associée les symboles de compilations conditionnelles `ALPHA` et `BETA`.

Spécifications d’attribut ([spécification des attributs](attributes.md#attribute-specification)) d’un attribut conditionnel sont incluses si un ou plusieurs de ses symboles de compilation conditionnelle associé sont défini au moment de la spécification, sinon l’attribut spécification est omise.

Il est important de noter que l’inclusion ou l’exclusion d’une spécification d’attribut d’une classe de l’attribut conditional est contrôlée par les symboles de compilation conditionnelle au moment de la spécification. Dans l’exemple

Fichier `test.cs`:

```csharp
using System;
using System.Diagnostics;

[Conditional("DEBUG")]

public class TestAttribute : Attribute {}
```

Fichier `class1.cs`:

```csharp
#define DEBUG

[Test]                // TestAttribute is specified

class Class1 {}
```

Fichier `class2.cs`:

```csharp
#undef DEBUG

[Test]                 // TestAttribute is not specified

class Class2 {}
```

les classes `Class1` et `Class2` sont chacun décorée avec l’attribut `Test`, qui est conditionnelle selon s’il faut ou non `DEBUG` est défini. Dans la mesure où ce symbole est défini dans le contexte de `Class1` mais pas `Class2`, la spécification de la `Test` attribut sur `Class1` est inclus lors de la spécification de la `Test` attribut sur `Class2` est omis.

### <a name="the-obsolete-attribute"></a>L’attribut Obsolete

L’attribut `Obsolete` est utilisé pour marquer des types et membres de types qui ne doivent plus être utilisés.

```csharp
namespace System
{
    [AttributeUsage(
        AttributeTargets.Class | 
        AttributeTargets.Struct |
        AttributeTargets.Enum | 
        AttributeTargets.Interface | 
        AttributeTargets.Delegate |
        AttributeTargets.Method | 
        AttributeTargets.Constructor |
        AttributeTargets.Property | 
        AttributeTargets.Field |
        AttributeTargets.Event,
        Inherited = false)
    ]
    public class ObsoleteAttribute: Attribute
    {
        public ObsoleteAttribute() {...}
        public ObsoleteAttribute(string message) {...}
        public ObsoleteAttribute(string message, bool error) {...}
        public string Message { get {...} }
        public bool IsError { get {...} }
    }
}
```

Si un programme utilise un type ou membre qui est décorée avec le `Obsolete` attribut, le compilateur émet un avertissement ou une erreur. Plus précisément, le compilateur émet un avertissement si aucun paramètre d’erreur n’est fournie, ou si le paramètre d’erreur est fourni et qu’il a la valeur `false`. Le compilateur émet une erreur si le paramètre d’erreur est spécifié et a la valeur `true`.

Dans l’exemple

```csharp
[Obsolete("This class is obsolete; use class B instead")]
class A
{
    public void F() {}
}

class B
{
    public void F() {}
}

class Test
{
    static void Main() {
        A a = new A();         // Warning
        a.F();
    }
}
```

la classe `A` est décorée avec le `Obsolete` attribut. L’utilisation de `A` dans `Main` entraîne un avertissement qui inclut le message spécifié, « cette classe est obsolète ; Utilisez la classe B à la place. »

### <a name="caller-info-attributes"></a>Attributs d’informations de l’appelant

À des fins telles que la journalisation et création de rapports, il est parfois utile pour un membre de fonction obtenir certaines informations de compilation sur le code appelant. Les attributs d’informations appelant fournissent un moyen de passer ces informations en toute transparence.

Lorsqu’un paramètre facultatif est annoté avec l’un des attributs d’informations de l’appelant, en omettant l’argument correspondant dans un appel ne provoquant pas forcément la valeur du paramètre par défaut à substituer. Au lieu de cela, si l’information spécifiée concernant le contexte d’appel est disponible, ces informations seront transmies en tant que la valeur d’argument.

Exemple :

```csharp
using System.Runtime.CompilerServices

...

public void Log(
    [CallerLineNumber] int line = -1,
    [CallerFilePath]   string path = null,
    [CallerMemberName] string name = null
)
{
    Console.WriteLine((line < 0) ? "No line" : "Line "+ line);
    Console.WriteLine((path == null) ? "No file path" : path);
    Console.WriteLine((name == null) ? "No member name" : name);
}
```

Un appel à `Log()` sans arguments affiche le chemin de nombre et le fichier de ligne de l’appel, ainsi que le nom du membre dans lequel l’appel s’est produite.

Attributs d’informations de l’appelant peuvent se produire sur n’importe où, les paramètres facultatifs, y compris dans les déclarations de délégué. Toutefois, les attributs d’informations appelant spécifique ont les restrictions sur les types des paramètres, qu'ils peuvent l’attribut, afin qu’il y aura toujours une conversion implicite d’une valeur substituée au type de paramètre.

Il est une erreur d’avoir le même attribut d’informations de l’appelant sur un paramètre de la définition et de mise en œuvre de la partie d’une déclaration de méthode partielle. Uniquement les attributs d’informations de l’appelant dans la partie de définition sont appliquées, tandis que les attributs des informations de l’appelant qui se produisent uniquement dans la partie implémentation sont ignorés.

Informations de l’appelant n’affectent pas la résolution de surcharge. Comme les paramètres facultatifs avec attributs sont toujours omises dans le code source de l’appelant, la résolution de surcharge ignore ces paramètres de la même façon qu’il ignore les autres paramètres facultatifs omis ([la résolution de surcharge](expressions.md#overload-resolution)).

Informations de l’appelant sont remplacées uniquement lorsqu’une fonction est appelée explicitement dans le code source. Appels implicites tels que les appels de constructeur implicite parent n’ont pas d’un emplacement source et ne remplacera pas les informations de l’appelant. En outre, les appels qui sont liées dynamiquement remplacera pas les informations de l’appelant. Quand une info appelant paramètre avec attributs est omis dans ce cas, la valeur du paramètre est utilisée à la place.

Seule exception concerne les expressions de requête. Celles-ci sont considérées comme des expansions syntaxiques, et si les appels qui se développent pour omettre les paramètres facultatifs avec attributs d’informations de l’appelant, les informations de l’appelant seront remplacées. L’emplacement utilisé est l’emplacement de la clause de requête généré à partir de l’appel.

Si plusieurs attributs d’informations sur l’appelant est spécifié sur un paramètre donné, ils sont préférables dans l’ordre suivant : `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.

#### <a name="the-callerlinenumber-attribute"></a>L’attribut CallerLineNumber

Le `System.Runtime.CompilerServices.CallerLineNumberAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de la valeur de constante `int.MaxValue` pour le type de paramètre. Cela garantit que tout numéro de ligne de non négatif jusqu'à cette valeur peut être passé sans erreur.

Si un appel de fonction à partir d’un emplacement dans le code source omet un paramètre facultatif avec la `CallerLineNumberAttribute`, un littéral numérique représentant le numéro de ligne de cet emplacement est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.

Si l’appel s’étend sur plusieurs lignes, la ligne choisie est dépend de l’implémentation.

Notez que le numéro de ligne peut être affecté par `#line` directives ([directives de ligne](lexical-structure.md#line-directives)).

#### <a name="the-callerfilepath-attribute"></a>L’attribut CallerFilePath

Le `System.Runtime.CompilerServices.CallerFilePathAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de `string` pour le type de paramètre.

Si un appel de fonction à partir d’un emplacement dans le code source omet un paramètre facultatif avec la `CallerFilePathAttribute`, un littéral de chaîne représentant le chemin d’accès de cet emplacement fichier est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.

Le format du chemin du fichier est dépend de l’implémentation.

Notez que le chemin d’accès de fichier peut être affectée par `#line` directives ([directives de ligne](lexical-structure.md#line-directives)).

#### <a name="the-callermembername-attribute"></a>L’attribut CallerMemberName

Le `System.Runtime.CompilerServices.CallerMemberNameAttribute` n’est autorisée sur les paramètres facultatifs quand il existe une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) à partir de `string` pour le type de paramètre.

Si un appel de fonction à partir d’un emplacement dans le corps d’une fonction membre ou au sein d’un attribut appliqué à la fonction membre lui-même ou son type de retour, paramètres ou des paramètres de type dans le code source omet un paramètre facultatif avec la `CallerMemberNameAttribute`, puis un littéral de chaîne représentant le nom de ce membre est utilisé en tant qu’argument à l’appel au lieu de la valeur du paramètre par défaut.

Pour les appels qui se produisent dans les méthodes génériques, uniquement le nom de méthode est utilisé, sans la liste de paramètres de type.

Pour les appels qui se produisent dans les implémentations de membres d’interface explicite, uniquement le nom de méthode est utilisé, sans la qualification d’interface précédent.

Pour les appels qui se produisent dans les accesseurs de propriété ou un événement, le nom de membre utilisé est celui de la propriété ou l’événement lui-même.

Pour les appels qui se produisent dans les accesseurs des indexeurs, le nom de membre utilisé est celui fourni par un `IndexerNameAttribute` ([l’attribut IndexerName](attributes.md#the-indexername-attribute)) sur le membre d’indexeur, le cas échéant, ou le nom par défaut `Item` dans le cas contraire.

Pour les appels qui se produisent dans les déclarations de constructeurs d’instance, des constructeurs statiques, des destructeurs et des opérateurs du membre nom utilisé est dépend de l’implémentation.

## <a name="attributes-for-interoperation"></a>Attributs d’interopérabilité

Remarque : Cette section s’applique uniquement à l’implémentation de Microsoft .NET de C#.

### <a name="interoperation-with-com-and-win32-components"></a>Interopérabilité avec les composants COM et Win32

La durée d’exécution .NET fournit un grand nombre d’attributs qui permettent aux programmes c# interagir avec les composants écrits à l’aide de COM et les DLL Win32. Par exemple, le `DllImport` attribut peut être utilisé sur un `static extern` méthode pour indiquer que l’implémentation de la méthode doit être trouvé dans une DLL Win32. Ces attributs sont trouvent dans le `System.Runtime.InteropServices` espace de noms et une documentation détaillée pour ces attributs se trouve dans la documentation du runtime .NET.

### <a name="interoperation-with-other-net-languages"></a>Interopérabilité avec d’autres langages .NET

#### <a name="the-indexername-attribute"></a>L’attribut IndexerName

Les indexeurs sont implémentés dans .NET à l’aide de propriétés indexées et ont un nom dans les métadonnées de .NET. Si aucun `IndexerName` attribut n’est présent pour un indexeur, puis le nom `Item` est utilisé par défaut. Le `IndexerName` attribut permet à un développeur remplacer cette valeur par défaut et spécifiez un autre nom.

```csharp
namespace System.Runtime.CompilerServices.CSharp
{
    [AttributeUsage(AttributeTargets.Property)]
    public class IndexerNameAttribute: Attribute
    {
        public IndexerNameAttribute(string indexerName) {...}
        public string Value { get {...} } 
    }
}
```
