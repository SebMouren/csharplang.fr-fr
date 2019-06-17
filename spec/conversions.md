---
ms.openlocfilehash: d082393a00496b948ad4e3ff9e135d94e89d2448
ms.sourcegitcommit: 1a46441156b13db6c845f4bbb886284387d73023
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67047038"
---
# <a name="conversions"></a>Conversions

Un ***conversion*** permet à une expression à être traitée comme étant d’un type particulier. Une conversion peut provoquer une expression d’un type donné pour être considérée comme ayant un type différent, ou elle peut provoquer une expression sans un type pour lequel obtenir un type. Les conversions peuvent être ***implicite*** ou ***explicite***, ce qui détermine si un cast explicite est requis. Par exemple, la conversion de type `int` à taper `long` est implicite, c’est le cas expressions de type `int` peuvent être traitées en tant que type `long`. La conversion opposée, à partir du type `long` à taper `int`, est explicite et un cast explicite est donc requis.

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

Certaines conversions sont définies par le langage. Programmes peuvent également définir leurs propres conversions ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).

## <a name="implicit-conversions"></a>Conversions implicites

Les conversions suivantes sont classées comme les conversions implicites :

*  Conversions d’identité
*  Conversions numériques implicites
*  Conversions d’énumération implicites.
*  Conversions implicites nullables
*  Conversions de littéral NULL
*  Conversions de référence implicite
*  Conversions boxing
*  Conversions implicites dynamiques
*  Conversions d’expression constante implicites
*  Conversions implicites définies par l’utilisateur
*  Conversions de fonctions anonymes
*  Conversions de groupe (méthode)

