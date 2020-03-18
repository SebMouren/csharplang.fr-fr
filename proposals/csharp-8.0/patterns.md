---
ms.openlocfilehash: fad1425e2871f395a2eb1f39faccbc773d88d6a3
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485062"
---
# <a name="recursive-pattern-matching"></a>Critères spéciaux récursifs

## <a name="summary"></a>Résumé
[summary]: #summary

Les extensions de critères C# spéciaux pour permettre un grand nombre des avantages des types de données algébriques et des critères spéciaux des langages fonctionnels, mais d’une manière qui s’intègre parfaitement au langage sous-jacent. Les éléments de cette approche sont inspirés par les fonctionnalités associées dans [F#](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Critères spéciaux extensibles via un langage léger") les langages de programmation et [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Objets correspondants avec des modèles, page 273").

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

### <a name="is-expression"></a>Expression is

L’opérateur `is` est étendu pour tester une expression par rapport à un *modèle*.

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

Cette forme de *relational_expression* s’ajoute aux formulaires existants dans la C# spécification. Il s’agit d’une erreur au moment de la compilation si le *relational_expression* à gauche du jeton `is` ne désigne pas une valeur ou n’a pas de type.

Chaque *identificateur* du modèle introduit une nouvelle variable locale qui est *définitivement assignée* une fois que l’opérateur `is` est `true` (c’est-à-dire *affecté définitivement lorsque la valeur est true*).

> Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié. Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre, car nous faisons une expression dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type). Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.

### <a name="patterns"></a>Modèles

Les modèles sont utilisés dans l’opérateur *is_pattern* , dans une *switch_Statement*et dans une *switch_expression* pour exprimer la forme des données par rapport à laquelle les données entrantes (que nous appelons la valeur d’entrée) doivent être comparées. Les modèles peuvent être récursifs afin que certaines parties des données puissent être comparées à des sous-modèles.

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' subpatterns? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a>Modèle de déclaration

```antlr
declaration_pattern
    : type simple_designation
    ;
```

Le *declaration_pattern* teste qu’une expression est d’un type donné et la convertit en ce type si le test a la valeur. Cela peut introduire une variable locale du type donné nommé par l’identificateur donné, si la désignation est un *single_variable_designation*. Cette variable locale est *assignée* de manière définitive lorsque le résultat de l’opération de correspondance de modèle est `true`.

