---
ms.openlocfilehash: ff31585520c9090ad92893a930327112743c8e77
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704005"
---
# <a name="basic-concepts"></a>Concepts de base

## <a name="application-startup"></a>Démarrage de l'application

Un assembly qui a un ***point d’entrée*** est appelé ***application***. Lorsqu’une application est exécutée, un nouveau ***domaine d’application*** est créé. Plusieurs instanciations différentes d’une application peuvent exister sur le même ordinateur en même temps et chacune possède son propre domaine d’application.

Un domaine d’application permet l’isolation d’applications en jouant le rôle d’un conteneur pour l’état de l’application. Un domaine d’application agit comme un conteneur et une limite pour les types définis dans l’application et les bibliothèques de classes qu’il utilise. Les types chargés dans un domaine d’application sont différents du même type chargé dans un autre domaine d’application, et les instances d’objets ne sont pas directement partagées entre les domaines d’application. Par exemple, chaque domaine d’application possède sa propre copie de variables statiques pour ces types, et un constructeur statique pour un type est exécuté au plus une fois par domaine d’application. Les implémentations sont gratuites pour fournir une stratégie ou des mécanismes spécifiques à l’implémentation pour la création et la destruction de domaines d’application.

Le démarrage de l' ***application*** se produit lorsque l’environnement d’exécution appelle une méthode désignée, appelée point d’entrée de l’application. Cette méthode de point d’entrée est toujours nommée `Main` et peut avoir l’une des signatures suivantes :

```csharp
static void Main() {...}

static void Main(string[] args) {...}

static int Main() {...}

static int Main(string[] args) {...}
```

