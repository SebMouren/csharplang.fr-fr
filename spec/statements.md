---
ms.openlocfilehash: 94346034a667ad4af26796c0c4bbc96d6ed79aba
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876834"
---
# <a name="statements"></a><span data-ttu-id="8dba2-101">Instructions</span><span class="sxs-lookup"><span data-stu-id="8dba2-101">Statements</span></span>

<span data-ttu-id="8dba2-102">C#fournit une série d’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-102">C# provides a variety of statements.</span></span> <span data-ttu-id="8dba2-103">La plupart de ces instructions seront familières aux développeurs qui ont programmé en C C++et.</span><span class="sxs-lookup"><span data-stu-id="8dba2-103">Most of these statements will be familiar to developers who have programmed in C and C++.</span></span>

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

<span data-ttu-id="8dba2-104">Le *embedded_statement* non terminal est utilisé pour les instructions qui s’affichent dans d’autres instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-104">The *embedded_statement* nonterminal is used for statements that appear within other statements.</span></span> <span data-ttu-id="8dba2-105">L’utilisation de *embedded_statement* au lieu d’une *instruction* exclut l’utilisation d’instructions de déclaration et d’instructions étiquetées dans ces contextes.</span><span class="sxs-lookup"><span data-stu-id="8dba2-105">The use of *embedded_statement* rather than *statement* excludes the use of declaration statements and labeled statements in these contexts.</span></span> <span data-ttu-id="8dba2-106">L’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-106">The example</span></span>
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
<span data-ttu-id="8dba2-107">génère une erreur au moment de la compilation, `if` car une instruction requiert un *embedded_statement* plutôt qu’une *instruction* pour sa branche if.</span><span class="sxs-lookup"><span data-stu-id="8dba2-107">results in a compile-time error because an `if` statement requires an *embedded_statement* rather than a *statement* for its if branch.</span></span> <span data-ttu-id="8dba2-108">Si ce code est autorisé, la variable `i` est déclarée, mais elle n’a jamais été utilisée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-108">If this code were permitted, then the variable `i` would be declared, but it could never be used.</span></span> <span data-ttu-id="8dba2-109">Notez, toutefois, qu’en plaçant `i`la déclaration de dans un bloc, l’exemple est valide.</span><span class="sxs-lookup"><span data-stu-id="8dba2-109">Note, however, that by placing `i`'s declaration in a block, the example is valid.</span></span>

## <a name="end-points-and-reachability"></a><span data-ttu-id="8dba2-110">Points de terminaison et accessibilité</span><span class="sxs-lookup"><span data-stu-id="8dba2-110">End points and reachability</span></span>

<span data-ttu-id="8dba2-111">Chaque instruction a un ***point de terminaison***.</span><span class="sxs-lookup"><span data-stu-id="8dba2-111">Every statement has an ***end point***.</span></span> <span data-ttu-id="8dba2-112">En termes intuitifs, le point de terminaison d’une instruction correspond à l’emplacement qui suit immédiatement l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-112">In intuitive terms, the end point of a statement is the location that immediately follows the statement.</span></span> <span data-ttu-id="8dba2-113">Les règles d’exécution des instructions composites (instructions qui contiennent des instructions incorporées) spécifient l’action qui est effectuée lorsque le contrôle atteint le point de terminaison d’une instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-113">The execution rules for composite statements (statements that contain embedded statements) specify the action that is taken when control reaches the end point of an embedded statement.</span></span> <span data-ttu-id="8dba2-114">Par exemple, lorsque le contrôle atteint le point de terminaison d’une instruction dans un bloc, le contrôle est transféré à l’instruction suivante dans le bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-114">For example, when control reaches the end point of a statement in a block, control is transferred to the next statement in the block.</span></span>

<span data-ttu-id="8dba2-115">Si une instruction peut être atteinte par l’exécution, l’instruction est dite ***accessible***.</span><span class="sxs-lookup"><span data-stu-id="8dba2-115">If a statement can possibly be reached by execution, the statement is said to be ***reachable***.</span></span> <span data-ttu-id="8dba2-116">À l’inverse, s’il n’est pas possible qu’une instruction soit exécutée, l’instruction est dite ***inaccessible***.</span><span class="sxs-lookup"><span data-stu-id="8dba2-116">Conversely, if there is no possibility that a statement will be executed, the statement is said to be ***unreachable***.</span></span>

<span data-ttu-id="8dba2-117">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-117">In the example</span></span>
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
<span data-ttu-id="8dba2-118">le deuxième appel de `Console.WriteLine` est inaccessible, car il n’est pas possible que l’instruction soit exécutée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-118">the second invocation of `Console.WriteLine` is unreachable because there is no possibility that the statement will be executed.</span></span>

<span data-ttu-id="8dba2-119">Un avertissement est signalé si le compilateur détermine qu’une instruction est inaccessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-119">A warning is reported if the compiler determines that a statement is unreachable.</span></span> <span data-ttu-id="8dba2-120">Il ne s’agit pas d’une erreur spécifique pour qu’une instruction soit inaccessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-120">It is specifically not an error for a statement to be unreachable.</span></span>

<span data-ttu-id="8dba2-121">Pour déterminer si une instruction ou un point de terminaison particulier est accessible, le compilateur effectue une analyse de workflow en fonction des règles d’accessibilité définies pour chaque instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-121">To determine whether a particular statement or end point is reachable, the compiler performs flow analysis according to the reachability rules defined for each statement.</span></span> <span data-ttu-id="8dba2-122">L’analyse du workflow prend en compte les valeurs des expressions constantes ([expressions constantes](expressions.md#constant-expressions)) qui contrôlent le comportement des instructions, mais les valeurs possibles des expressions non constantes ne sont pas prises en compte.</span><span class="sxs-lookup"><span data-stu-id="8dba2-122">The flow analysis takes into account the values of constant expressions ([Constant expressions](expressions.md#constant-expressions)) that control the behavior of statements, but the possible values of non-constant expressions are not considered.</span></span> <span data-ttu-id="8dba2-123">En d’autres termes, à des fins d’analyse de workflow de contrôle, une expression non constante d’un type donné est considérée comme ayant n’importe quelle valeur possible de ce type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-123">In other words, for purposes of control flow analysis, a non-constant expression of a given type is considered to have any possible value of that type.</span></span>

<span data-ttu-id="8dba2-124">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-124">In the example</span></span>
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
<span data-ttu-id="8dba2-125">l’expression booléenne de l' `if` instruction est une expression constante, car les deux opérandes `==` de l’opérateur sont des constantes.</span><span class="sxs-lookup"><span data-stu-id="8dba2-125">the boolean expression of the `if` statement is a constant expression because both operands of the `==` operator are constants.</span></span> <span data-ttu-id="8dba2-126">Étant donné que l’expression constante est évaluée au moment de la compilation `false`, produisant `Console.WriteLine` la valeur, l’appel est considéré comme inaccessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-126">As the constant expression is evaluated at compile-time, producing the value `false`, the `Console.WriteLine` invocation is considered unreachable.</span></span> <span data-ttu-id="8dba2-127">Toutefois, si `i` est remplacé par une variable locale</span><span class="sxs-lookup"><span data-stu-id="8dba2-127">However, if `i` is changed to be a local variable</span></span>
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
<span data-ttu-id="8dba2-128">l' `Console.WriteLine` appel est considéré comme accessible, même si, en réalité, il ne sera jamais exécuté.</span><span class="sxs-lookup"><span data-stu-id="8dba2-128">the `Console.WriteLine` invocation is considered reachable, even though, in reality, it will never be executed.</span></span>

<span data-ttu-id="8dba2-129">Le *bloc* d’un membre de fonction est toujours considéré comme accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-129">The *block* of a function member is always considered reachable.</span></span> <span data-ttu-id="8dba2-130">En évaluant successivement les règles d’accessibilité de chaque instruction dans un bloc, vous pouvez déterminer l’accessibilité d’une instruction donnée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-130">By successively evaluating the reachability rules of each statement in a block, the reachability of any given statement can be determined.</span></span>

<span data-ttu-id="8dba2-131">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-131">In the example</span></span>
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
<span data-ttu-id="8dba2-132">l’accessibilité de la seconde `Console.WriteLine` est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-132">the reachability of the second `Console.WriteLine` is determined as follows:</span></span>

*  <span data-ttu-id="8dba2-133">La première `Console.WriteLine` instruction d’expression est accessible car le bloc de la `F` méthode est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-133">The first `Console.WriteLine` expression statement is reachable because the block of the `F` method is reachable.</span></span>
*  <span data-ttu-id="8dba2-134">Le point de terminaison de la `Console.WriteLine` première instruction d’expression est accessible car cette instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-134">The end point of the first `Console.WriteLine` expression statement is reachable because that statement is reachable.</span></span>
*  <span data-ttu-id="8dba2-135">L' `if` instruction est accessible parce que le point de terminaison de la `Console.WriteLine` première instruction d’expression est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-135">The `if` statement is reachable because the end point of the first `Console.WriteLine` expression statement is reachable.</span></span>
*  <span data-ttu-id="8dba2-136">La deuxième `Console.WriteLine` instruction d’expression est accessible parce que l’expression booléenne de `if` l’instruction n’a pas la valeur `false`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-136">The second `Console.WriteLine` expression statement is reachable because the boolean expression of the `if` statement does not have the constant value `false`.</span></span>

<span data-ttu-id="8dba2-137">Il existe deux situations dans lesquelles il s’agit d’une erreur de compilation pour que le point de terminaison d’une instruction soit accessible :</span><span class="sxs-lookup"><span data-stu-id="8dba2-137">There are two situations in which it is a compile-time error for the end point of a statement to be reachable:</span></span>

*  <span data-ttu-id="8dba2-138">Étant donné `switch` que l’instruction ne permet pas à une section switch de passer à la section switch suivante, il s’agit d’une erreur de compilation pour le point de terminaison de la liste d’instructions d’une section switch à atteindre.</span><span class="sxs-lookup"><span data-stu-id="8dba2-138">Because the `switch` statement does not permit a switch section to "fall through" to the next switch section, it is a compile-time error for the end point of the statement list of a switch section to be reachable.</span></span> <span data-ttu-id="8dba2-139">Si cette erreur se produit, cela indique généralement qu’une `break` instruction est manquante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-139">If this error occurs, it is typically an indication that a `break` statement is missing.</span></span>
*  <span data-ttu-id="8dba2-140">Il s’agit d’une erreur au moment de la compilation pour le point de terminaison du bloc d’un membre de fonction qui calcule une valeur à atteindre.</span><span class="sxs-lookup"><span data-stu-id="8dba2-140">It is a compile-time error for the end point of the block of a function member that computes a value to be reachable.</span></span> <span data-ttu-id="8dba2-141">Si cette erreur se produit, cela indique généralement qu’une `return` instruction est manquante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-141">If this error occurs, it typically is an indication that a `return` statement is missing.</span></span>

## <a name="blocks"></a><span data-ttu-id="8dba2-142">Blocs</span><span class="sxs-lookup"><span data-stu-id="8dba2-142">Blocks</span></span>

<span data-ttu-id="8dba2-143">Un *bloc* autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-143">A *block* permits multiple statements to be written in contexts where a single statement is allowed.</span></span>

```antlr
block
    : '{' statement_list? '}'
    ;
```

