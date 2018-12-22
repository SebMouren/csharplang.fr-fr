# <a name="statements"></a>Instructions

C# fournit une variété d’instructions. La plupart de ces instructions seront familière aux développeurs qui ont déjà programmé en C et C++.

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

Le *embedded_statement* non terminal est utilisé pour les instructions qui s’affichent dans les autres instructions. L’utilisation de *embedded_statement* plutôt que *instruction* exclut l’utilisation des instructions de déclaration et d’instructions étiquetées dans ces contextes. L’exemple
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
génère une erreur de compilation, car un `if` instruction nécessite un *embedded_statement* au lieu d’un *instruction* pour ses si branche. Si ce code était autorisé, puis la variable `i` doit être déclarée, mais elle ne peut jamais être utilisée. Notez, cependant, qui en plaçant `i`de déclaration dans un bloc, l’exemple est valide.

## <a name="end-points-and-reachability"></a>Points de terminaison et accessibilité

Chaque instruction possède un ***point de terminaison***. En d’autres termes, le point de terminaison d’une instruction est l’emplacement qui suit immédiatement l’instruction. Les règles de l’exécution des instructions composées (les instructions qui contiennent des instructions incorporées) spécifient l’action à entreprendre lorsque le contrôle atteint le point de terminaison d’une instruction incorporée. Par exemple, lorsque le contrôle atteint le point de terminaison d’une instruction dans un bloc, le contrôle est transféré à l’instruction suivante dans le bloc.

Si une instruction peut éventuellement être atteinte par l’exécution, l’instruction est dite ***accessible***. À l’inverse, s’il n’existe aucun risque qu’une instruction doit être exécutée, l’instruction est dite ***inaccessible***.

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
le deuxième appel de `Console.WriteLine` est inaccessible, car il n’est pas possible que l’instruction doit être exécutée.

Un avertissement est émis si le compilateur détermine qu’une instruction est inaccessible. Il est spécifiquement pas une erreur pour une instruction comme inaccessibles.

