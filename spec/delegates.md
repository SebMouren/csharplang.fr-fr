---
ms.openlocfilehash: 08c14d9ef2afe30580f456995066c141653ede92
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488980"
---
# <a name="delegates"></a>Délégués

Les délégués activent des scénarios que d’autres langages, tels que C++, Pascal et Modula--ont abordés avec des pointeurs de fonction. Toutefois, contrairement aux pointeurs de fonction C++, les délégués sont entièrement orienté objet et contrairement aux pointeurs C++ vers des fonctions membres, les délégués encapsuler une instance d’objet et une méthode.

Une déclaration delegate définit une classe qui est dérivée de la classe `System.Delegate`. Une instance de délégué encapsule une liste d’appel, qui est une liste d’une ou plusieurs méthodes, chacune d’elles est appelé une entité pouvant être appelée. Par exemple méthodes, une entité pouvant être appelée se compose d’une instance et une méthode sur cette instance. Pour les méthodes statiques, une entité pouvant être appelée est simplement une méthode. Appel d’une instance de délégué avec un ensemble approprié d’arguments, chacune des entités pouvant être appelées du délégué à appeler avec l’ensemble donné d’arguments.

Une propriété intéressante et utile d’une instance de délégué est qu’il ne pas savoir ou en charge sur les classes des méthodes qu’il encapsule ; qui importe est que ces méthodes soient compatibles ([déclarations Delegate](delegates.md#delegate-declarations)) avec le type du délégué. Cela rend les délégués parfaitement adaptés pour les appels « anonyme ».

## <a name="delegate-declarations"></a>Déclarations de délégué

Un *delegate_declaration* est un *type_declaration* ([les déclarations de Type](namespaces.md#type-declarations)) qui déclare un nouveau type délégué.

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

Il s’agit d’une erreur lors de la compilation pour le même modificateur apparaît plusieurs fois dans une déclaration delegate.

Le `new` modificateur est autorisé uniquement sur les délégués déclarés dans un autre type, auquel cas il spécifie qu’un tel délégué masque un membre hérité par le même nom, comme décrit dans [le nouveau modificateur](classes.md#the-new-modifier).

Le `public`, `protected`, `internal`, et `private` modificateurs contrôlent l’accessibilité du type délégué. En fonction du contexte dans lequel la déclaration du délégué se produit, certains de ces modificateurs ne sont pas autorisées ([accessibilité déclarée](basic-concepts.md#declared-accessibility)).

Le nom du type délégué est *identificateur*.

Le paramètre facultatif *formal_parameter_list* spécifie les paramètres du délégué, et *return_type* indique le type de retour du délégué.

Le paramètre facultatif *variant_type_parameter_list* ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)) spécifie les paramètres de type pour le délégué lui-même.

Le type de retour d’un type délégué doit être `void`, ou de sortie-safe ([sécurité de Variance](interfaces.md#variance-safety)).

Tous les types de paramètres formels d’un type délégué doivent être entrée-safe. En outre, les `out` ou `ref` types de paramètres doivent également être compatibles avec la sortie. Notez que même `out` paramètres sont requis pour être sécurisé à l’entrée, en raison d’une limitation de la plateforme sous-jacente de l’exécution.

Les types délégués en c# sont nom équivalent, pas structurellement équivalents. Plus précisément, deux types délégués différents qui ont le même paramètre répertorie et retourner le type sont considérés comme des types délégués différents. Toutefois, les instances de deux types délégué distincts mais structurellement équivalents peuvent considérés comme égaux ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).

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

Les méthodes `A.M1` et `B.M1 `sont compatibles avec les deux types de délégués `D1` et `D2` , dans la mesure où ils ont le même retourner le type et liste de paramètres ; Toutefois, ces types de délégué sont deux types différents, ils sont donc pas interchangeables. Les méthodes `B.M2`, `B.M3`, et `B.M4` ne sont pas compatibles avec les types délégués `D1` et `D2`, dans la mesure où ils ont différents types de retournés ou des listes de paramètres.

Comme les autres déclarations de type générique, les arguments de type doivent être accordées à créer un type délégué construit. Les types de paramètre et le type de retour d’un type délégué construit sont créés en substituant, pour chaque paramètre de type dans la déclaration du délégué, l’argument de type correspondant du type délégué construit. Le type de retour qui en résulte et les types de paramètres sont utilisés pour déterminer quelles méthodes sont compatibles avec un type délégué construit. Exemple :

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

La méthode `X.F` est compatible avec le type de délégué `Predicate<int>` et la méthode `X.G` est compatible avec le type de délégué `Predicate<string>` .

La seule façon de déclarer un type délégué est un *delegate_declaration*. Un type délégué est un type de classe qui est dérivé de `System.Delegate`. Types délégués sont implicitement `sealed`, donc il n’est pas possible de dériver de n’importe quel type d’un type délégué. Il est également possible de dériver un type de classe non délégué à partir de `System.Delegate`. Notez que `System.Delegate` est pas lui-même un délégué tapez ; c’est un type de classe à partir de laquelle tous les types délégués sont dérivés.

C# fournit une syntaxe spéciale pour le délégué l’instanciation et l’appel. À l’exception de l’instanciation, toute opération qui peut être appliquée à une classe ou une instance de classe peut également être appliquée à une instance, ou une classe déléguée respectivement. En particulier, il est possible d’accéder aux membres de la `System.Delegate` type via la syntaxe d’accès de membre habituel.

L’ensemble des méthodes encapsulées par une instance de délégué est appelé une liste d’appel. Création d’une instance de délégué ([compatibilité des délégués](delegates.md#delegate-compatibility)) à partir d’une méthode unique, elle encapsule cette méthode et sa liste d’appel contient une seule entrée. Toutefois, lorsque deux instances de délégué non null sont combinées, leurs listes d’appel sont concaténées--de gauche opérande de droite, pour former une nouvelle liste d’appel, qui contient deux ou plusieurs entrées.

Les délégués sont combinées à l’aide du fichier binaire `+` ([opérateur d’Addition](expressions.md#addition-operator)) et `+=` opérateurs ([assignation composée](expressions.md#compound-assignment)). Un délégué peut être supprimé à partir d’une combinaison de délégués, à l’aide du fichier binaire `-` ([opérateur de soustraction](expressions.md#subtraction-operator)) et `-=` opérateurs ([assignation composée](expressions.md#compound-assignment)). Les délégués peuvent être comparées pour l’égalité ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).

L’exemple suivant illustre l’instanciation d’un nombre de délégués, et répertorie leur appel correspondant :

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

Lorsque `cd1` et `cd2` sont instanciés, ils encapsulent chacun une méthode. Lorsque `cd3` est instancié, il a une liste d’appel des deux méthodes, `M1` et `M2`, dans cet ordre. `cd4`de la liste d’appel contient `M1`, `M2`, et `M1`, dans cet ordre. Enfin, `cd5`de liste d’appel contient `M1`, `M2`, `M1`, `M1`, et `M2`, dans cet ordre. Pour plus d’exemples de combinaison de délégués (ainsi que la suppression), consultez [appel de délégué](delegates.md#delegate-invocation).

## <a name="delegate-compatibility"></a>Compatibilité des délégués

Une méthode ou un délégué `M` est ***compatible*** avec un type délégué `D` si toutes les conditions suivantes sont remplies :

*  `D` et `M` ont le même nombre de paramètres et chaque paramètre dans `D` a les mêmes `ref` ou `out` modificateurs que le paramètre correspondant dans `M`.
*  Pour chaque paramètre de valeur (un paramètre sans aucune `ref` ou `out` modificateur), une conversion d’identité ([conversion d’identité](conversions.md#identity-conversion)) ou conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) Il existe à partir du type de paramètre dans `D` pour le type de paramètre correspondant dans `M`.
*  Pour chaque `ref` ou `out` paramètre, le type de paramètre dans `D` est le même que le type de paramètre dans `M`.
*  Il existe une identité ou une conversion de référence implicite du type de retour de `M` pour le type de retour de `D`.

## <a name="delegate-instantiation"></a>Instanciation des délégués

Une instance d’un délégué est créée par un *delegate_creation_expression* ([expressions de création de délégué](expressions.md#delegate-creation-expressions)) ou une conversion vers un type délégué. L’instance de délégué nouvellement créé fait ensuite référence à un :

*  La méthode statique référencée dans le *delegate_creation_expression*, ou
*  L’objet cible (qui ne peut pas être `null`) et l’instance de méthode référencée dans le *delegate_creation_expression*, ou
*  Un autre délégué.

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

Une fois instancié, instances de délégué consultez toujours le même objet cible et la méthode. N’oubliez pas, lorsque deux délégués sont combinés, ou un est supprimé d’un autre, un nouveau délégué des résultats avec sa propre liste d’appel ; les listes d’appel des délégués combinés ou supprimés restent inchangées.

## <a name="delegate-invocation"></a>Appel de délégué

C# fournit une syntaxe spéciale pour l’appel d’un délégué. Lorsqu’une instance de délégué non null dont la liste d’appel contient une entrée est appelée, elle appelle la méthode avec les mêmes arguments a été spécifié, elle retourne la même valeur que les données désignée à méthode. (Consultez [appels de délégué](expressions.md#delegate-invocations) pour plus d’informations sur l’appel de délégué.) Si une exception se produit pendant l’appel d’un délégué de ce type, et cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception se poursuit dans la méthode ayant appelé le délégué, comme si cette méthode avait appelé directement la méthode à laquelle ce délégué est appelée.

Appel d’une instance de délégué dont la liste d’appel contient plusieurs entrées se poursuit en appelant chacune des méthodes dans la liste d’appel, de façon synchrone, dans l’ordre. Chaque méthode ce qu’on appelé reçoit le même ensemble d’arguments comme a été spécifié pour l’instance de délégué. Si un appel de délégué de ce type inclut les paramètres de référence ([paramètres de référence](classes.md#reference-parameters)), chaque appel de méthode se produit avec une référence à la même variable ; les modifications apportées à cette variable par une méthode dans la liste d’appel seront méthodes plus visible dans la liste d’appel. Si l’appel de délégué comprend des paramètres de sortie ou une valeur de retour, leur valeur finale proviendront de l’appel du dernier délégué dans la liste.

Si une exception se produit pendant le traitement de l’appel d’un délégué de ce type, et cette exception n’est pas interceptée dans la méthode qui a été appelée, la recherche d’une clause catch d’exception se poursuit dans la méthode ayant appelé le délégué, et toutes les méthodes plus bas la liste d’appel ne sont pas appelés.

Tentative d’appel d’une instance de délégué dont la valeur est null lève une exception de type `System.NullReferenceException`.

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

Comme indiqué dans l’instruction `cd3 += cd1;`, un délégué peut être présent dans une liste d’appel plusieurs fois. Dans ce cas, il est simplement appelé une fois par occurrence. Dans une liste d’appel telle que celle-ci, lorsque ce délégué est supprimé, la dernière occurrence dans la liste d’appel est celui réellement supprimé.

Immédiatement avant l’exécution de l’instruction finale, `cd3 -= cd1;`, le délégué `cd3` fait référence à une liste d’appel vide. Une tentative pour supprimer un délégué d’une liste vide (ou pour supprimer un délégué inexistant dans une liste non vide) n’est pas une erreur.

La sortie produite est :

```
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
