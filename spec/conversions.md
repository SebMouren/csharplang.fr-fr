---
ms.openlocfilehash: 4d6d28a3127bc701867afe157aa5496377a06f69
ms.sourcegitcommit: 63d276488c9770a565fd787020783ffc1d2af9d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868002"
---
# <a name="conversions"></a>Conversions

Une ***conversion*** permet à une expression d’être traitée comme étant d’un type particulier. Une conversion peut entraîner le traitement d’une expression d’un type donné comme ayant un type différent, ou elle peut provoquer l’utilisation d’une expression sans type pour obtenir un type. Les conversions peuvent être ***implicites*** ou ***explicites***, et cela détermine si un cast explicite est requis. Par exemple, la conversion du type `int` en type `long` est implicite, de sorte que les expressions de type `int` peuvent implicitement être traitées comme des `long`de type. La conversion inverse, du type `long` en type `int`, est explicite et un cast explicite est donc nécessaire.

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

Certaines conversions sont définies par le langage. Les programmes peuvent également définir leurs propres conversions ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).

## <a name="implicit-conversions"></a>Conversions implicites

Les conversions suivantes sont classées comme conversions implicites :

*  Conversions d’identité
*  Conversions numériques implicites
*  Conversions d’énumération implicites
*  Conversions de chaînes interpolées implicites
*  Conversions Nullable implicites
*  Conversions de littéral null
*  Conversions de références implicites
*  Conversions boxing
*  Conversions dynamiques implicites
*  Conversions d’expressions constantes implicites
*  Conversions implicites définies par l’utilisateur
*  Conversions de fonctions anonymes
*  Conversions de groupe de méthodes

