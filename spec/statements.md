---
ms.openlocfilehash: 7248a91976c479dc1b6b64b799639635617a7bec
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704044"
---
# <a name="statements"></a>Instructions

C#fournit une série d’instructions. La plupart de ces instructions seront familières aux développeurs qui ont programmé en C C++et.

```antlr
statement
    : labeled_statement
    | declaration_statement
    | embedded_statement
    ;

embedded_statement
    : block
    | empty_statement
    | expression_statement
    | selection_statement
    | iteration_statement
    | jump_statement
    | try_statement
    | checked_statement
    | unchecked_statement
    | lock_statement
    | using_statement
    | yield_statement
    | embedded_statement_unsafe
    ;
```

Le *embedded_statement* non terminal est utilisé pour les instructions qui s’affichent dans d’autres instructions. L’utilisation de *embedded_statement* au lieu d’une *instruction* exclut l’utilisation d’instructions de déclaration et d’instructions étiquetées dans ces contextes. L’exemple
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
génère une erreur au moment de la compilation, car une instruction `if` requiert un *embedded_statement* plutôt qu’une *instruction* pour sa branche if. Si ce code est autorisé, la variable `i` est déclarée, mais elle n’a jamais été utilisée. Notez, toutefois, qu’en plaçant `i`la déclaration de dans un bloc, l’exemple est valide.

## <a name="end-points-and-reachability"></a>Points de terminaison et accessibilité

Chaque instruction a un ***point de terminaison***. En termes intuitifs, le point de terminaison d’une instruction correspond à l’emplacement qui suit immédiatement l’instruction. Les règles d’exécution des instructions composites (instructions qui contiennent des instructions incorporées) spécifient l’action qui est effectuée lorsque le contrôle atteint le point de terminaison d’une instruction incorporée. Par exemple, lorsque le contrôle atteint le point de terminaison d’une instruction dans un bloc, le contrôle est transféré à l’instruction suivante dans le bloc.

Si une instruction peut être atteinte par l’exécution, l’instruction est dite ***accessible***. À l’inverse, s’il n’est pas possible qu’une instruction soit exécutée, l’instruction est dite ***inaccessible***.

Dans l’exemple
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
le deuxième appel de `Console.WriteLine` est inaccessible, car il n’est pas possible que l’instruction soit exécutée.

Un avertissement est signalé si le compilateur détermine qu’une instruction est inaccessible. Il ne s’agit pas d’une erreur spécifique pour qu’une instruction soit inaccessible.