<span data-ttu-id="8dba2-144">Un *bloc* se compose d’un *statement_list* facultatif ([listes d’instructions](statements.md#statement-lists)), placé entre accolades.</span><span class="sxs-lookup"><span data-stu-id="8dba2-144">A *block* consists of an optional *statement_list* ([Statement lists](statements.md#statement-lists)), enclosed in braces.</span></span> <span data-ttu-id="8dba2-145">Si la liste d’instructions est omise, le bloc est dit vide.</span><span class="sxs-lookup"><span data-stu-id="8dba2-145">If the statement list is omitted, the block is said to be empty.</span></span>

<span data-ttu-id="8dba2-146">Un bloc peut contenir des instructions de déclaration ([instructions de déclaration](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-146">A block may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="8dba2-147">La portée d’une variable locale ou d’une constante déclarée dans un bloc est le bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-147">The scope of a local variable or constant declared in a block is the block.</span></span>

<span data-ttu-id="8dba2-148">Un bloc est exécuté comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-148">A block is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-149">Si le bloc est vide, le contrôle est transféré vers le point de terminaison du bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-149">If the block is empty, control is transferred to the end point of the block.</span></span>
*  <span data-ttu-id="8dba2-150">Si le bloc n’est pas vide, le contrôle est transféré à la liste d’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-150">If the block is not empty, control is transferred to the statement list.</span></span> <span data-ttu-id="8dba2-151">Quand et si le contrôle atteint le point de terminaison de la liste d’instructions, le contrôle est transféré vers le point de terminaison du bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-151">When and if control reaches the end point of the statement list, control is transferred to the end point of the block.</span></span>

<span data-ttu-id="8dba2-152">La liste d’instructions d’un bloc est accessible si le bloc lui-même est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-152">The statement list of a block is reachable if the block itself is reachable.</span></span>

<span data-ttu-id="8dba2-153">Le point de terminaison d’un bloc est accessible si le bloc est vide ou si le point de terminaison de la liste d’instructions est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-153">The end point of a block is reachable if the block is empty or if the end point of the statement list is reachable.</span></span>

<span data-ttu-id="8dba2-154">Un *bloc* qui contient une ou plusieurs `yield` instructions ([l’instruction yield](statements.md#the-yield-statement)) est appelé un bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-154">A *block* that contains one or more `yield` statements ([The yield statement](statements.md#the-yield-statement)) is called an iterator block.</span></span> <span data-ttu-id="8dba2-155">Les blocs itérateurs sont utilisés pour implémenter des membres de fonction en tant qu’itérateurs ([itérateurs](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-155">Iterator blocks are used to implement function members as iterators ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="8dba2-156">Certaines restrictions supplémentaires s’appliquent aux blocs Iterator :</span><span class="sxs-lookup"><span data-stu-id="8dba2-156">Some additional restrictions apply to iterator blocks:</span></span>

*  <span data-ttu-id="8dba2-157">Il s’agit d’une erreur de compilation pour `return` qu’une instruction apparaisse dans un bloc itérateur `yield return` (les instructions sont autorisées).</span><span class="sxs-lookup"><span data-stu-id="8dba2-157">It is a compile-time error for a `return` statement to appear in an iterator block (but `yield return` statements are permitted).</span></span>
*  <span data-ttu-id="8dba2-158">Il s’agit d’une erreur au moment de la compilation pour qu’un bloc itérateur contienne un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-158">It is a compile-time error for an iterator block to contain an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="8dba2-159">Un bloc itérateur définit toujours un contexte sécurisé, même quand sa déclaration est imbriquée dans un contexte unsafe.</span><span class="sxs-lookup"><span data-stu-id="8dba2-159">An iterator block always defines a safe context, even when its declaration is nested in an unsafe context.</span></span>

### <a name="statement-lists"></a><span data-ttu-id="8dba2-160">Listes d’instructions</span><span class="sxs-lookup"><span data-stu-id="8dba2-160">Statement lists</span></span>

<span data-ttu-id="8dba2-161">Une ***liste*** d’instructions se compose d’une ou de plusieurs instructions écrites en séquence.</span><span class="sxs-lookup"><span data-stu-id="8dba2-161">A ***statement list*** consists of one or more statements written in sequence.</span></span> <span data-ttu-id="8dba2-162">Les *listes d’instructions se trouvent dans*les blocs ([blocs](statements.md#blocks)) et dans *switch_block*s ([instruction switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-162">Statement lists occur in *block*s ([Blocks](statements.md#blocks)) and in *switch_block*s ([The switch statement](statements.md#the-switch-statement)).</span></span>

```antlr
statement_list
    : statement+
    ;
```

<span data-ttu-id="8dba2-163">Une liste d’instructions est exécutée en transférant le contrôle à la première instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-163">A statement list is executed by transferring control to the first statement.</span></span> <span data-ttu-id="8dba2-164">Quand et si le contrôle atteint le point de terminaison d’une instruction, le contrôle est transféré à l’instruction suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-164">When and if control reaches the end point of a statement, control is transferred to the next statement.</span></span> <span data-ttu-id="8dba2-165">Quand et si le contrôle atteint le point de terminaison de la dernière instruction, le contrôle est transféré vers le point de terminaison de la liste d’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-165">When and if control reaches the end point of the last statement, control is transferred to the end point of the statement list.</span></span>

<span data-ttu-id="8dba2-166">Une instruction dans une liste d’instructions est accessible si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-166">A statement in a statement list is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-167">L’instruction est la première instruction et la liste d’instructions elle-même est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-167">The statement is the first statement and the statement list itself is reachable.</span></span>
*  <span data-ttu-id="8dba2-168">Le point de terminaison de l’instruction précédente est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-168">The end point of the preceding statement is reachable.</span></span>
*  <span data-ttu-id="8dba2-169">L’instruction est une instruction étiquetée et l’étiquette est référencée par une `goto` instruction accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-169">The statement is a labeled statement and the label is referenced by a reachable `goto` statement.</span></span>

<span data-ttu-id="8dba2-170">Le point de terminaison d’une liste d’instructions est accessible si le point de terminaison de la dernière instruction de la liste est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-170">The end point of a statement list is reachable if the end point of the last statement in the list is reachable.</span></span>

## <a name="the-empty-statement"></a><span data-ttu-id="8dba2-171">Instruction vide</span><span class="sxs-lookup"><span data-stu-id="8dba2-171">The empty statement</span></span>

<span data-ttu-id="8dba2-172">Un *empty_statement* ne fait rien.</span><span class="sxs-lookup"><span data-stu-id="8dba2-172">An *empty_statement* does nothing.</span></span>

```antlr
empty_statement
    : ';'
    ;
```

<span data-ttu-id="8dba2-173">Une instruction vide est utilisée lorsqu’il n’y a aucune opération à effectuer dans un contexte où une instruction est requise.</span><span class="sxs-lookup"><span data-stu-id="8dba2-173">An empty statement is used when there are no operations to perform in a context where a statement is required.</span></span>

<span data-ttu-id="8dba2-174">L’exécution d’une instruction vide transfère simplement le contrôle au point de terminaison de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-174">Execution of an empty statement simply transfers control to the end point of the statement.</span></span> <span data-ttu-id="8dba2-175">Ainsi, le point de terminaison d’une instruction vide est accessible si l’instruction vide est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-175">Thus, the end point of an empty statement is reachable if the empty statement is reachable.</span></span>

<span data-ttu-id="8dba2-176">Une instruction vide peut être utilisée lors de l' `while` écriture d’une instruction avec un corps NULL :</span><span class="sxs-lookup"><span data-stu-id="8dba2-176">An empty statement can be used when writing a `while` statement with a null body:</span></span>
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

<span data-ttu-id="8dba2-177">En outre, une instruction vide peut être utilisée pour déclarer une étiquette juste avant la fermeture`}`«» d’un bloc :</span><span class="sxs-lookup"><span data-stu-id="8dba2-177">Also, an empty statement can be used to declare a label just before the closing "`}`" of a block:</span></span>
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a><span data-ttu-id="8dba2-178">Instructions étiquetées</span><span class="sxs-lookup"><span data-stu-id="8dba2-178">Labeled statements</span></span>

<span data-ttu-id="8dba2-179">Un *labeled_statement* permet à une instruction d’être précédée d’une étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-179">A *labeled_statement* permits a statement to be prefixed by a label.</span></span> <span data-ttu-id="8dba2-180">Les instructions étiquetées sont autorisées dans des blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.</span><span class="sxs-lookup"><span data-stu-id="8dba2-180">Labeled statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

<span data-ttu-id="8dba2-181">Une instruction étiquetée déclare une étiquette portant le nom donné par l' *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-181">A labeled statement declares a label with the name given by the *identifier*.</span></span> <span data-ttu-id="8dba2-182">L’étendue d’une étiquette est le bloc entier dans lequel l’étiquette est déclarée, y compris les blocs imbriqués.</span><span class="sxs-lookup"><span data-stu-id="8dba2-182">The scope of a label is the whole block in which the label is declared, including any nested blocks.</span></span> <span data-ttu-id="8dba2-183">Il s’agit d’une erreur au moment de la compilation pour deux étiquettes portant le même nom pour lesquelles des étendues se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="8dba2-183">It is a compile-time error for two labels with the same name to have overlapping scopes.</span></span>

<span data-ttu-id="8dba2-184">Une étiquette peut être référencée à `goto` partir d’instructions ([l’instruction goto](statements.md#the-goto-statement)) dans la portée de l’étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-184">A label can be referenced from `goto` statements ([The goto statement](statements.md#the-goto-statement)) within the scope of the label.</span></span> <span data-ttu-id="8dba2-185">Cela signifie que `goto` les instructions peuvent transférer le contrôle dans les blocs et hors des blocs, mais jamais dans des blocs.</span><span class="sxs-lookup"><span data-stu-id="8dba2-185">This means that `goto` statements can transfer control within blocks and out of blocks, but never into blocks.</span></span>

<span data-ttu-id="8dba2-186">Les étiquettes ont leur propre espace de déclaration et n’interfèrent pas avec d’autres identificateurs.</span><span class="sxs-lookup"><span data-stu-id="8dba2-186">Labels have their own declaration space and do not interfere with other identifiers.</span></span> <span data-ttu-id="8dba2-187">L’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-187">The example</span></span>
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
<span data-ttu-id="8dba2-188">est valide et utilise le nom `x` en tant que paramètre et étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-188">is valid and uses the name `x` as both a parameter and a label.</span></span>

<span data-ttu-id="8dba2-189">L’exécution d’une instruction étiquetée correspond exactement à l’exécution de l’instruction qui suit l’étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-189">Execution of a labeled statement corresponds exactly to execution of the statement following the label.</span></span>

<span data-ttu-id="8dba2-190">Outre l’accessibilité fournie par le workflow normal de contrôle, une instruction étiquetée est accessible si l’étiquette est référencée par une `goto` instruction accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-190">In addition to the reachability provided by normal flow of control, a labeled statement is reachable if the label is referenced by a reachable `goto` statement.</span></span> <span data-ttu-id="8dba2-191">Titre Si une `goto` instruction est à l' `try` intérieur d’un `finally` qui comprend un bloc et que l’instruction étiquetée est en dehors de `finally` la `try`, et que le point de terminaison du bloc est inaccessible, l’instruction étiquetée n’est pas accessible à partir de cette `goto` instruction.)</span><span class="sxs-lookup"><span data-stu-id="8dba2-191">(Exception: If a `goto` statement is inside a `try` that includes a `finally` block, and the labeled statement is outside the `try`, and the end point of the `finally` block is unreachable, then the labeled statement is not reachable from that `goto` statement.)</span></span>

## <a name="declaration-statements"></a><span data-ttu-id="8dba2-192">Instructions de déclaration</span><span class="sxs-lookup"><span data-stu-id="8dba2-192">Declaration statements</span></span>

<span data-ttu-id="8dba2-193">Un *declaration_statement* déclare une variable locale ou une constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-193">A *declaration_statement* declares a local variable or constant.</span></span> <span data-ttu-id="8dba2-194">Les instructions de déclaration sont autorisées dans des blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.</span><span class="sxs-lookup"><span data-stu-id="8dba2-194">Declaration statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a><span data-ttu-id="8dba2-195">Déclarations de variables locales</span><span class="sxs-lookup"><span data-stu-id="8dba2-195">Local variable declarations</span></span>

<span data-ttu-id="8dba2-196">Un *local_variable_declaration* déclare une ou plusieurs variables locales.</span><span class="sxs-lookup"><span data-stu-id="8dba2-196">A *local_variable_declaration* declares one or more local variables.</span></span>

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

<span data-ttu-id="8dba2-197">Le *local_variable_type* d’un *local_variable_declaration* spécifie directement le type des variables introduites par la déclaration, ou indique avec `var` l’identificateur que le type doit être déduit en fonction d’une initialiseur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-197">The *local_variable_type* of a *local_variable_declaration* either directly specifies the type of the variables introduced by the declaration, or indicates with the identifier `var` that the type should be inferred based on an initializer.</span></span> <span data-ttu-id="8dba2-198">Le type est suivi d’une liste de *local_variable_declarator*s, chacun d’entre eux introduisant une nouvelle variable.</span><span class="sxs-lookup"><span data-stu-id="8dba2-198">The type is followed by a list of *local_variable_declarator*s, each of which introduces a new variable.</span></span> <span data-ttu-id="8dba2-199">Un *local_variable_declarator* se compose d’un *identificateur* qui nomme la variable, éventuellement suivie d’un jeton «`=`» et d’un *local_variable_initializer* qui donne la valeur initiale de la variable.</span><span class="sxs-lookup"><span data-stu-id="8dba2-199">A *local_variable_declarator* consists of an *identifier* that names the variable, optionally followed by an "`=`" token and a *local_variable_initializer* that gives the initial value of the variable.</span></span>

<span data-ttu-id="8dba2-200">Dans le contexte d’une déclaration de variable locale, l’identificateur var agit comme un mot clé contextuel ([Mots clés](lexical-structure.md#keywords)). Lorsque *local_variable_type* est spécifié comme et `var` qu’aucun type nommé `var` n’est dans la portée, la déclaration est une ***déclaration de variable locale implicitement typée***, dont le type est déduit du type de l’initialiseur associé formule.</span><span class="sxs-lookup"><span data-stu-id="8dba2-200">In the context of a local variable declaration, the identifier var acts as a contextual keyword ([Keywords](lexical-structure.md#keywords)).When the *local_variable_type* is specified as `var` and no type named `var` is in scope, the declaration is an ***implicitly typed local variable declaration***, whose type is inferred from the type of the associated initializer expression.</span></span> <span data-ttu-id="8dba2-201">Les déclarations de variables locales implicitement typées sont soumises aux restrictions suivantes :</span><span class="sxs-lookup"><span data-stu-id="8dba2-201">Implicitly typed local variable declarations are subject to the following restrictions:</span></span>

*  <span data-ttu-id="8dba2-202">Le *local_variable_declaration* ne peut pas inclure plusieurs *local_variable_declarator*s.</span><span class="sxs-lookup"><span data-stu-id="8dba2-202">The *local_variable_declaration* cannot include multiple *local_variable_declarator*s.</span></span>
*  <span data-ttu-id="8dba2-203">*Local_variable_declarator* doit inclure un *local_variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-203">The *local_variable_declarator* must include a *local_variable_initializer*.</span></span>
*  <span data-ttu-id="8dba2-204">*Local_variable_initializer* doit être une *expression*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-204">The *local_variable_initializer* must be an *expression*.</span></span>
*  <span data-ttu-id="8dba2-205">L' *expression* d’initialiseur doit avoir un type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="8dba2-205">The initializer *expression* must have a compile-time type.</span></span>
*  <span data-ttu-id="8dba2-206">L' *expression* d’initialiseur ne peut pas faire référence à la variable déclarée elle-même</span><span class="sxs-lookup"><span data-stu-id="8dba2-206">The initializer *expression* cannot refer to the declared variable itself</span></span>

<span data-ttu-id="8dba2-207">Voici des exemples de déclarations de variables locales implicitement typées incorrectes :</span><span class="sxs-lookup"><span data-stu-id="8dba2-207">The following are examples of incorrect implicitly typed local variable declarations:</span></span>

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

<span data-ttu-id="8dba2-208">La valeur d’une variable locale est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)) et la valeur d’une variable locale est modifiée à l’aide d’une *assignation* ([opérateurs d’affectation](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-208">The value of a local variable is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)), and the value of a local variable is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="8dba2-209">Une variable locale doit être assignée définitivement ([assignation définie](variables.md#definite-assignment)) à chaque emplacement où sa valeur est obtenue.</span><span class="sxs-lookup"><span data-stu-id="8dba2-209">A local variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at each location where its value is obtained.</span></span>

<span data-ttu-id="8dba2-210">La portée d’une variable locale déclarée dans un *local_variable_declaration* est le bloc dans lequel la déclaration se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-210">The scope of a local variable declared in a *local_variable_declaration* is the block in which the declaration occurs.</span></span> <span data-ttu-id="8dba2-211">La référence à une variable locale dans une position textuelle qui précède le *local_variable_declarator* de la variable locale est une erreur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-211">It is an error to refer to a local variable in a textual position that precedes the *local_variable_declarator* of the local variable.</span></span> <span data-ttu-id="8dba2-212">Dans l’étendue d’une variable locale, il s’agit d’une erreur de compilation pour déclarer une autre constante ou variable locale portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="8dba2-212">Within the scope of a local variable, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="8dba2-213">Une déclaration de variable locale qui déclare plusieurs variables est équivalente à plusieurs déclarations de variables uniques de même type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-213">A local variable declaration that declares multiple variables is equivalent to multiple declarations of single variables with the same type.</span></span> <span data-ttu-id="8dba2-214">En outre, un initialiseur de variable dans une déclaration de variable locale correspond exactement à une instruction d’assignation qui est insérée immédiatement après la déclaration.</span><span class="sxs-lookup"><span data-stu-id="8dba2-214">Furthermore, a variable initializer in a local variable declaration corresponds exactly to an assignment statement that is inserted immediately after the declaration.</span></span>

<span data-ttu-id="8dba2-215">L’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-215">The example</span></span>
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
<span data-ttu-id="8dba2-216">correspond exactement à</span><span class="sxs-lookup"><span data-stu-id="8dba2-216">corresponds exactly to</span></span>
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

<span data-ttu-id="8dba2-217">Dans une déclaration de variable locale implicitement typée, le type de la variable locale déclarée est considéré comme étant le même que le type de l’expression utilisée pour initialiser la variable.</span><span class="sxs-lookup"><span data-stu-id="8dba2-217">In an implicitly typed local variable declaration, the type of the local variable being declared is taken to be the same as the type of the expression used to initialize the variable.</span></span> <span data-ttu-id="8dba2-218">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8dba2-218">For example:</span></span>
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

<span data-ttu-id="8dba2-219">Les déclarations de variables locales implicitement typées ci-dessus sont équivalentes précisément aux déclarations explicitement typées suivantes :</span><span class="sxs-lookup"><span data-stu-id="8dba2-219">The implicitly typed local variable declarations above are precisely equivalent to the following explicitly typed declarations:</span></span>
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a><span data-ttu-id="8dba2-220">Déclarations de constantes locales</span><span class="sxs-lookup"><span data-stu-id="8dba2-220">Local constant declarations</span></span>

<span data-ttu-id="8dba2-221">Un *local_constant_declaration* déclare une ou plusieurs constantes locales.</span><span class="sxs-lookup"><span data-stu-id="8dba2-221">A *local_constant_declaration* declares one or more local constants.</span></span>

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

<span data-ttu-id="8dba2-222">Le *type* d’un *local_constant_declaration* spécifie le type des constantes introduites par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="8dba2-222">The *type* of a *local_constant_declaration* specifies the type of the constants introduced by the declaration.</span></span> <span data-ttu-id="8dba2-223">Le type est suivi d’une liste de *constant_declarator*s, chacun d’entre eux introduisant une nouvelle constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-223">The type is followed by a list of *constant_declarator*s, each of which introduces a new constant.</span></span> <span data-ttu-id="8dba2-224">Un *constant_declarator* se compose d’un *identificateur* qui nomme la constante, suivi d’un jeton`=`«», suivi d’un *constant_expression* ([expressions constantes](expressions.md#constant-expressions)) qui donne la valeur de la constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-224">A *constant_declarator* consists of an *identifier* that names the constant, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the constant.</span></span>

<span data-ttu-id="8dba2-225">Le *type* et le *constant_expression* d’une déclaration de constante locale doivent suivre les mêmes règles que celles d’une déclaration de membre constante ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-225">The *type* and *constant_expression* of a local constant declaration must follow the same rules as those of a constant member declaration ([Constants](classes.md#constants)).</span></span>

<span data-ttu-id="8dba2-226">La valeur d’une constante locale est obtenue dans une expression à l’aide d’un *simple_name* ([noms simples](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-226">The value of a local constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="8dba2-227">La portée d’une constante locale est le bloc dans lequel la déclaration se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-227">The scope of a local constant is the block in which the declaration occurs.</span></span> <span data-ttu-id="8dba2-228">Il est erroné de faire référence à une constante locale dans une position textuelle qui précède son *constant_declarator*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-228">It is an error to refer to a local constant in a textual position that precedes its *constant_declarator*.</span></span> <span data-ttu-id="8dba2-229">Dans l’étendue d’une constante locale, il s’agit d’une erreur de compilation pour déclarer une autre constante ou variable locale portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="8dba2-229">Within the scope of a local constant, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="8dba2-230">Une déclaration de constante locale qui déclare plusieurs constantes est équivalente à plusieurs déclarations de constantes uniques avec le même type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-230">A local constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same type.</span></span>

## <a name="expression-statements"></a><span data-ttu-id="8dba2-231">Instructions d’expression</span><span class="sxs-lookup"><span data-stu-id="8dba2-231">Expression statements</span></span>

<span data-ttu-id="8dba2-232">Un *expression_statement* évalue une expression donnée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-232">An *expression_statement* evaluates a given expression.</span></span> <span data-ttu-id="8dba2-233">La valeur calculée par l’expression, le cas échéant, est ignorée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-233">The value computed by the expression, if any, is discarded.</span></span>

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

<span data-ttu-id="8dba2-234">Toutes les expressions ne sont pas autorisées en tant qu’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-234">Not all expressions are permitted as statements.</span></span> <span data-ttu-id="8dba2-235">En particulier, les expressions telles `x + y` que `x == 1` et qui calculent simplement une valeur (qui sera ignorée) ne sont pas autorisées en tant qu’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-235">In particular, expressions such as `x + y` and `x == 1` that merely compute a value (which will be discarded), are not permitted as statements.</span></span>

<span data-ttu-id="8dba2-236">L’exécution d’un *expression_statement* évalue l’expression contenue, puis transfère le contrôle au point de terminaison du *expression_statement*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-236">Execution of an *expression_statement* evaluates the contained expression and then transfers control to the end point of the *expression_statement*.</span></span> <span data-ttu-id="8dba2-237">Le point de terminaison d’un *expression_statement* est accessible si ce *expression_statement* est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-237">The end point of an *expression_statement* is reachable if that *expression_statement* is reachable.</span></span>

## <a name="selection-statements"></a><span data-ttu-id="8dba2-238">Instructions de sélection</span><span class="sxs-lookup"><span data-stu-id="8dba2-238">Selection statements</span></span>

<span data-ttu-id="8dba2-239">Instructions de sélection sélectionnez l’une des nombreuses instructions possibles pour l’exécution en fonction de la valeur d’une expression.</span><span class="sxs-lookup"><span data-stu-id="8dba2-239">Selection statements select one of a number of possible statements for execution based on the value of some expression.</span></span>

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a><span data-ttu-id="8dba2-240">L’instruction if</span><span class="sxs-lookup"><span data-stu-id="8dba2-240">The if statement</span></span>

<span data-ttu-id="8dba2-241">L' `if` instruction sélectionne une instruction à exécuter en fonction de la valeur d’une expression booléenne.</span><span class="sxs-lookup"><span data-stu-id="8dba2-241">The `if` statement selects a statement for execution based on the value of a boolean expression.</span></span>

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="8dba2-242">Un `else` composant est associé au précédent `if` lexicalement le plus proche qui est autorisé par la syntaxe.</span><span class="sxs-lookup"><span data-stu-id="8dba2-242">An `else` part is associated with the lexically nearest preceding `if` that is allowed by the syntax.</span></span> <span data-ttu-id="8dba2-243">Ainsi, une `if` instruction de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-243">Thus, an `if` statement of the form</span></span>
```csharp
if (x) if (y) F(); else G();
```
<span data-ttu-id="8dba2-244">est équivalent à</span><span class="sxs-lookup"><span data-stu-id="8dba2-244">is equivalent to</span></span>
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

<span data-ttu-id="8dba2-245">Une `if` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-245">An `if` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-246">L’expression *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-246">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="8dba2-247">Si l’expression booléenne génère `true`, le contrôle est transféré à la première instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-247">If the boolean expression yields `true`, control is transferred to the first embedded statement.</span></span> <span data-ttu-id="8dba2-248">Quand et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de `if` terminaison de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-248">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="8dba2-249">Si l’expression booléenne génère `false` et si une `else` partie est présente, le contrôle est transféré à la deuxième instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-249">If the boolean expression yields `false` and if an `else` part is present, control is transferred to the second embedded statement.</span></span> <span data-ttu-id="8dba2-250">Quand et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de `if` terminaison de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-250">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="8dba2-251">Si l’expression booléenne génère `false` et si une `else` partie n’est pas présente, le contrôle est transféré au point de terminaison de `if` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-251">If the boolean expression yields `false` and if an `else` part is not present, control is transferred to the end point of the `if` statement.</span></span>

<span data-ttu-id="8dba2-252">La première instruction incorporée d' `if` une instruction est accessible si l' `if` instruction est accessible et que l’expression booléenne n’a pas la valeur `false`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-252">The first embedded statement of an `if` statement is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="8dba2-253">La deuxième instruction incorporée d' `if` une instruction, si elle est présente, est accessible `if` si l’instruction est accessible et si l’expression booléenne n’a pas la `true`valeur de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-253">The second embedded statement of an `if` statement, if present, is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

<span data-ttu-id="8dba2-254">Le point de terminaison d' `if` une instruction est accessible si le point de terminaison d’au moins une de ses instructions incorporées est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-254">The end point of an `if` statement is reachable if the end point of at least one of its embedded statements is reachable.</span></span> <span data-ttu-id="8dba2-255">En outre, le point de terminaison d' `if` une instruction `else` sans partie est accessible si l' `if` instruction est accessible et que l’expression booléenne n’a pas la valeur `true`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-255">In addition, the end point of an `if` statement with no `else` part is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-switch-statement"></a><span data-ttu-id="8dba2-256">Instruction switch</span><span class="sxs-lookup"><span data-stu-id="8dba2-256">The switch statement</span></span>

<span data-ttu-id="8dba2-257">L’instruction switch sélectionne pour l’exécution une liste d’instructions ayant une étiquette Switch associée qui correspond à la valeur de l’expression Switch.</span><span class="sxs-lookup"><span data-stu-id="8dba2-257">The switch statement selects for execution a statement list having an associated switch label that corresponds to the value of the switch expression.</span></span>

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

<span data-ttu-id="8dba2-258">Un *switch_Statement* se compose du mot `switch`clé, suivi d’une expression entre parenthèses (appelée expression de commutateur), suivie d’un *switch_block*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-258">A *switch_statement* consists of the keyword `switch`, followed by a parenthesized expression (called the switch expression), followed by a *switch_block*.</span></span> <span data-ttu-id="8dba2-259">*Switch_block* se compose de zéro, un ou plusieurs *switch_section*, entre accolades.</span><span class="sxs-lookup"><span data-stu-id="8dba2-259">The *switch_block* consists of zero or more *switch_section*s, enclosed in braces.</span></span> <span data-ttu-id="8dba2-260">Chaque *switch_section* se compose d’un ou plusieurs *switch_label*, suivis d’un *statement_list* ([listes d’instructions](statements.md#statement-lists)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-260">Each *switch_section* consists of one or more *switch_label*s followed by a *statement_list* ([Statement lists](statements.md#statement-lists)).</span></span>

<span data-ttu-id="8dba2-261">Le ***type gouvernant*** d’une `switch` instruction est établi par l’expression de switch.</span><span class="sxs-lookup"><span data-stu-id="8dba2-261">The ***governing type*** of a `switch` statement is established by the switch expression.</span></span>

*  <span data-ttu-id="8dba2-262">Si le type de l’expression de commutateur `sbyte`est `byte`, `short`, `ushort` `long` `ulong` `bool`,, `uint` `int`,,,, ,`char`, ou`string`  *enum_type*, ou s’il s’agit du type Nullable correspondant à l’un de ces types, il s’agit du type gouvernant `switch` de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-262">If the type of the switch expression is `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, or an *enum_type*, or if it is the nullable type corresponding to one of these types, then that is the governing type of the `switch` statement.</span></span>
*  <span data-ttu-id="8dba2-263">Dans le cas contraire, une seule conversion implicite définie par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) doit exister du type de l’expression de commutateur à l’un `sbyte`des types `short`régis possibles suivants :, `byte`,, `ushort` , `int`, `uint`, ,`long` ,`char`,ou, un type Nullable correspondant à l’un de ces types. `ulong` `string`</span><span class="sxs-lookup"><span data-stu-id="8dba2-263">Otherwise, exactly one user-defined implicit conversion ([User-defined conversions](conversions.md#user-defined-conversions)) must exist from the type of the switch expression to one of the following possible governing types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, or,  a nullable type corresponding to one of those types.</span></span>
*  <span data-ttu-id="8dba2-264">Sinon, si aucune conversion implicite n’existe, ou s’il existe plusieurs conversions implicites, une erreur se produit au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="8dba2-264">Otherwise, if no such implicit conversion exists, or if more than one such implicit conversion exists, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-265">L’expression constante de chaque `case` étiquette doit indiquer une valeur implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type gouvernant de l' `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-265">The constant expression of each `case` label must denote a value that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the `switch` statement.</span></span> <span data-ttu-id="8dba2-266">Une erreur de compilation se produit si au moins deux `case` étiquettes dans la même `switch` instruction spécifient la même valeur de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-266">A compile-time error occurs if two or more `case` labels in the same `switch` statement specify the same constant value.</span></span>

<span data-ttu-id="8dba2-267">Il peut y avoir au plus `default` une étiquette dans une instruction switch.</span><span class="sxs-lookup"><span data-stu-id="8dba2-267">There can be at most one `default` label in a switch statement.</span></span>

<span data-ttu-id="8dba2-268">Une `switch` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-268">A `switch` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-269">L’expression de commutateur est évaluée et convertie en type gouvernant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-269">The switch expression is evaluated and converted to the governing type.</span></span>
*  <span data-ttu-id="8dba2-270">Si l’une des constantes spécifiées dans `case` une étiquette dans la `switch` même instruction est égale à la valeur de l’expression de commutateur, le contrôle est transféré à la `case` liste d’instructions à la suite de l’étiquette correspondante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-270">If one of the constants specified in a `case` label in the same `switch` statement is equal to the value of the switch expression, control is transferred to the statement list following the matched `case` label.</span></span>
*  <span data-ttu-id="8dba2-271">Si aucune des constantes spécifiées dans `case` les étiquettes de la `switch` même instruction n’est égale à la valeur de l’expression de commutateur, `default` et si une étiquette est présente, le contrôle est transféré à la `default` liste d’instructions à la suite du noms.</span><span class="sxs-lookup"><span data-stu-id="8dba2-271">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if a `default` label is present, control is transferred to the statement list following the `default` label.</span></span>
*  <span data-ttu-id="8dba2-272">Si aucune des constantes spécifiées dans `case` les étiquettes de la `switch` même instruction n’est égale à la valeur de l’expression de commutateur, `default` et si aucune étiquette n’est présente, le contrôle est transféré au `switch` point de terminaison de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-272">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if no `default` label is present, control is transferred to the end point of the `switch` statement.</span></span>

<span data-ttu-id="8dba2-273">Si le point de terminaison de la liste d’instructions d’une section switch est accessible, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-273">If the end point of the statement list of a switch section is reachable, a compile-time error occurs.</span></span> <span data-ttu-id="8dba2-274">C’est ce que l’on appelle la règle « aucune chute ».</span><span class="sxs-lookup"><span data-stu-id="8dba2-274">This is known as the "no fall through" rule.</span></span> <span data-ttu-id="8dba2-275">L’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-275">The example</span></span>
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
<span data-ttu-id="8dba2-276">est valide, car aucune section switch n’a un point de terminaison accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-276">is valid because no switch section has a reachable end point.</span></span> <span data-ttu-id="8dba2-277">Contrairement à C C++et, l’exécution d’une section switch n’est pas autorisée à passer à la section switch suivante, et l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-277">Unlike C and C++, execution of a switch section is not permitted to "fall through" to the next switch section, and the example</span></span>
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
<span data-ttu-id="8dba2-278">génère une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="8dba2-278">results in a compile-time error.</span></span> <span data-ttu-id="8dba2-279">Lorsque l’exécution d’une section switch doit être suivie par l’exécution d’une autre section switch, `goto case` une `goto default` instruction explicite ou doit être utilisée :</span><span class="sxs-lookup"><span data-stu-id="8dba2-279">When execution of a switch section is to be followed by execution of another switch section, an explicit `goto case` or `goto default` statement must be used:</span></span>
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

<span data-ttu-id="8dba2-280">Plusieurs étiquettes sont autorisées dans un *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-280">Multiple labels are permitted in a *switch_section*.</span></span> <span data-ttu-id="8dba2-281">L’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-281">The example</span></span>
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
<span data-ttu-id="8dba2-282">est valide.</span><span class="sxs-lookup"><span data-stu-id="8dba2-282">is valid.</span></span> <span data-ttu-id="8dba2-283">L’exemple ne viole pas la règle « aucune chute », car les étiquettes `case 2:` et `default:` font partie du même *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-283">The example does not violate the "no fall through" rule because the labels `case 2:` and `default:` are part of the same *switch_section*.</span></span>

<span data-ttu-id="8dba2-284">La règle « aucune chute » empêche une classe courante de bogues qui se produisent dans C C++ et `break` lorsque les instructions sont omises par inadvertance.</span><span class="sxs-lookup"><span data-stu-id="8dba2-284">The "no fall through" rule prevents a common class of bugs that occur in C and C++ when `break` statements are accidentally omitted.</span></span> <span data-ttu-id="8dba2-285">En outre, en raison de cette règle, les sections Switch d' `switch` une instruction peuvent être réorganisées arbitrairement sans affecter le comportement de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-285">In addition, because of this rule, the switch sections of a `switch` statement can be arbitrarily rearranged without affecting the behavior of the statement.</span></span> <span data-ttu-id="8dba2-286">Par exemple, les sections de l' `switch` instruction ci-dessus peuvent être inversées sans affecter le comportement de l’instruction :</span><span class="sxs-lookup"><span data-stu-id="8dba2-286">For example, the sections of the `switch` statement above can be reversed without affecting the behavior of the statement:</span></span>
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

<span data-ttu-id="8dba2-287">La liste d’instructions d’une section switch se termine `break`généralement `goto case`par une `goto default` instruction,, ou, mais toute construction qui restitue le point de terminaison de la liste d’instructions inaccessible est autorisée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-287">The statement list of a switch section typically ends in a `break`, `goto case`, or `goto default` statement, but any construct that renders the end point of the statement list unreachable is permitted.</span></span> <span data-ttu-id="8dba2-288">Par exemple, une `while` instruction contrôlée par l’expression `true` booléenne est connue pour ne jamais atteindre son point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="8dba2-288">For example, a `while` statement controlled by the boolean expression `true` is known to never reach its end point.</span></span> <span data-ttu-id="8dba2-289">De même, `throw` une `return` instruction ou transfère toujours le contrôle ailleurs et n’atteint jamais son point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="8dba2-289">Likewise, a `throw` or `return` statement always transfers control elsewhere and never reaches its end point.</span></span> <span data-ttu-id="8dba2-290">Ainsi, l’exemple suivant est valide :</span><span class="sxs-lookup"><span data-stu-id="8dba2-290">Thus, the following example is valid:</span></span>
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

<span data-ttu-id="8dba2-291">Le type gouvernant d’une `switch` instruction peut être le type `string`.</span><span class="sxs-lookup"><span data-stu-id="8dba2-291">The governing type of a `switch` statement may be the type `string`.</span></span> <span data-ttu-id="8dba2-292">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8dba2-292">For example:</span></span>
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

<span data-ttu-id="8dba2-293">À l’instar des opérateurs d’égalité de chaînes (opérateurs `switch` d’égalité de[chaînes](expressions.md#string-equality-operators)), l’instruction respecte la casse et exécute une section switch donnée uniquement si la `case` chaîne d’expression de commutateur correspond exactement à une constante d’étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-293">Like the string equality operators ([String equality operators](expressions.md#string-equality-operators)), the `switch` statement is case sensitive and will execute a given switch section only if the switch expression string exactly matches a `case` label constant.</span></span>

<span data-ttu-id="8dba2-294">Lorsque le type gouvernant d’une `switch` instruction est `string`, la valeur `null` est autorisée comme constante d’étiquette case.</span><span class="sxs-lookup"><span data-stu-id="8dba2-294">When the governing type of a `switch` statement is `string`, the value `null` is permitted as a case label constant.</span></span>

<span data-ttu-id="8dba2-295">Les *statement_list*s d’un *switch_block* peuvent contenir des instructions de déclaration ([instructions de déclaration](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-295">The *statement_list*s of a *switch_block* may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="8dba2-296">La portée d’une variable locale ou d’une constante déclarée dans un bloc switch est le bloc switch.</span><span class="sxs-lookup"><span data-stu-id="8dba2-296">The scope of a local variable or constant declared in a switch block is the switch block.</span></span>

<span data-ttu-id="8dba2-297">La liste d’instructions d’une section switch donnée est accessible si l' `switch` instruction est accessible et qu’au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-297">The statement list of a given switch section is reachable if the `switch` statement is reachable and at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-298">L’expression de commutateur est une valeur non constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-298">The switch expression is a non-constant value.</span></span>
*  <span data-ttu-id="8dba2-299">L’expression de commutateur est une valeur constante qui correspond `case` à une étiquette dans la section switch.</span><span class="sxs-lookup"><span data-stu-id="8dba2-299">The switch expression is a constant value that matches a `case` label in the switch section.</span></span>
*  <span data-ttu-id="8dba2-300">L’expression de commutateur est une valeur constante qui ne correspond `case` à aucune étiquette, et la section Switch `default` contient l’étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-300">The switch expression is a constant value that doesn't match any `case` label, and the switch section contains the `default` label.</span></span>
*  <span data-ttu-id="8dba2-301">Une étiquette de commutateur de la section switch est référencée par une instruction `goto case` ou `goto default` accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-301">A switch label of the switch section is referenced by a reachable `goto case` or `goto default` statement.</span></span>

<span data-ttu-id="8dba2-302">Le point de terminaison d' `switch` une instruction est accessible si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-302">The end point of a `switch` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-303">L' `switch` instruction contient une `break` instruction accessible qui quitte l' `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-303">The `switch` statement contains a reachable `break` statement that exits the `switch` statement.</span></span>
*  <span data-ttu-id="8dba2-304">L' `switch` instruction est accessible, l’expression de commutateur est une valeur non constante et aucune `default` étiquette n’est présente.</span><span class="sxs-lookup"><span data-stu-id="8dba2-304">The `switch` statement is reachable, the switch expression is a non-constant value, and no `default` label is present.</span></span>
*  <span data-ttu-id="8dba2-305">L' `switch` instruction est accessible, l’expression de commutateur est une valeur constante qui ne correspond à `case` aucune étiquette et aucune `default` étiquette n’est présente.</span><span class="sxs-lookup"><span data-stu-id="8dba2-305">The `switch` statement is reachable, the switch expression is a constant value that doesn't match any `case` label, and no `default` label is present.</span></span>

## <a name="iteration-statements"></a><span data-ttu-id="8dba2-306">Instructions d’itération</span><span class="sxs-lookup"><span data-stu-id="8dba2-306">Iteration statements</span></span>

<span data-ttu-id="8dba2-307">Les instructions d’itération exécutent plusieurs fois une instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-307">Iteration statements repeatedly execute an embedded statement.</span></span>

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a><span data-ttu-id="8dba2-308">Instruction while</span><span class="sxs-lookup"><span data-stu-id="8dba2-308">The while statement</span></span>

<span data-ttu-id="8dba2-309">L' `while` instruction exécute de manière conditionnelle une instruction incorporée zéro ou plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-309">The `while` statement conditionally executes an embedded statement zero or more times.</span></span>

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

<span data-ttu-id="8dba2-310">Une `while` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-310">A `while` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-311">L’expression *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-311">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="8dba2-312">Si l’expression booléenne génère `true`, le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-312">If the boolean expression yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="8dba2-313">Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut- `continue` être à partir de l’exécution d’une instruction), `while` le contrôle est transféré au début de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-313">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), control is transferred to the beginning of the `while` statement.</span></span>
*  <span data-ttu-id="8dba2-314">Si l’expression booléenne génère `false`, le contrôle est transféré au point de terminaison de l' `while` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-314">If the boolean expression yields `false`, control is transferred to the end point of the `while` statement.</span></span>

<span data-ttu-id="8dba2-315">Dans l’instruction incorporée d' `while` une instruction, `break` une instruction ([instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de `while` l’instruction (terminant ainsi l’itération de l’instruction incorporée) et un `continue` Statement ([instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (ce qui effectue une autre `while` itération de l’instruction).</span><span class="sxs-lookup"><span data-stu-id="8dba2-315">Within the embedded statement of a `while` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `while` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus performing another iteration of the `while` statement).</span></span>

<span data-ttu-id="8dba2-316">L’instruction incorporée d' `while` une instruction est accessible si l' `while` instruction est accessible et que l’expression booléenne n’a pas la valeur `false`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-316">The embedded statement of a `while` statement is reachable if the `while` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="8dba2-317">Le point de terminaison d' `while` une instruction est accessible si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-317">The end point of a `while` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-318">L' `while` instruction contient une `break` instruction accessible qui quitte l' `while` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-318">The `while` statement contains a reachable `break` statement that exits the `while` statement.</span></span>
*  <span data-ttu-id="8dba2-319">L' `while` instruction est accessible et l’expression booléenne n’a pas la valeur `true`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-319">The `while` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-do-statement"></a><span data-ttu-id="8dba2-320">Instruction do</span><span class="sxs-lookup"><span data-stu-id="8dba2-320">The do statement</span></span>

<span data-ttu-id="8dba2-321">L' `do` instruction exécute de manière conditionnelle une instruction incorporée une ou plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-321">The `do` statement conditionally executes an embedded statement one or more times.</span></span>

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

<span data-ttu-id="8dba2-322">Une `do` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-322">A `do` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-323">Le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-323">Control is transferred to the embedded statement.</span></span>
*  <span data-ttu-id="8dba2-324">Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut- `continue` être à partir de l’exécution d’une instruction), le *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évalué.</span><span class="sxs-lookup"><span data-stu-id="8dba2-324">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span> <span data-ttu-id="8dba2-325">Si l’expression booléenne génère `true`, le contrôle est transféré au début de l' `do` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-325">If the boolean expression yields `true`, control is transferred to the beginning of the `do` statement.</span></span> <span data-ttu-id="8dba2-326">Sinon, le contrôle est transféré au point de terminaison de `do` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-326">Otherwise, control is transferred to the end point of the `do` statement.</span></span>

<span data-ttu-id="8dba2-327">Dans l’instruction incorporée d' `do` une instruction, `break` une instruction ([instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de `do` l’instruction (terminant ainsi l’itération de l’instruction incorporée) et un `continue` Statement ([instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-327">Within the embedded statement of a `do` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `do` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement.</span></span>

<span data-ttu-id="8dba2-328">L’instruction incorporée d' `do` une instruction est accessible si l' `do` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-328">The embedded statement of a `do` statement is reachable if the `do` statement is reachable.</span></span>

<span data-ttu-id="8dba2-329">Le point de terminaison d' `do` une instruction est accessible si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-329">The end point of a `do` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-330">L' `do` instruction contient une `break` instruction accessible qui quitte l' `do` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-330">The `do` statement contains a reachable `break` statement that exits the `do` statement.</span></span>
*  <span data-ttu-id="8dba2-331">Le point de terminaison de l’instruction incorporée est accessible et l’expression booléenne n’a pas la valeur `true`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-331">The end point of the embedded statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-for-statement"></a><span data-ttu-id="8dba2-332">Instruction for</span><span class="sxs-lookup"><span data-stu-id="8dba2-332">The for statement</span></span>

<span data-ttu-id="8dba2-333">L' `for` instruction évalue une séquence d’expressions d’initialisation, puis, alors qu’une condition a la valeur true, exécute à plusieurs reprises une instruction incorporée et évalue une séquence d’expressions d’itération.</span><span class="sxs-lookup"><span data-stu-id="8dba2-333">The `for` statement evaluates a sequence of initialization expressions and then, while a condition is true, repeatedly executes an embedded statement and evaluates a sequence of iteration expressions.</span></span>

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

<span data-ttu-id="8dba2-334">Le *for_initializer*, le cas échéant, se compose soit d’un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)), soit d’une liste d' *statement_expression*s ([instructions d’expression](statements.md#expression-statements)) séparées par des virgules.</span><span class="sxs-lookup"><span data-stu-id="8dba2-334">The *for_initializer*, if present, consists of either a *local_variable_declaration* ([Local variable declarations](statements.md#local-variable-declarations)) or a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span> <span data-ttu-id="8dba2-335">La portée d’une variable locale déclarée par un *for_initializer* commence au *local_variable_declarator* pour la variable et s’étend jusqu’à la fin de l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-335">The scope of a local variable declared by a *for_initializer* starts at the *local_variable_declarator* for the variable and extends to the end of the embedded statement.</span></span> <span data-ttu-id="8dba2-336">L’étendue comprend *for_condition* et *for_iterator*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-336">The scope includes the *for_condition* and the *for_iterator*.</span></span>

<span data-ttu-id="8dba2-337">Le *for_condition*, s’il est présent, doit être un *Boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-337">The *for_condition*, if present, must be a *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)).</span></span>

<span data-ttu-id="8dba2-338">Le *for_iterator*, le cas échéant, se compose d’une liste d' *statement_expression*s ([instructions d’expression](statements.md#expression-statements)) séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="8dba2-338">The *for_iterator*, if present, consists of a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span>

<span data-ttu-id="8dba2-339">Une instruction for est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-339">A for statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-340">Si un *for_initializer* est présent, les initialiseurs de variable ou les expressions d’instruction sont exécutés dans l’ordre dans lequel ils sont écrits.</span><span class="sxs-lookup"><span data-stu-id="8dba2-340">If a *for_initializer* is present, the variable initializers or statement expressions are executed in the order they are written.</span></span> <span data-ttu-id="8dba2-341">Cette étape n’est effectuée qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-341">This step is only performed once.</span></span>
*  <span data-ttu-id="8dba2-342">Si un *for_condition* est présent, il est évalué.</span><span class="sxs-lookup"><span data-stu-id="8dba2-342">If a *for_condition* is present, it is evaluated.</span></span>
*  <span data-ttu-id="8dba2-343">Si le *for_condition* n’est pas présent ou si l’évaluation génère `true`, le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-343">If the *for_condition* is not present or if the evaluation yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="8dba2-344">Quand et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut- `continue` être à partir de l’exécution d’une instruction), les expressions du *for_iterator*, le cas échéant, sont évaluées dans l’ordre, puis une autre itération est effectuée, en commençant par évaluation de *for_condition* dans l’étape ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="8dba2-344">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the expressions of the *for_iterator*, if any, are evaluated in sequence, and then another iteration is performed, starting with evaluation of the *for_condition* in the step above.</span></span>
*  <span data-ttu-id="8dba2-345">Si le *for_condition* est présent et que l’évaluation génère `false`, le contrôle est transféré au point de terminaison de `for` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-345">If the *for_condition* is present and the evaluation yields `false`, control is transferred to the end point of the `for` statement.</span></span>

<span data-ttu-id="8dba2-346">Dans l’instruction incorporée d' `for` une instruction, `break` une instruction ([instruction Break](statements.md#the-break-statement)) peut être utilisée pour transférer le contrôle au point de terminaison de `for` l’instruction (terminant ainsi l’itération de l’instruction incorporée) et un `continue` Statement ([instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (en exécutant donc le *for_iterator* et en effectuant une `for` autre itération de l’instruction, en commençant par *for_condition*).</span><span class="sxs-lookup"><span data-stu-id="8dba2-346">Within the embedded statement of a `for` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `for` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus executing the *for_iterator* and performing another iteration of the `for` statement, starting with the *for_condition*).</span></span>

<span data-ttu-id="8dba2-347">L’instruction incorporée d' `for` une instruction est accessible si l’une des conditions suivantes est remplie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-347">The embedded statement of a `for` statement is reachable if one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-348">L' `for` instruction est accessible et aucun *for_condition* n’est présent.</span><span class="sxs-lookup"><span data-stu-id="8dba2-348">The `for` statement is reachable and no *for_condition* is present.</span></span>
*  <span data-ttu-id="8dba2-349">L' `for` instruction est accessible et un *for_condition* est présent et n’a pas la valeur `false`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-349">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `false`.</span></span>

<span data-ttu-id="8dba2-350">Le point de terminaison d' `for` une instruction est accessible si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="8dba2-350">The end point of a `for` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="8dba2-351">L' `for` instruction contient une `break` instruction accessible qui quitte l' `for` instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-351">The `for` statement contains a reachable `break` statement that exits the `for` statement.</span></span>
*  <span data-ttu-id="8dba2-352">L' `for` instruction est accessible et un *for_condition* est présent et n’a pas la valeur `true`de constante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-352">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `true`.</span></span>

### <a name="the-foreach-statement"></a><span data-ttu-id="8dba2-353">Instruction foreach</span><span class="sxs-lookup"><span data-stu-id="8dba2-353">The foreach statement</span></span>

<span data-ttu-id="8dba2-354">L' `foreach` instruction énumère les éléments d’une collection, en exécutant une instruction incorporée pour chaque élément de la collection.</span><span class="sxs-lookup"><span data-stu-id="8dba2-354">The `foreach` statement enumerates the elements of a collection, executing an embedded statement for each element of the collection.</span></span>

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

<span data-ttu-id="8dba2-355">Le *type* et l' *identificateur* d’une `foreach` instruction déclarent la ***variable d’itération*** de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-355">The *type* and *identifier* of a `foreach` statement declare the ***iteration variable*** of the statement.</span></span> <span data-ttu-id="8dba2-356">Si l' `var` identificateur est donné en tant que *local_variable_type*et qu’aucun type nommé `var` n’est dans la portée, la variable d’itération est dite une ***variable d’itération implicitement typée***et son type est considéré comme étant le type d’élément de l’élément `foreach` comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8dba2-356">If the `var` identifier is given as the *local_variable_type*, and no type named `var` is in scope, the iteration variable is said to be an ***implicitly typed iteration variable***, and its type is taken to be the element type of the `foreach` statement, as specified below.</span></span> <span data-ttu-id="8dba2-357">La variable d’itération correspond à une variable locale en lecture seule avec une portée qui s’étend sur l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-357">The iteration variable corresponds to a read-only local variable with a scope that extends over the embedded statement.</span></span> <span data-ttu-id="8dba2-358">Pendant l’exécution d' `foreach` une instruction, la variable d’itération représente l’élément de collection pour lequel une itération est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="8dba2-358">During execution of a `foreach` statement, the iteration variable represents the collection element for which an iteration is currently being performed.</span></span> <span data-ttu-id="8dba2-359">Une erreur de compilation se produit si l’instruction incorporée tente de modifier la variable d’itération (par le `++` biais `--` de l’assignation ou des opérateurs et) ou `ref` de `out` passer la variable d’itération en tant que paramètre ou.</span><span class="sxs-lookup"><span data-stu-id="8dba2-359">A compile-time error occurs if the embedded statement attempts to modify the iteration variable (via assignment or the `++` and `--` operators) or pass the iteration variable as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="8dba2-360">Dans ce qui suit, par souci de `IEnumerable`concision `IEnumerable<T>` , `IEnumerator<T>` `IEnumerator`, et font référence aux types correspondants dans les `System.Collections` espaces `System.Collections.Generic`de noms et.</span><span class="sxs-lookup"><span data-stu-id="8dba2-360">In the following, for brevity, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` and `IEnumerator<T>` refer to the corresponding types in the namespaces `System.Collections` and `System.Collections.Generic`.</span></span>

<span data-ttu-id="8dba2-361">Le traitement au moment de la compilation d’une instruction foreach détermine d’abord le type de ***collection***, le type d' ***énumérateur*** et le ***type d’élément*** de l’expression.</span><span class="sxs-lookup"><span data-stu-id="8dba2-361">The compile-time processing of a foreach statement first determines the ***collection type***, ***enumerator type*** and ***element type*** of the expression.</span></span> <span data-ttu-id="8dba2-362">Cette détermination se déroule comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-362">This determination proceeds as follows:</span></span>

*  <span data-ttu-id="8dba2-363">Si le type `X` d' *expression* est un type tableau, il existe une `X` `IEnumerable` conversion de référence implicite de en interface ( `System.Array` puisque implémente cette interface).</span><span class="sxs-lookup"><span data-stu-id="8dba2-363">If the type `X` of *expression* is an array type then there is an implicit reference conversion from `X` to the `IEnumerable` interface (since `System.Array` implements this interface).</span></span> <span data-ttu-id="8dba2-364">Le ***type de collection*** est `IEnumerable` l’interface, le ***type*** d’énumérateur `IEnumerator` est l’interface et le ***type d’élément*** est le type d’élément `X`du type tableau.</span><span class="sxs-lookup"><span data-stu-id="8dba2-364">The ***collection type*** is the `IEnumerable` interface, the ***enumerator type*** is the `IEnumerator` interface and the ***element type*** is the element type of the array type `X`.</span></span>
*  <span data-ttu-id="8dba2-365">Si le type `X` d' *expression* est `dynamic` , il y a `IEnumerable` une conversion implicite d' *expression* en interface ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-365">If the type `X` of *expression* is `dynamic` then there is an implicit conversion from *expression* to the `IEnumerable` interface ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)).</span></span> <span data-ttu-id="8dba2-366">Le ***type de collection*** est `IEnumerable` l’interface et le ***type*** d’énumérateur `IEnumerator` est l’interface.</span><span class="sxs-lookup"><span data-stu-id="8dba2-366">The ***collection type*** is the `IEnumerable` interface and the ***enumerator type*** is the `IEnumerator` interface.</span></span> <span data-ttu-id="8dba2-367">Si l' `var` identificateur est donné en tant que *local_variable_type* , le ***type*** d’élément `dynamic`est, sinon il `object`est.</span><span class="sxs-lookup"><span data-stu-id="8dba2-367">If the `var` identifier is given as the *local_variable_type* then the ***element type*** is `dynamic`, otherwise it is `object`.</span></span>
*  <span data-ttu-id="8dba2-368">Sinon, déterminez si le `X` type a une `GetEnumerator` méthode appropriée :</span><span class="sxs-lookup"><span data-stu-id="8dba2-368">Otherwise, determine whether the type `X` has an appropriate `GetEnumerator` method:</span></span>
   * <span data-ttu-id="8dba2-369">Effectuez une recherche de membre sur `X` le type avec `GetEnumerator` l’identificateur et aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-369">Perform member lookup on the type `X` with identifier `GetEnumerator` and no type arguments.</span></span> <span data-ttu-id="8dba2-370">Si la recherche de membre ne produit pas de correspondance, si elle génère une ambiguïté ou si elle produit une correspondance qui n’est pas un groupe de méthodes, recherchez une interface énumérable comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8dba2-370">If the member lookup does not produce a match, or it produces an ambiguity, or produces a match that is not a method group, check for an enumerable interface as described below.</span></span> <span data-ttu-id="8dba2-371">Il est recommandé d’émettre un avertissement si la recherche de membres produit tout sauf un groupe de méthodes ou aucune correspondance.</span><span class="sxs-lookup"><span data-stu-id="8dba2-371">It is recommended that a warning be issued if member lookup produces anything except a method group or no match.</span></span>
   * <span data-ttu-id="8dba2-372">Exécutez la résolution de surcharge à l’aide du groupe de méthodes résultant et d’une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="8dba2-372">Perform overload resolution using the resulting method group and an empty argument list.</span></span> <span data-ttu-id="8dba2-373">Si la résolution de surcharge entraîne l’absence de méthodes applicables, provoque une ambiguïté ou produit une seule meilleure méthode, mais que cette méthode est statique ou non publique, recherchez une interface énumérable comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8dba2-373">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, check for an enumerable interface as described below.</span></span> <span data-ttu-id="8dba2-374">Il est recommandé d’émettre un avertissement si la résolution de surcharge produit tout sauf une méthode d’instance publique non ambiguë ou aucune méthode applicable.</span><span class="sxs-lookup"><span data-stu-id="8dba2-374">It is recommended that a warning be issued if overload resolution produces anything except an unambiguous public instance method or no applicable methods.</span></span>
   * <span data-ttu-id="8dba2-375">Si le type `E` de retour de `GetEnumerator` la méthode n’est pas un type de classe, de struct ou d’interface, une erreur est générée et aucune autre étape n’est prise.</span><span class="sxs-lookup"><span data-stu-id="8dba2-375">If the return type `E` of the `GetEnumerator` method is not a class, struct or interface type, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="8dba2-376">La recherche de membres est `E` effectuée sur avec l' `Current` identificateur et aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-376">Member lookup is performed on `E` with the identifier `Current` and no type arguments.</span></span> <span data-ttu-id="8dba2-377">Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf une propriété d’instance publique qui autorise la lecture, une erreur est générée et aucune autre étape n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-377">If the member lookup produces no match, the result is an error, or the result is anything except a public instance property that permits reading, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="8dba2-378">La recherche de membres est `E` effectuée sur avec l' `MoveNext` identificateur et aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-378">Member lookup is performed on `E` with the identifier `MoveNext` and no type arguments.</span></span> <span data-ttu-id="8dba2-379">Si la recherche de membre ne produit aucune correspondance, le résultat est une erreur, ou le résultat est tout sauf un groupe de méthodes, une erreur est générée et aucune autre étape n’est prise.</span><span class="sxs-lookup"><span data-stu-id="8dba2-379">If the member lookup produces no match, the result is an error, or the result is anything except a method group, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="8dba2-380">La résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="8dba2-380">Overload resolution is performed on the method group with an empty argument list.</span></span> <span data-ttu-id="8dba2-381">Si la résolution de surcharge entraîne l’absence de méthodes applicables, entraîne une ambiguïté ou produit une seule meilleure méthode, mais cette méthode est statique ou non publique, ou son type de retour n’est `bool`pas, une erreur est générée et aucune autre étape n’est prise.</span><span class="sxs-lookup"><span data-stu-id="8dba2-381">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, or its return type is not `bool`, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="8dba2-382">Le ***type*** de collection `X`est, le type d' `E` ***énumérateur*** est, et le type d' ***élément*** est le `Current` type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="8dba2-382">The ***collection type*** is `X`, the ***enumerator type*** is `E`, and the ***element type*** is the type of the `Current` property.</span></span>

*  <span data-ttu-id="8dba2-383">Sinon, recherchez une interface énumérable :</span><span class="sxs-lookup"><span data-stu-id="8dba2-383">Otherwise, check for an enumerable interface:</span></span>
   * <span data-ttu-id="8dba2-384">Si parmi tous les types `Ti` pour lesquels il existe une conversion implicite `X` de `IEnumerable<Ti>`en, il existe un type `T` unique, `T` qui n' `dynamic` est pas et pour l' `Ti` ensemble de l’autre, il existe un conversion implicite `IEnumerable<T>` de `IEnumerable<Ti>`en, le ***type de collection*** est l' `IEnumerable<T>`interface, ***le type d’énumérateur*** est `IEnumerator<T>`l’interface et le ***type d’élément*** est `T`.</span><span class="sxs-lookup"><span data-stu-id="8dba2-384">If among all the types `Ti` for which there is an implicit conversion from `X` to `IEnumerable<Ti>`, there is a unique type `T` such that `T` is not `dynamic` and for all the other `Ti` there is an implicit conversion from `IEnumerable<T>` to `IEnumerable<Ti>`, then the ***collection type*** is the interface `IEnumerable<T>`, the ***enumerator type*** is the interface `IEnumerator<T>`, and the ***element type*** is `T`.</span></span>
   * <span data-ttu-id="8dba2-385">Sinon, s’il existe plusieurs types de ce type `T`, une erreur est générée et aucune autre étape n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-385">Otherwise, if there is more than one such type `T`, then an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="8dba2-386">Sinon, s’il existe une conversion implicite `X` de `System.Collections.IEnumerable` en interface, le ***type de collection*** est cette interface, le ***type d’énumérateur*** est l' `System.Collections.IEnumerator`interface et le ***type d’élément*** est `object`.</span><span class="sxs-lookup"><span data-stu-id="8dba2-386">Otherwise, if there is an implicit conversion from `X` to the `System.Collections.IEnumerable` interface, then the ***collection type*** is this interface, the ***enumerator type*** is the interface `System.Collections.IEnumerator`, and the ***element type*** is `object`.</span></span>
   * <span data-ttu-id="8dba2-387">Dans le cas contraire, une erreur est générée et aucune autre étape n’est prise.</span><span class="sxs-lookup"><span data-stu-id="8dba2-387">Otherwise, an error is produced and no further steps are taken.</span></span>

<span data-ttu-id="8dba2-388">Les étapes ci-dessus, en cas de réussite, produisent sans ambiguïté `C`un type de `E` collection, un `T`type d’énumérateur et un type d’élément.</span><span class="sxs-lookup"><span data-stu-id="8dba2-388">The above steps, if successful, unambiguously produce a collection type `C`, enumerator type `E` and element type `T`.</span></span> <span data-ttu-id="8dba2-389">Une instruction foreach de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-389">A foreach statement of the form</span></span>
```csharp
foreach (V v in x) embedded_statement
```
<span data-ttu-id="8dba2-390">est ensuite développé pour :</span><span class="sxs-lookup"><span data-stu-id="8dba2-390">is then expanded to:</span></span>
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

<span data-ttu-id="8dba2-391">La variable `e` n’est pas visible ou accessible à l’expression `x` ou à l’instruction incorporée ou à tout autre code source du programme.</span><span class="sxs-lookup"><span data-stu-id="8dba2-391">The variable `e` is not visible to or accessible to the expression `x` or the embedded statement or any other source code of the program.</span></span> <span data-ttu-id="8dba2-392">La variable `v` est en lecture seule dans l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-392">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="8dba2-393">S’il n’existe pas de conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de `T` (le type d’élément) en `V` (le *local_variable_type* dans l’instruction foreach), une erreur est générée et aucune autre étape n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-393">If there is not an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) from `T` (the element type) to `V` (the *local_variable_type* in the foreach statement), an error is produced and no further steps are taken.</span></span> <span data-ttu-id="8dba2-394">Si `x` a la valeur `null`, une `System.NullReferenceException` est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="8dba2-394">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

<span data-ttu-id="8dba2-395">Une implémentation est autorisée à implémenter une instruction foreach donnée différemment, par exemple pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="8dba2-395">An implementation is permitted to implement a given foreach-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="8dba2-396">La position à `v` l’intérieur de la boucle while est importante pour la façon dont elle est capturée par toute fonction anonyme se produisant dans le *embedded_statement*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-396">The placement of `v` inside the while loop is important for how it is captured by any anonymous function occurring in the *embedded_statement*.</span></span>

<span data-ttu-id="8dba2-397">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8dba2-397">For example:</span></span>
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
<span data-ttu-id="8dba2-398">Si `v` a été déclaré en dehors de la boucle while, il est partagé entre toutes les itérations, et sa valeur après la boucle for est la valeur `13`finale,, qui correspond à l’impression `f` de l’appel.</span><span class="sxs-lookup"><span data-stu-id="8dba2-398">If `v` was declared outside of the while loop, it would be shared among all iterations, and its value after the for loop would be the final value, `13`, which is what the invocation of `f` would print.</span></span> <span data-ttu-id="8dba2-399">Au lieu de cela, étant donné que chaque `v`itération a sa propre variable `f` , celle capturée par dans la première itération continue `7`à contenir la valeur, qui est celle qui sera imprimée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-399">Instead, because each iteration has its own variable `v`, the one captured by `f` in the first iteration will continue to hold the value `7`, which is what will be printed.</span></span> <span data-ttu-id="8dba2-400">(Remarque : les versions antérieures C# de `v` ont été déclarées en dehors de la boucle while.)</span><span class="sxs-lookup"><span data-stu-id="8dba2-400">(Note: earlier versions of C# declared `v` outside of the while loop.)</span></span>

<span data-ttu-id="8dba2-401">Le corps du bloc finally est construit selon les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8dba2-401">The body of the finally block is constructed according to the following steps:</span></span>

*  <span data-ttu-id="8dba2-402">S’il existe une conversion implicite `E` de `System.IDisposable` en interface,</span><span class="sxs-lookup"><span data-stu-id="8dba2-402">If there is an implicit conversion from `E` to the `System.IDisposable` interface, then</span></span>
   *  <span data-ttu-id="8dba2-403">Si `E` est un type valeur qui n’autorise pas les valeurs NULL, la clause finally est développée avec l’équivalent sémantique de :</span><span class="sxs-lookup"><span data-stu-id="8dba2-403">If `E` is a non-nullable value type then the finally clause is expanded to the semantic equivalent  of:</span></span>

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  <span data-ttu-id="8dba2-404">Dans le cas contraire, la clause finally est étendue à l’équivalent sémantique de :</span><span class="sxs-lookup"><span data-stu-id="8dba2-404">Otherwise the finally clause is expanded to the semantic equivalent of:</span></span>

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   <span data-ttu-id="8dba2-405">Hormis le `E` fait que si est un type valeur ou un paramètre de type instancié à un type valeur, le Cast `e` de `System.IDisposable` en n’entraîne pas de conversion boxing.</span><span class="sxs-lookup"><span data-stu-id="8dba2-405">except that if `E` is a value type, or a type parameter instantiated to a value type, then the cast of `e` to `System.IDisposable` will not cause boxing to occur.</span></span>

*  <span data-ttu-id="8dba2-406">Sinon, si `E` est un type sealed, la clause finally est développée en bloc vide :</span><span class="sxs-lookup"><span data-stu-id="8dba2-406">Otherwise, if `E` is a sealed type, the finally clause is expanded to an empty block:</span></span>

   ```csharp
   finally {
   }
   ```

*  <span data-ttu-id="8dba2-407">Dans le cas contraire, la clause finally est développée en :</span><span class="sxs-lookup"><span data-stu-id="8dba2-407">Otherwise, the finally clause is expanded to:</span></span>

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   <span data-ttu-id="8dba2-408">La variable `d` locale n’est pas visible ou accessible à un code utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-408">The local variable `d` is not visible to or accessible to any user code.</span></span> <span data-ttu-id="8dba2-409">En particulier, il n’est pas en conflit avec une autre variable dont la portée comprend le bloc finally.</span><span class="sxs-lookup"><span data-stu-id="8dba2-409">In particular, it does not conflict with any other variable whose scope includes the finally block.</span></span>

<span data-ttu-id="8dba2-410">L’ordre dans lequel `foreach` traverse les éléments d’un tableau est le suivant : Pour les tableaux unidimensionnels, les éléments sont parcourus dans un ordre d’index `0` plus important, en `Length - 1`commençant par l’index et en terminant par l’index.</span><span class="sxs-lookup"><span data-stu-id="8dba2-410">The order in which `foreach` traverses the elements of an array, is as follows: For single-dimensional arrays elements are traversed in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="8dba2-411">Pour les tableaux multidimensionnels, les éléments sont parcourus de telle sorte que les index de la dimension la plus à droite soient augmentés en premier, puis la dimension gauche suivante, et ainsi de suite vers la gauche.</span><span class="sxs-lookup"><span data-stu-id="8dba2-411">For multi-dimensional arrays, elements are traversed such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span>

<span data-ttu-id="8dba2-412">L’exemple suivant imprime chaque valeur dans un tableau à deux dimensions, dans l’ordre des éléments :</span><span class="sxs-lookup"><span data-stu-id="8dba2-412">The following example prints out each value in a two-dimensional array, in element order:</span></span>
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
<span data-ttu-id="8dba2-413">La sortie produite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="8dba2-413">The output produced is as follows:</span></span>
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

<span data-ttu-id="8dba2-414">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-414">In the example</span></span>
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
<span data-ttu-id="8dba2-415">le type de `n` est déduit comme étant `int`, le type d’élément de `numbers`.</span><span class="sxs-lookup"><span data-stu-id="8dba2-415">the type of `n` is inferred to be `int`, the element type of `numbers`.</span></span>

## <a name="jump-statements"></a><span data-ttu-id="8dba2-416">Instructions de saut</span><span class="sxs-lookup"><span data-stu-id="8dba2-416">Jump statements</span></span>

<span data-ttu-id="8dba2-417">Les instructions de saut transfèrent le contrôle de manière inconditionnelle.</span><span class="sxs-lookup"><span data-stu-id="8dba2-417">Jump statements unconditionally transfer control.</span></span>

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

<span data-ttu-id="8dba2-418">L’emplacement vers lequel une instruction de saut transfère le contrôle est appelé la ***cible*** de l’instruction de saut.</span><span class="sxs-lookup"><span data-stu-id="8dba2-418">The location to which a jump statement transfers control is called the ***target*** of the jump statement.</span></span>

<span data-ttu-id="8dba2-419">Lorsqu’une instruction de saut se produit dans un bloc et que la cible de cette instruction de saut est en dehors de ce bloc, l’instruction de saut est dite pour ***quitter*** le bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-419">When a jump statement occurs within a block, and the target of that jump statement is outside that block, the jump statement is said to ***exit*** the block.</span></span> <span data-ttu-id="8dba2-420">Alors qu’une instruction de saut peut transférer le contrôle hors d’un bloc, elle ne peut jamais transférer le contrôle dans un bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-420">While a jump statement may transfer control out of a block, it can never transfer control into a block.</span></span>

<span data-ttu-id="8dba2-421">L’exécution d’instructions de saut est compliquée par la présence `try` d’instructions intermédiaires.</span><span class="sxs-lookup"><span data-stu-id="8dba2-421">Execution of jump statements is complicated by the presence of intervening `try` statements.</span></span> <span data-ttu-id="8dba2-422">En l’absence de ces `try` instructions, une instruction de saut transfère de manière inconditionnelle le contrôle de l’instruction de saut à sa cible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-422">In the absence of such `try` statements, a jump statement unconditionally transfers control from the jump statement to its target.</span></span> <span data-ttu-id="8dba2-423">En présence de ces `try` instructions intervenues, l’exécution est plus complexe.</span><span class="sxs-lookup"><span data-stu-id="8dba2-423">In the presence of such intervening `try` statements, execution is more complex.</span></span> <span data-ttu-id="8dba2-424">Si l’instruction de saut quitte un ou plusieurs `try` blocs avec des `finally` blocs associés, le contrôle `finally` est initialement transféré au bloc de l' `try` instruction la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-424">If the jump statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-425">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-425">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-426">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-426">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>

<span data-ttu-id="8dba2-427">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-427">In the example</span></span>
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
<span data-ttu-id="8dba2-428">les `finally` blocs associés à deux `try` instructions sont exécutés avant que le contrôle soit transféré à la cible de l’instruction de saut.</span><span class="sxs-lookup"><span data-stu-id="8dba2-428">the `finally` blocks associated with two `try` statements are executed before control is transferred to the target of the jump statement.</span></span>

<span data-ttu-id="8dba2-429">La sortie produite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="8dba2-429">The output produced is as follows:</span></span>
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a><span data-ttu-id="8dba2-430">Instruction Break</span><span class="sxs-lookup"><span data-stu-id="8dba2-430">The break statement</span></span>

<span data-ttu-id="8dba2-431">L' `break` instruction quitte l’instruction,,, ou `switch` `for` `while` `do`englobantela plusproche`foreach` .</span><span class="sxs-lookup"><span data-stu-id="8dba2-431">The `break` statement exits the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
break_statement
    : 'break' ';'
    ;
```

<span data-ttu-id="8dba2-432">La cible d’une `break` instruction est le point de terminaison de l’instruction, `while`, `switch` `do` `for`, ou `foreach` englobante la plus proche.</span><span class="sxs-lookup"><span data-stu-id="8dba2-432">The target of a `break` statement is the end point of the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="8dba2-433">Si une `break` instruction n’est pas entourée `while`d' `do`une `for` `switch`instruction, `foreach` ,, ou, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-433">If a `break` statement is not enclosed by a `switch`, `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-434">Lorsque plusieurs `switch`instructions `while`, `do`, `break` ,, ou`foreach` sont imbriquées les unes dans les autres, une instruction s’applique uniquement à l’instruction la plus profonde. `for`</span><span class="sxs-lookup"><span data-stu-id="8dba2-434">When multiple `switch`, `while`, `do`, `for`, or `foreach` statements are nested within each other, a `break` statement applies only to the innermost statement.</span></span> <span data-ttu-id="8dba2-435">Pour transférer le contrôle entre plusieurs niveaux d’imbrication, `goto` une instruction ([instruction goto](statements.md#the-goto-statement)) doit être utilisée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-435">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="8dba2-436">Une `break` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-436">A `break` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="8dba2-437">Lorsqu’une `break` instruction se produit dans `finally` un bloc, la cible de `break` l’instruction doit se trouver dans `finally` le même bloc ; sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-437">When a `break` statement occurs within a `finally` block, the target of the `break` statement must be within the same `finally` block; otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-438">Une `break` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-438">A `break` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-439">Si l' `break` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-439">If the `break` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-440">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-440">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-441">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-441">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="8dba2-442">Le contrôle est transféré à la cible de `break` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-442">Control is transferred to the target of the `break` statement.</span></span>

<span data-ttu-id="8dba2-443">Étant donné `break` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `break` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-443">Because a `break` statement unconditionally transfers control elsewhere, the end point of a `break` statement is never reachable.</span></span>

### <a name="the-continue-statement"></a><span data-ttu-id="8dba2-444">Instruction continue</span><span class="sxs-lookup"><span data-stu-id="8dba2-444">The continue statement</span></span>

<span data-ttu-id="8dba2-445">L' `continue` instruction démarre une nouvelle itération de l’instruction `do`,, `for`ou `while` `foreach` englobante la plus proche.</span><span class="sxs-lookup"><span data-stu-id="8dba2-445">The `continue` statement starts a new iteration of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
continue_statement
    : 'continue' ';'
    ;
```

<span data-ttu-id="8dba2-446">La cible d’une `continue` instruction est le point de terminaison de l’instruction incorporée de l’instruction englobante `do`, `for`, ou `foreach` englobante `while`la plus proche.</span><span class="sxs-lookup"><span data-stu-id="8dba2-446">The target of a `continue` statement is the end point of the embedded statement of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="8dba2-447">Si une `continue` instruction n’est pas entourée `do`d' `for`une `while`instruction `foreach` ,, ou, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-447">If a `continue` statement is not enclosed by a `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-448">Lorsque plusieurs `while`instructions `do`, `for`,, `foreach` ou sont imbriquées les unes dans les autres `continue` , une instruction s’applique uniquement à l’instruction la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-448">When multiple `while`, `do`, `for`, or `foreach` statements are nested within each other, a `continue` statement applies only to the innermost statement.</span></span> <span data-ttu-id="8dba2-449">Pour transférer le contrôle entre plusieurs niveaux d’imbrication, `goto` une instruction ([instruction goto](statements.md#the-goto-statement)) doit être utilisée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-449">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="8dba2-450">Une `continue` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-450">A `continue` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="8dba2-451">Lorsqu’une `continue` instruction se produit dans `finally` un bloc, la cible de `continue` l’instruction doit se trouver dans `finally` le même bloc ; sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-451">When a `continue` statement occurs within a `finally` block, the target of the `continue` statement must be within the same `finally` block; otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-452">Une `continue` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-452">A `continue` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-453">Si l' `continue` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-453">If the `continue` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-454">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-454">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-455">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-455">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="8dba2-456">Le contrôle est transféré à la cible de `continue` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-456">Control is transferred to the target of the `continue` statement.</span></span>

<span data-ttu-id="8dba2-457">Étant donné `continue` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `continue` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-457">Because a `continue` statement unconditionally transfers control elsewhere, the end point of a `continue` statement is never reachable.</span></span>

### <a name="the-goto-statement"></a><span data-ttu-id="8dba2-458">Instruction goto</span><span class="sxs-lookup"><span data-stu-id="8dba2-458">The goto statement</span></span>

<span data-ttu-id="8dba2-459">L' `goto` instruction transfère le contrôle à une instruction qui est marquée par une étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-459">The `goto` statement transfers control to a statement that is marked by a label.</span></span>

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

<span data-ttu-id="8dba2-460">La cible d’une `goto` instruction d' *identificateur* est l’instruction étiquetée avec l’étiquette donnée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-460">The target of a `goto` *identifier* statement is the labeled statement with the given label.</span></span> <span data-ttu-id="8dba2-461">Si une étiquette portant le nom spécifié n’existe pas dans le membre de fonction actuel, ou si `goto` l’instruction n’est pas dans la portée de l’étiquette, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-461">If a label with the given name does not exist in the current function member, or if the `goto` statement is not within the scope of the label, a compile-time error occurs.</span></span> <span data-ttu-id="8dba2-462">Cette règle autorise l’utilisation d’une `goto` instruction pour transférer le contrôle hors d’une portée imbriquée, mais pas dans une portée imbriquée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-462">This rule permits the use of a `goto` statement to transfer control out of a nested scope, but not into a nested scope.</span></span> <span data-ttu-id="8dba2-463">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-463">In the example</span></span>
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
<span data-ttu-id="8dba2-464">une `goto` instruction est utilisée pour transférer le contrôle hors d’une portée imbriquée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-464">a `goto` statement is used to transfer control out of a nested scope.</span></span>

<span data-ttu-id="8dba2-465">La cible d’une `goto case` instruction est la liste d’instructions dans l’instruction immédiatement `switch` englobante ([l’instruction switch](statements.md#the-switch-statement)), qui contient `case` une étiquette avec la valeur de constante donnée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-465">The target of a `goto case` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `case` label with the given constant value.</span></span> <span data-ttu-id="8dba2-466">Si l' `goto case` instruction n’est pas entourée d’une `switch` instruction, si *constant_expression* n’est pas implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type gouvernant de l’instruction `switch` englobante la plus proche, ou si l’instruction englobante `switch` la plus proche ne `case` contient pas d’étiquette avec la valeur de constante donnée, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-466">If the `goto case` statement is not enclosed by a `switch` statement, if the *constant_expression* is not implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the nearest enclosing `switch` statement, or if the nearest enclosing `switch` statement does not contain a `case` label with the given constant value, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-467">La cible d’une `goto default` instruction est la liste d’instructions dans l’instruction immédiatement `switch` englobante ([l’instruction switch](statements.md#the-switch-statement)), qui contient `default` une étiquette.</span><span class="sxs-lookup"><span data-stu-id="8dba2-467">The target of a `goto default` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `default` label.</span></span> <span data-ttu-id="8dba2-468">Si l' `goto default` instruction n’est pas entourée d’une `switch` instruction, ou si l’instruction `switch` englobante la plus proche `default` ne contient pas d’étiquette, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-468">If the `goto default` statement is not enclosed by a `switch` statement, or if the nearest enclosing `switch` statement does not contain a `default` label, a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-469">Une `goto` instruction ne peut pas `finally` quitter un bloc ([instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-469">A `goto` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="8dba2-470">Lorsqu’une `goto` instruction se produit dans `finally` un bloc, la cible de `goto` l’instruction doit se trouver dans `finally` le même bloc, sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-470">When a `goto` statement occurs within a `finally` block, the target of the `goto` statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-471">Une `goto` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-471">A `goto` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-472">Si l' `goto` instruction quitte un ou plusieurs `try` blocs avec des `finally` blocs `finally` associés, le contrôle est initialement transféré au bloc de l’instruction `try` la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-472">If the `goto` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-473">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-473">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-474">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions intermédiaires aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-474">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="8dba2-475">Le contrôle est transféré à la cible de `goto` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-475">Control is transferred to the target of the `goto` statement.</span></span>

<span data-ttu-id="8dba2-476">Étant donné `goto` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `goto` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-476">Because a `goto` statement unconditionally transfers control elsewhere, the end point of a `goto` statement is never reachable.</span></span>

### <a name="the-return-statement"></a><span data-ttu-id="8dba2-477">Instruction return</span><span class="sxs-lookup"><span data-stu-id="8dba2-477">The return statement</span></span>

<span data-ttu-id="8dba2-478">L' `return` instruction retourne le contrôle à l’appelant actuel de la fonction dans laquelle `return` l’instruction apparaît.</span><span class="sxs-lookup"><span data-stu-id="8dba2-478">The `return` statement returns control to the current caller of the function in which the `return` statement appears.</span></span>

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

<span data-ttu-id="8dba2-479">Une `return` instruction sans expression ne peut être utilisée que dans un membre de fonction qui ne calcule pas de valeur, autrement dit, une méthode avec le type de résultat (corps `void`de[méthode](classes.md#method-body)), l' `set` accesseur d’une propriété ou `add` d’un indexeur, le les `remove` accesseurs et d’un événement, un constructeur d’instance, un constructeur statique ou un destructeur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-479">A `return` statement with no expression can be used only in a function member that does not compute a value, that is, a method with the result type ([Method body](classes.md#method-body)) `void`, the `set` accessor of a property or indexer, the `add` and `remove` accessors of an event, an instance constructor, a static constructor, or a destructor.</span></span>

<span data-ttu-id="8dba2-480">Une `return` instruction avec une expression ne peut être utilisée que dans un membre de fonction qui calcule une valeur, autrement dit, une méthode avec un type de résultat non void `get` , l’accesseur d’une propriété ou un indexeur ou un opérateur défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-480">A `return` statement with an expression can only be used in a function member that computes a value, that is, a method with a non-void result type, the `get` accessor of a property or indexer, or a user-defined operator.</span></span> <span data-ttu-id="8dba2-481">Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister du type de l’expression vers le type de retour de la fonction membre conteneur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-481">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression to the return type of the containing function member.</span></span>

<span data-ttu-id="8dba2-482">Les instructions Return peuvent également être utilisées dans le corps d’expressions de fonction anonymes ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)) et participer à la détermination des conversions qui existent pour ces fonctions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-482">Return statements can also be used in the body of anonymous function expressions ([Anonymous function expressions](expressions.md#anonymous-function-expressions)), and participate in determining which conversions exist for those functions.</span></span>

<span data-ttu-id="8dba2-483">Il s’agit d’une erreur de compilation pour `return` qu’une instruction apparaisse `finally` dans un bloc ([instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-483">It is a compile-time error for a `return` statement to appear in a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="8dba2-484">Une `return` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-484">A `return` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-485">Si l' `return` instruction spécifie une expression, l’expression est évaluée et la valeur résultante est convertie dans le type de retour de la fonction conteneur par une conversion implicite.</span><span class="sxs-lookup"><span data-stu-id="8dba2-485">If the `return` statement specifies an expression, the expression is evaluated and the resulting value is converted to the return type of the containing function by an implicit conversion.</span></span> <span data-ttu-id="8dba2-486">Le résultat de la conversion devient la valeur de résultat produite par la fonction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-486">The result of the conversion becomes the result value produced by the function.</span></span>
*  <span data-ttu-id="8dba2-487">Si l' `return` instruction est entourée d’un `try` ou `catch` de plusieurs blocs `finally` ou avec des blocs associés, le contrôle `finally` est initialement transféré au `try` bloc de l’instruction la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-487">If the `return` statement is enclosed by one or more `try` or `catch` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-488">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-488">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-489">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions englobantes aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-489">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="8dba2-490">Si la fonction conteneur n’est pas une fonction Async, le contrôle est retourné à l’appelant de la fonction conteneur avec la valeur de résultat, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-490">If the containing function is not an async function, control is returned to the caller of the containing function along with the result value, if any.</span></span>
*  <span data-ttu-id="8dba2-491">Si la fonction conteneur est une fonction Async, le contrôle est retourné à l’appelant actuel et la valeur de résultat, le cas échéant, est enregistrée dans la tâche de retour, comme décrit dans ([interfaces d’énumération](classes.md#enumerator-interfaces)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-491">If the containing function is an async function, control is returned to the current caller, and the result value, if any, is recorded in the return task as described in ([Enumerator interfaces](classes.md#enumerator-interfaces)).</span></span>

<span data-ttu-id="8dba2-492">Étant donné `return` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `return` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-492">Because a `return` statement unconditionally transfers control elsewhere, the end point of a `return` statement is never reachable.</span></span>

### <a name="the-throw-statement"></a><span data-ttu-id="8dba2-493">Instruction throw</span><span class="sxs-lookup"><span data-stu-id="8dba2-493">The throw statement</span></span>

<span data-ttu-id="8dba2-494">L' `throw` instruction lève une exception.</span><span class="sxs-lookup"><span data-stu-id="8dba2-494">The `throw` statement throws an exception.</span></span>

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

<span data-ttu-id="8dba2-495">Une `throw` instruction avec une expression génère la valeur produite par l’évaluation de l’expression.</span><span class="sxs-lookup"><span data-stu-id="8dba2-495">A `throw` statement with an expression throws the value produced by evaluating the expression.</span></span> <span data-ttu-id="8dba2-496">L’expression doit indiquer une valeur du type `System.Exception`de classe, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de `System.Exception` type ayant (ou une sous-classe) comme classe de base effective.</span><span class="sxs-lookup"><span data-stu-id="8dba2-496">The expression must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="8dba2-497">Si l’évaluation de l’expression `null`produit, `System.NullReferenceException` une exception est levée à la place.</span><span class="sxs-lookup"><span data-stu-id="8dba2-497">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="8dba2-498">Une `throw` instruction sans expression ne peut être utilisée que dans un `catch` bloc, auquel cas cette instruction lève à nouveau l’exception qui est actuellement gérée par ce `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-498">A `throw` statement with no expression can be used only in a `catch` block, in which case that statement re-throws the exception that is currently being handled by that `catch` block.</span></span>

<span data-ttu-id="8dba2-499">Étant donné `throw` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `throw` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-499">Because a `throw` statement unconditionally transfers control elsewhere, the end point of a `throw` statement is never reachable.</span></span>

<span data-ttu-id="8dba2-500">Quand une exception est levée, le contrôle est transféré à la `catch` première clause dans une instruction `try` englobante qui peut gérer l’exception.</span><span class="sxs-lookup"><span data-stu-id="8dba2-500">When an exception is thrown, control is transferred to the first `catch` clause in an enclosing `try` statement that can handle the exception.</span></span> <span data-ttu-id="8dba2-501">Le processus qui a lieu à partir du point de l’exception levée jusqu’au point de transfert du contrôle à un gestionnaire d’exceptions approprié est appelé ***propagation des exceptions***.</span><span class="sxs-lookup"><span data-stu-id="8dba2-501">The process that takes place from the point of the exception being thrown to the point of transferring control to a suitable exception handler is known as ***exception propagation***.</span></span> <span data-ttu-id="8dba2-502">La propagation d’une exception consiste à évaluer à plusieurs reprises les étapes suivantes jusqu' `catch` à ce qu’une clause qui corresponde à l’exception soit trouvée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-502">Propagation of an exception consists of repeatedly evaluating the following steps until a `catch` clause that matches the exception is found.</span></span> <span data-ttu-id="8dba2-503">Dans cette description, le ***point de levée*** est initialement l’emplacement où l’exception est levée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-503">In this description, the ***throw point*** is initially the location at which the exception is thrown.</span></span>

*  <span data-ttu-id="8dba2-504">Dans le membre de fonction actuel, `try` chaque instruction qui encadre le point d’exception est examinée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-504">In the current function member, each `try` statement that encloses the throw point is examined.</span></span> <span data-ttu-id="8dba2-505">Pour chaque instruction `S`, en commençant par l' `try` instruction la plus profonde et en `try` terminant par l’instruction la plus extérieure, les étapes suivantes sont évaluées :</span><span class="sxs-lookup"><span data-stu-id="8dba2-505">For each statement `S`, starting with the innermost `try` statement and ending with the outermost `try` statement, the following steps are evaluated:</span></span>

   * <span data-ttu-id="8dba2-506">Si le `try` bloc de `S` encadre le point de levée et si S a une ou plusieurs `catch` clauses, les `catch` clauses sont examinées par ordre d’apparition pour trouver un gestionnaire approprié pour l’exception, conformément aux règles spécifiées dans Section [de l’instruction try](statements.md#the-try-statement).</span><span class="sxs-lookup"><span data-stu-id="8dba2-506">If the `try` block of `S` encloses the throw point and if S has one or more `catch` clauses, the `catch` clauses are examined in order of appearance to locate a suitable handler for the exception, according to the rules specified in Section [The try statement](statements.md#the-try-statement).</span></span> <span data-ttu-id="8dba2-507">Si une clause `catch` correspondante est trouvée, la propagation de l’exception est terminée en transférant le contrôle au bloc `catch` de cette clause.</span><span class="sxs-lookup"><span data-stu-id="8dba2-507">If a matching `catch` clause is located, the exception propagation is completed by transferring control to the block of that `catch` clause.</span></span>

   * <span data-ttu-id="8dba2-508">Sinon, si le `try` bloc ou un `catch` bloc de `S` englobe le point de levée et si `S` a un `finally` bloc, le contrôle est transféré au `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-508">Otherwise, if the `try` block or a `catch` block of `S` encloses the throw point and if `S` has a `finally` block, control is transferred to the `finally` block.</span></span> <span data-ttu-id="8dba2-509">Si le `finally` bloc lève une autre exception, le traitement de l’exception actuelle est terminé.</span><span class="sxs-lookup"><span data-stu-id="8dba2-509">If the `finally` block throws another exception, processing of the current exception is terminated.</span></span> <span data-ttu-id="8dba2-510">Dans le cas contraire, lorsque le contrôle atteint le `finally` point de terminaison du bloc, le traitement de l’exception actuelle est poursuivi.</span><span class="sxs-lookup"><span data-stu-id="8dba2-510">Otherwise, when control reaches the end point of the `finally` block, processing of the current exception is continued.</span></span>

*  <span data-ttu-id="8dba2-511">Si un gestionnaire d’exceptions n’a pas été trouvé dans l’appel de la fonction en cours, l’appel de la fonction se termine et l’un des éléments suivants se produit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-511">If an exception handler was not located in the current function invocation, the function invocation is terminated, and one of the following occurs:</span></span>

   * <span data-ttu-id="8dba2-512">Si la fonction active est non Async, les étapes ci-dessus sont répétées pour l’appelant de la fonction avec un point d’exception correspondant à l’instruction à partir de laquelle la fonction membre a été appelée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-512">If the current function is non-async, the steps above are repeated for the caller of the function with a throw point corresponding to the statement from which the function member was invoked.</span></span>

   * <span data-ttu-id="8dba2-513">Si la fonction active est Async et retourne une tâche, l’exception est enregistrée dans la tâche de retour, qui est placée dans un état Faulted ou Cancelled, comme décrit dans [interfaces d’énumérateur](classes.md#enumerator-interfaces).</span><span class="sxs-lookup"><span data-stu-id="8dba2-513">If the current function is async and task-returning, the exception is recorded in the return task, which is put into a faulted or cancelled state as described in [Enumerator interfaces](classes.md#enumerator-interfaces).</span></span>

   * <span data-ttu-id="8dba2-514">Si la fonction active est Async et void-retournant, le contexte de synchronisation du thread actuel est notifié comme décrit dans [interfaces Enumerable](classes.md#enumerable-interfaces).</span><span class="sxs-lookup"><span data-stu-id="8dba2-514">If the current function is async and void-returning, the synchronization context of the current thread is notified as described in [Enumerable interfaces](classes.md#enumerable-interfaces).</span></span>

*  <span data-ttu-id="8dba2-515">Si le traitement des exceptions met fin à tous les appels de membre de fonction dans le thread actuel, ce qui indique que le thread n’a aucun gestionnaire pour l’exception, le thread se termine lui-même.</span><span class="sxs-lookup"><span data-stu-id="8dba2-515">If the exception processing terminates all function member invocations in the current thread, indicating that the thread has no handler for the exception, then the thread is itself terminated.</span></span> <span data-ttu-id="8dba2-516">L’impact d’une telle terminaison est défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="8dba2-516">The impact of such termination is implementation-defined.</span></span>

## <a name="the-try-statement"></a><span data-ttu-id="8dba2-517">Instruction try</span><span class="sxs-lookup"><span data-stu-id="8dba2-517">The try statement</span></span>

<span data-ttu-id="8dba2-518">L' `try` instruction fournit un mécanisme permettant d’intercepter les exceptions qui se produisent pendant l’exécution d’un bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-518">The `try` statement provides a mechanism for catching exceptions that occur during execution of a block.</span></span> <span data-ttu-id="8dba2-519">En outre, `try` l’instruction offre la possibilité de spécifier un bloc de code qui est toujours exécuté lorsque le contrôle `try` quitte l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-519">Furthermore, the `try` statement provides the ability to specify a block of code that is always executed when control leaves the `try` statement.</span></span>

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

<span data-ttu-id="8dba2-520">Il existe trois formes d' `try` instructions possibles :</span><span class="sxs-lookup"><span data-stu-id="8dba2-520">There are three possible forms of `try` statements:</span></span>

*  <span data-ttu-id="8dba2-521">Bloc suivi d’un ou de plusieurs `catch` blocs. `try`</span><span class="sxs-lookup"><span data-stu-id="8dba2-521">A `try` block followed by one or more `catch` blocks.</span></span>
*  <span data-ttu-id="8dba2-522">Bloc suivi d’un `finally` bloc. `try`</span><span class="sxs-lookup"><span data-stu-id="8dba2-522">A `try` block followed by a `finally` block.</span></span>
*  <span data-ttu-id="8dba2-523">Bloc suivi d’un ou de plusieurs `catch` blocs suivis d' `finally` un bloc. `try`</span><span class="sxs-lookup"><span data-stu-id="8dba2-523">A `try` block followed by one or more `catch` blocks followed by a `finally` block.</span></span>

<span data-ttu-id="8dba2-524">Quand une `catch` clause spécifie un *exception_specifier*, le type doit `System.Exception`être, un type qui dérive `System.Exception` de ou un type de paramètre de `System.Exception` type ayant (ou une sous-classe) comme classe de base effective.</span><span class="sxs-lookup"><span data-stu-id="8dba2-524">When a `catch` clause specifies an *exception_specifier*, the type must be `System.Exception`, a type that derives from `System.Exception` or a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span>

<span data-ttu-id="8dba2-525">Lorsqu’une `catch` clause spécifie à la fois un *exception_specifier* avec un *identificateur*, une ***variable d’exception*** du nom et du type donnés est déclarée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-525">When a `catch` clause specifies both an *exception_specifier* with an *identifier*, an ***exception variable*** of the given name and type is declared.</span></span> <span data-ttu-id="8dba2-526">La variable d’exception correspond à une variable locale avec une étendue qui s’étend `catch` sur la clause.</span><span class="sxs-lookup"><span data-stu-id="8dba2-526">The exception variable corresponds to a local variable with a scope that extends over the `catch` clause.</span></span> <span data-ttu-id="8dba2-527">Pendant l’exécution du *exception_filter* et du *bloc*, la variable d’exception représente l’exception actuellement gérée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-527">During execution of the *exception_filter* and *block*, the exception variable represents the exception currently being handled.</span></span> <span data-ttu-id="8dba2-528">À des fins de vérification de l’attribution définie, la variable d’exception est considérée comme assignée de manière définitive dans toute son étendue.</span><span class="sxs-lookup"><span data-stu-id="8dba2-528">For purposes of definite assignment checking, the exception variable is considered definitely assigned in its entire scope.</span></span>

<span data-ttu-id="8dba2-529">À moins `catch` qu’une clause comprenne un nom de variable d’exception, il est impossible d’accéder à l’objet `catch` d’exception dans le filtre et le bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-529">Unless a `catch` clause includes an exception variable name, it is impossible to access the exception object in the filter and `catch` block.</span></span>

<span data-ttu-id="8dba2-530">Une `catch` clause qui ne spécifie pas de *exception_specifier* est appelée une `catch` clause générale.</span><span class="sxs-lookup"><span data-stu-id="8dba2-530">A `catch` clause that does not specify an *exception_specifier* is called a general `catch` clause.</span></span>

<span data-ttu-id="8dba2-531">Certains langages de programmation peuvent prendre en charge des exceptions qui ne peuvent pas être représentées en tant qu’objet dérivé de `System.Exception`, bien que ces exceptions ne puissent jamais être générées par C# le code.</span><span class="sxs-lookup"><span data-stu-id="8dba2-531">Some programming languages may support exceptions that are not representable as an object derived from `System.Exception`, although such exceptions could never be generated by C# code.</span></span> <span data-ttu-id="8dba2-532">Une clause `catch` générale peut être utilisée pour intercepter ces exceptions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-532">A general `catch` clause may be used to catch such exceptions.</span></span> <span data-ttu-id="8dba2-533">Par conséquent, une `catch` clause générale est sémantiquement différente d’une clause qui spécifie le type `System.Exception`, dans le cas où la première peut également intercepter des exceptions d’autres langages.</span><span class="sxs-lookup"><span data-stu-id="8dba2-533">Thus, a general `catch` clause is semantically different from one that specifies the type `System.Exception`, in that the former may also catch exceptions from other languages.</span></span>

<span data-ttu-id="8dba2-534">Afin de localiser un gestionnaire pour une exception, `catch` les clauses sont examinées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="8dba2-534">In order to locate a handler for an exception, `catch` clauses are examined in lexical order.</span></span> <span data-ttu-id="8dba2-535">Si une `catch` clause spécifie un type mais aucun filtre d’exception, il s’agit d’une erreur de compilation `catch` pour une clause ultérieure `try` dans la même instruction pour spécifier un type qui est le même que, ou qui est dérivé de ce type.</span><span class="sxs-lookup"><span data-stu-id="8dba2-535">If a `catch` clause specifies a type but no exception filter, it is a compile-time error for a later `catch` clause in the same `try` statement to specify a type that is the same as, or is derived from, that type.</span></span> <span data-ttu-id="8dba2-536">Si une `catch` clause ne spécifie aucun type et aucun filtre, il doit s' `catch` agir de la `try` dernière clause pour cette instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-536">If a `catch` clause specifies no type and no filter, it must be the last `catch` clause for that `try` statement.</span></span>

<span data-ttu-id="8dba2-537">Dans un `catch` bloc, une `throw` instruction ([instruction throw](statements.md#the-throw-statement)) sans expression peut être utilisée pour lever à nouveau l’exception interceptée par le `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-537">Within a `catch` block, a `throw` statement ([The throw statement](statements.md#the-throw-statement)) with no expression can be used to re-throw the exception that was caught by the `catch` block.</span></span> <span data-ttu-id="8dba2-538">Les assignations à une variable d’exception ne modifient pas l’exception qui est levée une nouvelle fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-538">Assignments to an exception variable do not alter the exception that is re-thrown.</span></span>

<span data-ttu-id="8dba2-539">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="8dba2-539">In the example</span></span>
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
<span data-ttu-id="8dba2-540">la méthode `F` intercepte une exception, écrit des informations de diagnostic dans la console, modifie la variable d’exception et lève à nouveau l’exception.</span><span class="sxs-lookup"><span data-stu-id="8dba2-540">the method `F` catches an exception, writes some diagnostic information to the console, alters the exception variable, and re-throws the exception.</span></span> <span data-ttu-id="8dba2-541">L’exception qui est levée une nouvelle fois est l’exception d’origine, de sorte que la sortie produite est :</span><span class="sxs-lookup"><span data-stu-id="8dba2-541">The exception that is re-thrown is the original exception, so the output produced is:</span></span>
```
Exception in F: G
Exception in Main: G
```

<span data-ttu-id="8dba2-542">Si le premier bloc catch avait été `e` levé au lieu de lever à nouveau l’exception actuelle, la sortie produite serait la suivante :</span><span class="sxs-lookup"><span data-stu-id="8dba2-542">If the first catch block had thrown `e` instead of rethrowing the current exception, the output produced would be as follows:</span></span>
```
Exception in F: G
Exception in Main: F
```

<span data-ttu-id="8dba2-543">Il s’agit d’une erreur de compilation pour `break`une `continue`instruction, `goto` ou pour transférer le contrôle hors d' `finally` un bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-543">It is a compile-time error for a `break`, `continue`, or `goto` statement to transfer control out of a `finally` block.</span></span> <span data-ttu-id="8dba2-544">Quand une `break`instruction `continue`, ou `goto` se trouve dans un `finally` bloc, la cible de l’instruction doit se trouver dans le `finally` même bloc, sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="8dba2-544">When a `break`, `continue`, or `goto` statement occurs in a `finally` block, the target of the statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="8dba2-545">Il s’agit d’une erreur de compilation pour `return` qu’une instruction se produise dans un `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-545">It is a compile-time error for a `return` statement to occur in a `finally` block.</span></span>

<span data-ttu-id="8dba2-546">Une `try` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-546">A `try` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-547">Le `try` contrôle est transféré au bloc.</span><span class="sxs-lookup"><span data-stu-id="8dba2-547">Control is transferred to the `try` block.</span></span>
*  <span data-ttu-id="8dba2-548">Quand et si le contrôle atteint le point de terminaison `try` du bloc :</span><span class="sxs-lookup"><span data-stu-id="8dba2-548">When and if control reaches the end point of the `try` block:</span></span>
   *  <span data-ttu-id="8dba2-549">Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="8dba2-549">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
   *  <span data-ttu-id="8dba2-550">Le contrôle est transféré au point de terminaison de `try` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-550">Control is transferred to the end point of the `try` statement.</span></span>

*  <span data-ttu-id="8dba2-551">Si une exception est propagée à l' `try` instruction pendant l’exécution `try` du bloc :</span><span class="sxs-lookup"><span data-stu-id="8dba2-551">If an exception is propagated to the `try` statement during execution of the `try` block:</span></span>
   *  <span data-ttu-id="8dba2-552">Les `catch` clauses, le cas échéant, sont examinées par ordre d’apparition pour trouver un gestionnaire approprié pour l’exception.</span><span class="sxs-lookup"><span data-stu-id="8dba2-552">The `catch` clauses, if any, are examined in order of appearance to locate a suitable handler for the exception.</span></span> <span data-ttu-id="8dba2-553">Si une `catch` clause ne spécifie pas de type, ou spécifie le type d’exception ou un type de base du type d’exception :</span><span class="sxs-lookup"><span data-stu-id="8dba2-553">If a `catch` clause does not specify a type, or specifies the exception type or a base type of the exception type:</span></span>
      *  <span data-ttu-id="8dba2-554">Si la `catch` clause déclare une variable d’exception, l’objet exception est assigné à la variable d’exception.</span><span class="sxs-lookup"><span data-stu-id="8dba2-554">If the `catch` clause declares an exception variable, the exception object is assigned to the exception variable.</span></span>
      *  <span data-ttu-id="8dba2-555">Si la `catch` clause déclare un filtre d’exception, le filtre est évalué.</span><span class="sxs-lookup"><span data-stu-id="8dba2-555">If the `catch` clause declares an exception filter, the filter is evaluated.</span></span> <span data-ttu-id="8dba2-556">Si la valeur `false`est, la clause catch n’est pas une correspondance, et la recherche se poursuit à travers `catch` toutes les clauses suivantes pour un gestionnaire approprié.</span><span class="sxs-lookup"><span data-stu-id="8dba2-556">If it evaluates to `false`, the catch clause is not a match, and the search continues through any subsequent `catch` clauses for a suitable handler.</span></span>
      *  <span data-ttu-id="8dba2-557">Dans le cas `catch` contraire, la clause est considérée comme une correspondance et le contrôle est `catch` transféré au bloc correspondant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-557">Otherwise, the `catch` clause is considered a match, and control is transferred to the matching `catch` block.</span></span>
      *  <span data-ttu-id="8dba2-558">Quand et si le contrôle atteint le point de terminaison `catch` du bloc :</span><span class="sxs-lookup"><span data-stu-id="8dba2-558">When and if control reaches the end point of the `catch` block:</span></span>
         * <span data-ttu-id="8dba2-559">Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="8dba2-559">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         * <span data-ttu-id="8dba2-560">Le contrôle est transféré au point de terminaison de `try` l’instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-560">Control is transferred to the end point of the `try` statement.</span></span>
      *  <span data-ttu-id="8dba2-561">Si une exception est propagée à l' `try` instruction pendant l’exécution `catch` du bloc :</span><span class="sxs-lookup"><span data-stu-id="8dba2-561">If an exception is propagated to the `try` statement during execution of the `catch` block:</span></span>
         *  <span data-ttu-id="8dba2-562">Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="8dba2-562">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         *  <span data-ttu-id="8dba2-563">L’exception est propagée à l’instruction englobante `try` suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-563">The exception is propagated to the next enclosing `try` statement.</span></span>
   *  <span data-ttu-id="8dba2-564">Si l' `try` instruction n’a `catch` pas de clauses ou `catch` si aucune clause ne correspond à l’exception :</span><span class="sxs-lookup"><span data-stu-id="8dba2-564">If the `try` statement has no `catch` clauses or if no `catch` clause matches the exception:</span></span>
      *  <span data-ttu-id="8dba2-565">Si l' `try` instruction contient un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="8dba2-565">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
      *  <span data-ttu-id="8dba2-566">L’exception est propagée à l’instruction englobante `try` suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-566">The exception is propagated to the next enclosing `try` statement.</span></span>

<span data-ttu-id="8dba2-567">Les instructions d’un `finally` bloc sont toujours exécutées lorsque le contrôle `try` quitte une instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-567">The statements of a `finally` block are always executed when control leaves a `try` statement.</span></span> <span data-ttu-id="8dba2-568">Cela est vrai que le transfert de contrôle se produit suite à une exécution normale, en raison de l’exécution d' `break`une `continue`instruction `goto`,, `return` ou, ou suite à la `try` propagation d’une exception à partir de gestion.</span><span class="sxs-lookup"><span data-stu-id="8dba2-568">This is true whether the control transfer occurs as a result of normal execution, as a result of executing a `break`, `continue`, `goto`, or `return` statement, or as a result of propagating an exception out of the `try` statement.</span></span>

<span data-ttu-id="8dba2-569">Si une exception est levée pendant l’exécution d' `finally` un bloc et n’est pas interceptée dans le même bloc finally, l’exception est propagée à l' `try` instruction englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-569">If an exception is thrown during execution of a `finally` block, and is not caught within the same finally block, the exception is propagated to the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-570">Si une autre exception était en cours de propagation, cette exception est perdue.</span><span class="sxs-lookup"><span data-stu-id="8dba2-570">If another exception was in the process of being propagated, that exception is lost.</span></span> <span data-ttu-id="8dba2-571">Le processus de propagation d’une exception est abordé plus en détail dans la description `throw` de l’instruction ([instruction throw](statements.md#the-throw-statement)).</span><span class="sxs-lookup"><span data-stu-id="8dba2-571">The process of propagating an exception is discussed further in the description of the `throw` statement ([The throw statement](statements.md#the-throw-statement)).</span></span>

<span data-ttu-id="8dba2-572">Le `try` bloc d’une `try` instruction est accessible si l' `try` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-572">The `try` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="8dba2-573">Un `catch` bloc `try` d’instruction est accessible si l’instruction est accessible. `try`</span><span class="sxs-lookup"><span data-stu-id="8dba2-573">A `catch` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="8dba2-574">Le `finally` bloc d’une `try` instruction est accessible si l' `try` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-574">The `finally` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="8dba2-575">Le point de terminaison d' `try` une instruction est accessible si les deux conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="8dba2-575">The end point of a `try` statement is reachable if both of the following are true:</span></span>

*  <span data-ttu-id="8dba2-576">Le point de terminaison du `try` bloc est accessible ou le point de terminaison d’au moins un `catch` bloc est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-576">The end point of the `try` block is reachable or the end point of at least one `catch` block is reachable.</span></span>
*  <span data-ttu-id="8dba2-577">Si un `finally` bloc est présent, le point `finally` de terminaison du bloc est accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-577">If a `finally` block is present, the end point of the `finally` block is reachable.</span></span>

## <a name="the-checked-and-unchecked-statements"></a><span data-ttu-id="8dba2-578">Instructions cochées et non vérifiées</span><span class="sxs-lookup"><span data-stu-id="8dba2-578">The checked and unchecked statements</span></span>

<span data-ttu-id="8dba2-579">Les `checked` instructions `unchecked` et sont utilisées pour contrôler le ***contexte*** de contrôle de dépassement de capacité pour les opérations arithmétiques de type intégral et les conversions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-579">The `checked` and `unchecked` statements are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

<span data-ttu-id="8dba2-580">L' `checked` instruction entraîne l’évaluation de toutes les expressions du *bloc* dans un contexte vérifié, et l' `unchecked` instruction entraîne l’évaluation de toutes les expressions du *bloc* dans un contexte non vérifié.</span><span class="sxs-lookup"><span data-stu-id="8dba2-580">The `checked` statement causes all expressions in the *block* to be evaluated in a checked context, and the `unchecked` statement causes all expressions in the *block* to be evaluated in an unchecked context.</span></span>

<span data-ttu-id="8dba2-581">Les `checked` instructions `unchecked` et`checked` sont équivalentes de manière précise `unchecked` aux opérateurs et ([opérateurs vérifiés et](expressions.md#the-checked-and-unchecked-operators)non vérifiés), sauf qu’ils opèrent sur des blocs au lieu d’expressions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-581">The `checked` and `unchecked` statements are precisely equivalent to the `checked` and `unchecked` operators ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)), except that they operate on blocks instead of expressions.</span></span>

## <a name="the-lock-statement"></a><span data-ttu-id="8dba2-582">Instruction lock</span><span class="sxs-lookup"><span data-stu-id="8dba2-582">The lock statement</span></span>

<span data-ttu-id="8dba2-583">L' `lock` instruction obtient le verrou d’exclusion mutuelle pour un objet donné, exécute une instruction, puis libère le verrou.</span><span class="sxs-lookup"><span data-stu-id="8dba2-583">The `lock` statement obtains the mutual-exclusion lock for a given object, executes a statement, and then releases the lock.</span></span>

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

<span data-ttu-id="8dba2-584">L’expression d’une `lock` instruction doit indiquer une valeur d’un type connu pour être un *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-584">The expression of a `lock` statement must denote a value of a type known to be a *reference_type*.</span></span> <span data-ttu-id="8dba2-585">Aucune conversion boxing implicite ([conversions boxing](conversions.md#boxing-conversions)) n’est jamais effectuée pour l’expression `lock` d’une instruction. par conséquent, il s’agit d’une erreur au moment de la compilation pour que l’expression désigne une valeur d’un *Value_type*.</span><span class="sxs-lookup"><span data-stu-id="8dba2-585">No implicit boxing conversion ([Boxing conversions](conversions.md#boxing-conversions)) is ever performed for the expression of a `lock` statement, and thus it is a compile-time error for the expression to denote a value of a *value_type*.</span></span>

<span data-ttu-id="8dba2-586">Une `lock` déclaration de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-586">A `lock` statement of the form</span></span>
```csharp
lock (x) ...
```
<span data-ttu-id="8dba2-587">où `x` est une expression d’un *reference_type*, équivaut précisément à</span><span class="sxs-lookup"><span data-stu-id="8dba2-587">where `x` is an expression of a *reference_type*, is precisely equivalent to</span></span>
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
<span data-ttu-id="8dba2-588">sauf que `x` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-588">except that `x` is only evaluated once.</span></span>

<span data-ttu-id="8dba2-589">Lorsqu’un verrou d’exclusion mutuelle est maintenu, le code qui s’exécute dans le même thread d’exécution peut également obtenir et libérer le verrou.</span><span class="sxs-lookup"><span data-stu-id="8dba2-589">While a mutual-exclusion lock is held, code executing in the same execution thread can also obtain and release the lock.</span></span> <span data-ttu-id="8dba2-590">Toutefois, le code qui s’exécute dans d’autres threads ne peut pas obtenir le verrou tant que le verrou n’est pas libéré.</span><span class="sxs-lookup"><span data-stu-id="8dba2-590">However, code executing in other threads is blocked from obtaining the lock until the lock is released.</span></span>

<span data-ttu-id="8dba2-591">Le `System.Type` verrouillage des objets pour synchroniser l’accès aux données statiques n’est pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="8dba2-591">Locking `System.Type` objects in order to synchronize access to static data is not recommended.</span></span> <span data-ttu-id="8dba2-592">Un autre code peut verrouiller le même type, ce qui peut entraîner un interblocage.</span><span class="sxs-lookup"><span data-stu-id="8dba2-592">Other code might lock on the same type, which can result in deadlock.</span></span> <span data-ttu-id="8dba2-593">Une meilleure approche consiste à synchroniser l’accès aux données statiques en verrouillant un objet statique privé.</span><span class="sxs-lookup"><span data-stu-id="8dba2-593">A better approach is to synchronize access to static data by locking a private static object.</span></span> <span data-ttu-id="8dba2-594">Exemple :</span><span class="sxs-lookup"><span data-stu-id="8dba2-594">For example:</span></span>
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

## <a name="the-using-statement"></a><span data-ttu-id="8dba2-595">Instruction using</span><span class="sxs-lookup"><span data-stu-id="8dba2-595">The using statement</span></span>

<span data-ttu-id="8dba2-596">L' `using` instruction obtient une ou plusieurs ressources, exécute une instruction, puis libère la ressource.</span><span class="sxs-lookup"><span data-stu-id="8dba2-596">The `using` statement obtains one or more resources, executes a statement, and then disposes of the resource.</span></span>

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

<span data-ttu-id="8dba2-597">Une ***ressource*** est une classe ou un struct qui implémente `System.IDisposable`, qui comprend une méthode unique sans paramètre nommée. `Dispose`</span><span class="sxs-lookup"><span data-stu-id="8dba2-597">A ***resource*** is a class or struct that implements `System.IDisposable`, which includes a single parameterless method named `Dispose`.</span></span> <span data-ttu-id="8dba2-598">Le code qui utilise une ressource peut appeler `Dispose` pour indiquer que la ressource n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="8dba2-598">Code that is using a resource can call `Dispose` to indicate that the resource is no longer needed.</span></span> <span data-ttu-id="8dba2-599">Si `Dispose` n’est pas appelé, la suppression automatique finit par se produire à la suite de garbage collection.</span><span class="sxs-lookup"><span data-stu-id="8dba2-599">If `Dispose` is not called, then automatic disposal eventually occurs as a consequence of garbage collection.</span></span>

<span data-ttu-id="8dba2-600">Si la forme de *resource_acquisition* est *local_variable_declaration* , le type de `dynamic` local_variable_declaration doit être ou un type qui peut être implicitement converti en. `System.IDisposable`</span><span class="sxs-lookup"><span data-stu-id="8dba2-600">If the form of *resource_acquisition* is *local_variable_declaration* then the type of the *local_variable_declaration* must be either `dynamic` or a type that can be implicitly converted to `System.IDisposable`.</span></span> <span data-ttu-id="8dba2-601">Si la forme de *resource_acquisition* est *expression* , cette expression doit être implicitement convertible en `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="8dba2-601">If the form of *resource_acquisition* is *expression* then this expression must be implicitly convertible to `System.IDisposable`.</span></span>

<span data-ttu-id="8dba2-602">Les variables locales déclarées dans un *resource_acquisition* sont en lecture seule et doivent inclure un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-602">Local variables declared in a *resource_acquisition* are read-only, and must include an initializer.</span></span> <span data-ttu-id="8dba2-603">Une erreur de compilation se produit si l’instruction incorporée tente de modifier ces variables locales (par le biais `++` de `--` l’assignation ou des opérateurs et), d’en prendre l’adresse `ref` ou `out` de les passer en tant que paramètres ou.</span><span class="sxs-lookup"><span data-stu-id="8dba2-603">A compile-time error occurs if the embedded statement attempts to modify these local variables (via assignment or the `++` and `--` operators) , take the address of them, or pass them as `ref` or `out` parameters.</span></span>

<span data-ttu-id="8dba2-604">Une `using` instruction est traduite en trois parties : l’acquisition, l’utilisation et la suppression.</span><span class="sxs-lookup"><span data-stu-id="8dba2-604">A `using` statement is translated into three parts: acquisition, usage, and disposal.</span></span> <span data-ttu-id="8dba2-605">L’utilisation de la ressource est implicitement incluse dans `try` une instruction qui comprend `finally` une clause.</span><span class="sxs-lookup"><span data-stu-id="8dba2-605">Usage of the resource is implicitly enclosed in a `try` statement that includes a `finally` clause.</span></span> <span data-ttu-id="8dba2-606">Cette `finally` clause supprime la ressource.</span><span class="sxs-lookup"><span data-stu-id="8dba2-606">This `finally` clause disposes of the resource.</span></span> <span data-ttu-id="8dba2-607">Si une `null` ressource est acquise, aucun appel à `Dispose` n’est effectué et aucune exception n’est levée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-607">If a `null` resource is acquired, then no call to `Dispose` is made, and no exception is thrown.</span></span> <span data-ttu-id="8dba2-608">Si la ressource est de type `dynamic` , elle est convertie dynamiquement via une conversion dynamique implicite ([conversions dynamiques implicites](conversions.md#implicit-dynamic-conversions)) `IDisposable` en lors de l’acquisition afin de garantir la réussite de la conversion avant l’utilisation et Evacuation.</span><span class="sxs-lookup"><span data-stu-id="8dba2-608">If the resource is of type `dynamic` it is dynamically converted through an implicit dynamic conversion ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)) to `IDisposable` during acquisition in order to ensure that the conversion is successful before the usage and disposal.</span></span>

<span data-ttu-id="8dba2-609">Une `using` déclaration de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-609">A `using` statement of the form</span></span>
```csharp
using (ResourceType resource = expression) statement
```
<span data-ttu-id="8dba2-610">correspond à l’une des trois expansions possibles.</span><span class="sxs-lookup"><span data-stu-id="8dba2-610">corresponds to one of three possible expansions.</span></span> <span data-ttu-id="8dba2-611">Lorsque `ResourceType` est un type valeur qui n’autorise pas les valeurs NULL, l’expansion est</span><span class="sxs-lookup"><span data-stu-id="8dba2-611">When `ResourceType` is a non-nullable value type, the expansion is</span></span>
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

<span data-ttu-id="8dba2-612">Dans le cas `ResourceType` contraire, quand est un type valeur Nullable ou un type `dynamic`référence autre que, le développement est</span><span class="sxs-lookup"><span data-stu-id="8dba2-612">Otherwise, when `ResourceType` is a nullable value type or a reference type other than `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="8dba2-613">Dans le cas `ResourceType` contraire `dynamic`, lorsque est, l’expansion est</span><span class="sxs-lookup"><span data-stu-id="8dba2-613">Otherwise, when `ResourceType` is `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="8dba2-614">Dans les deux expansions `resource` , la variable est en lecture seule dans l’instruction incorporée `d` , et la variable est inaccessible dans, et invisible à, l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-614">In either expansion, the `resource` variable is read-only in the embedded statement, and the `d` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="8dba2-615">Une implémentation de est autorisée à implémenter une instruction using donnée différemment, par exemple pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="8dba2-615">An implementation is permitted to implement a given using-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="8dba2-616">Une `using` déclaration de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-616">A `using` statement of the form</span></span>
```csharp
using (expression) statement
```
<span data-ttu-id="8dba2-617">a les mêmes trois expansions possibles.</span><span class="sxs-lookup"><span data-stu-id="8dba2-617">has the same three possible expansions.</span></span> <span data-ttu-id="8dba2-618">Dans ce cas `ResourceType` , est implicitement le type au moment de la `expression`compilation du, s’il en a un.</span><span class="sxs-lookup"><span data-stu-id="8dba2-618">In this case `ResourceType` is implicitly the compile-time type of the `expression`, if it has one.</span></span> <span data-ttu-id="8dba2-619">Dans le cas `IDisposable` contraire, l’interface elle-même est utilisée comme. `ResourceType`</span><span class="sxs-lookup"><span data-stu-id="8dba2-619">Otherwise the interface `IDisposable` itself is used as the `ResourceType`.</span></span> <span data-ttu-id="8dba2-620">La `resource` variable est inaccessible dans, et invisible à, l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="8dba2-620">The `resource` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="8dba2-621">Quand un *resource_acquisition* prend la forme d’un *local_variable_declaration*, il est possible d’acquérir plusieurs ressources d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="8dba2-621">When a *resource_acquisition* takes the form of a *local_variable_declaration*, it is possible to acquire multiple resources of a given type.</span></span> <span data-ttu-id="8dba2-622">Une `using` déclaration de la forme</span><span class="sxs-lookup"><span data-stu-id="8dba2-622">A `using` statement of the form</span></span>
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
<span data-ttu-id="8dba2-623">équivaut précisément à une séquence d' `using` instructions imbriquées :</span><span class="sxs-lookup"><span data-stu-id="8dba2-623">is precisely equivalent to a sequence of nested `using` statements:</span></span>
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

<span data-ttu-id="8dba2-624">L’exemple ci-dessous crée un `log.txt` fichier nommé et écrit deux lignes de texte dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="8dba2-624">The example below creates a file named `log.txt` and writes two lines of text to the file.</span></span> <span data-ttu-id="8dba2-625">L’exemple ouvre ensuite le même fichier pour la lecture et copie les lignes de texte contenues dans la console.</span><span class="sxs-lookup"><span data-stu-id="8dba2-625">The example then opens that same file for reading and copies the contained lines of text to the console.</span></span>
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

<span data-ttu-id="8dba2-626">Étant donné `TextWriter` que `TextReader` les classes et `IDisposable` implémentent l’interface, l' `using` exemple peut utiliser des instructions pour s’assurer que le fichier sous-jacent est correctement fermé après les opérations d’écriture ou de lecture.</span><span class="sxs-lookup"><span data-stu-id="8dba2-626">Since the `TextWriter` and `TextReader` classes implement the `IDisposable` interface, the example can use `using` statements to ensure that the underlying file is properly closed following the write or read operations.</span></span>

## <a name="the-yield-statement"></a><span data-ttu-id="8dba2-627">Instruction yield</span><span class="sxs-lookup"><span data-stu-id="8dba2-627">The yield statement</span></span>

<span data-ttu-id="8dba2-628">L' `yield` instruction est utilisée dans un bloc itérateur ([blocs](statements.md#blocks)) pour produire une valeur à l’objet énumérateur ([objets énumérateur](classes.md#enumerator-objects)) ou à un objet énumérable ([objets énumérables](classes.md#enumerable-objects)) d’un itérateur ou pour signaler la fin de l’itération.</span><span class="sxs-lookup"><span data-stu-id="8dba2-628">The `yield` statement is used in an iterator block ([Blocks](statements.md#blocks)) to yield a value to the enumerator object ([Enumerator objects](classes.md#enumerator-objects)) or enumerable object ([Enumerable objects](classes.md#enumerable-objects)) of an iterator or to signal the end of the iteration.</span></span>

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

<span data-ttu-id="8dba2-629">`yield`n’est pas un mot réservé ; Il a une signification spéciale uniquement lorsqu’il est utilisé `return` juste `break` avant un mot clé ou.</span><span class="sxs-lookup"><span data-stu-id="8dba2-629">`yield` is not a reserved word; it has special meaning only when used immediately before a `return` or `break` keyword.</span></span> <span data-ttu-id="8dba2-630">Dans d’autres contextes `yield` , peut être utilisé comme identificateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-630">In other contexts, `yield` can be used as an identifier.</span></span>

<span data-ttu-id="8dba2-631">Il existe plusieurs restrictions sur l’emplacement `yield` où une instruction peut apparaître, comme décrit dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-631">There are several restrictions on where a `yield` statement can appear, as described in the following.</span></span>

*  <span data-ttu-id="8dba2-632">Une erreur `yield` de compilation pour une instruction (de l’une des deux formes) s’affiche en dehors d’un *method_body*, *operator_body* ou *accessor_body*</span><span class="sxs-lookup"><span data-stu-id="8dba2-632">It is a compile-time error for a `yield` statement (of either form) to appear outside a *method_body*, *operator_body* or *accessor_body*</span></span>
*  <span data-ttu-id="8dba2-633">Il s’agit d’une erreur de compilation pour `yield` qu’une instruction (de l’une des deux formes) apparaisse dans une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="8dba2-633">It is a compile-time error for a `yield` statement (of either form) to appear inside an anonymous function.</span></span>
*  <span data-ttu-id="8dba2-634">Il s’agit d’une erreur de compilation pour `yield` qu’une instruction (de l’une des deux formes `finally` ) apparaisse `try` dans la clause d’une instruction.</span><span class="sxs-lookup"><span data-stu-id="8dba2-634">It is a compile-time error for a `yield` statement (of either form) to appear in the `finally` clause of a `try` statement.</span></span>
*  <span data-ttu-id="8dba2-635">Il s’agit d’une erreur de compilation pour `yield return` qu’une instruction apparaisse n' `try` importe où dans une `catch` instruction qui contient des clauses.</span><span class="sxs-lookup"><span data-stu-id="8dba2-635">It is a compile-time error for a `yield return` statement to appear anywhere in a `try` statement that contains any `catch` clauses.</span></span>

<span data-ttu-id="8dba2-636">L’exemple suivant montre des utilisations valides et non `yield` valides d’instructions.</span><span class="sxs-lookup"><span data-stu-id="8dba2-636">The following example shows some valid and invalid uses of `yield` statements.</span></span>

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

<span data-ttu-id="8dba2-637">Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l' `yield return` expression dans l’instruction vers le type Yield ([type yield](classes.md#yield-type)) de l’itérateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-637">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression in the `yield return` statement to the yield type ([Yield type](classes.md#yield-type)) of the iterator.</span></span>

<span data-ttu-id="8dba2-638">Une `yield return` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-638">A `yield return` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-639">L’expression donnée dans l’instruction est évaluée, implicitement convertie en type yield et assignée à la `Current` propriété de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-639">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
*  <span data-ttu-id="8dba2-640">L’exécution du bloc itérateur est suspendue.</span><span class="sxs-lookup"><span data-stu-id="8dba2-640">Execution of the iterator block is suspended.</span></span> <span data-ttu-id="8dba2-641">Si l' `yield return` instruction se trouve dans un ou `try` plusieurs blocs, les `finally` blocs associés ne sont pas exécutés pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-641">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
*  <span data-ttu-id="8dba2-642">La `MoveNext` méthode de l’objet énumérateur retourne `true` à son appelant, ce qui indique que l’objet énumérateur a réussi à avancer jusqu’à l’élément suivant.</span><span class="sxs-lookup"><span data-stu-id="8dba2-642">The `MoveNext` method of the enumerator object returns `true` to its caller, indicating that the enumerator object successfully advanced to the next item.</span></span>

<span data-ttu-id="8dba2-643">Le prochain appel à la méthode de `MoveNext` l’objet énumérateur reprend l’exécution du bloc itérateur à partir de l’emplacement où il a été suspendu pour la dernière fois.</span><span class="sxs-lookup"><span data-stu-id="8dba2-643">The next call to the enumerator object's `MoveNext` method resumes execution of the iterator block from where it was last suspended.</span></span>

<span data-ttu-id="8dba2-644">Une `yield break` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8dba2-644">A `yield break` statement is executed as follows:</span></span>

*  <span data-ttu-id="8dba2-645">Si l' `yield break` instruction est entourée d’un `try` ou de plusieurs `finally` blocs avec des `finally` blocs associés, le contrôle est initialement transféré au `try` bloc de l’instruction la plus profonde.</span><span class="sxs-lookup"><span data-stu-id="8dba2-645">If the `yield break` statement is enclosed by one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="8dba2-646">Quand et si le contrôle atteint le point de terminaison `finally` d’un bloc, le contrôle est `finally` transféré au bloc de l’instruction `try` englobante suivante.</span><span class="sxs-lookup"><span data-stu-id="8dba2-646">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="8dba2-647">Ce processus est répété jusqu’à `finally` ce que les blocs de `try` toutes les instructions englobantes aient été exécutés.</span><span class="sxs-lookup"><span data-stu-id="8dba2-647">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="8dba2-648">Le contrôle est retourné à l’appelant du bloc Iterator.</span><span class="sxs-lookup"><span data-stu-id="8dba2-648">Control is returned to the caller of the iterator block.</span></span> <span data-ttu-id="8dba2-649">Il s’agit de `MoveNext` la méthode `Dispose` ou de la méthode de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="8dba2-649">This is either the `MoveNext` method or `Dispose` method of the enumerator object.</span></span>

<span data-ttu-id="8dba2-650">Étant donné `yield break` qu’une instruction transfère sans condition le contrôle ailleurs, le point de `yield break` terminaison d’une instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="8dba2-650">Because a `yield break` statement unconditionally transfers control elsewhere, the end point of a `yield break` statement is never reachable.</span></span>
