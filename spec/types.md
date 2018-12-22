# <a name="types"></a>Types

Les types du langage c# sont divisés en deux catégories principales : ***types valeur*** et ***référencent des types***. Types valeur et types référence peuvent être ***types génériques***, qui accepte un ou plusieurs ***paramètres de type***. Paramètres de type peuvent désigner les deux types valeur et types référence.

```antlr
type
    : value_type
    | reference_type
    | type_parameter
    | type_unsafe
    ;
```

La dernière catégorie de types, des pointeurs, est disponible uniquement dans du code unsafe. Ce sujet est abordé plus en détail dans [types pointeur](unsafe-code.md#pointer-types).

Types valeur diffèrent des types référence dans la mesure où les variables des types valeur contiennent directement leurs données, tandis que le magasin de types de variables de la référence ***références*** à leurs données, ce dernier connu en tant que ***objets***. Avec les types référence, il est possible de deux variables référencent le même objet et par conséquent, les opérations sur une variable peuvent affecter l’objet référencé par l’autre variable. Avec les types valeur, les variables disposent chacune leur propre copie des données, et il n’est pas possible pour les opérations sur une d’affecter l’autre.

Système de type de # est unifié afin qu’une valeur de n’importe quel type peut être traitée en tant qu’objet. Chaque type dans C# dérive directement ou indirectement du type `object`, et `object` est la classe de base fondamentale de tous les types. Les valeurs des types référence sont considérées comme des objets simplement en affichant les valeurs en tant que type `object`. Valeurs des types valeur sont traitées en tant qu’objets en effectuant les opérations de boxing et unboxing ([Boxing et unboxing](types.md#boxing-and-unboxing)).

## <a name="value-types"></a>Types de valeur

Un type valeur est un type struct ou un type d’énumération. C# fournit un ensemble de types struct prédéfinis appelés les ***types simples***. Les types simples sont identifiés par des mots réservés.

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

Contrairement à une variable d’un type référence, une variable d’un type valeur peut contenir la valeur `null` uniquement si le type de valeur est un type nullable.  Pour chaque type de valeur non nullable, il existe un type valeur nullable correspondant qui dénote le même ensemble de valeurs, ainsi que la valeur `null`.

Assignation à une variable d’un type valeur crée une copie de la valeur assignée. Cela diffère d’affectation à une variable de type référence, qui copie la référence mais pas l’objet identifié par la référence.

### <a name="the-systemvaluetype-type"></a>Le type System.ValueType

Tous les types valeur héritent implicitement de la classe `System.ValueType`, qui, à son tour, hérite de la classe `object`. Il n’est pas possible pour n’importe quel type de dériver à partir d’un type valeur et types valeur sont sealed donc implicitement ([des classes Sealed](classes.md#sealed-classes)).

Notez que `System.ValueType` n’est pas lui-même un *value_type*. Au lieu de cela, il est un *class_type* à partir de laquelle toutes les *value_type*s sont automatiquement dérivées.

### <a name="default-constructors"></a>Constructeurs par défaut

Tous les types valeur déclarent implicitement un constructeur d’instance sans paramètre public appelé le ***constructeur par défaut***. Le constructeur par défaut retourne une instance initialisée à zéro, appelée le ***valeur par défaut*** pour le type de valeur :

*  Pour toutes les *simple_type*s, la valeur par défaut est la valeur produite par un modèle de bit de tous les zéros non significatifs :
    * Pour `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, et `ulong`, la valeur par défaut est `0`.
    * Pour `char`, la valeur par défaut est `'\x0000'`.
    * Pour `float`, la valeur par défaut est `0.0f`.
    * Pour `double`, la valeur par défaut est `0.0d`.
    * Pour `decimal`, la valeur par défaut est `0.0m`.
    * Pour `bool`, la valeur par défaut est `false`.
*  Pour un *type_de_liste* `E`, la valeur par défaut est `0`, converti au type `E`.
*  Pour un *struct_type*, la valeur par défaut est la valeur produite en affectant tous les champs de type valeur leur valeur par défaut et référence de tous les champs de type `null`.
*  Pour un *nullable_type* la valeur par défaut est une instance pour laquelle le `HasValue` propriété a la valeur false et le `Value` propriété n’est pas définie. La valeur par défaut est également connu sous le ***valeur null*** du type nullable.

Comme tout autre constructeur d’instance, le constructeur par défaut d’un type valeur est appelé en utilisant le `new` opérateur. Pour des raisons d’efficacité, cette exigence ne vise que l’implémentation génère réellement un appel de constructeur. Dans l’exemple suivant, les variables `i` et `j` sont initialisés à zéro.

```csharp
class A
{
    void F() {
        int i = 0;
        int j = new int();
    }
}
```

Étant donné que chaque type valeur a implicitement un constructeur d’instance sans paramètre public, il n’est pas possible pour un type struct contenir une déclaration explicite d’un constructeur sans paramètre. Un type struct est toutefois autorisé à déclarer des constructeurs d’instance paramétrés ([constructeurs](structs.md#constructors)).

### <a name="struct-types"></a>Types struct

Un type struct est un type valeur qui peut déclarer des constantes, champs, méthodes, propriétés, indexeurs, opérateurs, constructeurs d’instance, les constructeurs statiques et les types imbriqués. La déclaration de types struct est décrite dans [déclarations de Struct](structs.md#struct-declarations).

### <a name="simple-types"></a>Types simples

C# fournit un ensemble de types struct prédéfinis appelés les ***types simples***. Les types simples sont identifiés par des mots réservés, mais ces mots réservés sont tout simplement des alias pour les types struct prédéfinis dans le `System` espace de noms, comme décrit dans le tableau ci-dessous.


| __Mot réservé__ | __Type d’alias__ |
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

Puisqu’un type simple alias un type struct, chaque type simple possède des membres. Par exemple, `int` possède les membres déclarés dans `System.Int32` et les membres hérités de `System.Object`, et les instructions suivantes sont autorisées :

```csharp
int i = int.MaxValue;           // System.Int32.MaxValue constant
string s = i.ToString();        // System.Int32.ToString() instance method
string t = 123.ToString();      // System.Int32.ToString() instance method
```

Les types simples diffèrent des autres types struct en ce qu’ils autorisent des opérations supplémentaires :

*  La plupart des types simples permettent de valeurs doit être créé en écrivant *littéraux* ([littéraux](lexical-structure.md#literals)). Par exemple, `123` est un littéral de type `int` et `'a'` est un littéral de type `char`. C# ne prévoit pas pour les littéraux de types struct en général, et non par défaut des valeurs d’autres types struct sont finalement toujours créées par le biais des constructeurs d’instances de ces types struct.
*  Lorsque les opérandes d’une expression sont toutes les constantes de type simple, il est possible pour le compilateur évaluer l’expression au moment de la compilation. Une telle expression est appelée un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)). Expressions impliquant des opérateurs définis par d’autres types struct ne sont pas considérés comme des expressions constantes.
*  Via `const` déclarations, il est possible de déclarer des constantes des types simples ([constantes](classes.md#constants)). Il n’est pas possible d’avoir des constantes d’autres types struct, mais un résultat similaire est fourni par `static readonly` champs.
*  Conversions de types simples peuvent participer à l’évaluation des opérateurs de conversion définie par d’autres types struct, mais un opérateur de conversion définie par l’utilisateur ne peut jamais participer d’évaluation d’un autre opérateur défini par l’utilisateur ([version d’évaluation de conversions définies par l’utilisateur](conversions.md#evaluation-of-user-defined-conversions)).

### <a name="integral-types"></a>Types intégraux

C# prend en charge neuf types intégraux : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, et `char`. Les types intégraux ont les tailles et les plages de valeurs suivants :

*  Le `sbyte` type représente 8 bits entiers signés dont les valeurs comprises entre -128 et 127.
*  Le `byte` type représente des entiers 8 bits non signés avec des valeurs comprises entre 0 et 255.
*  Le `short` type représente 16 bits entiers signés dont les valeurs comprises entre -32768 et 32767.
*  Le `ushort` type représente des entiers 16 bits non signés avec des valeurs comprises entre 0 et 65535.
*  Le `int` type représente 32 bits entiers signés dont les valeurs comprises entre -2147483648 et 2147483647.
*  Le `uint` type représente des entiers 32 bits non signés avec des valeurs comprises entre 0 et 4294967295.
*  Le `long` type représente des entiers de 64 bits avec des valeurs comprises entre -9223372036854775808 et 9223372036854775807 signés.
*  Le `ulong` type représente des entiers 64 bits non signés avec des valeurs comprises entre 0 et 18446744073709551615.
*  Le `char` type représente des entiers 16 bits non signés avec des valeurs comprises entre 0 et 65535. Le jeu de valeurs possibles pour le `char` type correspond au jeu de caractères Unicode. Bien que `char` possède la même représentation en tant que `ushort`, pas toutes les opérations autorisées sur un type sont autorisées sur l’autre.

Le type intégral opérateurs unaires et binaires fonctionnent toujours avec précision de 32 bits signée, précision de 32 bits non signée, les précision 64 bits signée ou non signée 64 bits précision :

*  Dans le cas de `+` et `~` opérateurs, l’opérande est converti en type `T`, où `T` est le premier de `int`, `uint`, `long`, et `ulong` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision du type `T`, et le type du résultat est `T`.
*  Dans le cas de `-` opérateur, l’opérande est converti en type `T`, où `T` est le premier de `int` et `long` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision du type `T`, et le type du résultat est `T`. L’unaire `-` opérateur ne peut pas être appliqué aux opérandes de type `ulong`.
*  Pour le fichier binaire `+`, `-`, `*`, `/`, `%`, `&`, `^`, `|`, `==`, `!=`, `>`, `<`, `>=`, et `<=` opérateurs, les opérandes sont convertis en type `T`, où `T` est le premier de `int`, `uint`, `long`, et `ulong` qui peut représenter entièrement toutes les possibles valeurs des deux opérandes. L’opération est ensuite effectuée à l’aide de la précision du type `T`, et le type du résultat est `T` (ou `bool` pour les opérateurs relationnels). Il n’est pas autorisée pour un opérande de type `long` et l’autre pour être de type `ulong` avec les opérateurs binaires.
*  Pour le fichier binaire `<<` et `>>` opérateurs, l’opérande gauche est converti en type `T`, où `T` est le premier de `int`, `uint`, `long`, et `ulong` qui peut représenter entièrement toutes les valeurs possibles de l’opérande. L’opération est ensuite effectuée à l’aide de la précision du type `T`, et le type du résultat est `T`.

Le `char` type est classé comme un type intégral, mais il diffère des autres types intégraux de deux manières :

*  Aucune conversion implicite des autres types vers le `char` type. En particulier, même si le `sbyte`, `byte`, et `ushort` types ont des plages de valeurs qui sont entièrement pouvant être représentées à l’aide de la `char` tapez, les conversions implicites de `sbyte`, `byte`, ou `ushort` à `char` n’existent pas.
*  Constantes de la `char` type doit être écrites en tant que *character_literal*s ou en tant que *integer_literal*s en combinaison avec un cast en type `char`. Par exemple, `(char)10` est identique à `'\x000A'`.

Le `checked` et `unchecked` instructions et opérateurs sont utilisées pour contrôler le contrôle de dépassement pour les opérations arithmétiques de type intégral et les conversions ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)). Dans un `checked` contexte, un dépassement de capacité génère une erreur de compilation ou provoque un `System.OverflowException` levée. Dans un `unchecked` contexte, dépassements de capacité sont ignorés et tous les bits de poids fort qui ne tiennent pas dans le type de destination sont ignorées.

### <a name="floating-point-types"></a>Types à virgule flottante

C# prend en charge deux types à virgule flottante : `float` et `double`. Le `float` et `double` types sont représentés en utilisant les 32 bits simple précision et 64 bits double précision IEEE 754 formats, qui fournissent des ensembles de valeurs suivants :

*  Positif zéro et zéro négatif. Dans la plupart des cas, un zéro positif et négatif zéro se comportent de façon identique que la simple valeur zéro, mais certaines opérations de faire la distinction entre les deux ([opérateur de Division](expressions.md#division-operator)).
*  L’infini positif et infini négatif. Infinis sont produites par des opérations telles que la division par zéro d’un nombre différent de zéro. Par exemple, `1.0 / 0.0` génère l’infini positif, et `-1.0 / 0.0` l’infini négatif.
*  Le ***Not-a-Number*** valeur, souvent abrégé NaN. Valeurs NaN sont produites par des opérations à virgule flottante non valides, telles que la division de zéro par zéro.
*  L’ensemble fini de valeurs non nulles de l’écran `s * m * 2^e`, où `s` est 1 ou -1, et `m` et `e` sont déterminés par le type à virgule flottante particulier : Pour `float`, `0 < m < 2^24` et `-149 <= e <= 104`et pour `double`, `0 < m < 2^53` et `1075 <= e <= 970`. Les nombres à virgule flottante dénormalisés sont considérés comme des valeurs non nulles valides.

Le `float` type peut représenter des valeurs comprises entre environ `1.5 * 10^-45` à `3.4 * 10^38` avec une précision de 7 chiffres.

Le `double` type peut représenter des valeurs comprises entre environ `5.0 * 10^-324` à `1.7 × 10^308` avec une précision de 15-16 chiffres.

Si l’un des opérandes d’un opérateur binaire est d’un type à virgule flottante, puis l’autre opérande doit être d’un type intégral ou d’un type à virgule flottante, et l’opération est évaluée comme suit :

*  Si l’un des opérandes est de type intégral, cet opérande est converti vers le type à virgule flottante de l’autre opérande.
*  Ensuite, si un des opérandes est de type `double`, l’autre opérande est converti en `double`, l’opération est effectuée à l’aide au moins `double` plage et précision et le type du résultat est `double` (ou `bool` pour le opérateurs relationnels).
*  Sinon, l’opération est effectuée à l’aide au moins `float` plage et précision et le type du résultat est `float` (ou `bool` pour les opérateurs relationnels).

Les opérateurs à virgule flottante, y compris les opérateurs d’assignation, produisent jamais d’exceptions. Au lieu de cela, dans des situations exceptionnelles, les opérations à virgule flottante produisent zéro, l’infini ou NaN, comme décrit ci-dessous :

*  Si le résultat d’une opération à virgule flottante est trop petit pour le format de destination, le résultat de l’opération devient le zéro positif ou zéro négatif.
*  Si le résultat d’une opération à virgule flottante est trop grand pour le format de destination, le résultat de l’opération devient l’infini positif ou l’infini négatif.
*  Si une opération à virgule flottante n’est pas valide, le résultat de l’opération devient une NaN.
*  Si un ou deux opérandes d’une opération à virgule flottante est NaN, le résultat de l’opération devient une NaN.

Les opérations à virgule flottante peuvent être exécutées avec une précision plus élevée que le type de résultat de l’opération. Par exemple, certaines architectures matérielles prennent en charge un type à virgule flottante « étendu » ou « long double » avec la plage supérieure et une précision à la `double` tapez, puis appliquer implicitement toutes les opérations à virgule flottante à l’aide de ce type de précision plus élevé. Qu’au prix de performances ces architectures matérielles est possible d’effectuer des opérations à virgule flottante avec moins de précision, et plutôt que d’imposer une implémentation se dérouler au détriment des performances et précision, c# autorise un type de précision plus élevé être utilisé pour toutes les opérations à virgule flottante. Autres que des résultats plus précis, cette méthode présente rarement des effets mesurables. Toutefois, dans les expressions de la forme `x * y / z`, où la multiplication produit un résultat qui est en dehors de la `double` plage, mais la division qui suit affiche le résultat temporaire dans le `double` de plage, le fait que l’expression est évaluée dans une plage plus haute format peut provoquer un résultat fini à produire au lieu d’un nombre infini.

### <a name="the-decimal-type"></a>Le type decimal

Le type `decimal` est un type de données 128 bits adapté aux calculs financiers et monétaires. Le `decimal` type peut représenter des valeurs comprises entre `1.0 * 10^-28` à environ `7.9 * 10^28` avec 28-29 chiffres significatifs.

L’ensemble fini de valeurs de type `decimal` présentent sous la forme `(-1)^s * c * 10^-e`, où le signe `s` est 0 ou 1, le coefficient `c` est donnée par `0 <= *c* < 2^96`et l’échelle `e` est telle que `0 <= e <= 28`. Le `decimal` type ne prend pas en charge les zéros signés, infinis ou NaN. Un `decimal` est représentée comme un entier 96 bits mis à l’échelle par une puissance de dix. Pour `decimal`s avec une valeur absolue inférieure à `1.0m`, la valeur est exacte jusqu'à la 28ème décimale, mais pas plus loin. Pour `decimal`s avec une valeur absolue supérieure ou égale à `1.0m`, la valeur est exacte à 28 ou 29 chiffres. Contraire au `float` et `double` des types de données, des nombres fractionnaires décimaux, comme 0,1 peuvent être représentées exactement dans le `decimal` représentation. Dans le `float` et `double` représentations, ces nombres sont souvent des fractions infinies, rendre ces représentations plus propices à arrondi erreurs.

Si l’un des opérandes d’un opérateur binaire est de type `decimal`, puis l’autre opérande doit être de type intégral ou de type `decimal`. Si un opérande de type intégral est présent, il est converti en `decimal` avant l’opération est effectuée.

Le résultat d’une opération sur les valeurs de type `decimal` est que qui résulterait de calcul d’un résultat exact (préservation mise à l’échelle, comme défini pour chaque opérateur) et ensuite arrondi pour s’ajuster à la représentation. Les résultats sont arrondis à la plus proche de la valeur représentable, et que, lorsqu’un résultat est proche de deux valeurs représentables, à la valeur qui a un nombre pair à la position de chiffre moins significatif (cela est appelé « l’arrondi bancaire »). Un zéro résultat a toujours un signe de 0 et une échelle de 0.

Si une opération arithmétique décimale produit une valeur inférieure ou égale à `5 * 10^-29` en valeur absolue, le résultat de l’opération devient égal à zéro. Si un `decimal` opération arithmétique produit un résultat qui est trop grand pour le `decimal` format, un `System.OverflowException` est levée.

Le `decimal` type a une précision supérieure, mais de plus petite plage que les types à virgule flottante. Par conséquent, les conversions de types à virgule flottante `decimal` peut générer des exceptions de dépassement de capacité et les conversions de `decimal` pour les types à virgule flottante peut entraîner une perte de précision. Pour ces raisons, n’existe aucune conversion implicite entre les types à virgule flottante et `decimal`, et sans cast explicite, il n’est pas possible de combiner à virgule flottante et `decimal` opérandes dans la même expression.

### <a name="the-bool-type"></a>Le type bool

Le `bool` type représente des quantités logiques booléennes. Les valeurs possibles de type `bool` sont `true` et `false`.

Il n’existe aucune conversion standard entre `bool` et d’autres types. En particulier, le `bool` type est distinct et indépendant des types intégraux et un `bool` valeur ne peut pas être utilisée à la place d’une valeur intégrale et vice versa.

Dans les langages C et C++, une valeur zéro intégrale ou à virgule flottante ou un pointeur null peut être converti à la valeur booléenne `false`, et une valeur intégrale ou à virgule flottante de zéro ou un pointeur non null peut être converti en la valeur booléenne `true`. En c#, ces conversions sont accomplies en comparant explicitement une valeur intégrale ou à virgule flottante à zéro ou en comparant explicitement une référence d’objet `null`.

### <a name="enumeration-types"></a>Types d'énumération

Un type d’énumération est un type distinct avec des constantes nommées. Chaque type énumération a un type sous-jacent, ce qui doit être `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` ou `ulong`. L’ensemble de valeurs du type énumération est identique à l’ensemble de valeurs du type sous-jacent. Valeurs du type énumération ne sont pas limitées aux valeurs des constantes nommées. Types énumération sont définies dans des déclarations d’énumération ([les déclarations Enum](enums.md#enum-declarations)).

### <a name="nullable-types"></a>Types Nullable

Un type nullable peut représenter toutes les valeurs de ses ***type sous-jacent*** ainsi que d’une valeur null supplémentaire. Un type nullable est écrit `T?`, où `T` est le type sous-jacent. Cette syntaxe est l’abréviation de `System.Nullable<T>`, et les deux formes peuvent être utilisés indifféremment.

Un ***type valeur non nullable*** à l’inverse est n’importe quel type de valeur autre que `System.Nullable<T>` et son raccourci `T?` (pour tout `T`), ainsi que n’importe quel paramètre de type est contraint à être un type valeur non nullable (autrement dit, tout paramètre de type avec un `struct` contrainte). Le `System.Nullable<T>` type spécifie la contrainte de type de valeur pour `T` ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), ce qui signifie que le type sous-jacent d’un type nullable peut être n’importe quel type valeur non nullable. Le type sous-jacent d’un type nullable ne peut pas être un type nullable ou un type référence. Par exemple, `int??` et `string?` sont des types non valides.

Une instance d’un type nullable `T?` a deux propriétés publiques en lecture seule :

*  Un `HasValue` propriété de type `bool`
*  Un `Value` propriété de type `T`

Instance pour laquelle `HasValue` est la valeur true est dite non null. Une instance non null contient une valeur connue et `Value` retourne cette valeur.

Instance pour laquelle `HasValue` est false est dite avoir la valeur null. Une instance null a une valeur non définie. Une tentative de lecture la `Value` d’une instance null provoque un `System.InvalidOperationException` levée. Le processus de l’accès à la `Value` propriété d’une instance nullable est appelée ***désencapsulage***.

Outre le constructeur par défaut, tous les types nullable `T?` a un constructeur public qui accepte un seul argument de type `T`. Une valeur donnée `x` de type `T`, un appel de constructeur du formulaire

```csharp
new T?(x)
```
Crée une instance non null de `T?` pour lequel le `Value` propriété est `x`. Le processus de création d’une instance non null d’un type nullable pour une valeur donnée est appelée ***encapsulant***.

Les conversions implicites sont disponibles à partir de la `null` littéral `T?` ([Null littérales conversions](conversions.md#null-literal-conversions)) et à partir de `T` à `T?` ([les conversions implicites nullables](conversions.md#implicit-nullable-conversions)).

## <a name="reference-types"></a>Types référence

Un type référence est un type de classe, un type d’interface, un type tableau ou un type délégué.

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

Une valeur de type référence est une référence à un ***instance*** du type, connues sous le nom de ce dernier un ***objet***. La valeur spéciale `null` est compatible avec tous les types référence et indique l’absence d’une instance.

### <a name="class-types"></a>Types de classes

Un type de classe définit une structure de données qui contient les données membres (constantes et champs), des fonctions membres (méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et les types imbriqués. Types de classes prend en charge l’héritage, un mécanisme par lequel les classes dérivées peuvent étendre et spécialiser les classes de base. Instances de types de classe sont créées à l’aide de *object_creation_expression*s ([des expressions de la création d’objet](expressions.md#object-creation-expressions)).

Types de classe sont décrits dans [Classes](classes.md).

Certains types de classe prédéfinis ont une signification spéciale dans le langage c#, comme décrit dans le tableau ci-dessous.


| __Type de classe__     | __Description__                                         |
|--------------------|---------------------------------------------------------|
| `System.Object`    | La classe de base fondamentale de tous les autres types. Consultez [le type d’objet](types.md#the-object-type). | 
| `System.String`    | Le type de chaîne du langage c#. Consultez [le type de chaîne](types.md#the-string-type).         |
| `System.ValueType` | La classe de base de tous les types de valeur. Consultez [System.ValueType le type](types.md#the-systemvaluetype-type).          |
| `System.Enum`      | La classe de base de tous les types enum. Consultez [Enums](enums.md).              |
| `System.Array`     | La classe de base de tous les types tableau. Consultez [Tableaux](arrays.md).             |
| `System.Delegate`  | La classe de base de tous les types délégués. Consultez [délégués](delegates.md).          |
| `System.Exception` | La classe de base de tous les types d’exception. Consultez [Exceptions](exceptions.md).         |

### <a name="the-object-type"></a>Le type d’objet

Le `object` type de classe est la classe de base fondamentale de tous les autres types. Chaque type dans c# dérive directement ou indirectement le `object` type de classe.

Le mot clé `object` est simplement un alias de la classe prédéfinie `System.Object`.

### <a name="the-dynamic-type"></a>Le type dynamique

Le `dynamic` Entrez comme `object`, peut faire référence à n’importe quel objet. Lorsque les opérateurs sont appliqués à des expressions de type `dynamic`, leur résolution est différée jusqu'à ce que le programme est exécuté. Par conséquent, si l’opérateur légalement ne peut pas être appliqué qu’à l’objet référencé, aucune erreur n’est donné pendant la compilation. À la place une exception est levée en cas d’échec de résolution de l’opérateur au moment de l’exécution.

Son but est de permettre la liaison dynamique, qui est décrite en détail dans [liaison dynamique](expressions.md#dynamic-binding).

`dynamic` est considéré comme identique au `object` sauf dans les points suivants :

*  Opérations sur les expressions de type `dynamic` peut être liée de manière dynamique ([liaison dynamique](expressions.md#dynamic-binding)).
*  Inférence de type ([l’inférence de Type](expressions.md#type-inference)) préféreront `dynamic` sur `object` si les deux sont des candidats.

En raison de l’équivalence, les blocages suivantes :

*  Il existe une conversion implicite d’identité entre `object` et `dynamic`et entre les types construits sont les mêmes lors du remplacement `dynamic` avec `object`
*  Les conversions implicites et explicites vers et depuis `object` s’appliquent également vers et depuis `dynamic`.
*  Les signatures de méthode sont les mêmes lors du remplacement `dynamic` avec `object` sont considérés comme la même signature
*  Le type `dynamic` est indiscernable `object` au moment de l’exécution.
*  Une expression de type `dynamic` est appelé un ***expression dynamique***.

### <a name="the-string-type"></a>Le type de chaîne

Le `string` type est un type classe sealed qui hérite directement de `object`. Instances de la `string` classe représentent les chaînes de caractères Unicode.

Valeurs de la `string` type peut être écrit en tant que littéraux de chaîne ([littéraux de chaîne](lexical-structure.md#string-literals)).

Le mot clé `string` est simplement un alias de la classe prédéfinie `System.String`.

### <a name="interface-types"></a>Types interface

Une interface définit un contrat. Une classe ou un struct qui implémente une interface doit adhérer à ce contrat. Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.

Types d’interface sont décrits dans [Interfaces](interfaces.md).

### <a name="array-types"></a>Types de tableaux

Un tableau est une structure de données qui contient zéro ou plusieurs variables qui sont accessibles par le biais des indices calculés. Les variables contenues dans un tableau, également appelé éléments du tableau, sont tous du même type, et ce type est appelé le type d’élément du tableau.

Les types tableau sont décrits dans [tableaux](arrays.md).

### <a name="delegate-types"></a>Types délégués

Un délégué est une structure de données qui fait référence à une ou plusieurs méthodes. Par exemple méthodes, il fait également référence à leurs instances d’objet correspondant.

L’équivalent le plus proche d’un délégué en C ou C++ est un pointeur de fonction, mais tandis que les fonctions statiques ne peut référencer qu’un pointeur de fonction, un délégué peut faire référence à la fois static et méthodes d’instance. Dans ce cas, le délégué stocke non seulement une référence au point d’entrée de la méthode, mais également une référence à l’instance d’objet sur lequel appeler la méthode.

Types délégués sont décrits dans [délégués](delegates.md).

## <a name="boxing-and-unboxing"></a>Boxing et unboxing

Le concept de boxing et unboxing est central au système de type de #. Il fournit un pont entre *value_type*s et *reference_type*s en permettant de n’importe quelle valeur d’un *value_type* à convertir vers et à partir de type `object`. Boxing et unboxing permettent une vue unifiée du système de type dans lequel une valeur de n’importe quel type peut finalement être traitée en tant qu’objet.

### <a name="boxing-conversions"></a>Conversions boxing

Une conversion boxing permet un *value_type* être implicitement converti dans un *reference_type*. Les conversions boxing suivantes existent :

*  À partir d’un *value_type* au type `object`.
*  À partir d’un *value_type* au type `System.ValueType`.
*  À partir d’un *non_nullable_value_type* aux *interface_type* implémentée par le *value_type*.
*  À partir d’un *nullable_type* aux *interface_type* implémentée par le type sous-jacent de la *nullable_type*.
*  À partir d’un *type_de_liste* au type `System.Enum`.
*  À partir d’un *nullable_type* avec sous-jacent *type_de_liste* au type `System.Enum`.
*  Notez qu’une conversion implicite d’un paramètre de type sera exécutée en tant qu’une conversion boxing si au moment de l’exécution il finit par conversion à partir d’un type valeur à un type référence ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters)).

Conversion boxing d’une valeur un *non_nullable_value_type* se compose d’allouer une instance d’objet et à copier le *non_nullable_value_type* valeur dans cette instance.

Conversion boxing d’une valeur un *nullable_type* génère une référence null s’il s’agit du `null` valeur (`HasValue` est `false`), ou le résultat de dévoilement et boxing de la valeur sous-jacente dans le cas contraire.

Le processus de conversion boxing d’une valeur un *non_nullable_value_type* explique parfaitement d’imaginer l’existence d’un générique ***classe de boxing***, qui se comporte comme s’il était déclaré comme suit :

```csharp
sealed class Box<T>: System.ValueType
{
    T value;

    public Box(T t) {
        value = t;
    }
}
```

Le boxing d’une valeur `v` de type `T` maintenant se compose de l’exécution de l’expression `new Box<T>(v)`et en retournant l’instance obtenue en tant que valeur de type `object`. Par conséquent, les instructions
```csharp
int i = 123;
object box = i;
```
sur le plan conceptuel correspondent aux
```csharp
int i = 123;
object box = new Box<int>(i);
```

Une classe de boxing comme `Box<T>` ci-dessus n’en existe pas et le type dynamique d’une valeur boxed n’est pas réellement un type de classe. Au lieu de cela, une valeur boxed du type `T` a le type dynamique `T`et une validation de type dynamique à l’aide de la `is` opérateur peut référencer tout simplement de type `T`. Par exemple :
```csharp
int i = 123;
object box = i;
if (box is int) {
    Console.Write("Box contains an int");
}
```
génère la chaîne «`Box contains an int`» sur la console.

Une conversion boxing implique effectuant une copie de la valeur convertie. Cela est différent de la conversion d’un *reference_type* à taper `object`, dans laquelle la valeur continue à référencer la même instance et simplement est considérée comme le type moins dérivé `object`. Par exemple, prenons la déclaration
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
génère la valeur 10 sur la console, car l’opération de conversion boxing implicite se produit dans l’attribution de `p` à `box` provoque la valeur de `p` doit être copié. Avait `Point` été déclaré un `class` au lieu de cela, la valeur 20 serait sortie car `p` et `box` serait référencent la même instance.

### <a name="unboxing-conversions"></a>Conversions unboxing

Une conversion unboxing permet une *reference_type* pour être converti explicitement en un *value_type*. Les conversions unboxing suivantes existent :

*  À partir du type `object` aux *value_type*.
*  À partir du type `System.ValueType` aux *value_type*.
*  À partir d’un *interface_type* aux *non_nullable_value_type* qui implémente le *interface_type*.
*  À partir d’un *interface_type* aux *nullable_type* dont le type implémente le *interface_type*.
*  À partir du type `System.Enum` aux *type_de_liste*.
*  À partir du type `System.Enum` aux *nullable_type* avec sous-jacent *type_de_liste*.
*  Notez qu’une conversion explicite à un paramètre de type sera exécutée en tant qu’une conversion unboxing si au moment de l’exécution il finit par conversion à partir d’un type référence à un type valeur ([les conversions explicites dynamiques](conversions.md#explicit-dynamic-conversions)).

Une opération d’unboxing pour un *non_nullable_value_type* consistant à vérifier au préalable que l’instance d’objet est une valeur boxed de la donnée *non_nullable_value_type*, puis copiez la valeur de la instance.

Unboxing à un *nullable_type* produit la valeur null de la *nullable_type* si l’opérande source est `null`, ou le résultat inclus dans un wrapper d’unboxing de l’instance d’objet pour le type sous-jacent de la *nullable_type* dans le cas contraire.

Qui fait référence à la classe de boxing imaginaire décrite dans la section précédente, une conversion unboxing d’un objet `box` à un *value_type* `T` se compose de l’exécution de l’expression `((Box<T>)box).value`. Par conséquent, les instructions
```csharp
object box = 123;
int i = (int)box;
```
sur le plan conceptuel correspondent aux
```csharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

Pour une conversion unboxing à une donnée *non_nullable_value_type* pour réussir au moment de l’exécution, la valeur de l’opérande source doit être une référence à une valeur boxed de qui *non_nullable_value_type*. Si l’opérande source est `null`, un `System.NullReferenceException` est levée. Si l’opérande source est une référence à un objet incompatible, un `System.InvalidCastException` est levée.

Pour une conversion unboxing à une donnée *nullable_type* pour réussir au moment de l’exécution, la valeur de l’opérande source doit être `null` ou une référence à une valeur boxed de sous-jacent *non_nullable_value_type* de la *nullable_type*. Si l’opérande source est une référence à un objet incompatible, un `System.InvalidCastException` est levée.

## <a name="constructed-types"></a>Types construits

Une déclaration de type générique, en soi, désigne un ***indépendants de type générique*** qui est utilisé comme un « plan » pour former des différents types, par le biais d’application ***arguments de type***. Les arguments de type sont écrits dans les crochets pointus (`<` et `>`) immédiatement après le nom du type générique. Un type qui inclut au moins un argument de type est appelé un ***type construit***. Un type construit peut être utilisé dans la plupart des endroits du langage dans lequel un nom de type peut apparaître. Un type générique indépendant peut uniquement être utilisé dans un *typeof_expression* ([l’opérateur typeof](expressions.md#the-typeof-operator)).

Types construits peuvent également servir dans les expressions des noms simples ([noms simples](expressions.md#simple-names)) ou lors de l’accès à un membre ([l’accès au membre](expressions.md#member-access)).

Quand un *namespace_or_type_name* est évaluées, seuls les types génériques avec le nombre correct de paramètres sont considérés comme de type. Par conséquent, il est possible d’utiliser le même identificateur pour identifier les différents types, tant que les types ont des nombres différents de paramètres de type. Cela est utile lorsque vous combinez des classes génériques et non génériques dans le même programme :

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

Un *type_name* peut identifier un type construit même si elle ne spécifie pas directement les paramètres de type. Cela peut se produire où un type est imbriqué dans une déclaration de classe générique, et le type d’instance de la déclaration du conteneur est implicitement utilisé pour la recherche de nom ([les types dans les classes génériques imbriqués](classes.md#nested-types-in-generic-classes)) :

```csharp
class Outer<T>
{
    public class Inner {...}

    public Inner i;                // Type of i is Outer<T>.Inner
}
```

Dans du code unsafe, un type construit ne peut pas être utilisé comme un *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

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

Dans du code unsafe ([code Unsafe](unsafe-code.md)), un *type_argument* ne peut pas être un type pointeur. Chaque argument de type doit satisfaire toutes les contraintes sur le correspondant le paramètre de type ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).

### <a name="open-and-closed-types"></a>Types ouverts et fermés

Tous les types peuvent être classés comme étant ***types ouverts*** ou ***fermé types***. Un type ouvert est un type qui implique des paramètres de type. Plus précisément :

*  Un paramètre de type définit un type ouvert.
*  Un type tableau est un type ouvert si et seulement si son type d’élément est un type ouvert.
*  Un type construit est un type ouvert si et seulement si un ou plusieurs de ses arguments de type sont un type ouvert. Un type imbriqué construit est un type ouvert si et seulement si un ou plusieurs de ses arguments de type ou de son ou ses types contenant les arguments de type sont un type ouvert.

Un type fermé est un type qui n’est pas un type ouvert.

Au moment de l’exécution, tout le code dans une déclaration de type générique est exécutée dans le contexte d’un type construit fermé qui a été créé en appliquant des arguments de type à la déclaration générique. Chaque paramètre de type dans le type générique est lié à un type particulier d’exécution. L’exécution du traitement de toutes les instructions et expressions toujours se produit avec les types fermés, et les types ouverts se produisent uniquement pendant la compilation traitement.

Chaque type construit fermé possède son propre ensemble de variables statiques qui ne sont pas partagés avec d’autres types construits fermés. Dans la mesure où un type ouvert n’existe pas au moment de l’exécution, il n’existe aucune variable statique associée à un type ouvert. Deux types construits fermés sont du même type si elles sont construites à partir du même type générique indépendant et leurs arguments de type correspondant sont du même type.

### <a name="bound-and-unbound-types"></a>Types dépendants et indépendants

Le terme ***indépendant du type*** fait référence à un type non générique ou un type générique indépendant. Le terme ***lié de type*** fait référence à un type non générique ou un type construit.

Un type indépendant fait référence à l’entité déclarée par une déclaration de type. Un type générique indépendant n’est pas lui-même un type et ne peut pas être utilisé comme type d’une variable, l’argument ou la valeur de retour ou comme type de base. La seule construction dans lequel un type générique indépendant peut être référencé est la `typeof` expression ([l’opérateur typeof](expressions.md#the-typeof-operator)).

### <a name="satisfying-constraints"></a>Contraintes satisfaisantes

Chaque fois qu’un type construit ou une méthode générique est référencé, les arguments de type fourni sont vérifiées sur les contraintes de paramètre de type déclarés sur le type générique ou la méthode ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)). Pour chaque `where` clause, l’argument de type `A` qui correspond au paramètre de type est vérifié par rapport à chaque contrainte comme suit :

*  Si la contrainte est un type de classe, un type d’interface ou un paramètre de type, laissez `C` représentent que contrainte avec les arguments de type fourni est remplacé pour tous les paramètres de type qui s’affichent dans la contrainte. Pour satisfaire la contrainte, il doit être le cas tapez `A` est convertible en type `C` par une des opérations suivantes :
    * Une conversion d’identité ([conversion d’identité](conversions.md#identity-conversion))
    * Une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions))
    * Une conversion boxing ([conversions Boxing](conversions.md#boxing-conversions)), à condition que le type A est un type valeur non nullable.
    * Une conversion du paramètre de référence, boxing ou type implicite à partir d’un paramètre de type `A` à `C`.
*  Si la contrainte est la contrainte de type référence (`class`), le type `A` doit satisfaire une des opérations suivantes :
    * `A` est un type d’interface, le type de classe, le type délégué ou le type de tableau. Notez que `System.ValueType` et `System.Enum` sont des types référence qui correspondent à la contrainte.
    * `A` est un paramètre de type est connu pour être un type référence ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).
*  Si la contrainte est la contrainte de type valeur (`struct`), le type `A` doit satisfaire une des opérations suivantes :
    * `A` est un type struct ou type enum, mais pas un type nullable. Notez que `System.ValueType` et `System.Enum` sont des types référence qui ne répondent pas à cette contrainte.
    * `A` est un paramètre de type ayant la contrainte de type valeur ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).
*  Si la contrainte est la contrainte de constructeur `new()`, le type `A` ne doit pas être `abstract` et doit avoir un constructeur sans paramètre public. Cela est satisfaite si une des opérations suivantes est vraie :
    * `A` est un type valeur, étant donné que tous les types de valeur ont un constructeur public par défaut ([constructeurs par défaut](types.md#default-constructors)).
    * `A` est un paramètre de type ayant la contrainte de constructeur ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).
    * `A` est un paramètre de type ayant la contrainte de type valeur ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).
    * `A` est une classe qui n’est pas `abstract` et contient déclaré explicitement `public` constructeur sans paramètres.
    * `A` n’est pas `abstract` et possède un constructeur par défaut ([constructeurs par défaut](classes.md#default-constructors)).

Une erreur de compilation se produit si un ou plusieurs des contraintes d’un paramètre type ne sont pas satisfaites par les arguments de type donné.

Dans la mesure où les paramètres de type ne sont pas héritées, les contraintes ne sont jamais soit héritée. Dans l’exemple ci-dessous, `D` doit spécifier la contrainte sur son paramètre de type `T` afin que `T` satisfait à la contrainte imposée par la classe de base `B<T>`. En revanche, la classe `E` pas besoin de spécifier une contrainte, car `List<T>` implémente `IEnumerable` pour toute `T`.

```csharp
class B<T> where T: IEnumerable {...}

class D<T>: B<T> where T: IEnumerable {...}

class E<T>: B<List<T>> {...}
```

## <a name="type-parameters"></a>Paramètres de type

Un paramètre de type est un identificateur qui désigne un type valeur ou un type référence auquel le paramètre est lié au moment de l’exécution.

```antlr
type_parameter
    : identifier
    ;
```

Dans la mesure où un paramètre de type peut être instancié avec de nombreux arguments de type réel différents paramètres de type ont légèrement différentes opérations et les restrictions que les autres types. Elles incluent notamment :

*  Un paramètre de type ne peut pas être utilisé directement pour déclarer une classe de base ([classe de Base](classes.md#base-class)) ou une interface ([listes de paramètres de type Variant](interfaces.md#variant-type-parameter-lists)).
*  Les règles pour la recherche de membre sur le type de paramètres varient selon les contraintes, le cas échéant, appliqué au paramètre de type. Elles sont détaillées dans [recherche de membres](expressions.md#member-lookup).
*  Les conversions disponibles pour un paramètre de type varient selon les contraintes, cas échéant, appliqué au paramètre de type. Elles sont détaillées dans [conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters) et [les conversions explicites dynamiques](conversions.md#explicit-dynamic-conversions).
*  Le littéral `null` ne peut pas être converti en un type donné par un paramètre de type, sauf si le paramètre de type est connu pour être un type référence ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters)). Toutefois, un `default` expression ([des expressions de valeur par défaut](expressions.md#default-value-expressions)) peut être utilisé à la place. En outre, une valeur avec un type donné par un paramètre de type peut être comparée avec `null` à l’aide de `==` et `!=` ([opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators)), sauf si le paramètre de type a la contrainte de type valeur.
*  Un `new` expression ([des expressions de la création d’objet](expressions.md#object-creation-expressions)) peut uniquement être utilisé avec un paramètre de type, si le paramètre de type est contraint par une *constructor_constraint* ou la valeur de contrainte (detype[ Les contraintes de paramètre de type](classes.md#type-parameter-constraints)).
*  Un paramètre de type ne peut pas être utilisé n’importe où dans un attribut.
*  Un paramètre de type ne peut pas être utilisé dans un accès au membre ([l’accès au membre](expressions.md#member-access)) ou nom de type ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) pour identifier un membre statique ou un type imbriqué.
*  Dans du code unsafe, un paramètre de type ne peut pas être utilisé comme un *unmanaged_type* ([types pointeur](unsafe-code.md#pointer-types)).

En tant que type, les paramètres de type sont purement une construction de la compilation. Au moment de l’exécution, chaque paramètre de type est lié à un type au moment de l’exécution qui a été spécifié en fournissant un argument de type à la déclaration de type générique. Par conséquent, le type d’une variable déclarée avec un paramètre ne sera type, au moment de l’exécution, être un type construit fermé ([ouvert et fermé types](types.md#open-and-closed-types)). L’exécution de l’exécution de toutes les instructions et expressions impliquant des paramètres de type utilise le type réel qui a été fourni comme argument de type pour ce paramètre.

## <a name="expression-tree-types"></a>Types arborescence d’expression

***Arborescences d’expression*** permettant aux expressions lambda pour être représenté en tant que structures de données au lieu de code exécutable. Arborescences d’expression sont des valeurs de ***types arborescence d’expression*** du formulaire `System.Linq.Expressions.Expression<D>`, où `D` est n’importe quel type délégué. Pour le reste de cette spécification, nous ferons référence à ces types à l’aide de la syntaxe raccourcie `Expression<D>`.

S’il existe une conversion à partir d’une expression lambda à un type délégué `D`, il existe également une conversion vers le type d’arborescence expression `Expression<D>`. Tandis que la conversion d’une expression lambda à un type délégué génère un délégué qui fait référence au code exécutable de l’expression lambda, la conversion vers un type d’arborescence expression crée une arborescence d’expression de l’expression lambda.

Arborescences d’expression sont des représentations de données en mémoire efficace des expressions lambda et que la structure de l’expression lambda transparente et explicite.

Tout comme un type délégué `D`, `Expression<D>` est considéré comme paramètre et types de retour, qui sont les mêmes que celles de `D`.

L’exemple suivant représente une expression lambda en tant que code exécutable et comme une arborescence d’expression. Car il existe une conversion en `Func<int,int>`, il existe également une conversion en `Expression<Func<int,int>>`:

```csharp
Func<int,int> del = x => x + 1;                    // Code

Expression<Func<int,int>> exp = x => x + 1;        // Data
```

Suivez ces affectations, le délégué `del` fait référence à une méthode qui retourne `x + 1`et l’arborescence d’expression `exp` fait référence à une structure de données qui décrit l’expression `x => x + 1`.

La définition exacte du type générique `Expression<D>` , ainsi que les règles précises pour construire une arborescence d’expression lorsqu’une expression lambda est convertie en un type arborescence d’expression, sont tous deux en dehors de l’étendue de cette spécification.

Deux choses sont importantes pour rendre explicite :

*  Pas toutes les expressions lambda peuvent être converties en arborescences d’expression. Par exemple, les expressions lambda avec corps d’instruction et les expressions lambda contenant des expressions d’assignation ne peut pas être représenté. Dans ce cas, une conversion toujours existe mais échouera au moment de la compilation. Ces exceptions sont détaillées dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).
*   `Expression<D>` offre une méthode d’instance `Compile` qui produit un délégué du type `D`:

    ```csharp
    Func<int,int> del2 = exp.Compile();
    ```

    L’appel de ce délégué entraîne le code représenté par l’arborescence d’expression à exécuter. Par conséquent, compte tenu des définitions ci-dessus, del et del2 sont équivalents et les deux instructions suivantes auront le même effet :

    ```csharp
    int i1 = del(1);
    
    int i2 = del2(1);
    ```

    Après l’exécution de ce code, `i1` et `i2` se verront attribuer la valeur `2`.