Pour déterminer si une instruction ou un point de terminaison particulier est accessible, le compilateur effectue une analyse de workflow en fonction des règles d’accessibilité définies pour chaque instruction. L’analyse du workflow prend en compte les valeurs des expressions constantes ([expressions constantes](expressions.md#constant-expressions)) qui contrôlent le comportement des instructions, mais les valeurs possibles des expressions non constantes ne sont pas prises en compte. En d’autres termes, à des fins d’analyse de workflow de contrôle, une expression non constante d’un type donné est considérée comme ayant n’importe quelle valeur possible de ce type.

Dans l’exemple
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
l’expression booléenne de l' `if` instruction est une expression constante, car les deux opérandes `==` de l’opérateur sont des constantes. Étant donné que l’expression constante est évaluée au moment de la compilation `false`, produisant `Console.WriteLine` la valeur, l’appel est considéré comme inaccessible. Toutefois, si `i` est remplacé par une variable locale
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
l' `Console.WriteLine` appel est considéré comme accessible, même si, en réalité, il ne sera jamais exécuté.

Le *bloc* d’un membre de fonction est toujours considéré comme accessible. En évaluant successivement les règles d’accessibilité de chaque instruction dans un bloc, vous pouvez déterminer l’accessibilité d’une instruction donnée.

Dans l’exemple
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
l’accessibilité de la seconde `Console.WriteLine` est déterminée comme suit :

*  La première `Console.WriteLine` instruction d’expression est accessible car le bloc de la `F` méthode est accessible.
*  Le point de terminaison de la `Console.WriteLine` première instruction d’expression est accessible car cette instruction est accessible.
*  L' `if` instruction est accessible parce que le point de terminaison de la `Console.WriteLine` première instruction d’expression est accessible.
*  La deuxième `Console.WriteLine` instruction d’expression est accessible parce que l’expression booléenne de `if` l’instruction n’a pas la valeur `false`de constante.

Il existe deux situations dans lesquelles il s’agit d’une erreur de compilation pour que le point de terminaison d’une instruction soit accessible :

*  Étant donné `switch` que l’instruction ne permet pas à une section switch de passer à la section switch suivante, il s’agit d’une erreur de compilation pour le point de terminaison de la liste d’instructions d’une section switch à atteindre. Si cette erreur se produit, cela indique généralement qu’une `break` instruction est manquante.
*  Il s’agit d’une erreur au moment de la compilation pour le point de terminaison du bloc d’un membre de fonction qui calcule une valeur à atteindre. Si cette erreur se produit, cela indique généralement qu’une `return` instruction est manquante.

## <a name="blocks"></a>Blocs

Un *bloc* autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée.

```antlr
block
    : '{' statement_list? '}'
    ;
```

Un *bloc* se compose d’un *statement_list* facultatif ([listes d’instructions](statements.md#statement-lists)), placé entre accolades. Si la liste d’instructions est omise, le bloc est dit vide.

Un bloc peut contenir des instructions de déclaration ([instructions de déclaration](statements.md#declaration-statements)). La portée d’une variable locale ou d’une constante déclarée dans un bloc est le bloc.

Un bloc est exécuté comme suit :

*  Si le bloc est vide, le contrôle est transféré vers le point de terminaison du bloc.
*  Si le bloc n’est pas vide, le contrôle est transféré à la liste d’instructions. Quand et si le contrôle atteint le point de terminaison de la liste d’instructions, le contrôle est transféré vers le point de terminaison du bloc.

La liste d’instructions d’un bloc est accessible si le bloc lui-même est accessible.

Le point de terminaison d’un bloc est accessible si le bloc est vide ou si le point de terminaison de la liste d’instructions est accessible.

Un *bloc* qui contient une ou plusieurs `yield` instructions ([l’instruction yield](statements.md#the-yield-statement)) est appelé un bloc itérateur. Les blocs itérateurs sont utilisés pour implémenter des membres de fonction en tant qu’itérateurs ([itérateurs](classes.md#iterators)). Certaines restrictions supplémentaires s’appliquent aux blocs Iterator :

*  Il s’agit d’une erreur de compilation pour `return` qu’une instruction apparaisse dans un bloc itérateur `yield return` (les instructions sont autorisées).
*  Il s’agit d’une erreur au moment de la compilation pour qu’un bloc itérateur contienne un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)). Un bloc itérateur définit toujours un contexte sécurisé, même quand sa déclaration est imbriquée dans un contexte unsafe.

### <a name="statement-lists"></a>Listes d’instructions

Une ***liste*** d’instructions se compose d’une ou de plusieurs instructions écrites en séquence. Les *listes d’instructions se trouvent dans*les blocs ([blocs](statements.md#blocks)) et dans *switch_block*s ([instruction switch](statements.md#the-switch-statement)).

```antlr
statement_list
    : statement+
    ;
```

Une liste d’instructions est exécutée en transférant le contrôle à la première instruction. Quand et si le contrôle atteint le point de terminaison d’une instruction, le contrôle est transféré à l’instruction suivante. Quand et si le contrôle atteint le point de terminaison de la dernière instruction, le contrôle est transféré vers le point de terminaison de la liste d’instructions.

Une instruction dans une liste d’instructions est accessible si au moins l’une des conditions suivantes est vraie :

*  L’instruction est la première instruction et la liste d’instructions elle-même est accessible.
*  Le point de terminaison de l’instruction précédente est accessible.
*  L’instruction est une instruction étiquetée et l’étiquette est référencée par une `goto` instruction accessible.

Le point de terminaison d’une liste d’instructions est accessible si le point de terminaison de la dernière instruction de la liste est accessible.

## <a name="the-empty-statement"></a>Instruction vide

Un *empty_statement* ne fait rien.

```antlr
empty_statement
    : ';'
    ;
```

Une instruction vide est utilisée lorsqu’il n’y a aucune opération à effectuer dans un contexte où une instruction est requise.

L’exécution d’une instruction vide transfère simplement le contrôle au point de terminaison de l’instruction. Ainsi, le point de terminaison d’une instruction vide est accessible si l’instruction vide est accessible.

Une instruction vide peut être utilisée lors de l' `while` écriture d’une instruction avec un corps NULL :
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

En outre, une instruction vide peut être utilisée pour déclarer une étiquette juste avant la fermeture`}`«» d’un bloc :
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a>Instructions étiquetées

Un *labeled_statement* permet à une instruction d’être précédée d’une étiquette. Les instructions étiquetées sont autorisées dans des blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

Une instruction étiquetée déclare une étiquette portant le nom donné par l' *identificateur*. L’étendue d’une étiquette est le bloc entier dans lequel l’étiquette est déclarée, y compris les blocs imbriqués. Il s’agit d’une erreur au moment de la compilation pour deux étiquettes portant le même nom pour lesquelles des étendues se chevauchent.

Une étiquette peut être référencée à `goto` partir d’instructions ([l’instruction goto](statements.md#the-goto-statement)) dans la portée de l’étiquette. Cela signifie que `goto` les instructions peuvent transférer le contrôle dans les blocs et hors des blocs, mais jamais dans des blocs.

Les étiquettes ont leur propre espace de déclaration et n’interfèrent pas avec d’autres identificateurs. L’exemple
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
est valide et utilise le nom `x` en tant que paramètre et étiquette.

L’exécution d’une instruction étiquetée correspond exactement à l’exécution de l’instruction qui suit l’étiquette.

Outre l’accessibilité fournie par le workflow normal de contrôle, une instruction étiquetée est accessible si l’étiquette est référencée par une `goto` instruction accessible. Titre Si une `goto` instruction est à l' `try` intérieur d’un `finally` qui comprend un bloc et que l’instruction étiquetée est en dehors de `finally` la `try`, et que le point de terminaison du bloc est inaccessible, l’instruction étiquetée n’est pas accessible à partir de cette `goto` instruction.)

## <a name="declaration-statements"></a>Instructions de déclaration

Un *declaration_statement* déclare une variable locale ou une constante. Les instructions de déclaration sont autorisées dans des blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a>Déclarations de variables locales

Un *local_variable_declaration* déclare une ou plusieurs variables locales.

```antlr
local_variable_declaration
    : local_variable_type local_variable_declarators
    ;

local_variable_type
    : type
    | 'var'
    ;

local_variable_declarators
    : local_variable_declarator
    | local_variable_declarators ',' local_variable_declarator
    ;

local_variable_declarator
    : identifier
    | identifier '=' local_variable_initializer
    ;

local_variable_initializer
    : expression
    | array_initializer
    | local_variable_initializer_unsafe
    ;
```

Le *local_variable_type* d’un *local_variable_declaration* spécifie directement le type des variables introduites par la déclaration, ou indique avec l’identificateur `var` que le type doit être déduit en fonction d’un initialiseur. Le type est suivi d’une liste de *local_variable_declarator*s, chacun d’entre eux introduisant une nouvelle variable. Un *local_variable_declarator* se compose d’un *identificateur* qui nomme la variable, éventuellement suivie d’un jeton « `=` » et d’un *local_variable_initializer* qui donne la valeur initiale de la variable.

Dans le contexte d’une déclaration de variable locale, l’identificateur var agit comme un mot clé contextuel ([Mots clés](lexical-structure.md#keywords)). Quand *local_variable_type* est spécifié comme `var` et qu’aucun type nommé `var` n’est dans la portée, la déclaration est une ***déclaration de variable locale implicitement typée***, dont le type est déduit du type de l’expression d’initialiseur associée. Les déclarations de variables locales implicitement typées sont soumises aux restrictions suivantes :

*  Le *local_variable_declaration* ne peut pas inclure plusieurs *local_variable_declarator*s.
*  *Local_variable_declarator* doit inclure un *local_variable_initializer*.
*  *Local_variable_initializer* doit être une *expression*.
*  L' *expression* d’initialiseur doit avoir un type au moment de la compilation.
*  L' *expression* d’initialiseur ne peut pas faire référence à la variable déclarée elle-même

Voici des exemples de déclarations de variables locales implicitement typées incorrectes :

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

La valeur d’une variable locale est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) et la valeur d’une variable locale est modifiée à l’aide d’une *assignation* ([opérateurs d’affectation](expressions.md#assignment-operators)). Une variable locale doit être assignée définitivement ([assignation définie](variables.md#definite-assignment)) à chaque emplacement où sa valeur est obtenue.

La portée d’une variable locale déclarée dans un *local_variable_declaration* est le bloc dans lequel la déclaration se produit. La référence à une variable locale dans une position textuelle qui précède le *local_variable_declarator* de la variable locale est une erreur. Dans l’étendue d’une variable locale, il s’agit d’une erreur de compilation pour déclarer une autre constante ou variable locale portant le même nom.

Une déclaration de variable locale qui déclare plusieurs variables est équivalente à plusieurs déclarations de variables uniques de même type. En outre, un initialiseur de variable dans une déclaration de variable locale correspond exactement à une instruction d’assignation qui est insérée immédiatement après la déclaration.

L’exemple
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
correspond exactement à
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

Dans une déclaration de variable locale implicitement typée, le type de la variable locale déclarée est considéré comme étant le même que le type de l’expression utilisée pour initialiser la variable. Exemple :
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

Les déclarations de variables locales implicitement typées ci-dessus sont équivalentes précisément aux déclarations explicitement typées suivantes :
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a>Déclarations de constantes locales

Un *local_constant_declaration* déclare une ou plusieurs constantes locales.

```antlr
local_constant_declaration
    : 'const' type constant_declarators
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

Le *type* d’un *local_constant_declaration* spécifie le type des constantes introduites par la déclaration. Le type est suivi d’une liste de *constant_declarator*s, chacun d’entre eux introduisant une nouvelle constante. Un *constant_declarator* se compose d’un *identificateur* qui nomme la constante, suivi d’un jeton « `=` », suivi d’un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) qui donne la valeur de la constante.

Le *type* et le *constant_expression* d’une déclaration de constante locale doivent suivre les mêmes règles que celles d’une déclaration de membre constante ([constantes](classes.md#constants)).

La valeur d’une constante locale est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)).

La portée d’une constante locale est le bloc dans lequel la déclaration se produit. Il est erroné de faire référence à une constante locale dans une position textuelle qui précède son *constant_declarator*. Dans l’étendue d’une constante locale, il s’agit d’une erreur de compilation pour déclarer une autre constante ou variable locale portant le même nom.

Une déclaration de constante locale qui déclare plusieurs constantes est équivalente à plusieurs déclarations de constantes uniques avec le même type.

## <a name="expression-statements"></a>Instructions d’expression

Un *expression_statement* évalue une expression donnée. La valeur calculée par l’expression, le cas échéant, est ignorée.

```antlr
expression_statement
    : statement_expression ';'
    ;

statement_expression
    : invocation_expression
    | null_conditional_invocation_expression
    | object_creation_expression
    | assignment
    | post_increment_expression
    | post_decrement_expression
    | pre_increment_expression
    | pre_decrement_expression
    | await_expression
    ;
```

Toutes les expressions ne sont pas autorisées en tant qu’instructions. En particulier, les expressions telles `x + y` que `x == 1` et qui calculent simplement une valeur (qui sera ignorée) ne sont pas autorisées en tant qu’instructions.

L’exécution d’un *expression_statement* évalue l’expression contenue, puis transfère le contrôle au point de terminaison du *expression_statement*. Le point de terminaison d’un *expression_statement* est accessible si ce *expression_statement* est accessible.

## <a name="selection-statements"></a>Instructions de sélection

Instructions de sélection sélectionnez l’une des nombreuses instructions possibles pour l’exécution en fonction de la valeur d’une expression.

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a>L’instruction if

L' `if` instruction sélectionne une instruction à exécuter en fonction de la valeur d’une expression booléenne.

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Un `else` composant est associé au précédent `if` lexicalement le plus proche qui est autorisé par la syntaxe. Ainsi, une `if` instruction de la forme
```csharp
if (x) if (y) F(); else G();
```
est équivalent à
```csharp
if (x) {
    if (y) {
        F();
    }
    else {
        G();
    }
}
```

Une `if` instruction est exécutée comme suit :

*  L’expression *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.
*  Si l’expression booléenne génère `true`, le contrôle est transféré à la première instruction incorporée. Quand et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de `if` terminaison de l’instruction.
*  Si l’expression booléenne génère `false` et si une `else` partie est présente, le contrôle est transféré à la deuxième instruction incorporée. Quand et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de `if` terminaison de l’instruction.
*  Si l’expression booléenne génère `false` et si une `else` partie n’est pas présente, le contrôle est transféré au point de terminaison de `if` l’instruction.

La première instruction incorporée d' `if` une instruction est accessible si l' `if` instruction est accessible et que l’expression booléenne n’a pas la valeur `false`de constante.

La deuxième instruction incorporée d' `if` une instruction, si elle est présente, est accessible `if` si l’instruction est accessible et si l’expression booléenne n’a pas la `true`valeur de constante.

Le point de terminaison d' `if` une instruction est accessible si le point de terminaison d’au moins une de ses instructions incorporées est accessible. En outre, le point de terminaison d' `if` une instruction `else` sans partie est accessible si l' `if` instruction est accessible et que l’expression booléenne n’a pas la valeur `true`de constante.

### <a name="the-switch-statement"></a>Instruction switch

L’instruction switch sélectionne pour l’exécution une liste d’instructions ayant une étiquette Switch associée qui correspond à la valeur de l’expression Switch.

```antlr
switch_statement
    : 'switch' '(' expression ')' switch_block
    ;

switch_block
    : '{' switch_section* '}'
    ;

switch_section
    : switch_label+ statement_list
    ;

switch_label
    : 'case' constant_expression ':'
    | 'default' ':'
    ;
```

Un *switch_Statement* se compose du mot clé `switch`, suivi d’une expression entre parenthèses (appelée expression de commutateur), suivie d’un *switch_block*. *Switch_block* se compose de zéro, un ou plusieurs *switch_section*, entre accolades. Chaque *switch_section* se compose d’un ou plusieurs *switch_label*, suivis d’un *statement_list* ([listes d’instructions](statements.md#statement-lists)).

Le ***type gouvernant*** d’une `switch` instruction est établi par l’expression de switch.

*  Si le type de l’expression de commutateur est `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, 0 ou *enum_type*, ou s’il s’agit du type Nullable correspondant à l’un de ces types , il s’agit du type gouvernant de l’instruction 2.
*  Dans le cas contraire, une seule conversion implicite définie par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) doit exister du type de l’expression de commutateur à l’un `sbyte`des types `short`régis possibles suivants :, `byte`,, `ushort` , `int`, `uint`, ,`long` ,`char`,ou, un type Nullable correspondant à l’un de ces types. `ulong` `string`
*  Sinon, si aucune conversion implicite n’existe, ou s’il existe plusieurs conversions implicites, une erreur se produit au moment de la compilation.

L’expression constante de chaque `case` étiquette doit indiquer une valeur implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type gouvernant de l' `switch` instruction. Une erreur de compilation se produit si au moins deux `case` étiquettes dans la même `switch` instruction spécifient la même valeur de constante.

Il peut y avoir au plus `default` une étiquette dans une instruction switch.

Une `switch` instruction est exécutée comme suit :

*  L’expression de commutateur est évaluée et convertie en type gouvernant.
*  Si l’une des constantes spécifiées dans `case` une étiquette dans la `switch` même instruction est égale à la valeur de l’expression de commutateur, le contrôle est transféré à la `case` liste d’instructions à la suite de l’étiquette correspondante.
*  Si aucune des constantes spécifiées dans `case` les étiquettes de la `switch` même instruction n’est égale à la valeur de l’expression de commutateur, `default` et si une étiquette est présente, le contrôle est transféré à la `default` liste d’instructions à la suite du noms.
*  Si aucune des constantes spécifiées dans `case` les étiquettes de la `switch` même instruction n’est égale à la valeur de l’expression de commutateur, `default` et si aucune étiquette n’est présente, le contrôle est transféré au `switch` point de terminaison de l’instruction.

Si le point de terminaison de la liste d’instructions d’une section switch est accessible, une erreur de compilation se produit. C’est ce que l’on appelle la règle « aucune chute ». L’exemple
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
default:
    CaseOthers();
    break;
}
```
est valide, car aucune section switch n’a un point de terminaison accessible. Contrairement à C C++et, l’exécution d’une section switch n’est pas autorisée à passer à la section switch suivante, et l’exemple
```csharp
switch (i) {
case 0:
    CaseZero();
case 1:
    CaseZeroOrOne();
default:
    CaseAny();
}
```
génère une erreur au moment de la compilation. Lorsque l’exécution d’une section switch doit être suivie par l’exécution d’une autre section switch, `goto case` une `goto default` instruction explicite ou doit être utilisée :
```csharp
switch (i) {
case 0:
    CaseZero();
    goto case 1;
case 1:
    CaseZeroOrOne();
    goto default;
default:
    CaseAny();
    break;
}
```

Plusieurs étiquettes sont autorisées dans un *switch_section*. L’exemple
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
case 2:
default:
    CaseTwo();
    break;
}
```
est valide. L’exemple ne viole pas la règle « aucune chute », car les étiquettes `case 2:` et `default:` font partie du même *switch_section*.

La règle « aucune chute » empêche une classe courante de bogues qui se produisent dans C C++ et `break` lorsque les instructions sont omises par inadvertance. En outre, en raison de cette règle, les sections Switch d' `switch` une instruction peuvent être réorganisées arbitrairement sans affecter le comportement de l’instruction. Par exemple, les sections de l' `switch` instruction ci-dessus peuvent être inversées sans affecter le comportement de l’instruction :
```csharp
switch (i) {
default:
    CaseAny();
    break;
case 1:
    CaseZeroOrOne();
    goto default;
case 0:
    CaseZero();
    goto case 1;
}
```

La liste d’instructions d’une section switch se termine `break`généralement `goto case`par une `goto default` instruction,, ou, mais toute construction qui restitue le point de terminaison de la liste d’instructions inaccessible est autorisée. Par exemple, une `while` instruction contrôlée par l’expression `true` booléenne est connue pour ne jamais atteindre son point de terminaison. De même, `throw` une `return` instruction ou transfère toujours le contrôle ailleurs et n’atteint jamais son point de terminaison. Ainsi, l’exemple suivant est valide :
```csharp
switch (i) {
case 0:
    while (true) F();
case 1:
    throw new ArgumentException();
case 2:
    return;
}
```

Le type gouvernant d’une `switch` instruction peut être le type `string`. Exemple :
```csharp
void DoCommand(string command) {
    switch (command.ToLower()) {
    case "run":
        DoRun();
        break;
    case "save":
        DoSave();
        break;
    case "quit":
        DoQuit();
        break;
    default:
        InvalidCommand(command);
        break;
    }
}
```

À l’instar des opérateurs d’égalité de chaînes (opérateurs `switch` d’égalité de[chaînes](expressions.md#string-equality-operators)), l’instruction respecte la casse et exécute une section switch donnée uniquement si la `case` chaîne d’expression de commutateur correspond exactement à une constante d’étiquette.

Lorsque le type gouvernant d’une `switch` instruction est `string`, la valeur `null` est autorisée comme constante d’étiquette case.

Les *statement_list*s d’un *switch_block* peuvent contenir des instructions de déclaration ([instructions de déclaration](statements.md#declaration-statements)). La portée d’une variable locale ou d’une constante déclarée dans un bloc switch est le bloc switch.

La liste d’instructions d’une section switch donnée est accessible si l' `switch` instruction est accessible et qu’au moins l’une des conditions suivantes est vraie :

*  L’expression de commutateur est une valeur non constante.
*  L’expression de commutateur est une valeur constante qui correspond `case` à une étiquette dans la section switch.
*  L’expression de commutateur est une valeur constante qui ne correspond `case` à aucune étiquette, et la section Switch `default` contient l’étiquette.
*  Une étiquette de commutateur de la section switch est référencée par une instruction `goto case` ou `goto default` accessible.

Le point de terminaison d' `switch` une instruction est accessible si au moins l’une des conditions suivantes est vraie :

*  L' `switch` instruction contient une `break` instruction accessible qui quitte l' `switch` instruction.
*  L' `switch` instruction est accessible, l’expression de commutateur est une valeur non constante et aucune `default` étiquette n’est présente.
*  L' `switch` instruction est accessible, l’expression de commutateur est une valeur constante qui ne correspond à `case` aucune étiquette et aucune `default` étiquette n’est présente.

## <a name="iteration-statements"></a>Instructions d’itération

Les instructions d’itération exécutent plusieurs fois une instruction incorporée.

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a>Instruction while

L' `while` instruction exécute de manière conditionnelle une instruction incorporée zéro ou plusieurs fois.

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

Une `while` instruction est exécutée comme suit :

*  L’expression *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.
*  Si l’expression booléenne génère `true`, le contrôle est transféré à l’instruction incorporée. Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut- `continue` être à partir de l’exécution d’une instruction), `while` le contrôle est transféré au début de l’instruction.
*  Si l’expression booléenne génère `false`, le contrôle est transféré au point de terminaison de l' `while` instruction.

Dans l’instruction incorporée d' `while` une instruction, `break` une instruction ([instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de `while` l’instruction (terminant ainsi l’itération de l’instruction incorporée) et un `continue` Statement ([instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (ce qui effectue une autre `while` itération de l’instruction).

L’instruction incorporée d' `while` une instruction est accessible si l' `while` instruction est accessible et que l’expression booléenne n’a pas la valeur `false`de constante.

Le point de terminaison d' `while` une instruction est accessible si au moins l’une des conditions suivantes est vraie :

*  L' `while` instruction contient une `break` instruction accessible qui quitte l' `while` instruction.
*  L' `while` instruction est accessible et l’expression booléenne n’a pas la valeur `true`de constante.

### <a name="the-do-statement"></a>Instruction do

L' `do` instruction exécute de manière conditionnelle une instruction incorporée une ou plusieurs fois.

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

Une `do` instruction est exécutée comme suit :

*  Le contrôle est transféré à l’instruction incorporée.
*  Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être à partir de l’exécution d’une instruction `continue`), le *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évalué. Si l’expression booléenne génère `true`, le contrôle est transféré au début de l' `do` instruction. Sinon, le contrôle est transféré au point de terminaison de `do` l’instruction.

Dans l’instruction incorporée d' `do` une instruction, `break` une instruction ([instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de `do` l’instruction (terminant ainsi l’itération de l’instruction incorporée) et un `continue` Statement ([instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée.

L’instruction incorporée d' `do` une instruction est accessible si l' `do` instruction est accessible.

Le point de terminaison d' `do` une instruction est accessible si au moins l’une des conditions suivantes est vraie :

*  L' `do` instruction contient une `break` instruction accessible qui quitte l' `do` instruction.
*  Le point de terminaison de l’instruction incorporée est accessible et l’expression booléenne n’a pas la valeur `true`de constante.

### <a name="the-for-statement"></a>Instruction for

L' `for` instruction évalue une séquence d’expressions d’initialisation, puis, alors qu’une condition a la valeur true, exécute à plusieurs reprises une instruction incorporée et évalue une séquence d’expressions d’itération.

```antlr
for_statement
    : 'for' '(' for_initializer? ';' for_condition? ';' for_iterator? ')' embedded_statement
    ;

for_initializer
    : local_variable_declaration
    | statement_expression_list
    ;

for_condition
    : boolean_expression
    ;

for_iterator
    : statement_expression_list
    ;

statement_expression_list
    : statement_expression (',' statement_expression)*
    ;
```

Le *for_initializer*, le cas échéant, se compose soit d’un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)), soit d’une liste d' *statement_expression*s ([instructions d’expression](statements.md#expression-statements)) séparées par des virgules. La portée d’une variable locale déclarée par un *for_initializer* commence au *local_variable_declarator* pour la variable et s’étend jusqu’à la fin de l’instruction incorporée. L’étendue comprend *for_condition* et *for_iterator*.

Le *for_condition*, s’il est présent, doit être un *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)).

Le *for_iterator*, le cas échéant, se compose d’une liste d' *statement_expression*s ([instructions d’expression](statements.md#expression-statements)) séparés par des virgules.

Une instruction for est exécutée comme suit :

*  Si un *for_initializer* est présent, les initialiseurs de variable ou les expressions d’instruction sont exécutés dans l’ordre dans lequel ils sont écrits. Cette étape n’est effectuée qu’une seule fois.
*  Si un *for_condition* est présent, il est évalué.
*  Si le *for_condition* n’est pas présent ou si l’évaluation donne `true`, le contrôle est transféré à l’instruction incorporée. Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être à partir de l’exécution d’une instruction `continue`), les expressions du *for_iterator*, le cas échéant, sont évaluées dans l’ordre, puis une autre itération est effectuée, en commençant par évaluation de *for_condition* dans l’étape ci-dessus.
*  Si le *for_condition* est présent et que l’évaluation produit `false`, le contrôle est transféré vers le point de terminaison de l’instruction `for`.

Dans l’instruction incorporée d’une instruction `for`, une instruction `break` ([l’instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de l’instruction `for` (par conséquent, terminer l’itération de l’instruction incorporée) et une instruction `continue` ([ L’instruction continue](statements.md#the-continue-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de l’instruction incorporée (en exécutant donc le *for_iterator* et en exécutant une autre itération de l’instruction `for`, en commençant par *for_condition*).

L’instruction incorporée d' `for` une instruction est accessible si l’une des conditions suivantes est remplie :

*  L’instruction `for` est accessible et aucun *for_condition* n’est présent.
*  L’instruction `for` est accessible et un *for_condition* est présent et n’a pas la valeur constante `false`.

Le point de terminaison d' `for` une instruction est accessible si au moins l’une des conditions suivantes est vraie :

*  L' `for` instruction contient une `break` instruction accessible qui quitte l' `for` instruction.
*  L’instruction `for` est accessible et un *for_condition* est présent et n’a pas la valeur constante `true`.

### <a name="the-foreach-statement"></a>Instruction foreach

L' `foreach` instruction énumère les éléments d’une collection, en exécutant une instruction incorporée pour chaque élément de la collection.

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

Le *type* et l' *identificateur* d’une `foreach` instruction déclarent la ***variable d’itération*** de l’instruction. Si l’identificateur `var` est donné en tant que *local_variable_type*, et qu’aucun type nommé `var` n’est dans la portée, la variable d’itération est dite une ***variable d’itération implicitement typée***et son type est considéré comme le type d’élément du `foreach` comme indiqué ci-dessous. La variable d’itération correspond à une variable locale en lecture seule avec une portée qui s’étend sur l’instruction incorporée. Pendant l’exécution d' `foreach` une instruction, la variable d’itération représente l’élément de collection pour lequel une itération est en cours d’exécution. Une erreur de compilation se produit si l’instruction incorporée tente de modifier la variable d’itération (par le `++` biais `--` de l’assignation ou des opérateurs et) ou `ref` de `out` passer la variable d’itération en tant que paramètre ou.

Dans ce qui suit, par souci de `IEnumerable`concision `IEnumerable<T>` , `IEnumerator<T>` `IEnumerator`, et font référence aux types correspondants dans les `System.Collections` espaces `System.Collections.Generic`de noms et.

Le traitement au moment de la compilation d’une instruction foreach détermine d’abord le type de ***collection***, le type d' ***énumérateur*** et le ***type d’élément*** de l’expression. Cette détermination se déroule comme suit :

*  Si le type `X` d' *expression* est un type tableau, il existe une `X` `IEnumerable` conversion de référence implicite de en interface ( `System.Array` puisque implémente cette interface). Le ***type de collection*** est `IEnumerable` l’interface, le ***type*** d’énumérateur `IEnumerator` est l’interface et le ***type d’élément*** est le type d’élément `X`du type tableau.
*  Si le type `X` d' *expression* est `dynamic` , il y a `IEnumerable` une conversion implicite d' *expression* en interface ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions)). Le ***type de collection*** est `IEnumerable` l’interface et le ***type*** d’énumérateur `IEnumerator` est l’interface. Si l’identificateur `var` est fourni en tant que *local_variable_type* , le ***type d’élément*** est `dynamic`, sinon il est `object`.
*  Sinon, déterminez si le `X` type a une `GetEnumerator` méthode appropriée :
   * Effectuez une recherche de membre sur `X` le type avec `GetEnumerator` l’identificateur et aucun argument de type. Si la recherche de membre ne produit pas de correspondance, si elle génère une ambiguïté ou si elle produit une correspondance qui n’est pas un groupe de méthodes, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé d’émettre un avertissement si la recherche de membres produit tout sauf un groupe de méthodes ou aucune correspondance.
   * Exécutez la résolution de surcharge à l’aide du groupe de méthodes résultant et d’une liste d’arguments vide. Si la résolution de surcharge entraîne l’absence de méthodes applicables, provoque une ambiguïté ou produit une seule meilleure méthode, mais que cette méthode est statique ou non publique, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé d’émettre un avertissement si la résolution de surcharge produit tout sauf une méthode d’instance publique non ambiguë ou aucune méthode applicable.
   * Si le type `E` de retour de `GetEnumerator` la méthode n’est pas un type de classe, de struct ou d’interface, une erreur est générée et aucune autre étape n’est prise.
   * La recherche de membres est `E` effectuée sur avec l' `Current` identificateur et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf une propriété d’instance publique qui autorise la lecture, une erreur est générée et aucune autre étape n’est effectuée.
   * La recherche de membres est `E` effectuée sur avec l' `MoveNext` identificateur et aucun argument de type. Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf un groupe de méthodes, une erreur est générée et aucune autre étape n’est prise.
   * La résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide. Si la résolution de surcharge entraîne l’absence de méthodes applicables, entraîne une ambiguïté ou produit une seule meilleure méthode, mais cette méthode est statique ou non publique, ou son type de retour n’est `bool`pas, une erreur est générée et aucune autre étape n’est prise.
   * Le ***type*** de collection `X`est, le type d' `E` ***énumérateur*** est, et le type d' ***élément*** est le `Current` type de la propriété.

*  Sinon, recherchez une interface énumérable :
   * Si parmi tous les types `Ti` pour lesquels il existe une conversion implicite `X` de `IEnumerable<Ti>`en, il existe un type `T` unique, `T` qui n' `dynamic` est pas et pour l' `Ti` ensemble de l’autre, il existe un conversion implicite `IEnumerable<T>` de `IEnumerable<Ti>`en, le ***type de collection*** est l' `IEnumerable<T>`interface, ***le type d’énumérateur*** est `IEnumerator<T>`l’interface et le ***type d’élément*** est `T`.
   * Sinon, s’il existe plusieurs types de ce type `T`, une erreur est générée et aucune autre étape n’est effectuée.
   * Sinon, s’il existe une conversion implicite `X` de `System.Collections.IEnumerable` en interface, le ***type de collection*** est cette interface, le ***type d’énumérateur*** est l' `System.Collections.IEnumerator`interface et le ***type d’élément*** est `object`.
   * Dans le cas contraire, une erreur est générée et aucune autre étape n’est prise.

Les étapes ci-dessus, en cas de réussite, produisent sans ambiguïté `C`un type de `E` collection, un `T`type d’énumérateur et un type d’élément. Une instruction foreach de la forme
```csharp
foreach (V v in x) embedded_statement
```
est ensuite développé pour :
```csharp
{
    E e = ((C)(x)).GetEnumerator();
    try {
        while (e.MoveNext()) {
            V v = (V)(T)e.Current;
            embedded_statement
        }
    }
    finally {
        ... // Dispose e
    }
}
```

La variable `e` n’est pas visible ou accessible à l’expression `x` ou à l’instruction incorporée ou à tout autre code source du programme. La variable `v` est en lecture seule dans l’instruction incorporée. S’il n’existe pas de conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de `T` (type d’élément) en `V` (le *local_variable_type* dans l’instruction foreach), une erreur est générée et aucune autre étape n’est prise. Si `x` a la valeur `null`, une `System.NullReferenceException` est levée au moment de l’exécution.

Une implémentation est autorisée à implémenter une instruction foreach donnée différemment, par exemple pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.

La position de `v` à l’intérieur de la boucle while est importante pour la façon dont elle est capturée par toute fonction anonyme se produisant dans le *embedded_statement*.

Exemple :
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
Si `v` a été déclaré en dehors de la boucle while, il est partagé entre toutes les itérations, et sa valeur après la boucle for est la valeur `13`finale,, qui correspond à l’impression `f` de l’appel. Au lieu de cela, étant donné que chaque `v`itération a sa propre variable `f` , celle capturée par dans la première itération continue `7`à contenir la valeur, qui est celle qui sera imprimée. (Remarque : les versions antérieures C# de `v` ont été déclarées en dehors de la boucle while.)

Le corps du bloc finally est construit selon les étapes suivantes :

*  S’il existe une conversion implicite `E` de `System.IDisposable` en interface,
   *  Si `E` est un type valeur qui n’autorise pas les valeurs NULL, la clause finally est développée avec l’équivalent sémantique de :

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  Dans le cas contraire, la clause finally est étendue à l’équivalent sémantique de :

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   Hormis le `E` fait que si est un type valeur ou un paramètre de type instancié à un type valeur, le Cast `e` de `System.IDisposable` en n’entraîne pas de conversion boxing.

*  Sinon, si `E` est un type sealed, la clause finally est développée en bloc vide :

   ```csharp
   finally {
   }
   ```

*  Dans le cas contraire, la clause finally est développée en :

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   La variable `d` locale n’est pas visible ou accessible à un code utilisateur. En particulier, il n’est pas en conflit avec une autre variable dont la portée comprend le bloc finally.

L’ordre dans lequel `foreach` traverse les éléments d’un tableau est le suivant : Pour les tableaux unidimensionnels, les éléments sont parcourus dans un ordre d’index `0` plus important, en `Length - 1`commençant par l’index et en terminant par l’index. Pour les tableaux multidimensionnels, les éléments sont parcourus de telle sorte que les index de la dimension la plus à droite soient augmentés en premier, puis la dimension gauche suivante, et ainsi de suite vers la gauche.

L’exemple suivant imprime chaque valeur dans un tableau à deux dimensions, dans l’ordre des éléments :
```csharp
using System;

class Test
{
    static void Main() {
        double[,] values = {
            {1.2, 2.3, 3.4, 4.5},
            {5.6, 6.7, 7.8, 8.9}
        };

        foreach (double elementValue in values)
            Console.Write("{0} ", elementValue);

        Console.WriteLine();
    }
}
```
La sortie produite est la suivante :
```console
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

Dans l’exemple
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
le type de `n` est déduit comme étant `int`, le type d’élément de `numbers`.

## <a name="jump-statements"></a>Instructions de saut

Les instructions de saut transfèrent le contrôle de manière inconditionnelle.

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

L’emplacement vers lequel une instruction de saut transfère le contrôle est appelé la ***cible*** de l’instruction de saut.

Lorsqu’une instruction de saut se produit dans un bloc et que la cible de cette instruction de saut est en dehors de ce bloc, l’instruction de saut est dite pour ***quitter*** le bloc. Alors qu’une instruction de saut peut transférer le contrôle hors d’un bloc, elle ne peut jamais transférer le contrôle dans un bloc.

L’exécution d’instructions de saut est compliquée par la présence `try` d’instructions intermédiaires. En l’absence de ces `try` instructions, une instruction de saut transfère de manière inconditionnelle le contrôle de l’instruction de saut à sa cible. En présence de ces `try` instructions intervenues, l’exécution est plus complexe. Si l’instruction de saut quitte un ou plusieurs `try` blocs avec des `finally` blocs associés, le contrôle `finally` est initialement transféré au bloc de l' `try` instruction la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.

Dans l’exemple
```csharp
using System;

class Test
{
    static void Main() {
        while (true) {
            try {
                try {
                    Console.WriteLine("Before break");
                    break;
                }
                finally {
                    Console.WriteLine("Innermost finally block");
                }
            }
            finally {
                Console.WriteLine("Outermost finally block");
            }
        }
        Console.WriteLine("After break");
    }
}
```
les `finally` blocs associés à deux `try` instructions sont exécutés avant que le contrôle soit transféré à la cible de l’instruction de saut.

La sortie produite est la suivante :
```console
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a>Instruction Break

L' `break` instruction quitte l’instruction,,, ou `switch` `for` `while` `do`englobantela plusproche`foreach` .

```antlr
break_statement
    : 'break' ';'
    ;
```

La cible d’une `break` instruction est le point de terminaison de l’instruction, `while`, `switch` `do` `for`, ou `foreach` englobante la plus proche. Si une `break` instruction n’est pas entourée `while`d' `do`une `for` `switch`instruction, `foreach` ,, ou, une erreur de compilation se produit.

Lorsque plusieurs `switch`instructions `while`, `do`, `break` ,, ou`foreach` sont imbriquées les unes dans les autres, une instruction s’applique uniquement à l’instruction la plus profonde. `for` Pour transférer le contrôle entre plusieurs niveaux d’imbrication, `goto` une instruction ([instruction goto](statements.md#the-goto-statement)) doit être utilisée.

Une `break` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)). Lorsqu’une `break` instruction se produit dans `finally` un bloc, la cible de `break` l’instruction doit se trouver dans `finally` le même bloc ; sinon, une erreur de compilation se produit.

Une `break` instruction est exécutée comme suit :

*  Si l' `break` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.
*  Le contrôle est transféré à la cible de `break` l’instruction.

Étant donné `break` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `break` terminaison d’une instruction n’est jamais accessible.

### <a name="the-continue-statement"></a>Instruction continue

L' `continue` instruction démarre une nouvelle itération de l’instruction `do`,, `for`ou `while` `foreach` englobante la plus proche.

```antlr
continue_statement
    : 'continue' ';'
    ;
```

La cible d’une `continue` instruction est le point de terminaison de l’instruction incorporée de l’instruction englobante `do`, `for`, ou `foreach` englobante `while`la plus proche. Si une `continue` instruction n’est pas entourée `do`d' `for`une `while`instruction `foreach` ,, ou, une erreur de compilation se produit.

Lorsque plusieurs `while`instructions `do`, `for`,, `foreach` ou sont imbriquées les unes dans les autres `continue` , une instruction s’applique uniquement à l’instruction la plus profonde. Pour transférer le contrôle entre plusieurs niveaux d’imbrication, `goto` une instruction ([instruction goto](statements.md#the-goto-statement)) doit être utilisée.

Une `continue` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)). Lorsqu’une `continue` instruction se produit dans `finally` un bloc, la cible de `continue` l’instruction doit se trouver dans `finally` le même bloc ; sinon, une erreur de compilation se produit.

Une `continue` instruction est exécutée comme suit :

*  Si l' `continue` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.
*  Le contrôle est transféré à la cible de `continue` l’instruction.

Étant donné `continue` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `continue` terminaison d’une instruction n’est jamais accessible.

### <a name="the-goto-statement"></a>Instruction goto

L' `goto` instruction transfère le contrôle à une instruction qui est marquée par une étiquette.

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

La cible d’une `goto` instruction d' *identificateur* est l’instruction étiquetée avec l’étiquette donnée. Si une étiquette portant le nom spécifié n’existe pas dans le membre de fonction actuel, ou si `goto` l’instruction n’est pas dans la portée de l’étiquette, une erreur de compilation se produit. Cette règle autorise l’utilisation d’une `goto` instruction pour transférer le contrôle hors d’une portée imbriquée, mais pas dans une portée imbriquée. Dans l’exemple
```csharp
using System;

class Test
{
    static void Main(string[] args) {
        string[,] table = {
            {"Red", "Blue", "Green"},
            {"Monday", "Wednesday", "Friday"}
        };

        foreach (string str in args) {
            int row, colm;
            for (row = 0; row <= 1; ++row)
                for (colm = 0; colm <= 2; ++colm)
                    if (str == table[row,colm])
                         goto done;

            Console.WriteLine("{0} not found", str);
            continue;
    done:
            Console.WriteLine("Found {0} at [{1}][{2}]", str, row, colm);
        }
    }
}
```
une `goto` instruction est utilisée pour transférer le contrôle hors d’une portée imbriquée.

La cible d’une `goto case` instruction est la liste d’instructions dans l’instruction immédiatement `switch` englobante ([l’instruction switch](statements.md#the-switch-statement)), qui contient `case` une étiquette avec la valeur de constante donnée. Si l’instruction `goto case` n’est pas entourée d’une instruction `switch`, si le *constant_expression* n’est pas implicitement convertible ([conversion implicite](conversions.md#implicit-conversions)) en type gouvernant de l’instruction `switch` englobante la plus proche, ou si le le plus proche englobant l’instruction `switch` ne contient pas d’étiquette `case` avec la valeur de constante donnée, une erreur de compilation se produit.

La cible d’une `goto default` instruction est la liste d’instructions dans l’instruction immédiatement `switch` englobante ([l’instruction switch](statements.md#the-switch-statement)), qui contient `default` une étiquette. Si l' `goto default` instruction n’est pas entourée d’une `switch` instruction, ou si l’instruction `switch` englobante la plus proche `default` ne contient pas d’étiquette, une erreur de compilation se produit.

Une `goto` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)). Lorsqu’une `goto` instruction se produit dans `finally` un bloc, la cible de `goto` l’instruction doit se trouver dans `finally` le même bloc, sinon une erreur de compilation se produit.

Une `goto` instruction est exécutée comme suit :

*  Si l' `goto` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.
*  Le contrôle est transféré à la cible de `goto` l’instruction.

Étant donné `goto` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `goto` terminaison d’une instruction n’est jamais accessible.

### <a name="the-return-statement"></a>Instruction return

L' `return` instruction retourne le contrôle à l’appelant actuel de la fonction dans laquelle `return` l’instruction apparaît.

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

Une `return` instruction sans expression ne peut être utilisée que dans un membre de fonction qui ne calcule pas de valeur, autrement dit, une méthode avec le type de résultat (corps `void`de[méthode](classes.md#method-body)), l' `set` accesseur d’une propriété ou `add` d’un indexeur, le les `remove` accesseurs et d’un événement, un constructeur d’instance, un constructeur statique ou un destructeur.

Une `return` instruction avec une expression ne peut être utilisée que dans un membre de fonction qui calcule une valeur, autrement dit, une méthode avec un type de résultat non void `get` , l’accesseur d’une propriété ou un indexeur ou un opérateur défini par l’utilisateur. Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister du type de l’expression vers le type de retour de la fonction membre conteneur.

Les instructions Return peuvent également être utilisées dans le corps d’expressions de fonction anonymes ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) et participer à la détermination des conversions qui existent pour ces fonctions.

Il s’agit d’une erreur de compilation pour `return` qu’une instruction apparaisse `finally` dans un bloc ([instruction try](statements.md#the-try-statement)).

Une `return` instruction est exécutée comme suit :

*  Si l' `return` instruction spécifie une expression, l’expression est évaluée et la valeur résultante est convertie dans le type de retour de la fonction conteneur par une conversion implicite. Le résultat de la conversion devient la valeur de résultat produite par la fonction.
*  Si l' `return` instruction est entourée d’un `try` ou `catch` de plusieurs blocs `finally` ou avec des blocs associés, le contrôle `finally` est initialement transféré au `try` bloc de l’instruction la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions englobantes aient été exécutés.
*  Si la fonction conteneur n’est pas une fonction Async, le contrôle est retourné à l’appelant de la fonction conteneur avec la valeur de résultat, le cas échéant.
*  Si la fonction conteneur est une fonction Async, le contrôle est retourné à l’appelant actuel et la valeur de résultat, le cas échéant, est enregistrée dans la tâche de retour, comme décrit dans ([interfaces d’énumération](classes.md#enumerator-interfaces)).

Étant donné `return` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `return` terminaison d’une instruction n’est jamais accessible.

### <a name="the-throw-statement"></a>Instruction throw

L' `throw` instruction lève une exception.

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

Une `throw` instruction avec une expression génère la valeur produite par l’évaluation de l’expression. L’expression doit indiquer une valeur du type `System.Exception`de classe, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de `System.Exception` type ayant (ou une sous-classe) comme classe de base effective. Si l’évaluation de l’expression `null`produit, `System.NullReferenceException` une exception est levée à la place.

Une `throw` instruction sans expression ne peut être utilisée que dans un `catch` bloc, auquel cas cette instruction lève à nouveau l’exception qui est actuellement gérée par ce `catch` bloc.

Étant donné `throw` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `throw` terminaison d’une instruction n’est jamais accessible.

Quand une exception est levée, le contrôle est transféré à la `catch` première clause dans une instruction `try` englobante qui peut gérer l’exception. Le processus qui a lieu à partir du point de l’exception levée jusqu’au point de transfert du contrôle à un gestionnaire d’exceptions approprié est appelé ***propagation des exceptions***. La propagation d’une exception consiste à évaluer à plusieurs reprises les étapes suivantes jusqu' `catch` à ce qu’une clause qui corresponde à l’exception soit trouvée. Dans cette description, le ***point de levée*** est initialement l’emplacement où l’exception est levée.

*  Dans le membre de fonction actuel, `try` chaque instruction qui encadre le point d’exception est examinée. Pour chaque instruction `S`, en commençant par l' `try` instruction la plus profonde et en `try` terminant par l’instruction la plus extérieure, les étapes suivantes sont évaluées :

   * Si le `try` bloc de `S` encadre le point de levée et si S a une ou plusieurs `catch` clauses, les `catch` clauses sont examinées par ordre d’apparition pour trouver un gestionnaire approprié pour l’exception, conformément aux règles spécifiées dans Section [de l’instruction try](statements.md#the-try-statement). Si une clause `catch` correspondante est trouvée, la propagation de l’exception est terminée en transférant le contrôle au bloc `catch` de cette clause.

   * Sinon, si le `try` bloc ou un `catch` bloc de `S` englobe le point de levée et si `S` a un `finally` bloc, le contrôle est transféré au `finally` bloc. Si le `finally` bloc lève une autre exception, le traitement de l’exception actuelle est terminé. Dans le cas contraire, lorsque le contrôle atteint le `finally` point de terminaison du bloc, le traitement de l’exception actuelle est poursuivi.

*  Si un gestionnaire d’exceptions n’a pas été trouvé dans l’appel de la fonction en cours, l’appel de la fonction se termine et l’un des éléments suivants se produit :

   * Si la fonction active est non Async, les étapes ci-dessus sont répétées pour l’appelant de la fonction avec un point d’exception correspondant à l’instruction à partir de laquelle la fonction membre a été appelée.

   * Si la fonction active est Async et retourne une tâche, l’exception est enregistrée dans la tâche de retour, qui est placée dans un état Faulted ou Cancelled, comme décrit dans [interfaces d’énumérateur](classes.md#enumerator-interfaces).

   * Si la fonction active est Async et void-retournant, le contexte de synchronisation du thread actuel est notifié comme décrit dans [interfaces Enumerable](classes.md#enumerable-interfaces).

*  Si le traitement des exceptions met fin à tous les appels de membre de fonction dans le thread actuel, ce qui indique que le thread n’a aucun gestionnaire pour l’exception, le thread se termine lui-même. L’impact d’une telle terminaison est défini par l’implémentation.

## <a name="the-try-statement"></a>Instruction try

L' `try` instruction fournit un mécanisme permettant d’intercepter les exceptions qui se produisent pendant l’exécution d’un bloc. En outre, `try` l’instruction offre la possibilité de spécifier un bloc de code qui est toujours exécuté lorsque le contrôle `try` quitte l’instruction.

```antlr
try_statement
    : 'try' block catch_clause+
    | 'try' block finally_clause
    | 'try' block catch_clause+ finally_clause
    ;

catch_clause
    : 'catch' exception_specifier? exception_filter?  block
    ;

exception_specifier
    : '(' type identifier? ')'
    ;

exception_filter
    : 'when' '(' expression ')'
    ;

finally_clause
    : 'finally' block
    ;
```

Il existe trois formes d' `try` instructions possibles :

*  Bloc suivi d’un ou de plusieurs `catch` blocs. `try`
*  Bloc suivi d’un `finally` bloc. `try`
*  Bloc suivi d’un ou de plusieurs `catch` blocs suivis d' `finally` un bloc. `try`

Quand une clause `catch` spécifie un *exception_specifier*, le type doit être `System.Exception`, un type qui dérive de `System.Exception` ou un type de paramètre de type qui a `System.Exception` (ou une sous-classe de ce type) comme classe de base effective.

Quand une clause `catch` spécifie à la fois un *exception_specifier* avec un *identificateur*, une ***variable d’exception*** du nom et du type donnés est déclarée. La variable d’exception correspond à une variable locale avec une étendue qui s’étend `catch` sur la clause. Pendant l’exécution du *exception_filter* et du *bloc*, la variable d’exception représente l’exception actuellement gérée. À des fins de vérification de l’attribution définie, la variable d’exception est considérée comme assignée de manière définitive dans toute son étendue.

À moins `catch` qu’une clause comprenne un nom de variable d’exception, il est impossible d’accéder à l’objet `catch` d’exception dans le filtre et le bloc.

Une clause `catch` qui ne spécifie pas de *exception_specifier* est appelée clause générale `catch`.

Certains langages de programmation peuvent prendre en charge des exceptions qui ne peuvent pas être représentées en tant qu’objet dérivé de `System.Exception`, bien que ces exceptions ne puissent jamais être générées par C# le code. Une clause `catch` générale peut être utilisée pour intercepter ces exceptions. Par conséquent, une `catch` clause générale est sémantiquement différente d’une clause qui spécifie le type `System.Exception`, dans le cas où la première peut également intercepter des exceptions d’autres langages.

Afin de localiser un gestionnaire pour une exception, `catch` les clauses sont examinées dans l’ordre lexical. Si une `catch` clause spécifie un type mais aucun filtre d’exception, il s’agit d’une erreur de compilation `catch` pour une clause ultérieure `try` dans la même instruction pour spécifier un type qui est le même que, ou qui est dérivé de ce type. Si une `catch` clause ne spécifie aucun type et aucun filtre, il doit s' `catch` agir de la `try` dernière clause pour cette instruction.

Dans un `catch` bloc, une `throw` instruction ([instruction throw](statements.md#the-throw-statement)) sans expression peut être utilisée pour lever à nouveau l’exception interceptée par le `catch` bloc. Les assignations à une variable d’exception ne modifient pas l’exception qui est levée une nouvelle fois.

Dans l’exemple
```csharp
using System;

class Test
{
    static void F() {
        try {
            G();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in F: " + e.Message);
            e = new Exception("F");
            throw;                // re-throw
        }
    }

    static void G() {
        throw new Exception("G");
    }

    static void Main() {
        try {
            F();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in Main: " + e.Message);
        }
    }
}
```
la méthode `F` intercepte une exception, écrit des informations de diagnostic dans la console, modifie la variable d’exception et lève à nouveau l’exception. L’exception qui est levée une nouvelle fois est l’exception d’origine, de sorte que la sortie produite est :
```console
Exception in F: G
Exception in Main: G
```

Si le premier bloc catch avait été `e` levé au lieu de lever à nouveau l’exception actuelle, la sortie produite serait la suivante :
```console
Exception in F: G
Exception in Main: F
```

Il s’agit d’une erreur de compilation pour `break`une `continue`instruction, `goto` ou pour transférer le contrôle hors d' `finally` un bloc. Quand une `break`instruction `continue`, ou `goto` se trouve dans un `finally` bloc, la cible de l’instruction doit se trouver dans le `finally` même bloc, sinon une erreur de compilation se produit.

Il s’agit d’une erreur de compilation pour `return` qu’une instruction se produise dans un `finally` bloc.

Une `try` instruction est exécutée comme suit :

*  Le `try` contrôle est transféré au bloc.
*  Quand et si le contrôle atteint le point de terminaison `try` du bloc :
   *  Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.
   *  Le contrôle est transféré au point de terminaison de `try` l’instruction.

*  Si une exception est propagée à l' `try` instruction pendant l’exécution `try` du bloc :
   *  Les `catch` clauses, le cas échéant, sont examinées par ordre d’apparition pour trouver un gestionnaire approprié pour l’exception. Si une `catch` clause ne spécifie pas de type, ou spécifie le type d’exception ou un type de base du type d’exception :
      *  Si la `catch` clause déclare une variable d’exception, l’objet exception est assigné à la variable d’exception.
      *  Si la `catch` clause déclare un filtre d’exception, le filtre est évalué. Si la valeur `false`est, la clause catch n’est pas une correspondance, et la recherche se poursuit à travers `catch` toutes les clauses suivantes pour un gestionnaire approprié.
      *  Dans le cas `catch` contraire, la clause est considérée comme une correspondance et le contrôle est `catch` transféré au bloc correspondant.
      *  Quand et si le contrôle atteint le point de terminaison `catch` du bloc :
         * Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.
         * Le contrôle est transféré au point de terminaison de `try` l’instruction.
      *  Si une exception est propagée à l' `try` instruction pendant l’exécution `catch` du bloc :
         *  Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.
         *  L’exception est propagée à l’instruction englobante `try` suivante.
   *  Si l' `try` instruction n’a `catch` pas de clauses ou `catch` si aucune clause ne correspond à l’exception :
      *  Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.
      *  L’exception est propagée à l’instruction englobante `try` suivante.

Les instructions d’un `finally` bloc sont toujours exécutées lorsque le contrôle `try` quitte une instruction. Cela est vrai que le transfert de contrôle se produit suite à une exécution normale, en raison de l’exécution d' `break`une `continue`instruction `goto`,, `return` ou, ou suite à la `try` propagation d’une exception à partir de gestion.

Si une exception est levée pendant l’exécution d' `finally` un bloc et n’est pas interceptée dans le même bloc finally, l’exception est propagée à l' `try` instruction englobante suivante. Si une autre exception était en cours de propagation, cette exception est perdue. Le processus de propagation d’une exception est abordé plus en détail dans la description `throw` de l’instruction ([instruction throw](statements.md#the-throw-statement)).

Le `try` bloc d’une `try` instruction est accessible si l' `try` instruction est accessible.

Un `catch` bloc `try` d’instruction est accessible si l’instruction est accessible. `try`

Le `finally` bloc d’une `try` instruction est accessible si l' `try` instruction est accessible.

Le point de terminaison d' `try` une instruction est accessible si les deux conditions suivantes sont remplies :

*  Le point de terminaison du `try` bloc est accessible ou le point de terminaison d’au moins un `catch` bloc est accessible.
*  Si un `finally` bloc est présent, le point `finally` de terminaison du bloc est accessible.

## <a name="the-checked-and-unchecked-statements"></a>Instructions cochées et non vérifiées

Les `checked` instructions `unchecked` et sont utilisées pour contrôler le ***contexte*** de contrôle de dépassement de capacité pour les opérations arithmétiques de type intégral et les conversions.

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

L' `checked` instruction entraîne l’évaluation de toutes les expressions du *bloc* dans un contexte vérifié, et l' `unchecked` instruction entraîne l’évaluation de toutes les expressions du *bloc* dans un contexte non vérifié.

Les `checked` instructions `unchecked` et`checked` sont équivalentes de manière précise `unchecked` aux opérateurs et ([opérateurs vérifiés et](expressions.md#the-checked-and-unchecked-operators)non vérifiés), sauf qu’ils opèrent sur des blocs au lieu d’expressions.

## <a name="the-lock-statement"></a>Instruction lock

L' `lock` instruction obtient le verrou d’exclusion mutuelle pour un objet donné, exécute une instruction, puis libère le verrou.

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

L’expression d’une instruction `lock` doit désigner une valeur d’un type connu comme étant un *reference_type*. Aucune conversion boxing implicite ([conversions boxing](conversions.md#boxing-conversions)) n’est jamais effectuée pour l’expression d’une instruction `lock`. par conséquent, il s’agit d’une erreur au moment de la compilation pour que l’expression désigne une valeur d’un *Value_type*.

Une `lock` déclaration de la forme
```csharp
lock (x) ...
```
où `x` est une expression d’un *reference_type*, est exactement équivalent à
```csharp
bool __lockWasTaken = false;
try {
    System.Threading.Monitor.Enter(x, ref __lockWasTaken);
    ...
}
finally {
    if (__lockWasTaken) System.Threading.Monitor.Exit(x);
}
```
sauf que `x` n’est évalué qu’une seule fois.

Lorsqu’un verrou d’exclusion mutuelle est maintenu, le code qui s’exécute dans le même thread d’exécution peut également obtenir et libérer le verrou. Toutefois, le code qui s’exécute dans d’autres threads ne peut pas obtenir le verrou tant que le verrou n’est pas libéré.

Le `System.Type` verrouillage des objets pour synchroniser l’accès aux données statiques n’est pas recommandé. Un autre code peut verrouiller le même type, ce qui peut entraîner un interblocage. Une meilleure approche consiste à synchroniser l’accès aux données statiques en verrouillant un objet statique privé. Exemple :
```csharp
class Cache
{
    private static readonly object synchronizationObject = new object();

    public static void Add(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }

    public static void Remove(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }
}
```

## <a name="the-using-statement"></a>Instruction using

L' `using` instruction obtient une ou plusieurs ressources, exécute une instruction, puis libère la ressource.

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

Une ***ressource*** est une classe ou un struct qui implémente `System.IDisposable`, qui comprend une méthode unique sans paramètre nommée. `Dispose` Le code qui utilise une ressource peut appeler `Dispose` pour indiquer que la ressource n’est plus nécessaire. Si `Dispose` n’est pas appelé, la suppression automatique finit par se produire à la suite de garbage collection.

Si la forme de *resource_acquisition* est *local_variable_declaration* , le type de *local_variable_declaration* doit être `dynamic` ou un type qui peut être implicitement converti en `System.IDisposable`. Si la forme de *resource_acquisition* est *expression* , cette expression doit être implicitement convertible en `System.IDisposable`.

Les variables locales déclarées dans un *resource_acquisition* sont en lecture seule et doivent inclure un initialiseur. Une erreur de compilation se produit si l’instruction incorporée tente de modifier ces variables locales (par le biais `++` de `--` l’assignation ou des opérateurs et), d’en prendre l’adresse `ref` ou `out` de les passer en tant que paramètres ou.

Une `using` instruction est traduite en trois parties : l’acquisition, l’utilisation et la suppression. L’utilisation de la ressource est implicitement incluse dans `try` une instruction qui comprend `finally` une clause. Cette `finally` clause supprime la ressource. Si une `null` ressource est acquise, aucun appel à `Dispose` n’est effectué et aucune exception n’est levée. Si la ressource est de type `dynamic` , elle est convertie dynamiquement via une conversion dynamique implicite ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions)) `IDisposable` en lors de l’acquisition afin de garantir la réussite de la conversion avant l’utilisation et Evacuation.

Une `using` déclaration de la forme
```csharp
using (ResourceType resource = expression) statement
```
correspond à l’une des trois expansions possibles. Lorsque `ResourceType` est un type valeur qui n’autorise pas les valeurs NULL, l’expansion est
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        ((IDisposable)resource).Dispose();
    }
}
```

Dans le cas `ResourceType` contraire, quand est un type valeur Nullable ou un type `dynamic`référence autre que, le développement est
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        if (resource != null) ((IDisposable)resource).Dispose();
    }
}
```

Dans le cas `ResourceType` contraire `dynamic`, lorsque est, l’expansion est
```csharp
{
    ResourceType resource = expression;
    IDisposable d = (IDisposable)resource;
    try {
        statement;
    }
    finally {
        if (d != null) d.Dispose();
    }
}
```

Dans les deux expansions `resource` , la variable est en lecture seule dans l’instruction incorporée `d` , et la variable est inaccessible dans, et invisible à, l’instruction incorporée.

Une implémentation de est autorisée à implémenter une instruction using donnée différemment, par exemple pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.

Une `using` déclaration de la forme
```csharp
using (expression) statement
```
a les mêmes trois expansions possibles. Dans ce cas `ResourceType` , est implicitement le type au moment de la `expression`compilation du, s’il en a un. Dans le cas `IDisposable` contraire, l’interface elle-même est utilisée comme. `ResourceType` La `resource` variable est inaccessible dans, et invisible à, l’instruction incorporée.

Quand un *resource_acquisition* prend la forme d’un *local_variable_declaration*, il est possible d’acquérir plusieurs ressources d’un type donné. Une `using` déclaration de la forme
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
équivaut précisément à une séquence d' `using` instructions imbriquées :
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

L’exemple ci-dessous crée un `log.txt` fichier nommé et écrit deux lignes de texte dans le fichier. L’exemple ouvre ensuite le même fichier pour la lecture et copie les lignes de texte contenues dans la console.
```csharp
using System;
using System.IO;

class Test
{
    static void Main() {
        using (TextWriter w = File.CreateText("log.txt")) {
            w.WriteLine("This is line one");
            w.WriteLine("This is line two");
        }

        using (TextReader r = File.OpenText("log.txt")) {
            string s;
            while ((s = r.ReadLine()) != null) {
                Console.WriteLine(s);
            }

        }
    }
}
```

Étant donné `TextWriter` que `TextReader` les classes et `IDisposable` implémentent l’interface, l' `using` exemple peut utiliser des instructions pour s’assurer que le fichier sous-jacent est correctement fermé après les opérations d’écriture ou de lecture.

## <a name="the-yield-statement"></a>Instruction yield

L' `yield` instruction est utilisée dans un bloc itérateur ([blocs](statements.md#blocks)) pour produire une valeur à l’objet énumérateur ([objets énumérateur](classes.md#enumerator-objects)) ou à un objet énumérable ([objets énumérables](classes.md#enumerable-objects)) d’un itérateur ou pour signaler la fin de l’itération.

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

`yield`n’est pas un mot réservé ; Il a une signification spéciale uniquement lorsqu’il est utilisé `return` juste `break` avant un mot clé ou. Dans d’autres contextes `yield` , peut être utilisé comme identificateur.

Il existe plusieurs restrictions sur l’emplacement `yield` où une instruction peut apparaître, comme décrit dans l’exemple suivant.

*  Une erreur de compilation pour une instruction `yield` (de l’une des deux formes) s’affiche en dehors d’un *method_body*, *operator_body* ou *accessor_body*
*  Il s’agit d’une erreur de compilation pour `yield` qu’une instruction (de l’une des deux formes) apparaisse dans une fonction anonyme.
*  Il s’agit d’une erreur de compilation pour `yield` qu’une instruction (de l’une des deux formes `finally` ) apparaisse `try` dans la clause d’une instruction.
*  Il s’agit d’une erreur de compilation pour `yield return` qu’une instruction apparaisse n' `try` importe où dans une `catch` instruction qui contient des clauses.

L’exemple suivant montre des utilisations valides et non `yield` valides d’instructions.

```csharp
delegate IEnumerable<int> D();

IEnumerator<int> GetEnumerator() {
    try {
        yield return 1;        // Ok
        yield break;           // Ok
    }
    finally {
        yield return 2;        // Error, yield in finally
        yield break;           // Error, yield in finally
    }

    try {
        yield return 3;        // Error, yield return in try...catch
        yield break;           // Ok
    }
    catch {
        yield return 4;        // Error, yield return in try...catch
        yield break;           // Ok
    }

    D d = delegate { 
        yield return 5;        // Error, yield in an anonymous function
    }; 
}

int MyMethod() {
    yield return 1;            // Error, wrong return type for an iterator block
}
```

Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l' `yield return` expression dans l’instruction vers le type Yield ([type yield](classes.md#yield-type)) de l’itérateur.

Une `yield return` instruction est exécutée comme suit :

*  L’expression donnée dans l’instruction est évaluée, implicitement convertie en type yield et assignée à la `Current` propriété de l’objet énumérateur.
*  L’exécution du bloc itérateur est suspendue. Si l' `yield return` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés ne sont pas exécutés pour l’instant.
*  La `MoveNext` méthode de l’objet énumérateur retourne `true` à son appelant, ce qui indique que l’objet énumérateur a réussi à avancer jusqu’à l’élément suivant.

Le prochain appel à la méthode de `MoveNext` l’objet énumérateur reprend l’exécution du bloc itérateur à partir de l’emplacement où il a été suspendu pour la dernière fois.

Une `yield break` instruction est exécutée comme suit :

*  Si l' `yield break` instruction est entourée d’un `try` ou de plusieurs `finally` blocs avec des `finally` blocs associés, le contrôle est initialement transféré au `try` bloc de l’instruction la plus profonde. Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante. Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions englobantes aient été exécutés.
*  Le contrôle est retourné à l’appelant du bloc Iterator. Il s’agit de `MoveNext` la méthode `Dispose` ou de la méthode de l’objet énumérateur.

Étant donné `yield break` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `yield break` terminaison d’une instruction n’est jamais accessible.
