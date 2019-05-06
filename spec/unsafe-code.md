---
ms.openlocfilehash: 90001cf3d48f216787fc65e59166ec57c5d0ca34
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229621"
---
# <a name="unsafe-code"></a>Code unsafe

Le cœur du langage C#, tel que défini dans les chapitres précédents, diffère sensiblement de C et C++ dans son omission des pointeurs comme un type de données. Au lieu de cela, C# fournit des références et la possibilité de créer des objets qui sont gérés par un garbage collector. Cette conception, couplée avec d’autres fonctionnalités, fait de C# un langage beaucoup plus sécurisé que C ou C++. Dans le langage principal C# il est tout simplement pas possible d’avoir une variable non initialisée, un pointeur « non résolue » ou une expression qui indexe un tableau au-delà de ses limites. Ensemble des catégories de bogues mortels autrement régulièrement C et les programmes C++ sont donc éliminés.

Alors que fait chaque construction de type pointeur en C ou C++ a un équivalent de type référence en C#, néanmoins, il existe des situations où accès aux types pointeur l'devient une nécessité. Par exemple, interagissant avec le système d’exploitation sous-jacent, l’accès à un périphérique mappé en mémoire ou implémentation d’un algorithme de durée critique peut-être pas possible ou pratique sans accès aux pointeurs. Pour répondre à ce besoin, C# offre la possibilité d’écrire ***code unsafe***.

Dans du code unsafe, il est possible de déclarer et fonctionner sur des pointeurs, pour effectuer des conversions entre les pointeurs et les types intégraux, de prendre l’adresse des variables et ainsi de suite. Dans un sens, écrire du code unsafe est similaire à écrire du code C au sein d’un programme C#.

Le code unsafe est en fait une fonctionnalité « sécurisée » du point de vue des développeurs et des utilisateurs. Le code unsafe doit être clairement marqué avec le modificateur `unsafe`, de sorte que les développeurs ne puissent pas utiliser des fonctionnalités non sécurisées par inadvertance, et le moteur d’exécution fonctionne pour vous assurer que le code unsafe ne peut pas être exécuté dans un environnement non fiable.

## <a name="unsafe-contexts"></a>Contextes non sécurisés

Les fonctionnalités non sécurisées de C# sont disponibles uniquement dans des contextes unsafe. Un contexte unsafe est introduit en incluant un `unsafe` modificateur dans la déclaration d’un type ou un membre, ou en utilisant un *unsafe_statement*:

*  Une déclaration d’une classe, un struct, une interface ou un délégué peut comprendre un `unsafe` modificateur, auquel cas toute l’étendue de texte de cette déclaration de type (y compris le corps de la classe, un struct ou une interface) est considérée comme un contexte unsafe.
*  Une déclaration d’un champ, méthode, propriété, événement, l’indexeur, opérateur, constructeur d’instance, destructeur ou constructeur statique peut comprendre un `unsafe` modificateur, auquel cas toute l’étendue de texte de cette déclaration de membre est considérée comme un unsafe contexte.
*  Un *unsafe_statement* permet d’utiliser un contexte unsafe dans un *bloc*. Toute l’étendue de texte associé *bloc* est considéré comme un contexte unsafe.

Les productions grammaire associée sont présentées ci-dessous.

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

le `unsafe` modificateur spécifié dans la déclaration de struct, toute l’étendue de texte de la déclaration de struct pour devenir un contexte unsafe. Par conséquent, il est possible de déclarer la `Left` et `Right` champs à être d’un type pointeur. L’exemple ci-dessus peut aussi être écrite

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

Ici, le `unsafe` modificateurs dans les déclarations de champ fait que ces déclarations à prendre en considération les contextes non sécurisés.

Autre que l’établissement d’un contexte unsafe, permettant ainsi l’utilisation de types pointeur, le `unsafe` modificateur n’a aucun effet sur un type ou un membre. Dans l’exemple

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

le `unsafe` modificateur sur le `F` méthode dans `A` fait simplement que l’étendue de texte `F` pour devenir un contexte unsafe dans lequel les fonctionnalités non sécurisées du langage peuvent être utilisées. Dans la substitution de `F` dans `B`, il est inutile de spécifier à nouveau le `unsafe` modificateur--, sauf si, bien sûr, le `F` méthode dans `B` lui-même a besoin d’accéder à des fonctionnalités non sécurisées.

La situation est légèrement différente lorsqu’un type pointeur fait partie de la signature de la méthode

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

Ici, car `F`de signature inclut un type pointeur, elle peut uniquement être écrite dans un contexte unsafe. Toutefois, le contexte non sécurisé peut être introduit soit en rendant l’intégralité de la classe unsafe, comme c’est le cas dans `A`, ou en incluant un `unsafe` modificateur dans la déclaration de méthode, comme c’est le cas dans `B`.

## <a name="pointer-types"></a>Types de pointeur

Dans un contexte unsafe, un *type* ([Types](types.md)) peut être un *type_pointeur* ainsi qu’un *value_type* ou un *reference_type* . Toutefois, un *type_pointeur* peuvent également être utilisés dans un `typeof` expression ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) en dehors d’un contexte unsafe ainsi l’utilisation n’est pas dangereuse.

```antlr
type_unsafe
    : pointer_type
    ;
```

A *type_pointeur* est écrit comme un *unmanaged_type* ou le mot clé `void`, suivi par un `*` jeton :

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

Le type spécifié avant le `*` dans un pointeur de type est appelé le ***type référent*** du type pointeur. Il représente le type de la variable vers laquelle pointe une valeur du type pointeur.

Contrairement aux références (les valeurs des types référence), des pointeurs ne sont pas suivies par le garbage collector, le garbage collector ne connaît pas de pointeurs et des données à laquelle ils pointent. Pour cette raison, un pointeur n’est pas autorisée pour pointer vers une référence ou un struct qui contient des références et le type référent d’un pointeur doit être un *unmanaged_type*.

