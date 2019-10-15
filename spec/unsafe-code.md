---
ms.openlocfilehash: dbea611280a644adc25247b9887986e129c59b68
ms.sourcegitcommit: a5e393b018b04dfa55aae0000290ca087b508495
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72310362"
---
# <a name="unsafe-code"></a>Code unsafe

Le langage C# principal, tel que défini dans les chapitres précédents, diffère notamment de C C++ et de l’omission des pointeurs en tant que type de données. À la C# place, fournit des références et la capacité de créer des objets gérés par un garbage collector. Cette conception, couplée à d’autres fonctionnalités C# , rend un langage plus sûr que C C++ou. Dans le langage C# principal, il n’est tout simplement pas possible d’avoir une variable non initialisée, un pointeur « non résolu » ou une expression qui indexe un tableau au-delà de ses limites. Les catégories entières de bogues qui imposent C++ régulièrement le C et les programmes sont donc éliminées.

Bien que presque chaque construction de type pointeur en C++ C ou ait un équivalent de C#type référence dans, néanmoins, il existe des situations où l’accès aux types pointeur devient une nécessité. Par exemple, l’interfaçage avec le système d’exploitation sous-jacent, l’accès à un périphérique mappé en mémoire ou l’implémentation d’un algorithme de temps critique peut ne pas être possible ou pratique sans accès aux pointeurs. Pour répondre à ce besoin C# , offre la possibilité d’écrire du ***code non sécurisé***.

Dans du code non sécurisé, il est possible de déclarer et d’utiliser des pointeurs, d’effectuer des conversions entre les pointeurs et les types intégraux, de prendre l’adresse des variables, et ainsi de suite. Dans un sens, l’écriture de code non sécurisé est très similaire à l’écriture C# de code C dans un programme.

Le code non sécurisé est en fait une fonctionnalité « sécurisée » du point de vue des développeurs et des utilisateurs. Le code unsafe doit être clairement marqué avec le modificateur `unsafe`, de sorte que les développeurs ne peuvent pas utiliser accidentellement des fonctionnalités potentiellement dangereuses, et le moteur d’exécution fonctionne pour s’assurer que le code unsafe ne peut pas être exécuté dans un environnement non fiable.

## <a name="unsafe-contexts"></a>Contextes non sécurisés

Les fonctionnalités non sécurisées C# de sont disponibles uniquement dans les contextes non sécurisés. Un contexte unsafe est introduit en incluant un modificateur `unsafe` dans la déclaration d’un type ou d’un membre, ou en utilisant un *unsafe_statement*:

*  Une déclaration d’une classe, d’une structure, d’une interface ou d’un délégué peut inclure un modificateur `unsafe`, auquel cas la totalité de l’étendue textuelle de cette déclaration de type (y compris le corps de la classe, de la structure ou de l’interface) est considérée comme un contexte non sécurisé.
*  Une déclaration d’un champ, d’une méthode, d’une propriété, d’un événement, d’un indexeur, d’un opérateur, d’un constructeur d’instance, d’un destructeur ou d’un constructeur statique peut inclure un modificateur `unsafe`, auquel cas la totalité de l’étendue textuelle de cette déclaration de membre est considérée comme un contexte non sécurisé.
*  Un *unsafe_statement* permet l’utilisation d’un contexte non sécurisé au sein d’un *bloc*. La totalité de l’étendue textuelle du *bloc* associé est considérée comme un contexte non sécurisé.

Les productions grammaticales associées sont indiquées ci-dessous.

```antlr
class_modifier_unsafe
    : 'unsafe'
    ;

struct_modifier_unsafe
    : 'unsafe'
    ;

interface_modifier_unsafe
    : 'unsafe'
    ;

delegate_modifier_unsafe
    : 'unsafe'
    ;

field_modifier_unsafe
    : 'unsafe'
    ;

method_modifier_unsafe
    : 'unsafe'
    ;

property_modifier_unsafe
    : 'unsafe'
    ;

event_modifier_unsafe
    : 'unsafe'
    ;

indexer_modifier_unsafe
    : 'unsafe'
    ;

operator_modifier_unsafe
    : 'unsafe'
    ;

constructor_modifier_unsafe
    : 'unsafe'
    ;

destructor_declaration_unsafe
    : attributes? 'extern'? 'unsafe'? '~' identifier '(' ')' destructor_body
    | attributes? 'unsafe'? 'extern'? '~' identifier '(' ')' destructor_body
    ;

static_constructor_modifiers_unsafe
    : 'extern'? 'unsafe'? 'static'
    | 'unsafe'? 'extern'? 'static'
    | 'extern'? 'static' 'unsafe'?
    | 'unsafe'? 'static' 'extern'?
    | 'static' 'extern'? 'unsafe'?
    | 'static' 'unsafe'? 'extern'?
    ;

embedded_statement_unsafe
    : unsafe_statement
    | fixed_statement
    ;

unsafe_statement
    : 'unsafe' block
    ;
```

Dans l’exemple

```csharp
public unsafe struct Node
{
    public int Value;
    public Node* Left;
    public Node* Right;
}
```

le modificateur `unsafe` spécifié dans la déclaration de struct fait en sorte que la totalité de l’étendue textuelle de la déclaration de struct devienne un contexte non sécurisé. Par conséquent, il est possible de déclarer les champs `Left` et `Right` comme étant d’un type pointeur. L’exemple ci-dessus peut également être écrit

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

Ici, les modificateurs `unsafe` dans les déclarations de champ provoquent la prise en compte de contextes non sécurisés par ces déclarations.

En dehors de l’établissement d’un contexte non sécurisé, ce qui autorise l’utilisation de types pointeur, le modificateur `unsafe` n’a aucun effet sur un type ou un membre. Dans l’exemple

```csharp
public class A
{
    public unsafe virtual void F() {
        char* p;
        ...
    }
}

public class B: A
{
    public override void F() {
        base.F();
        ...
    }
}
```

le modificateur `unsafe` sur la méthode `F` dans `A` fait simplement en sorte que l’étendue textuelle de `F` devienne un contexte non sécurisé dans lequel les fonctionnalités potentiellement dangereuses du langage peuvent être utilisées. Dans la substitution de `F` dans `B`, il n’est pas nécessaire de spécifier à nouveau le modificateur `unsafe`, sauf si, bien sûr, la méthode `F` dans `B` lui-même a besoin d’accéder à des fonctionnalités non sécurisées.

La situation est légèrement différente lorsqu’un type pointeur fait partie de la signature de la méthode.

```csharp
public unsafe class A
{
    public virtual void F(char* p) {...}
}

public class B: A
{
    public unsafe override void F(char* p) {...}
}
```

Dans ce cas, étant donné que la signature de `F` comprend un type pointeur, elle peut uniquement être écrite dans un contexte non sécurisé. Toutefois, le contexte unsafe peut être introduit soit en rendant la classe entière non sécurisée, comme c’est le cas dans `A`, soit en incluant un modificateur `unsafe` dans la déclaration de méthode, comme c’est le cas dans `B`.

## <a name="pointer-types"></a>Types de pointeur

Dans un contexte unsafe, un *type* ([types](types.md)) peut être un *pointer_type* , ainsi qu’un *Value_type* ou un *reference_type*. Toutefois, un *pointer_type* peut également être utilisé dans une expression `typeof` ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) en dehors d’un contexte non sécurisé, car cette utilisation n’est pas fiable.