Pour déterminer si une instruction particulière ou un point de terminaison est accessible, le compilateur effectue une analyse de flux selon les règles d’accessibilité définies pour chaque instruction. L’analyse de flux prend en compte les valeurs des expressions constantes ([expressions constantes](expressions.md#constant-expressions)) qui contrôlent le comportement des instructions, mais les valeurs possibles des expressions non constantes ne sont pas considérés comme. En d’autres termes, à des fins d’analyse de flux de contrôle, une expression non constante d’un type donné est considérée comme ayant toutes les valeurs possibles de ce type.

Dans l’exemple
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
l’expression booléenne de le `if` instruction est une expression constante, car les deux opérandes de le `==` opérateur sont des constantes. Comme l’expression constante est évaluée au moment de la compilation, qui produit la valeur `false`, le `Console.WriteLine` appel est considéré comme étant inaccessible. Toutefois, si `i` est modifié pour être une variable locale
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
le `Console.WriteLine` appel est considéré comme accessible, même si en réalité, il sera jamais exécuté.

Le *bloc* d’une fonction membre est toujours considéré comme accessible. En évaluant successivement les règles d’accessibilité de chaque instruction dans un bloc, l’accessibilité d’une instruction peut être déterminée.

Dans l’exemple
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
l’accessibilité de la seconde `Console.WriteLine` est déterminée comme suit :

*  La première `Console.WriteLine` instruction d’expression est accessible, car le bloc de la `F` méthode est accessible.
*  Le point de terminaison de la première `Console.WriteLine` instruction d’expression est accessible, car cette instruction est accessible.
*  Le `if` instruction est accessible, car le point de la fin de la première `Console.WriteLine` instruction d’expression est accessible.
*  La seconde `Console.WriteLine` instruction d’expression est accessible, car l’expression booléenne de le `if` instruction n’a pas la valeur de constante `false`.

Il existe deux situations dans lesquelles il est une erreur de compilation pour le point de terminaison d’une instruction pour être accessible :

*  Étant donné que la `switch` instruction ne permet pas d’une section de commutateur « fourre-tout » à la section switch suivante, c’est une erreur de compilation pour le point de terminaison de la liste d’instructions d’une section de commutateur pour être accessible. Si cette erreur se produit, il est généralement une indication qui un `break` instruction est manquante.
*  Il est une erreur de compilation pour le point de terminaison du bloc d’une fonction membre qui calcule une valeur pour être accessible. Si cette erreur se produit, il est généralement une indication qui un `return` instruction est manquante.

## <a name="blocks"></a>Blocs

Un *bloc* autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée.

```antlr
block
    : '{' statement_list? '}'
    ;
```

Un *bloc* se compose d’un texte facultatif *statement_list* ([instruction répertorie](statements.md#statement-lists)), placé entre accolades. Si la liste d’instructions est omise, le bloc est dit être vide.

Un bloc peut contenir des instructions de déclaration ([les instructions de déclaration](statements.md#declaration-statements)). La portée d’une variable locale ou constante déclarée dans un bloc est le bloc.

Un bloc est exécuté comme suit :

*  Si le bloc est vide, le contrôle est transféré au point de terminaison du bloc.
*  Si le bloc n’est pas vide, le contrôle est transféré à la liste d’instructions. Lorsqu’et si le contrôle atteint le point de terminaison de la liste d’instructions, le contrôle est transféré au point de terminaison du bloc.

La liste d’instructions d’un bloc est accessible si le bloc lui-même est accessible.

Le point de terminaison d’un bloc est accessible si le bloc est vide ou si le point de terminaison de la liste d’instructions est accessible.

Un *bloc* qui contient un ou plusieurs `yield` instructions ([l’instruction yield](statements.md#the-yield-statement)) est appelé un bloc itérateur. Blocs itérateurs sont utilisés pour implémenter des fonctions membres en tant qu’itérateurs ([itérateurs](classes.md#iterators)). Certaines restrictions s’appliquent aux blocs d’itérateur :

*  Il s’agit d’une erreur lors de la compilation pour un `return` instruction apparaissent dans un bloc itérateur (mais `yield return` instructions sont autorisées).
*  Il s’agit d’une erreur de compilation pour un bloc itérateur contenir un contexte unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)). Un bloc itérateur définit toujours un contexte sécurisé, même lorsque sa déclaration est imbriquée dans un contexte unsafe.

### <a name="statement-lists"></a>Listes d’instructions

Un ***liste d’instructions*** se compose d’une ou plusieurs instructions écrites dans l’ordre. Listes d’instructions se produisent dans *bloc*s ([blocs](statements.md#blocks)) et dans *switch_block*s ([l’instruction switch](statements.md#the-switch-statement)).

```antlr
statement_list
    : statement+
    ;
```

Une liste d’instructions est exécutée en transférant le contrôle à la première instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’une instruction, le contrôle est transféré à l’instruction suivante. Lorsqu’et si le contrôle atteint le point de terminaison de la dernière instruction, le contrôle est transféré au point de terminaison de la liste d’instructions.

Une instruction dans une liste d’instructions est accessible si au moins une des opérations suivantes est vraie :

*  L’instruction est la première instruction et la liste d’instructions est accessible.
*  Le point de terminaison de l’instruction précédente est accessible.
*  L’instruction est une instruction étiquetée et l’étiquette est référencé par un accessible `goto` instruction.

Le point de terminaison d’une liste d’instructions est accessible si le point de terminaison de la dernière instruction dans la liste est accessible.

## <a name="the-empty-statement"></a>Instruction vide

Un *empty_statement* ne fait rien.

```antlr
empty_statement
    : ';'
    ;
```

Une instruction vide est utilisée lorsqu’il n’y a aucune opération à effectuer dans un contexte où une instruction est requise.

Exécution d’une instruction vide transfère simplement le contrôle au point de terminaison de l’instruction. Par conséquent, le point de terminaison d’une instruction vide est accessible si l’instruction vide est accessible.

Une instruction vide peut être utilisée lorsque vous écrivez un `while` instruction avec un corps null :
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

En outre, une instruction vide peut être utilisée pour déclarer une étiquette juste avant la fermeture «`}`» d’un bloc :
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a>Instructions étiquetées

Un *labeled_statement* autorise une instruction pour être préfixé par une étiquette. Instructions étiquetées sont autorisées dans les blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

Une instruction étiquetée déclare une étiquette avec le nom attribué par le *identificateur*. La portée d’une étiquette est l’ensemble du bloc dans lequel l’étiquette est déclarée, y compris les blocs imbriqués. Il s’agit d’une erreur de compilation pour deux étiquettes portant le même nom pour bénéficier d’étendues qui se chevauchent.

Une étiquette peut être référencée à partir de `goto` instructions ([l’instruction goto](statements.md#the-goto-statement)) dans l’étendue de l’étiquette. Cela signifie que `goto` instructions peuvent transférer le contrôle au sein de blocs et en dehors de blocs, mais jamais en blocs.

Les étiquettes ont leur propre espace de déclaration et n’interfèrent pas avec d’autres identificateurs. L’exemple
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
est valide et utilise le nom `x` comme paramètre et une étiquette.

L’exécution d’une instruction étiquetée correspond exactement à l’exécution de l’instruction qui suit l’étiquette.

En plus de l’accessibilité fournie par le flux de contrôle normal, une instruction étiquetée est accessible si l’étiquette est référencée par un accessible `goto` instruction. (Exception : Si un `goto` instruction se trouve dans un `try` qui inclut un `finally` bloc et l’instruction étiquetée est en dehors de la `try`et le point de terminaison de la `finally` bloc n’est pas accessible, puis l’instruction étiquetée n’est pas accessible à partir de qui `goto` instruction.)

## <a name="declaration-statements"></a>Instructions de déclaration

Un *declaration_statement* déclare une variable locale ou une constante. Les instructions de déclaration sont autorisées dans les blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.

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

Le *local_variable_type* d’un *local_variable_declaration* directement Spécifie le type des variables introduites par la déclaration, ou indique avec l’identificateur `var` qui le type doit être déduit selon un initialiseur. Le type est suivi d’une liste de *local_variable_declarator*s, chacun présentant une nouvelle variable. Un *local_variable_declarator* se compose d’un *identificateur* qui nomme la variable, éventuellement suivie d’une «`=`« jeton et un *local_variable_initializer* qui fournit la valeur initiale de la variable.

Dans le contexte d’une déclaration de variable locale, l’identificateur var agit comme un mot clé contextuel ([mots clés](lexical-structure.md#keywords)). Lorsque le *local_variable_type* est spécifié en tant que `var` et aucun type nommé `var` est dans la portée, la déclaration est une ***implicitement typées déclaration de variable locale***, dont le type est déduit le type de l’expression d’initialiseur associé. Les déclarations de variables locales implicitement typées sont soumis aux restrictions suivantes :

*  Le *local_variable_declaration* ne peut pas inclure plusieurs *local_variable_declarator*s.
*  Le *local_variable_declarator* doit inclure un *local_variable_initializer*.
*  Le *local_variable_initializer* doit être un *expression*.
*  L’initialiseur *expression* doit avoir un type de compilation.
*  L’initialiseur *expression* ne peut pas faire référence à la variable déclarée lui-même

Voici quelques exemples de déclarations de variable locale implicitement typées incorrectes :

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

La valeur d’une variable locale est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)), et la valeur d’une variable locale est modifiée à l’aide un *attribution* () [Opérateurs d’assignation](expressions.md#assignment-operators)). Une variable locale doit absolument être assignée ([assignation définie](variables.md#definite-assignment)) à chaque emplacement où sa valeur est obtenue.

La portée d’une variable locale déclarée dans un *local_variable_declaration* est le bloc dans lequel la déclaration se produit. C’est une erreur pour faire référence à une variable locale dans une position du texte qui précède le *local_variable_declarator* de la variable locale. Dans l’étendue d’une variable locale, il est une erreur de compilation pour déclarer une autre local variable ou constante portant le même nom.

Une déclaration de variable locale qui déclare plusieurs variables équivaut à plusieurs déclarations de variables uniques avec le même type. En outre, un initialiseur de variable dans une déclaration de variable locale correspond exactement à une instruction d’assignation qui est insérée immédiatement après la déclaration.

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

Dans une déclaration de variable locale implicitement typée, le type de la variable locale déclarée est pris pour être le même que le type de l’expression utilisée pour initialiser la variable. Exemple :
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

Les déclarations variable locales implicitement typées ci-dessus sont précisément équivalentes aux déclarations explicitement typées suivantes :
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a>Déclarations de constante locale

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

Le *type* d’un *local_constant_declaration* Spécifie le type de l’une des constantes introduites par la déclaration. Le type est suivi d’une liste de *constant_declarator*s, chacun d’eux présente une nouvelle constante. A *constant_declarator* se compose d’un *identificateur* qui nomme la constante, suivie d’une «`=`» jeton, suivi par un *constant_expression* ([ Expressions constantes](expressions.md#constant-expressions)) qui fournit la valeur de la constante.

Le *type* et *constant_expression* d’une déclaration de constante locale doit suivre les mêmes règles que celles d’une déclaration de membre de constante ([constantes](classes.md#constants)).

La valeur d’une constante locale est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)).

La portée d’une constante locale est le bloc dans lequel la déclaration se produit. C’est une erreur pour faire référence à une constante locale dans une position du texte qui précède sa *constant_declarator*. Dans la portée d’une constante locale, il est une erreur de compilation pour déclarer une autre local variable ou constante portant le même nom.

Une déclaration de constante locale qui déclare plusieurs constantes équivaut à plusieurs déclarations de constantes uniques avec le même type.

## <a name="expression-statements"></a>Instructions d’expression

Un *expression_statement* évalue une expression donnée. La valeur calculée par l’expression, le cas échéant, est ignoré.

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

Pas toutes les expressions sont autorisées en tant qu’instructions. En particulier, des expressions telles que `x + y` et `x == 1` qui simplement de calculer une valeur (qui sera ignorée), ne sont pas autorisées en tant qu’instructions.

L’exécution d’une *expression_statement* évalue l’expression de relation contenant-contenue et puis transfère le contrôle au point de terminaison de la *expression_statement*. Le point de terminaison d’un *expression_statement* est accessible si le problème *expression_statement* est accessible.

## <a name="selection-statements"></a>Instructions de sélection

Instructions de sélection Sélectionnez un d’un nombre d’instructions possibles pour l’exécution selon la valeur d’une expression.

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a>If instruction

La `if` instruction sélectionne une instruction à exécuter selon la valeur d’une expression booléenne.

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Un `else` partie est associée à l’exemple précédent le plus proche lexicalement `if` qui n’est autorisé par la syntaxe. Par conséquent, un `if` instruction du formulaire
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

Un `if` instruction est exécutée comme suit :

*  Le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.
*  Si l’expression booléenne génère `true`, le contrôle est transféré à la première instruction incorporée. Lorsqu’et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de terminaison de la `if` instruction.
*  Si l’expression booléenne génère `false` et si un `else` partie est présente, le contrôle est transféré à la deuxième instruction incorporée. Lorsqu’et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de terminaison de la `if` instruction.
*  Si l’expression booléenne génère `false` et si un `else` partie n’est pas présente, le contrôle est transféré au point de terminaison de la `if` instruction.

La première instruction d’incorporée une `if` instruction est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `false`.

La deuxième instruction d’incorporée une `if` instruction, le cas échéant, est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.

Le point de terminaison d’un `if` instruction est accessible si le point de terminaison d’au moins un de ses instructions incorporées est accessible. En outre, de point de la fin d’une `if` instruction sans aucune `else` partie est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.

### <a name="the-switch-statement"></a>L’instruction switch

L’instruction switch sélectionne l’exécution d’une liste d’instructions ayant une étiquette de commutateur associé correspond à la valeur de l’expression de switch.

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

Un *switch_statement* se compose du mot clé `switch`par une expression entre parenthèses (appelée expression de switch), suivi par un *switch_block*. Le *switch_block* se compose de zéro ou plusieurs *switch_section*s, entre accolades. Chaque *switch_section* se compose d’une ou plusieurs *switch_label*s suivi par un *statement_list* ([instruction répertorie](statements.md#statement-lists)).

Le ***régissant type*** d’un `switch` instruction est établie par l’expression de switch.

*  Si le type de l’expression de switch est `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, ou un *type_de_liste*, ou si elle est le type nullable correspondant à un de ces types, qui est le régissant le type de la `switch` instruction.
*  Sinon, un seul définis par l’utilisateur une conversion implicite ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) doit exister entre le type de l’expression de switch à un des régissant les types possibles suivants : `sbyte`, `byte`, `short` , `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, ou un type nullable correspondant à une de ces types.
*  Sinon, si telle conversion implicite n’existe, ou si plus d’une telle conversion implicite existe, une erreur de compilation se produit.

L’expression constante de chaque `case` étiquette doit indiquer une valeur qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) vers le type de gouvernant le `switch` instruction. Une erreur de compilation se produit si deux ou plusieurs `case` étiquettes dans le même `switch` instruction spécifient la même valeur de constante.

Il peut y avoir au plus une `default` étiquette dans une instruction switch.

Un `switch` instruction est exécutée comme suit :

*  L’expression de switch est évaluée et convertie vers le type gouvernant.
*  Si une des constantes spécifiée dans un `case` étiquette dans le même `switch` instruction est égale à la valeur de l’expression de switch, le contrôle est transféré à la liste d’instructions suivant la mise en correspondance `case` étiquette.
*  Si aucune des constantes spécifiées dans `case` étiquettes dans le même `switch` instruction est égale à la valeur de l’expression de switch et si un `default` étiquette est présente, le contrôle est transféré à l’instruction qui suit liste le `default` étiquette.
*  Si aucune des constantes spécifiées dans `case` étiquettes dans le même `switch` instruction est égale à la valeur de l’expression de switch et si aucun `default` étiquette est présente, le contrôle est transféré au point de terminaison de la `switch` instruction.

Si le point de terminaison de la liste d’instructions d’une section switch est accessible, une erreur de compilation se produit. Il s’agit en tant que la règle « aucune de passage ». L’exemple
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
est valide car aucune section switch n’a un point de terminaison accessible. Contrairement à C et C++, l’exécution d’une section switch est interdit à « fourre-tout » à la section switch suivante et l’exemple
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
génère une erreur de compilation. Quand l’exécution d’une section switch doit être suivie de l’exécution d’une autre section de commutation, explicite `goto case` ou `goto default` instruction doit être utilisée :
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
n’est valide. L’exemple ne viole pas la règle « aucune de passage », car les étiquettes `case 2:` et `default:` font partie du même *switch_section*.

La règle « aucune de passage » empêche une classe commune de bogues qui se produisent en C et C++ lorsque `break` instructions sont omises par inadvertance. En outre, en raison de cette règle, les sections switch d’un `switch` instruction peut être réorganisée arbitrairement sans affecter le comportement de l’instruction. Par exemple, les sections de la `switch` instruction ci-dessus peut être annulée sans affecter le comportement de l’instruction :
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

La liste d’instructions d’une section switch se termine généralement par un `break`, `goto case`, ou `goto default` instruction, mais toute construction qui rend le point de terminaison de la liste d’instructions inaccessible est autorisée. Par exemple, un `while` instruction contrôlée par l’expression booléenne `true` est connu pour ne jamais atteindre son point de terminaison. De même, un `throw` ou `return` instruction toujours transfère le contrôle ailleurs et n’atteint jamais son point de terminaison. Par conséquent, l’exemple suivant est valide :
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

Le type gouvernant d’un `switch` instruction peut être de type `string`. Exemple :
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

Comme les opérateurs d’égalité ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)), la `switch` instruction respecte la casse et exécutera une section de commutateur donné uniquement si la chaîne d’expression de switch correspond exactement à un `case` étiquette constante.

Lorsque le contrôle de type d’un `switch` instruction est `string`, la valeur `null` est autorisée comme une constante d’étiquette case.

Le *statement_list*s d’un *switch_block* peut contenir des instructions de déclaration ([les instructions de déclaration](statements.md#declaration-statements)). La portée d’une variable locale ou constante déclarée dans un bloc switch est le bloc switch.

La liste d’instructions d’une section de commutateur donné est accessible si le `switch` instruction est accessible et au moins une des opérations suivantes est vraie :

*  L’expression de switch est une valeur non constante.
*  L’expression de switch est une valeur constante qui correspond à un `case` étiquette dans la section de commutation.
*  L’expression de switch est une valeur constante qui ne correspond à aucune `case` étiquette et la section switch contient le `default` étiquette.
*  Une étiquette de la section de commutation switch est référencée par un accessible `goto case` ou `goto default` instruction.

Le point de terminaison d’un `switch` instruction est accessible si au moins une des opérations suivantes est vraie :

*  Le `switch` instruction contient un accessible `break` instruction se termine le `switch` instruction.
*  Le `switch` instruction est accessible, l’expression de switch est une valeur non constante et non `default` étiquette n’est présente.
*  Le `switch` instruction est accessible, l’expression de switch est une valeur constante qui ne correspond à aucune `case` étiquette et non `default` étiquette n’est présente.

## <a name="iteration-statements"></a>Instructions d’itération

Instructions d’itération exécuter à plusieurs reprises une instruction incorporée.

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a>L’instruction while

La `while` instruction if exécute une instruction incorporée zéro ou plusieurs fois.

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

Un `while` instruction est exécutée comme suit :

*  Le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.
*  Si l’expression booléenne génère `true`, le contrôle est transféré à l’instruction incorporée. Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), le contrôle est transféré vers le début de la `while` instruction.
*  Si l’expression booléenne génère `false`, le contrôle est transféré au point de terminaison de la `while` instruction.

Dans l’instruction incorporée d’un `while` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `while` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (exécutant ainsi une autre itération de la `while` instruction).

L’instruction incorporée d’un `while` instruction est accessible si le `while` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `false`.

Le point de terminaison d’un `while` instruction est accessible si au moins une des opérations suivantes est vraie :

*  Le `while` instruction contient un accessible `break` instruction se termine le `while` instruction.
*  Le `while` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.

### <a name="the-do-statement"></a>L’instruction do

La `do` instruction if exécute une instruction incorporée une ou plusieurs fois.

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

Un `do` instruction est exécutée comme suit :

*  Le contrôle est transféré à l’instruction incorporée.
*  Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée. Si l’expression booléenne génère `true`, le contrôle est transféré vers le début de la `do` instruction. Sinon, le contrôle est transféré au point de terminaison de la `do` instruction.

Dans l’instruction incorporée d’un `do` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `do` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée.

L’instruction incorporée d’un `do` instruction est accessible si le `do` instruction est accessible.

Le point de terminaison d’un `do` instruction est accessible si au moins une des opérations suivantes est vraie :

*  Le `do` instruction contient un accessible `break` instruction se termine le `do` instruction.
*  Le point de terminaison de l’instruction incorporée est accessible et l’expression booléenne n’a pas la valeur de constante `true`.

### <a name="the-for-statement"></a>L’instruction for

La `for` instruction évalue une séquence d’expressions d’initialisation et puis, lorsqu’une condition a la valeur est true, à plusieurs reprises exécute une instruction incorporée et évalue une séquence d’expressions d’itération.

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

Le *for_initializer*, le cas échéant, se compose d’un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)) ou une liste de *statement_ expression*s ([instructions d’Expression](statements.md#expression-statements)) séparés par des virgules. La portée d’une variable locale déclarée par un *for_initializer* commence au *local_variable_declarator* pour la variable et s’étend jusqu'à la fin de l’instruction incorporée. L’étendue inclut la *for_condition* et *for_iterator*.

Le *for_condition*, le cas échéant, doit être un *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)).

Le *for_iterator*, le cas échéant, se compose d’une liste de *statement_expression*s ([instructions d’Expression](statements.md#expression-statements)) séparés par des virgules.

Une instruction for est exécutée comme suit :

*  Si un *for_initializer* est présent, les initialiseurs de variable ou d’expressions d’instruction sont exécutées dans l’ordre qu’ils sont écrits. Cette étape est effectuée uniquement une seule fois.
*  Si un *for_condition* est présent, elle est évaluée.
*  Si le *for_condition* n’est pas présent ou si l’évaluation génère `true`, le contrôle est transféré à l’instruction incorporée. Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), les expressions de la *for_iterator*, si une, évaluées dans l’ordre, et puis une autre itération est effectuée, en commençant par la version d’évaluation de la *for_condition* à l’étape précédente.
*  Si le *for_condition* est présent et l’évaluation donne `false`, le contrôle est transféré au point de terminaison de la `for` instruction.

Dans l’instruction incorporée d’un `for` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `for` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (exécutant ainsi le *for_iterator* et exécution d’une autre itération de la `for` instruction, en commençant par le *for_condition*).

L’instruction incorporée d’un `for` instruction est accessible si une des opérations suivantes est remplie :

*  Le `for` instruction est accessible et aucun *for_condition* est présent.
*  Le `for` instruction est accessible et une *for_condition* est présent et n’a pas la valeur de constante `false`.

Le point de terminaison d’un `for` instruction est accessible si au moins une des opérations suivantes est vraie :

*  Le `for` instruction contient un accessible `break` instruction se termine le `for` instruction.
*  Le `for` instruction est accessible et une *for_condition* est présent et n’a pas la valeur de constante `true`.

### <a name="the-foreach-statement"></a>L’instruction foreach

La `foreach` instruction énumère les éléments d’une collection, en exécutant une instruction incorporée pour chaque élément de la collection.

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

Le *type* et *identificateur* d’un `foreach` instruction déclare le ***variable d’itération*** de l’instruction. Si le `var` identificateur donné comme le *local_variable_type*et aucun type nommé `var` est dans la portée, la variable d’itération est dite un ***variable d’itération implicitement typée***, et son type est considéré comme étant le type d’élément de la `foreach` instruction, comme indiqué ci-dessous. La variable d’itération correspond à une variable locale en lecture seule avec une portée qui s’étend sur l’instruction incorporée. Pendant l’exécution d’un `foreach` instruction, la variable d’itération représente l’élément de collection pour laquelle une itération est en cours d’exécution. Une erreur de compilation se produit si l’instruction incorporée tente de modifier la variable d’itération (via l’attribution ou le `++` et `--` opérateurs) ou passer la variable d’itération comme un `ref` ou `out` paramètre.

Dans l’exemple suivant, par souci de concision, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` et `IEnumerator<T>` font référence aux types correspondants dans les espaces de noms `System.Collections` et `System.Collections.Generic`.

Le traitement de la compilation d’une instruction foreach détermine tout d’abord le ***type de collection***, ***type d’énumérateur*** et ***type d’élément*** de l’expression. Cette détermination procède comme suit :

*  Si le type `X` de *expression* est un type tableau, il y a une conversion de référence implicite à partir de `X` à la `IEnumerable` interface (dans la mesure où `System.Array` implémente cette interface). Le ***type de collection*** est la `IEnumerable` interface, le ***type d’énumérateur*** est la `IEnumerator` interface et le ***type d’élément*** est le type d’élément de la type de tableau `X`.
*  Si le type `X` de *expression* est `dynamic` , il y a une conversion implicite de *expression* à la `IEnumerable` interface ([dynamique implicite conversions](conversions.md#implicit-dynamic-conversions)). Le ***type de collection*** est la `IEnumerable` interface et le ***type d’énumérateur*** est le `IEnumerator` interface. Si le `var` identificateur donné comme le *local_variable_type* le ***type d’élément*** est `dynamic`, sinon il est `object`.
*  Sinon, déterminez si le type `X` a approprié `GetEnumerator` méthode :
   * Effectuer la recherche de membres sur le type `X` avec l’identificateur `GetEnumerator` sans aucun argument de type. Si la recherche de membres ne produit pas une correspondance, ou il génère une ambiguïté, ou génère une correspondance qui n’est pas un groupe de méthodes, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé qu’un avertissement émis si la recherche de membres produit quoi que ce soit, à l’exception d’un groupe de méthodes ou aucune correspondance.
   * Effectuer une résolution de surcharge en utilisant le groupe résultant de la méthode et une liste d’arguments vide. Si la résolution de surcharge aboutit à aucune méthode applicable, provoque une ambiguïté ou entraîne une meilleure méthode unique, mais cette méthode est statique ou non public, recherchez une interface énumérable comme décrit ci-dessous. Il est recommandé qu’un avertissement est émis si la résolution de surcharge produit quoi que ce soit, à l’exception d’une méthode d’instance public non équivoque ou aucune méthode applicable.
   * Si le type de retour `E` de la `GetEnumerator` méthode n’est pas une classe, type de struct ou d’interface, une erreur est généré et aucune autre étape n’est effectuées.
   * Recherche de membres est effectuée sur `E` avec l’identificateur `Current` sans aucun argument de type. Si la recherche de membres ne produit aucune correspondance, le résultat est une erreur ou le résultat est une valeur autre qu’une propriété d’instance publics qui autorise la lecture, une erreur se produit et aucune autre étape n’est effectuées.
   * Recherche de membres est effectuée sur `E` avec l’identificateur `MoveNext` sans aucun argument de type. Si la recherche de membres ne produit aucune correspondance, le résultat est une erreur ou le résultat est une valeur autre qu’un groupe de méthodes, une erreur se produit et aucune autre étape n’est effectuées.
   * Résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide. Si les résultats de résolution de surcharge dans aucune méthodes applicables, les résultats dans une ambiguïté ou entraîne une meilleure méthode unique, mais cette méthode est statique ou non public, ou son type de retour n’est pas `bool`, une erreur se produit et aucune autre étape n’est effectuées.
   * Le ***type de collection*** est `X`, le ***type d’énumérateur*** est `E`et le ***type d’élément*** est le type de la `Current` propriété.

*  Sinon, recherchez une interface énumérable :
   * If parmi tous les types `Ti` pour lequel il existe une conversion implicite de `X` à `IEnumerable<Ti>`, il existe un type unique `T` telles que `T` n’est pas `dynamic` et pour tous les autres `Ti` est un conversion implicite de `IEnumerable<T>` à `IEnumerable<Ti>`, puis le ***type de collection*** est l’interface `IEnumerable<T>`, le ***type d’énumérateur*** est l’interface `IEnumerator<T>`et le ***type d’élément*** est `T`.
   * Sinon, s’il existe plus d’un tel type `T`, une erreur se produit et aucune autre étape n’est effectuées.
   * Sinon, s’il existe une conversion implicite de `X` à la `System.Collections.IEnumerable` interface, puis le ***type de collection*** est cette interface, le ***type d’énumérateur*** est l’interface `System.Collections.IEnumerator`et le ***type d’élément*** est `object`.
   * Sinon, une erreur se produit et aucune étape supplémentaire n’est effectuée.

Les étapes ci-dessus, en cas de réussite, sans ambiguïté produisent un type de collection `C`, type d’énumérateur `E` et type d’élément `T`. Une instruction foreach du formulaire
```csharp
foreach (V v in x) embedded_statement
```
est ensuite étendue à :
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

La variable `e` n’est pas visible à ou accessibles à l’expression `x` ou l’instruction incorporée ou tout autre code source du programme. La variable `v` est en lecture seule dans l’instruction incorporée. Si n’est pas une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) à partir de `T` (le type d’élément) pour `V` (le *local_variable_type* dans l’instruction foreach), une erreur se produit et aucune étape supplémentaire n’est effectuée. Si `x` a la valeur `null`, un `System.NullReferenceException` est levée au moment de l’exécution.

Une implémentation est autorisée à implémenter une instruction foreach donnée différemment, par exemple, pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.

Le placement de `v` à l’intérieur de le pendant que la boucle est importante pour la façon dont il est capturé par n’importe quelle fonction anonyme qui se produisent dans le *embedded_statement*.

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
Si `v` a été déclaré en dehors de le pendant que boucle, il est partagé entre toutes les itérations et sa valeur après le pour boucle serait la valeur finale, `13`, c'est-à-dire quel l’appel de `f` imprimerait. Au lieu de cela, étant donné que chaque itération possède sa propre variable `v`, celui capturé par `f` dans la première itération continue à contenir la valeur `7`, qui est la zone d’impression. (Remarque : les versions antérieures de c# déclaré `v` boucle de l’extérieur du while.)

Le corps de l’enfin le bloc est construit en respectant les étapes suivantes :

*  S’il existe une conversion implicite de `E` à la `System.IDisposable` de l’interface, puis
   *  Si `E` est un type valeur non nullable l’enfin clause est développée à l’équivalent sémantique de :

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  Sinon l’enfin clause est développée à l’équivalent sémantique de :

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   à l’exception qu’en cas `E` est un type valeur ou un paramètre de type instancié pour un type valeur, puis le cast de `e` à `System.IDisposable` n’entraîne pas de boxing se produise.

*  Sinon, si `E` est un type sealed, l’enfin clause est développée en un bloc vide :

   ```csharp
   finally {
   }
   ```

*  Sinon, l’enfin clause est développée pour :

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   La variable locale `d` n’est pas visible à ou accessibles à n’importe quel code utilisateur. En particulier, il ne sont pas en conflit avec toute autre variable dont l’étendue inclut le bloc finally.

L’ordre dans lequel `foreach` parcourt les éléments d’un tableau, se présente comme suit : Pour les éléments de tableaux unidimensionnels sont parcourus dans l’ordre d’index croissant, en commençant par index `0` et se terminant par index `Length - 1`. Pour les tableaux multidimensionnels, les éléments sont parcourus telles que les index de la dimension la plus à droite sont accrues en premier, puis la dimension immédiatement à gauche, et ainsi de suite vers la gauche.

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
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

Dans l’exemple
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
le type de `n` est déduite comme étant `int`, le type d’élément de `numbers`.

## <a name="jump-statements"></a>Instructions de saut

Instructions de saut transfèrent sans condition le contrôle.

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

L’emplacement auquel une instruction de saut transfère le contrôle est appelé le ***cible*** de l’instruction de saut.

Lorsqu’une instruction de saut se produit dans un bloc, et la cible de cette instruction de saut est en dehors de ce bloc, l’instruction de saut est dite ***quitter*** le bloc. Pendant une instruction de saut peut transférer le contrôle hors d’un bloc, il peut transférer le contrôle jamais dans un bloc.

L’exécution d’instructions de saut est compliquée par la présence d’intervenants `try` instructions. En l’absence de telles `try` instructions, une instruction de saut transfère sans condition le contrôle à partir de l’instruction de saut à sa cible. En présence de ce type intervenants `try` instructions, l’exécution est plus complexe. Si l’instruction de saut quitte un ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.

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
le `finally` blocs associés à deux `try` instructions sont exécutées avant que le contrôle est transféré à la cible de l’instruction de saut.

La sortie produite est la suivante :
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a>L’instruction break

Le `break` instruction termine l’englobante la plus proche `switch`, `while`, `do`, `for`, ou `foreach` instruction.

```antlr
break_statement
    : 'break' ';'
    ;
```

La cible d’un `break` instruction est le point de terminaison de l’englobante la plus proche `switch`, `while`, `do`, `for`, ou `foreach` instruction. Si un `break` instruction n’est pas englobée par un `switch`, `while`, `do`, `for`, ou `foreach` instruction, une erreur de compilation survient.

Lorsque plusieurs `switch`, `while`, `do`, `for`, ou `foreach` instructions sont imbriquées dans d’autres, un `break` instruction s’applique uniquement à l’instruction plus profonde. Pour transférer le contrôle sur plusieurs niveaux d’imbrication, un `goto` instruction ([l’instruction goto](statements.md#the-goto-statement)) doit être utilisé.

Un `break` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)). Quand un `break` instruction se produit dans un `finally` bloquer, la cible de la `break` instruction doit être dans le même `finally` bloquer ; sinon, une erreur de compilation se produit.

Un `break` instruction est exécutée comme suit :

*  Si le `break` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.
*  Le contrôle est transféré à la cible de la `break` instruction.

Étant donné qu’un `break` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `break` instruction n’est jamais accessible.

### <a name="the-continue-statement"></a>L’instruction continue

Le `continue` instruction démarre une nouvelle itération de l’englobante la plus proche `while`, `do`, `for`, ou `foreach` instruction.

```antlr
continue_statement
    : 'continue' ';'
    ;
```

La cible d’un `continue` instruction est le point de terminaison de l’instruction incorporée de l’englobante la plus proche `while`, `do`, `for`, ou `foreach` instruction. Si un `continue` instruction n’est pas englobée par un `while`, `do`, `for`, ou `foreach` instruction, une erreur de compilation survient.

Lorsque plusieurs `while`, `do`, `for`, ou `foreach` instructions sont imbriquées dans d’autres, un `continue` instruction s’applique uniquement à l’instruction plus profonde. Pour transférer le contrôle sur plusieurs niveaux d’imbrication, un `goto` instruction ([l’instruction goto](statements.md#the-goto-statement)) doit être utilisé.

Un `continue` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)). Quand un `continue` instruction se produit dans un `finally` bloquer, la cible de la `continue` instruction doit être dans le même `finally` bloquer ; sinon une erreur de compilation se produit.

Un `continue` instruction est exécutée comme suit :

*  Si le `continue` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.
*  Le contrôle est transféré à la cible de la `continue` instruction.

Étant donné qu’un `continue` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `continue` instruction n’est jamais accessible.

### <a name="the-goto-statement"></a>Instruction goto

La `goto` instruction transfère le contrôle à une instruction qui est marquée par une étiquette.

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

La cible d’un `goto` *identificateur* instruction est une instruction étiquetée avec l’étiquette donnée. Si une étiquette portant le nom spécifié n’existe pas dans la fonction membre en cours, ou si le `goto` instruction n’est pas dans la portée de l’étiquette, une erreur de compilation se produit. Cette règle autorise l’utilisation d’un `goto` instruction pour transférer le contrôle hors d’une étendue imbriquée, mais pas dans une étendue imbriquée. Dans l’exemple
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
un `goto` instruction est utilisée pour transférer le contrôle hors d’une étendue imbriquée.

La cible d’un `goto case` instruction est la liste d’instructions dans immédiatement englobante `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)), qui contient un `case` étiquette avec la valeur de constante donnée. Si le `goto case` instruction n’est pas englobée par un `switch` instruction, si le *constant_expression* n’est pas implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) vers le type gouvernant de la le plus proche englobante `switch` instruction, ou si l’englobante la plus proche `switch` instruction ne contient-elle pas un `case` étiquette avec la valeur de constante donnée, une erreur de compilation se produit.

La cible d’un `goto default` instruction est la liste d’instructions dans immédiatement englobante `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)), qui contient un `default` étiquette. Si le `goto default` instruction n’est pas englobée par un `switch` instruction, ou si l’englobante la plus proche `switch` instruction ne contient-elle pas un `default` de l’étiquette, une erreur de compilation se produit.

Un `goto` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)). Quand un `goto` instruction se produit dans un `finally` bloquer, la cible de la `goto` instruction doit être dans le même `finally` bloc, ou sinon une erreur de compilation se produit.

Un `goto` instruction est exécutée comme suit :

*  Si le `goto` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.
*  Le contrôle est transféré à la cible de la `goto` instruction.

Étant donné qu’un `goto` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `goto` instruction n’est jamais accessible.

### <a name="the-return-statement"></a>L’instruction return

Le `return` instruction retourne le contrôle à l’appelant actuel de la fonction dans laquelle la `return` instruction apparaît.

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

Un `return` instruction sans expression peut être utilisée uniquement dans une fonction membre qui ne calcule pas une valeur, autrement dit, une méthode avec le type de résultat ([corps de la méthode](classes.md#method-body)) `void`, le `set` accesseur d’une propriété ou indexeur, le `add` et `remove` accesseurs d’un événement, un constructeur d’instance, un constructeur statique ou un destructeur.

Un `return` instruction avec une expression peut uniquement être utilisée dans une fonction membre qui calcule une valeur, autrement dit, une méthode avec un type de résultat de non void, le `get` accesseur d’une propriété ou indexeur ou un opérateur défini par l’utilisateur. Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l’expression pour le type de retour de la fonction membre conteneur.

Retourner les instructions peuvent également être utilisées dans le corps d’expressions de fonction anonyme ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)) et participer à déterminer quelles conversions existent pour ces fonctions.

Il s’agit d’une erreur lors de la compilation pour un `return` instruction apparaissent dans un `finally` bloc ([l’instruction try](statements.md#the-try-statement)).

Un `return` instruction est exécutée comme suit :

*  Si la `return` instruction spécifie une expression, l’expression est évaluée et la valeur résultante est convertie au type de retour de la fonction conteneur par une conversion implicite. Le résultat de la conversion devient la valeur de résultat produite par la fonction.
*  Si le `return` instruction est englobée par un ou plusieurs `try` ou `catch` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` bloque tous les caractères entourant `try` instructions ont été exécutées.
*  Si la fonction qui le contient n’est pas une fonction asynchrone, le contrôle est retourné à l’appelant de la fonction contenante, ainsi que la valeur de résultat, le cas échéant.
*  Si la fonction conteneur est une fonction async, contrôle est retourné à l’appelant actuel, et la valeur de résultat, le cas échéant, est enregistrée dans la tâche retournée comme décrit dans ([interfaces d’énumérateur](classes.md#enumerator-interfaces)).

Étant donné qu’un `return` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `return` instruction n’est jamais accessible.

### <a name="the-throw-statement"></a>L’instruction throw

La `throw` instruction lève une exception.

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

Un `throw` instruction avec une expression lève la valeur produite en évaluant l’expression. L’expression doit indiquer une valeur de type classe `System.Exception`, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de type a `System.Exception` (ou une sous-classe) en tant que sa classe de base efficace. Si l’évaluation de l’expression génère `null`, un `System.NullReferenceException` est levée à la place.

Un `throw` instruction sans expression peut être utilisée uniquement dans un `catch` bloquer, auquel cas cette instruction lève à nouveau l’exception qui est gérée par cette `catch` bloc.

Étant donné qu’un `throw` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `throw` instruction n’est jamais accessible.

Lorsqu’une exception est levée, le contrôle est transféré à la première `catch` clause dans une englobante `try` instruction qui peut gérer l’exception. Le processus qui a lieu à partir du point de l’exception levée au point de transfert de contrôle vers un gestionnaire d’exceptions approprié est appelé ***la propagation d’exception***. La propagation d’une exception se compose de plusieurs fois l’évaluation de la procédure suivante avant un `catch` clause qui correspond à l’exception est trouvé. Dans cette description, le ***lever point*** est initialement l’emplacement auquel l’exception est levée.

*  Dans la fonction membre en cours, chaque `try` instruction qui englobe le point de levée est examinée. Pour chaque instruction `S`, en commençant par la plus intérieure `try` instruction et se terminant par le plus externe `try` instruction, les étapes suivantes sont évaluées :

   * Si le `try` bloquer de `S` englobe le point de levée et que S a un ou plusieurs `catch` clauses, le `catch` clauses sont examinées dans l’ordre d’apparition pour trouver un gestionnaire approprié pour l’exception, selon les règles spécifiées dans Section [l’instruction try](statements.md#the-try-statement). Si une correspondance `catch` clause se trouve, la propagation des exceptions est terminée en transférant le contrôle au bloc de qui `catch` clause.

   * Sinon, si le `try` bloc ou une `catch` bloquer de `S` englobe le point de levée et si `S` a un `finally` bloc, le contrôle est transféré vers le `finally` bloc. Si le `finally` bloc lève une autre exception, le traitement de l’exception actuelle est interrompu. Sinon, lorsque le contrôle atteint le point de terminaison de la `finally` bloc, la suite de traitement de l’exception actuelle.

*  Si un gestionnaire d’exceptions ne se trouvait pas dans l’appel de fonction en cours, l’appel de fonction se termine, et une des actions suivantes se produit :

   * Si la fonction actuelle est non asynchrone, les étapes ci-dessus sont répétées pour l’appelant de la fonction avec un point de levée correspondant à l’instruction à partir de laquelle la fonction membre a été appelée.

   * Si la fonction active est asynchrone et retournant des tâches, l’exception est enregistrée dans la tâche de retournée, qui est placée dans un état défectueux ou annulé, comme décrit dans [interfaces d’énumérateur](classes.md#enumerator-interfaces).

   * Si la fonction active est asynchrone et qui retournent void, le contexte de synchronisation du thread actuel est notifié comme décrit dans [interfaces énumérables](classes.md#enumerable-interfaces).

*  Si le traitement des exceptions met fin à tous les appels de fonctions membres dans le thread actuel, qui indique que le thread n’a aucun gestionnaire pour l’exception, puis le thread est lui-même s’est arrêté. L’impact d’un tel arrêt est défini par l’implémentation.

## <a name="the-try-statement"></a>L’instruction try

La `try` instruction fournit un mécanisme d’interception des exceptions qui se produisent pendant l’exécution d’un bloc. En outre, le `try` instruction offre la possibilité de spécifier un bloc de code qui est toujours exécuté lorsque le contrôle quitte la `try` instruction.

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

Il existe trois formes possibles de `try` instructions :

*  Un `try` bloc suivie d’une ou plusieurs `catch` blocs.
*  Un `try` bloc suivi par un `finally` bloc.
*  Un `try` bloc suivie d’une ou plusieurs `catch` blocs suivi par un `finally` bloc.

Quand un `catch` clause spécifie un *exception_specifier*, le type doit être `System.Exception`, un type qui dérive de `System.Exception` ou un type de paramètre de type qui a `System.Exception` (ou une sous-classe) en tant que son effet classe de base.

Quand un `catch` clause spécifie à la fois un *exception_specifier* avec un *identificateur*, un ***variable d’exception*** du prénom et du type est déclaré. La variable d’exception correspond à une variable locale avec une portée qui s’étend sur la `catch` clause. Pendant l’exécution de la *exception_filter* et *bloc*, la variable d’exception représente l’exception en cours de traitement. À des fins de vérification de l’assignation définie, la variable d’exception est considérée comme définitivement assignée dans toute sa portée.

Sauf si un `catch` clause inclut un nom de variable d’exception, il est impossible d’accéder à l’objet d’exception dans le filtre et `catch` bloc.

Un `catch` clause qui ne spécifie pas un *exception_specifier* est appelée un général `catch` clause.

Certains langages de programmation peuvent prendre en charge les exceptions qui ne sont pas qui peut être représentées comme un objet dérivé `System.Exception`, bien que ces exceptions ne peuvent jamais être générées par le code C#. Un grand `catch` clause peut être utilisée pour intercepter de telles exceptions. Par conséquent, un grand `catch` clause est sémantiquement différente à partir d’un objet qui spécifie le type `System.Exception`, car la première peut également intercepter des exceptions à partir d’autres langages.

Afin de trouver le gestionnaire pour une exception, `catch` clauses sont examinées dans l’ordre lexical. Si un `catch` clause spécifie un type, mais aucun filtre d’exception, c’est une erreur de compilation pour une version ultérieure `catch` clause dans la même `try` instruction pour spécifier un type qui est identique, ou est dérivé, que vous tapez. Si un `catch` clause spécifie aucun type et aucun filtre, il doit être le dernier `catch` clause pour ce `try` instruction.

Au sein d’un `catch` bloc, un `throw` instruction ([l’instruction throw](statements.md#the-throw-statement)) sans expression peut être utilisé pour lever à nouveau l’exception qui a été interceptée par le `catch` bloc. Assignations à une variable d’exception ne modifient pas l’exception est levée de nouveau.

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
la méthode `F` intercepte une exception, écrit des informations de diagnostic dans la console, modifie la variable d’exception et lève à nouveau l’exception. L’exception est levée de nouveau est l’exception d’origine, donc le résultat obtenu est :
```
Exception in F: G
Exception in Main: G
```

Si le premier bloc catch avait levé `e` au lieu de lever de nouveau l’exception actuelle, le résultat obtenu se présente comme suit :
```csharp
Exception in F: G
Exception in Main: F
```

Il s’agit d’une erreur lors de la compilation pour un `break`, `continue`, ou `goto` instruction pour transférer le contrôle d’un `finally` bloc. Quand un `break`, `continue`, ou `goto` instruction s’exécute dans un `finally` bloc, la cible de l’instruction doit être dans le même `finally` bloc, ou sinon une erreur de compilation se produit.

Il s’agit d’une erreur lors de la compilation pour un `return` instruction peut se produire dans un `finally` bloc.

Un `try` instruction est exécutée comme suit :

*  Le contrôle est transféré à la `try` bloc.
*  Lorsqu’et si le contrôle atteint le point de terminaison de la `try` bloc :
   *  Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.
   *  Le contrôle est transféré au point de terminaison de la `try` instruction.

*  Si une exception est propagée vers le `try` instruction pendant l’exécution de la `try` bloc :
   *  Le `catch` clauses, le cas échéant, sont examinées dans l’ordre d’apparition pour trouver un gestionnaire approprié pour l’exception. Si un `catch` clause ne spécifie pas un type, ou spécifie le type d’exception ou un type de base du type d’exception :
      *  Si le `catch` clause déclare une variable d’exception, l’objet exception est attribuée à la variable d’exception.
      *  Si le `catch` clause déclare un filtre d’exception, le filtre est évalué. Si elle a la valeur `false`, la clause catch n’est pas une correspondance, et la recherche se poursuit via suivantes `catch` clauses pour un gestionnaire approprié.
      *  Sinon, le `catch` clause est considéré comme une correspondance, et le contrôle est transféré à la mise en correspondance `catch` bloc.
      *  Lorsqu’et si le contrôle atteint le point de terminaison de la `catch` bloc :
         * Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.
         * Le contrôle est transféré au point de terminaison de la `try` instruction.
      *  Si une exception est propagée vers le `try` instruction pendant l’exécution de la `catch` bloc :
         *  Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.
         *  L’exception est propagée vers la forme suivante `try` instruction.
   *  Si le `try` instruction n’a aucun `catch` clauses ou si aucun `catch` clause correspond à l’exception :
      *  Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.
      *  L’exception est propagée vers la forme suivante `try` instruction.

Les instructions d’un `finally` bloc sont toujours exécutées lorsque le contrôle quitte un `try` instruction. Cela est vrai si le transfert de contrôle se produit suite à une exécution normale, suite à l’exécution un `break`, `continue`, `goto`, ou `return` instruction, ou à la suite de propagation d’une exception de la `try` instruction.

Si une exception est levée pendant l’exécution d’un `finally` bloquer et n’est pas interceptée dans le même bloc finally, l’exception est propagée vers la forme suivante `try` instruction. Si une autre exception était en cours de propagation, cette exception est perdue. Le processus de propagation d’une exception est abordé plus en détail dans la description de la `throw` instruction ([l’instruction throw](statements.md#the-throw-statement)).

Le `try` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.

Un `catch` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.

Le `finally` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.

Le point de terminaison d’un `try` instruction est accessible si des opérations suivantes sont remplies :

*  Le point de terminaison de la `try` bloc est accessible ou point de la fin d’au moins une `catch` bloc est accessible.
*  Si un `finally` bloc est présent, le point de terminaison de la `finally` bloc est accessible.

## <a name="the-checked-and-unchecked-statements"></a>Les instructions checked et unchecked

Le `checked` et `unchecked` instructions permettent de contrôler le ***contexte de vérification de dépassement de capacité*** pour les conversions et les opérations arithmétiques de type intégral.

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

Le `checked` instruction provoque toutes les expressions dans le *bloc* à évaluer dans un contexte vérifié et le `unchecked` instruction provoque toutes les expressions dans le *bloc* à évaluer dans un un contexte non vérifié.

Le `checked` et `unchecked` instructions sont précisément équivalentes à la `checked` et `unchecked` opérateurs ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)), à ceci près qu’ils opèrent sur les blocs au lieu d’expressions .

## <a name="the-lock-statement"></a>L’instruction lock

La `lock` instruction Obtient le verrou d’exclusion mutuelle pour un objet donné, exécute une instruction, puis libère le verrou.

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

L’expression d’un `lock` instruction doit indiquer une valeur d’un type connu pour être un *reference_type*. Aucune conversion boxing implicite ([conversions Boxing](conversions.md#boxing-conversions)) n’est jamais effectuée pour l’expression d’un `lock` instruction et par conséquent, il est une erreur de compilation pour l’expression indiquer une valeur d’un *value_type*.

Un `lock` instruction du formulaire
```csharp
lock (x) ...
```
où `x` est une expression d’un *reference_type*, est équivalente à
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

Lorsqu’un verrou d’exclusion mutuelle est appliqué, l’exécution de code dans le même thread d’exécution peut également obtenir et libérer le verrou. Toutefois, l’exécution de code dans d’autres threads ne peut pas obtenir le verrou jusqu'à ce que le verrou est libéré.

Verrouillage `System.Type` objets afin de synchroniser l’accès aux données statiques n’est pas recommandée. Tout autre code risque de verrouiller sur le même type, ce qui peut entraîner un blocage. Une meilleure approche consiste à synchroniser l’accès aux données statiques en verrouillant un objet statique privé. Exemple :
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

La `using` instruction Obtient une ou plusieurs ressources, exécute une instruction, puis supprime la ressource.

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

Un ***ressource*** est une classe ou un struct qui implémente `System.IDisposable`, qui inclut une méthode sans paramètre unique nommée `Dispose`. Le code qui utilise une ressource peut appeler `Dispose` pour indiquer que la ressource n’est plus nécessaire. Si `Dispose` n’est pas appelée, élimination automatique se produit par la suite par conséquent le garbage collection.

Si la forme de *resource_acquisition* est *local_variable_declaration* , le type de la *local_variable_declaration* doit avoir la valeur `dynamic` ou un type qui peut être converti implicitement en `System.IDisposable`. Si la forme de *resource_acquisition* est *expression* , cette expression doit être implicitement convertible en `System.IDisposable`.

Les variables locales déclarées dans un *resource_acquisition* sont en lecture seule et doit inclure un initialiseur. Une erreur de compilation se produit si l’instruction incorporée tente de modifier ces variables locales (via l’attribution ou le `++` et `--` opérateurs), qui utilisent l’adresse d'entre eux ou les passer en tant que `ref` ou `out` paramètres.

Un `using` instruction est traduite en trois parties : acquisition, de l’utilisation et de suppression. L’utilisation de la ressource est englobée implicitement dans une `try` instruction qui inclut un `finally` clause. Cela `finally` clause supprime la ressource. Si un `null` l’acquisition de ressources, puis aucun appel à `Dispose` est effectuée, et aucune exception n’est levée. Si la ressource est de type `dynamic` il est converti dynamiquement via une conversion implicite dynamique ([les conversions implicites dynamiques](conversions.md#implicit-dynamic-conversions)) à `IDisposable` pendant l’acquisition d’afin de garantir que la conversion est réussi avant l’utilisation et la suppression.

Un `using` instruction du formulaire
```csharp
using (ResourceType resource = expression) statement
```
correspond à un des trois expansions possibles. Lorsque `ResourceType` est un type valeur non nullable, l’expansion est
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

Sinon, lorsque `ResourceType` est un type valeur nullable ou un type référence autre que `dynamic`, l’expansion est
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

Sinon, lorsque `ResourceType` est `dynamic`, l’expansion est
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

Dans les deux expansions, la `resource` variable est en lecture seule dans l’instruction incorporée et le `d` variable est inaccessible dans et invisible à l’instruction incorporée.

Une implémentation est autorisée à implémenter une instruction using donnée différemment, par exemple, pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.

Un `using` instruction du formulaire
```csharp
using (expression) statement
```
a les expansions possibles trois mêmes. Dans ce cas `ResourceType` est implicitement le type de compilation de la `expression`, le cas échéant. Sinon, l’interface `IDisposable` lui-même est utilisé comme le `ResourceType`. Le `resource` variable est inaccessible dans et invisible à l’instruction incorporée.

Quand un *resource_acquisition* prend la forme d’un *local_variable_declaration*, il est possible d’acquérir plusieurs ressources d’un type donné. Un `using` instruction du formulaire
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
imbriqué avec précision équivalent à une séquence de `using` instructions :
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

L’exemple ci-dessous crée un fichier nommé `log.txt` et écrit deux lignes de texte dans le fichier. L’exemple ouvre ce même fichier pour la lecture, puis copie les lignes de relation contenant-contenus de texte dans la console.
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

Dans la mesure où le `TextWriter` et `TextReader` classes implémentent le `IDisposable` interface, l’exemple peut utiliser `using` des instructions pour les opérations de lecture ou de vous assurer que le fichier sous-jacent est fermé correctement après l’écriture.

## <a name="the-yield-statement"></a>L’instruction yield

Le `yield` instruction est utilisée dans un bloc itérateur ([blocs](statements.md#blocks)) afin de produire une valeur à l’objet énumérateur ([objets d’énumérateur](classes.md#enumerator-objects)) ou un objet énumérable ([objets énumérables](classes.md#enumerable-objects)) d’un itérateur ou pour signaler la fin de l’itération.

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

`yield` n’est pas un mot réservé ; Il a une signification spéciale uniquement lorsque utilisé qu’immédiatement avant une `return` ou `break` mot clé. Dans d’autres contextes, `yield` peut être utilisé en tant qu’identificateur.

Il existe plusieurs restrictions sur l’emplacement un `yield` instruction peut apparaître, comme décrit dans l’exemple suivant.

*  Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) à apparaître en dehors d’un *method_body*, *operator_body* ou *accessor_body*
*  Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) à apparaître à l’intérieur d’une fonction anonyme.
*  Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) apparaissent dans le `finally` clause d’une `try` instruction.
*  Il s’agit d’une erreur lors de la compilation pour un `yield return` instruction à apparaître n’importe où dans un `try` instruction qui contient des `catch` clauses.

L’exemple suivant montre certaines utilisations valides et non valides de `yield` instructions.

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

Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l’expression dans le `yield return` instruction vers le type yield ([Yield type](classes.md#yield-type)) de l’itérateur.

Un `yield return` instruction est exécutée comme suit :

*  L’expression donnée dans l’instruction est évaluée, implicitement convertie vers le type yield et assignée à la `Current` propriété de l’objet énumérateur.
*  L’exécution du bloc itérateur est suspendue. Si le `yield return` instruction se trouve dans un ou plusieurs `try` bloque associé `finally` blocs ne sont pas exécutées pour l’instant.
*  Le `MoveNext` méthode de l’objet énumérateur renvoie `true` à son appelant, indiquant que l’objet énumérateur a réussi à avancer jusqu'à l’élément suivant.

L’appel suivant à l’objet énumérateur `MoveNext` méthode reprend l’exécution du bloc itérateur à partir de l’endroit où il a été suspendue en dernier.

Un `yield break` instruction est exécutée comme suit :

*  Si le `yield break` instruction est englobée par un ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction. Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction. Ce processus est répété jusqu'à ce que le `finally` bloque tous les caractères entourant `try` instructions ont été exécutées.
*  Contrôle est retourné à l’appelant du bloc itérateur. Il s’agit soit du `MoveNext` méthode ou `Dispose` méthode de l’objet énumérateur.

Étant donné qu’un `yield break` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `yield break` instruction n’est jamais accessible.