Un *unmanaged_type* est n’importe quel type qui n’est pas un *reference_type* ou type construit et ne contient pas *reference_type* ou construit des champs de type à n’importe quel niveau de imbrication. En d’autres termes, une *unmanaged_type* est une des opérations suivantes :

*  `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, ou `bool`.
*  N’importe quel *type_de_liste*.
*  N’importe quel *type_pointeur*.
*  Un est défini par l’utilisateur *struct_type* qui n’est pas un type construit et qui contient les champs de *unmanaged_type*s uniquement.

La règle intuitive pour combiner les pointeurs et références est que les référents des références (objets) sont autorisées à contenir des pointeurs, mais référents des pointeurs ne sont pas autorisées à contenir des références.

Certains des exemples de types pointeur sont donnés dans le tableau ci-dessous :

| __Exemple__ | __Description__                               |
|-------------|-----------------------------------------------|
| `byte*`     | pointeur vers `byte`                             |
| `char*`     | pointeur vers `char`                             |
| `int**`     | Pointeur vers `int`                   |
| `int*[]`    | Tableau unidimensionnel de pointeurs vers `int` |
| `void*`     | Pointeur vers un type inconnu                       |

Pour une implémentation donnée, tous les types de pointeur doivent avoir la même taille et la représentation.

Contrairement à C et C++, lorsque plusieurs pointeurs sont déclarés dans la même déclaration, en C# le `*` est écrit, ainsi que le type sous-jacent uniquement, pas comme une indirection sur chaque nom de pointeur. Exemple :

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

La valeur d’un pointeur ayant le type `T*` représente l’adresse d’une variable de type `T`. L’opérateur d’indirection de pointeur `*` ([indirection de pointeur](unsafe-code.md#pointer-indirection)) peut être utilisé pour accéder à cette variable. Par exemple, prenons une variable `P` de type `int*`, l’expression `*P` désigne le `int` variable trouvée à l’adresse contenue dans `P`.

Comme une référence d’objet, un pointeur peut être `null`. Application de l’opérateur d’indirection à un `null` pointeur entraîne un comportement défini par l’implémentation. Un pointeur avec la valeur `null` est représenté par tous les bits à zéro.

Le `void*` type représente un pointeur vers un type inconnu. Étant donné que le type référent est inconnu, l’opérateur d’indirection ne peut pas être appliqué à un pointeur de type `void*`, ni de toute opération arithmétique de données peut être effectuée sur ce type de pointeur. Toutefois, un pointeur de type `void*` peut être casté en un autre type de pointeur (et vice versa).

Types de pointeurs sont une catégorie distincte de types. Contrairement aux types référence et les types valeur, les types pointeur n’héritent pas de `object` et il n’existe aucune conversion entre types pointeur et `object`. En particulier, le boxing et unboxing ([Boxing et unboxing](types.md#boxing-and-unboxing)) ne sont pas pris en charge pour les pointeurs. Toutefois, les conversions sont autorisées entre différents types pointeur et entre les types pointeur et les types intégraux. Cette opération est décrite dans [conversions de pointeur](unsafe-code.md#pointer-conversions).

Un *type_pointeur* ne peut pas être utilisé comme argument de type ([types construits](types.md#constructed-types)) et l’inférence de type ([l’inférence de Type](expressions.md#type-inference)) échoue sur les appels de méthode générique qui aurait déduit un argument soit un type de pointeur de type.

Un *type_pointeur* peut être utilisé comme type d’un champ volatile ([des champs volatiles](classes.md#volatile-fields)).

Bien que les pointeurs peuvent être passés en tant que `ref` ou `out` paramètres, cela peut entraîner un comportement non défini, étant donné que le pointeur peut ainsi être définie pour pointer vers une variable locale qui n’existe plus lorsque la méthode appelée est retournée, soit l’objet fixe pour lequel il utilisé pour pointer, est fixe n’est plus. Exemple :

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

Une méthode peut retourner une valeur d’un certain type, et ce type peut être un pointeur. Par exemple, lorsqu’un pointeur vers une séquence contiguë de `int`s, nombre d’éléments de cette séquence et dans d’autres `int` valeur, la méthode suivante retourne l’adresse de cette valeur dans cette séquence, si une correspondance est trouvée ; sinon, elle retourne `null`:

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

Dans un contexte unsafe, plusieurs constructions sont disponibles pour l’exploitation de pointeurs :

*  Le `*` opérateur peut être utilisé pour effectuer l’indirection de pointeur ([indirection de pointeur](unsafe-code.md#pointer-indirection)).
*  Le `->` opérateur peut être utilisé pour accéder à un membre d’un struct via un pointeur ([accès des membres pointeurs](unsafe-code.md#pointer-member-access)).
*  Le `[]` opérateur peut être utilisé pour indexer un pointeur ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)).
*  Le `&` opérateur peut être utilisé pour obtenir l’adresse d’une variable ([l’opérateur address-of](unsafe-code.md#the-address-of-operator)).
*  Le `++` et `--` opérateurs peuvent être utilisés pour incrémenter et décrémenter des pointeurs ([pointeur incrémentation et décrémentation](unsafe-code.md#pointer-increment-and-decrement)).
*  Le `+` et `-` opérateurs peuvent être utilisés pour effectuer des opérations arithmétiques de pointeur ([opération arithmétique de pointeur](unsafe-code.md#pointer-arithmetic)).
*  Le `==`, `!=`, `<`, `>`, `<=`, et `=>` opérateurs peuvent être utilisés pour comparer les pointeurs ([comparaison de pointeurs](unsafe-code.md#pointer-comparison)).
*  Le `stackalloc` opérateur peut être utilisé pour allouer de la mémoire à partir de la pile des appels ([mémoires tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).
*  Le `fixed` instruction peut être utilisée pour fixer temporairement une variable afin de son adresse peut être obtenue ([l’instruction fixed](unsafe-code.md#the-fixed-statement)).

## <a name="fixed-and-moveable-variables"></a>Variables fixes et déplaçables

L’opérateur address-of ([l’opérateur address-of](unsafe-code.md#the-address-of-operator)) et le `fixed` instruction ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) diviser les variables en deux catégories : ***Correction des variables*** et ***variables déplaçables***.

Variables fixes résident dans des emplacements de stockage qui ne sont pas affectés par l’opération du garbage collector. (Exemples de variables fixes incluent les variables locales, les paramètres de valeur et les variables créées par déréférencer les pointeurs.) En revanche, variables déplaçables résident dans des emplacements de stockage qui sont susceptibles d’être déplacée ou supprimée par le garbage collector. (Les exemples de variables déplaçables les champs dans des objets et des éléments de tableaux.)

Le `&` opérateur ([l’opérateur address-of](unsafe-code.md#the-address-of-operator)) permet à l’adresse d’une variable fixe doivent être obtenues sans restrictions. Toutefois, car une variable déplaçable peut être déplacée ou supprimée par le garbage collector, l’adresse d’une variable déplaçable peut uniquement être obtenue en utilisant un `fixed` instruction ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) et cette adresse reste valide uniquement pendant la durée de cette `fixed` instruction.

Plus précisément, tant que variable fixe est une des opérations suivantes :

*  Une variable issue d’un *simple_name* ([noms simples](expressions.md#simple-names)) qui fait référence à une variable locale ou un paramètre de valeur, sauf si la variable est capturée par une fonction anonyme.
*  Une variable issue d’un *member_access* ([l’accès au membre](expressions.md#member-access)) sous la forme `V.I`, où `V` est une variable fixe d’un *struct_type*.
*  Une variable issue d’un *pointer_indirection_expression* ([indirection de pointeur](unsafe-code.md#pointer-indirection)) sous la forme `*P`, un *pointer_member_access* ([Accès des membres pointeurs](unsafe-code.md#pointer-member-access)) sous la forme `P->I`, ou un *pointer_element_access* ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)) sous la forme `P[E]`.

Toutes les autres variables sont classés en tant que variables pouvant être déplacés.

Notez qu’un champ statique est classé comme une variable mobile. Notez également qu’un `ref` ou `out` paramètre est classé comme une variable déplaçable, même si l’argument fourni pour le paramètre est une variable fixe. Enfin, notez qu’une variable produite par le déréférencement de pointeur est toujours classée comme tant que variable fixe.

## <a name="pointer-conversions"></a>Conversions de pointeurs

Dans un contexte unsafe, le jeu de conversions implicites disponibles ([conversions implicites](conversions.md#implicit-conversions)) est étendu pour inclure les conversions de pointeur implicites suivantes :

*  À partir d’un *type_pointeur* au type `void*`.
*  À partir de la `null` littéral à aucun *type_pointeur*.

En outre, dans un contexte non sécurisé, le jeu de conversions implicites disponibles ([conversions explicites](conversions.md#explicit-conversions)) est étendu pour inclure les conversions de pointeur explicites suivantes :

*  À partir d’un *type_pointeur* n’importe quel autre *type_pointeur*.
*  À partir de `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, ou `ulong` aux *type_pointeur*.
*  À partir d’un *type_pointeur* à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, ou `ulong`.

