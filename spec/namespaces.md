---
ms.openlocfilehash: 3232163ed91d9d8bb6b0babf94c4282bfd60976c
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488934"
---
# <a name="namespaces"></a>Espaces de noms

Les programmes c# sont organisés à l’aide d’espaces de noms. Espaces de noms sont utilisés comme un système d’organisation « interne » pour un programme et comme un système d’organisation « external » : une façon de présenter des éléments de programme qui sont exposés à d’autres programmes.

Directives using ([à l’aide de directives](namespaces.md#using-directives)) sont fournies pour faciliter l’utilisation des espaces de noms.

## <a name="compilation-units"></a>Unités de compilation

Un *compilation_unit* définit la structure globale d’un fichier source. Une unité de compilation se compose de zéro ou plusieurs *using_directive*s suivie de zéro ou plusieurs *global_attributes* suivie de zéro ou plusieurs *namespace_member_declaration*s .

```antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? namespace_member_declaration*
    ;
```

Un programme c# se compose d’une ou plusieurs unités de compilation, tous contenus dans un fichier source séparé. Lorsqu’un programme c# est compilé, toutes les unités de compilation sont traités ensemble. Par conséquent, les unités de compilation peuvent dépendre eux, éventuellement de manière circulaire.

Le *using_directive*s d’un effet d’unité de compilation la *global_attributes* et *namespace_member_declaration*s de cette unité de compilation, mais n’ont aucun effet autres unités de compilation.

Le *global_attributes* ([attributs](attributes.md)) d’une unité de compilation permettent la spécification d’attributs pour l’assembly cible et le module. Les modules et assemblys agissent comme conteneurs physiques pour les types. Un assembly peut se composer de plusieurs modules distincts.

Le *namespace_member_declaration*s de chaque unité de compilation d’un programme fournissent des membres à un espace de déclaration unique appelée espace de noms global. Exemple :

Fichier `A.cs`:
```csharp
class A {}
```

Fichier `B.cs`:
```csharp
class B {}
```

Les unités de deux compilation contribuent à l’espace de noms global unique, dans ce cas déclare deux classes dont les noms qualifiés complets `A` et `B`. Étant donné que les unités de deux compilation contribuent au même espace de déclaration, il aurait été une erreur si les deux contenaient une déclaration d’un membre portant le même nom.

## <a name="namespace-declarations"></a>Déclarations d'espaces de noms

Un *namespace_declaration* se compose du mot clé `namespace`, suivie d’un espace de noms et le corps, éventuellement suivie d’un point-virgule.

```antlr
namespace_declaration
    : 'namespace' qualified_identifier namespace_body ';'?
    ;

qualified_identifier
    : identifier ('.' identifier)*
    ;

namespace_body
    : '{' extern_alias_directive* using_directive* namespace_member_declaration* '}'
    ;
```

Un *namespace_declaration* peut se produire dans une déclaration de niveau supérieur dans un *compilation_unit* ou comme une déclaration de membre dans une autre *namespace_declaration*. Quand un *namespace_declaration* se produit en tant que déclaration de niveau supérieur dans un *compilation_unit*, l’espace de noms devient membre de l’espace de noms global. Quand un *namespace_declaration* se produit dans une autre *namespace_declaration*, l’espace de noms interne devient un membre de l’espace de noms externe. Dans les deux cas, le nom d’un espace de noms doit être unique au sein de l’espace de noms qui le contient.

Espaces de noms sont implicitement `public` et la déclaration d’un espace de noms ne peut pas inclure des modificateurs d’accès.

Au sein d’un *namespace_body*, le paramètre facultatif *using_directive*s importer les noms des autres espaces de noms, les types et membres, ce qui leur permet d’être référencées directement au lieu de par le biais des noms qualifiés. Le paramètre facultatif *namespace_member_declaration*s fournissent des membres à l’espace de déclaration de l’espace de noms. Notez que tous les *using_directive*s doit apparaître avant les déclarations de membre.

Le *qualified_identifier* d’un *namespace_declaration* peut être un identificateur unique ou une séquence d’identificateurs séparés par «`.`« jetons. Cette dernière forme autorise un programme pour définir un espace de noms imbriqué sans imbriquer lexicalement plusieurs déclarations d’espace de noms. Par exemple :

```csharp
namespace N1.N2
{
    class A {}

    class B {}
}
```
est sémantiquement équivalente à
```csharp
namespace N1
{
    namespace N2
    {
        class A {}

        class B {}
    }
}
```

Espaces de noms sont ouverts, et deux déclarations d’espace de noms portant le même nom qualifié complet contribuent au même espace de déclaration ([déclarations](basic-concepts.md#declarations)). Dans l’exemple
```csharp
namespace N1.N2
{
    class A {}
}

namespace N1.N2
{
    class B {}
}
```
les deux déclarations d’espace de noms contribuent au même espace de déclaration, dans ce cas déclare deux classes dont les noms qualifiés complets `N1.N2.A` et `N1.N2.B`. Étant donné que les deux déclarations contribuent au même espace de déclaration, il aurait été une erreur si chaque contenait une déclaration d’un membre portant le même nom.

## <a name="extern-aliases"></a>Alias extern

Un *extern_alias_directive* présente un identificateur qui sert d’alias pour un espace de noms. La spécification de l’espace de noms d’alias est externe au code source du programme et s’applique également à des espaces de noms imbriqués de l’espace de noms d’alias.

```antlr
extern_alias_directive
    : 'extern' 'alias' identifier ';'
    ;
```

L’étendue d’un *extern_alias_directive* s’étend sur la *using_directive*s, *global_attributes* et *namespace_member_declaration*s de son corps d’unité ou un espace de noms compilation conteneur immédiat.

Dans un corps de compilation unité ou un espace de noms qui contient un *extern_alias_directive*, l’identificateur introduit par le *extern_alias_directive* peut être utilisé pour référencer l’espace de noms d’alias. Il s’agit d’une erreur lors de la compilation pour le *identificateur* du mot `global`.

Un *extern_alias_directive* rend un alias disponible dans un corps d’unité ou un espace de noms de compilation particulier, mais il ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent. En d’autres termes, une *extern_alias_directive* n’est pas transitive, mais, au lieu de cela, affecte uniquement la compilation unité ou un espace de noms du corps dans lequel il se produit.

Le programme suivant déclare et utilise deux alias extern, `X` et `Y`, chacun de qui représentent la racine d’une hiérarchie de l’espace de noms distincts :
```csharp
extern alias X;
extern alias Y;

class Test
{
    X::N.A a;
    X::N.B b1;
    Y::N.B b2;
    Y::N.C c;
}
```

Le programme déclare l’existence d’extern alias `X` et `Y`, mais les définitions actuelles des alias sont externes au programme. Portant le même nom `N.B` classes peuvent désormais être référencées en tant que `X.N.B` et `Y.N.B`, ou à l’aide du qualificateur d’alias d’espace de noms, `X::N.B` et `Y::N.B`. Une erreur se produit si un programme déclare un alias extern pour lesquels aucune définition externe n’est fournie.

## <a name="using-directives"></a>directives using

***À l’aide de directives*** facilitent l’utilisation des espaces de noms et les types définis dans d’autres espaces de noms. À l’aide d’impact sur les directives du processus de résolution de noms de *namespace_or_type_name*s ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) et *simple_name*s ([noms simples ](expressions.md#simple-names)), mais contrairement aux déclarations, à l’aide de directives ne contribuent pas de nouveaux membres pour les espaces de déclaration sous-jacente des unités de compilation ou espaces de noms dans lequel ils sont utilisés.

```antlr
using_directive
    : using_alias_directive
    | using_namespace_directive
    | using_static_directive
    ;
```

Un *using_alias_directive* ([à l’aide de directives d’alias](namespaces.md#using-alias-directives)) introduit un alias pour un espace de noms ou un type.

Un *using_namespace_directive* ([à l’aide de directives d’espace de noms](namespaces.md#using-namespace-directives)) importe les membres de type d’un espace de noms.

Un *using_static_directive* ([directives Using static](namespaces.md#using-static-directives)) importe les types imbriqués et les membres statiques d’un type.

L’étendue d’un *using_directive* s’étend sur la *namespace_member_declaration*s de son corps d’unité ou un espace de noms compilation conteneur immédiat. L’étendue d’un *using_directive* n’inclut pas spécifiquement son homologue *using_directive*s. Par conséquent, de l’homologue *using_directive*s n’affectent pas les uns des autres, et l’ordre dans lequel ils sont écrits n’est pas significatif.

### <a name="using-alias-directives"></a>À l’aide de directives d’alias

Un *using_alias_directive* présente un identificateur qui sert d’alias pour un espace de noms ou un type dans le corps d’unité ou un espace de noms compilation immédiatement englobant.

```antlr
using_alias_directive
    : 'using' identifier '=' namespace_or_type_name ';'
    ;
```

Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_alias_directive*, l’identificateur introduit par le *using_alias_directive* peut être utilisé pour référencer le donné espace de noms ou type. Exemple :
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;

    class B: A {}
}
```

Ci-dessus, dans les déclarations de membre dans le `N3` espace de noms, `A` est un alias pour `N1.N2.A`et par conséquent, la classe `N3.B` dérive de la classe `N1.N2.A`. Le même effet peut être obtenu en créant un alias `R` pour `N1.N2` et y faire référence `R.A`:
```csharp
namespace N3
{
    using R = N1.N2;

    class B: R.A {}
}
```

Le *identificateur* d’un *using_alias_directive* doit être unique dans l’espace de déclaration de l’unité de compilation ou d’un espace de noms qui contient le *using_alias_directive* . Exemple :
```csharp
namespace N3
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;        // Error, A already exists
}
```

Ci-dessus, `N3` contient déjà un membre `A`, de sorte que c’est une erreur de compilation pour un *using_alias_directive* à utiliser cet identificateur. De même, il est une erreur de compilation pour au moins deux *using_alias_directive*s dans la même compilation unité ou un espace de noms corps pour déclarer des alias par le même nom.

Un *using_alias_directive* rend un alias disponible dans un corps d’unité ou un espace de noms de compilation particulier, mais il ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent. En d’autres termes, une *using_alias_directive* n’est pas transitive, mais au lieu de cela affecte uniquement la compilation unité ou un espace de noms du corps dans lequel il se produit. Dans l’exemple
```csharp
namespace N3
{
    using R = N1.N2;
}

namespace N3
{
    class B: R.A {}            // Error, R unknown
}
```
l’étendue de la *using_alias_directive* qui introduit `R` s’étend uniquement aux déclarations de membre dans le corps de l’espace de noms dans lequel elle est contenue, donc `R` est inconnu dans la seconde déclaration d’espace de noms. Toutefois, en plaçant le *using_alias_directive* dans la compilation conteneur unité entraîne l’alias devient disponible dans les deux déclarations d’espace de noms :
```csharp
using R = N1.N2;

namespace N3
{
    class B: R.A {}
}

namespace N3
{
    class C: R.A {}
}
```

Tout comme les membres standard, les noms introduits par *using_alias_directive*s sont masqués par des membres portant le même nom dans les portées imbriquées. Dans l’exemple
```csharp
using R = N1.N2;

namespace N3
{
    class R {}

    class B: R.A {}        // Error, R has no member A
}
```
la référence à `R.A` dans la déclaration de `B` provoque une erreur de compilation, car `R` fait référence à `N3.R`, et non `N1.N2`.

L’ordre dans lequel *using_alias_directive*s sont écrits n’a aucune signification et la résolution de la *namespace_or_type_name* référencé par un *using_alias_directive*n’est pas affectée par le *using_alias_directive* lui-même ou par d’autres *using_directive*s dans le corps d’unité ou un espace de noms compilation conteneur immédiat. En d’autres termes, le *namespace_or_type_name* d’un *using_alias_directive* est résolue comme si le corps d’unité ou un espace de noms conteneur immédiat compilation n’avait pas *using_directive*s. Un *using_alias_directive* peut toutefois être affecté par *extern_alias_directive*s dans le corps d’unité ou un espace de noms compilation conteneur immédiat. Dans l’exemple
```csharp
namespace N1.N2 {}

namespace N3
{
    extern alias E;

    using R1 = E.N;        // OK

    using R2 = N1;         // OK

    using R3 = N1.N2;      // OK

    using R4 = R2.N2;      // Error, R2 unknown
}
```
la dernière *using_alias_directive* entraîne une erreur de compilation, car il n’est pas affecté par la première *using_alias_directive*. La première *using_alias_directive* n’entraîne pas une erreur depuis l’étendue de l’alias extern `E` inclut le *using_alias_directive*.

Un *using_alias_directive* pouvez créer un alias pour n’importe quel espace de noms ou un type, y compris l’espace de noms dans lequel il apparaît et tout espace de noms ou un type imbriqué dans cet espace de noms.

L’accès d’un espace de noms ou un type via un alias produit exactement le même résultat que l’accès à cet espace de noms ou d’un type par son nom déclaré. Par exemple, étant donné
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using R1 = N1;
    using R2 = N1.N2;

    class B
    {
        N1.N2.A a;            // refers to N1.N2.A
        R1.N2.A b;            // refers to N1.N2.A
        R2.A c;               // refers to N1.N2.A
    }
}
```
les noms `N1.N2.A`, `R1.N2.A`, et `R2.A` sont équivalent et tous font référence à la classe dont le nom qualifié complet est `N1.N2.A`.

L’utilisation d’alias peut le nom d’un type construit fermé, mais ne peut pas de nom d’une déclaration de type générique indépendant sans fournir d’arguments de type. Exemple :
```csharp
namespace N1
{
    class A<T>
    {
        class B {}
    }
}

namespace N2
{
    using W = N1.A;          // Error, cannot name unbound generic type

    using X = N1.A.B;        // Error, cannot name unbound generic type

    using Y = N1.A<int>;     // Ok, can name closed constructed type

    using Z<T> = N1.A<T>;    // Error, using alias cannot have type parameters
}
```

### <a name="using-namespace-directives"></a>À l’aide de directives d’espace de noms

Un *using_namespace_directive* importe les types contenus dans un espace de noms dans l’immédiatement englobante compilation unité ou un espace de noms corps, l’activation de l’identificateur de chaque type à être utilisé sans qualification.

```antlr
using_namespace_directive
    : 'using' namespace_name ';'
    ;
```

Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_namespace_directive*, les types contenus dans l’espace de noms donné peuvent être référencés directement. Exemple :
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1.N2;

    class B: A {}
}
```

Ci-dessus, dans les déclarations de membre dans le `N3` espace de noms, les membres de type de `N1.N2` sont directement disponibles et par conséquent, la classe `N3.B` dérive de la classe `N1.N2.A`.

Un *using_namespace_directive* importe les types contenus dans l’espace de noms donné, mais plus précisément n’importe pas les espaces de noms imbriqués. Dans l’exemple
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1;

    class B: N2.A {}        // Error, N2 unknown
}
```
le *using_namespace_directive* importe les types contenus dans `N1`, mais pas les espaces de noms imbriqués dans `N1`. Par conséquent, la référence à `N2.A` dans la déclaration de `B` génère une erreur de compilation, car aucun membre ne nommé `N2` sont dans la portée.

Contrairement à un *using_alias_directive*, un *using_namespace_directive* peut importer les types dont les identificateurs sont déjà définis dans le corps d’unité ou un espace de noms englobant compilation. En effet, les noms importés par un *using_namespace_directive* sont masqués par des membres portant le même nom dans le corps d’unité ou un espace de noms englobant compilation. Exemple :
```csharp
namespace N1.N2
{
    class A {}

    class B {}
}

namespace N3
{
    using N1.N2;

    class A {}
}
```

Ici, dans les déclarations de membre dans le `N3` espace de noms, `A` fait référence à `N3.A` plutôt que `N1.N2.A`.

Lorsque plus d’un espace de noms ou le type importé par *using_namespace_directive*s ou *using_static_directive*s dans le même corps de compilation unité ou un espace de noms contiennent des types portant le même nom, les références à Ce nom comme un *type_name* sont considérés comme ambiguë. Dans l’exemple
```csharp
namespace N1
{
    class A {}
}

namespace N2
{
    class A {}
}

namespace N3
{
    using N1;

    using N2;

    class B: A {}                // Error, A is ambiguous
}
```
les deux `N1` et `N2` contiennent un membre `A`et parce que `N3` importe les deux, faisant référence à `A` dans `N3` est une erreur de compilation. Dans ce cas, le conflit peut être résolu par la qualification des références à `A`, ou en introduisant un *using_alias_directive* qui sélectionne un particulier `A`. Exemple :
```csharp
namespace N3
{
    using N1;

    using N2;

    using A = N1.A;

    class B: A {}                // A means N1.A
}
```

En outre, lorsque plus d’un espace de noms ou le type importé par *using_namespace_directive*s ou *using_static_directive*s dans le même corps de compilation unité ou un espace de noms contiennent des types ou membres par le même nom, fait référence à ce nom comme un *simple_name* sont considérés comme ambiguë. Dans l’exemple
```csharp
namespace N1
{
    class A {}
}

class C
{
    public static int A;
}

namespace N2
{
    using N1;
    using static C;

    class B
    {
        void M() 
        { 
            A a = new A();   // Ok, A is unambiguous as a type-name
            A.Equals(2);     // Error, A is ambiguous as a simple-name
        }
    }
}
```
`N1` contient un membre de type `A`, et `C` contient un champ statique `A`et parce que `N2` importe les deux, faisant référence à `A` comme un *simple_name* est ambigu et un moment de la compilation erreur. 

Comme un *using_alias_directive*, un *using_namespace_directive* ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent de l’unité de compilation ou d’un espace de noms, mais affecte uniquement le compilation unité corps ou d’espace de noms dans lequel elle apparaît.

Le *nom_espace_de_noms* référencé par un *using_namespace_directive* est résolu dans la même façon que le *namespace_or_type_name* référencé par un  *using_alias_directive*. Par conséquent, *using_namespace_directive*s dans le même corps d’unité ou un espace de noms de compilation n’affectent pas les uns des autres et peuvent être écrites dans n’importe quel ordre.

### <a name="using-static-directives"></a>Directives using static

Un *using_static_directive* importe les types imbriqués et les membres statiques contenus directement dans une déclaration de type dans l’immédiatement englobante compilation unité ou un espace de noms corps, l’activation de l’identificateur de chaque membre et le type à être utilisé sans qualification.

```antlr
using_static_directive
    : 'using' 'static' type_name ';'
    ;
```

Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_static_directive*, l’accessibles imbriqué types et membres statiques (à l’exception des méthodes d’extension) contenus directement dans la déclaration de la type donné peut être référencé directement. Exemple :

```csharp
namespace N1
{
    class A 
    {
        public class B{}
        public static B M(){ return new B(); }
    }
}

namespace N2
{
    using static N1.A;
    class C
    {
        void N() { B b = M(); }
    }
}
```

Ci-dessus, dans les déclarations de membre dans le `N2` espace de noms, les membres statiques et les types imbriqués de `N1.A` sont directement disponibles et par conséquent, la méthode `N` est en mesure de faire référence à la fois le `B` et `M` membres de `N1.A`.

Un *using_static_directive* spécifiquement n’importe pas les méthodes d’extension directement en tant que méthodes statiques, mais les rend disponibles pour l’appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)). Dans l’exemple

```csharp
namespace N1 
{
    static class A 
    {
        public static void M(this string s){}
    }
}

namespace N2
{
    using static N1.A;

    class B
    {
        void N() 
        {
            M("A");      // Error, M unknown
            "B".M();     // Ok, M known as extension method
            N1.A.M("C"); // Ok, fully qualified
        }
    }
}
```
le *using_static_directive* importe la méthode d’extension `M` contenue dans `N1.A`, mais uniquement comme une méthode d’extension. Par conséquent, la première référence à `M` dans le corps de `B.N` génère une erreur de compilation, car aucun membre ne nommé `M` sont dans la portée.

Un *using_static_directive* importe uniquement les membres et types déclarés directement dans le type donné, pas les types et les membres déclarés dans les classes de base.

TODO: Exemple

Ambiguïtés entre plusieurs *using_namespace_directives* et *using_static_directives* sont abordées dans [à l’aide de directives d’espace de noms](namespaces.md#using-namespace-directives).

## <a name="namespace-members"></a>Membres de Namespace

Un *namespace_member_declaration* est soit un *namespace_declaration* ([Namespace déclarations](namespaces.md#namespace-declarations)) ou un *type_declaration* () [Les déclarations de type](namespaces.md#type-declarations)).

```antlr
namespace_member_declaration
    : namespace_declaration
    | type_declaration
    ;
```

Une unité de compilation ou un corps de l’espace de noms peut contenir *namespace_member_declaration*s et ces déclarations contribueront de nouveaux membres à l’espace de déclaration sous-jacent contenant compilation unité ou un espace de noms du corps de la.

## <a name="type-declarations"></a>Déclarations de type

Un *type_declaration* est un *class_declaration* ([déclarations de classe](classes.md#class-declarations)), un *struct_declaration* ([Struct déclarations](structs.md#struct-declarations)), un *interface_declaration* ([déclarations d’Interface](interfaces.md#interface-declarations)), un *enum_declaration* ([Enum déclarations](enums.md#enum-declarations)), ou un *delegate_declaration* ([déclarations Delegate](delegates.md#delegate-declarations)).

```antlr
type_declaration
    : class_declaration
    | struct_declaration
    | interface_declaration
    | enum_declaration
    | delegate_declaration
    ;
```

Un *type_declaration* peut se produire sous la forme d’une déclaration de niveau supérieur dans une unité de compilation ou une déclaration de membre dans un espace de noms, une classe ou un struct.

Lorsqu’une déclaration de type pour un type `T` se produit en tant que déclaration de niveau supérieur dans une unité de compilation, le nom qualifié complet du type qui vient d’être déclaré est simplement `T`. Lorsqu’une déclaration de type pour un type `T` se produit dans un espace de noms, classe ou struct, le nom qualifié complet du type qui vient d’être déclaré est `N.T`, où `N` est le nom qualifié complet de l’espace de noms, classe ou struct qui le contient.

Un type déclaré dans une classe ou struct est appelé type imbriqué ([les types imbriqués](classes.md#nested-types)).

Les modificateurs d’accès autorisé et l’accès par défaut pour une déclaration de type dépendent du contexte dans lequel la déclaration a lieu ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) :

*  Les types déclarés dans les unités de compilation ou d’espaces de noms peuvent avoir `public` ou `internal` accès. La valeur par défaut est `internal` accès.
*  Les types déclarés dans les classes peuvent avoir `public`, `protected internal`, `protected`, `internal`, ou `private` accès. La valeur par défaut est `private` accès.
*  Les types déclarés dans les structures peuvent avoir `public`, `internal`, ou `private` accès. La valeur par défaut est `private` accès.

## <a name="namespace-alias-qualifiers"></a>Qualificateurs d’alias Namespace

Le ***qualificateur d’alias d’espace de noms*** `::` rend possible garantir que les recherches de nom de type ne sont pas affectés par l’introduction de nouveaux types et membres. Le qualificateur d’alias d’espace de noms apparaît toujours entre deux identificateurs sont appelés les identificateurs de gauche et droite. Contrairement à la mise à jour `.` qualificateur, l’identificateur de gauche de la `::` qualificateur s’effectue la recherche à distance uniquement comme un extern ou à l’aide d’alias.

Un *qualified_alias_member* est défini comme suit :

```antlr
qualified_alias_member
    : identifier '::' identifier type_argument_list?
    ;
```

Un *qualified_alias_member* peut être utilisé comme un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) ou en tant que l’opérande gauche dans un *member_access* ([L’accès au membre](expressions.md#member-access)).

Un *qualified_alias_member* a une des deux formes :

*  `N::I<A1, ..., Ak>`, où `N` et `I` représentent des identificateurs, et `<A1, ..., Ak>` est une liste d’arguments de type. (`K` est toujours au moins un.)
*  `N::I`, où `N` et `I` représentent des identificateurs. (Dans ce cas, `K` est considéré comme étant égal à zéro.)

À l’aide de cette notation, la signification d’un *qualified_alias_member* est déterminée comme suit :

*  Si `N` est l’identificateur `global`, puis est recherchée dans l’espace de noms global `I`:
   * Si l’espace de noms global contient un espace de noms nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à cet espace de noms.
   * Sinon, si l’espace de noms global contient un type non générique nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à ce type.
   * Sinon, si l’espace de noms global contient un type nommé `I` qui a `K`  paramètres de type, puis le *qualified_alias_member* fait référence à ce type construit avec les arguments de type donné.
   * Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.

*  Sinon, en commençant par la déclaration d’espace de noms ([Namespace déclarations](namespaces.md#namespace-declarations)) immédiatement contenant le *qualified_alias_member* (le cas échéant), poursuite de l’opération avec chaque déclaration d’espace de noms englobant (le cas échéant) et se terminant par l’unité de compilation contenant le *qualified_alias_member*, les étapes suivantes sont évaluées jusqu'à ce qu’une entité se trouve :

   * Si l’unité de compilation ou de la déclaration d’espace de noms contient un *using_alias_directive* qui associe `N` avec un type, puis le *qualified_alias_member* n’est pas défini et le moment de la compilation erreur se produit.
   * Sinon, si l’unité de compilation ou de la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe `N` avec un espace de noms, puis :
     * Si l’espace de noms associé à `N` contient un espace de noms nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à cet espace de noms.
     * Sinon, si l’espace de noms associé à `N` contient un type non générique nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à ce type.
     * Sinon, si l’espace de noms associé à `N` contient un type nommé `I` qui a `K`  paramètres de type, puis le *qualified_alias_member* fait référence à que type construit avec les arguments de type donné.
     * Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.
*  Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.

Notez que l’utilisation du qualificateur d’alias d’espace de noms avec un alias qui référence un type entraîne une erreur de compilation. Notez que si l’identificateur `N` est `global`, puis recherche est effectuée dans l’espace de noms global, même s’il existe une association d’alias en utilisant `global` avec un type ou d’un espace de noms.

### <a name="uniqueness-of-aliases"></a>Unicité d’alias

Chaque corps de la compilation unité et espace de noms a un espace de déclaration distincte pour les alias externes et l’utilisation d’alias. Par conséquent, bien que le nom d’un alias extern ou à l’aide d’alias doit être unique dans le jeu d’alias extern et l’utilisation d’alias déclarées dans le corps d’unité ou un espace de noms compilation conteneur immédiat, un alias est autorisé à avoir le même nom en tant que type ou espace de noms tant que j’ai t est utilisé uniquement avec le `::` qualificateur.

Dans l’exemple
```csharp
namespace N
{
    public class A {}

    public class B {}
}

namespace N
{
    using A = System.IO;

    class X
    {
        A.Stream s1;            // Error, A is ambiguous

        A::Stream s2;           // Ok
    }
}
```
le nom `A` a deux significations possibles dans le corps de la deuxième espace de noms, car les deux la classe `A` et à l’aide de l’alias `A` sont dans la portée. Pour cette raison, l’utilisation de `A` dans le nom qualifié `A.Stream` est ambigu et provoque une erreur de compilation se produise. Toutefois, utiliser des `A` avec la `::` qualificateur n’est pas une erreur, car `A` est recherchée uniquement comme un alias d’espace de noms.
