---
ms.openlocfilehash: 088c4a77cecde490c556c44c239a3496f896582e
ms.sourcegitcommit: 4ddf18d000734c1b6d0a48127bf338086fc3f2c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616139"
---
# <a name="types"></a>Types

Les types de C# langage sont divisés en deux catégories principales : ***types valeur*** et ***types référence***. Les types valeur et les types référence peuvent être des ***types génériques***, qui prennent un ou plusieurs ***paramètres de type***. Les paramètres de type peuvent désigner à la fois les types valeur et les types référence.

```antlr
type
    : value_type
    | reference_type
    | type_parameter
    | type_unsafe
    ;
```

La catégorie finale de types, pointeurs, est uniquement disponible dans du code unsafe. Ce sujet est abordé plus en détail dans [types de pointeurs](unsafe-code.md#pointer-types).

Les types valeur diffèrent des types référence dans le fait que les variables des types valeur contiennent directement leurs données, alors que les variables des types référence stockent les ***références*** à leurs données, ce dernier étant appelé ***objets***. Avec les types référence, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter l’objet référencé par l’autre variable. Avec les types valeur, les variables disposent chacune de leur propre copie des données, et il n’est pas possible que les opérations sur l’une d’elles affectent l’autre.

C#le système de type de est unifié de telle sorte qu’une valeur de n’importe quel type peut être traitée en tant qu’objet. Chaque type dans C# dérive directement ou indirectement du type `object`, et `object` est la classe de base fondamentale de tous les types. Les valeurs des types référence sont considérées comme des objets simplement en affichant les valeurs en tant que type `object`. Les valeurs des types valeur sont traitées comme des objets en effectuant des opérations de boxing et d’unboxing ([boxing et unboxing](types.md#boxing-and-unboxing)).

## <a name="value-types"></a>Types de valeur

Un type valeur est soit un type struct, soit un type énumération. C#fournit un ensemble de types struct prédéfinis appelés ***types simples***. Les types simples sont identifiés à l’aide de mots réservés.

```antlr
value_type
    : struct_type
    | enum_type
    ;

struct_type
    : type_name
    | simple_type
    | nullable_type
    ;

simple_type
    : numeric_type
    | 'bool'
    ;

numeric_type
    : integral_type
    | floating_point_type
    | 'decimal'
    ;

integral_type
    : 'sbyte'
    | 'byte'
    | 'short'
    | 'ushort'
    | 'int'
    | 'uint'
    | 'long'
    | 'ulong'
    | 'char'
    ;

floating_point_type
    : 'float'
    | 'double'
    ;

nullable_type
    : non_nullable_value_type '?'
    ;

non_nullable_value_type
    : type
    ;

enum_type
    : type_name
    ;
```

Contrairement à une variable d’un type référence, une variable d’un type valeur peut contenir la valeur `null` uniquement si le type valeur est un type Nullable.  Pour chaque type valeur qui n’autorise pas les valeurs NULL, il existe un type valeur Nullable correspondant indiquant le même jeu de valeurs plus la valeur `null`.

L’assignation à une variable d’un type valeur crée une copie de la valeur assignée. Cela diffère d’une assignation à une variable d’un type référence, qui copie la référence, mais pas l’objet identifié par la référence.

### <a name="the-systemvaluetype-type"></a>Type System. ValueType

Tous les types valeur héritent implicitement de la classe `System.ValueType`qui, à son tour, hérite de la classe `object`. Il n’est pas possible pour un type de dériver d’un type valeur, et les types valeur sont donc implicitement sealed ([classes sealed](classes.md#sealed-classes)).

Notez que `System.ValueType` n’est pas *Value_type*. Au lieu de cela, il s’agit d’un *class_type* à partir duquel tous les *Value_type*sont dérivés automatiquement.

### <a name="default-constructors"></a>Constructeurs par défaut

Tous les types valeur déclarent implicitement un constructeur d’instance sans paramètre public appelé le ***constructeur par défaut***. Le constructeur par défaut retourne une instance initialisée à zéro appelée « ***valeur par défaut*** » pour le type de valeur :

*  Pour tous les *simple_type*s, la valeur par défaut est la valeur produite par un modèle binaire de zéros :
    * Pour `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`et `ulong`, la valeur par défaut est `0`.
    * Par `char`, la valeur par défaut est `'\x0000'`.
    * Par `float`, la valeur par défaut est `0.0f`.
    * Par `double`, la valeur par défaut est `0.0d`.
    * Par `decimal`, la valeur par défaut est `0.0m`.
    * Par `bool`, la valeur par défaut est `false`.
*  Pour un `E`*enum_type* , la valeur par défaut est `0`, convertie en type `E`.
*  Pour un *struct_type*, la valeur par défaut est la valeur produite en affectant à tous les champs de type valeur leur valeur par défaut et tous les champs de type référence à `null`.
*  Pour un *nullable_type* , la valeur par défaut est une instance pour laquelle la propriété `HasValue` a la valeur false et la propriété `Value` est non définie. La valeur par défaut est également connue sous le nom de ***valeur null*** du type Nullable.

Comme tout autre constructeur d’instance, le constructeur par défaut d’un type valeur est appelé à l’aide de l’opérateur `new`. Pour des raisons d’efficacité, cette exigence n’est pas destinée à faire en sorte que l’implémentation génère un appel de constructeur. Dans l’exemple ci-dessous, les variables `i` et `j` sont toutes deux initialisées à zéro.

```csharp
class A
{
    void F() {
        int i = 0;
        int j = new int();
    }
}
```

Étant donné que chaque type valeur a implicitement un constructeur d’instance sans paramètre public, il n’est pas possible pour un type struct de contenir une déclaration explicite d’un constructeur sans paramètre. Un type struct est toutefois autorisé à déclarer des constructeurs d’instances paramétrables ([constructeurs](structs.md#constructors)).

### <a name="struct-types"></a>Types struct

Un type struct est un type valeur qui peut déclarer des constantes, des champs, des méthodes, des propriétés, des indexeurs, des opérateurs, des constructeurs d’instance, des constructeurs statiques et des types imbriqués. La déclaration des types struct est décrite dans [déclarations de struct](structs.md#struct-declarations).

### <a name="simple-types"></a>Types simples

C#fournit un ensemble de types struct prédéfinis appelés ***types simples***. Les types simples sont identifiés par le biais de mots réservés, mais ces mots réservés sont simplement des alias pour les types struct prédéfinis dans l’espace de noms `System`, comme décrit dans le tableau ci-dessous.


| __Mot réservé__ | __Type avec alias__ |
|-------------------|------------------|
| `sbyte`           | `System.SByte`   | 
| `byte`            | `System.Byte`    | 
| `short`           | `System.Int16`   | 
| `ushort`          | `System.UInt16`  | 
| `int`             | `System.Int32`   | 
| `uint`            | `System.UInt32`  | 
| `long`            | `System.Int64`   | 
| `ulong`           | `System.UInt64`  | 
| `char`            | `System.Char`    | 
| `float`           | `System.Single`  | 
| `double`          | `System.Double`  | 
| `bool`            | `System.Boolean` | 
| `decimal`         | `System.Decimal` | 

Étant donné qu’un type simple est un alias de type struct, chaque type simple a des membres. Par exemple, `int` a les membres déclarés dans `System.Int32` et les membres hérités de `System.Object`, et les instructions suivantes sont autorisées :

```csharp
int i = int.MaxValue;           // System.Int32.MaxValue constant
string s = i.ToString();        // System.Int32.ToString() instance method
string t = 123.ToString();      // System.Int32.ToString() instance method
```

Les types simples diffèrent des autres types struct en ce qu’ils autorisent des opérations supplémentaires :

*  La plupart des types simples autorisent la création de valeurs en écrivant des *littéraux* ([littéraux](lexical-structure.md#literals)). Par exemple, `123` est un littéral de type `int` et `'a'` est un littéral de type `char`. C#n’offre aucune provision pour les littéraux de types struct en général, et les valeurs autres que celles par défaut des autres types struct sont finalement toujours créées via les constructeurs d’instance de ces types struct.
*  Quand les opérandes d’une expression sont toutes des constantes de type simple, il est possible que le compilateur évalue l’expression au moment de la compilation. Une telle expression est appelée *constant_expression* ([expressions constantes](expressions.md#constant-expressions)). Les expressions qui impliquent des opérateurs définis par d’autres types struct ne sont pas considérées comme des expressions constantes.
*  Grâce aux déclarations de `const`, il est possible de déclarer des constantes des types simples ([constantes](classes.md#constants)). Il n’est pas possible d’avoir des constantes d’autres types struct, mais un effet similaire est fourni par `static readonly` champs.
*  Les conversions impliquant des types simples peuvent participer à l’évaluation des opérateurs de conversion définis par d’autres types struct, mais un opérateur de conversion défini par l’utilisateur ne peut jamais participer à l’évaluation d’un autre opérateur défini par l’utilisateur ([évaluation de conversions définies par l’utilisateur](conversions.md#evaluation-of-user-defined-conversions)).

### <a name="integral-types"></a>Types intégraux

C#prend en charge neuf types intégraux : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`et `char`. Les types intégraux ont les tailles et plages de valeurs suivantes :

*  Le type de `sbyte` représente des entiers 8 bits signés dont les valeurs sont comprises entre-128 et 127.
*  Le type de `byte` représente des entiers 8 bits non signés dont la valeur est comprise entre 0 et 255.
*  Le type de `short` représente des entiers 16 bits signés dont les valeurs sont comprises entre-32768 et 32767.
*  Le type de `ushort` représente des entiers 16 bits non signés dont la valeur est comprise entre 0 et 65535.
*  Le type de `int` représente des entiers 32 bits signés dont les valeurs sont comprises entre-2147483648 et 2147483647.
*  Le type de `uint` représente des entiers 32 bits non signés dont la valeur est comprise entre 0 et 4294967295.
*  Le type de `long` représente des entiers 64 bits signés dont la valeur est comprise entre-9223372036854775808 et 9223372036854775807.
*  Le type de `ulong` représente des entiers 64 bits non signés dont la valeur est comprise entre 0 et 18446744073709551615.
*  Le type de `char` représente des entiers 16 bits non signés dont la valeur est comprise entre 0 et 65535. Le jeu de valeurs possibles pour le type de `char` correspond au jeu de caractères Unicode. Bien que `char` ait la même représentation que `ushort`, les opérations autorisées sur un type ne sont pas toutes autorisées sur l’autre.

Les opérateurs unaires et binaires de type intégral fonctionnent toujours avec la précision 32 bits signée, la précision 32 bits non signée, la précision 64 bits signée ou la précision non signée de 64 bits :

*  Pour les opérateurs unaires `+` et `~`, l’opérande est converti en type `T`, où `T` est le premier d' `int`, `uint`, `long`et `ulong` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision de type `T`, et le type du résultat est `T`.
*  Pour l’opérateur de `-` unaire, l’opérande est converti en type `T`, où `T` est le premier d' `int` et `long` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision de type `T`, et le type du résultat est `T`. L’opérateur de `-` unaire ne peut pas être appliqué aux opérandes de type `ulong`.
*  Pour les opérateurs binaires `+`, `-`, `*`, `/`, `%`, `&`, `^`, `|`, `==`, `!=`, `>`, `<`, `>=`et `<=` , les opérandes sont convertis en type `T`, où `T` est le premier d' `int`, `uint`, `long`et `ulong` qui peut représenter entièrement toutes les valeurs possibles des deux opérandes. L’opération est ensuite effectuée à l’aide de la précision de type `T`, et le type du résultat est `T` (ou `bool` pour les opérateurs relationnels). Il n’est pas autorisé qu’un opérande soit de type `long` et que l’autre soit de type `ulong` avec les opérateurs binaires.
*  Pour les opérateurs binaires `<<` et `>>`, l’opérande de gauche est converti en type `T`, où `T` est le premier d' `int`, `uint`, `long`et `ulong` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision de type `T`, et le type du résultat est `T`.

Le type de `char` est classé en tant que type intégral, mais il diffère des autres types intégraux de deux manières :

*  Il n’existe pas de conversion implicite d’autres types en type `char`. En particulier, même si les types `sbyte`, `byte`et `ushort` ont des plages de valeurs qui sont entièrement représentables à l’aide du type `char`, les conversions implicites de `sbyte`, `byte`ou `ushort` à `char` n’existent pas.
*  Les constantes du type de `char` doivent être écrites en *character_literal*s ou en tant que *integer_literal*s en association avec un cast en type `char`. Par exemple, `(char)10` est identique à `'\x000A'`.

Les opérateurs et les instructions `checked` et `unchecked` sont utilisés pour contrôler le contrôle de dépassement pour les opérations arithmétiques de type intégral et les conversions ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)). Dans un contexte de `checked`, un dépassement de capacité génère une erreur au moment de la compilation ou provoque la levée d’une `System.OverflowException`. Dans un contexte de `unchecked`, les dépassements de capacité sont ignorés et tous les bits de poids fort qui ne tiennent pas dans le type de destination sont ignorés.

### <a name="floating-point-types"></a>Types à virgule flottante

C#prend en charge deux types à virgule flottante : `float` et `double`. Les types `float` et `double` sont représentés à l’aide des formats IEEE 754 simple précision et double précision 64 bits de 32, qui fournissent les ensembles de valeurs suivants :

*  Zéro positif et zéro négatif. Dans la plupart des cas, le zéro positif et le zéro négatif se comportent de la même façon que la valeur simple zéro, mais certaines opérations distinguent les deux ([opérateur de division](expressions.md#division-operator)).
*  Infini positif et infini négatif. Les infinis sont produites par des opérations telles que la Division d’un nombre différent de zéro par zéro. Par exemple, `1.0 / 0.0` produit une infinité positive et `-1.0 / 0.0` produit une infini négatif.
*  Valeur ***not-a-Number*** , souvent abrégée Nan. Les valeurs NaN sont produites par des opérations à virgule flottante non valides, telles que la Division de zéro par zéro.
*  Le jeu fini de valeurs non null de la forme `s * m * 2^e`, où `s` a la valeur 1 ou-1, et `m` et `e` sont déterminés par le type à virgule flottante particulier : pour `float`, `0 < m < 2^24` et `-149 <= e <= 104`, et pour `double`, `0 < m < 2^53` et `-1075 <= e <= 970`. Les nombres à virgule flottante dénormalisés sont considérés comme des valeurs non nulles valides.

Le type de `float` peut représenter des valeurs allant d’environ `1.5 * 10^-45` à `3.4 * 10^38` avec une précision de 7 chiffres.

Le type de `double` peut représenter des valeurs allant d’environ `5.0 * 10^-324` à `1.7 × 10^308` avec une précision de 15-16 chiffres.

Si l’un des opérandes d’un opérateur binaire est d’un type à virgule flottante, l’autre opérande doit être d’un type intégral ou d’un type à virgule flottante, et l’opération est évaluée comme suit :

*  Si l’un des opérandes est d’un type intégral, cet opérande est converti en type à virgule flottante de l’autre opérande.
*  Ensuite, si l’un des opérandes est de type `double`, l’autre opérande est converti en `double`, l’opération est effectuée à l’aide d’au moins `double` plage et précision, et le type du résultat est `double` (ou `bool` pour les opérateurs relationnels).
*  Dans le cas contraire, l’opération est effectuée à l’aide d’au moins `float` plage et précision, et le type du résultat est `float` (ou `bool` pour les opérateurs relationnels).

Les opérateurs à virgule flottante, y compris les opérateurs d’assignation, ne produisent jamais d’exceptions. À la place, dans des situations exceptionnelles, les opérations à virgule flottante produisent zéro, Infinity ou NaN, comme décrit ci-dessous :

*  Si le résultat d’une opération à virgule flottante est trop petit pour le format de destination, le résultat de l’opération devient un zéro positif ou un zéro négatif.
*  Si le résultat d’une opération à virgule flottante est trop grand pour le format de destination, le résultat de l’opération devient une infini positif ou négatif.
*  Si une opération à virgule flottante n’est pas valide, le résultat de l’opération devient NaN.
*  Si l’un des opérandes ou les deux d’une opération à virgule flottante est NaN, le résultat de l’opération devient NaN.

Les opérations à virgule flottante peuvent être effectuées avec une précision supérieure à celle du type de résultat de l’opération. Par exemple, certaines architectures matérielles prennent en charge un type à virgule flottante « étendu » ou « long double » avec une plage et une précision supérieures à celles du type de `double`, et effectuent implicitement toutes les opérations en virgule flottante à l’aide de ce type de précision plus élevée. Le fait d’effectuer des opérations de calcul en virgule flottante avec moins de précision, et au lieu d’exiger une implémentation pour perdre à la fois les performances et la précision, C# offre un type de précision plus élevé. à utiliser pour toutes les opérations en virgule flottante. En dehors de la diffusion de résultats plus précis, cela a rarement des effets mesurables. Toutefois, dans les expressions de la forme `x * y / z`, où la multiplication produit un résultat en dehors de la plage de `double`, mais la Division suivante ramène le résultat temporaire à la plage de `double`, le fait que l’expression soit évaluée dans une un format de plage plus élevé peut entraîner la production d’un résultat fini à la place d’une infini.

### <a name="the-decimal-type"></a>Type décimal

Le type `decimal` est un type de données 128 bits adapté aux calculs financiers et monétaires. Le type de `decimal` peut représenter des valeurs allant de `1.0 * 10^-28` à environ `7.9 * 10^28` avec 28-29 chiffres significatifs.

L’ensemble fini de valeurs de type `decimal` se présentent sous la forme `(-1)^s * c * 10^-e`, où le signe `s` est 0 ou 1, le coefficient `c` est donné par `0 <= *c* < 2^96`, et la `e` d’échelle est telle que `0 <= e <= 28`. Le type de `decimal` ne prend pas en charge les zéros, les infinis ou les NaN signés. Un `decimal` est représenté sous la forme d’un entier 96 bits mis à l’échelle par une puissance de dix. Pour `decimal`s avec une valeur absolue inférieure à `1.0m`, la valeur est exacte jusqu’au 28 décimal, mais pas plus. Pour les `decimal`avec une valeur absolue supérieure ou égale à `1.0m`, la valeur est exacte à 28 ou 29 chiffres. Contrairement aux types de données `float` et `double`, les nombres fractionnaires décimaux tels que 0,1 peuvent être représentés exactement dans la représentation `decimal`. Dans les représentations `float` et `double`, ces nombres sont souvent des fractions infinies, ce qui rend ces représentations plus sujettes aux erreurs d’arrondi.

Si l’un des opérandes d’un opérateur binaire est de type `decimal`, l’autre opérande doit être de type intégral ou de type `decimal`. Si un opérande de type intégral est présent, il est converti en `decimal` avant l’exécution de l’opération.

Le résultat d’une opération sur les valeurs de type `decimal` est que ce qui résulte du calcul d’un résultat exact (préservation de l’échelle, tel que défini pour chaque opérateur), puis à l’arrondi pour s’ajuster à la représentation. Les résultats sont arrondis à la valeur représentable la plus proche et, lorsqu’un résultat est proche de deux valeurs représentables, à la valeur qui a un nombre pair dans la position de chiffre la moins significative (c’est ce qu’on appelle le « arrondi bancaire »). Un résultat zéro a toujours un signe de 0 et une échelle de 0.

Si une opération arithmétique décimale produit une valeur inférieure ou égale à `5 * 10^-29` en valeur absolue, le résultat de l’opération devient zéro. Si une `decimal` opération arithmétique produit un résultat trop grand pour le format `decimal`, une `System.OverflowException` est levée.

Le type de `decimal` a une plus grande précision, mais une plage plus petite que les types à virgule flottante. Ainsi, les conversions des types à virgule flottante en `decimal` peuvent générer des exceptions de dépassement de capacité et les conversions de `decimal` en types virgule flottante peuvent entraîner une perte de précision. Pour ces raisons, il n’existe pas de conversion implicite entre les types à virgule flottante et les `decimal`, et sans casts explicites, il n’est pas possible de mélanger des opérandes à virgule flottante et `decimal` dans la même expression.

### <a name="the-bool-type"></a>Type bool

Le type de `bool` représente des quantités logiques booléennes. Les valeurs possibles de type `bool` sont `true` et `false`.

Il n’existe pas de conversion standard entre `bool` et d’autres types. En particulier, le type de `bool` est distinct et distinct des types intégraux, et une valeur de `bool` ne peut pas être utilisée à la place d’une valeur intégrale, et vice versa.

Dans les langages C++ C et, une valeur intégrale nulle ou à virgule flottante, ou un pointeur NULL peut être converti en valeur booléenne `false`, et une valeur intégrale ou à virgule flottante différente de zéro, ou un pointeur non NULL peut être converti en valeur booléenne `true`. Dans C#, ces conversions sont accomplies en comparant explicitement une valeur intégrale ou à virgule flottante à zéro, ou en comparant explicitement une référence d’objet à `null`.

### <a name="enumeration-types"></a>Types d'énumération

Un type énumération est un type distinct avec des constantes nommées. Chaque type énumération a un type sous-jacent, qui doit être `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`. L’ensemble de valeurs du type énumération est le même que l’ensemble de valeurs du type sous-jacent. Les valeurs du type d’énumération ne sont pas limitées aux valeurs des constantes nommées. Les types énumération sont définis par le biais de déclarations d’énumération ([déclarations enum](enums.md#enum-declarations)).

### <a name="nullable-types"></a>Types Nullable

Un type Nullable peut représenter toutes les valeurs de son ***type sous-jacent*** plus une valeur null supplémentaire. Un type Nullable est écrit `T?`, où `T` est le type sous-jacent. Cette syntaxe est raccourcie pour `System.Nullable<T>`, et les deux formulaires peuvent être utilisés indifféremment.

Un ***type valeur qui n’autorise pas*** les valeurs NULL est inversement tout type valeur autre que `System.Nullable<T>` et son raccourci `T?` (pour n’importe quel `T`), plus tout paramètre de type qui est imposé comme étant un type valeur non Nullable (autrement dit, tout paramètre de type avec une `struct` contrainte). Le type de `System.Nullable<T>` spécifie la contrainte de type valeur pour `T` ([contraintes de paramètre de type](classes.md#type-parameter-constraints)), ce qui signifie que le type sous-jacent d’un type Nullable peut être n’importe quel type valeur n’acceptant pas les valeurs NULL. Le type sous-jacent d’un type Nullable ne peut pas être un type Nullable ou un type référence. Par exemple, `int??` et `string?` sont des types non valides.

Une instance d’un type Nullable `T?` a deux propriétés publiques en lecture seule :

*  Propriété `HasValue` de type `bool`
*  Propriété `Value` de type `T`

Une instance pour laquelle `HasValue` a la valeur true est dite non null. Une instance non null contient une valeur connue et `Value` retourne cette valeur.

Une instance pour laquelle `HasValue` a la valeur false est considérée comme étant null. Une instance null a une valeur non définie. Toute tentative de lecture de la `Value` d’une instance null entraîne la levée d’une `System.InvalidOperationException`. Le processus d’accès à la propriété `Value` d’une instance Nullable est appelé ***désencapsulage***.

En plus du constructeur par défaut, chaque type Nullable `T?` a un constructeur public qui accepte un argument unique de type `T`. À partir d’une valeur `x` de type `T`, un appel de constructeur au format

```csharp
new T?(x)
```
crée une instance non null de `T?` pour laquelle la propriété `Value` est `x`. Le processus de création d’une instance non null d’un type Nullable pour une valeur donnée est appelé « ***Wrapping***».

Les conversions implicites sont disponibles à partir du littéral `null` pour `T?` ([conversions de littéraux NULL](conversions.md#null-literal-conversions)) et de `T` à `T?` ([conversions nullables implicites](conversions.md#implicit-nullable-conversions)).

## <a name="reference-types"></a>Types référence

Un type référence est un type de classe, un type interface, un type tableau ou un type délégué.

```antlr
reference_type
    : class_type
    | interface_type
    | array_type
    | delegate_type
    ;

class_type
    : type_name
    | 'object'
    | 'dynamic'
    | 'string'
    ;

interface_type
    : type_name
    ;

array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;

delegate_type
    : type_name
    ;
```

Une valeur de type référence est une référence à une ***instance*** du type, ce dernier connu sous le nom d' ***objet***. La valeur spéciale `null` est compatible avec tous les types de référence et indique l’absence d’une instance.

### <a name="class-types"></a>Types de classes

Un type de classe définit une structure de données qui contient des données membres (constantes et champs), des fonctions membres (méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et des types imbriqués. Les types de classe prennent en charge l’héritage, mécanisme dans lequel les classes dérivées peuvent étendre et spécialiser les classes de base. Les instances de types de classe sont créées à l’aide de *object_creation_expression*s ([expressions de création d’objet](expressions.md#object-creation-expressions)).

Les types de classe sont décrits dans les [classes](classes.md).

Certains types de classe prédéfinis ont une signification particulière C# dans le langage, comme décrit dans le tableau ci-dessous.


| __Type de classe__     | __Description__                                         |
|--------------------|---------------------------------------------------------|
| `System.Object`    | Classe de base fondamentale de tous les autres types. Consultez [le type d’objet](types.md#the-object-type). | 
| `System.String`    | Type de chaîne de la C# langue. Consultez [type de chaîne](types.md#the-string-type).         |
| `System.ValueType` | Classe de base de tous les types valeur. Consultez [le type System. ValueType](types.md#the-systemvaluetype-type).          |
| `System.Enum`      | Classe de base de tous les types ENUM. Consultez [enums](enums.md).              |
| `System.Array`     | Classe de base de tous les types tableau. Consultez [Tableaux](arrays.md).             |
| `System.Delegate`  | Classe de base de tous les types délégués. Consultez [délégués](delegates.md).          |
| `System.Exception` | Classe de base de tous les types d’exception. Consultez [exceptions](exceptions.md).         |

### <a name="the-object-type"></a>Type d’objet

Le type de classe `object` est la classe de base fondamentale de tous les autres types. Chaque type dans C# directement ou indirectement dérive du type de classe `object`.

Le mot clé `object` est simplement un alias pour la classe prédéfinie `System.Object`.

### <a name="the-dynamic-type"></a>Type dynamique

Le type de `dynamic`, comme `object`, peut faire référence à n’importe quel objet. Lorsque des opérateurs sont appliqués à des expressions de type `dynamic`, leur résolution est différée jusqu’à ce que le programme soit exécuté. Ainsi, si l’opérateur ne peut pas être légalement appliqué à l’objet référencé, aucune erreur n’est donnée durant la compilation. Au lieu de cela, une exception est levée lorsque la résolution de l’opérateur échoue au moment de l’exécution.

Son objectif est d’autoriser la liaison dynamique, qui est décrite en détail dans [liaison dynamique](expressions.md#dynamic-binding).

`dynamic` est considéré comme identique à `object` à l’exception des points suivants :

*  Les opérations sur les expressions de type `dynamic` peuvent être liées dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).
*  L’inférence de type ([inférence de type](expressions.md#type-inference)) préfère `dynamic` sur `object` si les deux sont des candidats.

En raison de cette équivalence, les éléments suivants sont les suivants :

*  Il existe une conversion d’identité implicite entre `object` et `dynamic`, et entre les types construits qui sont identiques lors du remplacement d' `dynamic` par `object`
*  Les conversions implicites et explicites vers et à partir de `object` s’appliquent également à et à partir de `dynamic`.
*  Les signatures de méthode qui sont les mêmes lors du remplacement de `dynamic` par des `object` sont considérées comme la même signature
*  Le type `dynamic` ne peut pas être distingué du `object` au moment de l’exécution.
*  Une expression de type `dynamic` est appelée ***expression dynamique***.

### <a name="the-string-type"></a>Type de chaîne

Le type de `string` est un type de classe sealed qui hérite directement de `object`. Les instances de la classe `string` représentent des chaînes de caractères Unicode.

Les valeurs du type de `string` peuvent être écrites sous la forme de littéraux de chaîne ([littéraux de chaîne](lexical-structure.md#string-literals)).

Le mot clé `string` est simplement un alias pour la classe prédéfinie `System.String`.

### <a name="interface-types"></a>Types interface

Une interface définit un contrat. Une classe ou un struct qui implémente une interface doit adhérer à son contrat. Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.

Les types d’interface sont décrits dans [interfaces](interfaces.md).

### <a name="array-types"></a>Types de tableaux

Un tableau est une structure de données qui contient zéro, une ou plusieurs variables accessibles par le biais d’index calculés. Les variables contenues dans un tableau, également appelées éléments du tableau, sont toutes du même type, et ce type est appelé le type d’élément du tableau.

Les types tableau sont décrits dans les [tableaux](arrays.md).

### <a name="delegate-types"></a>Types délégués

Un délégué est une structure de données qui fait référence à une ou plusieurs méthodes. Pour les méthodes d’instance, elle fait également référence aux instances d’objet correspondantes.

L’équivalent le plus proche d’un délégué en C C++ ou est un pointeur de fonction, mais alors qu’un pointeur de fonction ne peut référencer que des fonctions statiques, un délégué peut faire référence à la fois à des méthodes statiques et d’instance. Dans ce dernier cas, le délégué stocke non seulement une référence au point d’entrée de la méthode, mais également une référence à l’instance d’objet sur laquelle appeler la méthode.

Les types délégués sont décrits dans [délégués](delegates.md).

## <a name="boxing-and-unboxing"></a>Boxing et unboxing

Le concept de boxing et d’unboxing est central C#pour le système de type de. Il fournit un pont entre *Value_type*s et *reference_type*s en autorisant la conversion d’une valeur d’un *Value_type* en type `object`. Le boxing et l’unboxing permettent une vue unifiée du système de type dans lequel une valeur de n’importe quel type peut finalement être traitée comme un objet.

### <a name="boxing-conversions"></a>Conversions boxing

Une conversion boxing permet de convertir implicitement un *Value_type* en *reference_type*. Les conversions boxing suivantes existent :

*  De n’importe quel *Value_type* vers le type `object`.
*  De n’importe quel *Value_type* vers le type `System.ValueType`.
*  Depuis n’importe quel *non_nullable_value_type* vers n’importe quel *INTERFACE_TYPE* implémenté par *Value_type*.
*  De n’importe quel *nullable_type* à n’importe quel *INTERFACE_TYPE* implémenté par le type sous-jacent de *nullable_type*.
*  De n’importe quel *enum_type* vers le type `System.Enum`.
*  À partir de n’importe quel *nullable_type* avec un *enum_type* sous-jacent au type `System.Enum`.
*  Notez qu’une conversion implicite à partir d’un paramètre de type sera exécutée comme conversion boxing si, au moment de l’exécution, elle finit par convertir un type valeur en un type référence ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters)).

Le boxing d’une valeur d’un *non_nullable_value_type* consiste à allouer une instance d’objet et à copier la valeur *non_nullable_value_type* dans cette instance.

Le boxing d’une valeur d’un *nullable_type* produit une référence null s’il s’agit de la valeur `null` (`HasValue` est `false`), ou le résultat de la désencapsulation et du boxing de la valeur sous-jacente dans le cas contraire.

Le processus réel de boxing d’une valeur d’un *non_nullable_value_type* est mieux expliqué en imaginant l’existence d’une ***classe Boxing***générique, qui se comporte comme si elle avait été déclarée comme suit :

```csharp
sealed class Box<T>: System.ValueType
{
    T value;

    public Box(T t) {
        value = t;
    }
}
```

La conversion boxing d’une valeur `v` de type `T` consiste à exécuter l’expression `new Box<T>(v)`et à retourner l’instance résultante sous la forme d’une valeur de type `object`. Ainsi, les instructions
```csharp
int i = 123;
object box = i;
```
correspondent de manière conceptuelle à
```csharp
int i = 123;
object box = new Box<int>(i);
```

Une classe Boxing comme `Box<T>` ci-dessus n’existe pas réellement et le type dynamique d’une valeur boxed n’est en fait pas un type de classe. Au lieu de cela, une valeur boxed de type `T` a le type dynamique `T`et une vérification de type dynamique à l’aide de l’opérateur `is` peut simplement référencer le type `T`. Par exemple :
```csharp
int i = 123;
object box = i;
if (box is int) {
    Console.Write("Box contains an int");
}
```
génère la chaîne «`Box contains an int`» sur la console.

Une conversion boxing implique de faire une copie de la valeur convertie. Cela est différent d’une conversion d’un *reference_type* en type `object`, dans lequel la valeur continue à faire référence à la même instance et est simplement considérée comme le type moins dérivé `object`. Par exemple, étant donné la déclaration
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
les instructions suivantes
```csharp
Point p = new Point(10, 10);
object box = p;
p.x = 20;
Console.Write(((Point)box).x);
```
génère la valeur 10 dans la console, car l’opération boxing implicite qui se produit lors de l’affectation de `p` à `box` entraîne la copie de la valeur de `p`. A `Point` été déclarée comme `class` à la place, la valeur 20 était sortie, car `p` et `box` référençaient la même instance.

### <a name="unboxing-conversions"></a>Conversions unboxing

Une conversion unboxing permet de convertir explicitement un *reference_type* en *Value_type*. Les conversions unboxing suivantes existent :

*  Du type `object` à n’importe quel *Value_type*.
*  Du type `System.ValueType` à n’importe quel *Value_type*.
*  De n’importe quel *INTERFACE_TYPE* à n’importe quel *non_nullable_value_type* qui implémente le *INTERFACE_TYPE*.
*  De n’importe quel *INTERFACE_TYPE* à n’importe quel *nullable_type* dont le type sous-jacent implémente le *INTERFACE_TYPE*.
*  Du type `System.Enum` à n’importe quel *enum_type*.
*  Du type `System.Enum` à n’importe quel *nullable_type* avec un *enum_type*sous-jacent.
*  Notez qu’une conversion explicite en paramètre de type sera exécutée comme conversion unboxing si, au moment de l’exécution, elle finit par convertir un type référence en un type valeur ([conversions dynamiques explicites](conversions.md#explicit-dynamic-conversions)).

Une opération d’unboxing sur un *non_nullable_value_type* consiste à vérifier en premier lieu que l’instance de l’objet est une valeur boxed du *non_nullable_value_type*donné, puis à copier la valeur hors de l’instance.

La conversion unboxing en *nullable_type* produit la valeur null de *nullable_type* si l’opérande source est `null`, ou le résultat encapsulé de l’unboxing de l’instance d’objet vers le type sous-jacent du *nullable_type* dans le cas contraire.

En faisant référence à la classe de boxing imaginaire décrite dans la section précédente, une conversion unboxing d’un objet `box` en *value_type* `T` consiste à exécuter l’expression `((Box<T>)box).value`. Ainsi, les instructions
```csharp
object box = 123;
int i = (int)box;
```
correspondent de manière conceptuelle à
```csharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

Pour qu’une conversion unboxing vers un *non_nullable_value_type* donné aboutisse au moment de l’exécution, la valeur de l’opérande source doit être une référence à une valeur boxed de ce *non_nullable_value_type*. Si l’opérande source est `null`, un `System.NullReferenceException` est levé. Si l’opérande source est une référence à un objet incompatible, une `System.InvalidCastException` est levée.

Pour qu’une conversion unboxing vers un *nullable_type* donné aboutisse au moment de l’exécution, la valeur de l’opérande source doit être `null` ou une référence à une valeur boxed du *non_nullable_value_type* sous-jacent du *nullable_type*. Si l’opérande source est une référence à un objet incompatible, une `System.InvalidCastException` est levée.

## <a name="constructed-types"></a>Types construits

Une déclaration de type générique, par elle-même, dénote un ***type générique indépendant*** utilisé comme « Blueprint » pour former de nombreux types différents, en appliquant des ***arguments de type***. Les arguments de type sont écrits entre crochets pointus (`<` et `>`) juste après le nom du type générique. Un type qui comprend au moins un argument de type est appelé ***type construit***. Un type construit peut être utilisé à la plupart des emplacements dans le langage dans lequel un nom de type peut apparaître. Un type générique indépendant ne peut être utilisé que dans un *typeof_expression* ([l’opérateur typeof](expressions.md#the-typeof-operator)).

Les types construits peuvent également être utilisés dans les expressions en tant que noms simples ([noms simples](expressions.md#simple-names)) ou lors de l’accès à un membre ([accès aux membres](expressions.md#member-access)).

Quand un *namespace_or_type_name* est évalué, seuls les types génériques avec le nombre correct de paramètres de type sont pris en compte. Par conséquent, il est possible d’utiliser le même identificateur pour identifier les différents types, à condition que les types aient des nombres de paramètres de type différents. Cela est utile pour mélanger des classes génériques et non génériques dans le même programme :

```csharp
namespace Widgets
{
    class Queue {...}
    class Queue<TElement> {...}
}

namespace MyApplication
{
    using Widgets;

    class X
    {
        Queue q1;            // Non-generic Widgets.Queue
        Queue<int> q2;       // Generic Widgets.Queue
    }
}
```

Un *type_name* peut identifier un type construit bien qu’il ne spécifie pas directement les paramètres de type. Cela peut se produire lorsqu’un type est imbriqué dans une déclaration de classe générique et que le type d’instance de la déclaration conteneur est implicitement utilisé pour la recherche de nom ([types imbriqués dans les classes génériques](classes.md#nested-types-in-generic-classes)) :

```csharp
class Outer<T>
{
    public class Inner {...}

    public Inner i;                // Type of i is Outer<T>.Inner
}
```

Dans du code unsafe, un type construit ne peut pas être utilisé en tant que *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

### <a name="type-arguments"></a>Arguments de type

Chaque argument dans une liste d’arguments de type est simplement un *type*.

```antlr
type_argument_list
    : '<' type_arguments '>'
    ;

type_arguments
    : type_argument (',' type_argument)*
    ;

type_argument
    : type
    ;
```

Dans du code non sécurisé ([code non sécurisé](unsafe-code.md)), un *type_argument* ne peut pas être un type pointeur. Chaque argument de type doit satisfaire toutes les contraintes sur le paramètre de type correspondant ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).

### <a name="open-and-closed-types"></a>Types ouverts et fermés

Tous les types peuvent être classés en tant que ***types ouverts*** ou ***types fermés***. Un type ouvert est un type qui implique des paramètres de type. Plus précisément :

*  Un paramètre de type définit un type ouvert.
*  Un type tableau est un type ouvert si et uniquement si son type d’élément est un type ouvert.
*  Un type construit est un type ouvert si et seulement si un ou plusieurs de ses arguments de type est un type ouvert. Un type imbriqué construit est un type ouvert si et seulement si un ou plusieurs de ses arguments de type ou les arguments de type de ses types conteneur sont un type ouvert.

Un type fermé est un type qui n’est pas un type ouvert.

Au moment de l’exécution, tout le code au sein d’une déclaration de type générique est exécuté dans le contexte d’un type construit fermé qui a été créé en appliquant des arguments de type à la déclaration générique. Chaque paramètre de type dans le type générique est lié à un type au moment de l’exécution particulier. Le traitement au moment de l’exécution de toutes les instructions et expressions se produit toujours avec les types fermés, et les types ouverts se produisent uniquement pendant le traitement au moment de la compilation.

Chaque type construit fermé a son propre ensemble de variables statiques, qui ne sont pas partagées avec d’autres types construits fermés. Étant donné qu’il n’existe pas de type ouvert au moment de l’exécution, aucune variable statique n’est associée à un type ouvert. Deux types construits fermés sont du même type s’ils sont construits à partir du même type générique indépendant, et que leurs arguments de type correspondants sont du même type.

### <a name="bound-and-unbound-types"></a>Types liés et indépendants

Le terme ***type indépendant*** fait référence à un type non générique ou à un type générique indépendant. Le ***type lié*** à un terme fait référence à un type non générique ou à un type construit.

Un type indépendant fait référence à l’entité déclarée par une déclaration de type. Un type générique indépendant n’est pas lui-même un type et ne peut pas être utilisé en tant que type d’une variable, d’un argument ou d’une valeur de retour, ou en tant que type de base. La seule construction dans laquelle un type générique indépendant peut être référencé est l’expression `typeof` ([opérateur typeof](expressions.md#the-typeof-operator)).

### <a name="satisfying-constraints"></a>Contraintes de satisfaction

Chaque fois qu’un type construit ou une méthode générique est référencé, les arguments de type fournis sont vérifiés par rapport aux contraintes de paramètre de type déclarées sur le type ou la méthode générique ([contraintes de paramètre de type](classes.md#type-parameter-constraints)). Pour chaque clause `where`, l’argument de type `A` qui correspond au paramètre de type nommé est vérifié par rapport à chaque contrainte comme suit :

*  Si la contrainte est un type de classe, un type d’interface ou un paramètre de type, laissez `C` représenter cette contrainte avec les arguments de type fournis, en remplaçant tous les paramètres de type qui apparaissent dans la contrainte. Pour satisfaire la contrainte, il faut que le type `A` soit convertible en type `C` par l’un des éléments suivants :
    * Conversion d’identité ([conversion d’identité](conversions.md#identity-conversion))
    * Conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions))
    * Conversion boxing ([conversions boxing](conversions.md#boxing-conversions)), à condition que le type a soit un type valeur n’acceptant pas les valeurs NULL.
    * Une référence implicite, une conversion boxing ou un paramètre de type d’un paramètre de type `A` à `C`.
*  Si la contrainte est la contrainte de type référence (`class`), le type `A` doit correspondre à l’un des éléments suivants :
    * `A` est un type d’interface, un type de classe, un type délégué ou un type tableau. Notez que `System.ValueType` et `System.Enum` sont des types référence qui satisfont cette contrainte.
    * `A` est un paramètre de type qui est connu comme étant un type référence ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).
*  Si la contrainte est la contrainte de type valeur (`struct`), le type `A` doit correspondre à l’un des éléments suivants :
    * `A` est un type struct ou un type enum, mais pas un type Nullable. Notez que `System.ValueType` et `System.Enum` sont des types référence qui ne répondent pas à cette contrainte.
    * `A` est un paramètre de type ayant la contrainte de type valeur ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).
*  Si la contrainte est la contrainte de constructeur `new()`, le `A` de type ne doit pas être `abstract` et doit avoir un constructeur sans paramètre public. Cette condition est remplie si l’une des conditions suivantes est remplie :
    * `A` est un type valeur, car tous les types valeur ont un constructeur public par défaut ([constructeurs par défaut](types.md#default-constructors)).
    * `A` est un paramètre de type ayant la contrainte de constructeur ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).
    * `A` est un paramètre de type ayant la contrainte de type valeur ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).
    * `A` est une classe qui n’est pas `abstract` et qui contient un constructeur `public` déclaré explicitement sans paramètres.
    * `A` n’est pas `abstract` et a un constructeur par défaut ([constructeurs par défaut](classes.md#default-constructors)).

Une erreur de compilation se produit si une ou plusieurs des contraintes d’un paramètre de type ne sont pas satisfaites par les arguments de type donnés.

Étant donné que les paramètres de type ne sont pas hérités, les contraintes ne sont jamais héritées non plus. Dans l’exemple ci-dessous, `D` doit spécifier la contrainte sur son paramètre de type `T` afin que `T` satisfasse à la contrainte imposée par la classe de base `B<T>`. En revanche, les `E` de classe n’ont pas besoin de spécifier de contrainte, car `List<T>` implémente `IEnumerable` pour tout `T`.

```csharp
class B<T> where T: IEnumerable {...}

class D<T>: B<T> where T: IEnumerable {...}

class E<T>: B<List<T>> {...}
```

## <a name="type-parameters"></a>Paramètres de type

Un paramètre de type est un identificateur désignant un type valeur ou un type référence auquel le paramètre est lié au moment de l’exécution.

```antlr
type_parameter
    : identifier
    ;
```

Étant donné qu’un paramètre de type peut être instancié avec un grand nombre d’arguments de type réels différents, les paramètres de type ont des opérations et des restrictions légèrement différentes de celles des autres types. Elles incluent notamment les suivantes :

*  Un paramètre de type ne peut pas être utilisé directement pour déclarer une classe de base ([classe de base](classes.md#base-class)) ou une interface (listes de[paramètres de type Variant](interfaces.md#variant-type-parameter-lists)).
*  Les règles de recherche de membre sur les paramètres de type dépendent des contraintes, le cas échéant, appliquées au paramètre de type. Ils sont détaillés dans la [recherche de membres](expressions.md#member-lookup).
*  Les conversions disponibles pour un paramètre de type dépendent des contraintes, le cas échéant, appliquées au paramètre de type. Ils sont détaillés dans les [conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters) et des [conversions dynamiques explicites](conversions.md#explicit-dynamic-conversions).
*  La `null` littérale ne peut pas être convertie en un type donné par un paramètre de type, sauf si le paramètre de type est connu comme étant un type référence ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters)). Toutefois, une expression `default` ([expressions de valeur par défaut](expressions.md#default-value-expressions)) peut être utilisée à la place. En outre, une valeur avec un type donné par un paramètre de type peut être comparée à `null` à l’aide de `==` et `!=` ([opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators)) à moins que le paramètre de type ait la contrainte de type valeur.
*  Une expression `new` ([expressions de création d’objet](expressions.md#object-creation-expressions)) peut uniquement être utilisée avec un paramètre de type si le paramètre de type est imposé par un *constructor_constraint* ou la contrainte de type valeur (contraintes de[paramètre de type](classes.md#type-parameter-constraints)).
*  Un paramètre de type ne peut pas être utilisé n’importe où dans un attribut.
*  Un paramètre de type ne peut pas être utilisé dans un accès de membre ([accès aux membres](expressions.md#member-access)) ou un nom de type (noms d’espaces de noms[et de types](basic-concepts.md#namespace-and-type-names)) pour identifier un membre statique ou un type imbriqué.
*  Dans du code unsafe, un paramètre de type ne peut pas être utilisé en tant que *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

En tant que type, les paramètres de type sont purement une construction au moment de la compilation. Au moment de l’exécution, chaque paramètre de type est lié à un type au moment de l’exécution qui a été spécifié en fournissant un argument de type à la déclaration de type générique. Ainsi, le type d’une variable déclarée avec un paramètre de type sera, au moment de l’exécution, un type construit fermé ([types ouverts et fermés](types.md#open-and-closed-types)). L’exécution de toutes les instructions et expressions impliquant des paramètres de type au moment de l’exécution utilise le type réel fourni comme argument de type pour ce paramètre.

## <a name="expression-tree-types"></a>Types d’arborescence d’expression

Les ***arborescences d’expressions*** permettent aux expressions lambda d’être représentées en tant que structures de données au lieu du code exécutable. Les arborescences d’expressions sont des valeurs des ***types d’arborescence d’expression*** de la forme `System.Linq.Expressions.Expression<D>`, où `D` est un type délégué. Pour le reste de cette spécification, nous faisons référence à ces types à l’aide de la `Expression<D>`abrégée.

S’il existe une conversion d’une expression lambda en un type délégué `D`, une conversion existe également dans le type d’arborescence de l’expression `Expression<D>`. Tandis que la conversion d’une expression lambda en un type délégué génère un délégué qui fait référence à du code exécutable pour l’expression lambda, la conversion en un type d’arborescence d’expression crée une représentation d’arborescence d’expression de l’expression lambda.

Les arborescences d’expressions sont des représentations efficaces des données en mémoire des expressions lambda et rendent la structure de l’expression lambda transparente et explicite.

Tout comme un type délégué `D`, `Expression<D>` est considéré comme ayant des types de paramètres et de retour, qui sont identiques à ceux de `D`.

L’exemple suivant représente une expression lambda à la fois comme du code exécutable et comme une arborescence de l’expression. Étant donné qu’il existe une conversion à `Func<int,int>`, une conversion existe également pour `Expression<Func<int,int>>`:

```csharp
Func<int,int> del = x => x + 1;                    // Code

Expression<Func<int,int>> exp = x => x + 1;        // Data
```

À la suite de ces assignations, le délégué `del` référence une méthode qui retourne `x + 1`, et l’arborescence d’expression `exp` référence une structure de données qui décrit l' `x => x + 1`d’expression.

La définition exacte du type générique `Expression<D>` ainsi que les règles précises pour la construction d’une arborescence d’expressions quand une expression lambda est convertie en un type d’arborescence d’expression, elles sont toutes deux en dehors de la portée de cette spécification.

Deux choses sont importantes pour rendre explicite :

*  Toutes les expressions lambda ne peuvent pas être converties en arborescences d’expressions. Par exemple, les expressions lambda avec corps d’instruction et les expressions lambda contenant des expressions d’assignation ne peuvent pas être représentées. Dans ce cas, une conversion existe toujours, mais elle échoue au moment de la compilation. Ces exceptions sont détaillées dans les [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).
*   `Expression<D>` offre une méthode d’instance `Compile` qui produit un délégué de type `D`:

    ```csharp
    Func<int,int> del2 = exp.Compile();
    ```

    L’appel de ce délégué provoque l’exécution du code représenté par l’arborescence de l’expression. Par conséquent, étant donné les définitions ci-dessus, del et DEL2 sont équivalentes, et les deux instructions suivantes ont le même effet :

    ```csharp
    int i1 = del(1);
    
    int i2 = del2(1);
    ```

    Après l’exécution de ce code, `i1` et `i2` auront tous les deux la valeur `2`.