Enfin, dans un contexte non sécurisé, le jeu de conversions implicites standards ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) inclut la conversion de pointeur suivantes :

*  À partir d’un *type_pointeur* au type `void*`.

Conversions entre deux types de pointeur jamais changer la valeur réelle du pointeur. En d’autres termes, une conversion d’un type pointeur vers un autre n’a aucun effet sur l’adresse sous-jacente fournie par le pointeur.

Lorsqu’un type pointeur est converti en un autre, si le pointeur résultant n’est pas correctement aligné pour le type désigné, le comportement est indéfini si le résultat est déréférencé. En règle générale, le concept « aligné correctement » est transitif : si un pointeur vers un type `A` est aligné correctement pour un pointeur vers un type `B`, qui, à son tour, est aligné correctement pour un pointeur vers un type `C`, puis un pointeur vers type `A`est aligné correctement pour un pointeur vers un type `C`.

Prenons le cas suivant dans lequel une variable ayant un type est accessible via un pointeur vers un type différent :

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

Lorsqu’un type pointeur est converti en un pointeur vers un octet, le résultat pointe vers l’octet traité le plus bas de la variable. Des incréments successifs du résultat, jusqu'à la taille de la variable, génèrent des pointeurs vers les octets restants de cette variable. Par exemple, la méthode suivante affiche chacun des huit octets dans un double en tant que valeur hexadécimale :

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

Bien sûr, le résultat obtenu dépend de l’endianness.

Mappages entre les pointeurs et les entiers sont définis par l’implémentation. Toutefois, sur 32 * et architectures de processeur 64 bits avec un espace d’adressage linéaire, les conversions de pointeurs vers ou à partir de types intégraux se comportent généralement comme des conversions de `uint` ou `ulong` des valeurs, respectivement, ou à partir de ces types intégraux.

### <a name="pointer-arrays"></a>Tableaux de pointeur

Dans un contexte unsafe, tableaux de pointeurs peuvent être construits. Uniquement certaines conversions qui s’appliquent à d’autres types de tableau sont autorisées sur les tableaux de pointeur :