```antlr
type_unsafe
    : pointer_type
    ;
```

Un *pointer_type* est écrit comme un *unmanaged_type* ou le mot clé `void`, suivi d’un jeton `*` :

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

Le type spécifié avant le `*` dans un type pointeur est appelé ***type référent*** du type pointeur. Il représente le type de la variable vers laquelle la valeur du type pointeur pointe.

Contrairement aux références (valeurs de types référence), les pointeurs ne sont pas suivis par le récupérateur de mémoire : le garbage collector n’a aucune connaissance des pointeurs et des données auxquelles ils pointent. Pour cette raison, un pointeur n’est pas autorisé à pointer vers une référence ou un struct qui contient des références, et le type référent d’un pointeur doit être un *unmanaged_type*.

Un *unmanaged_type* est un type qui n’est pas un type *reference_type* ou construit, et ne contient pas de champs de type *reference_type* ou construits à aucun niveau d’imbrication. En d’autres termes, un *unmanaged_type* est l’un des éléments suivants :

*  `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, 0, 1 ou 2.
*  N’importe quel *enum_type*.
*  N’importe quel *pointer_type*.
*  Tout *struct_type* défini par l’utilisateur qui n’est pas un type construit et contient uniquement des champs de *unmanaged_type*s.

La règle intuitive pour mélanger des pointeurs et des références est que les référents de références (objets) sont autorisés à contenir des pointeurs, mais les référents de pointeurs ne sont pas autorisés à contenir des références.

Des exemples de types pointeur sont fournis dans le tableau ci-dessous :

| __Exemple__ | __Description__                               |
|-------------|-----------------------------------------------|
| `byte*`     | Pointeur vers `byte`                             |
| `char*`     | Pointeur vers `char`                             |
| `int**`     | Pointeur vers un pointeur vers `int`                   |
| `int*[]`    | Tableau unidimensionnel de pointeurs vers `int` |
| `void*`     | Pointeur vers un type inconnu                       |

Pour une implémentation donnée, tous les types de pointeurs doivent avoir la même taille et la même représentation.

Contrairement à C C++et, lorsque plusieurs pointeurs sont déclarés dans la même déclaration C# , dans le `*` est écrit avec le type sous-jacent uniquement, et non comme un signe de ponctuation de préfixe sur chaque nom de pointeur. Exemple :

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

La valeur d’un pointeur de type `T*` représente l’adresse d’une variable de type `T`. L’opérateur d’indirection de pointeur `*` ([indirection de pointeur](unsafe-code.md#pointer-indirection)) peut être utilisé pour accéder à cette variable. Par exemple, à partir d’une variable `P` de type `int*`, l’expression `*P` indique la variable `int` trouvée à l’adresse contenue dans `P`.

À l’instar d’une référence d’objet, un pointeur peut être `null`. L’application de l’opérateur d’indirection à un pointeur `null` entraîne un comportement défini par l’implémentation. Un pointeur avec la valeur `null` est représenté par All-bits-Zero.

Le type `void*` représente un pointeur vers un type inconnu. Étant donné que le type référent est inconnu, l’opérateur d’indirection ne peut pas être appliqué à un pointeur de type `void*`, et aucune arithmétique ne peut être exécutée sur ce pointeur. Toutefois, un pointeur de type `void*` peut être casté en n’importe quel autre type pointeur (et inversement).

Les types pointeur sont une catégorie distincte de types. Contrairement aux types référence et aux types valeur, les types pointeur n’héritent pas de `object` et aucune conversion n’existe entre les types pointeur et `object`. En particulier, le boxing et l’unboxing ([boxing et unboxing](types.md#boxing-and-unboxing)) ne sont pas pris en charge pour les pointeurs. Toutefois, les conversions sont autorisées entre les différents types pointeur et entre les types pointeur et les types intégraux. Cela est décrit dans [conversions de pointeurs](unsafe-code.md#pointer-conversions).

Un *pointer_type* ne peut pas être utilisé en tant qu’argument de type ([types construits](types.md#constructed-types)), et l’inférence de type ([inférence de type](expressions.md#type-inference)) échoue sur les appels de méthode génériques qui auraient pu déduire un argument de type comme étant un type pointeur.

Un *pointer_type* peut être utilisé comme type de champ volatile ([champs volatiles](classes.md#volatile-fields)).

Bien que les pointeurs puissent être passés en tant que paramètres `ref` ou `out`, cela peut entraîner un comportement indéfini, puisque le pointeur peut être configuré pour pointer vers une variable locale qui n’existe plus quand la méthode appelée est retournée, ou l’objet fixe auquel elle est utilisée pour pointer , n’est plus résolu. Exemple :

```csharp
using System;

class Test
{
    static int value = 20;

    unsafe static void F(out int* pi1, ref int* pi2) {
        int i = 10;
        pi1 = &i;

        fixed (int* pj = &value) {
            // ...
            pi2 = pj;
        }
    }

    static void Main() {
        int i = 10;
        unsafe {
            int* px1;
            int* px2 = &i;

            F(out px1, ref px2);

            Console.WriteLine("*px1 = {0}, *px2 = {1}",
                *px1, *px2);    // undefined behavior
        }
    }
}
```

Une méthode peut retourner une valeur d’un certain type et ce type peut être un pointeur. Par exemple, lorsqu’un pointeur désignant une séquence contiguë de `int`, le nombre d’éléments de cette séquence et une autre valeur `int`, la méthode suivante retourne l’adresse de cette valeur dans cette séquence, si une correspondance est trouvée ; dans le cas contraire, elle retourne `null` :

```csharp
unsafe static int* Find(int* pi, int size, int value) {
    for (int i = 0; i < size; ++i) {
        if (*pi == value) 
            return pi;
        ++pi;
    }
    return null;
}
```

Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour l’utilisation des pointeurs :

*  L’opérateur `*` peut être utilisé pour effectuer une indirection de pointeur ([indirection de pointeur](unsafe-code.md#pointer-indirection)).
*  L’opérateur `->` peut être utilisé pour accéder à un membre d’un struct via un pointeur ([accès aux membres du pointeur](unsafe-code.md#pointer-member-access)).
*  L’opérateur `[]` peut être utilisé pour indexer un pointeur ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)).
*  L’opérateur `&` peut être utilisé pour obtenir l’adresse d’une variable ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)).
*  Les opérateurs `++` et `--` peuvent être utilisés pour incrémenter et décrémenter des pointeurs ([incrémentation et décrémentation de pointeur](unsafe-code.md#pointer-increment-and-decrement)).
*  Les opérateurs `+` et `-` peuvent être utilisés pour effectuer des opérations arithmétiques sur les pointeurs ([opérations arithmétiques](unsafe-code.md#pointer-arithmetic)sur les pointeurs).
*  Les opérateurs `==`, `!=`, `<`, `>`, `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).
*  L’opérateur `stackalloc` peut être utilisé pour allouer de la mémoire à partir de la pile des appels ([mémoires tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).
*  L’instruction `fixed` peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([l’instruction fixed](unsafe-code.md#the-fixed-statement)).

## <a name="fixed-and-moveable-variables"></a>Variables fixes et déplaçables

L’opérateur d’adresse ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)) et l’instruction `fixed` ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) divisent les variables en deux catégories : ***Variables fixes*** et ***variables déplaçables***.