Les conversions implicites peuvent se produire dans diverses situations, y compris les appels de membres[de fonctions (vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions de cast ([expressions de cast](expressions.md#cast-expressions)) et les assignations ([opérateurs d’assignation](expressions.md#assignment-operators)).

Les conversions implicites prédéfinies aboutissent toujours et n’entraînent jamais la levée d’exceptions. Les conversions implicites définies par l’utilisateur doivent également présenter ces caractéristiques.

Dans le cadre de la conversion, les types `object` et `dynamic` sont considérés comme équivalents.

Toutefois, les conversions dynamiques (conversions[dynamiques implicites](conversions.md#implicit-dynamic-conversions) et [conversions dynamiques explicites](conversions.md#explicit-dynamic-conversions)) s’appliquent uniquement aux expressions de type `dynamic` ([type dynamique](types.md#the-dynamic-type)).

### <a name="identity-conversion"></a>Conversion d’identité

Une conversion d’identité est convertie de n’importe quel type vers le même type. Cette conversion existe de telle sorte qu’une entité qui a déjà un type requis peut être considérée comme convertible en ce type.

*  Étant donné que les `object` et les `dynamic` sont considérés comme équivalents, il existe une conversion d’identité entre `object` et `dynamic`, et entre les types construits qui sont identiques lors du remplacement de toutes les occurrences de `dynamic` par `object`.

### <a name="implicit-numeric-conversions"></a>Conversions numériques implicites

Les conversions numériques implicites sont les suivantes :

*  De `sbyte` à `short`, `int`, `long`, `float`, `double`ou `decimal`.
*  De `byte` à `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`ou `decimal`.
*  De `short` à `int`, `long`, `float`, `double`ou `decimal`.
*  De `ushort` à `int`, `uint`, `long`, `ulong`, `float`, `double`ou `decimal`.
*  De `int` à `long`, `float`, `double`ou `decimal`.
*  De `uint` à `long`, `ulong`, `float`, `double`ou `decimal`.
*  De `long` à `float`, `double`ou `decimal`.
*  De `ulong` à `float`, `double`ou `decimal`.
*  De `char` à `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`ou `decimal`.
*  De `float` à `double`

Les conversions de `int`, `uint`, `long`ou `ulong` en `float` et de `long` ou `ulong` à `double` peuvent entraîner une perte de précision, mais n’entraînent jamais de perte de magnitude. Les autres conversions numériques implicites ne perdent jamais d’informations.

Il n’existe pas de conversion implicite vers le type `char`, donc les valeurs des autres types intégraux ne sont pas automatiquement converties en type `char`.

### <a name="implicit-enumeration-conversions"></a>Conversions d’énumération implicites

Une conversion d’énumération implicite permet à l' *decimal_integer_literal* `0` être convertie en n’importe quel *enum_type* et en n’importe quel *nullable_type* dont le type sous-jacent est un *enum_type*. Dans ce dernier cas, la conversion est évaluée en convertissant les *enum_type* sous-jacentes et en encapsulant le résultat ([types Nullable](types.md#nullable-types)).

### <a name="implicit-interpolated-string-conversions"></a>Conversions de chaînes interpolées implicites

Une conversion de chaîne interpolée implicite permet à une *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)) d’être converties en `System.IFormattable` ou `System.FormattableString` (qui implémente `System.IFormattable`).

Lorsque cette conversion est appliquée, une valeur de chaîne n’est pas composée de la chaîne interpolée. Au lieu de cela, une instance de `System.FormattableString` est créée, comme décrit plus loin dans [chaînes interpolées](expressions.md#interpolated-strings).

### <a name="implicit-nullable-conversions"></a>Conversions Nullable implicites

Les conversions implicites prédéfinies qui fonctionnent sur des types valeur non Nullable peuvent également être utilisées avec des formes Nullable de ces types. Pour chaque identité implicite prédéfinie et conversion numérique qui convertissent un type valeur n’acceptant pas les valeurs NULL `S` en un type valeur non Nullable `T`, les conversions Nullable implicites suivantes existent :

*  Conversion implicite de `S?` en `T?`.
*  Conversion implicite de `S` en `T?`.

L’évaluation d’une conversion Nullable implicite basée sur une conversion sous-jacente de `S` à `T` se déroule comme suit :

*  Si la conversion Nullable est comprise entre `S?` et `T?`:
    * Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est la valeur null de type `T?`.
    * Dans le cas contraire, la conversion est évaluée en tant que désencapsulation de `S?` à `S`, suivi de la conversion sous-jacente de `S` en `T`, suivie d’un encapsulation ([types Nullable](types.md#nullable-types)) de `T` à `T?`.

*  Si la conversion Nullable est comprise entre `S` et `T?`, la conversion est évaluée comme la conversion sous-jacente de `S` en `T` suivie d’une encapsulation de `T` à `T?`.

### <a name="null-literal-conversions"></a>Conversions de littéral null

Une conversion implicite existe à partir du littéral `null` en n’importe quel type Nullable. Cette conversion produit la valeur null ([types Nullable](types.md#nullable-types)) du type Nullable donné.

### <a name="implicit-reference-conversions"></a>Conversions de références implicites

Les conversions de référence implicites sont les suivantes :

*  Depuis n’importe quel *reference_type* vers `object` et `dynamic`.
*  À partir de tout *class_type* `S` à n’importe quel `T`*class_type* , fourni `S` est dérivé de `T`.
*  À partir de tout *class_type* `S` à n’importe quel `T`*interface_type* , fourni `S` implémente `T`.
*  À partir de tout *interface_type* `S` à n’importe quel `T`*interface_type* , fourni `S` est dérivé de `T`.
*  D’un *array_type* `S` avec un type d’élément `SE` à un *array_type* `T` avec un type d’élément `TE`, à condition que les conditions suivantes soient remplies :
    * `S` et `T` diffèrent uniquement par le type d’élément. En d’autres termes, `S` et `T` ont le même nombre de dimensions.
    * Les `SE` et les `TE` sont *reference_type*s.
    * Une conversion de référence implicite existe de `SE` à `TE`.
*  De tout *array_type* vers `System.Array` et les interfaces qu’il implémente.
*  À partir d’un type tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et à ses interfaces de base, à condition qu’il existe une conversion implicite d’identité ou de référence de `S` en `T`.
*  De tout *delegate_type* vers `System.Delegate` et les interfaces qu’il implémente.
*  Du littéral null à n’importe quel *reference_type*.
*  De tout *reference_type* à un *reference_type* `T` s’il possède une conversion d’identité ou de référence implicite en une *reference_type* `T0` et `T0` a une conversion d’identité en `T`.
*  De tout *reference_type* à un type d’interface ou de délégué `T` s’il a une conversion d’identité ou de référence implicite en une interface ou un type délégué `T0` et `T0` est convertible en variance ([conversion d’écart](interfaces.md#variance-conversion)) en `T`.
*  Conversions implicites impliquant des paramètres de type connus comme des types référence. Pour plus d’informations sur les conversions implicites impliquant des paramètres de type, consultez [conversions implicites impliquant des paramètres](conversions.md#implicit-conversions-involving-type-parameters) de type.

Les conversions de référence implicites sont les conversions entre *reference_type*s qui peuvent être prouvées de toujours s’exécuter correctement, et ne nécessitent donc pas de vérification au moment de l’exécution.

Les conversions de référence, implicites ou explicites, ne modifient jamais l’identité référentielle de l’objet en cours de conversion. En d’autres termes, si une conversion de référence peut modifier le type de la référence, elle ne modifie jamais le type ou la valeur de l’objet auquel il est fait référence.

### <a name="boxing-conversions"></a>Conversions boxing

Une conversion boxing permet à un *Value_type* d’être implicitement converti en un type référence. Une conversion boxing existe de tout *non_nullable_value_type* à `object` et `dynamic`, à `System.ValueType` et à tout *INTERFACE_TYPE* implémenté par le *non_nullable_value_type*. En outre, une *enum_type* peut être convertie en type `System.Enum`.

Une conversion boxing existe d’un *nullable_type* à un type référence, si et seulement s’il existe une conversion boxing du *non_nullable_value_type* sous-jacent au type référence.

Un type valeur a une conversion boxing en type interface `I` s’il a une conversion boxing en type interface `I0` et `I0` a une conversion d’identité en `I`.

Un type valeur a une conversion boxing en type interface `I` s’il a une conversion boxing en une interface ou un type délégué `I0` et `I0` est convertible en variance ([conversion d’écart](interfaces.md#variance-conversion)) en `I`.

Le boxing d’une valeur d’une *non_nullable_value_type* consiste à allouer une instance d’objet et à copier la valeur *Value_type* dans cette instance. Une structure peut être convertie dans le type `System.ValueType`, car il s’agit d’une classe de base pour tous les structs ([héritage](structs.md#inheritance)).

Le boxing d’une valeur d’un *nullable_type* se déroule comme suit :

*  Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est une référence null du type cible.
*  Dans le cas contraire, le résultat est une référence à un boxed `T` produit en désencapsulant et en Boxing la valeur source.

Les conversions boxing sont décrites plus en détail dans [conversions boxing](types.md#boxing-conversions).

### <a name="implicit-dynamic-conversions"></a>Conversions dynamiques implicites

Une conversion dynamique implicite existe à partir d’une expression de type `dynamic` à n’importe quel type `T`. La conversion est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), ce qui signifie qu’une conversion implicite est recherchée au moment de l’exécution à partir du type au moment de l’exécution de l’expression à `T`. Si aucune conversion n’est trouvée, une exception Runtime est levée.

Notez que cette conversion implicite n’enfreint apparemment pas les conseils au début des [conversions implicites](conversions.md#implicit-conversions) qu’une conversion implicite ne doit jamais provoquer une exception. Toutefois, il ne s’agit pas de la conversion elle-même, mais de la *détection* de la conversion à l’origine de l’exception. Le risque d’exceptions au moment de l’exécution est inhérent à l’utilisation de la liaison dynamique. Si la liaison dynamique de la conversion n’est pas souhaitée, l’expression peut être d’abord convertie en `object`, puis vers le type souhaité.

L’exemple suivant illustre des conversions dynamiques implicites :

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

Les assignations à `s2` et `i` utilisent des conversions dynamiques implicites, où la liaison des opérations est suspendue jusqu’au moment de l’exécution. Au moment de l’exécution, les conversions implicites sont recherchées à partir du type au moment de l’exécution de `d` -- `string` vers le type cible. Une conversion a été détectée pour `string` mais pas pour `int`.

### <a name="implicit-constant-expression-conversions"></a>Conversions d’expressions constantes implicites

Une conversion d’expression constante implicite autorise les conversions suivantes :

*  Une *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) de type `int` peut être convertie en type `sbyte`, `byte`, `short`, `ushort`, `uint`ou `ulong`, à condition que la valeur de la *constant_expression* soit comprise dans la plage du type de destination.
*  Une *constant_expression* de type `long` peut être convertie en type `ulong`, à condition que la valeur de la *constant_expression* ne soit pas négative.

### <a name="implicit-conversions-involving-type-parameters"></a>Conversions implicites impliquant des paramètres de type

Les conversions implicites suivantes existent pour un paramètre de type donné `T`:

*  De `T` à sa classe de base effective `C`, de `T` à toute classe de base de `C`, et de `T` à toute interface implémentée par `C`. Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée en tant que conversion boxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence implicite ou conversion d’identité.
*  De `T` à un type d’interface `I` dans le jeu d’interfaces effectifs de `T`et de `T` à toute interface de base de `I`. Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée en tant que conversion boxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence implicite ou conversion d’identité.
*  De `T` à un paramètre de type `U`, fourni `T` dépend de `U` ([contraintes de paramètre de type](classes.md#type-parameter-constraints)). Au moment de l’exécution, si `U` est un type valeur, `T` et `U` sont nécessairement du même type et aucune conversion n’est effectuée. Sinon, si `T` est un type valeur, la conversion est exécutée en tant que conversion boxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence implicite ou conversion d’identité.
*  Du littéral null à `T`, fourni `T` est considéré comme un type référence.
*  De `T` à un type référence `I` s’il a une conversion implicite en type référence `S0` et `S0` a une conversion d’identité en `S`. Au moment de l’exécution, la conversion est exécutée de la même façon que la conversion en `S0`.
*  De `T` à un type d’interface `I` s’il a une conversion implicite en une interface ou un type délégué `I0` et `I0` est convertible en `I` ([conversion d’écart](interfaces.md#variance-conversion)). Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée en tant que conversion boxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence implicite ou conversion d’identité.

Si `T` est connu comme un type référence ([contraintes de paramètre de type](classes.md#type-parameter-constraints)), les conversions ci-dessus sont toutes classées comme conversions de référence implicites (conversions de[référence implicites](conversions.md#implicit-reference-conversions)). Si `T` n’est pas connu comme étant un type référence, les conversions ci-dessus sont classées comme conversions boxing (conversions[boxing](conversions.md#boxing-conversions)).

### <a name="user-defined-implicit-conversions"></a>Conversions implicites définies par l’utilisateur

Une conversion implicite définie par l’utilisateur se compose d’une conversion implicite standard facultative, suivie de l’exécution d’un opérateur de conversion implicite défini par l’utilisateur, suivie d’une autre conversion implicite standard facultative. Les règles exactes d’évaluation des conversions implicites définies par l’utilisateur sont décrites dans [traitement des conversions implicites définies par l’utilisateur](conversions.md#processing-of-user-defined-implicit-conversions).

### <a name="anonymous-function-conversions-and-method-group-conversions"></a>Conversions de fonctions anonymes et conversions de groupes de méthodes

Les fonctions anonymes et les groupes de méthodes n’ont pas de types dans et à eux-mêmes, mais peuvent être implicitement convertis en types délégués ou en types d’arborescences d’expressions. Les conversions de fonctions anonymes sont décrites plus en détail dans conversions de [fonctions anonymes](conversions.md#anonymous-function-conversions) et conversions de groupes de méthodes dans les conversions de [groupe de méthodes](conversions.md#method-group-conversions).

## <a name="explicit-conversions"></a>Conversions explicites

Les conversions suivantes sont classées comme des conversions explicites :

*  Toutes les conversions implicites.
*  Conversions numériques explicites.
*  Conversions d’énumération explicites.
*  Conversions Nullable explicites.
*  Conversions de référence explicites.
*  Conversions d’interface explicites.
*  Conversions unboxing.
*  Conversions dynamiques explicites
*  Conversions explicites définies par l’utilisateur.

Les conversions explicites peuvent se produire dans les expressions de cast ([expressions de cast](expressions.md#cast-expressions)).

L’ensemble de conversions explicites comprend toutes les conversions implicites. Cela signifie que les expressions de cast redondantes sont autorisées.

Les conversions explicites qui ne sont pas des conversions implicites sont des conversions qui ne peuvent pas être prouvées comme ayant échoué, des conversions qui sont susceptibles de perdre des informations et des conversions entre des domaines de types suffisamment différentes pour mériter une valeur explicite conventions.

### <a name="explicit-numeric-conversions"></a>Conversions numériques explicites

Les conversions numériques explicites sont les conversions d’un *numeric_type* à un autre *numeric_type* pour lequel une conversion numérique implicite ([conversions numériques implicites](conversions.md#implicit-numeric-conversions)) n’existe pas encore :

*  De `sbyte` à `byte`, `ushort`, `uint`, `ulong`ou `char`.
*  De `byte` à `sbyte` et `char`.
*  De `short` à `sbyte`, `byte`, `ushort`, `uint`, `ulong`ou `char`.
*  De `ushort` à `sbyte`, `byte`, `short`ou `char`.
*  De `int` à `sbyte`, `byte`, `short`, `ushort`, `uint`, `ulong`ou `char`.
*  De `uint` à `sbyte`, `byte`, `short`, `ushort`, `int`ou `char`.
*  De `long` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `ulong`ou `char`.
*  De `ulong` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`ou `char`.
*  De `char` à `sbyte`, `byte`ou `short`.
*  De `float` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`ou `decimal`.
*  De `double` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`ou `decimal`.
*  De `decimal` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`ou `double`.

Étant donné que les conversions explicites incluent toutes les conversions numériques implicites et explicites, il est toujours possible de convertir des *numeric_type* en tout autre *numeric_type* à l’aide d’une expression de cast ([expressions de cast](expressions.md#cast-expressions)).

Les conversions numériques explicites peuvent éventuellement perdre des informations ou provoquer des exceptions. Une conversion numérique explicite est traitée comme suit :

*  Pour une conversion d’un type intégral vers un autre type intégral, le traitement dépend du contexte de vérification de dépassement de capacité ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)) dans lequel la conversion a lieu :
    * Dans un contexte de `checked`, la conversion s’effectue correctement si la valeur de l’opérande source est comprise dans la plage du type de destination, mais lève une `System.OverflowException` si la valeur de l’opérande source est en dehors de la plage du type de destination.
    * Dans un contexte de `unchecked`, la conversion est toujours réussie et se poursuit comme suit.
        * Si le type source est plus grand que le type de destination, la valeur source est tronquée via l’abandon de ses bits « supplémentaires » les plus significatifs. Le résultat est ensuite traité en tant que valeur du type de destination.
        * Si le type source est plus petit que le type de destination, la valeur source fait l’objet d’une extension de signe ou d’une extension de zéros, pour avoir la même taille que le type de destination. L’extension de signe est utilisée si le type source est signé. L’extension de zéro est utilisée si le type source est non signé. Le résultat est ensuite traité en tant que valeur du type de destination.
        * Si le type source a la même taille que le type de destination, la valeur source est traitée comme une valeur du type de destination.
*  Pour une conversion de `decimal` en type intégral, la valeur source est arrondie vers zéro à la valeur intégrale la plus proche, et cette valeur intégrale devient le résultat de la conversion. Si la valeur intégrale obtenue est en dehors de la plage du type de destination, une `System.OverflowException` est levée.
*  Pour une conversion à partir de `float` ou `double` en type intégral, le traitement dépend du contexte de vérification de dépassement de capacité ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)) dans lequel la conversion a lieu :
    * Dans un contexte de `checked`, la conversion se poursuit comme suit :
        * Si la valeur de l’opérande est NaN ou infinie, une `System.OverflowException` est levée.
        * Dans le cas contraire, l’opérande source est arrondi vers la valeur intégrale la plus proche de zéro. Si cette valeur intégrale est comprise dans la plage du type de destination, cette valeur est le résultat de la conversion.
        * Sinon, une exception `System.OverflowException` est levée.
    * Dans un contexte de `unchecked`, la conversion est toujours réussie et se poursuit comme suit.
        * Si la valeur de l’opérande est NaN ou infinie, le résultat de la conversion est une valeur non spécifiée du type de destination.
        * Dans le cas contraire, l’opérande source est arrondi vers la valeur intégrale la plus proche de zéro. Si cette valeur intégrale est comprise dans la plage du type de destination, cette valeur est le résultat de la conversion.
        * Dans le cas contraire, le résultat de la conversion est une valeur non spécifiée du type de destination.
*  Pour une conversion de `double` en `float`, la valeur de `double` est arrondie à la valeur de `float` la plus proche. Si la valeur `double` est trop petite pour être représentée en tant que `float`, le résultat devient un zéro positif ou un zéro négatif. Si la valeur `double` est trop grande pour être représentée en tant que `float`, le résultat devient une infini positif ou négatif. Si la valeur `double` est NaN, le résultat est également NaN.
*  Pour une conversion de `float` ou `double` en `decimal`, la valeur source est convertie en `decimal` représentation et arrondie au nombre le plus proche après la valeur de 28 décimales, si nécessaire ([type décimal](types.md#the-decimal-type)). Si la valeur source est trop petite pour être représentée en tant que `decimal`, le résultat devient zéro. Si la valeur source est NaN, Infinity ou trop grande pour être représentée comme un `decimal`, un `System.OverflowException` est levé.
*  Pour une conversion de `decimal` en `float` ou `double`, la valeur `decimal` est arrondie à la valeur `double` ou `float` la plus proche. Bien que cette conversion puisse perdre la précision, elle ne provoque jamais la levée d’une exception.

### <a name="explicit-enumeration-conversions"></a>Conversions d’énumération explicites

Les conversions d’énumération explicites sont les suivantes :

*  À partir de `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`ou `decimal` à n’importe quel *enum_type*.
*  À partir de n’importe quelle *enum_type* vers `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`ou `decimal`.
*  De tout *enum_type* à n’importe quel autre *enum_type*.

Une conversion d’énumération explicite entre deux types est traitée en traitant tout *enum_type* participant en tant que type sous-jacent de ce *enum_type*, puis en effectuant une conversion numérique implicite ou explicite entre les types résultants. Par exemple, étant donné un *enum_type* `E` avec et le type sous-jacent de `int`, une conversion de `E` en `byte` est traitée comme une conversion numérique explicite ([conversions numériques explicites](conversions.md#explicit-numeric-conversions)) de `int` à `byte`, et une conversion de `byte` en `E` est traitée comme une conversion numérique implicite ([conversions numériques implicites](conversions.md#implicit-numeric-conversions)) de `byte` à `int`.

### <a name="explicit-nullable-conversions"></a>Conversions Nullable explicites

Les ***conversions Nullable explicites*** autorisent les conversions explicites prédéfinies qui fonctionnent sur les types valeur non Nullable pour être également utilisées avec les formes Nullable de ces types. Pour chacune des conversions explicites prédéfinies qui convertissent un type valeur n’acceptant pas les valeurs NULL `S` en un type valeur non Nullable `T` ([conversion d’identité](conversions.md#identity-conversion), conversions [numériques implicites](conversions.md#implicit-numeric-conversions), conversions d' [énumération implicites](conversions.md#implicit-enumeration-conversions), [conversions numériques explicites](conversions.md#explicit-numeric-conversions)et [conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)), les conversions Nullable suivantes existent :

*  Conversion explicite de `S?` en `T?`.
*  Conversion explicite de `S` en `T?`.
*  Conversion explicite de `S?` en `T`.

L’évaluation d’une conversion Nullable basée sur une conversion sous-jacente de `S` en `T` se déroule comme suit :

*  Si la conversion Nullable est comprise entre `S?` et `T?`:
    * Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est la valeur null de type `T?`.
    * Dans le cas contraire, la conversion est évaluée en tant que désencapsulation de `S?` à `S`, suivi de la conversion sous-jacente de `S` en `T`, suivie d’une encapsulation de `T` à `T?`.
*  Si la conversion Nullable est comprise entre `S` et `T?`, la conversion est évaluée comme la conversion sous-jacente de `S` en `T` suivie d’une encapsulation de `T` à `T?`.
*  Si la conversion Nullable s’effectue de `S?` à `T`, la conversion est évaluée comme un désencapsulation de `S?` à `S` suivi de la conversion sous-jacente de `S` à `T`.

Notez qu’une tentative de désencapsulation d’une valeur Nullable lève une exception si la valeur est `null`.

### <a name="explicit-reference-conversions"></a>Conversions de références explicites

Les conversions de référence explicites sont les suivantes :

*  À partir de `object` et `dynamic` à n’importe quel autre *reference_type*.
*  À partir de n’importe quelle *class_type* `S` à tout `T`*class_type* , fourni `S` est une classe de base de `T`.
*  À partir de tout *class_type* `S` à tout `T`*interface_type* , fourni `S` n’est pas sealed et fourni `S` n’implémente pas `T`.
*  À partir de tout *interface_type* `S` à tout `T`*class_type* , fourni `T` n’est pas scellé ou fourni `T` implémente `S`.
*  À partir de tout *interface_type* `S` à n’importe quel `T`*interface_type* , fourni `S` n’est pas dérivé de `T`.
*  D’un *array_type* `S` avec un type d’élément `SE` à un *array_type* `T` avec un type d’élément `TE`, à condition que les conditions suivantes soient remplies :
    * `S` et `T` diffèrent uniquement par le type d’élément. En d’autres termes, `S` et `T` ont le même nombre de dimensions.
    * Les `SE` et les `TE` sont *reference_type*s.
    * Une conversion de référence explicite existe de `SE` à `TE`.
*  À partir de `System.Array` et des interfaces qu’il implémente sur n’importe quel *array_type*.
*  À partir d’un type tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et à ses interfaces de base, à condition qu’il existe une conversion de référence explicite de `S` en `T`.
*  De `System.Collections.Generic.IList<S>` et ses interfaces de base à un type de tableau unidimensionnel `T[]`, à condition qu’il existe une conversion d’identité ou de référence explicite de `S` à `T`.
*  À partir de `System.Delegate` et des interfaces qu’il implémente sur n’importe quel *delegate_type*.
*  D’un type référence à un type référence `T` s’il a une conversion de référence explicite en un type référence `T0` et `T0` a une `T`de conversion d’identité.
*  D’un type référence à un type d’interface ou de délégué `T` s’il a une conversion de référence explicite en une interface ou un type délégué `T0` et que `T0` est convertible en `T` ou `T` est convertible en `T0` ([conversion d’écart](interfaces.md#variance-conversion)).
*  De `D<S1...Sn>` à `D<T1...Tn>` où `D<X1...Xn>` est un type délégué générique, `D<S1...Sn>` n’est pas compatible avec ou identique à `D<T1...Tn>`, et pour chaque paramètre de type `Xi` de `D` éléments suivants :
    * Si `Xi` est invariant, `Si` est identique à `Ti`.
    * Si `Xi` est covariant, il existe une conversion d’identité ou de référence implicite ou explicite de `Si` en `Ti`.
    * Si `Xi` est contravariant, `Si` et `Ti` sont identiques ou les deux types de référence.
*  Les conversions explicites impliquant des paramètres de type connus comme des types référence. Pour plus d’informations sur les conversions explicites impliquant des paramètres de type, consultez [conversions explicites impliquant des paramètres de type](conversions.md#explicit-conversions-involving-type-parameters).

Les conversions de référence explicites sont les conversions entre les types référence qui requièrent des vérifications à l’exécution pour s’assurer qu’elles sont correctes.

Pour qu’une conversion de référence explicite aboutisse au moment de l’exécution, la valeur de l’opérande source doit être `null`ou le type réel de l’objet référencé par l’opérande source doit être un type qui peut être converti vers le type de destination par une conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) ou une conversion boxing ([conversions boxing](conversions.md#boxing-conversions)). En cas d’échec d’une conversion de référence explicite, une `System.InvalidCastException` est levée.

Les conversions de référence, implicites ou explicites, ne modifient jamais l’identité référentielle de l’objet en cours de conversion. En d’autres termes, si une conversion de référence peut modifier le type de la référence, elle ne modifie jamais le type ou la valeur de l’objet auquel il est fait référence.

### <a name="unboxing-conversions"></a>Conversions unboxing

Une conversion unboxing permet à un type de référence d’être converti explicitement en *Value_type*. Une conversion unboxing existe à partir des types `object`, `dynamic` et `System.ValueType` à n’importe quel *non_nullable_value_type*, et de tout *INTERFACE_TYPE* à n’importe quel *non_nullable_value_type* qui implémente le *INTERFACE_TYPE*. En outre, le type `System.Enum` peut être unboxed en *enum_type*.

Une conversion unboxing existe d’un type référence à un *nullable_type* s’il existe une conversion unboxing du type référence au *non_nullable_value_type* sous-jacent de l' *nullable_type*.

Un type valeur `S` a une conversion unboxing d’un type interface `I` s’il a une conversion unboxing d’un type interface `I0` et `I0` a une conversion d’identité en `I`.

Un type valeur `S` a une conversion unboxing d’un type interface `I` s’il a une conversion unboxing à partir d’une interface ou d’un type délégué `I0` et `I0` est convertible en `I` ou `I` est convertible en `I0` ([conversion d’écart](interfaces.md#variance-conversion)).

Une opération d’unboxing consiste à vérifier en premier lieu que l’instance d’objet est une valeur boxed du *Value_type*donné, puis à copier la valeur hors de l’instance. La conversion unboxing d’une référence null en *nullable_type* produit la valeur null du *nullable_type*. Un struct peut être unboxed du type `System.ValueType`, car il s’agit d’une classe de base pour tous les structs ([héritage](structs.md#inheritance)).

Les conversions unboxing sont décrites plus en détail dans [conversions unboxing](types.md#unboxing-conversions).

### <a name="explicit-dynamic-conversions"></a>Conversions dynamiques explicites

Une conversion dynamique explicite existe à partir d’une expression de type `dynamic` à n’importe quel type `T`. La conversion est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), ce qui signifie qu’une conversion explicite sera recherchée au moment de l’exécution à partir du type d’exécution de l’expression à `T`. Si aucune conversion n’est trouvée, une exception Runtime est levée.

Si la liaison dynamique de la conversion n’est pas souhaitée, l’expression peut être d’abord convertie en `object`, puis vers le type souhaité.

Supposons que la classe suivante soit définie :
```csharp
class C
{
    int i;

    public C(int i) { this.i = i; }

    public static explicit operator C(string s) 
    {
        return new C(int.Parse(s));
    }
}
```

L’exemple suivant illustre des conversions dynamiques explicites :
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

La meilleure conversion de `o` en `C` est trouvée au moment de la compilation pour être une conversion de référence explicite. Cela échoue au moment de l’exécution, car `"1"` n’est pas en fait un `C`. La conversion de `d` en `C` Toutefois, en tant que conversion dynamique explicite, est suspendue au moment de l’exécution, où une conversion définie par l’utilisateur à partir du type d’exécution de `d` -- `string`--à `C` est trouvée, et elle se déroule correctement.

### <a name="explicit-conversions-involving-type-parameters"></a>Conversions explicites impliquant des paramètres de type

Les conversions explicites suivantes existent pour un paramètre de type donné `T`:

*  À partir de la classe de base effective `C` de `T` à `T` et à partir d’une classe de base de `C` à `T`. Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée en tant que conversion unboxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  De n’importe quel type d’interface à `T`. Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée en tant que conversion unboxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  De `T` à n’importe quel *interface_type* `I` à condition qu’il n’y ait pas encore de conversion implicite de `T` en `I`. Au moment de l’exécution, si `T` est un type valeur, la conversion est exécutée comme une conversion boxing suivie d’une conversion de référence explicite. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  À partir d’un paramètre de type `U` à `T`, fourni `T` dépend de `U` ([contraintes de paramètre de type](classes.md#type-parameter-constraints)). Au moment de l’exécution, si `U` est un type valeur, `T` et `U` sont nécessairement du même type et aucune conversion n’est effectuée. Sinon, si `T` est un type valeur, la conversion est exécutée en tant que conversion unboxing. Dans le cas contraire, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.

Si `T` est considéré comme un type référence, les conversions ci-dessus sont toutes classées comme des conversions de référence explicites (conversions de[référence explicites](conversions.md#explicit-reference-conversions)). Si `T` n’est pas connu comme étant un type référence, les conversions ci-dessus sont classées comme conversions unboxing (conversions[unboxing](conversions.md#unboxing-conversions)).

Les règles ci-dessus n’autorisent pas une conversion explicite directe d’un paramètre de type sans contrainte en un type qui n’est pas une interface, ce qui peut être surprenant. La raison de cette règle est d’éviter toute confusion et de rendre la sémantique de ces conversions claire. Observez par exemple la déclaration suivante :
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

Si la conversion directe explicite de `t` en `int` était autorisée, il peut s’attendre à ce que `X<int>.F(7)` retourne `7L`. Toutefois, ce n’est pas le cas, car les conversions numériques standard sont uniquement prises en compte lorsque les types sont identifiés comme étant numériques au moment de la liaison. Pour que la sémantique soit claire, l’exemple ci-dessus doit plutôt être écrit :
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

Ce code va maintenant être compilé, mais l’exécution de `X<int>.F(7)` lèvera une exception au moment de l’exécution, car un `int` boxed ne peut pas être converti directement en `long`.

### <a name="user-defined-explicit-conversions"></a>Conversions explicites définies par l’utilisateur

Une conversion explicite définie par l’utilisateur se compose d’une conversion explicite standard facultative, suivie de l’exécution d’un opérateur de conversion implicite ou explicite défini par l’utilisateur, puis d’une autre conversion explicite standard facultative. Les règles exactes d’évaluation des conversions explicites définies par l’utilisateur sont décrites dans [traitement des conversions explicites définies par l’utilisateur](conversions.md#processing-of-user-defined-explicit-conversions).

## <a name="standard-conversions"></a>Conversions standard

Les conversions standard sont les conversions prédéfinies qui peuvent se produire dans le cadre d’une conversion définie par l’utilisateur.

### <a name="standard-implicit-conversions"></a>Conversions implicites standard

Les conversions implicites suivantes sont classées comme conversions implicites standard :

*  Conversions d’identité ([conversion d’identité](conversions.md#identity-conversion))
*  Conversions numériques implicites ([conversions numériques implicites](conversions.md#implicit-numeric-conversions))
*  Conversions Nullable implicites ([conversions Nullable implicites](conversions.md#implicit-nullable-conversions))
*  Conversions de référence implicites ([conversions de référence implicites](conversions.md#implicit-reference-conversions))
*  Conversions boxing ([conversions boxing](conversions.md#boxing-conversions))
*  Conversions d’expressions constantes implicites ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions))
*  Conversions implicites impliquant des paramètres de type ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters))

Les conversions implicites standard excluent spécifiquement les conversions implicites définies par l’utilisateur.

### <a name="standard-explicit-conversions"></a>Conversions explicites standard

Les conversions explicites standard sont toutes des conversions implicites standard, ainsi que le sous-ensemble des conversions explicites pour lesquelles il existe une conversion implicite standard opposée. En d’autres termes, s’il existe une conversion implicite standard d’un type `A` à un type `B`, une conversion explicite standard existe à partir du type `A` en type `B` et du type `B` au type `A`.

## <a name="user-defined-conversions"></a>Conversions définies par l’utilisateur

C#permet aux conversions implicites et explicites prédéfinies d’être complétées par les ***conversions définies***par l’utilisateur. Les conversions définies par l’utilisateur sont introduites par la déclaration d’opérateurs de conversion ([opérateurs de conversion](classes.md#conversion-operators)) dans les types classe et struct.

### <a name="permitted-user-defined-conversions"></a>Conversions définies par l’utilisateur autorisées

C#autorise uniquement la déclaration de certaines conversions définies par l’utilisateur. En particulier, il n’est pas possible de redéfinir une conversion implicite ou explicite existante.

Pour un type de source donné `S` et le type de cible `T`, si `S` ou `T` sont des types Nullable, laissez `S0` et `T0` faire référence à leurs types sous-jacents, sinon `S0` et `T0` sont égaux à `S` et `T` respectivement. Une classe ou un struct est autorisé à déclarer une conversion d’un type source `S` en un type cible `T` uniquement si toutes les conditions suivantes sont vraies :

*  `S0` et `T0` sont des types différents.
*  `S0` ou `T0` est le type de classe ou de struct dans lequel la déclaration d’opérateur a lieu.
*  Ni `S0` ni `T0` n’est un *INTERFACE_TYPE*.
*  En excluant les conversions définies par l’utilisateur, il n’existe pas de conversion de `S` à `T` ou de `T` à `S`.

Les restrictions qui s’appliquent aux conversions définies par l’utilisateur sont abordées plus en détail dans [opérateurs de conversion](classes.md#conversion-operators).

### <a name="lifted-conversion-operators"></a>Opérateurs de conversion levés

Dans le cas d’un opérateur de conversion défini par l’utilisateur qui convertit un type valeur n’acceptant pas les valeurs NULL `S` en un type valeur n’acceptant pas les valeurs NULL `T`, il existe un ***opérateur de conversion levé*** qui convertit de `S?` en `T?`. Cet opérateur de conversion levé effectue un désencapsulage de `S?` à `S` suivi de la conversion définie par l’utilisateur de `S` à `T` suivi d’un renvoi de `T` à `T?`, sauf qu’une valeur null `S?` convertit directement en `T?`valeur null.

Un opérateur de conversion levé a la même classification implicite ou explicite que son opérateur de conversion défini par l’utilisateur sous-jacent. Le terme « conversion définie par l’utilisateur » s’applique à l’utilisation des opérateurs de conversion levés et définis par l’utilisateur.

### <a name="evaluation-of-user-defined-conversions"></a>Évaluation des conversions définies par l’utilisateur

Une conversion définie par l’utilisateur convertit une valeur de son type, appelée ***type source***, en un autre type, appelé le ***type cible***. Évaluation d’un centre de conversion défini par l’utilisateur sur la recherche de l’opérateur de conversion défini par l’utilisateur le ***plus spécifique*** pour les types source et cible particuliers. Cette détermination est divisée en plusieurs étapes :

*  Recherche de l’ensemble de classes et de structures à partir desquelles les opérateurs de conversion définis par l’utilisateur seront pris en compte. Cet ensemble se compose du type de source et de ses classes de base, ainsi que du type cible et de ses classes de base (avec les hypothèses implicites que seules les classes et les structs peuvent déclarer des opérateurs définis par l’utilisateur, et que les types non-classes n’ont pas de classes de base). Dans le cadre de cette étape, si le type source ou cible est un *nullable_type*, le type sous-jacent est utilisé à la place.
*  À partir de cet ensemble de types, déterminer quels opérateurs de conversion définis par l’utilisateur et levés sont applicables. Pour qu’un opérateur de conversion soit applicable, il doit être possible d’effectuer une conversion standard ([conversions standard](conversions.md#standard-conversions)) du type source en type d’opérande de l’opérateur, et il doit être possible d’effectuer une conversion standard du type de résultat de l’opérateur vers le type cible.
*  À partir de l’ensemble des opérateurs définis par l’utilisateur applicables, en déterminant quel opérateur est sans ambiguïté le plus spécifique. En règle générale, l’opérateur le plus spécifique est l’opérateur dont le type d’opérande est « le plus proche » du type source et dont le type de résultat est « le plus proche » du type cible. Les opérateurs de conversion définis par l’utilisateur sont préférés aux opérateurs de conversion levés. Les règles exactes pour l’établissement de l’opérateur de conversion défini par l’utilisateur le plus spécifique sont définies dans les sections suivantes.

Une fois qu’un opérateur de conversion défini par l’utilisateur le plus spécifique a été identifié, l’exécution réelle de la conversion définie par l’utilisateur implique jusqu’à trois étapes :

*  Tout d’abord, si nécessaire, en effectuant une conversion standard du type source vers le type d’opérande de l’opérateur de conversion défini par l’utilisateur ou levé.
*  Ensuite, appelez l’opérateur de conversion défini par l’utilisateur ou levé pour effectuer la conversion.
*  Enfin, si nécessaire, effectuer une conversion standard du type de résultat de l’opérateur de conversion défini par l’utilisateur ou levé vers le type cible.

L’évaluation d’une conversion définie par l’utilisateur n’implique jamais plus d’un opérateur de conversion défini par l’utilisateur ou levé. En d’autres termes, une conversion du type `S` en type `T` n’exécute jamais d’abord une conversion définie par l’utilisateur de `S` en `X`, puis exécute une conversion définie par l’utilisateur de `X` à `T`.

Les définitions exactes de l’évaluation des conversions implicites ou explicites définies par l’utilisateur sont indiquées dans les sections suivantes. Les définitions utilisent les termes suivants :

*  Si une conversion implicite standard ([conversions implicites standard](conversions.md#standard-implicit-conversions)) existe à partir d’un type `A` à un type `B`, et si ni `A`, ni `B` ne sont *interface_type*s, `A` est supposé être ***englobé par*** `B`, et `B` est dit ***englober*** `A`.
*  Le ***type le plus englobant*** dans un ensemble de types est celui qui englobe tous les autres types dans le jeu. Si aucun type unique n’englobe tous les autres types, le jeu n’a pas de type englobant le plus. En termes plus intuitifs, le type le plus englobant est le plus grand type dans l’ensemble, à savoir le type dans lequel chacun des autres types peut être converti implicitement.
*  Le ***type le plus englobé*** dans un ensemble de types est celui qui est englobé par tous les autres types dans le jeu. Si aucun type unique n’est englobé par tous les autres types, le jeu n’a pas de type le plus englobé. En termes plus intuitifs, le type le plus englobé est le type le plus petit dans le jeu, le type qui peut être converti implicitement en chacun des autres types.

### <a name="processing-of-user-defined-implicit-conversions"></a>Traitement des conversions implicites définies par l’utilisateur

Une conversion implicite définie par l’utilisateur de type `S` en type `T` est traitée comme suit :

*  Déterminez les types `S0` et `T0`. Si `S` ou `T` sont des types Nullable, `S0` et `T0` sont leurs types sous-jacents, sinon `S0` et `T0` sont égaux à `S` et `T` respectivement.
*  Recherchez l’ensemble de types, `D`, à partir duquel les opérateurs de conversion définis par l’utilisateur seront pris en compte. Cet ensemble se compose d' `S0` (si `S0` est une classe ou un struct), des classes de base de `S0` (si `S0` est une classe) et `T0` (si `T0` est une classe ou un struct).
*  Recherchez l’ensemble des opérateurs de conversion démontés et définis par l’utilisateur applicables, `U`. Cet ensemble se compose des opérateurs de conversion implicite définis par l’utilisateur et levés déclarés par les classes ou les structs dans `D` qui convertissent un type englobant `S` en un type englobé par `T`. Si `U` est vide, la conversion n’est pas définie et une erreur de compilation se produit.
*  Recherchez le type de source le plus spécifique, `SX`, des opérateurs dans `U`:
    * Si l’un des opérateurs de `U` convertir à partir d' `S`, `SX` est `S`.
    * Dans le cas contraire, `SX` est le type le plus englobé dans l’ensemble combiné des types de sources des opérateurs dans `U`. Si exactement un type le plus englobé est introuvable, la conversion est ambiguë et une erreur de compilation se produit.
*  Recherchez le type de cible le plus spécifique, `TX`, des opérateurs dans `U`:
    * Si l’un des opérateurs de `U` convertir en `T`, `TX` est `T`.
    * Dans le cas contraire, `TX` est le type le plus englobant dans l’ensemble combiné des types cibles des opérateurs dans `U`. Si un seul type englobant est introuvable, la conversion est ambiguë et une erreur de compilation se produit.
*  Recherchez l’opérateur de conversion le plus spécifique :
    * Si `U` contient exactement un opérateur de conversion défini par l’utilisateur qui effectue la conversion de `SX` en `TX`, il s’agit de l’opérateur de conversion le plus spécifique.
    * Sinon, si `U` contient exactement un opérateur de conversion levé qui convertit de `SX` en `TX`, il s’agit de l’opérateur de conversion le plus spécifique.
    * Dans le cas contraire, la conversion est ambiguë et une erreur de compilation se produit.
*  Enfin, appliquez la conversion :
    * Si `S` n’est pas `SX`, une conversion implicite standard de `S` en `SX` est effectuée.
    * L’opérateur de conversion le plus spécifique est appelé pour convertir `SX` en `TX`.
    * Si `TX` n’est pas `T`, une conversion implicite standard de `TX` en `T` est effectuée.

### <a name="processing-of-user-defined-explicit-conversions"></a>Traitement des conversions explicites définies par l’utilisateur

Une conversion explicite définie par l’utilisateur du type `S` en type `T` est traitée comme suit :

*  Déterminez les types `S0` et `T0`. Si `S` ou `T` sont des types Nullable, `S0` et `T0` sont leurs types sous-jacents, sinon `S0` et `T0` sont égaux à `S` et `T` respectivement.
*  Recherchez l’ensemble de types, `D`, à partir duquel les opérateurs de conversion définis par l’utilisateur seront pris en compte. Cet ensemble se compose d' `S0` (si `S0` est une classe ou un struct), des classes de base de `S0` (si `S0` est une classe), d' `T0` (si `T0` est une classe ou un struct) et des classes de base de `T0` (si `T0` est une classe).
*  Recherchez l’ensemble des opérateurs de conversion démontés et définis par l’utilisateur applicables, `U`. Cet ensemble se compose des opérateurs de conversion implicite ou explicite définis par l’utilisateur déclarés par les classes ou les structs dans `D` qui effectuent une conversion à partir d’un type englobant ou englobé par `S` à un type englobant ou englobé par `T`. Si `U` est vide, la conversion n’est pas définie et une erreur de compilation se produit.
*  Recherchez le type de source le plus spécifique, `SX`, des opérateurs dans `U`:
    * Si l’un des opérateurs de `U` convertir à partir d' `S`, `SX` est `S`.
    * Sinon, si l’un des opérateurs de `U` convertir des types qui englobent `S`, `SX` est le type le plus englobé dans l’ensemble combiné des types de sources de ces opérateurs. Si aucun type englobant le plus n’est trouvé, la conversion est ambiguë et une erreur de compilation se produit.
    * Dans le cas contraire, `SX` est le type le plus englobant dans l’ensemble combiné des types de sources des opérateurs dans `U`. Si un seul type englobant est introuvable, la conversion est ambiguë et une erreur de compilation se produit.
*  Recherchez le type de cible le plus spécifique, `TX`, des opérateurs dans `U`:
    * Si l’un des opérateurs de `U` convertir en `T`, `TX` est `T`.
    * Sinon, si l’un des opérateurs de `U` convertir en types qui sont entourés par `T`, `TX` est le type le plus englobant dans l’ensemble combiné de types cibles de ces opérateurs. Si un seul type englobant est introuvable, la conversion est ambiguë et une erreur de compilation se produit.
    * Dans le cas contraire, `TX` est le type le plus englobé dans l’ensemble combiné de types cibles des opérateurs dans `U`. Si aucun type englobant le plus n’est trouvé, la conversion est ambiguë et une erreur de compilation se produit.
*  Recherchez l’opérateur de conversion le plus spécifique :
    * Si `U` contient exactement un opérateur de conversion défini par l’utilisateur qui effectue la conversion de `SX` en `TX`, il s’agit de l’opérateur de conversion le plus spécifique.
    * Sinon, si `U` contient exactement un opérateur de conversion levé qui convertit de `SX` en `TX`, il s’agit de l’opérateur de conversion le plus spécifique.
    * Dans le cas contraire, la conversion est ambiguë et une erreur de compilation se produit.
*  Enfin, appliquez la conversion :
    * Si `S` n’est pas `SX`, une conversion explicite standard de `S` en `SX` est effectuée.
    * L’opérateur de conversion défini par l’utilisateur le plus spécifique est appelé pour convertir des `SX` en `TX`.
    * Si `TX` n’est pas `T`, une conversion explicite standard de `TX` en `T` est effectuée.

## <a name="anonymous-function-conversions"></a>Conversions de fonctions anonymes

Un *anonymous_method_expression* ou *lambda_expression* est classé comme une fonction anonyme ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)). L’expression n’a pas de type mais peut être convertie implicitement en un type délégué compatible ou un type d’arborescence d’expression. Plus précisément, une fonction anonyme `F` est compatible avec un type délégué `D` fourni :

*  Si `F` contient une *anonymous_function_signature*, `D` et `F` ont le même nombre de paramètres.
*  Si `F` ne contient pas de *anonymous_function_signature*, `D` peut avoir zéro, un ou plusieurs paramètres de tout type, à condition qu’aucun paramètre de `D` n’ait le modificateur de paramètre `out`.
*  Si `F` contient une liste de paramètres explicitement typée, chaque paramètre dans `D` a les mêmes type et modificateurs que le paramètre correspondant dans `F`.
*  Si `F` contient une liste de paramètres typée implicitement, `D` n’a pas de paramètres `ref` ou `out`.
*  Si le corps de `F` est une expression et que `D` a un type de retour `void` ou `F` est Async et `D` a le type de retour `Task`, alors, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est une expression valide (les [expressions](expressions.md)WRT) qui serait autorisée en tant que *statement_expression* ([instructions d’expression](statements.md#expression-statements)).
*  Si le corps de `F` est un bloc d’instructions et `D` a un type de retour `void` ou `F` est Async et `D` a le type de retour `Task`, alors, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est un bloc d’instruction valide ( [blocs](statements.md#blocks)WRT) dans lequel aucune instruction `return` spécifie une expression.
*  Si le corps de `F` est une expression et que *`F` est non asynchrone et `D`* a un type de retour non void `T`, *ou* `F` est Async et `D` a un type de retour `Task<T>`, alors, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est une expression valide ( [expressions](expressions.md)WRT) qui est implicitement convertible en `T`.
*  Si le corps de `F` est un bloc d’instructions et *que `F` est non asynchrone et `D`* a un type de retour non void `T`, *ou* `F` est Async et `D` a un type de retour `Task<T>`, alors, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est un bloc d’instructions valide ( [blocs](statements.md#blocks)WRT) avec un point de terminaison non accessible dans lequel chaque instruction `return` spécifie une expression implicitement convertible en `T`.

Pour des raisons de concision, cette section utilise la forme abrégée pour les types de tâches `Task` et `Task<T>` ([fonctions Async](classes.md#async-functions)).

Une expression lambda `F` est compatible avec un type d’arborescence d’expression `Expression<D>` si `F` est compatible avec le type délégué `D`. Notez que cela ne s’applique pas aux méthodes anonymes, mais uniquement aux expressions lambda.

Certaines expressions lambda ne peuvent pas être converties en types d’arborescence d’expression : bien que la conversion *existe*, elle échoue au moment de la compilation. C’est le cas si l’expression lambda :

*  A un corps de *bloc*
*  Contient des opérateurs d’assignation simples ou composés
*  Contient une expression liée dynamiquement
*  Est asynchrone

Les exemples qui suivent utilisent un type délégué générique `Func<A,R>` qui représente une fonction qui accepte un argument de type `A` et retourne une valeur de type `R`:
```csharp
delegate R Func<A,R>(A arg);
```

Dans les affectations
```csharp
Func<int,int> f1 = x => x + 1;                 // Ok

Func<int,double> f2 = x => x + 1;              // Ok

Func<double,int> f3 = x => x + 1;              // Error

Func<int, Task<int>> f4 = async x => x + 1;    // Ok
```
le paramètre et les types de retour de chaque fonction anonyme sont déterminés à partir du type de la variable à laquelle la fonction anonyme est assignée.

La première assignation convertit correctement la fonction anonyme en type délégué `Func<int,int>` car, lorsque `x` reçoit le type `int`, `x+1` est une expression valide qui est implicitement convertible en type `int`.

De même, la deuxième assignation convertit correctement la fonction anonyme en type délégué `Func<int,double>`, car le résultat de `x+1` (de type `int`) est implicitement convertible en type `double`.

Toutefois, la troisième assignation est une erreur au moment de la compilation, car, lorsque `x` est donné à un type `double`, le résultat de `x+1` (de type `double`) n’est pas implicitement convertible en type `int`.

La quatrième assignation convertit correctement la fonction Async anonyme en type délégué `Func<int, Task<int>>`, car le résultat de `x+1` (de type `int`) est implicitement convertible en type de résultat `int` du type de tâche `Task<int>`.

Les fonctions anonymes peuvent influencer la résolution de surcharge et participer à l’inférence de type. Pour plus d’informations, consultez [membres de fonction](expressions.md#function-members) .

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a>Évaluation de conversions de fonctions anonymes en types délégués

La conversion d’une fonction anonyme en type délégué produit une instance de délégué qui fait référence à la fonction anonyme et au jeu (éventuellement vide) des variables externes capturées qui sont actives au moment de l’évaluation. Lorsque le délégué est appelé, le corps de la fonction anonyme est exécuté. Le code du corps est exécuté à l’aide de l’ensemble des variables externes capturées référencées par le délégué.

La liste d’appel d’un délégué produit à partir d’une fonction anonyme contient une seule entrée. L’objet cible et la méthode cible exacts du délégué ne sont pas spécifiés. En particulier, il n’est pas spécifié si l’objet cible du délégué est `null`, la valeur `this` du membre de fonction englobant ou un autre objet.

Les conversions de fonctions anonymes sémantiquement identiques avec le même ensemble (éventuellement vide) d’instances de variable externe capturées vers les mêmes types délégués sont autorisées (mais pas obligatoires) pour retourner la même instance de délégué. Le terme sémantiquement identique est utilisé ici pour signifier que l’exécution des fonctions anonymes produira, dans tous les cas, les mêmes effets en raison des mêmes arguments. Cette règle permet d’optimiser le code tel que le suivant.

```csharp
delegate double Function(double x);

class Test
{
    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void F(double[] a, double[] b) {
        a = Apply(a, (double x) => Math.Sin(x));
        b = Apply(b, (double y) => Math.Sin(y));
        ...
    }
}
```

Étant donné que les deux délégués de fonction anonymes ont le même ensemble (vide) de variables externes capturées, et puisque les fonctions anonymes sont sémantiquement identiques, le compilateur est autorisé à faire en sorte que les délégués fassent référence à la même méthode cible. En effet, le compilateur est autorisé à retourner la même instance de délégué à partir des deux expressions de fonction anonymes.

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a>Évaluation de conversions de fonctions anonymes en types d’arborescences d’expression

La conversion d’une fonction anonyme en un type d’arborescence d’expression produit une arborescence d’expression ([types d’arborescence d’expression](types.md#expression-tree-types)). Plus précisément, l’évaluation de la conversion de fonction anonyme provoque la construction d’une structure d’objet qui représente la structure de la fonction anonyme elle-même. La structure précise de l’arborescence de l’expression, ainsi que le processus exact de création de celle-ci, sont définis par l’implémentation.

### <a name="implementation-example"></a>Exemple d'implémentation

Cette section décrit une implémentation possible de conversions de fonctions anonymes en termes C# d’autres constructions. L’implémentation décrite ici est basée sur les mêmes principes que ceux utilisés par C# le compilateur Microsoft, mais il ne s’agit pas d’une implémentation forcée, ni de la seule possibilité. Il mentionne brièvement les conversions vers les arborescences d’expressions, car leur sémantique exacte ne se situe pas dans le cadre de cette spécification.

Le reste de cette section fournit plusieurs exemples de code qui contient des fonctions anonymes avec différentes caractéristiques. Pour chaque exemple, une traduction correspondante vers du code qui utilise uniquement C# d’autres constructions est fournie. Dans les exemples, l’identificateur `D` est supposé par représenter le type délégué suivant :
```csharp
public delegate void D();
```

La forme la plus simple d’une fonction anonyme est celle qui ne capture aucune variable externe :
```csharp
class Test
{
    static void F() {
        D d = () => { Console.WriteLine("test"); };
    }
}
```

Il peut être traduit en une instanciation de délégué qui fait référence à une méthode statique générée par le compilateur dans laquelle le code de la fonction anonyme est placé :
```csharp
class Test
{
    static void F() {
        D d = new D(__Method1);
    }

    static void __Method1() {
        Console.WriteLine("test");
    }
}
```

Dans l’exemple suivant, la fonction anonyme fait référence aux membres d’instance de `this`:
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

Il peut être traduit en une méthode d’instance générée par le compilateur contenant le code de la fonction anonyme :
```csharp
class Test
{
    int x;

    void F() {
        D d = new D(__Method1);
    }

    void __Method1() {
        Console.WriteLine(x);
    }
}
```

Dans cet exemple, la fonction anonyme capture une variable locale :
```csharp
class Test
{
    void F() {
        int y = 123;
        D d = () => { Console.WriteLine(y); };
    }
}
```

La durée de vie de la variable locale doit maintenant être étendue à au moins la durée de vie du délégué de fonction anonyme. Cela peut être obtenu en « délevage » la variable locale dans un champ d’une classe générée par le compilateur. L’instanciation de la variable locale ([instanciation de variables locales](expressions.md#instantiation-of-local-variables)) correspond ensuite à la création d’une instance de la classe générée par le compilateur, et l’accès à la variable locale correspond à l’accès à un champ dans l’instance de la classe générée par le compilateur. En outre, la fonction anonyme devient une méthode d’instance de la classe générée par le compilateur :
```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.y = 123;
        D d = new D(__locals1.__Method1);
    }

    class __Locals1
    {
        public int y;

        public void __Method1() {
            Console.WriteLine(y);
        }
    }
}
```

Enfin, la fonction anonyme suivante capture `this` ainsi que deux variables locales avec des durées de vie différentes :
```csharp
class Test
{
    int x;

    void F() {
        int y = 123;
        for (int i = 0; i < 10; i++) {
            int z = i * 2;
            D d = () => { Console.WriteLine(x + y + z); };
        }
    }
}
```

Ici, une classe générée par le compilateur est créée pour chaque bloc d’instructions dans lequel les variables locales sont capturées, de sorte que les variables locales dans les différents blocs peuvent avoir des durées de vie indépendantes. Une instance de `__Locals2`, la classe générée par le compilateur pour le bloc d’instructions interne, contient la variable locale `z` et un champ qui fait référence à une instance de `__Locals1`.  Une instance de `__Locals1`, la classe générée par le compilateur pour le bloc d’instructions externe, contient la variable locale `y` et un champ qui référence `this` du membre de fonction englobant. Avec ces structures de données, il est possible d’atteindre toutes les variables externes capturées par le biais d’une instance de `__Local2`, et le code de la fonction anonyme peut donc être implémenté comme une méthode d’instance de cette classe.

```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.__this = this;
        __locals1.y = 123;
        for (int i = 0; i < 10; i++) {
            __Locals2 __locals2 = new __Locals2();
            __locals2.__locals1 = __locals1;
            __locals2.z = i * 2;
            D d = new D(__locals2.__Method1);
        }
    }

    class __Locals1
    {
        public Test __this;
        public int y;
    }

    class __Locals2
    {
        public __Locals1 __locals1;
        public int z;

        public void __Method1() {
            Console.WriteLine(__locals1.__this.x + __locals1.y + z);
        }
    }
}
```

La même technique appliquée ici pour capturer les variables locales peut également être utilisée lors de la conversion de fonctions anonymes en arborescences d’expressions : les références aux objets générés par le compilateur peuvent être stockées dans l’arborescence de l’expression, et l’accès aux variables locales peut être représentés en tant qu’accès au champ sur ces objets. L’avantage de cette approche est qu’elle permet aux variables locales « levées » d’être partagées entre les délégués et les arborescences d’expressions.

## <a name="method-group-conversions"></a>Conversions de groupe de méthodes

Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe d’un groupe de méthodes ([classifications d’expressions](expressions.md#expression-classifications)) en un type délégué compatible. Pour un type délégué `D` et une expression `E` classée comme un groupe de méthodes, une conversion implicite existe de `E` en `D` si `E` contient au moins une méthode applicable dans sa forme normale ([fonction membre applicable](expressions.md#applicable-function-member)) à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de `D`, comme décrit dans l’exemple suivant.

L’application de compilation d’une conversion d’un groupe de méthodes `E` à un type délégué `D` est décrite dans ce qui suit. Notez que l’existence d’une conversion implicite de `E` en `D` ne garantit pas que l’application de compilation de la conversion échouera sans erreur.

*  Une seule méthode `M` est sélectionnée correspondant à un appel de méthode ([appel de méthode](expressions.md#method-invocations)) de la forme `E(A)`, avec les modifications suivantes :
    * La liste d’arguments `A` est une liste d’expressions, chacune classée en tant que variable et avec le type et le modificateur (`ref` ou `out`) du paramètre correspondant dans la *formal_parameter_list* de `D`.
    * Les méthodes candidates prises en compte sont uniquement celles qui sont applicables dans leur forme normale ([fonction membre applicable](expressions.md#applicable-function-member)), et non celles qui s’appliquent uniquement dans leur forme développée.
*  Si l’algorithme d' [appels de méthode](expressions.md#method-invocations) génère une erreur, une erreur de compilation se produit. Dans le cas contraire, l’algorithme produit une seule meilleure méthode `M` avoir le même nombre de paramètres que `D` et la conversion est considérée comme existante.
*  La méthode sélectionnée `M` doit être compatible ([déléguer la compatibilité](delegates.md#delegate-compatibility)) avec le type délégué `D`ou, dans le cas contraire, une erreur de compilation se produit.
*  Si la méthode sélectionnée `M` est une méthode d’instance, l’expression d’instance associée à `E` détermine l’objet cible du délégué.
*  Si la méthode sélectionnée M est une méthode d’extension qui est indiquée au moyen d’un accès de membre sur une expression d’instance, cette expression de l’instance détermine l’objet cible du délégué.
*  Le résultat de la conversion est une valeur de type `D`, à savoir un délégué nouvellement créé qui fait référence à la méthode et à l’objet cible sélectionnés.
*  Notez que ce processus peut mener à la création d’un délégué à une méthode d’extension, si l’algorithme d' [appels de méthode](expressions.md#method-invocations) ne parvient pas à trouver une méthode d’instance mais réussit à traiter l’appel de `E(A)` en tant qu’appel de méthode d’extension ([appels de méthode d’extension](expressions.md#extension-method-invocations)). Un délégué ainsi créé capture la méthode d’extension ainsi que son premier argument.

L’exemple suivant illustre les conversions de groupe de méthodes :
```csharp
delegate string D1(object o);

delegate object D2(string s);

delegate object D3();

delegate string D4(object o, params object[] a);

delegate string D5(int i);

class Test
{
    static string F(object o) {...}

    static void G() {
        D1 d1 = F;            // Ok
        D2 d2 = F;            // Ok
        D3 d3 = F;            // Error -- not applicable
        D4 d4 = F;            // Error -- not applicable in normal form
        D5 d5 = F;            // Error -- applicable but not compatible

    }
}
```

L’assignation à `d1` convertit implicitement le groupe de méthodes `F` en une valeur de type `D1`.

L’assignation à `d2` montre comment il est possible de créer un délégué vers une méthode qui a des types de paramètres moins dérivés (contravariant) et un type de retour plus dérivé (covariant).

L’assignation à `d3` montre comment aucune conversion n’existe si la méthode n’est pas applicable.

L’assignation à `d4` montre comment la méthode doit être applicable dans sa forme normale.

L’assignation à `d5` montre comment les types de paramètres et de retour du délégué et de la méthode sont autorisés à différer uniquement pour les types référence.

Comme avec toutes les autres conversions implicites et explicites, l’opérateur de cast peut être utilisé pour effectuer explicitement une conversion de groupe de méthodes. Ainsi, l’exemple
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
à la place, il est possible d’écrire
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

Les groupes de méthodes peuvent influencer la résolution de surcharge et participer à l’inférence de type. Pour plus d’informations, consultez [membres de fonction](expressions.md#function-members) .

L’évaluation au moment de l’exécution d’une conversion de groupe de méthodes se déroule comme suit :

*  Si la méthode sélectionnée au moment de la compilation est une méthode d’instance, ou s’il s’agit d’une méthode d’extension accessible en tant que méthode d’instance, l’objet cible du délégué est déterminé à partir de l’expression d’instance associée à `E`:
    * L’expression d’instance est évaluée. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
    * Si l’expression d’instance est d’un *reference_type*, la valeur calculée par l’expression d’instance devient l’objet cible. Si la méthode sélectionnée est une méthode d’instance et que l’objet cible est `null`, un `System.NullReferenceException` est levé et aucune autre étape n’est exécutée.
    * Si l’expression d’instance est d’un *Value_type*, une opération boxing ([conversions boxing](types.md#boxing-conversions)) est effectuée pour convertir la valeur en objet, et cet objet devient l’objet cible.
*  Sinon, la méthode sélectionnée fait partie d’un appel de méthode statique, et l’objet cible du délégué est `null`.
*  Une nouvelle instance du type délégué `D` est allouée. Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
*  La nouvelle instance de délégué est initialisée avec une référence à la méthode qui a été déterminée au moment de la compilation et une référence à l’objet cible calculé ci-dessus.