*  La conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) à partir d’un *array_type* à `System.Array` et les interfaces qu’il implémente également s’appliquent aux tableaux de pointeur. Toutefois, toute tentative d’accéder aux éléments du tableau via `System.Array` ou les interfaces qu’il implémente entraîne une exception au moment de l’exécution, car les types pointeur ne sont pas convertibles en `object`.
*  Conversions de référence implicites et explicites ([conversions de référence implicite](conversions.md#implicit-reference-conversions), [conversions de référence explicite](conversions.md#explicit-reference-conversions)) à partir d’un type de tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et ses interfaces de base génériques jamais pour appliquer aux tableaux de pointeur, étant donné que les types pointeur ne peut pas être utilisés en tant qu’arguments de type, et aucune conversion depuis les types pointeur pour les types non pointeur.
*  La conversion de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) à partir de `System.Array` et les interfaces qu’il implémente à aucun *array_type* s’appliquent aux tableaux de pointeur.
*  Les conversions de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) à partir de `System.Collections.Generic.IList<S>` et sa base d’interfaces pour un type de tableau unidimensionnel `T[]` jamais s’applique aux tableaux de pointeur, étant donné que les types pointeur ne peut pas être utilisé comme arguments de type, et aucune conversion depuis les types pointeur pour les types non pointeur.