Les variables fixes résident dans des emplacements de stockage qui ne sont pas affectés par l’opération du garbage collector. (Les variables locales, les paramètres de valeur et les variables créées par des pointeurs de déréférencement sont des exemples de variables fixes.) En revanche, les variables déplaçables résident dans des emplacements de stockage qui peuvent faire l’objet d’un déplacement ou d’une suppression par le garbage collector. (Les exemples de variables déplaçables incluent des champs dans des objets et des éléments de tableaux.)

L’opérateur `&` ([l’opérateur d’adresse](unsafe-code.md#the-address-of-operator)) autorise l’obtention de l’adresse d’une variable fixe sans restrictions. Toutefois, étant donné qu’une variable déplaçable est sujette à un déplacement ou une suppression par le garbage collector, l’adresse d’une variable déplaçable peut uniquement être obtenue à l’aide d’une instruction `fixed` ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) et cette adresse reste valide uniquement pour le durée de cette instruction `fixed`.

En termes précis, une variable fixe est l’un des éléments suivants :

*  Variable résultant d’un *simple_name* ([noms simples](expressions.md#simple-names)) qui fait référence à une variable locale ou à un paramètre de valeur, sauf si la variable est capturée par une fonction anonyme.
*  Variable résultant d’un *member_access* ([accès aux membres](expressions.md#member-access)) de la forme `V.I`, où `V` est une variable fixe d’un *struct_type*.
*  Une variable résultant d’un *pointer_indirection_expression* ([indirection de pointeur](unsafe-code.md#pointer-indirection)) de la forme `*P`, un *pointer_member_access* ([accès au membre pointeur](unsafe-code.md#pointer-member-access)) de la forme `P->I` ou un *pointer_element_access* ( [Accès à l’élément pointeur](unsafe-code.md#pointer-element-access)) de la forme `P[E]`.

Toutes les autres variables sont classées comme variables déplaçables.

Notez qu’un champ statique est classé comme une variable déplaçable. Notez également qu’un paramètre `ref` ou `out` est classé en tant que variable déplaçable, même si l’argument donné pour le paramètre est une variable fixe. Enfin, Notez qu’une variable produite par le déréférencement d’un pointeur est toujours classifiée comme une variable fixe.

## <a name="pointer-conversions"></a>Conversions de pointeurs

Dans un contexte non sécurisé, l’ensemble des conversions implicites disponibles ([conversions implicites](conversions.md#implicit-conversions)) est étendu pour inclure les conversions de pointeur implicites suivantes :

*  De n’importe quel *pointer_type* vers le type `void*`.
*  Du littéral `null` à n’importe quel *pointer_type*.

En outre, dans un contexte non sécurisé, l’ensemble des conversions explicites disponibles ([conversions explicites](conversions.md#explicit-conversions)) est étendu pour inclure les conversions de pointeur explicites suivantes :

*  De n’importe quel *pointer_type* à n’importe quel autre *pointer_type*.
*  À partir de `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong` à n’importe quel *pointer_type*.
*  De n’importe quel *pointer_type* à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`.

Enfin, dans un contexte non sécurisé, l’ensemble de conversions implicites standard ([conversions implicites standard](conversions.md#standard-implicit-conversions)) comprend la conversion de pointeur suivante :

*  De n’importe quel *pointer_type* vers le type `void*`.

Les conversions entre deux types pointeur ne modifient jamais la valeur du pointeur réel. En d’autres termes, une conversion d’un type pointeur vers un autre n’a aucun effet sur l’adresse sous-jacente donnée par le pointeur.

Quand un type pointeur est converti en un autre, si le pointeur résultant n’est pas correctement aligné pour le type pointé, le comportement n’est pas défini si le résultat est déréférencé. En général, le concept « aligné correctement » est transitif : si un pointeur vers le type `A` est correctement aligné pour un pointeur vers le type `B` qui, à son tour, est correctement aligné pour un pointeur vers le type `C`, un pointeur vers le type `A` est correctement aligné pour un pointeur vers le type `C`.

Considérez le cas suivant dans lequel une variable ayant un type est accessible via un pointeur vers un type différent :

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

Quand un type pointeur est converti en un pointeur vers un octet, le résultat pointe vers l’octet adressé le plus bas de la variable. Les incréments successifs du résultat, jusqu’à la taille de la variable, produisent des pointeurs vers les octets restants de cette variable. Par exemple, la méthode suivante affiche chacun des huit octets d’une valeur double sous la forme d’une valeur hexadécimale :

```csharp
using System;

class Test
{
    unsafe static void Main() {
      double d = 123.456e23;
        unsafe {
           byte* pb = (byte*)&d;
            for (int i = 0; i < sizeof(double); ++i)
               Console.Write("{0:X2} ", *pb++);
            Console.WriteLine();
        }
    }
}
```

Bien entendu, la sortie générée dépend de endianness.

Les mappages entre les pointeurs et les entiers sont définis par l’implémentation. Toutefois, sur les architectures d’UC 32 * et 64 bits avec un espace d’adressage linéaire, les conversions de pointeurs vers ou depuis les types intégraux se comportent généralement exactement comme des conversions de valeurs `uint` ou `ulong`, respectivement, vers ou à partir de ces types intégraux.

### <a name="pointer-arrays"></a>Tableaux de pointeurs

Dans un contexte non sécurisé, des tableaux de pointeurs peuvent être construits. Seules certaines des conversions qui s’appliquent à d’autres types de tableau sont autorisées sur les tableaux de pointeurs :

*  La conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) de n’importe quel *array_type* en `System.Array` et les interfaces qu’elle implémente s’applique également aux tableaux de pointeurs. Toutefois, toute tentative d’accès aux éléments du tableau par le biais de `System.Array` ou des interfaces qu’il implémente entraîne une exception au moment de l’exécution, car les types de pointeurs ne sont pas convertibles en `object`.
*  Les conversions de référence implicite et explicite (conversions de[référence implicites](conversions.md#implicit-reference-conversions), [conversions de référence explicites](conversions.md#explicit-reference-conversions)) à partir d’un type de tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et ses interfaces de base génériques ne s’appliquent jamais aux tableaux de pointeurs. étant donné que les types pointeur ne peuvent pas être utilisés en tant qu’arguments de type, et qu’il n’y a pas de conversion de types pointeur en types non-pointeur.
*  La conversion de référence explicite ([conversions de référence explicites](conversions.md#explicit-reference-conversions)) de `System.Array` et les interfaces qu’elle implémente à n’importe quel *array_type* s’applique aux tableaux de pointeurs.
*  Les conversions de référence explicites ([conversions de référence explicites](conversions.md#explicit-reference-conversions)) de `System.Collections.Generic.IList<S>` et ses interfaces de base en un type tableau unidimensionnel `T[]` ne s’appliquent jamais aux tableaux de pointeurs, car les types pointeur ne peuvent pas être utilisés en tant qu’arguments de type, et il y a aucune conversion des types pointeur en types non-pointeur.

Ces restrictions signifient que l’expansion de l’instruction `foreach` sur les tableaux décrits dans [l’instruction foreach](statements.md#the-foreach-statement) ne peut pas être appliquée aux tableaux de pointeurs. Au lieu de cela, une instruction foreach de la forme

```csharp
foreach (V v in x) embedded_statement
```

où le type de `x` est un type de tableau de la forme `T[,,...,]`, `N` est le nombre de dimensions moins 1 et `T` ou `V` est un type pointeur, est développé à l’aide de boucles for imbriquées comme suit :

```csharp
{
    T[,,...,] a = x;
    for (int i0 = a.GetLowerBound(0); i0 <= a.GetUpperBound(0); i0++)
    for (int i1 = a.GetLowerBound(1); i1 <= a.GetUpperBound(1); i1++)
    ...
    for (int iN = a.GetLowerBound(N); iN <= a.GetUpperBound(N); iN++) {
        V v = (V)a.GetValue(i0,i1,...,iN);
        embedded_statement
    }
}
```

Les variables `a`, `i0`, `i1`,..., `iN` ne sont pas visibles ou accessibles à `x`, à l' *embedded_statement* ou à tout autre code source du programme. La variable `v` est en lecture seule dans l’instruction incorporée. S’il n’existe pas de conversion explicite ([conversions de pointeur](unsafe-code.md#pointer-conversions)) de `T` (type d’élément) en `V`, une erreur est générée et aucune autre étape n’est effectuée. Si `x` a la valeur `null`, une `System.NullReferenceException` est levée au moment de l’exécution.

## <a name="pointers-in-expressions"></a>Pointeurs dans les expressions

Dans un contexte unsafe, une expression peut produire un résultat d’un type pointeur, mais en dehors d’un contexte unsafe, il s’agit d’une erreur de compilation pour qu’une expression soit d’un type pointeur. En termes précis, en dehors d’un contexte non sécurisé, une erreur de compilation se produit si des *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([accès aux membres](expressions.md#member-access)), *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)) ou  *element_access* ([accès à l’élément](expressions.md#element-access)) est de type pointeur.

Dans un contexte non sécurisé, les productions *primary_no_array_creation_expression* ([expressions primaires](expressions.md#primary-expressions)) et *unary_expression* ([opérateurs unaires](expressions.md#unary-operators)) autorisent les constructions supplémentaires suivantes :

```antlr
primary_no_array_creation_expression_unsafe
    : pointer_member_access
    | pointer_element_access
    | sizeof_expression
    ;

unary_expression_unsafe
    : pointer_indirection_expression
    | addressof_expression
    ;
```

Ces constructions sont décrites dans les sections suivantes. La priorité et l’associativité des opérateurs non sécurisés sont implicites par la grammaire.

### <a name="pointer-indirection"></a>Indirection de pointeur

Un *pointer_indirection_expression* se compose d’un astérisque (`*`) suivi d’un *unary_expression*.

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

L’opérateur unaire `*` désigne l’indirection de pointeur et est utilisé pour obtenir la variable vers laquelle pointe un pointeur. Le résultat de l’évaluation de `*P`, où `P` est une expression d’un type pointeur `T*`, est une variable de type `T`. Il s’agit d’une erreur au moment de la compilation pour appliquer l’opérateur unaire `*` à une expression de type `void*` ou à une expression qui n’est pas d’un type pointeur.

L’effet de l’application de l’opérateur unaire `*` à un pointeur `null` est défini par l’implémentation. En particulier, il n’y a aucune garantie que cette opération lève une `System.NullReferenceException`.

Si une valeur non valide a été assignée au pointeur, le comportement de l’opérateur unaire `*` n’est pas défini. Parmi les valeurs non valides pour le déréférencement d’un pointeur par l’opérateur unaire `*`, on trouve une adresse alignée de manière inappropriée pour le type pointé (Voir l’exemple dans les [conversions de pointeur](unsafe-code.md#pointer-conversions)) et l’adresse d’une variable après la fin de sa durée de vie.

À des fins d’analyse d’assignation définie, une variable produite par l’évaluation d’une expression de la forme `*P` est considérée comme initialement assignée ([variables initialement affectées](variables.md#initially-assigned-variables)).

### <a name="pointer-member-access"></a>Accès au membre pointeur

Un *pointer_member_access* se compose d’un *primary_expression*, suivi d’un jeton « `->` », suivi d’un *identificateur* et d’un *type_argument_list*facultatif.

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

Dans un accès de membre de pointeur au format `P->I`, `P` doit être une expression d’un type pointeur autre que `void*`, et `I` doit désigner un membre accessible du type auquel `P` pointe.

Un accès de membre pointeur au format `P->I` est évalué exactement comme `(*P).I`. Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection). Pour obtenir une description de l’opérateur d’accès aux membres (`.`), consultez [accès aux membres](expressions.md#member-access).

Dans l’exemple

```csharp
using System;

struct Point
{
    public int x;
    public int y;

    public override string ToString() {
        return "(" + x + "," + y + ")";
    }
}

class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            p->x = 10;
            p->y = 20;
            Console.WriteLine(p->ToString());
        }
    }
}
```

l’opérateur `->` est utilisé pour accéder aux champs et appeler une méthode d’un struct via un pointeur. Étant donné que l’opération `P->I` équivaut précisément à `(*P).I`, la méthode `Main` peut également être écrite :

```csharp
class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            (*p).x = 10;
            (*p).y = 20;
            Console.WriteLine((*p).ToString());
        }
    }
}
```

### <a name="pointer-element-access"></a>Accès à l’élément pointeur

Un *pointer_element_access* se compose d’un *primary_no_array_creation_expression* suivi d’une expression placée dans « `[` » et « `]` ».

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

Dans un accès à un élément pointeur au format `P[E]`, `P` doit être une expression d’un type pointeur autre que `void*`, et `E` doit être une expression qui peut être convertie implicitement en `int`, `uint`, `long` ou `ulong`.

Un accès à un élément pointeur au format `P[E]` est évalué exactement comme `*(P + E)`. Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection). Pour obtenir une description de l’opérateur d’addition de pointeurs (`+`), consultez [opérations arithmétiques sur les pointeurs](unsafe-code.md#pointer-arithmetic).

Dans l’exemple

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) p[i] = (char)i;
        }
    }
}
```

un accès à un élément pointeur est utilisé pour initialiser la mémoire tampon de caractères dans une boucle `for`. Étant donné que l’opération `P[E]` équivaut précisément à `*(P + E)`, l’exemple peut également être écrit :

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) *(p + i) = (char)i;
        }
    }
}
```

L’opérateur d’accès à l’élément pointeur ne vérifie pas les erreurs de limites hors limites et le comportement lors de l’accès à un élément hors limites n’est pas défini. Il s’agit du même que C C++et.

### <a name="the-address-of-operator"></a>Opérateur d’adresse

Un *addressof_expression* se compose d’un et commercial (`&`) suivi d’un *unary_expression*.

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

Dans le cas d’une expression `E` qui est de type `T` et qui est classée en tant que variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), la construction `&E` calcule l’adresse de la variable donnée par `E`. Le type du résultat est `T*` et est classifié comme une valeur. Une erreur de compilation se produit si `E` n’est pas classée en tant que variable, si `E` est classée en tant que variable locale en lecture seule, ou si `E` désigne une variable déplaçable. Dans le dernier cas, une instruction fixed ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) peut être utilisée pour « corriger » temporairement la variable avant d’obtenir son adresse. Comme indiqué dans l' [accès aux membres](expressions.md#member-access), en dehors d’un constructeur d’instance ou d’un constructeur statique pour un struct ou une classe qui définit un champ `readonly`, ce champ est considéré comme une valeur, et non comme une variable. Par conséquent, son adresse ne peut pas être prise. De même, l’adresse d’une constante ne peut pas être prise.

L’opérateur `&` ne requiert pas l’assignation définitive de son argument, mais après une opération `&`, la variable à laquelle l’opérateur est appliqué est considérée comme assignée définitivement dans le chemin d’exécution dans lequel l’opération se produit. Il incombe au programmeur de s’assurer que l’initialisation correcte de la variable a réellement lieu dans cette situation.

Dans l’exemple

```csharp
using System;

class Test
{
    static void Main() {
        int i;
        unsafe {
            int* p = &i;
            *p = 123;
        }
        Console.WriteLine(i);
    }
}
```

`i` est considéré comme définitivement affecté à la suite de l’opération `&i` utilisée pour initialiser `p`. L’assignation à `*p` en effet initialise `i`, mais l’inclusion de cette initialisation est la responsabilité du programmeur, et aucune erreur de compilation ne se produit si l’assignation a été supprimée.

Les règles d’attribution définie pour l’opérateur `&` existent, de sorte que l’initialisation redondante de variables locales peut être évitée. Par exemple, de nombreuses API externes prennent un pointeur vers une structure qui est remplie par l’API. Les appels à ces API passent généralement l’adresse d’une variable de struct locale et sans la règle, l’initialisation redondante de la variable de struct est nécessaire.

### <a name="pointer-increment-and-decrement"></a>Incrémenter et décrémenter des pointeurs

Dans un contexte non sécurisé, les opérateurs `++` et `--` ([opérateurs suffixés d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs d’incrémentation et de décrémentation de préfixe](expressions.md#prefix-increment-and-decrement-operators)) peuvent être appliqués aux variables de pointeur de tous les types, à l’exception de `void*`. Ainsi, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

Les opérateurs produisent les mêmes résultats que `x + 1` et `x - 1`, respectivement ([arithmétique de pointeur](unsafe-code.md#pointer-arithmetic)). En d’autres termes, pour une variable pointeur de type `T*`, l’opérateur `++` ajoute `sizeof(T)` à l’adresse contenue dans la variable, et l’opérateur `--` soustrait `sizeof(T)` de l’adresse contenue dans la variable.

Si une opération d’incrémentation ou de décrémentation de pointeur dépasse le domaine du type pointeur, le résultat est défini par l’implémentation, mais aucune exception n’est générée.

### <a name="pointer-arithmetic"></a>Arithmétique des pointeurs

Dans un contexte non sécurisé, les opérateurs `+` et `-` (opérateur d'[addition](expressions.md#addition-operator) et [opérateur de soustraction](expressions.md#subtraction-operator)) peuvent être appliqués aux valeurs de tous les types pointeur, à l’exception de `void*`. Ainsi, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :

```csharp
T* operator +(T* x, int y);
T* operator +(T* x, uint y);
T* operator +(T* x, long y);
T* operator +(T* x, ulong y);

T* operator +(int x, T* y);
T* operator +(uint x, T* y);
T* operator +(long x, T* y);
T* operator +(ulong x, T* y);

T* operator -(T* x, int y);
T* operator -(T* x, uint y);
T* operator -(T* x, long y);
T* operator -(T* x, ulong y);

long operator -(T* x, T* y);
```

À partir d’une expression `P` d’un type pointeur `T*` et d’une expression `N` de type `int`, `uint`, `long` ou `ulong`, les expressions `P + N` et `N + P` calculent la valeur de pointeur de type `T*` qui résulte de l’ajout de 0 à l’adresse fourni par 1. De même, l’expression `P - N` calcule la valeur de pointeur de type `T*` qui résulte de la soustraction de `N * sizeof(T)` de l’adresse donnée par `P`.

À partir de deux expressions, `P` et `Q`, d’un type pointeur `T*`, l’expression `P - Q` calcule la différence entre les adresses fournies par `P` et `Q`, puis divise cette différence par `sizeof(T)`. Le type du résultat est toujours `long`. En effet, `P - Q` est calculé comme `((long)(P) - (long)(Q)) / sizeof(T)`.

Exemple :

```csharp
using System;

class Test
{
    static void Main() {
        unsafe {
            int* values = stackalloc int[20];
            int* p = &values[1];
            int* q = &values[15];
            Console.WriteLine("p - q = {0}", p - q);
            Console.WriteLine("q - p = {0}", q - p);
        }
    }
}
```

ce qui génère la sortie :

```console
p - q = -14
q - p = 14
```

Si une opération arithmétique de pointeur dépasse le domaine du type pointeur, le résultat est tronqué en mode d’implémentation défini, mais aucune exception n’est générée.

### <a name="pointer-comparison"></a>Comparaison de pointeurs

Dans un contexte non sécurisé, les opérateurs `==`, `!=`, `<`, `>`, `<=` et `=>` ([opérateurs relationnels et de test de type](expressions.md#relational-and-type-testing-operators)) peuvent être appliqués aux valeurs de tous les types pointeur. Les opérateurs de comparaison de pointeurs sont les suivants :

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

Étant donné qu’une conversion implicite existe de tout type pointeur vers le type `void*`, les opérandes de tout type pointeur peuvent être comparés à l’aide de ces opérateurs. Les opérateurs de comparaison comparent les adresses données par les deux opérandes comme s’il s’agissait d’entiers non signés.

### <a name="the-sizeof-operator"></a>Opérateur sizeof

L’opérateur `sizeof` retourne le nombre d’octets occupés par une variable d’un type donné. Le type spécifié comme opérande à `sizeof` doit être un *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

Le résultat de l’opérateur `sizeof` est une valeur de type `int`. Pour certains types prédéfinis, l’opérateur `sizeof` produit une valeur constante, comme indiqué dans le tableau ci-dessous.


| __Expression__   | __Résultat__ |
|------------------|------------|
| `sizeof(sbyte)`  | `1`        |
| `sizeof(byte)`   | `1`        |
| `sizeof(short)`  | `2`        |
| `sizeof(ushort)` | `2`        |
| `sizeof(int)`    | `4`        |
| `sizeof(uint)`   | `4`        |
| `sizeof(long)`   | `8`        |
| `sizeof(ulong)`  | `8`        |
| `sizeof(char)`   | `2`        |
| `sizeof(float)`  | `4`        |
| `sizeof(double)` | `8`        |
| `sizeof(bool)`   | `1`        |

Pour tous les autres types, le résultat de l’opérateur `sizeof` est défini par l’implémentation et est classifié comme une valeur, et non comme une constante.

L’ordre dans lequel les membres sont empaquetés dans un struct n’est pas spécifié.

À des fins d’alignement, il peut y avoir un remplissage sans nom au début d’un struct, dans un struct et à la fin du struct. Le contenu des bits utilisés comme remplissage est indéterminé.

En cas d’application à un opérande qui a le type struct, le résultat est le nombre total d’octets dans une variable de ce type, y compris tout remplissage.

## <a name="the-fixed-statement"></a>Instruction fixed

Dans un contexte non sécurisé, la production *embedded_statement* ([instructions](statements.md)) autorise une construction supplémentaire, l’instruction `fixed`, qui est utilisée pour « corriger » une variable déplaçable de telle sorte que son adresse reste constante pendant toute la durée de l’instruction. .

```antlr
fixed_statement
    : 'fixed' '(' pointer_type fixed_pointer_declarators ')' embedded_statement
    ;

fixed_pointer_declarators
    : fixed_pointer_declarator (','  fixed_pointer_declarator)*
    ;

fixed_pointer_declarator
    : identifier '=' fixed_pointer_initializer
    ;

fixed_pointer_initializer
    : '&' variable_reference
    | expression
    ;
```

Chaque *fixed_pointer_declarator* déclare une variable locale du *pointer_type* donné et initialise cette variable locale avec l’adresse calculée par le *fixed_pointer_initializer*correspondant. Une variable locale déclarée dans une instruction `fixed` est accessible dans tout *fixed_pointer_initializer*s se trouvant à droite de la déclaration de cette variable et dans le *embedded_statement* de l’instruction `fixed`. Une variable locale déclarée par une instruction `fixed` est considérée comme étant en lecture seule. Une erreur de compilation se produit si l’instruction incorporée tente de modifier cette variable locale (par le biais de l’assignation ou des opérateurs `++` et `--`) ou de la passer en tant que paramètre `ref` ou `out`.

Un *fixed_pointer_initializer* peut être l’un des suivants :

*  Le jeton « `&` » suivi d’un *variable_reference* ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) à une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) d’un type non managé `T`, à condition que le type `T*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`. Dans ce cas, l’initialiseur calcule l’adresse de la variable donnée, et la variable est garantie à conserver à une adresse fixe pendant la durée de l’instruction `fixed`.
*  Expression d’un *array_type* avec des éléments d’un type non managé `T`, à condition que le type `T*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`. Dans ce cas, l’initialiseur calcule l’adresse du premier élément du tableau, et le tableau entier est toujours conservé à une adresse fixe pendant toute la durée de l’instruction `fixed`. Si l’expression de tableau a la valeur null ou si le tableau n’a aucun élément, l’initialiseur calcule une adresse égale à zéro.
*  Expression de type `string`, à condition que le type `char*` soit implicitement convertible en type pointeur donné dans l’instruction `fixed`. Dans ce cas, l’initialiseur calcule l’adresse du premier caractère de la chaîne, et la chaîne entière est garantie pour rester à une adresse fixe pendant la durée de l’instruction `fixed`. Le comportement de l’instruction `fixed` est défini par l’implémentation si l’expression de chaîne est null.
*  *Simple_name* ou *member_access* qui référence un membre de mémoire tampon de taille fixe d’une variable déplaçable, à condition que le type du membre de la mémoire tampon de taille fixe soit implicitement convertible en type pointeur donné dans l’instruction `fixed`. Dans ce cas, l’initialiseur calcule un pointeur vers le premier élément de la mémoire tampon de taille fixe ([mémoires tampons de taille fixe dans les expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), et la mémoire tampon de taille fixe est garantie pour rester à une adresse fixe pendant la durée de l’instruction `fixed`.

Pour chaque adresse calculée par un *fixed_pointer_initializer* , l’instruction `fixed` garantit que la variable référencée par l’adresse n’est pas sujette à un déplacement ou une suppression par le garbage collector pendant la durée de l’instruction `fixed`. Par exemple, si l’adresse calculée par un *fixed_pointer_initializer* fait référence à un champ d’un objet ou à un élément d’une instance de tableau, l’instruction `fixed` garantit que l’instance d’objet conteneur n’est pas déplacée ou supprimée pendant la durée de vie de l’instruction.

Il incombe au programmeur de s’assurer que les pointeurs créés par les instructions `fixed` ne survivent pas au-delà de l’exécution de ces instructions. Par exemple, lorsque des pointeurs créés par des instructions `fixed` sont passés à des API externes, il incombe au programmeur de s’assurer que les API ne conservent aucune mémoire de ces pointeurs.

Les objets fixes peuvent entraîner la fragmentation du tas (car ils ne peuvent pas être déplacés). Pour cette raison, les objets doivent être corrigés uniquement lorsque cela est absolument nécessaire, puis uniquement pour la durée la plus faible possible.

L’exemple

```csharp
class Test
{
    static int x;
    int y;

    unsafe static void F(int* p) {
        *p = 1;
    }

    static void Main() {
        Test t = new Test();
        int[] a = new int[10];
        unsafe {
            fixed (int* p = &x) F(p);
            fixed (int* p = &t.y) F(p);
            fixed (int* p = &a[0]) F(p);
            fixed (int* p = a) F(p);
        }
    }
}
```

illustre plusieurs utilisations de l’instruction `fixed`. La première instruction corrige et obtient l’adresse d’un champ statique, la deuxième instruction corrige et obtient l’adresse d’un champ d’instance, et la troisième instruction corrige et obtient l’adresse d’un élément de tableau. Dans chaque cas, l’utilisation de l’opérateur normal `&` était une erreur, car les variables sont toutes classées comme variables déplaçables.

La quatrième instruction `fixed` de l’exemple ci-dessus produit un résultat similaire à la troisième.

Cet exemple de l’instruction `fixed` utilise `string` :

```csharp
class Test
{
    static string name = "xx";

    unsafe static void F(char* p) {
        for (int i = 0; p[i] != '\0'; ++i)
            Console.WriteLine(p[i]);
    }

    static void Main() {
        unsafe {
            fixed (char* p = name) F(p);
            fixed (char* p = "xx") F(p);
        }
    }
}
```

Dans un tableau de contexte non sécurisé, les éléments des tableaux unidimensionnels sont stockés dans l’ordre d’index de plus en plus important, en commençant par l’index `0` et en terminant par l’index `Length - 1`. Pour les tableaux multidimensionnels, les éléments de tableau sont stockés de telle sorte que les index de la dimension la plus à droite soient augmentés en premier, puis la dimension gauche suivante, et ainsi de suite vers la gauche. Dans une instruction `fixed` qui obtient un pointeur `p` vers une instance de tableau `a`, les valeurs de pointeur allant de `p` à `p + a.Length - 1` représentent les adresses des éléments dans le tableau. De même, les variables allant de `p[0]` à `p[a.Length - 1]` représentent les éléments de tableau réels. Étant donné la façon dont les tableaux sont stockés, nous pouvons traiter un tableau de toute dimension comme s’il était linéaire.

Exemple :

```csharp
using System;

class Test
{
    static void Main() {
        int[,,] a = new int[2,3,4];
        unsafe {
            fixed (int* p = a) {
                for (int i = 0; i < a.Length; ++i)    // treat as linear
                    p[i] = i;
            }
        }

        for (int i = 0; i < 2; ++i)
            for (int j = 0; j < 3; ++j) {
                for (int k = 0; k < 4; ++k)
                    Console.Write("[{0},{1},{2}] = {3,2} ", i, j, k, a[i,j,k]);
                Console.WriteLine();
            }
    }
}
```

ce qui génère la sortie :

```console
[0,0,0] =  0 [0,0,1] =  1 [0,0,2] =  2 [0,0,3] =  3
[0,1,0] =  4 [0,1,1] =  5 [0,1,2] =  6 [0,1,3] =  7
[0,2,0] =  8 [0,2,1] =  9 [0,2,2] = 10 [0,2,3] = 11
[1,0,0] = 12 [1,0,1] = 13 [1,0,2] = 14 [1,0,3] = 15
[1,1,0] = 16 [1,1,1] = 17 [1,1,2] = 18 [1,1,3] = 19
[1,2,0] = 20 [1,2,1] = 21 [1,2,2] = 22 [1,2,3] = 23
```

Dans l’exemple

```csharp
class Test
{
    unsafe static void Fill(int* p, int count, int value) {
        for (; count != 0; count--) *p++ = value;
    }

    static void Main() {
        int[] a = new int[100];
        unsafe {
            fixed (int* p = a) Fill(p, 100, -1);
        }
    }
}
```

une instruction `fixed` est utilisée pour corriger un tableau afin que son adresse puisse être passée à une méthode qui prend un pointeur.

Dans l'exemple :

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    Font f;

    unsafe static void Main()
    {
        Test test = new Test();
        test.f.size = 10;
        fixed (char* p = test.f.name) {
            PutString("Times New Roman", p, 32);
        }
    }
}
```

une instruction fixed est utilisée pour corriger une mémoire tampon de taille fixe d’un struct afin que son adresse puisse être utilisée comme pointeur.

Une valeur `char*` produite en résolvant une instance de chaîne pointe toujours vers une chaîne terminée par le caractère null. Dans une instruction fixed qui obtient un pointeur `p` vers une instance de chaîne `s`, les valeurs de pointeur comprises entre `p` et `p + s.Length - 1` représentent les adresses des caractères de la chaîne, tandis que la valeur de pointeur `p + s.Length` pointe toujours sur un caractère null (le caractère avec la valeur `'\0'`).

La modification d’objets de type managé par le biais de pointeurs fixes peut engendrer un comportement indéfini. Par exemple, étant donné que les chaînes sont immuables, il incombe au programmeur de s’assurer que les caractères référencés par un pointeur vers une chaîne fixe ne sont pas modifiés.

L’arrêt automatique des chaînes de caractères est particulièrement pratique lors de l’appel d’API externes qui attendent des chaînes de style C. Notez, toutefois, qu’une instance de chaîne est autorisée à contenir des caractères null. Si ces caractères Null sont présents, la chaîne apparaît tronquée lorsqu’elle est traitée comme un `char*` se terminant par un caractère null.

## <a name="fixed-size-buffers"></a>Mémoires tampons de taille fixe

Les mémoires tampons de taille fixe sont utilisées pour déclarer des tableaux en ligne « C style » comme membres de structs, et sont principalement utiles pour l’interfaçage avec les API non managées.

### <a name="fixed-size-buffer-declarations"></a>Déclarations de mémoire tampon de taille fixe

Une ***mémoire tampon de taille fixe*** est un membre qui représente le stockage pour une mémoire tampon de longueur fixe des variables d’un type donné. Une déclaration de mémoire tampon de taille fixe introduit une ou plusieurs mémoires tampons de taille fixe d’un type d’élément donné. Les mémoires tampons de taille fixe sont autorisées uniquement dans les déclarations de struct et peuvent se produire uniquement dans des contextes non sécurisés ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)).