Comme indiqué, le point d’entrée peut éventuellement retourner une valeur `int`. Cette valeur de retour est utilisée dans l’arrêt de l’application (arrêt de l'[application](basic-concepts.md#application-termination)).

Le point d’entrée peut éventuellement avoir un paramètre formel. Le paramètre peut avoir n’importe quel nom, mais le type du paramètre doit être `string[]`. Si le paramètre formel est présent, l’environnement d’exécution crée et passe un argument `string[]` contenant les arguments de ligne de commande spécifiés au démarrage de l’application. L’argument `string[]` n’est jamais null, mais peut avoir une longueur de zéro si aucun argument de ligne de commande n’a été spécifié.

Étant C# donné que prend en charge la surcharge de méthode, une classe ou un struct peut contenir plusieurs définitions d’une méthode, à condition que chacune ait une signature différente. Toutefois, au sein d’un même programme, aucune classe ou aucun struct ne peut contenir plusieurs méthodes appelées `Main` dont la définition la qualifie à utiliser comme point d’entrée de l’application. Toutefois, d’autres versions surchargées de `Main` sont autorisées, à condition qu’elles aient plusieurs paramètres, ou que leur seul paramètre soit autre que le type `string[]`.

Une application peut être composée de plusieurs classes ou structures. Il est possible que plus d’une de ces classes ou structures contiennent une méthode appelée `Main` dont la définition la qualifie à utiliser comme point d’entrée de l’application. Dans ce cas, un mécanisme externe (tel qu’une option du compilateur de ligne de commande) doit être utilisé pour sélectionner l’une de ces méthodes `Main` comme point d’entrée.

Dans C#, chaque méthode doit être définie en tant que membre d’une classe ou d’un struct. En règle générale, l’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) d’une méthode est déterminée par les modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) spécifiés dans sa déclaration, et de la même façon, l’accessibilité déclarée d’un type est déterminée par le modificateurs d’accès spécifiés dans sa déclaration. Pour qu’une méthode donnée d’un type donné puisse être appelée, le type et le membre doivent être accessibles. Toutefois, le point d’entrée de l’application est un cas particulier. Plus précisément, l’environnement d’exécution peut accéder au point d’entrée de l’application, quelle que soit l’accessibilité déclarée et indépendamment de l’accessibilité déclarée de ses déclarations de type englobant.

La méthode de point d’entrée de l’application ne peut pas figurer dans une déclaration de classe générique.

Dans tous les autres aspects, les méthodes de point d’entrée se comportent comme celles qui ne sont pas des points d’entrée.

## <a name="application-termination"></a>Arrêt de l’application

L’arrêt de l' ***application*** retourne le contrôle à l’environnement d’exécution.

Si le type de retour de la méthode de ***point d’entrée*** de l’application est `int`, la valeur retournée fait office de code d’état d' ***arrêt***de l’application. L’objectif de ce code est de permettre la communication de la réussite ou de l’échec à l’environnement d’exécution.

Si le type de retour de la méthode de point d’entrée est `void`, l’atteinte de l’accolade fermante (`}`) qui termine cette méthode, ou l’exécution d’une instruction `return` qui n’a aucune expression, aboutit à un code d’état d’arrêt de `0`.

Avant l’arrêt d’une application, les destructeurs de tous ses objets qui n’ont pas encore été récupérés par le garbage collector sont appelés, à moins que ce nettoyage ait été supprimé (par un appel à la méthode de la bibliothèque `GC.SuppressFinalize`, par exemple).

## <a name="declarations"></a>Déclarations

Les déclarations d' C# un programme définissent les éléments constitutifs du programme. C#les programmes sont organisés à l’aide d’espaces de noms ([espaces de noms](namespaces.md)), qui peuvent contenir des déclarations de type et des déclarations d’espaces de noms imbriqués. Les déclarations de type ([déclarations de type](namespaces.md#type-declarations)) sont utilisées pour définir des classes ([classes](classes.md)), des structures ([structs](structs.md)), des interfaces ([interfaces](interfaces.md)), des enums ([énumérations](enums.md)) et des délégués ([délégués](delegates.md)). Les genres de membres autorisés dans une déclaration de type dépendent de la forme de la déclaration de type. Par exemple, les déclarations de classe peuvent contenir des déclarations pour les constantes ([constantes](classes.md#constants)), les champs ([champs](classes.md#fields)), les méthodes ([méthodes](classes.md#methods)), les propriétés ([Propriétés](classes.md#properties)), les événements ([événements](classes.md#events)), les indexeurs ([indexeurs](classes.md#indexers)), opérateurs ([opérateurs](classes.md#operators)), constructeurs d’instance ([constructeurs d’instance](classes.md#instance-constructors)), constructeurs statiques ([constructeurs statiques](classes.md#static-constructors)), destructeurs ([destructeurs](classes.md#destructors)) et types imbriqués ([types imbriqués](classes.md#nested-types)).

Une déclaration définit un nom dans l' ***espace de déclaration*** auquel la déclaration appartient. À l’exception des membres surchargés ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)), il s’agit d’une erreur au moment de la compilation d’avoir au moins deux déclarations qui introduisent des membres portant le même nom dans un espace de déclaration. Il n’est jamais possible pour un espace de déclaration de contenir différents genres de membres portant le même nom. Par exemple, un espace de déclaration ne peut jamais contenir un champ et une méthode du même nom.

Il existe différents types d’espaces de déclaration, comme décrit dans l’exemple suivant.

*  Dans tous les fichiers sources d’un programme, *namespace_member_declaration*sans *namespace_declaration* englobant sont membres d’un espace de déclaration combiné unique, appelé ***espace de déclaration global***.
*  Dans tous les fichiers sources d’un programme, les *namespace_member_declaration*dans *namespace_declaration*s qui ont le même nom d’espace de noms complet sont membres d’un espace de déclaration combiné unique.
*  Chaque déclaration de classe, de struct ou d’interface crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration via *class_member_declaration*s, *struct_member_declaration*s, *interface_member_declaration*s ou *type_parameter*s. Sauf pour les déclarations de constructeur d’instance surchargé et les déclarations de constructeur statiques, une classe ou un struct ne peut pas contenir une déclaration de membre portant le même nom que la classe ou le struct. Une classe, un struct ou une interface autorise la déclaration de méthodes surchargées et d’indexeurs. En outre, une classe ou un struct autorise la déclaration de constructeurs et d’opérateurs d’instance surchargés. Par exemple, une classe, un struct ou une interface peut contenir plusieurs déclarations de méthode portant le même nom, à condition que ces déclarations de méthode diffèrent dans leur signature ([signatures et surcharge](basic-concepts.md#signatures-and-overloading)). Notez que les classes de base ne contribuent pas à l’espace de déclaration d’une classe et que les interfaces de base ne contribuent pas à l’espace de déclaration d’une interface. Ainsi, une classe ou une interface dérivée est autorisée à déclarer un membre portant le même nom qu’un membre hérité. Ce membre est dit de ***Masquer*** le membre hérité.
*  Chaque déclaration Delegate crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration via des paramètres formels (*fixed_parameter*s et *parameter_array*s) et *type_parameter*s.
*  Chaque déclaration d’énumération crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration par le biais de *enum_member_declarations*.
*  Chaque déclaration de méthode, déclaration d’indexeur, déclaration d’opérateur, déclaration de constructeur d’instance et fonction anonyme crée un espace de déclaration appelé ***espace de déclaration de variable locale***. Les noms sont introduits dans cet espace de déclaration via des paramètres formels (*fixed_parameter*s et *parameter_array*s) et *type_parameter*s. Le corps de la fonction membre ou de la fonction anonyme, le cas échéant, est considéré comme imbriqué dans l’espace de déclaration de la variable locale. Il s’agit d’une erreur pour un espace de déclaration de variable locale et un espace de déclaration de variable locale imbriqué pour contenir des éléments portant le même nom. Ainsi, dans un espace de déclaration imbriqué, il n’est pas possible de déclarer une variable locale ou une constante portant le même nom qu’une variable locale ou une constante dans un espace de déclaration englobant. Il est possible que deux espaces de déclaration contiennent des éléments portant le même nom, à condition que l’espace de déclaration ne contienne pas l’autre.
*  Chaque *bloc* ou *switch_block* , ainsi qu’une instruction *for*, *foreach* et *using* , crée un espace de déclaration de variable locale pour les variables locales et les constantes locales. Les noms sont introduits dans cet espace de déclaration par le biais de *local_variable_declaration*s et *local_constant_declaration*s. Notez que les blocs qui se produisent en tant que ou dans le corps d’un membre de fonction ou d’une fonction anonyme sont imbriqués dans l’espace de déclaration de variable locale déclaré par ces fonctions pour leurs paramètres. Par conséquent, il s’agit d’une erreur d’avoir une méthode avec une variable locale et un paramètre du même nom.
*  Chaque *bloc* ou *switch_block* crée un espace de déclaration distinct pour les étiquettes. Les noms sont introduits dans cet espace de déclaration par le biais de *labeled_statement*s et les noms sont référencés par le biais de *goto_statement*s. L' ***espace de déclaration d’étiquette*** d’un bloc comprend tous les blocs imbriqués. Ainsi, dans un bloc imbriqué, il n’est pas possible de déclarer une étiquette portant le même nom qu’une étiquette dans un bloc englobant.

L’ordre textuel dans lequel les noms sont déclarés n’a généralement pas d’importance. En particulier, l’ordre textuel n’est pas significatif pour la déclaration et l’utilisation des espaces de noms, des constantes, des méthodes, des propriétés, des événements, des indexeurs, des opérateurs, des constructeurs d’instance, des destructeurs, des constructeurs statiques et des types. L’ordre de déclaration est significatif des manières suivantes :

*  L’ordre de déclaration pour les déclarations de champ et les déclarations de variable locale détermine l’ordre dans lequel leurs initialiseurs (le cas échéant) sont exécutés.
*  Les variables locales doivent être définies avant d’être utilisées ([étendues](basic-concepts.md#scopes)).
*  L’ordre de déclaration pour les déclarations de membre enum ([membres enum](enums.md#enum-members)) est significatif lorsque les valeurs *constant_expression* sont omises.

L’espace de déclaration d’un espace de noms est « Open Closed » et deux déclarations d’espaces de noms portant le même nom qualifié complet participent au même espace de déclaration. Exemple :
```csharp
namespace Megacorp.Data
{
    class Customer
    {
        ...
    }
}

namespace Megacorp.Data
{
    class Order
    {
        ...
    }
}
```

Les deux déclarations d’espaces de noms ci-dessus contribuent au même espace de déclaration, dans ce cas, en déclarant deux classes avec les noms qualifiés complets `Megacorp.Data.Customer` et `Megacorp.Data.Order`. Étant donné que les deux déclarations contribuent au même espace de déclaration, elles auraient provoqué une erreur au moment de la compilation si chacune contient une déclaration d’une classe portant le même nom.

Comme indiqué ci-dessus, l’espace de déclaration d’un bloc comprend tous les blocs imbriqués. Ainsi, dans l’exemple suivant, les méthodes `F` et `G` provoquent une erreur au moment de la compilation, car le nom `i` est déclaré dans le bloc externe et ne peut pas être redéclaré dans le bloc interne. Toutefois, les méthodes `H` et `I` sont valides, car les deux `i` sont déclarées dans des blocs non imbriqués distincts.

```csharp
class A
{
    void F() {
        int i = 0;
        if (true) {
            int i = 1;            
        }
    }

    void G() {
        if (true) {
            int i = 0;
        }
        int i = 1;                
    }

    void H() {
        if (true) {
            int i = 0;
        }
        if (true) {
            int i = 1;
        }
    }

    void I() {
        for (int i = 0; i < 10; i++)
            H();
        for (int i = 0; i < 10; i++)
            H();
    }
}
```

## <a name="members"></a>Membres

Les espaces de noms et les types ont des ***membres***. Les membres d’une entité sont généralement disponibles à l’aide d’un nom qualifié qui commence par une référence à l’entité, suivi d’un jeton « `.` », suivi du nom du membre.

Les membres d’un type sont déclarés dans la déclaration de type ou ***hérités*** de la classe de base du type. Lorsqu’un type hérite d’une classe de base, tous les membres de la classe de base, à l’exception des constructeurs d’instance, des destructeurs et des constructeurs statiques, deviennent membres du type dérivé. L’accessibilité déclarée d’un membre de classe de base ne contrôle pas si le membre est hérité ; l’héritage s’étend à tout membre qui n’est pas un constructeur d’instance, un constructeur statique ou un destructeur. Toutefois, un membre hérité peut ne pas être accessible dans un type dérivé, soit en raison de son accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)), soit parce qu’il est masqué par une déclaration dans le type lui-même ([masquage par héritage](basic-concepts.md#hiding-through-inheritance)).

### <a name="namespace-members"></a>Membres d’espace de noms

Les espaces de noms et les types qui n’ont pas d’espace de noms englobant sont membres de l' ***espace de noms global***. Cela correspond directement aux noms déclarés dans l’espace de déclaration globale.

Les espaces de noms et les types déclarés dans un espace de noms sont membres de cet espace de noms. Cela correspond directement aux noms déclarés dans l’espace de déclaration de l’espace de noms.

Les espaces de noms ne présentent aucune limitation d’accès. Il n’est pas possible de déclarer des espaces de noms privés, protégés ou internes, et les noms d’espaces de noms sont toujours accessibles publiquement.

### <a name="struct-members"></a>Membres de struct

Les membres d’un struct sont les membres déclarés dans le struct et les membres hérités de la classe de base directe du struct `System.ValueType` et la classe de base indirecte `object`.

Les membres d’un type simple correspondent directement aux membres du type struct dont le type est simple :

*  Les membres de `sbyte` sont les membres de la structure `System.SByte`.
*  Les membres de `byte` sont les membres de la structure `System.Byte`.
*  Les membres de `short` sont les membres de la structure `System.Int16`.
*  Les membres de `ushort` sont les membres de la structure `System.UInt16`.
*  Les membres de `int` sont les membres de la structure `System.Int32`.
*  Les membres de `uint` sont les membres de la structure `System.UInt32`.
*  Les membres de `long` sont les membres de la structure `System.Int64`.
*  Les membres de `ulong` sont les membres de la structure `System.UInt64`.
*  Les membres de `char` sont les membres de la structure `System.Char`.
*  Les membres de `float` sont les membres de la structure `System.Single`.
*  Les membres de `double` sont les membres de la structure `System.Double`.
*  Les membres de `decimal` sont les membres de la structure `System.Decimal`.
*  Les membres de `bool` sont les membres de la structure `System.Boolean`.

### <a name="enumeration-members"></a>Membres de l’énumération

Les membres d’une énumération sont les constantes déclarées dans l’énumération et les membres hérités de la classe de base directe de l’énumération `System.Enum` et les classes de base indirectes `System.ValueType` et `object`.

### <a name="class-members"></a>Membres de classe

Les membres d’une classe sont les membres déclarés dans la classe et les membres hérités de la classe de base (à l’exception de la classe `object` qui n’a pas de classe de base). Les membres hérités de la classe de base incluent les constantes, les champs, les méthodes, les propriétés, les événements, les indexeurs, les opérateurs et les types de la classe de base, mais pas les constructeurs d’instance, les destructeurs et les constructeurs statiques de la classe de base. Les membres de la classe de base sont hérités sans tenir compte de leur accessibilité.

Une déclaration de classe peut contenir des déclarations de constantes, des champs, des méthodes, des propriétés, des événements, des indexeurs, des opérateurs, des constructeurs d’instance, des destructeurs, des constructeurs statiques et des types.

Les membres de `object` et `string` correspondent directement aux membres des types de classe qu’ils aliasent :

*  Les membres de `object` sont les membres de la classe `System.Object`.
*  Les membres de `string` sont les membres de la classe `System.String`.

### <a name="interface-members"></a>Membres d’interface

Les membres d’une interface sont les membres déclarés dans l’interface et dans toutes les interfaces de base de l’interface. Les membres de la classe `object` ne sont pas, strictement parlants, des membres de toute interface ([membres d’interface](interfaces.md#interface-members)). Toutefois, les membres de la classe `object` sont disponibles via la recherche de membres dans n’importe quel type d’interface ([recherche de membre](expressions.md#member-lookup)).

### <a name="array-members"></a>Membres du tableau

Les membres d’un tableau sont les membres hérités de la classe `System.Array`.

### <a name="delegate-members"></a>Membres délégués

Les membres d’un délégué sont les membres hérités de la classe `System.Delegate`.

## <a name="member-access"></a>Accès au membre

Les déclarations de membres permettent de contrôler l’accès aux membres. L’accessibilité d’un membre est établie par l’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) du membre combiné avec l’accessibilité du type conteneur immédiatement, le cas échéant.

Lorsque l’accès à un membre particulier est autorisé, le membre est dit ***accessible***. Inversement, lorsque l’accès à un membre particulier n’est pas autorisé, le membre est dit ***inaccessible***. L’accès à un membre est autorisé lorsque l’emplacement du texte dans lequel l’accès a lieu est inclus dans le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) du membre.

### <a name="declared-accessibility"></a>Accessibilité déclarée

L' ***accessibilité déclarée*** d’un membre peut être l’une des suivantes :

*  Public, qui est sélectionné en incluant un modificateur `public` dans la déclaration de membre. La signification intuitive de `public` est « accès non limité ».
*  Protégé, qui est sélectionné en incluant un modificateur `protected` dans la déclaration de membre. La signification intuitive de `protected` est « l’accès est limité à la classe conteneur ou aux types dérivés de la classe conteneur ».
*  Interne, qui est sélectionné en incluant un modificateur `internal` dans la déclaration de membre. La signification intuitive de `internal` est « accès limité à ce programme ».
*  Protected internal (c’est-à-dire protected ou Internal), qui est sélectionné en incluant à la fois un modificateur `protected` et un modificateur `internal` dans la déclaration de membre. La signification intuitive de `protected internal` est « accès limité à ce programme ou aux types dérivés de la classe conteneur ».
*  Privé, qui est sélectionné en incluant un modificateur `private` dans la déclaration de membre. La signification intuitive de `private` est « accès limité au type conteneur ».

En fonction du contexte dans lequel la déclaration d’un membre a lieu, seuls certains types d’accessibilité déclarés sont autorisés. En outre, lorsqu’une déclaration de membre n’inclut pas de modificateur d’accès, le contexte dans lequel la déclaration a lieu détermine l’accessibilité déclarée par défaut.

*  Les espaces de noms ont implicitement `public` l’accessibilité déclarée. Aucun modificateur d’accès n’est autorisé sur les déclarations d’espace de noms.
*  Les types déclarés dans les unités de compilation ou les espaces de noms peuvent avoir l’accessibilité déclarée `public` ou `internal` et la valeur par défaut `internal` l’accessibilité déclarée.
*  Les membres de classe peuvent avoir l’un des cinq types d’accessibilité déclarés et la valeur par défaut à `private` l’accessibilité déclarée. (Notez qu’un type déclaré en tant que membre d’une classe peut avoir l’un des cinq types d’accessibilité déclarés, alors qu’un type déclaré en tant que membre d’un espace de noms ne peut avoir que l’accessibilité déclarée `public` ou `internal`.)
*  Les membres de struct peuvent avoir l’accessibilité déclarée `public`, `internal` ou `private`, et la valeur par défaut `private` l’accessibilité déclarée, car les structs sont implicitement sealed. Les membres de struct introduits dans un struct (autrement dit, qui n’est pas hérité par ce struct) ne peuvent pas avoir l’accessibilité déclarée `protected` ou `protected internal`. (Notez qu’un type déclaré en tant que membre d’un struct peut avoir l’accessibilité déclarée `public`, `internal` ou `private`, alors qu’un type déclaré en tant que membre d’un espace de noms ne peut avoir qu’une accessibilité `public` ou `internal`.)
*  Les membres d’interface ont implicitement `public` l’accessibilité déclarée. Aucun modificateur d’accès n’est autorisé sur les déclarations de membres d’interface.
*  Les membres de l’énumération ont implicitement `public` l’accessibilité déclarée. Aucun modificateur d’accès n’est autorisé sur les déclarations de membre d’énumération.

### <a name="accessibility-domains"></a>Domaines d’accessibilité

Le ***domaine d’accessibilité*** d’un membre se compose des sections (éventuellement disjointes) du texte de programme dans lesquelles l’accès au membre est autorisé. Dans le cadre de la définition du domaine d’accessibilité d’un membre, un membre est dit comme étant de ***niveau supérieur*** s’il n’est pas déclaré dans un type, et un membre est dit ***imbriqué*** s’il est déclaré dans un autre type. En outre, le ***texte de programme*** d’un programme est défini comme tout le texte de programme contenu dans tous les fichiers sources du programme, et le texte de programme d’un type est défini comme tout le texte de programme contenu dans le *type_declaration*s de ce type (y compris, éventuellement, les types imbriqués dans le type).

Le domaine d’accessibilité d’un type prédéfini (tel que `object`, `int` ou `double`) est illimité.

Le domaine d’accessibilité d’un type indépendant de niveau supérieur `T` ([types liés et indépendants](types.md#bound-and-unbound-types)) qui est déclaré dans un programme `P` est défini comme suit :

*  Si l’accessibilité déclarée de `T` est `public`, le domaine d’accessibilité de `T` est le texte de programme de `P` et tout programme qui référence `P`.
*  Si l'accessibilité déclarée de `T` est `internal`, le domaine d'accessibilité de `T` correspond au texte de programme de `P`.

À partir de ces définitions, il en résulte que le domaine d’accessibilité d’un type indépendant de niveau supérieur est toujours au moins le texte de programme du programme dans lequel ce type est déclaré.

Le domaine d’accessibilité d’un type construit `T<A1, ..., An>` est l’intersection du domaine d’accessibilité du type générique indépendant `T` et les domaines d’accessibilité des arguments de type `A1, ..., An`.

Le domaine d’accessibilité d’un membre imbriqué `M` déclaré dans un type `T` dans un programme `P` est défini comme suit (Notez que `M` lui-même peut être un type) :

*  Si l'accessibilité déclarée de `M` est `public`, le domaine d'accessibilité de `M` correspond à celui de `T`.
*  Si l’accessibilité déclarée de `M` est `protected internal`, laissez `D` être l’Union du texte de programme de `P` et le texte de programme de tout type dérivé de `T`, déclaré en dehors de `P`. Le domaine d’accessibilité de `M` correspond à l’intersection du domaine d’accessibilité de `T` avec `D`.
*  Si l’accessibilité déclarée de `M` est `protected`, laissez `D` être l’Union du texte de programme de `T` et le texte de programme de n’importe quel type dérivé de `T`. Le domaine d’accessibilité de `M` correspond à l’intersection du domaine d’accessibilité de `T` avec `D`.
*  Si l'accessibilité déclarée de `M` est `internal`, le domaine d'accessibilité de `M` correspond à l'intersection du domaine d'accessibilité de `T` avec le texte de programme de `P`.
*  Si l'accessibilité déclarée de `M` est `private`, le domaine d'accessibilité de `M` correspond au texte de programme de `T`.

À partir de ces définitions, il s’ensuit que le domaine d’accessibilité d’un membre imbriqué est toujours au moins le texte de programme du type dans lequel le membre est déclaré. En outre, il s’ensuit que le domaine d’accessibilité d’un membre n’est jamais plus inclusif que le domaine d’accessibilité du type dans lequel le membre est déclaré.

En termes intuitifs, lors de l’accès à un type ou membre `M`, les étapes suivantes sont évaluées pour s’assurer que l’accès est autorisé :

*  Premièrement, si `M` est déclaré dans un type (par opposition à une unité de compilation ou un espace de noms), une erreur de compilation se produit si ce type n’est pas accessible.
*  Ensuite, si `M` est `public`, l’accès est autorisé.
*  Sinon, si `M` est `protected internal`, l’accès est autorisé s’il se produit dans le programme dans lequel `M` est déclaré, ou s’il se produit dans une classe dérivée de la classe dans laquelle `M` est déclarée et qui a lieu via le type de classe dérivée ([protected accès pour les membres d’instance](basic-concepts.md#protected-access-for-instance-members)).
*  Sinon, si `M` est `protected`, l’accès est autorisé s’il se produit dans la classe dans laquelle `M` est déclaré, ou s’il se produit dans une classe dérivée de la classe dans laquelle `M` est déclarée et qui a lieu via le type de classe dérivée ([protected accès pour les membres d’instance](basic-concepts.md#protected-access-for-instance-members)).
*  Sinon, si `M` est `internal`, l’accès est autorisé s’il se produit dans le programme dans lequel `M` est déclaré.
*  Sinon, si `M` est `private`, l’accès est autorisé s’il se produit dans le type dans lequel `M` est déclaré.
*  Dans le cas contraire, le type ou le membre n’est pas accessible et une erreur de compilation se produit.

Dans l’exemple
```csharp
public class A
{
    public static int X;
    internal static int Y;
    private static int Z;
}

internal class B
{
    public static int X;
    internal static int Y;
    private static int Z;

    public class C
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }

    private class D
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }
}
```
les classes et les membres ont les domaines d’accessibilité suivants :

*  Le domaine d’accessibilité de `A` et `A.X` est illimité.
*  Le domaine d’accessibilité de `A.Y`, `B`, `B.X`, `B.Y`, `B.C`, `B.C.X` et `B.C.Y` est le texte du programme contenant.
*  Le domaine d’accessibilité de `A.Z` correspond au texte de programme de `A`.
*  Le domaine d’accessibilité de `B.Z` et `B.D` est le texte de programme de `B`, y compris le texte de programme de `B.C` et `B.D`.
*  Le domaine d’accessibilité de `B.C.Z` correspond au texte de programme de `B.C`.
*  Le domaine d’accessibilité de `B.D.X` et `B.D.Y` est le texte de programme de `B`, y compris le texte de programme de `B.C` et `B.D`.
*  Le domaine d’accessibilité de `B.D.Z` correspond au texte de programme de `B.D`.

Comme l’illustre l’exemple, le domaine d’accessibilité d’un membre n’est jamais plus grand que celui d’un type conteneur. Par exemple, même si tous les membres `X` ont une accessibilité déclarée publique, tous les @no__t, mais-1 ont des domaines d’accessibilité qui sont limités par un type conteneur.

Comme décrit dans [membres](basic-concepts.md#members), tous les membres d’une classe de base, à l’exception des constructeurs d’instance, des destructeurs et des constructeurs statiques, sont hérités par les types dérivés. Cela comprend même les membres privés d’une classe de base. Toutefois, le domaine d’accessibilité d’un membre privé comprend uniquement le texte de programme du type dans lequel le membre est déclaré. Dans l’exemple
```csharp
class A
{
    int x;

    static void F(B b) {
        b.x = 1;        // Ok
    }
}

class B: A
{
    static void F(B b) {
        b.x = 1;        // Error, x not accessible
    }
}
```
la classe `B` hérite du membre privé `x` de la classe `A`. Étant donné que le membre est privé, il est uniquement accessible dans le *class_body* de `A`. Ainsi, l’accès à `b.x` réussit dans la méthode `A.F`, mais échoue dans la méthode `B.F`.

### <a name="protected-access-for-instance-members"></a>Accès protégé pour les membres d’instance

Lorsqu’un membre d’instance `protected` est accessible en dehors du texte de programme de la classe dans laquelle il est déclaré, et quand un membre d’instance `protected internal` est accessible en dehors du texte de programme du programme dans lequel il est déclaré, l’accès doit avoir lieu dans une classe déclaration qui dérive de la classe dans laquelle elle est déclarée. En outre, l’accès doit être effectué par le biais d’une instance de ce type de classe dérivée ou d’un type de classe construit à partir de celui-ci. Cette restriction empêche une classe dérivée d’accéder aux membres protégés d’autres classes dérivées, même lorsque les membres sont hérités de la même classe de base.

Laissez `B` être une classe de base qui déclare un membre d’instance protégée `M` et laissez `D` être une classe qui dérive de `B`. Dans le *class_body* de `D`, l’accès à `M` peut prendre l’une des formes suivantes :

*  *Type_name* ou *primary_expression* non qualifié de la forme `M`.
*  Un *primary_expression* de la forme `E.M`, à condition que le type de `E` soit `T` ou une classe dérivée de `T`, où `T` est le type de classe `D` ou un type de classe construit à partir de `D`
*  *Primary_expression* de la forme `base.M`.

En plus de ces formes d’accès, une classe dérivée peut accéder à un constructeur d’instance protégé d’une classe de base dans un *constructor_initializer* ([initialiseurs de constructeur](classes.md#constructor-initializers)).

Dans l’exemple
```csharp
public class A
{
    protected int x;

    static void F(A a, B b) {
        a.x = 1;        // Ok
        b.x = 1;        // Ok
    }
}

public class B: A
{
    static void F(A a, B b) {
        a.x = 1;        // Error, must access through instance of B
        b.x = 1;        // Ok
    }
}
```
dans `A`, il est possible d’accéder à `x` via des instances de `A` et `B`, car dans les deux cas, l’accès s’effectue via une instance de `A` ou une classe dérivée de `A`. Toutefois, dans `B`, il n’est pas possible d’accéder à `x` via une instance de `A`, puisque `A` ne dérive pas de `B`.

Dans l’exemple
```csharp
class C<T>
{
    protected T x;
}

class D<T>: C<T>
{
    static void F() {
        D<T> dt = new D<T>();
        D<int> di = new D<int>();
        D<string> ds = new D<string>();
        dt.x = default(T);
        di.x = 123;
        ds.x = "test";
    }
}
```
les trois assignations à `x` sont autorisées, car elles ont toutes lieu via des instances de types de classe construits à partir du type générique.

### <a name="accessibility-constraints"></a>Contraintes d’accessibilité

Plusieurs constructions dans le C# langage requièrent qu’un type soit ***au moins aussi accessible qu'*** un membre ou un autre type. Un type `T` est considéré comme étant au moins aussi accessible qu’un membre ou un type `M` si le domaine d’accessibilité de `T` est un sur-ensemble du domaine d’accessibilité de `M`. En d’autres termes, `T` est au moins aussi accessible que `M` si `T` est accessible dans tous les contextes dans lesquels `M` est accessible.

Les contraintes d’accessibilité suivantes existent :

*  La classe de base directe d’un type de classe doit être au moins aussi accessible que le type de classe lui-même.
*  Les interfaces de base explicites d’un type d’interface doivent être au moins aussi accessibles que le type d’interface lui-même.
*  Le type de retour et les types de paramètres d’un type délégué doivent être au moins aussi accessibles que le type délégué lui-même.
*  Le type d’une constante doit être au moins aussi accessible que la constante elle-même.
*  Le type d’un champ doit être au moins aussi accessible que le champ lui-même.
*  Le type de retour et les types de paramètres d’une méthode doivent être au moins aussi accessibles que la méthode elle-même.
*  Le type d’une propriété doit être au moins aussi accessible que la propriété elle-même.
*  Le type d’un événement doit être au moins aussi accessible que l’événement lui-même.
*  Le type et les types de paramètres d’un indexeur doivent être au moins aussi accessibles que l’indexeur lui-même.
*  Le type de retour et les types de paramètres d’un opérateur doivent être au moins aussi accessibles que l’opérateur lui-même.
*  Les types de paramètres d’un constructeur d’instance doivent être au moins aussi accessibles que le constructeur d’instance lui-même.

Dans l’exemple
```csharp
class A {...}

public class B: A {...}
```
la classe `B` génère une erreur au moment de la compilation, car `A` n’est pas au moins aussi accessible que `B`.

De même, dans l’exemple
```csharp
class A {...}

public class B
{
    A F() {...}

    internal A G() {...}

    public A H() {...}
}
```
la méthode `H` dans `B` génère une erreur au moment de la compilation, car le type de retour `A` n’est pas au moins aussi accessible que la méthode.

## <a name="signatures-and-overloading"></a>Signatures et surcharge

Les méthodes, constructeurs d’instance, indexeurs et opérateurs sont caractérisés par leurs ***signatures***:

*  La signature d’une méthode se compose du nom de la méthode, du nombre de paramètres de type et du type et du type (valeur, référence ou sortie) de chacun de ses paramètres formels, pris en compte dans l’ordre de gauche à droite. Pour ces raisons, tout paramètre de type de la méthode qui se produit dans le type d’un paramètre formel est identifié par son nom, mais par sa position ordinale dans la liste d’arguments de type de la méthode. La signature d’une méthode n’inclut pas spécifiquement le type de retour, le modificateur `params` qui peut être spécifié pour le paramètre le plus à droite, ni les contraintes de paramètre de type facultatives.
*  La signature d’un constructeur d’instance se compose du type et du type (valeur, référence ou sortie) de chacun de ses paramètres formels, pris en compte dans l’ordre de gauche à droite. La signature d’un constructeur d’instance n’inclut pas spécifiquement le modificateur `params` qui peut être spécifié pour le paramètre le plus à droite.
*  La signature d’un indexeur se compose du type de chacun de ses paramètres formels, pris en compte dans l’ordre de gauche à droite. La signature d’un indexeur n’inclut pas spécifiquement le type d’élément et n’inclut pas non plus le modificateur `params` qui peut être spécifié pour le paramètre le plus à droite.
*  La signature d’un opérateur se compose du nom de l’opérateur et du type de chacun de ses paramètres formels, pris en compte dans l’ordre de gauche à droite. La signature d’un opérateur n’inclut pas spécifiquement le type de résultat.

Les signatures sont le mécanisme d’activation pour la ***surcharge*** des membres dans des classes, des structures et des interfaces :

*  La surcharge de méthodes permet à une classe, un struct ou une interface de déclarer plusieurs méthodes portant le même nom, à condition que leurs signatures soient uniques dans cette classe, structure ou interface.
*  La surcharge des constructeurs d’instance permet à une classe ou un struct de déclarer plusieurs constructeurs d’instance, à condition que leurs signatures soient uniques au sein de cette classe ou de ce struct.
*  La surcharge d’indexeurs permet à une classe, un struct ou une interface de déclarer plusieurs indexeurs, à condition que leurs signatures soient uniques au sein de cette classe, structure ou interface.
*  La surcharge des opérateurs permet à une classe ou un struct de déclarer plusieurs opérateurs portant le même nom, à condition que leurs signatures soient uniques au sein de cette classe ou de ce struct.

Bien que les modificateurs de paramètres `out` et `ref` soient considérés comme faisant partie d’une signature, les membres déclarés dans un même type ne peuvent pas différer dans la signature uniquement par `ref` et `out`. Une erreur de compilation se produit si deux membres sont déclarés dans le même type avec des signatures qui seraient les mêmes si tous les paramètres dans les deux méthodes avec des modificateurs `out` ont été changés en modificateurs `ref`. À d’autres fins de correspondance de signature (par exemple, le masquage ou la substitution), `ref` et `out` sont considérés comme faisant partie de la signature et ne correspondent pas l’un à l’autre. (Cette restriction consiste à permettre C# aux programmes d’être facilement traduits pour s’exécuter sur le Common Language Infrastructure (interface de commande), ce qui ne permet pas de définir des méthodes qui diffèrent uniquement par `ref` et `out`.)

Pour les besoins des signatures, les types `object` et `dynamic` sont considérés comme identiques. Les membres déclarés dans un même type ne peuvent donc pas être différents dans la signature uniquement par `object` et `dynamic`.

L’exemple suivant montre un ensemble de déclarations de méthode surchargées, ainsi que leurs signatures.
```csharp
interface ITest
{
    void F();                        // F()

    void F(int x);                   // F(int)

    void F(ref int x);               // F(ref int)

    void F(out int x);               // F(out int)      error

    void F(int x, int y);            // F(int, int)

    int F(string s);                 // F(string)

    int F(int x);                    // F(int)          error

    void F(string[] a);              // F(string[])

    void F(params string[] a);       // F(string[])     error
}
```

Notez que les modificateurs de paramètre `ref` et `out` ([paramètres de méthode](classes.md#method-parameters)) font partie d’une signature. Par conséquent, `F(int)` et `F(ref int)` sont des signatures uniques. Toutefois, `F(ref int)` et `F(out int)` ne peuvent pas être déclarés dans la même interface, car leurs signatures diffèrent uniquement par `ref` et `out`. Notez également que le type de retour et le modificateur `params` ne font pas partie d’une signature. il n’est donc pas possible de se surcharger uniquement en fonction du type de retour ou de l’inclusion ou de l’exclusion du modificateur `params`. Ainsi, les déclarations des méthodes `F(int)` et `F(params string[])` identifiées ci-dessus entraînent une erreur au moment de la compilation.

## <a name="scopes"></a>Portées

La ***portée*** d’un nom est la région du texte de programme dans laquelle il est possible de faire référence à l’entité déclarée par le nom sans qualification du nom. Les portées peuvent être ***imbriquées***et une portée interne peut redéclarer la signification d’un nom à partir d’une étendue externe (Toutefois, cela ne supprime pas la restriction imposée par les [déclarations](basic-concepts.md#declarations) dans un bloc imbriqué il n’est pas possible de déclarer une variable locale avec l’instruction nom identique à celui d’une variable locale dans un bloc englobant). Le nom de l’étendue externe est ensuite dit ***masqué*** dans la région du texte de programme couvert par l’étendue interne, et l’accès au nom externe n’est possible qu’en qualifiant le nom.

*  L’étendue d’un membre d’espace de noms déclaré par un *namespace_member_declaration* ([membres d’espace de noms](namespaces.md#namespace-members)) sans *namespace_declaration* englobant correspond au texte de programme entier.
*  La portée d’un membre d’espace de noms déclaré par un *namespace_member_declaration* dans un *namespace_declaration* dont le nom qualifié complet est `N` est le *namespace_body* de chaque *namespace_declaration* dont la le nom qualifié est `N` ou commence par `N`, suivi d’un point.
*  L’étendue de nom définie par un *extern_alias_directive* s’étend sur les *using_directive*s, *global_attributes* et *namespace_member_declaration*s de son corps d’unité de compilation ou d’espace de noms contenant immédiatement. Un *extern_alias_directive* ne contribue pas aux nouveaux membres de l’espace de déclaration sous-jacent. En d’autres termes, un *extern_alias_directive* n’est pas transitif, mais il affecte uniquement l’unité de compilation ou le corps de l’espace de noms dans lequel il se produit.
*  L’étendue d’un nom défini ou importé par un *using_directive* ([à l’aide de directives](namespaces.md#using-directives)) s’étend sur les *namespace_member_declaration*s du *compilation_unit* ou *namespace_body* dans lequel le *using_directive* se produit. Un *using_directive* peut rendre zéro ou plusieurs noms d’espace de noms, de type ou de membre disponibles dans un *compilation_unit* ou *namespace_body*particulier, mais ne contribue pas à de nouveaux membres dans l’espace de déclaration sous-jacent. En d’autres termes, un *using_directive* n’est pas transitif, mais affecte uniquement le *compilation_unit* ou le *namespace_body* dans lequel il se produit.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *class_declaration* ([déclarations de classe](classes.md#class-declarations)) est *class_base*, *type_parameter_constraints_clause*s et *class_body* de ce  *class_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *struct_declaration* ([déclarations de struct](structs.md#struct-declarations)) est *struct_interfaces*, *type_parameter_constraints_clause*s et *struct_body* de *struct_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *interface_declaration* ([déclarations d’interface](interfaces.md#interface-declarations)) est *interface_base*, *type_parameter_constraints_clause*s et *interface_body* de cet *interface_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *delegate_declaration* ([déclarations de délégué](delegates.md#delegate-declarations)) est *type_retour*, *formal_parameter_list*et *type_parameter_constraints_clause* s de ce *delegate_declaration*.
*  La portée d’un membre déclaré par un *class_member_declaration* ([corps de classe](classes.md#class-body)) est le *class_body* dans lequel la déclaration se produit. En outre, l’étendue d’un membre de classe s’étend au *class_body* de ces classes dérivées qui sont incluses dans le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) du membre.
*  La portée d’un membre déclaré par un *struct_member_declaration* ([membres de struct](structs.md#struct-members)) est le *struct_body* dans lequel la déclaration se produit.
*  La portée d’un membre déclaré par un *enum_member_declaration* ([membres enum](enums.md#enum-members)) est le *enum_body* dans lequel la déclaration se produit.
*  La portée d’un paramètre déclaré dans un *method_declaration* ([méthodes](classes.md#methods)) est le *method_body* de ce *method_declaration*.
*  La portée d’un paramètre déclaré dans un *indexer_declaration* ([indexeurs](classes.md#indexers)) est le *accessor_declarations* de ce *indexer_declaration*.
*  La portée d’un paramètre déclaré dans un *operator_declaration* ([opérateurs](classes.md#operators)) est le *bloc* de ce *operator_declaration*.
*  La portée d’un paramètre déclaré dans un *constructor_declaration* ([constructeurs d’instance](classes.md#instance-constructors)) est le *constructor_initializer* et le *bloc* de ce *constructor_declaration*.
*  La portée d’un paramètre déclaré dans un *lambda_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est le *anonymous_function_body* de ce *lambda_expression*
*  La portée d’un paramètre déclaré dans un *anonymous_method_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) est le *bloc* de ce *anonymous_method_expression*.
*  La portée d’une étiquette déclarée dans un *labeled_statement* ([instructions étiquetées](statements.md#labeled-statements)) est le *bloc* dans lequel la déclaration se produit.
*  La portée d’une variable locale déclarée dans un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)) est le bloc dans lequel la déclaration se produit.
*  La portée d’une variable locale déclarée dans un *switch_block* d’une instruction `switch` ([l’instruction switch](statements.md#the-switch-statement)) est *switch_block*.
*  La portée d’une variable locale déclarée dans *un for_initializer* d’une instruction `for` ([l’instruction for](statements.md#the-for-statement)) est *for_initializer*, *for_condition*, *for_iterator*et l' *instruction* contenue de l’objet instruction `for`.
*  La portée d’une constante locale déclarée dans un *local_constant_declaration* ([déclarations de constantes locales](statements.md#local-constant-declarations)) est le bloc dans lequel la déclaration se produit. Il s’agit d’une erreur de compilation pour faire référence à une constante locale dans une position textuelle qui précède son *constant_declarator*.
*  La portée d’une variable déclarée dans le cadre d’un *foreach_statement*, *using_statement*, *lock_statement* ou *query_expression* est déterminée par l’expansion de la construction donnée.

Dans l’étendue d’un espace de noms, d’une classe, d’une structure ou d’un membre d’énumération, il est possible de faire référence au membre dans une position textuelle qui précède la déclaration du membre. Exemple :
```csharp
class A
{
    void F() {
        i = 1;
    }

    int i = 0;
}
```
Ici, il est valide pour `F` de faire référence à `i` avant d’être déclaré.

Dans l’étendue d’une variable locale, il s’agit d’une erreur de compilation qui fait référence à la variable locale dans une position textuelle qui précède le *local_variable_declarator* de la variable locale. Exemple :
```csharp
class A
{
    int i = 0;

    void F() {
        i = 1;                  // Error, use precedes declaration
        int i;
        i = 2;
    }

    void G() {
        int j = (j = 1);        // Valid
    }

    void H() {
        int a = 1, b = ++a;    // Valid
    }
}
```

Dans la méthode `F` ci-dessus, la première assignation à `i` ne fait pas référence au champ déclaré dans l’étendue externe. Au lieu de cela, il fait référence à la variable locale et génère une erreur de compilation, car il précède la déclaration de la variable dans le texte. Dans la méthode `G`, l’utilisation de `j` dans l’initialiseur pour la déclaration de `j` est valide, car l’utilisation ne précède pas *local_variable_declarator*. Dans la méthode `H`, un *local_variable_declarator* suivant fait correctement référence à une variable locale déclarée dans un *local_variable_declarator* antérieur au sein du même *local_variable_declaration*.

Les règles de portée pour les variables locales sont conçues pour garantir que la signification d’un nom utilisé dans un contexte d’expression est toujours la même dans un bloc. Si la portée d’une variable locale ne doit s’étendre qu’à partir de sa déclaration à la fin du bloc, alors, dans l’exemple ci-dessus, la première assignation est assignée à la variable d’instance et la deuxième assignation est assignée à la variable locale, ce qui peut aboutir à erreurs au moment de la compilation si les instructions du bloc ont été réorganisées par la suite.

La signification d’un nom dans un bloc peut varier selon le contexte dans lequel le nom est utilisé. Dans l’exemple
```csharp
using System;

class A {}

class Test
{
    static void Main() {
        string A = "hello, world";
        string s = A;                            // expression context

        Type t = typeof(A);                      // type context

        Console.WriteLine(s);                    // writes "hello, world"
        Console.WriteLine(t);                    // writes "A"
    }
}
```
le nom `A` est utilisé dans un contexte d’expression pour faire référence à la variable locale `A` et dans un contexte de type pour faire référence à la classe `A`.

### <a name="name-hiding"></a>Masquage de nom

La portée d’une entité comprend généralement plus de texte de programme que l’espace de déclaration de l’entité. En particulier, l’étendue d’une entité peut inclure des déclarations qui introduisent de nouveaux espaces de déclaration contenant des entités du même nom. De telles déclarations entraînent le ***masquage***de l’entité d’origine. À l’inverse, une entité est dite ***visible*** lorsqu’elle n’est pas masquée.

Le masquage de nom se produit lorsque les étendues se chevauchent lors de l’imbrication et lorsque les étendues chevauchent l’héritage. Les caractéristiques des deux types de masquage sont décrites dans les sections suivantes.

#### <a name="hiding-through-nesting"></a>Masquer par imbrication

Le masquage de nom via l’imbrication peut se produire en raison de l’imbrication d’espaces de noms ou de types dans les espaces de noms, en raison de l’imbrication de types dans des classes ou des structs, et de la suite de déclarations de variables locales et de paramètres.

Dans l’exemple
```csharp
class A
{
    int i = 0;

    void F() {
        int i = 1;
    }

    void G() {
        i = 1;
    }
}
```
dans la méthode `F`, la variable d’instance `i` est masquée par la variable locale `i`, mais dans la méthode `G`, `i` fait toujours référence à la variable d’instance.

Lorsqu’un nom dans une portée interne masque un nom dans une portée externe, il masque toutes les occurrences surchargées de ce nom. Dans l’exemple
```csharp
class Outer
{
    static void F(int i) {}

    static void F(string s) {}

    class Inner
    {
        void G() {
            F(1);              // Invokes Outer.Inner.F
            F("Hello");        // Error
        }

        static void F(long l) {}
    }
}
```
l’appel `F(1)` appelle la `F` déclarée dans `Inner`, car toutes les occurrences externes de `F` sont masquées par la déclaration interne. Pour la même raison, l’appel `F("Hello")` génère une erreur au moment de la compilation.

#### <a name="hiding-through-inheritance"></a>Masquer par héritage

Le masquage de nom par héritage se produit lorsque des classes ou des structs déclarent des noms qui ont été hérités de classes de base. Ce type de masquage de nom prend l’une des formes suivantes :

*  Une constante, un champ, une propriété, un événement ou un type introduit dans une classe ou un struct masque tous les membres de la classe de base portant le même nom.
*  Une méthode introduite dans une classe ou un struct masque tous les membres de classe de base non-méthode portant le même nom, ainsi que toutes les méthodes de la classe de base ayant la même signature (nom de méthode et nombre de paramètres, modificateurs et types).
*  Un indexeur introduit dans une classe ou un struct masque tous les indexeurs de la classe de base avec la même signature (nombre de paramètres et types).

Les règles gouvernant les déclarations d’opérateur ([opérateurs](classes.md#operators)) rendent impossible une classe dérivée de déclarer un opérateur avec la même signature qu’un opérateur dans une classe de base. Ainsi, les opérateurs ne sont jamais masqués.

Contrairement au masquage d’un nom dans une portée externe, le masquage d’un nom accessible à partir d’une portée héritée entraîne le signalement d’un avertissement. Dans l’exemple
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    public void F() {}        // Warning, hiding an inherited name
}
```
la déclaration de `F` dans `Derived` provoque le signalement d’un avertissement. Le masquage d’un nom hérité n’est pas une erreur, car cela empêche l’évolution distincte des classes de base. Par exemple, la situation ci-dessus peut être due au fait qu’une version ultérieure de `Base` a introduit une méthode `F` qui n’était pas présente dans une version antérieure de la classe. Si la situation précédente était une erreur, toute modification apportée à une classe de base dans une bibliothèque de classes avec version distincte pourrait entraîner la non-validité des classes dérivées.

L’avertissement provoqué par le masquage d’un nom hérité peut être éliminé grâce à l’utilisation du modificateur `new` :
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    new public void F() {}
}
```

Le modificateur `new` indique que la `F` dans `Derived` est « New », et qu’elle est effectivement destinée à masquer le membre hérité.

Une déclaration d’un nouveau membre masque un membre hérité uniquement dans la portée du nouveau membre.

```csharp
class Base
{
    public static void F() {}
}

class Derived: Base
{
    new private static void F() {}    // Hides Base.F in Derived only
}

class MoreDerived: Derived
{
    static void G() { F(); }          // Invokes Base.F
}
```

Dans l’exemple ci-dessus, la déclaration de `F` dans `Derived` masque le `F` hérité de `Base`, mais étant donné que la nouvelle `F` dans `Derived` a un accès privé, son étendue ne s’étend pas à `MoreDerived`. Ainsi, l’appel `F()` dans `MoreDerived.G` est valide et appellera `Base.F`.

## <a name="namespace-and-type-names"></a>Noms d’espaces de noms et de types

Plusieurs contextes dans un C# programme nécessitent la spécification d’un *namespace_name* ou d’un *type_name* .

```antlr
namespace_name
    : namespace_or_type_name
    ;

type_name
    : namespace_or_type_name
    ;

namespace_or_type_name
    : identifier type_argument_list?
    | namespace_or_type_name '.' identifier type_argument_list?
    | qualified_alias_member
    ;
```

Un *namespace_name* est un *namespace_or_type_name* qui fait référence à un espace de noms. À la résolution suivante, comme décrit ci-dessous, le *namespace_or_type_name* d’un *namespace_name* doit faire référence à un espace de noms, sinon une erreur de compilation se produit. Aucun argument de type ([arguments de type](types.md#type-arguments)) ne peut être présent dans un *namespace_name* (seuls les types peuvent avoir des arguments de type).

Un *type_name* est un *namespace_or_type_name* qui fait référence à un type. Après la résolution comme décrit ci-dessous, le *namespace_or_type_name* d’un *type_name* doit faire référence à un type, ou une erreur de compilation se produit.

Si le *namespace_or_type_name* est un membre d’alias qualifié, son sens est décrit dans [qualificateurs d’alias d’espace de noms](namespaces.md#namespace-alias-qualifiers). Dans le cas contraire, un *namespace_or_type_name* a l’une des quatre formes suivantes :

*  `I`
*  `I<A1, ..., Ak>`
*  `N.I`
*  `N.I<A1, ..., Ak>`

où `I` est un identificateur unique, `N` est un *namespace_or_type_name* et `<A1, ..., Ak>` est un *type_argument_list*facultatif. Si aucun *type_argument_list* n’est spécifié, envisagez `k` pour être égal à zéro.

La signification d’un *namespace_or_type_name* est déterminée comme suit :

*   Si le *namespace_or_type_name* se présente sous la forme `I` ou sous la forme `I<A1, ..., Ak>` :
    * Si `K` est égal à zéro et que le *namespace_or_type_name* apparaît dans une déclaration de méthode générique ([méthodes](classes.md#methods)) et si cette déclaration comprend un paramètre de type ([paramètres de type](classes.md#type-parameters)) nommé @ no__t-4, le *namespace_or_type_ Name* fait référence à ce paramètre de type.
    * Sinon, si le *namespace_or_type_name* apparaît dans une déclaration de type, pour chaque type d’instance @ no__t-1 ([type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de cette déclaration de type et en continuant avec le type d’instance de chaque Déclaration de classe ou de struct englobante (le cas échéant) :
        * Si `K` est égal à zéro et que la déclaration de `T` comprend un paramètre de type nommé @ no__t-2, le *namespace_or_type_name* fait référence à ce paramètre de type.
        * Sinon, si le *namespace_or_type_name* apparaît dans le corps de la déclaration de type et que `T` ou l’un de ses types de base contient un type accessible imbriqué ayant les paramètres Name @ no__t-2 et `K` @ no__t-4Type, le *namespace_or_type _name* fait référence à ce type construit avec les arguments de type donnés. S’il existe plusieurs types de ce type, le type déclaré dans le type plus dérivé est sélectionné. Notez que les membres qui ne sont pas de type (constantes, champs, méthodes, propriétés, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et les membres de type avec un nombre différent de paramètres de type sont ignorés lors de la détermination de la signification du *namespace_or_type_name*.
    * Si les étapes précédentes n’ont pas réussi, pour chaque espace de noms @ no__t-0, en commençant par l’espace de noms dans lequel le *namespace_or_type_name* se produit, en continuant avec chaque espace de noms englobant (le cas échéant) et en terminant par l’espace de noms global, les éléments suivants les étapes sont évaluées jusqu’à ce qu’une entité soit localisée :
        * Si `K` est égal à zéro et que `I` est le nom d’un espace de noms dans @ no__t-2, alors :
            * Si l’emplacement où le *namespace_or_type_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom @ no __t-4 avec un espace de noms ou un type, le *namespace_or_type_name* est ambigu et une erreur de compilation se produit.
            * Sinon, *namespace_or_type_name* fait référence à l’espace de noms nommé `I` dans `N`.
        * Sinon, si `N` contient un type accessible avec les paramètres Name @ no__t-1 et `K` @ no__t-3Type, alors :
            * Si `K` est égal à zéro et que l’emplacement où le *namespace_or_type_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom @ no__t-5 à un espace de noms ou à un type, alors le *namespace_or_type_name* est ambigu et une erreur de compilation se produit.
            * Sinon, *namespace_or_type_name* fait référence au type construit avec les arguments de type donnés.
        * Sinon, si l’emplacement où le *namespace_or_type_name* se produit est entouré d’une déclaration d’espace de noms pour `N` :
            * Si `K` est égal à zéro et que la déclaration d’espace de noms contient un *extern_alias_directive* ou un *using_alias_directive* qui associe le nom @ no__t-3 à un espace de noms ou un type importé, le *namespace_or_type_name* fait référence à ce. espace de noms ou type.
            * Sinon, si les espaces de noms et les déclarations de type importés par les *using_namespace_directive*et *using_alias_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible portant le nom @ no__t-2 et `K` @ no__t-4Type paramètres, puis le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donnés.
            * Sinon, si les espaces de noms et les déclarations de type importés par les *using_namespace_directive*et *using_alias_directive*s de la déclaration d’espace de noms contiennent plusieurs types accessibles portant le nom @ no__t-2 et `K` @ no__t-4Type paramètres, le *namespace_or_type_name* est ambigu et une erreur se produit.
    * Dans le cas contraire, le *namespace_or_type_name* n’est pas défini et une erreur de compilation se produit.
*  Dans le cas contraire, le *namespace_or_type_name* se présente sous la forme `N.I` ou sous la forme `N.I<A1, ..., Ak>`. `N` est tout d’abord résolu en tant que *namespace_or_type_name*. Si la résolution de `N` échoue, une erreur de compilation se produit. Dans le cas contraire, `N.I` ou `N.I<A1, ..., Ak>` est résolu comme suit :
    * Si `K` est égal à zéro et que `N` fait référence à un espace de noms et que `N` contient un espace de noms imbriqué avec le nom `I`, le *namespace_or_type_name* fait référence à cet espace de noms imbriqué.
    * Sinon, si `N` fait référence à un espace de noms et que `N` contient un type accessible avec les paramètres Name @ no__t-2 et `K` @ no__t-4Type, alors le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donnés.
    * Sinon, si `N` fait référence à un type de classe ou de struct (éventuellement construit) et que `N` ou l’une de ses classes de base contient un type accessible imbriqué ayant les paramètres Name @ no__t-2 et `K` @ no__t-4Type, alors le *namespace_or_type_name* fait référence à ce type a été construit avec les arguments de type donnés. S’il existe plusieurs types de ce type, le type déclaré dans le type plus dérivé est sélectionné. Notez que si la signification de `N.I` est déterminée dans le cadre de la résolution de la spécification de la classe de base de `N`, la classe de base directe de `N` est considérée comme Object ([classes de base](classes.md#base-classes)).
    * Dans le cas contraire, `N.I` est un *namespace_or_type_name*non valide et une erreur de compilation se produit.

Un *namespace_or_type_name* est autorisé à référencer une classe statique ([classes statiques](classes.md#static-classes)) uniquement si

*  *Namespace_or_type_name* est le `T` dans un *namespace_or_type_name* de la forme `T.I`, ou
*  *Namespace_or_type_name* est le `T` dans un *typeof_expression* ([liste d’arguments](expressions.md#argument-lists)1) de la forme `typeof(T)`.

### <a name="fully-qualified-names"></a>Noms qualifiés complets

Chaque espace de noms et type a un ***nom qualifié complet***, qui identifie de façon unique l’espace de noms ou le type parmi tous les autres. Le nom qualifié complet d’un espace de noms ou d’un type `N` est déterminé comme suit :

*  Si `N` est un membre de l’espace de noms global, son nom complet est `N`.
*  Dans le cas contraire, son nom qualifié complet est `S.N`, où `S` est le nom qualifié complet de l’espace de noms ou du type dans lequel `N` est déclaré.

En d’autres termes, le nom qualifié complet de `N` est le chemin d’accès hiérarchique complet des identificateurs qui mènent à `N`, en commençant par l’espace de noms global. Étant donné que chaque membre d’un espace de noms ou d’un type doit avoir un nom unique, il suit que le nom qualifié complet d’un espace de noms ou d’un type est toujours unique.

L’exemple ci-dessous montre plusieurs déclarations d’espace de noms et de type, ainsi que leurs noms complets associés.
```csharp
class A {}                // A

namespace X               // X
{
    class B               // X.B
    {
        class C {}        // X.B.C
    }

    namespace Y           // X.Y
    {
        class D {}        // X.Y.D
    }
}

namespace X.Y             // X.Y
{
    class E {}            // X.Y.E
}
```

## <a name="automatic-memory-management"></a>Gestion automatique de la mémoire

C#utilise la gestion automatique de la mémoire, qui évite aux développeurs d’allouer et de libérer manuellement la mémoire occupée par les objets. Les stratégies de gestion automatique de la mémoire sont implémentées par un ***garbage collector***. Le cycle de vie de la gestion de la mémoire d’un objet est le suivant :

1. Lorsque l’objet est créé, la mémoire est allouée, le constructeur est exécuté et l’objet est considéré comme dynamique.
2. Si l’objet, ou une partie de celui-ci, n’est pas accessible par une continuation d’exécution possible, autre que l’exécution de destructeurs, l’objet est considéré comme n’étant plus utilisé et il est éligible pour la destruction. Le C# compilateur et le garbage collector peuvent choisir d’analyser le code pour déterminer les références à un objet qui peuvent être utilisées ultérieurement. Par exemple, si une variable locale qui est dans la portée est la seule référence existante à un objet, mais que cette variable locale n’est jamais référencée dans une éventuelle continuation d’exécution à partir du point d’exécution actuel de la procédure, le garbage collector peut (mais n’est pas requis pour) traiter l’objet comme n’étant plus utilisé.
3. Une fois que l’objet est éligible pour la destruction, à une date non spécifiée plus tard, le[destructeur (le cas échéant) de](classes.md#destructors)l’objet est exécuté. Dans des circonstances normales, le destructeur de l’objet est exécuté une seule fois, bien que les API spécifiques à l’implémentation puissent autoriser le remplacement de ce comportement.
4. Une fois que le destructeur pour un objet est exécuté, si cet objet, ou une partie de celui-ci, n’est pas accessible par toute continuation d’exécution possible, y compris l’exécution de destructeurs, l’objet est considéré comme étant inaccessible et l’objet devient éligible pour la collecte.
5. Enfin, une fois que l’objet est éligible pour la collecte, le garbage collector libère la mémoire associée à cet objet.

Le garbage collector conserve des informations sur l’utilisation des objets et utilise ces informations pour prendre des décisions de gestion de la mémoire, telles que l’emplacement de la mémoire pour localiser un objet nouvellement créé, quand déplacer un objet, et quand un objet n’est plus utilisé ou n’est pas accessible.

Comme les autres langages qui supposent l’existence d’un C# garbage collector, est conçu de sorte que le garbage collector puisse implémenter une large gamme de stratégies de gestion de la mémoire. Par exemple, C# ne nécessite pas l’exécution des destructeurs ou la collecte des objets dès qu’ils sont éligibles, ou l’exécution des destructeurs dans un ordre particulier ou sur un thread particulier.

Le comportement du garbage collector peut être contrôlé, à un certain degré, via des méthodes statiques sur la classe `System.GC`. Cette classe peut être utilisée pour demander qu’une collection se produise, que les destructeurs soient exécutés (ou non), et ainsi de suite.

Dans la mesure où le garbage collector est autorisé à atteindre une grande latitude en déterminant quand collecter des objets et exécuter des destructeurs, une implémentation conforme peut produire une sortie qui diffère de celle indiquée par le code suivant. Le programme
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }
}

class B
{
    object Ref;

    public B(object o) {
        Ref = o;
    }

    ~B() {
        Console.WriteLine("Destruct instance of B");
    }
}

class Test
{
    static void Main() {
        B b = new B(new A());
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
    }
}
```
crée une instance de la classe `A` et une instance de la classe `B`. Ces objets deviennent éligibles pour garbage collection lorsque la valeur `null` est assignée à la variable `b`, étant donné qu’il est impossible pour un code écrit par l’utilisateur d’y accéder. La sortie peut être

```console
Destruct instance of A
Destruct instance of B
```
ou
```console
Destruct instance of B
Destruct instance of A
```
étant donné que le langage n’impose aucune contrainte sur l’ordre dans lequel les objets sont récupérés par le garbage collector.

Dans les cas subtils, la distinction entre « éligible à la destruction » et « éligible pour la collecte » peut être importante. Par exemple :
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }

    public void F() {
        Console.WriteLine("A.F");
        Test.RefA = this;
    }
}

class B
{
    public A Ref;

    ~B() {
        Console.WriteLine("Destruct instance of B");
        Ref.F();
    }
}

class Test
{
    public static A RefA;
    public static B RefB;

    static void Main() {
        RefB = new B();
        RefA = new A();
        RefB.Ref = RefA;
        RefB = null;
        RefA = null;

        // A and B now eligible for destruction
        GC.Collect();
        GC.WaitForPendingFinalizers();

        // B now eligible for collection, but A is not
        if (RefA != null)
            Console.WriteLine("RefA is not null");
    }
}
```

Dans le programme ci-dessus, si le garbage collector choisit d’exécuter le destructeur de `A` avant le destructeur de `B`, la sortie de ce programme peut être :
```console
Destruct instance of A
Destruct instance of B
A.F
RefA is not null
```

Notez que même si l’instance de `A` n’était pas en cours d’utilisation et que le destructeur de `A` était exécuté, il est toujours possible pour les méthodes de `A` (dans ce cas, `F`) d’être appelées à partir d’un autre destructeur. Notez également que l’exécution d’un destructeur peut entraîner la réutilisation d’un objet à partir du programme principal. Dans ce cas, l’exécution du destructeur de `B` a entraîné une instance de `A` qui n’était pas utilisée précédemment pour devenir accessible à partir de la référence active `Test.RefA`. Après l’appel à `WaitForPendingFinalizers`, l’instance de `B` est éligible pour la collection, mais l’instance de `A` ne l’est pas, en raison de la référence `Test.RefA`.

Pour éviter toute confusion et un comportement inattendu, il est généralement judicieux que les destructeurs effectuent uniquement un nettoyage sur les données stockées dans les champs de leur propre objet, et non sur les objets référencés ou les champs statiques.

Une alternative à l’utilisation des destructeurs consiste à permettre à une classe d’implémenter l’interface `System.IDisposable`. Cela permet au client de l’objet de déterminer quand libérer les ressources de l’objet, en généralement en accédant à l’objet en tant que ressource dans une instruction `using` ([l’instruction using](statements.md#the-using-statement)).

## <a name="execution-order"></a>Ordre d’exécution

L’exécution d' C# un programme continue de telle sorte que les effets secondaires de chaque thread d’exécution soient conservés aux points d’exécution critiques. Un ***effet secondaire*** est défini comme une lecture ou une écriture d’un champ volatile, une écriture dans une variable non volatile, une écriture dans une ressource externe et la levée d’une exception. Les points d’exécution critiques à partir desquels l’ordre de ces effets secondaires doivent être préservés sont des références à des champs volatils ([champs volatils](classes.md#volatile-fields)), à des instructions `lock` ([instruction lock](statements.md#the-lock-statement)) et à la création et à l’arrêt des threads. L’environnement d’exécution est libre de modifier l’ordre d’exécution d' C# un programme, selon les contraintes suivantes :

*  La dépendance des données est conservée dans un thread d’exécution. Autrement dit, la valeur de chaque variable est calculée comme si toutes les instructions du thread étaient exécutées dans l’ordre du programme d’origine.
*  Les règles de classement d’initialisation sont conservées ([initialisation de champ](classes.md#field-initialization) et [initialiseurs de variable](classes.md#variable-initializers)).
*  L’ordre des effets secondaires est préservé en ce qui concerne les lectures et les écritures volatiles ([champs volatiles](classes.md#volatile-fields)). En outre, l’environnement d’exécution n’a pas besoin d’évaluer une partie d’une expression s’il peut déduire que la valeur de cette expression n’est pas utilisée et qu’aucun effet secondaire nécessaire n’est produit (y compris les effets causés par l’appel d’une méthode ou l’accès à un champ volatile). Lorsque l’exécution du programme est interrompue par un événement asynchrone (par exemple, une exception levée par un autre thread), il n’est pas garanti que les effets secondaires observables sont visibles dans l’ordre du programme d’origine.