Ces restrictions signifient que l’extension pour le `foreach` instruction sur les tableaux décrits dans [l’instruction foreach](statements.md#the-foreach-statement) ne peut pas être appliqué aux tableaux de pointeur. Au lieu de cela, une instruction foreach du formulaire

```csharp
foreach (V v in x) embedded_statement
```

où le type de `x` est un type de tableau sous la forme `T[,,...,]`, `N` est le nombre de dimensions moins 1 et `T` ou `V` est un type pointeur, est développé à l’aide de boucles imbriquées comme suit :

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

Les variables `a`, `i0`, `i1`,..., `iN` ne sont pas visibles par ou accessibles `x` ou *embedded_statement* ou tout autre code source du programme. La variable `v` est en lecture seule dans l’instruction incorporée. Si n’est pas une conversion explicite ([conversions de pointeur](unsafe-code.md#pointer-conversions)) à partir de `T` (le type d’élément) pour `V`, une erreur se produit et aucune autre étape n’est effectuées. Si `x` a la valeur `null`, un `System.NullReferenceException` est levée au moment de l’exécution.

## <a name="pointers-in-expressions"></a>Pointeurs dans les expressions

Dans un contexte unsafe, une expression peut produire un résultat d’un type pointeur, mais en dehors d’un contexte unsafe, c’est une erreur de compilation pour une expression à être d’un type pointeur. Plus précisément, en dehors d’un contexte unsafe une erreur de compilation se produit si n’importe quel *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([accès aux membres ](expressions.md#member-access)), *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)), ou *element_access* ([accès à un élément](expressions.md#element-access)) est d’un type pointeur.

Dans un contexte unsafe, la *primary_no_array_creation_expression* ([expressions primaires](expressions.md#primary-expressions)) et *unary_expression* ([opérateurs unaires](expressions.md#unary-operators)) productions autorisent les constructions supplémentaires suivantes :

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

Ces constructions sont décrites dans les sections suivantes. La priorité et l’associativité des opérateurs unsafe est impliquée par la grammaire.

### <a name="pointer-indirection"></a>Indirection de pointeur

Un *pointer_indirection_expression* se compose d’un astérisque (`*`) suivie d’un *unary_expression*.

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

L’unaire `*` opérateur désigne l’indirection de pointeur et est utilisé pour obtenir la variable vers laquelle pointe un pointeur. Le résultat de l’évaluation `*P`, où `P` est une expression d’un type pointeur `T*`, est une variable de type `T`. C’est une erreur de compilation pour appliquer l’unaire `*` opérateur à une expression de type `void*` ou une expression qui n’est pas d’un type pointeur.

L’effet d’appliquer l’unaire `*` opérateur à un `null` pointeur est défini par l’implémentation. En particulier, il n’existe aucune garantie que cette opération lève une `System.NullReferenceException`.

Si une valeur non valide a été affectée au pointeur, le comportement de l’unaire `*` opérateur n’est pas défini. Parmi les valeurs non valides pour déréférencer un pointeur par l’unaire `*` opérateur sont une adresse alignée de façon inappropriée pour le type désigné (consultez l’exemple dans [conversions de pointeur](unsafe-code.md#pointer-conversions)) et l’adresse d’une variable après le fin de sa durée de vie.

À des fins d’analyse de l’assignation définie, une variable produite en évaluant une expression sous la forme `*P` est considérée comme initialement assignée ([initialement affecté variables](variables.md#initially-assigned-variables)).

### <a name="pointer-member-access"></a>Accès au membre de pointeur

A *pointer_member_access* se compose d’un *primary_expression*, suivi par un «`->`» jeton, suivie une *identificateur* et un facultatif*type_argument_list*.

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

Dans un accès de membre de pointeur de la forme `P->I`, `P` doit être une expression d’un type pointeur autre que `void*`, et `I` doit indiquer un membre accessible du type auquel `P` points.

Un accès de membre de pointeur de la forme `P->I` est évalué exactement comme `(*P).I`. Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection). Pour obtenir une description de l’opérateur d’accès de membre (`.`), consultez [l’accès au membre](expressions.md#member-access).

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

le `->` opérateur est utilisé pour accéder aux champs et appeler une méthode d’un struct via un pointeur. Étant donné que l’opération `P->I` est équivalente à `(*P).I`, le `Main` méthode tout aussi bien aurait pu être écrite :

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

### <a name="pointer-element-access"></a>Accès aux éléments de pointeur

Un *pointer_element_access* se compose d’un *primary_no_array_creation_expression* suivie d’une expression entre «`[`« et »`]`».

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

Dans un accès à un élément pointeur sous la forme `P[E]`, `P` doit être une expression d’un type pointeur autre que `void*`, et `E` doit être une expression qui peut être implicitement convertie en `int`, `uint`, `long`, ou `ulong`.

Un accès à un élément pointeur sous la forme `P[E]` est évalué exactement comme `*(P + E)`. Pour obtenir une description de l’opérateur d’indirection de pointeur (`*`), consultez [indirection de pointeur](unsafe-code.md#pointer-indirection). Pour obtenir une description de l’opérateur d’addition de pointeur (`+`), consultez [opération arithmétique de pointeur](unsafe-code.md#pointer-arithmetic).

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

un accès aux éléments de pointeur est utilisé pour initialiser la mémoire tampon de caractères dans un `for` boucle. Étant donné que l’opération `P[E]` est équivalente à `*(P + E)`, l’exemple tout aussi bien aurait pu être écrite :

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

L’opérateur d’accès élément pointeur ne vérifie pas hors limites pour les erreurs et le comportement lors de l’accès à un élément hors limites n’est pas défini. Cela est identique à C et C++.

### <a name="the-address-of-operator"></a>L’opérateur address-of

Un *addressof_expression* se compose d’une esperluette (`&`) suivie d’un *unary_expression*.

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

Étant donné une expression `E` qui est de type `T` et est classé en tant que variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), la construction `&E` calcule l’adresse de la variable donnée par `E`. Le type du résultat est `T*` et est classé en tant que valeur. Une erreur de compilation se produit si `E` n’est pas classé en tant que variable, si `E` est classé comme une variable locale en lecture seule, ou si `E` désigne une variable mobile. Dans le dernier cas, une instruction fixed ([l’instruction fixed](unsafe-code.md#the-fixed-statement)) peut être utilisé pour « fixer temporairement » la variable avant d’obtenir son adresse. Comme indiqué dans [l’accès au membre](expressions.md#member-access), en dehors d’un constructeur d’instance ou un constructeur statique pour un struct ou une classe qui définit un `readonly` champ, ce champ est considéré comme une valeur, et non une variable. Par conséquent, son adresse ne peut pas être prise. De même, l’adresse d’une constante ne peut pas être effectuée.

Le `&` opérateur ne nécessite pas de son argument sans aucun doute, mais après un `&` opération, la variable à laquelle l’opérateur est appliqué est considérée comme étant définitivement assignée dans le chemin d’exécution dans lequel l’opération se produit. Il s’agit de la responsabilité du programmeur pour vous assurer que l’initialisation correcte de la variable se produise réellement dans cette situation.

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

`i` est considéré comme définitivement assignée après la `&i` opération permettant d’initialiser `p`. L’assignation à `*p` initialise en vigueur `i`, mais l’inclusion de cette initialisation est la responsabilité du programmeur, et aucune erreur de compilation se produirait si l’assignation a été supprimée.

Les règles d’assignation définie pour le `&` opérateur existe telles que l’initialisation redondante des variables locales peut être évitée. Par exemple, de nombreuses API externes associent un pointeur vers une structure qui est remplie par l’API. Appels à ces API passent généralement l’adresse d’une variable locale de struct, et sans la règle, l’initialisation redondante de la variable de struct serait nécessaire.

### <a name="pointer-increment-and-decrement"></a>Décrément et l’incrément de pointeur

Dans un contexte unsafe, la `++` et `--` opérateurs ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators) et [Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)) peut être appliqué au pointeur les variables de tous les types sauf `void*`. Par conséquent, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

Les opérateurs produisent les mêmes résultats que `x + 1` et `x - 1`, respectivement ([opération arithmétique de pointeur](unsafe-code.md#pointer-arithmetic)). En d’autres termes, pour une variable de pointeur de type `T*`, le `++` opérateur ajoute `sizeof(T)` à l’adresse contenue dans la variable et le `--` opérateur soustrait `sizeof(T)` à partir de l’adresse contenue dans la variable.

Si un incrément de pointeur ou de décrémentation opération dépasse le domaine du type pointeur, le résultat est défini par l’implémentation, mais aucune exception n’est générée.

### <a name="pointer-arithmetic"></a>Opération arithmétique de pointeur

Dans un contexte unsafe, la `+` et `-` opérateurs ([opérateur d’Addition](expressions.md#addition-operator) et [opérateur de soustraction](expressions.md#subtraction-operator)) peut être appliqué aux valeurs de tous les types de pointeur à l’exception `void*`. Par conséquent, pour chaque type de pointeur `T*`, les opérateurs suivants sont définis implicitement :

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

Étant donné une expression `P` d’un type pointeur `T*` et une expression `N` de type `int`, `uint`, `long`, ou `ulong`, les expressions `P + N` et `N + P` calculer le valeur de pointeur de type `T*` obtenu en ajoutant `N * sizeof(T)` à l’adresse fournie par `P`. De même, l’expression `P - N` calcule la valeur du pointeur de type `T*` qui résulte de la soustraction `N * sizeof(T)` à partir de l’adresse fournie par `P`.

Étant donné deux expressions, `P` et `Q`, d’un type pointeur `T*`, l’expression `P - Q` calcule la différence entre les adresses fournies par `P` et `Q` puis divise cette différence par `sizeof(T)`. Le type du résultat est toujours `long`. En effet, `P - Q` est calculée comme `((long)(P) - (long)(Q)) / sizeof(T)`.

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

```
p - q = -14
q - p = 14
```

Si une opération arithmétique de pointeur dépasse le domaine du type pointeur, le résultat est tronqué de manière défini par l’implémentation, mais aucune exception n’est générée.

### <a name="pointer-comparison"></a>Comparaison de pointeurs

Dans un contexte unsafe, la `==`, `!=`, `<`, `>`, `<=`, et `=>` opérateurs ([relationnels et opérateurs de test de type](expressions.md#relational-and-type-testing-operators)) peut être appliqué aux valeurs de tous les types de pointeur. Les opérateurs de comparaison de pointeur sont :

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

Car il existe une conversion implicite à partir de n’importe quel type de pointeur à la `void*` type, les opérandes de n’importe quel type de pointeur peut être comparée à l’aide de ces opérateurs. Les opérateurs de comparaison comparent les adresses fournies par les deux opérandes comme s’il s’agissait d’entiers non signés.

### <a name="the-sizeof-operator"></a>L’opérateur sizeof

Le `sizeof` opérateur retourne le nombre d’octets occupés par une variable d’un type donné. Le type spécifié en tant qu’opérande pour `sizeof` doit être un *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

Le résultat de la `sizeof` opérateur est une valeur de type `int`. Pour certains des types prédéfinis, le `sizeof` opérateur génère une valeur constante, comme indiqué dans le tableau ci-dessous.


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

Pour tous les autres types, le résultat de la `sizeof` opérateur est défini par l’implémentation et est classé en tant que valeur, pas une constante.

L’ordre dans lequel les membres sont placés dans un struct n’est pas spécifié.

À des fins d’alignement, il peut être remplissage au début d’un struct, au sein d’un struct et à la fin de la structure sans nom. Le contenu des bits utilisés comme remplissage est indéterminé.

Lorsqu’il est appliqué à un opérande de type struct, le résultat est le nombre total d’octets dans une variable de ce type, y compris le remplissage.

## <a name="the-fixed-statement"></a>L’instruction fixed

Dans un contexte unsafe, la *embedded_statement* ([instructions](statements.md)) production autorise une construction supplémentaire, le `fixed` instruction, qui est utilisée pour « réparer » une variable déplaçable telles que son adresse reste constante pendant la durée de l’instruction.

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

Chaque *fixed_pointer_declarator* déclare une variable locale de la donnée *type_pointeur* et initialise cette variable locale avec l’adresse calculée par le correspondantes *fixed_ pointer_initializer*. Une variable locale déclarée dans un `fixed` instruction est accessible dans les *fixed_pointer_initializer*s situés à droite de la déclaration de cette variable, puis dans le *embedded_statement* de la `fixed` instruction. Une variable locale déclarée par un `fixed` instruction est considéré comme en lecture seule. Une erreur de compilation se produit si l’instruction incorporée tente de modifier cette variable locale (via l’attribution ou le `++` et `--` opérateurs) ou passer en tant qu’un `ref` ou `out` paramètre.

Un *fixed_pointer_initializer* peut prendre l’une des opérations suivantes :

*  Le jeton «`&`» suivie d’un *variable_reference* ([règles précises de détermination d’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)) à une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) d’un type non managé `T`, à condition que le type `T*` est implicitement convertible vers le type pointeur donné dans le `fixed` instruction. Dans ce cas, l’initialiseur calcule l’adresse de la variable donnée, et il est garantie que la variable reste à une adresse fixe pendant la durée de la `fixed` instruction.
*  Une expression d’un *array_type* avec des éléments d’un type non managé `T`, à condition que le type `T*` est implicitement convertible vers le type pointeur donné dans le `fixed` instruction. Dans ce cas, l’initialiseur calcule l’adresse du premier élément du tableau, et l’intégralité du tableau est garanti reste à une adresse fixe pendant la durée de la `fixed` instruction. Si l’expression de tableau est null ou si le tableau n’a aucun élément, l’initialiseur calcule un adresse d’égal à zéro.
*  Une expression de type `string`, à condition que le type `char*` est implicitement convertible vers le type pointeur donné dans le `fixed` instruction. Dans ce cas, l’initialiseur calcule l’adresse du premier caractère dans la chaîne, et la chaîne entière est garantie reste à une adresse fixe pendant la durée de la `fixed` instruction. Le comportement de la `fixed` instruction est défini par l’implémentation si l’expression de chaîne est null.
*  Un *simple_name* ou *member_access* qui fait référence à un membre de mémoire tampon de taille fixe d’une variable déplaçable, fournie par le type du membre de mémoire tampon de taille fixe est implicitement convertible vers le type pointeur donné dans la `fixed` instruction. Dans ce cas, l’initialiseur calcule un pointeur vers le premier élément de la mémoire tampon de taille fixe ([fixe de mémoires tampons de taille dans les expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), et la mémoire tampon de taille fixe est garantie reste à une adresse fixe pendant la durée de la `fixed`instruction.

Pour chaque adresse calculée par un *fixed_pointer_initializer* la `fixed` instruction permet de garantir que la variable référencée par l’adresse n’est pas être déplacée ou supprimée par le garbage collector pendant la durée de la `fixed` instruction. Par exemple, si l’adresse calculée par un *fixed_pointer_initializer* fait référence à un champ d’un objet ou un élément d’une instance de tableau, le `fixed` instruction garantit que l’instance d’objet conteneur n’est pas déplacé ou supprimé pendant la durée de vie de l’instruction.

Il est la responsabilité du programmeur pour vous assurer que les pointeurs créé par `fixed` instructions ne survivent pas au-delà de l’exécution de ces instructions. Par exemple, lorsque des pointeurs créés par `fixed` instructions sont passées à des API externes, il est la responsabilité du programmeur pour vous assurer que les API ne conservent pas ces pointeurs de mémoire.

Objets fixes peuvent entraîner la fragmentation du tas (car ils ne peuvent pas être déplacées). Pour cette raison, les objets doivent être fixe uniquement lorsque cela est absolument nécessaire, puis uniquement pour le montant le plus court de temps possible.

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

illustre plusieurs utilisations de la `fixed` instruction. La première instruction fixe et obtient l’adresse d’un champ statique, la seconde instruction fixe et obtient l’adresse d’un champ d’instance et la troisième instruction fixe et obtient l’adresse d’un élément de tableau. Dans chaque cas il aurait été une erreur d’utiliser la mise à jour `&` opérateur dans la mesure où les variables sont classés en tant que variables pouvant être déplacés.

La quatrième `fixed` instruction dans l’exemple ci-dessus produit un résultat similaire à la troisième.

Cet exemple de la `fixed` instruction utilise `string`:

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

Dans un contexte unsafe, éléments de tableau de tableaux unidimensionnels sont stockés dans l’ordre croissant des index, en commençant par index `0` et se terminant par index `Length - 1`. Pour les tableaux multidimensionnels, tableau, les éléments sont stockés tels que les indices de la dimension la plus à droite sont incrémentés tout d’abord, puis la dimension immédiatement à gauche, et ainsi de suite vers la gauche. Au sein d’un `fixed` instruction qui obtient un pointeur `p` à une instance de tableau `a`, les valeurs de pointeur comprises entre `p` à `p + a.Length - 1` représentent les adresses des éléments dans le tableau. De même, les variables comprises entre `p[0]` à `p[a.Length - 1]` représentent les éléments de tableau réel. Compte tenu du mode dans lequel les tableaux sont stockés, nous pouvons traiter un tableau de n’importe quelle dimension comme s’il s’agissait linéaire.

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

```
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

un `fixed` instruction est utilisée pour résoudre un tableau de son adresse peut être passé à une méthode qui accepte un pointeur.

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

une instruction fixed est utilisée pour résoudre une mémoire tampon de taille fixe d’un struct, son adresse peut être utilisée comme pointeur.

Un `char*` valeur produite en corrigeant une instance de chaîne toujours pointe vers une chaîne se terminant par null. Dans une instruction fixed qui obtient un pointeur `p` à une instance de chaîne `s`, les valeurs de pointeur comprises entre `p` à `p + s.Length - 1` représentent des adresses des caractères dans la chaîne et la valeur de pointeur `p + s.Length` pointe toujours vers un caractère null (le caractère avec la valeur `'\0'`).

Modification d’objets de type managé via des pointeurs fixes peuvent entraîne un comportement non défini. Par exemple, étant donné que les chaînes sont immuables, il est la responsabilité du programmeur pour vous assurer que les caractères référencés par un pointeur vers une chaîne fixe ne sont pas modifiées.

Le caractère nul de terminaison automatique des chaînes est particulièrement utile lors de l’appel d’API externes qui attendent des chaînes de style « C ». Toutefois, notez qu’une instance de chaîne est autorisée à contenir des caractères null. Si ces caractères null sont présents, la chaîne apparaît tronquée traitée comme une valeur null se terminant par `char*`.

## <a name="fixed-size-buffers"></a>Mémoires tampons de taille fixe

Mémoires tampons de taille fixe sont utilisées pour déclarer des ensembles de lignes « style C » en tant que membres de structures et sont principalement utiles pour l’interface avec les API non managées.

### <a name="fixed-size-buffer-declarations"></a>Déclarations de mémoire tampon de taille fixe

Un ***fixe de mémoire tampon de taille*** est un membre qui représente le stockage pour une mémoire tampon de longueur fixe des variables d’un type donné. Une déclaration de mémoire tampon de taille fixe introduit un ou plusieurs tampons de taille fixe d’un type d’élément donné. Mémoires tampons de taille fixe sont autorisées uniquement dans les déclarations de struct et peut se produire uniquement dans des contextes unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)).

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

Une déclaration de mémoire tampon de taille fixe peut inclure un ensemble d’attributs ([attributs](attributes.md)), un `new` modificateur ([modificateurs](classes.md#modifiers)), une combinaison valide des quatre modificateurs d’accès ([Type paramètres et contraintes](classes.md#type-parameters-and-constraints)) et un `unsafe` modificateur ([contextes Unsafe](unsafe-code.md#unsafe-contexts)). Les attributs et les modificateurs s’appliquent à tous les membres déclarés par la déclaration de mémoire tampon de taille fixe. C’est une erreur pour le même modificateur apparaît plusieurs fois dans une déclaration de mémoire tampon de taille fixe.

Une déclaration de mémoire tampon de taille fixe n’est pas autorisée à inclure le `static` modificateur.

Le type d’élément de mémoire tampon d’une déclaration de mémoire tampon de taille fixe Spécifie le type d’élément de la tampon introduites par la déclaration. Le type d’élément de mémoire tampon doit être un des types prédéfinis `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, ou `bool`.

Le type d’élément de mémoire tampon est suivi d’une liste de déclarateurs de mémoire tampon de taille fixe, chacun d’eux présente un nouveau membre. Un déclarateur de mémoire tampon de taille fixe se compose d’un identificateur qui nomme le membre, suivi d’une expression constante entre `[` et `]` jetons. L’expression constante indique le nombre d’éléments dans le membre introduite par ce déclarateur de mémoire tampon de taille fixe. Le type de l’expression constante doit être implicitement convertible en type `int`, et la valeur doit être un entier positif non nul.

Les éléments d’une mémoire tampon de taille fixe sont assurés d’être disposés séquentiellement en mémoire.

Une déclaration de mémoire tampon de taille fixe qui déclare plusieurs mémoires tampons de taille fixe est équivalente à plusieurs déclarations d’une déclaration de mémoire tampon de taille fixe unique avec les mêmes attributs et les types d’éléments. Exemple :

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

Recherche de membres ([opérateurs](expressions.md#operators)) d’une taille fixe, membre de la mémoire tampon passe exactement comme la recherche de membres d’un champ.

Une mémoire tampon de taille fixe peut être référencé dans une expression qui utilise un *simple_name* ([l’inférence de Type](expressions.md#type-inference)) ou un *member_access* ([la vérification de compilation résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Lorsqu’un membre de mémoire tampon de taille fixe est référencé comme un nom simple, l’effet est le même qu’un accès de membre de la forme `this.I`, où `I` est le membre de mémoire tampon de taille fixe.

Dans un accès de membre de la forme `E.I`si `E` est d’un type struct et recherche d’un membre de `I` car type struct identifie un membre de taille fixe, puis `E.I` est évaluée une classifiée comme suit :

*  Si l’expression `E.I` ne se produit pas dans un contexte unsafe, une erreur de compilation se produit.
*  Si `E` est classé en tant que valeur, une erreur de compilation se produit.
*  Sinon, si `E` est une variable déplaçable ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)) et l’expression `E.I` n’est pas un *fixed_pointer_initializer* ([fixe instruction](unsafe-code.md#the-fixed-statement)), une erreur de compilation se produit.
*  Sinon, `E` fait référence à une variable fixe et le résultat de l’expression est un pointeur vers le premier élément du membre de mémoire tampon de taille fixe `I` dans `E`. Le résultat est de type `S*`, où `S` est le type d’élément de `I`et est classé en tant que valeur.

Les éléments suivants de la mémoire tampon de taille fixe sont accessibles à l’aide d’opérations de pointeur du premier élément. Contrairement à l’accès aux tableaux, accès aux éléments d’une mémoire tampon de taille fixe est une opération risquée et n’est pas activée.

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

### <a name="definite-assignment-checking"></a>Vérification de l’assignation définie

Mémoires tampons de taille fixe ne sont pas soumis à la vérification de l’assignation définie ([assignation définie](variables.md#definite-assignment)), et les membres de mémoire tampon de taille fixe sont ignorés pour les besoins de l’état d’assignation de variables de type struct.

Lorsque la variable de struct contenant extérieur d’un membre de mémoire tampon de taille fixe est une variable statique, une variable d’instance d’une instance de classe ou d’un élément de tableau, les éléments de la mémoire tampon de taille fixe sont automatiquement initialisés à leurs valeurs par défaut ([Valeurs par défaut](variables.md#default-values)). Dans tous les autres cas, le contenu initial d’une mémoire tampon de taille fixe n’est pas défini.

## <a name="stack-allocation"></a>Allocation de piles

Dans un contexte unsafe, une déclaration de variable locale ([déclarations de variables locales](statements.md#local-variable-declarations)) peut inclure un initialiseur d’allocation de pile qui alloue la mémoire à partir de la pile des appels.

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

Le *unmanaged_type* indique le type des éléments qui seront stockées dans le nouvel emplacement alloué, et le *expression* indique le nombre de ces éléments. Prises ensemble, ils spécifient la taille d’allocation requis. Étant donné que la taille d’une allocation de pile ne peut pas être négatif, une erreur de compilation pour spécifier le nombre d’éléments en tant qu’est un *constant_expression* qui prend une valeur négative.

Un initialiseur d’allocation de pile du formulaire `stackalloc T[E]` requiert `T` à être un type non managé ([types pointeur](unsafe-code.md#pointer-types)) et `E` pour être une expression de type `int`. Alloue de la construction `E * sizeof(T)` octets à partir de l’appel de la pile et retourne un pointeur, de type `T*`, vers le bloc nouvellement alloué. Si `E` est une valeur négative, le comportement est indéfini. Si `E` est égal à zéro, alors aucune allocation n’est effectuée, et le pointeur retourné est défini par l’implémentation. Si l’absence de mémoire disponible insuffisante pour allouer un bloc de la taille donnée, un `System.StackOverflowException` est levée.

Le contenu de la mémoire nouvellement allouée n’est pas défini.

Initialiseurs d’allocation de pile ne sont pas autorisées dans `catch` ou `finally` blocs ([l’instruction try](statements.md#the-try-statement)).

Il n’existe aucun moyen de libérer explicitement la mémoire allouée à l’aide de `stackalloc`. Tous les blocs de mémoire allouée à la pile créées pendant l’exécution d’une fonction membre sont automatiquement ignorés lors de la fonction membre. Cela correspond à la `alloca` (fonction), une extension couramment utilisés dans les implémentations C et C++.

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

un `stackalloc` initialiseur est utilisé dans le `IntToString` méthode allouer un tampon de 16 caractères sur la pile. La mémoire tampon est automatiquement supprimée lorsque la méthode est retournée.

## <a name="dynamic-memory-allocation"></a>Allocation de mémoire dynamique

À l’exception de la `stackalloc` opérateur, C# ne fournit aucuns constructions prédéfinies pour la gestion de la mémoire collectée non garbage. Ces services sont généralement fournis en prenant en charge des bibliothèques de classes ou importés directement depuis le système d’exploitation sous-jacent. Par exemple, le `Memory` classe ci-dessous illustre comment les fonctions du tas du système d’exploitation sous-jacent peuvent être accessible à partir de C# :

```csharp
using System;
using System.Runtime.InteropServices;

public unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    static int ph = GetProcessHeap();

    // Private instance constructor to prevent instantiation.
    private Memory() {}

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size) {
        void* result = HeapAlloc(ph, HEAP_ZERO_MEMORY, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count) {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd) {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd) {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block) {
        if (!HeapFree(ph, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size) {
        void* result = HeapReAlloc(ph, HEAP_ZERO_MEMORY, block, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block) {
        int result = HeapSize(ph, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    static extern int GetProcessHeap();

    [DllImport("kernel32")]
    static extern void* HeapAlloc(int hHeap, int flags, int size);

    [DllImport("kernel32")]
    static extern bool HeapFree(int hHeap, int flags, void* block);

    [DllImport("kernel32")]
    static extern void* HeapReAlloc(int hHeap, int flags, void* block, int size);

    [DllImport("kernel32")]
    static extern int HeapSize(int hHeap, int flags, void* block);
}
```

Un exemple qui utilise le `Memory` classe est donnée ci-dessous :

```csharp
class Test
{
    static void Main() {
        unsafe {
            byte* buffer = (byte*)Memory.Alloc(256);
            try {
                for (int i = 0; i < 256; i++) buffer[i] = (byte)i;
                byte[] array = new byte[256];
                fixed (byte* p = array) Memory.Copy(buffer, p, 256); 
            }
            finally {
                Memory.Free(buffer);
            }
            for (int i = 0; i < 256; i++) Console.WriteLine(array[i]);
        }
    }
}
```

L’exemple alloue 256 octets de mémoire via `Memory.Alloc` et initialise le bloc de mémoire avec des valeurs croissantes comprise entre 0 et 255. Il alloue un tableau d’octets de 256 éléments, puis utilise `Memory.Copy` pour copier le contenu du bloc de mémoire dans le tableau d’octets. Enfin, le bloc de mémoire est libéré à l’aide de `Memory.Free` et le contenu du tableau d’octets est sortie sur la console.