```antlr
struct_member_declaration_unsafe
    : fixed_size_buffer_declaration
    ;

fixed_size_buffer_declaration
    : attributes? fixed_size_buffer_modifier* 'fixed' buffer_element_type fixed_size_buffer_declarator+ ';'
    ;

fixed_size_buffer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'unsafe'
    ;

buffer_element_type
    : type
    ;

fixed_size_buffer_declarator
    : identifier '[' constant_expression ']'
    ;
```

Une déclaration de mémoire tampon de taille fixe peut inclure un ensemble d’attributs ([attributs](attributes.md)), un modificateur `new` ([modificateurs](classes.md#modifiers)), une combinaison valide des quatre modificateurs d’accès ([paramètres de type et contraintes](classes.md#type-parameters-and-constraints)) et un modificateur `unsafe` ([unsafe contextes](unsafe-code.md#unsafe-contexts)). Les attributs et les modificateurs s’appliquent à tous les membres déclarés par la déclaration de mémoire tampon de taille fixe. Il s’agit d’une erreur pour que le même modificateur apparaisse plusieurs fois dans une déclaration de mémoire tampon de taille fixe.

Une déclaration de mémoire tampon de taille fixe n’est pas autorisée à inclure le modificateur `static`.

Le type d’élément de mémoire tampon d’une déclaration de mémoire tampon de taille fixe spécifie le type d’élément de la ou des mémoires tampons introduites par la déclaration. Le type d’élément de la mémoire tampon doit être l’un des types prédéfinis `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, 0 ou 1.

Le type d’élément de mémoire tampon est suivi d’une liste de déclarateurs de mémoires tampons de taille fixe, chacun d’entre eux introduisant un nouveau membre. Un déclarateur de mémoire tampon de taille fixe se compose d’un identificateur qui nomme le membre, suivi d’une expression constante placée entre des jetons `[` et `]`. L’expression constante indique le nombre d’éléments dans le membre introduit par ce déclarateur de mémoire tampon de taille fixe. Le type de l’expression constante doit être implicitement convertible en type `int`, et la valeur doit être un entier positif différent de zéro.

Il est garanti que les éléments d’une mémoire tampon de taille fixe sont disposés de façon séquentielle dans la mémoire.

Une déclaration de mémoire tampon de taille fixe qui déclare plusieurs mémoires tampons de taille fixe équivaut à plusieurs déclarations d’une déclaration de mémoire tampon de taille fixe unique avec les mêmes attributs et types d’éléments. Exemple :

```csharp
unsafe struct A
{
   public fixed int x[5], y[10], z[100];
}
```

est équivalent à

```csharp
unsafe struct A
{
   public fixed int x[5];
   public fixed int y[10];
   public fixed int z[100];
}
```

### <a name="fixed-size-buffers-in-expressions"></a>Mémoires tampons de taille fixe dans les expressions

La recherche de membre ([opérateurs](expressions.md#operators)) d’un membre de mémoire tampon de taille fixe se déroule exactement comme la recherche de membres d’un champ.

Une mémoire tampon de taille fixe peut être référencée dans une expression à l’aide d’un *simple_name* ([inférence de type](expressions.md#type-inference)) ou d’un *member_access* ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Lorsqu’un membre de la mémoire tampon de taille fixe est référencé comme un nom simple, l’effet est le même qu’un accès au membre de la forme `this.I`, où `I` est le membre de mémoire tampon de taille fixe.

Dans un accès aux membres de la forme `E.I`, si `E` est de type struct et qu’une recherche de membre de `I` dans ce type struct identifie un membre de taille fixe, `E.I` est évaluée comme suit :

*  Si l’expression `E.I` ne se produit pas dans un contexte unsafe, une erreur se produit au moment de la compilation.
*  Si `E` est classée en tant que valeur, une erreur de compilation se produit.
*  Sinon, si `E` est une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) et que l’expression `E.I` n’est pas un *fixed_pointer_initializer* ([l’instruction fixed](unsafe-code.md#the-fixed-statement)), une erreur de compilation se produit.
*  Dans le cas contraire, `E` fait référence à une variable fixe et le résultat de l’expression est un pointeur vers le premier élément du membre de la mémoire tampon de taille fixe `I` dans `E`. Le résultat est de type `S*`, où `S` est le type d’élément de `I` et est classifié comme une valeur.

Les éléments suivants de la mémoire tampon de taille fixe sont accessibles à l’aide d’opérations de pointeur à partir du premier élément. Contrairement à l’accès aux tableaux, l’accès aux éléments d’une mémoire tampon de taille fixe est une opération risquée et l’option n’est pas cochée.

L’exemple suivant déclare et utilise un struct avec un membre de mémoire tampon de taille fixe.

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    unsafe static void Main()
    {
        Font f;
        f.size = 10;
        PutString("Times New Roman", f.name, 32);
    }
}
```

### <a name="definite-assignment-checking"></a>Vérification de l’attribution définie

Les mémoires tampons de taille fixe ne sont pas soumises à la vérification de l’assignation définie ([assignation définie](variables.md#definite-assignment)) et les membres de la mémoire tampon de taille fixe sont ignorés à des fins de vérification de l’assignation définie des variables de type struct.

Lorsque la variable de struct conteneur la plus externe d’un membre de mémoire tampon de taille fixe est une variable statique, une variable d’instance d’une instance de classe ou un élément de tableau, les éléments de la mémoire tampon de taille fixe sont automatiquement initialisés à leurs valeurs par défaut ([par défaut valeurs](variables.md#default-values)). Dans tous les autres cas, le contenu initial d’une mémoire tampon de taille fixe n’est pas défini.

## <a name="stack-allocation"></a>Allocation de pile

Dans un contexte non sécurisé, une déclaration de variable locale ([déclarations de variable locale](statements.md#local-variable-declarations)) peut inclure un initialiseur d’allocation de pile qui alloue de la mémoire à partir de la pile des appels.

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

*Unmanaged_type* indique le type des éléments qui seront stockés dans l’emplacement nouvellement alloué, et l' *expression* indique le nombre de ces éléments. Prises ensemble, elles spécifient la taille d’allocation requise. Étant donné que la taille d’une allocation de pile ne peut pas être négative, il s’agit d’une erreur au moment de la compilation pour spécifier le nombre d’éléments en tant que *constant_expression* qui prend une valeur négative.

Un initialiseur d’allocation de pile de la forme `stackalloc T[E]` requiert que `T` soit un type non managé ([types pointeur](unsafe-code.md#pointer-types)) et `E` soit une expression de type `int`. La construction alloue `E * sizeof(T)` octets à partir de la pile des appels et retourne un pointeur, de type `T*`, au bloc nouvellement alloué. Si `E` est une valeur négative, le comportement n’est pas défini. Si `E` est égal à zéro, aucune allocation n’est effectuée et le pointeur retourné est défini par l’implémentation. Si la mémoire disponible est insuffisante pour allouer un bloc de la taille donnée, une `System.StackOverflowException` est levée.

Le contenu de la mémoire nouvellement allouée n’est pas défini.

Les initialiseurs d’allocation de pile ne sont pas autorisés dans les blocs `catch` ou `finally` ([instruction try](statements.md#the-try-statement)).

Il n’existe aucun moyen de libérer explicitement la mémoire allouée à l’aide de `stackalloc`. Tous les blocs de mémoire alloués par la pile créés lors de l’exécution d’un membre de fonction sont automatiquement ignorés quand ce membre de fonction retourne. Cela correspond à la fonction `alloca`, une extension couramment trouvée dans C et C++ les implémentations.

Dans l’exemple

```csharp
using System;

class Test
{
    static string IntToString(int value) {
        int n = value >= 0? value: -value;
        unsafe {
            char* buffer = stackalloc char[16];
            char* p = buffer + 16;
            do {
                *--p = (char)(n % 10 + '0');
                n /= 10;
            } while (n != 0);
            if (value < 0) *--p = '-';
            return new string(p, 0, (int)(buffer + 16 - p));
        }
    }

    static void Main() {
        Console.WriteLine(IntToString(12345));
        Console.WriteLine(IntToString(-999));
    }
}
```

un initialiseur `stackalloc` est utilisé dans la méthode `IntToString` pour allouer une mémoire tampon de 16 caractères sur la pile. La mémoire tampon est automatiquement supprimée lorsque la méthode est retournée.

## <a name="dynamic-memory-allocation"></a>Allocation de mémoire dynamique

À l’exception de l’opérateur @no__t- C# 0, ne fournit aucune construction prédéfinie pour la gestion de la mémoire qui n’est pas récupérée par le garbage collector. Ces services sont généralement fournis par la prise en charge des bibliothèques de classes ou importés directement depuis le système d’exploitation sous-jacent. Par exemple, la classe `Memory` ci-dessous illustre la façon dont les fonctions de tas d’un système d’exploitation C#sous-jacent sont accessibles à partir de :

```csharp
using System;
using System.Runtime.InteropServices;

public static unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    private static readonly IntPtr s_heap = GetProcessHeap();

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size)
    {
        void* result = HeapAlloc(s_heap, HEAP_ZERO_MEMORY, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count)
    {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd)
        {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd)
        {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block)
    {
        if (!HeapFree(s_heap, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size)
    {
        void* result = HeapReAlloc(s_heap, HEAP_ZERO_MEMORY, block, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block)
    {
        int result = (int)HeapSize(s_heap, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    private const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    private static extern IntPtr GetProcessHeap();

    [DllImport("kernel32")]
    private static extern void* HeapAlloc(IntPtr hHeap, int flags, UIntPtr size);

    [DllImport("kernel32")]
    private static extern bool HeapFree(IntPtr hHeap, int flags, void* block);

    [DllImport("kernel32")]
    private static extern void* HeapReAlloc(IntPtr hHeap, int flags, void* block, UIntPtr size);

    [DllImport("kernel32")]
    private static extern UIntPtr HeapSize(IntPtr hHeap, int flags, void* block);
}
```

Vous trouverez ci-dessous un exemple qui utilise la classe `Memory` :

```csharp
class Test
{
    static unsafe void Main()
    {
        byte* buffer = null;
        try
        {
            const int Size = 256;
            buffer = (byte*)Memory.Alloc(Size);
            for (int i = 0; i < Size; i++) buffer[i] = (byte)i;
            byte[] array = new byte[Size];
            fixed (byte* p = array) Memory.Copy(buffer, p, Size);
            for (int i = 0; i < Size; i++) Console.WriteLine(array[i]);
        }
        finally
        {
            if (buffer != null) Memory.Free(buffer);
        }
    }
}
```

L’exemple alloue 256 octets de mémoire par le biais de `Memory.Alloc` et initialise le bloc de mémoire avec des valeurs qui s’incrémentent de 0 à 255. Il alloue ensuite un tableau d’octets d’éléments 256 et utilise `Memory.Copy` pour copier le contenu du bloc de mémoire dans le tableau d’octets. Enfin, le bloc de mémoire est libéré à l’aide de `Memory.Free` et le contenu du tableau d’octets est généré sur la console.