La sémantique d’exécution de cette expression est qu’elle teste le type de Runtime de l’opérande de *relational_expression* gauche par rapport au *type* dans le modèle.  S’il s’agit d’un type de Runtime (ou d’un sous-type) et non `null`, le résultat de la `is operator` est `true`.

Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation. Une valeur de type statique `E` est dite compatible avec les *modèles* avec un type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`, ou si l’un de ces types est un type ouvert. Il s’agit d’une erreur de compilation si une entrée de type `E` n’est pas *compatible* avec le *type* de modèle dans un modèle de type auquel elle est associée.

Le modèle de type est utile pour effectuer des tests de type au moment de l’exécution de types référence et remplace l’idiome

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

Avec un peu plus concis

```csharp
if (expr is Type v) { // code using v
```

Il s’agit d’une erreur si le *type* est un type valeur Nullable.

Le modèle de type peut être utilisé pour tester des valeurs de types Nullable : une valeur de type `Nullable<T>` (ou un `T`boxed) correspond à un modèle de type `T2 id` si la valeur n’est pas null et que le type de `T2` est `T`, ou un type de base ou une interface de `T`. Par exemple, dans le fragment de code

```csharp
int? x = 3;
if (x is int v) { // code using v
```

La condition de l’instruction `if` est `true` au moment de l’exécution et la variable `v` contient la valeur `3` de type `int` à l’intérieur du bloc. Après le bloc, la variable `v` est dans la portée, mais elle n’est pas définitivement assignée.

#### <a name="constant-pattern"></a>Modèle de constante

```antlr
constant_pattern
    : constant_expression
    ;
```

Un modèle de constante teste la valeur d’une expression par rapport à une valeur constante. La constante peut être n’importe quelle expression constante, telle qu’un littéral, le nom d’une variable `const` déclarée ou une constante d’énumération. Lorsque la valeur d’entrée n’est pas un type ouvert, l’expression constante est implicitement convertie dans le type de l’expression correspondante ; Si le type de la valeur d’entrée n’est pas *compatible* avec le type de l’expression constante, l’opération de correspondance de modèle est une erreur.

Le modèle *c* est considéré comme correspondant à la valeur d’entrée convertie *e* si `object.Equals(c, e)` retournerait `true`.

Nous pensons que `e is null` est le moyen le plus courant de tester `null` dans du code récemment écrit, car il ne peut pas appeler un `operator==`défini par l’utilisateur.

#### <a name="var-pattern"></a>Modèle var

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

Si la *désignation* est un *simple_designation*, une expression *e* correspond au modèle. En d’autres termes, une correspondance avec un *modèle var* est toujours réussie avec un *simple_designation*. Si le *simple_designation* est un *single_variable_designation*, la valeur de *e* est liée à une variable locale nouvellement introduite. Le type de la variable locale est le type statique de *e*.

Si la *désignation* est un *tuple_designation*, le modèle est équivalent à une *positional_pattern* de la forme `(var` *désignation*,... `)` où les *désignations*sont celles qui se trouvent dans le *tuple_designation*.  Par exemple, le modèle `var (x, (y, z))` est équivalent à `(var x, (var y, var z))`.

Il s’agit d’une erreur si le nom `var` est lié à un type.

#### <a name="discard-pattern"></a>Supprimer le modèle

```antlr
discard_pattern
    : '_'
    ;
```

Une expression *e* correspond au modèle `_` toujours. En d’autres termes, chaque expression correspond au modèle d’abandon.

Un modèle d’abandon ne peut pas être utilisé comme modèle d’un *is_pattern_expression*.

#### <a name="positional-pattern"></a>Modèle positionnel

Un modèle positionnel vérifie que la valeur d’entrée n’est pas `null`, appelle une méthode de `Deconstruct` appropriée et effectue d’autres critères spéciaux sur les valeurs résultantes.  Il prend également en charge une syntaxe de modèle de type Tuple (sans le type fourni) quand le type de la valeur d’entrée est le même que le type contenant `Deconstruct`, ou si le type de la valeur d’entrée est un type de tuple, ou si le type de la valeur d’entrée est `object` ou `ITuple` et que le type de Runtime de l’expression implémente `ITuple`.

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

Si le *type* est omis, nous le prenons comme type statique de la valeur d’entrée.

Pour une correspondance entre une valeur d’entrée et le *type* de modèle `(` *subpattern_list* `)`, une méthode est sélectionnée en recherchant dans *type* les déclarations accessibles de `Deconstruct` et en en sélectionnant une parmi celles-ci en utilisant les mêmes règles que pour la déclaration de déconstruction.

Il s’agit d’une erreur si un *positional_pattern* omet le type, a un seul sous- *modèle* sans *identificateur*, n’a pas de *property_subpattern* et n’a pas de *simple_designation*. Ce ambiguïté entre une *constant_pattern* entre parenthèses et un *positional_pattern*.

Afin d’extraire les valeurs pour les faire correspondre aux modèles de la liste,
- Si le *type* a été omis et que le type de la valeur d’entrée est un type tuple, le nombre de sous-modèles doit être le même que la cardinalité du tuple. Chaque élément de tuple est mis en correspondance avec le sous- *modèle*correspondant, et la correspondance est établie si tous ces éléments sont correctement exécutés. Si un sous- *modèle* a un *identificateur*, cela doit nommer un élément de tuple à la position correspondante dans le type de Tuple.
- Dans le cas contraire, si un `Deconstruct` approprié existe en tant que membre de *type*, il s’agit d’une erreur de compilation si le type de la valeur d’entrée n’est pas *compatible* avec le *type*. Au moment de l’exécution, la valeur d’entrée est testée par rapport au *type*. En cas d’échec, la correspondance du modèle positionnel échoue. Si elle réussit, la valeur d’entrée est convertie vers ce type et `Deconstruct` est appelée avec les nouvelles variables générées par le compilateur pour recevoir les paramètres `out`. Chaque valeur reçue est mise en correspondance avec le sous- *modèle*correspondant, et la correspondance est établie si toutes ces valeurs sont exécutées correctement. Si un sous- *modèle* a un *identificateur*, alors il doit nommer un paramètre à la position correspondante de `Deconstruct`.
- Sinon, si le *type* a été omis et que la valeur d’entrée est de type `object` ou `ITuple` ou d’un type qui peut être converti en `ITuple` par une conversion de référence implicite, et qu’aucun *identificateur* n’apparaît parmi les sous-modèles, nous effectuons une correspondance à l’aide de `ITuple`.
- Dans le cas contraire, le modèle est une erreur au moment de la compilation.

L’ordre dans lequel les sous-modèles sont mis en correspondance au moment de l’exécution n’est pas spécifié, et une erreur de correspondance peut ne pas tenter de faire correspondre tous les sous-modèles.

##### <a name="example"></a>Exemple

Cet exemple utilise un grand nombre des fonctionnalités décrites dans cette spécification.

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a>Modèle de propriété

Un modèle de propriété vérifie que la valeur d’entrée n’est pas `null` et fait correspondre de manière récursive les valeurs extraites par l’utilisation de propriétés ou de champs accessibles.

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

Il s’agit d’une erreur si un sous- _modèle_ d’un _property_pattern_ ne contient pas d' _identificateur_ (il doit être de la deuxième forme, qui a un _identificateur_).  Une virgule de fin après le dernier sous-modèle est facultative.

Notez qu’un modèle de vérification null est issu d’un modèle de propriété trivial. Pour vérifier si la `s` de chaîne n’est pas null, vous pouvez écrire l’un des formulaires suivants

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

En raison de la correspondance d’une expression *e* au *type* de modèle `{` *property_pattern_list* `}`, il s’agit d’une erreur de compilation si l’expression *e* n’est pas *compatible* avec le type *t* désigné par *type*. Si le type est absent, nous le prenons comme type statique de *e*. Si l' *identificateur* est présent, il déclare une variable de modèle *de type type.* Chacun des identificateurs figurant sur le côté gauche de son *property_pattern_list* doit désigner une propriété ou un champ accessible en lecture accessible de *T*. Si le *simple_designation* du *property_pattern* est présent, il définit une variable de modèle de type *T*.

Au moment de l’exécution, l’expression est testée par rapport à *T*. En cas d’échec, la correspondance du modèle de propriété échoue et le résultat est `false`. Si elle est réussie, chaque *property_subpattern* champ ou propriété est lu et sa valeur correspond à son modèle correspondant. Le résultat de la correspondance entière est `false` uniquement si le résultat de l’une d’entre elles est `false`. L’ordre dans lequel les sous-modèles sont mis en correspondance n’est pas spécifié, et une correspondance qui a échoué peut ne pas correspondre à tous les sous-modèles au moment de l’exécution. Si la correspondance est établie et que la *simple_designation* du *property_pattern* est un *single_variable_designation*, elle définit une variable de type *T* à laquelle est assignée la valeur correspondante.

> Remarque : le modèle de propriété peut être utilisé pour effectuer une correspondance de modèle avec des types anonymes.

##### <a name="example"></a>Exemple

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a>Expression de switch

Un *switch_expression* est ajouté pour prendre en charge la sémantique de type `switch`pour un contexte d’expression.

La C# syntaxe du langage est augmentée avec les productions syntaxiques suivantes :

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

Le *switch_expression* n’est pas autorisé en tant que *expression_statement*.

> Nous allons nous pencher sur la prochaine révision.

Le type de l' *switch_expression* est le [*meilleur type commun*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) des expressions apparaissant à droite des jetons de `=>` des *switch_expression_arm*s si un tel type existe et l’expression de chaque ARM de l’expression de commutateur peut être convertie implicitement en ce type.  En outre, nous ajoutons une nouvelle *conversion d’expression de commutateur*, qui est une conversion implicite prédéfinie d’une expression de commutateur à chaque type `T` pour laquelle il existe une conversion implicite de l’expression de chaque arm en `T`.

Il s’agit d’une erreur si le modèle de *switch_expression_arm*ne peut pas affecter le résultat, car un modèle précédent et une protection correspondent toujours.

Une expression de commutateur est dite *exhaustive* si une partie de l’expression de commutateur gère chaque valeur de son entrée.  Le compilateur génère un avertissement si une expression de commutateur n’est pas *exhaustive*.

Lors de l’exécution, le résultat de l' *switch_expression* est la valeur de l' *expression* de la première *switch_expression_arm* pour laquelle l’expression située à gauche du *switch_expression* correspond au modèle de l' *switch_expression_arm*, et pour laquelle la *case_guard* du *switch_expression_arm*, le cas échéant, est évaluée à `true`. S’il n’y a pas de *switch_expression_arm*de ce type, la *switch_expression* lève une instance de l’exception `System.Runtime.CompilerServices.SwitchExpressionException`.

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a>Parenthèses facultatif lors du basculement sur un littéral de Tuple

Pour activer un littéral de tuple à l’aide de la *switch_Statement*, vous devez écrire ce qui semble être redondant parenthèses

```csharp
switch ((a, b))
{
```

Pour autoriser

```csharp
switch (a, b)
{
```

les parenthèses de l’instruction switch sont facultatives lorsque l’expression qui est basculée est un littéral de Tuple.

### <a name="order-of-evaluation-in-pattern-matching"></a>Ordre d’évaluation dans la mise en correspondance des modèles

Donner à la flexibilité du compilateur la possibilité de réorganiser les opérations exécutées pendant la mise en correspondance des modèles peut permettre de bénéficier d’une certaine flexibilité pour améliorer l’efficacité de la mise en correspondance des modèles. L’exigence (non appliquée) serait que les propriétés accessibles dans un modèle et les méthodes de déconstruction doivent être « pures » (effet secondaire gratuit, idempotent, etc.). Cela ne signifie pas que nous ajouterions la pureté comme un concept de langage, seulement que nous autoriserions la flexibilité du compilateur à réorganiser les opérations.

**Résolution 2018-04-04 LDM**: confirmée : le compilateur est autorisé à réorganiser les appels à `Deconstruct`, les accès aux propriétés et les appels de méthodes dans `ITuple`, et peut supposer que les valeurs retournées sont identiques à celles des appels multiples. Le compilateur ne doit pas appeler de fonctions qui ne peuvent pas affecter le résultat, et nous allons faire attention avant d’apporter des modifications à l’ordre d’évaluation généré par le compilateur à l’avenir.

### <a name="some-possible-optimizations"></a>Optimisations possibles

La compilation des critères spéciaux peut tirer parti des parties communes des modèles. Par exemple, si le test de type de niveau supérieur de deux modèles successifs dans un *switch_Statement* est du même type, le code généré peut ignorer le test de type pour le deuxième modèle.

Quand certains des modèles sont des entiers ou des chaînes, le compilateur peut générer le même type de code qu’il génère pour une instruction switch dans les versions antérieures du langage.

Pour plus d’informations sur ces types d’optimisations, consultez [[Scott et Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Quand les heuristiques de compilation des correspondances sont-elles importantes ?").