Les conversions implicites peuvent se produire dans diverses situations, y compris les appels de fonctions membres ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions de cast ([les expressions de Cast](expressions.md#cast-expressions)), et les affectations ([opérateurs d’assignation](expressions.md#assignment-operators)).

Les conversions implicites prédéfinies réussissent toujours et ne provoquent jamais levée d’exceptions. Correctement conçu les conversions implicites définies par l’utilisateur doivent présenter ces caractéristiques ainsi.

Dans le cadre de la conversion, les types `object` et `dynamic` sont considérées comme équivalentes.

Toutefois, les conversions dynamiques ([les conversions implicites dynamiques](conversions.md#implicit-dynamic-conversions) et [les conversions explicites dynamiques](conversions.md#explicit-dynamic-conversions)) s’appliquent uniquement aux expressions de type `dynamic` ([du type dynamic](types.md#the-dynamic-type)).

### <a name="identity-conversion"></a>Conversion d’identité

Une conversion d’identité convertit à partir de n’importe quel type vers le même type. Cette conversion existe telles qu’une entité qui possède déjà un type requis peut être désignée pour être convertible vers ce type.

*  Étant donné que `object` et `dynamic` sont considérés comme équivalents, il existe une conversion d’identité entre `object` et `dynamic`et entre les types construits sont les mêmes lors du remplacement de toutes les occurrences de `dynamic` avec `object`.

### <a name="implicit-numeric-conversions"></a>Conversions numériques implicites

Les conversions numériques implicites sont :

*  À partir de `sbyte` à `short`, `int`, `long`, `float`, `double`, ou `decimal`.
*  À partir de `byte` à `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, ou `decimal`.
*  À partir de `short` à `int`, `long`, `float`, `double`, ou `decimal`.
*  À partir de `ushort` à `int`, `uint`, `long`, `ulong`, `float`, `double`, ou `decimal`.
*  À partir de `int` à `long`, `float`, `double`, ou `decimal`.
*  À partir de `uint` à `long`, `ulong`, `float`, `double`, ou `decimal`.
*  À partir de `long` à `float`, `double`, ou `decimal`.
*  À partir de `ulong` à `float`, `double`, ou `decimal`.
*  À partir de `char` à `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, ou `decimal`.
*  À partir de `float` à `double`.

Les conversions de `int`, `uint`, `long`, ou `ulong` à `float` et à partir de `long` ou `ulong` à `double` peut entraîner une perte de précision, mais ne provoqueront jamais une perte d’amplitude. Les autres conversions numériques implicites perdent jamais toutes les informations.

Aucune conversion implicite vers le `char` tapez, donc les valeurs des autres types intégraux ne convertissez pas automatiquement le `char` type.

### <a name="implicit-enumeration-conversions"></a>Conversions d’énumération implicites

Une conversion implicite d’énumération permet la *decimal_integer_literal* `0` à convertir en un *type_de_liste* et tous les *nullable_type* dont le type sous-jacent est un *type_de_liste*. Dans ce cas, la conversion est évaluée en convertissant sous-jacentes *type_de_liste* et le résultat d’habillage ([types Nullable](types.md#nullable-types)).

### <a name="implicit-interpolated-string-conversions"></a>Conversions implicites de chaîne interpolée

Implicite interpolées permis de conversion de chaîne une *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)) à convertir en `System.IFormattable` ou `System.FormattableString` (qui met en œuvre `System.IFormattable`).

Lorsque cette conversion est appliquée une valeur de chaîne n’est pas composée à partir de la chaîne interpolée. À la place une instance de `System.FormattableString` est créée, comme indiqué dans [chaînes interpolées](expressions.md#interpolated-strings).

### <a name="implicit-nullable-conversions"></a>Conversions implicites nullables

Les conversions implicites prédéfinies qui opèrent sur des types valeur non nullable peuvent également servir avec forms nullables de ces types. Pour chaque identité implicites prédéfinie et des conversions numériques convertir à partir d’un type valeur non nullable `S` à un type valeur non nullable `T`, les conversions implicites de nullables suivantes existent :

*  Une conversion implicite de `S?` à `T?`.
*  Une conversion implicite de `S` à `T?`.

Évaluation d’une conversion implicite nullable basée sur une conversion sous-jacent de `S` à `T` procède comme suit :

*  Si la conversion nullable à partir de `S?` à `T?`:
    * Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est la valeur null de type `T?`.
    * Sinon, la conversion est évaluée comme un dévoilement de `S?` à `S`, suivie de la conversion sous-jacent de `S` à `T`, suivie d’un retour à la ligne ([types Nullable](types.md#nullable-types)) à partir de `T` à `T?`.

*  Si la conversion nullable à partir de `S` à `T?`, la conversion est évaluée comme la conversion sous-jacent de `S` à `T` suivie d’un retour à la ligne à partir de `T` à `T?`.

### <a name="null-literal-conversions"></a>Conversions de littéral NULL

Il existe une conversion implicite à partir de la `null` littéral à n’importe quel type nullable. Cette conversion génère la valeur null ([types Nullable](types.md#nullable-types)) du type nullable donné.

### <a name="implicit-reference-conversions"></a>Conversions de référence implicite

Les conversions implicites de référence sont :

*  À partir d’un *reference_type* à `object` et `dynamic`.
*  À partir d’un *class_type* `S` aux *class_type* `T`, fourni `S` est dérivée de `T`.
*  À partir d’un *class_type* `S` aux *interface_type* `T`, fourni `S` implémente `T`.
*  À partir d’un *interface_type* `S` aux *interface_type* `T`, fourni `S` est dérivée de `T`.
*  À partir d’un *array_type* `S` avec un type d’élément `SE` à un *array_type* `T` avec un type d’élément `TE`, à condition que toutes les conditions suivantes sont remplies :
    * `S` et `T` diffèrent uniquement par le type d’élément. En d’autres termes, `S` et `T` ont le même nombre de dimensions.
    * Les deux `SE` et `TE` sont *reference_type*s.
    * Il existe une conversion de référence implicite à partir de `SE` à `TE`.
*  À partir d’un *array_type* à `System.Array` et les interfaces qu’il implémente.
*  À partir d’un type de tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et ses interfaces de base, autant que qu’il existe une conversion implicite de référence ou d’identité à partir de `S` à `T`.
*  À partir d’un *delegate_type* à `System.Delegate` et les interfaces qu’il implémente.
*  À partir du littéral null en un *reference_type*.
*  À partir d’un *reference_type* à un *reference_type* `T` si elle a une conversion implicite de référence ou d’identité pour un *reference_type* `T0` et `T0` a une conversion d’identité à `T`.
*  À partir d’un *reference_type* à un type d’interface ou un délégué `T` si elle a une conversion implicite de référence ou d’identité à un type d’interface ou un délégué `T0` et `T0` est convertible en variance ([ Conversion de variance](interfaces.md#variance-conversion)) à `T`.
*  Conversions implicites impliquant des paramètres de type qui sont connus pour être des types référence. Consultez [conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters) pour plus d’informations sur les conversions implicites impliquant des paramètres de type.

Les conversions de référence implicites sont les conversions entre *reference_type*s qui peut être prouvé aboutissent toujours et ne nécessitent donc aucune vérification au moment de l’exécution.

Conversions de référence, implicites ou explicites, jamais changent l’identité référentielle de l’objet en cours de conversion. En d’autres termes, si une conversion de référence peut modifier le type de la référence, il ne change jamais le type ou la valeur de l’objet fait référence.

### <a name="boxing-conversions"></a>Conversions boxing

Une conversion boxing permet un *value_type* être converti implicitement en un type référence. Il existe une conversion boxing de n’importe quel *non_nullable_value_type* à `object` et `dynamic`, à `System.ValueType` et tous les *interface_type* implémentée par le *non_ nullable_value_type*. En outre un *type_de_liste* peut être converti au type `System.Enum`.

Une conversion boxing existe à partir d’un *nullable_type* à un type référence, si et seulement si une conversion boxing existe sous-jacent *non_nullable_value_type* pour le type de référence.

Un type valeur a une conversion boxing en un type interface `I` si elle a une conversion boxing en un type interface `I0` et `I0` a une conversion d’identité à `I`.

Un type valeur a une conversion boxing en un type interface `I` si elle a une conversion boxing à un type d’interface ou un délégué `I0` et `I0` est convertible en variance ([la conversion de Variance](interfaces.md#variance-conversion)) à `I`.

Conversion boxing d’une valeur un *non_nullable_value_type* se compose d’allouer une instance d’objet et à copier le *value_type* valeur dans cette instance. Un struct peut être converti au type `System.ValueType`, puisque c’est une classe de base pour tous les structs ([héritage](structs.md#inheritance)).

Conversion boxing d’une valeur un *nullable_type* procède comme suit :

*  Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est une référence null du type cible.
*  Sinon, le résultat est une référence à un boxed `T` produites par le désencapsulage et le boxing de la valeur source.

Les conversions boxing sont décrites dans [conversions Boxing](types.md#boxing-conversions).

### <a name="implicit-dynamic-conversions"></a>Conversions implicites dynamiques

Il existe une conversion implicite de dynamique à partir d’une expression de type `dynamic` à n’importe quel type `T`. La conversion est dynamiquement liée ([liaison dynamique](expressions.md#dynamic-binding)), ce qui signifie qu’une conversion implicite vous est demandée au moment de l’exécution à partir du type d’exécution de l’expression à `T`. Si aucune conversion n’est trouvée, une exception au moment de l’exécution est levée.

Notez que cette conversion implicite a apparemment enfreint le Conseil au début de [conversions implicites](conversions.md#implicit-conversions) qu’une conversion implicite ne doit jamais lever une exception. Il n’est toutefois pas la conversion elle-même, mais le *recherche* de la conversion qui provoque l’exception. Le risque d’exceptions d’exécution est inhérent à l’utilisation de la liaison dynamique. Si la liaison dynamique de la conversion n’est pas souhaitée, l’expression peut être convertie tout d’abord à `object`et ensuite vers le type souhaité.

L’exemple suivant illustre les conversions implicites dynamiques :

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

Les affectations à `s2` et `i` les deux utilisent des conversions implicites dynamiques, dans laquelle la liaison des opérations est interrompue avant l’exécution. Au moment de l’exécution, les conversions implicites sont recherchées à partir du type d’exécution de `d`  --  `string` --vers le type cible. Une conversion a été `string` , mais pas au `int`.

### <a name="implicit-constant-expression-conversions"></a>Conversions d’expression constante implicites

Une conversion d’expression constante implicite permet les conversions suivantes :

*  Un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) de type `int` peut être converti en type `sbyte`, `byte`, `short`, `ushort`, `uint`, ou `ulong`, fourni la valeur de la *constant_expression* se trouve dans la plage du type de destination.
*  Un *constant_expression* de type `long` peut être converti en type `ulong`, fourni la valeur de la *constant_expression* ne soit pas négative.

### <a name="implicit-conversions-involving-type-parameters"></a>Conversions implicites impliquant des paramètres de type

Les conversions implicites suivantes existent pour un paramètre de type donné `T`:

*  À partir de `T` à sa classe de base efficace `C`, à partir de `T` à n’importe quelle classe de base de `C`et à partir de `T` à n’importe quelle interface implémentée par `C`. Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion boxing. Sinon, la conversion est exécutée en tant qu’une conversion de référence implicite ou la conversion d’identité.
*  À partir de `T` en un type interface `I` dans `T`ensemble interface efficace et à partir de `T` à n’importe quelle interface de base de `I`. Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion boxing. Sinon, la conversion est exécutée en tant qu’une conversion de référence implicite ou la conversion d’identité.
*  À partir de `T` à un paramètre de type `U`, fourni `T` dépend `U` ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)). Au moment de l’exécution, if `U` est un type valeur, puis `T` et `U` sont nécessairement du même type et aucune conversion n’est effectuée. Sinon, si `T` est un type valeur, la conversion est exécutée comme une conversion boxing. Sinon, la conversion est exécutée en tant qu’une conversion de référence implicite ou la conversion d’identité.
*  À partir du littéral null en `T`, fourni `T` est connu pour être un type référence.
*  À partir de `T` à un type référence `I` si elle a une conversion implicite en un type référence `S0` et `S0` a une conversion d’identité à `S`. Au moment de l’exécution, la conversion est exécutée la même façon que la conversion en `S0`.
*  À partir de `T` en un type interface `I` si elle a une conversion implicite en une interface ou un type délégué `I0` et `I0` est convertible en variance à `I` ([la conversion de Variance](interfaces.md#variance-conversion) ). Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion boxing. Sinon, la conversion est exécutée en tant qu’une conversion de référence implicite ou la conversion d’identité.

Si `T` est connu pour être un type référence ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)), les conversions ci-dessus sont classées comme des conversions de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)). Si `T` est ne pas connu pour être un type référence, les conversions ci-dessus sont classées comme des conversions boxing ([conversions Boxing](conversions.md#boxing-conversions)).

### <a name="user-defined-implicit-conversions"></a>Conversions implicites définies par l’utilisateur

Une conversion implicite défini par l’utilisateur se compose d’une conversion implicite standard facultative, suivie de l’exécution d’un opérateur de conversion implicite définie par l’utilisateur, suivie d’une autre conversion implicite standard facultative. Les règles exactes d’évaluation des conversions implicites définies par l’utilisateur sont décrites dans [le traitement de conversions implicites définies par l’utilisateur](conversions.md#processing-of-user-defined-implicit-conversions).

### <a name="anonymous-function-conversions-and-method-group-conversions"></a>Conversions de fonctions anonymes et les conversions de groupe (méthode)

Fonctions anonymes et les groupes de méthode et ne sont pas types dans leur propre, mais peuvent être implicitement convertis pour déléguer des types ou des types arborescence d’expression. Conversions de fonctions anonymes sont décrits plus en détail dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions) méthode conversions et groupe dans [conversions de groupes de méthode](conversions.md#method-group-conversions).

## <a name="explicit-conversions"></a>Conversions explicites

Les conversions suivantes sont classées comme des conversions explicites :

*  Toutes les conversions implicites.
*  Conversions numériques explicites.
*  Conversions d’énumération explicites.
*  Conversions explicites nullables.
*  Conversions de référence explicite.
*  Conversions d’interface explicite.
*  Conversions unboxing.
*  Conversions explicites dynamiques
*  Conversions explicites définies par l’utilisateur.

Les conversions explicites peuvent se produire dans les expressions de cast ([les expressions de Cast](expressions.md#cast-expressions)).

Le jeu de conversions explicites inclut toutes les conversions implicites. Cela signifie que les expressions cast redondantes sont autorisées.

Les conversions explicites qui ne sont pas les conversions implicites sont les conversions qui ne peut pas être prouvées aboutissent toujours, les conversions qui sont connues peuvent entraîner une perte d’informations et les conversions entre des domaines de types suffisamment différents pour mériter explicite notation.

### <a name="explicit-numeric-conversions"></a>Conversions numériques explicites

Conversions numériques explicites sont les conversions à partir d’un *numeric_type* vers un autre *numeric_type* pour lequel une conversion numérique implicite ([lesconversionsnumériquesimplicites](conversions.md#implicit-numeric-conversions)) n’existe pas déjà :

*  À partir de `sbyte` à `byte`, `ushort`, `uint`, `ulong`, ou `char`.
*  À partir de `byte` à `sbyte` et `char`.
*  À partir de `short` à `sbyte`, `byte`, `ushort`, `uint`, `ulong`, ou `char`.
*  À partir de `ushort` à `sbyte`, `byte`, `short`, ou `char`.
*  À partir de `int` à `sbyte`, `byte`, `short`, `ushort`, `uint`, `ulong`, ou `char`.
*  À partir de `uint` à `sbyte`, `byte`, `short`, `ushort`, `int`, ou `char`.
*  À partir de `long` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `ulong`, ou `char`.
*  À partir de `ulong` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, ou `char`.
*  À partir de `char` à `sbyte`, `byte`, ou `short`.
*  À partir de `float` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, ou `decimal`.
*  À partir de `double` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, ou `decimal`.
*  À partir de `decimal` à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, ou `double`.

Étant donné que les conversions explicites incluent toutes les conversions numériques implicites et explicites, il est toujours possible de convertir à partir d’un *numeric_type* n’importe quel autre *numeric_type* à l’aide d’une expression de cast () [Les expressions de cast](expressions.md#cast-expressions)).

Conversions numériques explicites peuvent entraîner une perte d’informations ou éventuellement causer la levée d’exceptions. Une conversion numérique explicite est traitée comme suit :

*  Pour une conversion à partir d’un type intégral en un autre type intégral, le traitement varie selon le contexte de vérification de dépassement de capacité ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)) dans ce qui amène la conversion placer :
    * Dans un `checked` contexte, la conversion réussit si la valeur de l’opérande source se trouve dans la plage du type de destination, mais lève une `System.OverflowException` si la valeur de l’opérande source est en dehors de la plage du type de destination.
    * Dans un `unchecked` contexte, la conversion réussit toujours et procède comme suit.
        * Si le type source est plus grand que le type de destination, la valeur source est tronquée via l’abandon de ses bits « supplémentaires » les plus significatifs. Le résultat est ensuite traité en tant que valeur du type de destination.
        * Si le type source est plus petit que le type de destination, la valeur source fait l’objet d’une extension de signe ou d’une extension de zéros, pour avoir la même taille que le type de destination. L’extension de signe est utilisée si le type source est signé. L’extension de zéro est utilisée si le type source est non signé. Le résultat est ensuite traité en tant que valeur du type de destination.
        * Si le type source a la même taille que le type de destination, la valeur source est traitée comme une valeur du type de destination.
*  Pour une conversion de `decimal` vers un type intégral, la valeur source est arrondie vers zéro à la valeur intégrale la plus proche, et cette valeur intégrale devient le résultat de la conversion. Si la valeur intégrale obtenue est en dehors de la plage du type de destination, un `System.OverflowException` est levée.
*  Pour une conversion à partir de `float` ou `double` vers un type intégral, le traitement varie selon le contexte de vérification de dépassement de capacité ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)) dans ce qui amène la conversion placer :
    * Dans un `checked` contexte, la conversion se déroule comme suit :
        * Si la valeur de l’opérande est NaN ou infinie, une `System.OverflowException` est levée.
        * Sinon, l’opérande source est arrondie vers zéro à la valeur intégrale la plus proche. Si cette valeur intégrale est dans la plage du type de destination de cette valeur est le résultat de la conversion.
        * Sinon, une exception `System.OverflowException` est levée.
    * Dans un `unchecked` contexte, la conversion réussit toujours et procède comme suit.
        * Si la valeur de l’opérande est NaN ou infinie, le résultat de la conversion est une valeur non spécifiée du type de destination.
        * Sinon, l’opérande source est arrondie vers zéro à la valeur intégrale la plus proche. Si cette valeur intégrale est dans la plage du type de destination de cette valeur est le résultat de la conversion.
        * Sinon, le résultat de la conversion est une valeur non spécifiée du type de destination.
*  Pour une conversion à partir de `double` à `float`, le `double` valeur est arrondie à la plus proche `float` valeur. Si le `double` valeur est trop petite pour représenter un `float`, le résultat devient le zéro positif ou zéro négatif. Si le `double` valeur est trop grand pour être représenté comme un `float`, le résultat devient l’infini positif ou l’infini négatif. Si le `double` valeur est NaN, le résultat est également NaN.
*  Pour une conversion à partir de `float` ou `double` à `decimal`, la valeur source est convertie en `decimal` représentation et arrondie au nombre le plus proche après la 28ème décimale, si nécessaire ([type décimal](types.md#the-decimal-type)). Si la valeur source est trop petite pour représenter un `decimal`, le résultat est zéro. Si la valeur source est NaN, infini, ou il est trop grand pour être représenté comme un `decimal`, un `System.OverflowException` est levée.
*  Pour une conversion à partir de `decimal` à `float` ou `double`, le `decimal` valeur est arrondie à la plus proche `double` ou `float` valeur. Bien que cette conversion peut-être manquer de précision, elle ne génère jamais une exception levée.

### <a name="explicit-enumeration-conversions"></a>Conversions d’énumération explicites

Les conversions d’énumération explicite sont :

*  À partir de `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, ou `decimal` à n’importe quel *type_de_liste*.
*  À partir d’un *type_de_liste* à `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, ou `decimal`.
*  À partir d’un *type_de_liste* n’importe quel autre *type_de_liste*.

Une conversion d’énumération explicite entre deux types est traitée en considérant tout participant *type_de_liste* en tant que le type sous-jacent de qui *type_de_liste*, puis en effectuant une implicite ou explicite conversion numérique entre les types résultants. Par exemple, étant donné un *type_de_liste* `E` avec et le type sous-jacent de `int`, une conversion de `E` à `byte` est traité comme une conversion numérique explicite ([explicite conversions numériques](conversions.md#explicit-numeric-conversions)) à partir de `int` à `byte`et une conversion à partir de `byte` à `E` est traité comme une conversion numérique implicite ([lesconversionsnumériquesimplicites](conversions.md#implicit-numeric-conversions)) à partir de `byte` à `int`.

### <a name="explicit-nullable-conversions"></a>Conversions explicites nullables

***Les conversions explicites nullables*** permis prédéfinis des conversions explicites qui fonctionnent sur des types valeur non nullable à utiliser avec les formulaires nullables de ces types. Pour chacun des conversions explicites prédéfinies convertir à partir d’un type valeur non nullable `S` à un type valeur non nullable `T` ([conversion d’identité](conversions.md#identity-conversion), [lesconversionsnumériquesimplicites](conversions.md#implicit-numeric-conversions), [Conversions d’énumération implicites](conversions.md#implicit-enumeration-conversions), [conversions numériques explicites](conversions.md#explicit-numeric-conversions), et [conversions d’énumération explicites](conversions.md#explicit-enumeration-conversions)), ce qui suit Il existe des conversions nullables :

*  Une conversion explicite de `S?` à `T?`.
*  Une conversion explicite de `S` à `T?`.
*  Une conversion explicite de `S?` à `T`.

Évaluation d’une conversion nullable basée sur une conversion sous-jacent de `S` à `T` procède comme suit :

*  Si la conversion nullable à partir de `S?` à `T?`:
    * Si la valeur source est null (`HasValue` propriété a la valeur false), le résultat est la valeur null de type `T?`.
    * Sinon, la conversion est évaluée comme un dévoilement de `S?` à `S`, suivie de la conversion sous-jacent de `S` à `T`, suivie d’un retour à la ligne à partir de `T` à `T?`.
*  Si la conversion nullable à partir de `S` à `T?`, la conversion est évaluée comme la conversion sous-jacent de `S` à `T` suivie d’un retour à la ligne à partir de `T` à `T?`.
*  Si la conversion nullable à partir de `S?` à `T`, la conversion est évaluée comme un dévoilement de `S?` à `S` suivie de la conversion sous-jacent de `S` à `T`.

Notez que toute tentative pour désencapsuler une valeur nullable lève une exception si la valeur est `null`.

### <a name="explicit-reference-conversions"></a>Conversions de référence explicite

Les conversions de référence explicite sont :

*  À partir de `object` et `dynamic` n’importe quel autre *reference_type*.
*  À partir d’un *class_type* `S` aux *class_type* `T`, fourni `S` est une classe de base `T`.
*  À partir d’un *class_type* `S` aux *interface_type* `T`, fourni `S` n’est pas scellé et fourni `S` n’implémente pas `T`.
*  À partir d’un *interface_type* `S` aux *class_type* `T`, fourni `T` n’est pas scellé ou fourni `T` implémente `S`.
*  À partir d’un *interface_type* `S` aux *interface_type* `T`, fourni `S` n’est pas dérivé `T`.
*  À partir d’un *array_type* `S` avec un type d’élément `SE` à un *array_type* `T` avec un type d’élément `TE`, à condition que toutes les conditions suivantes sont remplies :
    * `S` et `T` diffèrent uniquement par le type d’élément. En d’autres termes, `S` et `T` ont le même nombre de dimensions.
    * Les deux `SE` et `TE` sont *reference_type*s.
    * Il existe une conversion de référence explicite à partir de `SE` à `TE`.
*  À partir de `System.Array` et les interfaces qu’il implémente à aucun *array_type*.
*  À partir d’un type de tableau unidimensionnel `S[]` à `System.Collections.Generic.IList<T>` et ses interfaces de base fournis qu’il existe une conversion de référence explicite à partir de `S` à `T`.
*  À partir de `System.Collections.Generic.IList<S>` et sa base d’interfaces pour un type de tableau unidimensionnel `T[]`, à condition que qu’il existe une conversion de référence ou d’identité explicite à partir de `S` à `T`.
*  À partir de `System.Delegate` et les interfaces qu’il implémente à aucun *delegate_type*.
*  À partir d’un type référence à un type référence `T` si elle a une conversion de référence explicite à un type référence `T0` et `T0` a une conversion d’identité `T`.
*  À partir d’un type référence à un type d’interface ou un délégué `T` si elle a une conversion de référence explicite à un type d’interface ou un délégué `T0` et `T0` est convertible en variance à `T` ou `T` est variance-convertible en `T0` ([la conversion de Variance](interfaces.md#variance-conversion)).
*  À partir de `D<S1...Sn>` à `D<T1...Tn>` où `D<X1...Xn>` est un type délégué générique, `D<S1...Sn>` n’est pas compatible avec ou identique à `D<T1...Tn>`et pour chaque paramètre de type `Xi` de `D` les blocages suivantes :
    * Si `Xi` est invariant, puis `Si` est identique à `Ti`.
    * Si `Xi` est covariant, il existe une conversion de référence ou d’identité implicite ou explicite de `Si` à `Ti`.
    * Si `Xi` est contravariant, puis `Si` et `Ti` sont identiques ou les deux types de référence.
*  Conversions explicites impliquant des paramètres de type qui sont connus pour être des types référence. Pour plus d’informations sur les conversions explicites impliquant des paramètres de type, consultez [conversions explicites impliquant des paramètres de type](conversions.md#explicit-conversions-involving-type-parameters).

Les conversions de référence explicites sont les conversions entre des types référence qui nécessitent des vérifications à l’exécution pour vous assurer qu’ils sont corrects.

Pour une conversion de référence explicite réussisse au moment de l’exécution, la valeur de l’opérande source doit être `null`, ou le type réel de l’objet référencé par l’opérande source doit être un type qui peut être converti vers le type de destination par une référence implicite conversion ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) ou une conversion boxing ([conversions Boxing](conversions.md#boxing-conversions)). Si une conversion de référence explicite échoue, un `System.InvalidCastException` est levée.

Conversions de référence, implicites ou explicites, jamais changent l’identité référentielle de l’objet en cours de conversion. En d’autres termes, si une conversion de référence peut modifier le type de la référence, il ne change jamais le type ou la valeur de l’objet fait référence.

### <a name="unboxing-conversions"></a>Conversions unboxing

Une conversion unboxing permet à un type référence pour être converti explicitement en un *value_type*. Il existe une conversion unboxing à partir des types `object`, `dynamic` et `System.ValueType` aux *non_nullable_value_type*et à partir d’un *interface_type* aux *non_ nullable_value_type* qui implémente le *interface_type*. En outre tapez `System.Enum` peuvent être unboxed aux *type_de_liste*.

Il existe une conversion unboxing d’un type référence en un *nullable_type* s’il existe une conversion unboxing du type de référence en sous-jacent *non_nullable_value_type* de la  *nullable_type*.

Un type valeur `S` a une conversion unboxing d’un type interface `I` si elle a une conversion unboxing d’un type interface `I0` et `I0` a une conversion d’identité à `I`.

Un type valeur `S` a une conversion unboxing d’un type interface `I` si elle a une conversion unboxing à partir d’une interface ou un type délégué `I0` et `I0` est convertible en variance à `I` ou `I`est convertible en variance à `I0` ([la conversion de Variance](interfaces.md#variance-conversion)).

Une opération d’unboxing se compose de vérifier au préalable que l’instance d’objet est une valeur boxed de la donnée *value_type*, puis copiez la valeur en dehors de l’instance. Unboxing d’une référence null à un *nullable_type* produit la valeur null de la *nullable_type*. Un struct peut être unboxed à partir du type `System.ValueType`, puisque c’est une classe de base pour tous les structs ([héritage](structs.md#inheritance)).

Les conversions unboxing sont décrites dans [les conversions Unboxing](types.md#unboxing-conversions).

### <a name="explicit-dynamic-conversions"></a>Conversions explicites dynamiques

Il existe une conversion explicite de dynamique à partir d’une expression de type `dynamic` à n’importe quel type `T`. La conversion est dynamiquement liée ([liaison dynamique](expressions.md#dynamic-binding)), ce qui signifie qu’une conversion explicite vous est demandée au moment de l’exécution à partir du type d’exécution de l’expression à `T`. Si aucune conversion n’est trouvée, une exception au moment de l’exécution est levée.

Si la liaison dynamique de la conversion n’est pas souhaitée, l’expression peut être convertie tout d’abord à `object`et ensuite vers le type souhaité.

Supposons que la classe suivante est définie :
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

L’exemple suivant illustre les conversions explicites dynamiques :
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

La meilleure conversion de `o` à `C` est trouvé à la compilation pour permettre une conversion de référence explicite. Cette opération échoue au moment de l’exécution, car `"1"` n’est pas en fait un `C`. La conversion de `d` à `C` Toutefois, comme une conversion explicite dynamique, est suspendue au moment de l’exécution, où un utilisateur défini la conversion du type d’exécution de `d`  --  `string` --à `C` est trouvée, et réussit.

### <a name="explicit-conversions-involving-type-parameters"></a>Conversions explicites impliquant des paramètres de type

Les conversions explicites suivantes existent pour un paramètre de type donné `T`:

*  À partir de la classe de base efficace `C` de `T` à `T` et à partir de n’importe quelle classe de base de `C` à `T`. Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion unboxing. Sinon, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  À partir de n’importe quel type d’interface pour `T`. Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion unboxing. Sinon, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  À partir de `T` aux *interface_type* `I` fournie il n’est pas déjà une conversion implicite de `T` à `I`. Au moment de l’exécution, if `T` est un type valeur, la conversion est exécutée comme une conversion boxing suivie d’une conversion de référence explicite. Sinon, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.
*  À partir d’un paramètre de type `U` à `T`, fourni `T` dépend `U` ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)). Au moment de l’exécution, if `U` est un type valeur, puis `T` et `U` sont nécessairement du même type et aucune conversion n’est effectuée. Sinon, si `T` est un type valeur, la conversion est exécutée comme une conversion unboxing. Sinon, la conversion est exécutée en tant que conversion de référence explicite ou conversion d’identité.

Si `T` est connu pour être un type référence, les conversions ci-dessus sont toutes classées comme des conversions de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)). Si `T` est ne pas connu pour être un type référence, les conversions ci-dessus sont classées comme des conversions unboxing ([les conversions Unboxing](conversions.md#unboxing-conversions)).

Les règles ci-dessus ne permettent pas d’une conversion explicite directe à partir d’un paramètre de type sans contrainte à un type sans interface, ce qui peut être surprenant. La raison de cette règle consiste à éviter toute confusion et rendre la sémantique de ces conversions clair. Observez par exemple la déclaration suivante :
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

Si la conversion explicite directe de `t` à `int` étaient autorisées, on peut facilement s’attendre à qui `X<int>.F(7)` retournerait `7L`. Toutefois, il le ferait pas, car les conversions numériques standard sont considérées comme uniquement lorsque les types sont connus comme étant numériques au moment de la liaison. Afin de rendre la sémantique clair, l’exemple ci-dessus doit plutôt être écrit :
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

Ce code compilera maintenant en cours d’exécution mais `X<int>.F(7)` puis lève une exception au moment de l’exécution, depuis un boxed `int` ne peut pas être converti directement un `long`.

### <a name="user-defined-explicit-conversions"></a>Conversions explicites définies par l’utilisateur

Une conversion explicite définie par l’utilisateur se compose d’une conversion explicite standard facultative, suivie de l’exécution d’un opérateur de conversion implicite ou explicite défini par l’utilisateur, suivie d’une autre conversion explicite standard facultative. Les règles exactes d’évaluation des conversions explicites définies par l’utilisateur sont décrites dans [le traitement de conversions explicites définies par l’utilisateur](conversions.md#processing-of-user-defined-explicit-conversions).

## <a name="standard-conversions"></a>Conversions standard

Les conversions standard sont les conversions prédéfinies qui peuvent se produire dans le cadre d’une conversion définie par l’utilisateur.

### <a name="standard-implicit-conversions"></a>Conversions implicites standards

Les conversions implicites suivantes sont classées comme des conversions implicites standards :

*  Conversions d’identité ([conversion d’identité](conversions.md#identity-conversion))
*  Conversions numériques implicites ([conversions numériques implicites](conversions.md#implicit-numeric-conversions))
*  Les conversions implicites nullables ([les conversions implicites nullables](conversions.md#implicit-nullable-conversions))
*  Conversions de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions))
*  Les conversions boxing ([conversions Boxing](conversions.md#boxing-conversions))
*  Conversions d’expression constante implicites ([les conversions implicites dynamiques](conversions.md#implicit-dynamic-conversions))
*  Les conversions implicites impliquant des paramètres de type ([conversions implicites impliquant des paramètres de type](conversions.md#implicit-conversions-involving-type-parameters))

Les conversions implicites standards exclure spécifiquement les conversions implicites définies par l’utilisateur.

### <a name="standard-explicit-conversions"></a>Conversions explicites standards

Les conversions explicites standard sont toutes les conversions implicites standards ainsi que le sous-ensemble des conversions explicites pour lequel il existe une conversion implicite standard opposée. En d’autres termes, si une norme implicite conversion existe à partir d’un type `A` à un type `B`, puis il existe une conversion explicite standard du type `A` en type `B` et du type `B` au type `A`.

## <a name="user-defined-conversions"></a>Conversions définies par l’utilisateur

C# permet les conversions implicites et explicites prédéfinies être complété par ***conversions définies par l’utilisateur***. Conversions définies par l’utilisateur sont introduites en déclarant des opérateurs de conversion ([les opérateurs de Conversion](classes.md#conversion-operators)) dans la classe et les types struct.

### <a name="permitted-user-defined-conversions"></a>Autorisé des conversions définies par l’utilisateur

C# autorise uniquement certaines conversions définies par l’utilisateur à être déclarés. En particulier, il n’est pas possible de redéfinir une conversion implicite ou explicite déjà existante.

Pour un type de source donné `S` et type de cible `T`si `S` ou `T` sont des types nullables, permettent de `S0` et `T0` font référence à leurs types sous-jacents, sinon `S0` et `T0` sont égal à `S` et `T` respectivement. Une classe ou un struct est autorisé à déclarer une conversion à partir d’un type de source `S` à un type de cible `T` uniquement si toutes les conditions suivantes sont remplies :

*  `S0` et `T0` sont les différents types.
*  Soit `S0` ou `T0` est le type de classe ou struct dans lequel la déclaration d’opérateur a lieu.
*  Ni `S0` ni `T0` est un *interface_type*.
*  À l’exception des conversions définies par l’utilisateur, une conversion n’existe pas de `S` à `T` ou à partir de `T` à `S`.

Les restrictions qui s’appliquent aux conversions définies par l’utilisateur sont décrites plus en détail dans [les opérateurs de Conversion](classes.md#conversion-operators).

### <a name="lifted-conversion-operators"></a>Opérateurs de conversion levé

Étant donné un opérateur de conversion définie par l’utilisateur qui convertit un type valeur non nullable `S` à un type valeur non nullable `T`, un ***levé d’opérateur de conversion*** existe qui convertit à partir de `S?` à `T?`. Cet opérateur de conversion levé effectue un Désencapsulation à partir de `S?` à `S` suivie de la conversion définie par l’utilisateur à partir de `S` à `T` suivie d’un retour à la ligne à partir de `T` à `T?`, sauf qu’une valeur null valeurs `S?` convertit directement à une valeur null à valeur `T?`.

Un opérateur de conversion levé a le même classement implicite ou explicit en tant que son opérateur de conversion définie par l’utilisateur sous-jacent. Le terme « conversion définie par l’utilisateur » s’applique à l’utilisation des deux défini par l’utilisateur et levée des opérateurs de conversion.

### <a name="evaluation-of-user-defined-conversions"></a>Évaluation des conversions définies par l’utilisateur

Une conversion définie par l’utilisateur convertit une valeur de son type, appelé le ***type de source***, à un autre type, appelé le ***type cible***. Évaluation d’une conversion définie par l’utilisateur se concentre sur la recherche le ***plus spécifique*** opérateur de conversion définie par l’utilisateur pour les types source et cible particuliers. Cette détermination est divisée en plusieurs étapes :

*  Recherche de l’ensemble des classes et des structs qui seront considérée comme des opérateurs de conversion définie par l’utilisateur. Cet ensemble comprend le type de source et de ses classes de base et le type de cible et ses classes de base (avec les hypothèses implicites que seules les classes et structs peuvent déclarer des opérateurs définis par l’utilisateur, et ne qu’aucune classe de base sur les types sans classe). Dans le cadre de cette étape, si le type de source ou cible est un *nullable_type*, leur type sous-jacent est utilisé à la place.
*  À partir de cet ensemble de types, la détermination de qui défini par l’utilisateur et levée des opérateurs de conversion sont applicables. Pour un opérateur de conversion soit applicable, il doit être possible d’effectuer une conversion standard ([conversions Standard](conversions.md#standard-conversions)) à partir du type de source à l’opérande de type de l’opérateur et il doit être possible d’effectuer une conversion standard à partir du type de résultat de l’opérateur vers le type cible.
*  À partir de l’ensemble des opérateurs définis par l’utilisateur applicables, déterminer quel opérateur est sans ambiguïté le plus spécifique. En règle générale, l’opérateur plus spécifique est l’opérateur dont le type d’opérande est « plus proche » pour le type de source et dont le type de résultat est « plus proche » en type cible. Opérateurs de conversion définie par l’utilisateur sont préférés aux opérateurs de conversion levé. Les règles exactes pour l’établissement de l’opérateur de conversion définie par l’utilisateur plus spécifique sont définies dans les sections suivantes.

Une fois qu’un opérateur de conversion définie par l’utilisateur plus spécifique a été identifié, l’exécution réelle de la conversion définie par l’utilisateur implique trois étapes :

*  Tout d’abord, si nécessaire, effectuer une conversion standard du type source vers le type d’opérande de l’opérateur de conversion défini par l’utilisateur ou levé.
*  Ensuite, l’appel de l’opérateur de conversion défini par l’utilisateur ou levé pour effectuer la conversion.
*  Enfin, si nécessaire, effectuer une conversion standard du type de résultat de l’opérateur de conversion défini par l’utilisateur ou levé vers le type cible.

Évaluation d’une conversion définie par l’utilisateur jamais implique plusieurs opérateurs de conversion définie par l’utilisateur ou levé. En d’autres termes, une conversion de type `S` à taper `T` jamais commence par exécuter une conversion définie par l’utilisateur à partir de `S` à `X` , puis exécutez une conversion définie par l’utilisateur à partir de `X` à `T`.

Les définitions exactes d’évaluation des conversions implicites ou explicites définies par l’utilisateur sont données dans les sections suivantes. Les définitions utilisent les termes suivants :

*  Si une conversion implicite standard ([conversions implicites Standard](conversions.md#standard-implicit-conversions)) existe à partir d’un type `A` à un type `B`et si ni `A` ni `B` sont *interface_type*s, puis `A` est dite ***englobé par*** `B`, et `B` est dite ***englober*** `A`.
*  Le ***type le plus englobant*** dans un ensemble de types est celui qui englobe tous les autres types dans le jeu. Si aucun type individuel n’englobe tous les autres types, le jeu n’a aucun type le plus englobant. En termes plus intuitives, le type le plus englobant est le « plus grand » type dans le jeu, le type auquel chacun des autres types peut être converti implicitement.
*  Le ***type le plus englobé*** dans un ensemble de types est celui qui est englobé par tous les autres types dans le jeu. Si aucun type unique n’est englobé par tous les autres types, puis le jeu a ne type le plus englobé. En termes plus intuitives, le type le plus englobé est le « plus petit » type dans le jeu, le type qui peut être converti implicitement à chacun des autres types.

### <a name="processing-of-user-defined-implicit-conversions"></a>Traitement des conversions implicites définies par l’utilisateur

Une conversion implicite défini par l’utilisateur à partir du type `S` à taper `T` est traité comme suit :

*  Déterminer les types `S0` et `T0`. Si `S` ou `T` sont des types nullables, `S0` et `T0` sont leurs types sous-jacents, sinon `S0` et `T0` sont égaux à `S` et `T` respectivement.
*  Trouver l’ensemble de types, `D`, à partir de la conversion définie par l’utilisateur sont considérés comme opérateurs. Ce groupe se compose de `S0` (si `S0` est une classe ou struct), les classes de base de `S0` (si `S0` est une classe), et `T0` (si `T0` est une classe ou struct).
*  Rechercher l’ensemble des opérateurs de conversion définie par l’utilisateur et levé applicable, `U`. Ce groupe se compose des opérateurs de conversion implicite défini par l’utilisateur et levé déclarés par les classes ou les structs dans `D` qui convertir à partir d’un type englobant `S` à un type englobé par `T`. Si `U` est vide, la conversion n’est pas définie et une erreur de compilation se produit.
*  Rechercher le type de source plus spécifique, `SX`, des opérateurs dans `U`:
    * Si un des opérateurs dans `U` convertir à partir de `S`, puis `SX` est `S`.
    * Sinon, `SX` est le type le plus englobé dans l’ensemble combiné des types source des opérateurs dans `U`. Si seul le plus englobé type ne peut pas être trouvé, puis la conversion est ambiguë et une erreur de compilation se produit.
*  Rechercher le type de cible plus spécifique, `TX`, des opérateurs dans `U`:
    * Si un des opérateurs dans `U` convertir `T`, puis `TX` est `T`.
    * Sinon, `TX` est le type le plus englobant dans l’ensemble combiné des types cible des opérateurs dans `U`. Si exactement un type plus englobant ne peut pas être trouvé, la conversion est ambiguë et une erreur de compilation se produit.
*  Rechercher l’opérateur de conversion plus spécifique :
    * Si `U` contient exactement un opérateur de conversion définie par l’utilisateur qui effectue la conversion à partir de `SX` à `TX`, il s’agit de l’opérateur de conversion plus spécifique.
    * Sinon, si `U` contient exactement un opérateur de conversion levé convertit à partir de `SX` à `TX`, il s’agit de l’opérateur de conversion plus spécifique.
    * Sinon, la conversion est ambiguë et une erreur de compilation se produit.
*  Enfin, appliquez la conversion :
    * Si `S` n’est pas `SX`, puis une conversion implicite standard de `S` à `SX` est effectuée.
    * L’opérateur de conversion plus spécifique est appelée pour convertir à partir de `SX` à `TX`.
    * Si `TX` n’est pas `T`, puis une conversion implicite standard de `TX` à `T` est effectuée.

### <a name="processing-of-user-defined-explicit-conversions"></a>Traitement des conversions explicites définies par l’utilisateur

Une conversion explicite définie par l’utilisateur à partir du type `S` à taper `T` est traité comme suit :

*  Déterminer les types `S0` et `T0`. Si `S` ou `T` sont des types nullables, `S0` et `T0` sont leurs types sous-jacents, sinon `S0` et `T0` sont égaux à `S` et `T` respectivement.
*  Trouver l’ensemble de types, `D`, à partir de la conversion définie par l’utilisateur sont considérés comme opérateurs. Ce groupe se compose de `S0` (si `S0` est une classe ou struct), les classes de base de `S0` (si `S0` est une classe), `T0` (si `T0` est une classe ou struct) et les classes de base de `T0` (si `T0`est une classe).
*  Rechercher l’ensemble des opérateurs de conversion définie par l’utilisateur et levé applicable, `U`. Cet ensemble comprend les défini par l’utilisateur et levé implicite ou opérateurs de conversion explicite déclarés par les classes ou les structs dans `D` qui convertir à partir d’un type englobant ou englobé par `S` à un type englobant ou englobé par `T`. Si `U` est vide, la conversion n’est pas définie et une erreur de compilation se produit.
*  Rechercher le type de source plus spécifique, `SX`, des opérateurs dans `U`:
    * Si un des opérateurs dans `U` convertir à partir de `S`, puis `SX` est `S`.
    * Sinon, si un des opérateurs dans `U` convertir à partir de types qui englobent `S`, puis `SX` est le type le plus englobé dans l’ensemble combiné des types de source de ces opérateurs. Si aucun la plupart n’englobé type peut être trouvé, puis la conversion est ambiguë et une erreur de compilation se produit.
    * Sinon, `SX` est le type le plus englobant dans l’ensemble combiné des types source des opérateurs dans `U`. Si exactement un type plus englobant ne peut pas être trouvé, la conversion est ambiguë et une erreur de compilation se produit.
*  Rechercher le type de cible plus spécifique, `TX`, des opérateurs dans `U`:
    * Si un des opérateurs dans `U` convertir `T`, puis `TX` est `T`.
    * Sinon, si un des opérateurs dans `U` convertir des types qui sont comprises dans la bibliothèque `T`, puis `TX` est le type le plus englobant dans l’ensemble combiné des types de cible de ces opérateurs. Si exactement un type plus englobant ne peut pas être trouvé, la conversion est ambiguë et une erreur de compilation se produit.
    * Sinon, `TX` est le type le plus englobé dans l’ensemble combiné des types cible des opérateurs dans `U`. Si aucun la plupart n’englobé type peut être trouvé, puis la conversion est ambiguë et une erreur de compilation se produit.
*  Rechercher l’opérateur de conversion plus spécifique :
    * Si `U` contient exactement un opérateur de conversion définie par l’utilisateur qui effectue la conversion à partir de `SX` à `TX`, il s’agit de l’opérateur de conversion plus spécifique.
    * Sinon, si `U` contient exactement un opérateur de conversion levé convertit à partir de `SX` à `TX`, il s’agit de l’opérateur de conversion plus spécifique.
    * Sinon, la conversion est ambiguë et une erreur de compilation se produit.
*  Enfin, appliquez la conversion :
    * Si `S` n’est pas `SX`, puis une conversion explicite standard de `S` à `SX` est effectuée.
    * Opérateur de conversion définie par l’utilisateur le plus spécifique est appelée pour convertir à partir de `SX` à `TX`.
    * Si `TX` n’est pas `T`, puis une conversion explicite standard de `TX` à `T` est effectuée.

## <a name="anonymous-function-conversions"></a>Conversions de fonctions anonymes

Un *anonymous_method_expression* ou *lambda_expression* est classé comme une fonction anonyme ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)). L’expression n’a pas un type, mais peut être implicitement convertie en type de délégué compatible ou type arborescence d’expression. Plus précisément, une fonction anonyme `F` est compatible avec un type délégué `D` fourni :

*  Si `F` contient un *anonymous_function_signature*, puis `D` et `F` ont le même nombre de paramètres.
*  Si `F` ne contient pas une *anonymous_function_signature*, puis `D` peut avoir zéro ou plusieurs paramètres de type quelconque, à condition qu’aucun paramètre de `D` a le `out` modificateur de paramètre.
*  Si `F` possède une liste de paramètres explicitement typée, chaque paramètre dans `D` a le même type et modificateurs que le paramètre correspondant dans `F`.
*  Si `F` possède une liste de paramètres implicitement typé, `D` n’a aucun `ref` ou `out` paramètres.
*  Si le corps de `F` est une expression et soit `D` a un `void` type de retour ou `F` est asynchrone et `D` a le type de retour `Task`, lorsque chaque paramètre de `F` est fonction du type de la paramètre correspondant dans `D`, le corps de `F` est une expression valide (wrt [Expressions](expressions.md)) qui serait autorisée en tant qu’un *statement_expression* ([Instructions d’expression](statements.md#expression-statements)).
*  Si le corps de `F` est un bloc d’instructions et soit `D` a un `void` type de retour ou `F` est asynchrone et `D` a le type de retour `Task`, lorsque chaque paramètre de `F` est fonction du type de le paramètre correspondant dans `D`, le corps de `F` est un bloc d’instructions valides (wrt [blocs](statements.md#blocks)) dans lesquels aucun `return` instruction spécifie une expression.
*  Si le corps de `F` est une expression, et *soit* `F` est non asynchrone et `D` a un type de retour non void `T`, *ou* `F` est asynchrone et `D` a un type de retour `Task<T>`, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est une expression valide (wrt [ Expressions](expressions.md)) qui est implicitement convertible en `T`.
*  Si le corps de `F` est un bloc d’instructions, et *soit* `F` est non asynchrone et `D` a un type de retour non void `T`, *ou* `F` est asynchrone et `D` a un type de retour `Task<T>`, lorsque chaque paramètre de `F` reçoit le type du paramètre correspondant dans `D`, le corps de `F` est un bloc d’instructions valides (wrt [blocs ](statements.md#blocks)) avec un point de terminaison non accessible dans lequel chaque `return` instruction spécifie une expression qui est implicitement convertible en `T`.

À des fins de concision, cette section utilise la forme abrégée pour les types de tâches `Task` et `Task<T>` ([fonctions Async](classes.md#async-functions)).

Une expression lambda `F` est compatible avec un type d’arborescence expression `Expression<D>` si `F` est compatible avec le type de délégué `D`. Notez que cela ne s’applique pas aux méthodes anonymes, seules les expressions lambda.

Certaines expressions lambda ne peut pas être converties en types arborescence d’expression : Même si la conversion *existe*, elle échoue au moment de la compilation. Cela est le cas si l’expression lambda :

*  A un *bloc* corps
*  Contient les opérateurs d’assignation simple ou composite
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
les types de paramètre et de retour de chaque fonction anonyme sont déterminés à partir du type de la variable à laquelle la fonction anonyme est affectée.

La première assignation convertit correctement la fonction anonyme pour le type de délégué `Func<int,int>` car, lorsque `x` est de type donné `int`, `x+1` est une expression valide qui est implicitement convertible en type `int`.

De même, la deuxième assignation convertit correctement la fonction anonyme pour le type de délégué `Func<int,double>` , car le résultat de `x+1` (de type `int`) est implicitement convertible en type `double`.

Toutefois, l’attribution de tiers est une erreur de compilation car, lorsque `x` est de type donné `double`, le résultat de `x+1` (de type `double`) n’est pas implicitement convertible en type `int`.

L’attribution de quatrième convertit correctement la fonction anonyme async pour le type de délégué `Func<int, Task<int>>` , car le résultat de `x+1` (de type `int`) est implicitement convertible en type de résultat `int` du type de tâche `Task<int>`.

Fonctions anonymes peuvent influencer la résolution de surcharge et participer à l’inférence de type. Consultez [membres de fonction](expressions.md#function-members) pour plus d’informations.

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a>Évaluation des conversions de fonction anonyme pour les types délégués

Conversion d’une fonction anonyme en un type délégué génère une instance de délégué qui fait référence à la fonction anonyme et l’ensemble (éventuellement vide) de variables capturées externes qui sont actives au moment de l’évaluation. Lorsque le délégué est appelé, le corps de la fonction anonyme est exécuté. Le code dans le corps est exécuté à l’aide de l’ensemble de variables capturées d’externes référencée par le délégué.

La liste d’appel d’un délégué généré à partir d’une fonction anonyme contient une seule entrée. L’objet cible exacte et la méthode cible du délégué ne sont pas spécifiés. En particulier, il n’est pas spécifié si l’objet cible du délégué est `null`, le `this` valeur de la fonction membre englobante, ou un autre objet.

Les conversions de fonctions anonymes sémantiquement identiques avec un même ensemble d’instances de variable externes capturées (éventuellement vide) pour les mêmes types de délégué sont autorisées (mais pas obligatoires) pour retourner la même instance de délégué. Le terme sémantiquement identique est utilisé ici pour signifier que l’exécution des fonctions anonymes, dans tous les cas, génère les mêmes effets étant données les mêmes arguments. Cette règle autorise le code suivant à optimiser.

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

Étant donné que les deux délégués de fonction anonyme ont les mêmes (vide) de variables externes capturées, étant donné que les fonctions anonymes sont sémantiquement identiques, le compilateur peut avoir les délégués font référence à la même méthode cible. En effet, le compilateur est autorisé à renvoyer la même instance de délégué à partir de deux expressions de fonction anonyme.

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a>Évaluation des conversions de fonction anonyme pour les types arborescence d’expression

Conversion d’une fonction anonyme en un type d’arborescence expression génère une arborescence d’expressions ([types arborescence d’Expression](types.md#expression-tree-types)). Plus précisément, la version d’évaluation de la conversion d’une fonction anonyme entraîne la construction d’une structure d’objet qui représente la structure de la fonction anonyme elle-même. La structure précise de l’arborescence d’expression, ainsi que le processus exact pour sa création, sont définie par l’implémentation.

### <a name="implementation-example"></a>Exemple d’implémentation

Cette section décrit une implémentation possible de conversions de fonction anonyme en termes d’autres constructions c#. L’implémentation décrite ici est basée sur les mêmes principes que celui utilisés par le compilateur Microsoft c#, mais il n’est pas une implémentation autorisée, ni de la possible qu’une seule. Elle mentionne brièvement les conversions en arborescences d’expression, comme leur sémantique exacte est en dehors de l’étendue de cette spécification.

Le reste de cette section fournit plusieurs exemples de code qui contient les fonctions anonymes présentant des caractéristiques différentes. Pour chaque exemple, une traduction correspondante au code qui utilise uniquement autres constructions c# est fournie. Dans les exemples, l’identificateur `D` est supposé par représentent le type délégué suivant :
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

Cela peut être traduit en une instanciation de délégué qui fait référence à une méthode statique générée par le compilateur dans lequel est placé le code de la fonction anonyme :
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

Dans l’exemple suivant, la fonction anonyme fait référence à des membres d’instance `this`:
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

Cela peut être traduit en une méthode d’instance généré par le compilateur qui contient le code de la fonction anonyme :
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

La durée de vie de la variable locale doit maintenant être étendue au moins la durée de vie du délégué de fonction anonyme. Cela est possible en « levée » de la variable locale dans un champ d’une classe générée par le compilateur. Instanciation de la variable locale ([instanciation des variables locales](expressions.md#instantiation-of-local-variables)) qui correspond à la création d’une instance de la classe générée par le compilateur et l’accès à la variable locale correspond à l’accès à un champ dans l’instance de la classe générée par le compilateur. En outre, la fonction anonyme devient une méthode d’instance de la classe générée par le compilateur :
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

Enfin, les anonymes de fonctions suivants captures `this` , ainsi que deux variables locales avec différentes durées de vie :
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

Ici, une classe générée par le compilateur est créée pour chaque instruction bloquer dans les variables locales sont capturées telles que les variables locales dans les blocs différents peuvent avoir des durées de vie indépendantes. Une instance de `__Locals2`, la classe générée par le compilateur pour le bloc d’instruction interne, contient la variable locale `z` et un champ qui fait référence à une instance de `__Locals1`.  Une instance de `__Locals1`, la classe générée par le compilateur pour le bloc d’instruction externe, contient la variable locale `y` et un champ qui fait référence à `this` du membre de fonction englobante. Ces structures de données qu’il est possible d’atteindre tous les capturés variables externes via une instance de `__Local2`, et le code de la fonction anonyme peut par conséquent être implémenté comme une méthode d’instance de cette classe.

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

La même technique appliquée ici pour capturer les variables locales permettre également servir lors de la conversion des fonctions anonymes arborescences d’expression : Références à des objets générés par le compilateur peuvent être stockés dans l’arborescence d’expression, et accès aux variables locales peuvent être représentés comme champ accède à ces objets. L’avantage de cette approche est qu’elle autorise les variables locales « levés » être partagés entre les délégués et les arborescences d’expression.

## <a name="method-group-conversions"></a>Conversions de groupe (méthode)

Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir d’un groupe de méthodes ([classifications des expressions](expressions.md#expression-classifications)) à un type délégué compatible. Étant donné un type délégué `D` et une expression `E` qui est classé comme un groupe de méthodes, il existe une conversion implicite à partir de `E` à `D` si `E` contient au moins une méthode qui n’est applicable dans sa forme normale () [Membre de fonction applicable](expressions.md#applicable-function-member)) à une liste d’arguments construit en utilisant des types de paramètres et des modificateurs de `D`, comme décrit dans l’exemple suivant.

L’application de la compilation d’une conversion à partir d’un groupe de méthodes `E` à un type délégué `D` est décrite dans l’exemple suivant. Notez que l’existence d’une conversion implicite de `E` à `D` ne garantit pas que l’application lors de la compilation de la conversion réussit sans erreur.

*  Une seule méthode `M` est sélectionnée correspondant à un appel de méthode ([appels de méthode](expressions.md#method-invocations)) sous la forme `E(A)`, avec les modifications suivantes :
    * La liste d’arguments `A` est une liste d’expressions, chaque classifiées en tant que variable et avec le type et le modificateur (`ref` ou `out`) du paramètre correspondant dans le *formal_parameter_list* de `D`.
    * Les méthodes de candidat considéré comme sont uniquement les méthodes qui sont applicables dans leur forme normale ([membre de fonction Applicable](expressions.md#applicable-function-member)), pas celles applicables uniquement dans leur forme étendue.
*  Si l’algorithme de [appels de méthode](expressions.md#method-invocations) génère une erreur, une erreur de compilation se produit. Sinon, l’algorithme produit une meilleure méthode unique `M` ayant le même nombre de paramètres en tant que `D` et la conversion est considéré comme existant.
*  La méthode sélectionnée `M` doivent être compatibles ([compatibilité des délégués](delegates.md#delegate-compatibility)) avec le type de délégué `D`, ou dans le cas contraire, une erreur de compilation se produit.
*  Si la méthode sélectionnée `M` est une méthode d’instance, l’expression d’instance associée `E` détermine l’objet cible du délégué.
*  Si la méthode sélectionnée M est une méthode d’extension qui est indiquée au moyen d’un accès aux membres sur une expression d’instance, cette expression d’instance détermine l’objet cible du délégué.
*  Le résultat de la conversion est une valeur de type `D`, à savoir un délégué nouvellement créé qui fait référence à l’objet de méthode et la cible sélectionnée.
*  Notez que ce processus peut entraîner la création d’un délégué à une méthode d’extension, si l’algorithme de [appels de méthode](expressions.md#method-invocations) ne parvient pas à trouver une méthode d’instance mais réussit lors du traitement de l’appel de `E(A)` en tant qu’extension appel de méthode ([appels de méthode d’Extension](expressions.md#extension-method-invocations)). Un délégué créé par conséquent capture la méthode d’extension, ainsi que son premier argument.

L’exemple suivant montre les conversions de groupes de méthode :
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

L’assignation à `d1` convertit implicitement le groupe de méthodes `F` à une valeur de type `D1`.

L’assignation à `d2` montre comment il est possible de créer un délégué à une méthode qui a des types de paramètre moins dérivé (contravariant) et une plus dérivés (covariant) type de retour.

L’assignation à `d3` montre comment n’existe aucune conversion si la méthode n’est pas applicable.

L’assignation à `d4` montre comment la méthode doit être applicable dans sa forme normale.

L’assignation à `d5` montre comment les types de paramètre et de retour du délégué et la méthode sont autorisés à différer uniquement pour les types référence.

Comme avec toutes les autres conversions implicites et explicites, l’opérateur de conversion peut être utilisé pour effectuer explicitement une conversion de groupe de méthode. Par conséquent, l’exemple
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
au lieu de cela peut être écrit
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

Les groupes de méthode peuvent influencer la résolution de surcharge et participer à l’inférence de type. Consultez [membres de fonction](expressions.md#function-members) pour plus d’informations.

La version d’évaluation de l’exécution d’une conversion de groupe de méthode procède comme suit :

*  Si la méthode sélectionnée au moment de la compilation est une méthode d’instance, ou il est une méthode d’extension qui est accessible en tant qu’une méthode d’instance, l’objet cible du délégué est déterminé à partir de l’expression d’instance associée `E`:
    * L’expression d’instance est évaluée. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
    * Si l’expression d’instance est d’un *reference_type*, la valeur calculée par l’expression d’instance devient l’objet cible. Si la méthode sélectionnée est une méthode d’instance et l’objet cible est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
    * Si l’expression d’instance est d’un *value_type*, une opération de boxing ([conversions Boxing](types.md#boxing-conversions)) est effectuée pour convertir la valeur d’un objet, et cet objet devienne l’objet cible.
*  Sinon, la méthode sélectionnée fait partie d’un appel de méthode statique, et l’objet cible du délégué est `null`.
*  Une nouvelle instance du type délégué `D` est allouée. Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
*  La nouvelle instance de délégué est initialisée avec une référence à la méthode qui a été déterminée au moment de la compilation et une référence à l’objet cible calculé ci-dessus.
