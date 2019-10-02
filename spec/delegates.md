---
ms.openlocfilehash: d162d4b6a489032dcdfca9094a39d88fd03d4013
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704101"
---
# <a name="delegates"></a>Délégués

Les délégués permettent aux scénarios que d’autres langages, tels que C++, Pascal et Modula, aient résolu les pointeurs de fonction. Contrairement C++ aux pointeurs de fonction, toutefois, les délégués sont entièrement orientés C++ objet et contrairement aux pointeurs vers des fonctions membres, les délégués encapsulent à la fois une instance d’objet et une méthode.

Une déclaration Delegate définit une classe dérivée de la classe `System.Delegate`. Une instance de délégué encapsule une liste d’appel, qui est une liste d’une ou plusieurs méthodes, chacune d’elles étant désignée sous le terme d’entité pouvant être appelée. Pour les méthodes d’instance, une entité pouvant être appelée se compose d’une instance et d’une méthode sur cette instance. Pour les méthodes statiques, une entité pouvant être appelée se compose simplement d’une méthode. L’appel d’une instance de délégué avec un jeu d’arguments approprié entraîne l’appel de chacune des entités pouvant être appelées par le délégué avec le jeu d’arguments donné.

Une propriété intéressante et utile d’une instance de délégué est qu’elle ne connaît pas ou ne tient pas compte des classes des méthodes qu’elle encapsule ; Il est important que ces méthodes soient compatibles ([déclarations déléguées](delegates.md#delegate-declarations)) avec le type du délégué. Les délégués sont alors parfaitement adaptés à l’appel « anonyme ».

## <a name="delegate-declarations"></a>Déclarations de délégué

Un *delegate_declaration* est un *type_declaration* ([déclarations de type](namespaces.md#type-declarations)) qui déclare un nouveau type délégué.

```antlr
delegate_declaration
    : attributes? delegate_modifier* 'delegate' return_type
      identifier variant_type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;

delegate_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | delegate_modifier_unsafe
    ;
```

Il s’agit d’une erreur au moment de la compilation pour que le même modificateur apparaisse plusieurs fois dans une déclaration Delegate.

Le modificateur `new` est autorisé uniquement sur les délégués déclarés dans un autre type, auquel cas il spécifie qu’un tel délégué masque un membre hérité du même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).

Les modificateurs `public`, `protected`, `internal` et `private` contrôlent l’accessibilité du type délégué. En fonction du contexte dans lequel la déclaration du délégué se produit, certains de ces modificateurs peuvent ne pas être autorisés ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Le nom de type du délégué est *identifier*.

Le *formal_parameter_list* facultatif spécifie les paramètres du délégué et *type_retour* indique le type de retour du délégué.

Le *variant_type_parameter_list* facultatif ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)) spécifie les paramètres de type pour le délégué lui-même.

Le type de retour d’un type délégué doit être `void`, ou de sortie sécurisée (sécurité de l'[écart](interfaces.md#variance-safety)).

Tous les types de paramètres formels d’un type délégué doivent être de type sécurisé d’entrée. En outre, tous les types de paramètres `out` ou `ref` doivent également être de type sécurisé de sortie. Notez que même les paramètres `out` sont requis pour la sécurité d’entrée, en raison d’une limitation de la plateforme d’exécution sous-jacente.

Les types délégués C# dans sont équivalents au nom, et non structurellement équivalents. Plus précisément, deux types délégués différents ayant les mêmes listes de paramètres et type de retour sont considérés comme des types délégués différents. Toutefois, les instances de deux types délégués distincts mais structurellement équivalents peuvent être considérées comme égales ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).

Exemple :

```csharp
delegate int D1(int i, double d);

class A
{
    public static int M1(int a, double b) {...}
}

class B
{
    delegate int D2(int c, double d);
    public static int M1(int f, double g) {...}
    public static void M2(int k, double l) {...}
    public static int M3(int g) {...}
    public static void M4(int g) {...}
}
```

Les méthodes `A.M1` et `B.M1` sont compatibles avec les deux types délégués `D1` et `D2`, car ils ont le même type de retour et la même liste de paramètres ; Toutefois, ces types délégués sont de deux types différents. ils ne sont donc pas interchangeables. Les méthodes `B.M2`, `B.M3` et `B.M4` sont incompatibles avec les types délégués `D1` et `D2`, car elles ont des types de retour ou des listes de paramètres différents.

Comme d’autres déclarations de type générique, les arguments de type doivent être fournis pour créer un type délégué construit. Les types de paramètres et le type de retour d’un type délégué construit sont créés en substituant, pour chaque paramètre de type dans la déclaration du délégué, l’argument de type correspondant du type délégué construit. Le type de retour et les types de paramètres résultants sont utilisés pour déterminer quelles méthodes sont compatibles avec un type délégué construit. Exemple :

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

La méthode `X.F` est compatible avec le type délégué `Predicate<int>` et la méthode `X.G` est compatible avec le type délégué `Predicate<string>`.

La seule façon de déclarer un type délégué consiste à utiliser un *delegate_declaration*. Un type délégué est un type de classe dérivé de `System.Delegate`. Les types délégués sont implicitement `sealed`. il n’est donc pas possible de dériver un type d’un type délégué. Il n’est pas non plus possible de dériver un type de classe non déléguée à partir de `System.Delegate`. Notez que `System.Delegate` n’est pas lui-même un type délégué ; Il s’agit d’un type de classe dont tous les types délégués sont dérivés.

C#fournit une syntaxe spéciale pour l’instanciation et l’appel de délégués. À l’exception de l’instanciation, toute opération qui peut être appliquée à une classe ou une instance de classe peut également être appliquée à une instance ou une classe déléguée, respectivement. En particulier, il est possible d’accéder aux membres du type `System.Delegate` par le biais de la syntaxe d’accès aux membres habituelle.

L’ensemble de méthodes encapsulé par une instance de délégué est appelé une liste d’appel. Lorsqu’une instance de délégué est créée ([déléguer la compatibilité](delegates.md#delegate-compatibility)) à partir d’une seule méthode, elle encapsule cette méthode et sa liste d’appel contient une seule entrée. Toutefois, lorsque deux instances de délégués non NULL sont combinées, leurs listes d’appel sont concaténées, dans l’opérande de l’ordre de gauche, puis dans l’opérande de droite, pour former une nouvelle liste d’appel, qui contient au moins deux entrées.

Les délégués sont combinés à l’aide des opérateurs binaires `+` ([opérateur d’addition](expressions.md#addition-operator)) et `+=` ([assignation composée](expressions.md#compound-assignment)). Un délégué peut être supprimé d’une combinaison de délégués à l’aide de l’opérateur binaire `-` ([opérateur de soustraction](expressions.md#subtraction-operator)) et des opérateurs `-=` ([assignation composée](expressions.md#compound-assignment)). Les délégués peuvent être comparés pour déterminer leur égalité ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).

L’exemple suivant illustre l’instanciation d’un certain nombre de délégués et les listes d’appel correspondantes :

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public static void M2(int i) {...}

}

class Test
{
    static void Main() {
        D cd1 = new D(C.M1);      // M1
        D cd2 = new D(C.M2);      // M2
        D cd3 = cd1 + cd2;        // M1 + M2
        D cd4 = cd3 + cd1;        // M1 + M2 + M1
        D cd5 = cd4 + cd3;        // M1 + M2 + M1 + M1 + M2
    }

}
```

Lorsque `cd1` et `cd2` sont instanciés, ils encapsulent chacun une méthode. Lorsque `cd3` est instancié, il a une liste d’appel de deux méthodes, `M1` et `M2`, dans cet ordre. la liste d’appel de `cd4` contient `M1`, `M2` et `M1`, dans cet ordre. Enfin, la liste d’appel de `cd5` contient `M1`, `M2`, `M1`, `M1` et `M2`, dans cet ordre. Pour obtenir plus d’exemples de combinaison (et de suppression) de délégués, consultez [appel de délégué](delegates.md#delegate-invocation).

## <a name="delegate-compatibility"></a>Déléguer la compatibilité

Une méthode ou un délégué `M` est ***compatible*** avec un type délégué `D` si tous les éléments suivants sont vrais :

*  `D` et `M` ont le même nombre de paramètres, et chaque paramètre dans `D` a les mêmes modificateurs `ref` ou `out` que le paramètre correspondant dans `M`.
*  Pour chaque paramètre de valeur (un paramètre sans @no__t modificateur-0 ou `out`), une conversion d’identité ([conversion d’identité](conversions.md#identity-conversion)) ou une conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe à partir du type de paramètre dans `D` vers le type de paramètre correspondant dans `M`.
*  Pour chaque paramètre `ref` ou `out`, le type de paramètre dans `D` est le même que le type de paramètre dans `M`.
*  Une conversion d’identité ou de référence implicite existe du type de retour de `M` au type de retour de `D`.

## <a name="delegate-instantiation"></a>Instanciation de délégué

Une instance d’un délégué est créée par un *delegate_creation_expression* ([expressions de création de délégué](expressions.md#delegate-creation-expressions)) ou une conversion vers un type délégué. L’instance de délégué nouvellement créée fait référence à l’une des deux :

*  Méthode statique référencée dans *delegate_creation_expression*, ou
*  L’objet cible (qui ne peut pas être `null`) et la méthode d’instance référencée dans *delegate_creation_expression*, ou
*  Autre délégué.

Exemple :

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public void M2(int i) {...}
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);        // static method
        C t = new C();
        D cd2 = new D(t.M2);        // instance method
        D cd3 = new D(cd2);        // another delegate
    }
}
```

Une fois instanciés, les instances de délégué font toujours référence aux mêmes objet et méthode cible. Rappelez-vous que lorsque deux délégués sont combinés, ou lorsqu’un nouveau délégué est supprimé d’un autre, un nouveau délégué est obtenu avec sa propre liste d’appel ; les listes d’appel des délégués combinés ou supprimés restent inchangées.

## <a name="delegate-invocation"></a>Appel de délégué

C#fournit une syntaxe spéciale pour appeler un délégué. Lorsqu’une instance de délégué non null dont la liste d’appel contient une entrée est appelée, elle appelle la méthode à l’aide des mêmes arguments qu’elle a été donnée et retourne la même valeur que la méthode référencée. (Pour plus d’informations sur l’appel de délégué, consultez [appel de délégué](expressions.md#delegate-invocations) .) Si une exception se produit pendant l’appel d’un tel délégué et que cette exception n’est pas interceptée dans la méthode appelée, la recherche d’une clause catch d’exception continue dans la méthode qui a appelé le délégué, comme si cette méthode avait directement appelé méthode à laquelle ce délégué a été désigné.

L’appel d’une instance de délégué dont la liste d’appel contient plusieurs entrées se poursuit en appelant chaque méthode de la liste d’appel, de manière synchrone, dans l’ordre. Chaque méthode appelée reçoit le même jeu d’arguments que celui qui a été donné à l’instance de délégué. Si un tel appel de délégué comprend des paramètres de référence ([paramètres de référence](classes.md#reference-parameters)), chaque appel de méthode se produit avec une référence à la même variable ; les modifications apportées à cette variable par une méthode dans la liste d’appel seront visibles par les méthodes plus loin dans la liste d’appel. Si l’appel de délégué comprend des paramètres de sortie ou une valeur de retour, la valeur finale provient de l’appel du dernier délégué de la liste.

Si une exception se produit pendant le traitement de l’appel d’un délégué de ce type, et si cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception continue dans la méthode qui a appelé le délégué, et toutes les méthodes plus loin la liste d’appel n’est pas appelée.

Toute tentative d’appel d’une instance de délégué dont la valeur est null entraîne une exception de type `System.NullReferenceException`.

L’exemple suivant montre comment instancier, combiner, supprimer et appeler des délégués :

```csharp
using System;

delegate void D(int x);

class C
{
    public static void M1(int i) {
        Console.WriteLine("C.M1: " + i);
    }

    public static void M2(int i) {
        Console.WriteLine("C.M2: " + i);
    }

    public void M3(int i) {
        Console.WriteLine("C.M3: " + i);
    }
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);
        cd1(-1);                // call M1

        D cd2 = new D(C.M2);
        cd2(-2);                // call M2

        D cd3 = cd1 + cd2;
        cd3(10);                // call M1 then M2

        cd3 += cd1;
        cd3(20);                // call M1, M2, then M1

        C c = new C();
        D cd4 = new D(c.M3);
        cd3 += cd4;
        cd3(30);                // call M1, M2, M1, then M3

        cd3 -= cd1;             // remove last M1
        cd3(40);                // call M1, M2, then M3

        cd3 -= cd4;
        cd3(50);                // call M1 then M2

        cd3 -= cd2;
        cd3(60);                // call M1

        cd3 -= cd2;             // impossible removal is benign
        cd3(60);                // call M1

        cd3 -= cd1;             // invocation list is empty so cd3 is null

        cd3(70);                // System.NullReferenceException thrown

        cd3 -= cd1;             // impossible removal is benign
    }
}
```

Comme indiqué dans l’instruction `cd3 += cd1;`, un délégué peut être présent plusieurs fois dans une liste d’appel. Dans ce cas, elle est simplement appelée une fois par occurrence. Dans une liste d’appel telle que celle-ci, lorsque ce délégué est supprimé, la dernière occurrence dans la liste d’appel est celle qui est réellement supprimée.

Juste avant l’exécution de la dernière instruction, `cd3 -= cd1;`, le délégué `cd3` fait référence à une liste d’appel vide. Toute tentative de suppression d’un délégué d’une liste vide (ou de suppression d’un délégué inexistant d’une liste non vide) n’est pas une erreur.

La sortie produite est la suivante :

```console
C.M1: -1
C.M2: -2
C.M1: 10
C.M2: 10
C.M1: 20
C.M2: 20
C.M1: 20
C.M1: 30
C.M2: 30
C.M1: 30
C.M3: 30
C.M1: 40
C.M2: 40
C.M3: 40
C.M1: 50
C.M2: 50
C.M1: 60
C.M1: 60
```
