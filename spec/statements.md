---
ms.openlocfilehash: 8f9551b9e7f70379836c23a60f0d37dc02f8e18e
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229700"
---
# <a name="statements"></a><span data-ttu-id="047cd-101">Instructions</span><span class="sxs-lookup"><span data-stu-id="047cd-101">Statements</span></span>

<span data-ttu-id="047cd-102">C# fournit une variété d’instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-102">C# provides a variety of statements.</span></span> <span data-ttu-id="047cd-103">La plupart de ces instructions seront familière aux développeurs qui ont déjà programmé en C et C++.</span><span class="sxs-lookup"><span data-stu-id="047cd-103">Most of these statements will be familiar to developers who have programmed in C and C++.</span></span>

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

<span data-ttu-id="047cd-104">Le *embedded_statement* non terminal est utilisé pour les instructions qui s’affichent dans les autres instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-104">The *embedded_statement* nonterminal is used for statements that appear within other statements.</span></span> <span data-ttu-id="047cd-105">L’utilisation de *embedded_statement* plutôt que *instruction* exclut l’utilisation des instructions de déclaration et d’instructions étiquetées dans ces contextes.</span><span class="sxs-lookup"><span data-stu-id="047cd-105">The use of *embedded_statement* rather than *statement* excludes the use of declaration statements and labeled statements in these contexts.</span></span> <span data-ttu-id="047cd-106">L’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-106">The example</span></span>
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
<span data-ttu-id="047cd-107">génère une erreur de compilation, car un `if` instruction nécessite un *embedded_statement* au lieu d’un *instruction* pour ses si branche.</span><span class="sxs-lookup"><span data-stu-id="047cd-107">results in a compile-time error because an `if` statement requires an *embedded_statement* rather than a *statement* for its if branch.</span></span> <span data-ttu-id="047cd-108">Si ce code était autorisé, puis la variable `i` doit être déclarée, mais elle ne peut jamais être utilisée.</span><span class="sxs-lookup"><span data-stu-id="047cd-108">If this code were permitted, then the variable `i` would be declared, but it could never be used.</span></span> <span data-ttu-id="047cd-109">Notez, cependant, qui en plaçant `i`de déclaration dans un bloc, l’exemple est valide.</span><span class="sxs-lookup"><span data-stu-id="047cd-109">Note, however, that by placing `i`'s declaration in a block, the example is valid.</span></span>

## <a name="end-points-and-reachability"></a><span data-ttu-id="047cd-110">Points de terminaison et accessibilité</span><span class="sxs-lookup"><span data-stu-id="047cd-110">End points and reachability</span></span>

<span data-ttu-id="047cd-111">Chaque instruction possède un ***point de terminaison***.</span><span class="sxs-lookup"><span data-stu-id="047cd-111">Every statement has an ***end point***.</span></span> <span data-ttu-id="047cd-112">En d’autres termes, le point de terminaison d’une instruction est l’emplacement qui suit immédiatement l’instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-112">In intuitive terms, the end point of a statement is the location that immediately follows the statement.</span></span> <span data-ttu-id="047cd-113">Les règles de l’exécution des instructions composées (les instructions qui contiennent des instructions incorporées) spécifient l’action à entreprendre lorsque le contrôle atteint le point de terminaison d’une instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-113">The execution rules for composite statements (statements that contain embedded statements) specify the action that is taken when control reaches the end point of an embedded statement.</span></span> <span data-ttu-id="047cd-114">Par exemple, lorsque le contrôle atteint le point de terminaison d’une instruction dans un bloc, le contrôle est transféré à l’instruction suivante dans le bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-114">For example, when control reaches the end point of a statement in a block, control is transferred to the next statement in the block.</span></span>

<span data-ttu-id="047cd-115">Si une instruction peut éventuellement être atteinte par l’exécution, l’instruction est dite ***accessible***.</span><span class="sxs-lookup"><span data-stu-id="047cd-115">If a statement can possibly be reached by execution, the statement is said to be ***reachable***.</span></span> <span data-ttu-id="047cd-116">À l’inverse, s’il n’existe aucun risque qu’une instruction doit être exécutée, l’instruction est dite ***inaccessible***.</span><span class="sxs-lookup"><span data-stu-id="047cd-116">Conversely, if there is no possibility that a statement will be executed, the statement is said to be ***unreachable***.</span></span>

<span data-ttu-id="047cd-117">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-117">In the example</span></span>
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
<span data-ttu-id="047cd-118">le deuxième appel de `Console.WriteLine` est inaccessible, car il n’est pas possible que l’instruction doit être exécutée.</span><span class="sxs-lookup"><span data-stu-id="047cd-118">the second invocation of `Console.WriteLine` is unreachable because there is no possibility that the statement will be executed.</span></span>

<span data-ttu-id="047cd-119">Un avertissement est émis si le compilateur détermine qu’une instruction est inaccessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-119">A warning is reported if the compiler determines that a statement is unreachable.</span></span> <span data-ttu-id="047cd-120">Il est spécifiquement pas une erreur pour une instruction comme inaccessibles.</span><span class="sxs-lookup"><span data-stu-id="047cd-120">It is specifically not an error for a statement to be unreachable.</span></span>

<span data-ttu-id="047cd-121">Pour déterminer si une instruction particulière ou un point de terminaison est accessible, le compilateur effectue une analyse de flux selon les règles d’accessibilité définies pour chaque instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-121">To determine whether a particular statement or end point is reachable, the compiler performs flow analysis according to the reachability rules defined for each statement.</span></span> <span data-ttu-id="047cd-122">L’analyse de flux prend en compte les valeurs des expressions constantes ([expressions constantes](expressions.md#constant-expressions)) qui contrôlent le comportement des instructions, mais les valeurs possibles des expressions non constantes ne sont pas considérés comme.</span><span class="sxs-lookup"><span data-stu-id="047cd-122">The flow analysis takes into account the values of constant expressions ([Constant expressions](expressions.md#constant-expressions)) that control the behavior of statements, but the possible values of non-constant expressions are not considered.</span></span> <span data-ttu-id="047cd-123">En d’autres termes, à des fins d’analyse de flux de contrôle, une expression non constante d’un type donné est considérée comme ayant toutes les valeurs possibles de ce type.</span><span class="sxs-lookup"><span data-stu-id="047cd-123">In other words, for purposes of control flow analysis, a non-constant expression of a given type is considered to have any possible value of that type.</span></span>

<span data-ttu-id="047cd-124">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-124">In the example</span></span>
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
<span data-ttu-id="047cd-125">l’expression booléenne de le `if` instruction est une expression constante, car les deux opérandes de le `==` opérateur sont des constantes.</span><span class="sxs-lookup"><span data-stu-id="047cd-125">the boolean expression of the `if` statement is a constant expression because both operands of the `==` operator are constants.</span></span> <span data-ttu-id="047cd-126">Comme l’expression constante est évaluée au moment de la compilation, qui produit la valeur `false`, le `Console.WriteLine` appel est considéré comme étant inaccessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-126">As the constant expression is evaluated at compile-time, producing the value `false`, the `Console.WriteLine` invocation is considered unreachable.</span></span> <span data-ttu-id="047cd-127">Toutefois, si `i` est modifié pour être une variable locale</span><span class="sxs-lookup"><span data-stu-id="047cd-127">However, if `i` is changed to be a local variable</span></span>
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
<span data-ttu-id="047cd-128">le `Console.WriteLine` appel est considéré comme accessible, même si en réalité, il sera jamais exécuté.</span><span class="sxs-lookup"><span data-stu-id="047cd-128">the `Console.WriteLine` invocation is considered reachable, even though, in reality, it will never be executed.</span></span>

<span data-ttu-id="047cd-129">Le *bloc* d’une fonction membre est toujours considéré comme accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-129">The *block* of a function member is always considered reachable.</span></span> <span data-ttu-id="047cd-130">En évaluant successivement les règles d’accessibilité de chaque instruction dans un bloc, l’accessibilité d’une instruction peut être déterminée.</span><span class="sxs-lookup"><span data-stu-id="047cd-130">By successively evaluating the reachability rules of each statement in a block, the reachability of any given statement can be determined.</span></span>

<span data-ttu-id="047cd-131">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-131">In the example</span></span>
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
<span data-ttu-id="047cd-132">l’accessibilité de la seconde `Console.WriteLine` est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-132">the reachability of the second `Console.WriteLine` is determined as follows:</span></span>

*  <span data-ttu-id="047cd-133">La première `Console.WriteLine` instruction d’expression est accessible, car le bloc de la `F` méthode est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-133">The first `Console.WriteLine` expression statement is reachable because the block of the `F` method is reachable.</span></span>
*  <span data-ttu-id="047cd-134">Le point de terminaison de la première `Console.WriteLine` instruction d’expression est accessible, car cette instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-134">The end point of the first `Console.WriteLine` expression statement is reachable because that statement is reachable.</span></span>
*  <span data-ttu-id="047cd-135">Le `if` instruction est accessible, car le point de la fin de la première `Console.WriteLine` instruction d’expression est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-135">The `if` statement is reachable because the end point of the first `Console.WriteLine` expression statement is reachable.</span></span>
*  <span data-ttu-id="047cd-136">La seconde `Console.WriteLine` instruction d’expression est accessible, car l’expression booléenne de le `if` instruction n’a pas la valeur de constante `false`.</span><span class="sxs-lookup"><span data-stu-id="047cd-136">The second `Console.WriteLine` expression statement is reachable because the boolean expression of the `if` statement does not have the constant value `false`.</span></span>

<span data-ttu-id="047cd-137">Il existe deux situations dans lesquelles il est une erreur de compilation pour le point de terminaison d’une instruction pour être accessible :</span><span class="sxs-lookup"><span data-stu-id="047cd-137">There are two situations in which it is a compile-time error for the end point of a statement to be reachable:</span></span>

*  <span data-ttu-id="047cd-138">Étant donné que la `switch` instruction ne permet pas d’une section de commutateur « fourre-tout » à la section switch suivante, c’est une erreur de compilation pour le point de terminaison de la liste d’instructions d’une section de commutateur pour être accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-138">Because the `switch` statement does not permit a switch section to "fall through" to the next switch section, it is a compile-time error for the end point of the statement list of a switch section to be reachable.</span></span> <span data-ttu-id="047cd-139">Si cette erreur se produit, il est généralement une indication qui un `break` instruction est manquante.</span><span class="sxs-lookup"><span data-stu-id="047cd-139">If this error occurs, it is typically an indication that a `break` statement is missing.</span></span>
*  <span data-ttu-id="047cd-140">Il est une erreur de compilation pour le point de terminaison du bloc d’une fonction membre qui calcule une valeur pour être accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-140">It is a compile-time error for the end point of the block of a function member that computes a value to be reachable.</span></span> <span data-ttu-id="047cd-141">Si cette erreur se produit, il est généralement une indication qui un `return` instruction est manquante.</span><span class="sxs-lookup"><span data-stu-id="047cd-141">If this error occurs, it typically is an indication that a `return` statement is missing.</span></span>

## <a name="blocks"></a><span data-ttu-id="047cd-142">Blocs</span><span class="sxs-lookup"><span data-stu-id="047cd-142">Blocks</span></span>

<span data-ttu-id="047cd-143">Un *bloc* autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée.</span><span class="sxs-lookup"><span data-stu-id="047cd-143">A *block* permits multiple statements to be written in contexts where a single statement is allowed.</span></span>

```antlr
block
    : '{' statement_list? '}'
    ;
```

<span data-ttu-id="047cd-144">Un *bloc* se compose d’un texte facultatif *statement_list* ([instruction répertorie](statements.md#statement-lists)), placé entre accolades.</span><span class="sxs-lookup"><span data-stu-id="047cd-144">A *block* consists of an optional *statement_list* ([Statement lists](statements.md#statement-lists)), enclosed in braces.</span></span> <span data-ttu-id="047cd-145">Si la liste d’instructions est omise, le bloc est dit être vide.</span><span class="sxs-lookup"><span data-stu-id="047cd-145">If the statement list is omitted, the block is said to be empty.</span></span>

<span data-ttu-id="047cd-146">Un bloc peut contenir des instructions de déclaration ([les instructions de déclaration](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="047cd-146">A block may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="047cd-147">La portée d’une variable locale ou constante déclarée dans un bloc est le bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-147">The scope of a local variable or constant declared in a block is the block.</span></span>

<span data-ttu-id="047cd-148">Un bloc est exécuté comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-148">A block is executed as follows:</span></span>

*  <span data-ttu-id="047cd-149">Si le bloc est vide, le contrôle est transféré au point de terminaison du bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-149">If the block is empty, control is transferred to the end point of the block.</span></span>
*  <span data-ttu-id="047cd-150">Si le bloc n’est pas vide, le contrôle est transféré à la liste d’instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-150">If the block is not empty, control is transferred to the statement list.</span></span> <span data-ttu-id="047cd-151">Lorsqu’et si le contrôle atteint le point de terminaison de la liste d’instructions, le contrôle est transféré au point de terminaison du bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-151">When and if control reaches the end point of the statement list, control is transferred to the end point of the block.</span></span>

<span data-ttu-id="047cd-152">La liste d’instructions d’un bloc est accessible si le bloc lui-même est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-152">The statement list of a block is reachable if the block itself is reachable.</span></span>

<span data-ttu-id="047cd-153">Le point de terminaison d’un bloc est accessible si le bloc est vide ou si le point de terminaison de la liste d’instructions est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-153">The end point of a block is reachable if the block is empty or if the end point of the statement list is reachable.</span></span>

<span data-ttu-id="047cd-154">Un *bloc* qui contient un ou plusieurs `yield` instructions ([l’instruction yield](statements.md#the-yield-statement)) est appelé un bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-154">A *block* that contains one or more `yield` statements ([The yield statement](statements.md#the-yield-statement)) is called an iterator block.</span></span> <span data-ttu-id="047cd-155">Blocs itérateurs sont utilisés pour implémenter des fonctions membres en tant qu’itérateurs ([itérateurs](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="047cd-155">Iterator blocks are used to implement function members as iterators ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="047cd-156">Certaines restrictions s’appliquent aux blocs d’itérateur :</span><span class="sxs-lookup"><span data-stu-id="047cd-156">Some additional restrictions apply to iterator blocks:</span></span>

*  <span data-ttu-id="047cd-157">Il s’agit d’une erreur lors de la compilation pour un `return` instruction apparaissent dans un bloc itérateur (mais `yield return` instructions sont autorisées).</span><span class="sxs-lookup"><span data-stu-id="047cd-157">It is a compile-time error for a `return` statement to appear in an iterator block (but `yield return` statements are permitted).</span></span>
*  <span data-ttu-id="047cd-158">Il s’agit d’une erreur de compilation pour un bloc itérateur contenir un contexte unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="047cd-158">It is a compile-time error for an iterator block to contain an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="047cd-159">Un bloc itérateur définit toujours un contexte sécurisé, même lorsque sa déclaration est imbriquée dans un contexte unsafe.</span><span class="sxs-lookup"><span data-stu-id="047cd-159">An iterator block always defines a safe context, even when its declaration is nested in an unsafe context.</span></span>

### <a name="statement-lists"></a><span data-ttu-id="047cd-160">Listes d’instructions</span><span class="sxs-lookup"><span data-stu-id="047cd-160">Statement lists</span></span>

<span data-ttu-id="047cd-161">Un ***liste d’instructions*** se compose d’une ou plusieurs instructions écrites dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="047cd-161">A ***statement list*** consists of one or more statements written in sequence.</span></span> <span data-ttu-id="047cd-162">Listes d’instructions se produisent dans *bloc*s ([blocs](statements.md#blocks)) et dans *switch_block*s ([l’instruction switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-162">Statement lists occur in *block*s ([Blocks](statements.md#blocks)) and in *switch_block*s ([The switch statement](statements.md#the-switch-statement)).</span></span>

```antlr
statement_list
    : statement+
    ;
```

<span data-ttu-id="047cd-163">Une liste d’instructions est exécutée en transférant le contrôle à la première instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-163">A statement list is executed by transferring control to the first statement.</span></span> <span data-ttu-id="047cd-164">Lorsqu’et si le contrôle atteint le point de terminaison d’une instruction, le contrôle est transféré à l’instruction suivante.</span><span class="sxs-lookup"><span data-stu-id="047cd-164">When and if control reaches the end point of a statement, control is transferred to the next statement.</span></span> <span data-ttu-id="047cd-165">Lorsqu’et si le contrôle atteint le point de terminaison de la dernière instruction, le contrôle est transféré au point de terminaison de la liste d’instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-165">When and if control reaches the end point of the last statement, control is transferred to the end point of the statement list.</span></span>

<span data-ttu-id="047cd-166">Une instruction dans une liste d’instructions est accessible si au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-166">A statement in a statement list is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-167">L’instruction est la première instruction et la liste d’instructions est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-167">The statement is the first statement and the statement list itself is reachable.</span></span>
*  <span data-ttu-id="047cd-168">Le point de terminaison de l’instruction précédente est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-168">The end point of the preceding statement is reachable.</span></span>
*  <span data-ttu-id="047cd-169">L’instruction est une instruction étiquetée et l’étiquette est référencé par un accessible `goto` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-169">The statement is a labeled statement and the label is referenced by a reachable `goto` statement.</span></span>

<span data-ttu-id="047cd-170">Le point de terminaison d’une liste d’instructions est accessible si le point de terminaison de la dernière instruction dans la liste est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-170">The end point of a statement list is reachable if the end point of the last statement in the list is reachable.</span></span>

## <a name="the-empty-statement"></a><span data-ttu-id="047cd-171">Instruction vide</span><span class="sxs-lookup"><span data-stu-id="047cd-171">The empty statement</span></span>

<span data-ttu-id="047cd-172">Un *empty_statement* ne fait rien.</span><span class="sxs-lookup"><span data-stu-id="047cd-172">An *empty_statement* does nothing.</span></span>

```antlr
empty_statement
    : ';'
    ;
```

<span data-ttu-id="047cd-173">Une instruction vide est utilisée lorsqu’il n’y a aucune opération à effectuer dans un contexte où une instruction est requise.</span><span class="sxs-lookup"><span data-stu-id="047cd-173">An empty statement is used when there are no operations to perform in a context where a statement is required.</span></span>

<span data-ttu-id="047cd-174">Exécution d’une instruction vide transfère simplement le contrôle au point de terminaison de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-174">Execution of an empty statement simply transfers control to the end point of the statement.</span></span> <span data-ttu-id="047cd-175">Par conséquent, le point de terminaison d’une instruction vide est accessible si l’instruction vide est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-175">Thus, the end point of an empty statement is reachable if the empty statement is reachable.</span></span>

<span data-ttu-id="047cd-176">Une instruction vide peut être utilisée lorsque vous écrivez un `while` instruction avec un corps null :</span><span class="sxs-lookup"><span data-stu-id="047cd-176">An empty statement can be used when writing a `while` statement with a null body:</span></span>
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

<span data-ttu-id="047cd-177">En outre, une instruction vide peut être utilisée pour déclarer une étiquette juste avant la fermeture «`}`» d’un bloc :</span><span class="sxs-lookup"><span data-stu-id="047cd-177">Also, an empty statement can be used to declare a label just before the closing "`}`" of a block:</span></span>
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a><span data-ttu-id="047cd-178">Instructions étiquetées</span><span class="sxs-lookup"><span data-stu-id="047cd-178">Labeled statements</span></span>

<span data-ttu-id="047cd-179">Un *labeled_statement* autorise une instruction pour être préfixé par une étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-179">A *labeled_statement* permits a statement to be prefixed by a label.</span></span> <span data-ttu-id="047cd-180">Instructions étiquetées sont autorisées dans les blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.</span><span class="sxs-lookup"><span data-stu-id="047cd-180">Labeled statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

<span data-ttu-id="047cd-181">Une instruction étiquetée déclare une étiquette avec le nom attribué par le *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="047cd-181">A labeled statement declares a label with the name given by the *identifier*.</span></span> <span data-ttu-id="047cd-182">La portée d’une étiquette est l’ensemble du bloc dans lequel l’étiquette est déclarée, y compris les blocs imbriqués.</span><span class="sxs-lookup"><span data-stu-id="047cd-182">The scope of a label is the whole block in which the label is declared, including any nested blocks.</span></span> <span data-ttu-id="047cd-183">Il s’agit d’une erreur de compilation pour deux étiquettes portant le même nom pour bénéficier d’étendues qui se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="047cd-183">It is a compile-time error for two labels with the same name to have overlapping scopes.</span></span>

<span data-ttu-id="047cd-184">Une étiquette peut être référencée à partir de `goto` instructions ([l’instruction goto](statements.md#the-goto-statement)) dans l’étendue de l’étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-184">A label can be referenced from `goto` statements ([The goto statement](statements.md#the-goto-statement)) within the scope of the label.</span></span> <span data-ttu-id="047cd-185">Cela signifie que `goto` instructions peuvent transférer le contrôle au sein de blocs et en dehors de blocs, mais jamais en blocs.</span><span class="sxs-lookup"><span data-stu-id="047cd-185">This means that `goto` statements can transfer control within blocks and out of blocks, but never into blocks.</span></span>

<span data-ttu-id="047cd-186">Les étiquettes ont leur propre espace de déclaration et n’interfèrent pas avec d’autres identificateurs.</span><span class="sxs-lookup"><span data-stu-id="047cd-186">Labels have their own declaration space and do not interfere with other identifiers.</span></span> <span data-ttu-id="047cd-187">L’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-187">The example</span></span>
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
<span data-ttu-id="047cd-188">est valide et utilise le nom `x` comme paramètre et une étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-188">is valid and uses the name `x` as both a parameter and a label.</span></span>

<span data-ttu-id="047cd-189">L’exécution d’une instruction étiquetée correspond exactement à l’exécution de l’instruction qui suit l’étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-189">Execution of a labeled statement corresponds exactly to execution of the statement following the label.</span></span>

<span data-ttu-id="047cd-190">En plus de l’accessibilité fournie par le flux de contrôle normal, une instruction étiquetée est accessible si l’étiquette est référencée par un accessible `goto` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-190">In addition to the reachability provided by normal flow of control, a labeled statement is reachable if the label is referenced by a reachable `goto` statement.</span></span> <span data-ttu-id="047cd-191">(Exception : Si un `goto` instruction se trouve dans un `try` qui inclut un `finally` bloc et l’instruction étiquetée est en dehors de la `try`et le point de terminaison de la `finally` bloc n’est pas accessible, puis l’instruction étiquetée n’est pas accessible à partir de qui `goto` instruction.)</span><span class="sxs-lookup"><span data-stu-id="047cd-191">(Exception: If a `goto` statement is inside a `try` that includes a `finally` block, and the labeled statement is outside the `try`, and the end point of the `finally` block is unreachable, then the labeled statement is not reachable from that `goto` statement.)</span></span>

## <a name="declaration-statements"></a><span data-ttu-id="047cd-192">Instructions de déclaration</span><span class="sxs-lookup"><span data-stu-id="047cd-192">Declaration statements</span></span>

<span data-ttu-id="047cd-193">Un *declaration_statement* déclare une variable locale ou une constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-193">A *declaration_statement* declares a local variable or constant.</span></span> <span data-ttu-id="047cd-194">Les instructions de déclaration sont autorisées dans les blocs, mais ne sont pas autorisées en tant qu’instructions incorporées.</span><span class="sxs-lookup"><span data-stu-id="047cd-194">Declaration statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a><span data-ttu-id="047cd-195">Déclarations de variables locales</span><span class="sxs-lookup"><span data-stu-id="047cd-195">Local variable declarations</span></span>

<span data-ttu-id="047cd-196">Un *local_variable_declaration* déclare une ou plusieurs variables locales.</span><span class="sxs-lookup"><span data-stu-id="047cd-196">A *local_variable_declaration* declares one or more local variables.</span></span>

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

<span data-ttu-id="047cd-197">Le *local_variable_type* d’un *local_variable_declaration* directement Spécifie le type des variables introduites par la déclaration, ou indique avec l’identificateur `var` qui le type doit être déduit selon un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="047cd-197">The *local_variable_type* of a *local_variable_declaration* either directly specifies the type of the variables introduced by the declaration, or indicates with the identifier `var` that the type should be inferred based on an initializer.</span></span> <span data-ttu-id="047cd-198">Le type est suivi d’une liste de *local_variable_declarator*s, chacun présentant une nouvelle variable.</span><span class="sxs-lookup"><span data-stu-id="047cd-198">The type is followed by a list of *local_variable_declarator*s, each of which introduces a new variable.</span></span> <span data-ttu-id="047cd-199">Un *local_variable_declarator* se compose d’un *identificateur* qui nomme la variable, éventuellement suivie d’une «`=`« jeton et un *local_variable_initializer* qui fournit la valeur initiale de la variable.</span><span class="sxs-lookup"><span data-stu-id="047cd-199">A *local_variable_declarator* consists of an *identifier* that names the variable, optionally followed by an "`=`" token and a *local_variable_initializer* that gives the initial value of the variable.</span></span>

<span data-ttu-id="047cd-200">Dans le contexte d’une déclaration de variable locale, l’identificateur var agit comme un mot clé contextuel ([mots clés](lexical-structure.md#keywords)). Lorsque le *local_variable_type* est spécifié en tant que `var` et aucun type nommé `var` est dans la portée, la déclaration est une ***implicitement typées déclaration de variable locale***, dont le type est déduit le type de l’expression d’initialiseur associé.</span><span class="sxs-lookup"><span data-stu-id="047cd-200">In the context of a local variable declaration, the identifier var acts as a contextual keyword ([Keywords](lexical-structure.md#keywords)).When the *local_variable_type* is specified as `var` and no type named `var` is in scope, the declaration is an ***implicitly typed local variable declaration***, whose type is inferred from the type of the associated initializer expression.</span></span> <span data-ttu-id="047cd-201">Les déclarations de variables locales implicitement typées sont soumis aux restrictions suivantes :</span><span class="sxs-lookup"><span data-stu-id="047cd-201">Implicitly typed local variable declarations are subject to the following restrictions:</span></span>

*  <span data-ttu-id="047cd-202">Le *local_variable_declaration* ne peut pas inclure plusieurs *local_variable_declarator*s.</span><span class="sxs-lookup"><span data-stu-id="047cd-202">The *local_variable_declaration* cannot include multiple *local_variable_declarator*s.</span></span>
*  <span data-ttu-id="047cd-203">Le *local_variable_declarator* doit inclure un *local_variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="047cd-203">The *local_variable_declarator* must include a *local_variable_initializer*.</span></span>
*  <span data-ttu-id="047cd-204">Le *local_variable_initializer* doit être un *expression*.</span><span class="sxs-lookup"><span data-stu-id="047cd-204">The *local_variable_initializer* must be an *expression*.</span></span>
*  <span data-ttu-id="047cd-205">L’initialiseur *expression* doit avoir un type de compilation.</span><span class="sxs-lookup"><span data-stu-id="047cd-205">The initializer *expression* must have a compile-time type.</span></span>
*  <span data-ttu-id="047cd-206">L’initialiseur *expression* ne peut pas faire référence à la variable déclarée lui-même</span><span class="sxs-lookup"><span data-stu-id="047cd-206">The initializer *expression* cannot refer to the declared variable itself</span></span>

<span data-ttu-id="047cd-207">Voici quelques exemples de déclarations de variable locale implicitement typées incorrectes :</span><span class="sxs-lookup"><span data-stu-id="047cd-207">The following are examples of incorrect implicitly typed local variable declarations:</span></span>

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

<span data-ttu-id="047cd-208">La valeur d’une variable locale est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)), et la valeur d’une variable locale est modifiée à l’aide un *attribution* () [Opérateurs d’assignation](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="047cd-208">The value of a local variable is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)), and the value of a local variable is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="047cd-209">Une variable locale doit absolument être assignée ([assignation définie](variables.md#definite-assignment)) à chaque emplacement où sa valeur est obtenue.</span><span class="sxs-lookup"><span data-stu-id="047cd-209">A local variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at each location where its value is obtained.</span></span>

<span data-ttu-id="047cd-210">La portée d’une variable locale déclarée dans un *local_variable_declaration* est le bloc dans lequel la déclaration se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-210">The scope of a local variable declared in a *local_variable_declaration* is the block in which the declaration occurs.</span></span> <span data-ttu-id="047cd-211">C’est une erreur pour faire référence à une variable locale dans une position du texte qui précède le *local_variable_declarator* de la variable locale.</span><span class="sxs-lookup"><span data-stu-id="047cd-211">It is an error to refer to a local variable in a textual position that precedes the *local_variable_declarator* of the local variable.</span></span> <span data-ttu-id="047cd-212">Dans l’étendue d’une variable locale, il est une erreur de compilation pour déclarer une autre local variable ou constante portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="047cd-212">Within the scope of a local variable, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="047cd-213">Une déclaration de variable locale qui déclare plusieurs variables équivaut à plusieurs déclarations de variables uniques avec le même type.</span><span class="sxs-lookup"><span data-stu-id="047cd-213">A local variable declaration that declares multiple variables is equivalent to multiple declarations of single variables with the same type.</span></span> <span data-ttu-id="047cd-214">En outre, un initialiseur de variable dans une déclaration de variable locale correspond exactement à une instruction d’assignation qui est insérée immédiatement après la déclaration.</span><span class="sxs-lookup"><span data-stu-id="047cd-214">Furthermore, a variable initializer in a local variable declaration corresponds exactly to an assignment statement that is inserted immediately after the declaration.</span></span>

<span data-ttu-id="047cd-215">L’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-215">The example</span></span>
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
<span data-ttu-id="047cd-216">correspond exactement à</span><span class="sxs-lookup"><span data-stu-id="047cd-216">corresponds exactly to</span></span>
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

<span data-ttu-id="047cd-217">Dans une déclaration de variable locale implicitement typée, le type de la variable locale déclarée est pris pour être le même que le type de l’expression utilisée pour initialiser la variable.</span><span class="sxs-lookup"><span data-stu-id="047cd-217">In an implicitly typed local variable declaration, the type of the local variable being declared is taken to be the same as the type of the expression used to initialize the variable.</span></span> <span data-ttu-id="047cd-218">Exemple :</span><span class="sxs-lookup"><span data-stu-id="047cd-218">For example:</span></span>
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

<span data-ttu-id="047cd-219">Les déclarations variable locales implicitement typées ci-dessus sont précisément équivalentes aux déclarations explicitement typées suivantes :</span><span class="sxs-lookup"><span data-stu-id="047cd-219">The implicitly typed local variable declarations above are precisely equivalent to the following explicitly typed declarations:</span></span>
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a><span data-ttu-id="047cd-220">Déclarations de constante locale</span><span class="sxs-lookup"><span data-stu-id="047cd-220">Local constant declarations</span></span>

<span data-ttu-id="047cd-221">Un *local_constant_declaration* déclare une ou plusieurs constantes locales.</span><span class="sxs-lookup"><span data-stu-id="047cd-221">A *local_constant_declaration* declares one or more local constants.</span></span>

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

<span data-ttu-id="047cd-222">Le *type* d’un *local_constant_declaration* Spécifie le type de l’une des constantes introduites par la déclaration.</span><span class="sxs-lookup"><span data-stu-id="047cd-222">The *type* of a *local_constant_declaration* specifies the type of the constants introduced by the declaration.</span></span> <span data-ttu-id="047cd-223">Le type est suivi d’une liste de *constant_declarator*s, chacun d’eux présente une nouvelle constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-223">The type is followed by a list of *constant_declarator*s, each of which introduces a new constant.</span></span> <span data-ttu-id="047cd-224">A *constant_declarator* se compose d’un *identificateur* qui nomme la constante, suivie d’une «`=`» jeton, suivi par un *constant_expression* ([ Expressions constantes](expressions.md#constant-expressions)) qui fournit la valeur de la constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-224">A *constant_declarator* consists of an *identifier* that names the constant, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the constant.</span></span>

<span data-ttu-id="047cd-225">Le *type* et *constant_expression* d’une déclaration de constante locale doit suivre les mêmes règles que celles d’une déclaration de membre de constante ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="047cd-225">The *type* and *constant_expression* of a local constant declaration must follow the same rules as those of a constant member declaration ([Constants](classes.md#constants)).</span></span>

<span data-ttu-id="047cd-226">La valeur d’une constante locale est obtenue dans une expression qui utilise un *simple_name* ([noms simples](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="047cd-226">The value of a local constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="047cd-227">La portée d’une constante locale est le bloc dans lequel la déclaration se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-227">The scope of a local constant is the block in which the declaration occurs.</span></span> <span data-ttu-id="047cd-228">C’est une erreur pour faire référence à une constante locale dans une position du texte qui précède sa *constant_declarator*.</span><span class="sxs-lookup"><span data-stu-id="047cd-228">It is an error to refer to a local constant in a textual position that precedes its *constant_declarator*.</span></span> <span data-ttu-id="047cd-229">Dans la portée d’une constante locale, il est une erreur de compilation pour déclarer une autre local variable ou constante portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="047cd-229">Within the scope of a local constant, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="047cd-230">Une déclaration de constante locale qui déclare plusieurs constantes équivaut à plusieurs déclarations de constantes uniques avec le même type.</span><span class="sxs-lookup"><span data-stu-id="047cd-230">A local constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same type.</span></span>

## <a name="expression-statements"></a><span data-ttu-id="047cd-231">Instructions d’expression</span><span class="sxs-lookup"><span data-stu-id="047cd-231">Expression statements</span></span>

<span data-ttu-id="047cd-232">Un *expression_statement* évalue une expression donnée.</span><span class="sxs-lookup"><span data-stu-id="047cd-232">An *expression_statement* evaluates a given expression.</span></span> <span data-ttu-id="047cd-233">La valeur calculée par l’expression, le cas échéant, est ignoré.</span><span class="sxs-lookup"><span data-stu-id="047cd-233">The value computed by the expression, if any, is discarded.</span></span>

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

<span data-ttu-id="047cd-234">Pas toutes les expressions sont autorisées en tant qu’instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-234">Not all expressions are permitted as statements.</span></span> <span data-ttu-id="047cd-235">En particulier, des expressions telles que `x + y` et `x == 1` qui simplement de calculer une valeur (qui sera ignorée), ne sont pas autorisées en tant qu’instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-235">In particular, expressions such as `x + y` and `x == 1` that merely compute a value (which will be discarded), are not permitted as statements.</span></span>

<span data-ttu-id="047cd-236">L’exécution d’une *expression_statement* évalue l’expression de relation contenant-contenue et puis transfère le contrôle au point de terminaison de la *expression_statement*.</span><span class="sxs-lookup"><span data-stu-id="047cd-236">Execution of an *expression_statement* evaluates the contained expression and then transfers control to the end point of the *expression_statement*.</span></span> <span data-ttu-id="047cd-237">Le point de terminaison d’un *expression_statement* est accessible si le problème *expression_statement* est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-237">The end point of an *expression_statement* is reachable if that *expression_statement* is reachable.</span></span>

## <a name="selection-statements"></a><span data-ttu-id="047cd-238">Instructions de sélection</span><span class="sxs-lookup"><span data-stu-id="047cd-238">Selection statements</span></span>

<span data-ttu-id="047cd-239">Instructions de sélection Sélectionnez un d’un nombre d’instructions possibles pour l’exécution selon la valeur d’une expression.</span><span class="sxs-lookup"><span data-stu-id="047cd-239">Selection statements select one of a number of possible statements for execution based on the value of some expression.</span></span>

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a><span data-ttu-id="047cd-240">If instruction</span><span class="sxs-lookup"><span data-stu-id="047cd-240">The if statement</span></span>

<span data-ttu-id="047cd-241">La `if` instruction sélectionne une instruction à exécuter selon la valeur d’une expression booléenne.</span><span class="sxs-lookup"><span data-stu-id="047cd-241">The `if` statement selects a statement for execution based on the value of a boolean expression.</span></span>

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="047cd-242">Un `else` partie est associée à l’exemple précédent le plus proche lexicalement `if` qui n’est autorisé par la syntaxe.</span><span class="sxs-lookup"><span data-stu-id="047cd-242">An `else` part is associated with the lexically nearest preceding `if` that is allowed by the syntax.</span></span> <span data-ttu-id="047cd-243">Par conséquent, un `if` instruction du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-243">Thus, an `if` statement of the form</span></span>
```csharp
if (x) if (y) F(); else G();
```
<span data-ttu-id="047cd-244">est équivalent à</span><span class="sxs-lookup"><span data-stu-id="047cd-244">is equivalent to</span></span>
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

<span data-ttu-id="047cd-245">Un `if` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-245">An `if` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-246">Le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.</span><span class="sxs-lookup"><span data-stu-id="047cd-246">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="047cd-247">Si l’expression booléenne génère `true`, le contrôle est transféré à la première instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-247">If the boolean expression yields `true`, control is transferred to the first embedded statement.</span></span> <span data-ttu-id="047cd-248">Lorsqu’et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de terminaison de la `if` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-248">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="047cd-249">Si l’expression booléenne génère `false` et si un `else` partie est présente, le contrôle est transféré à la deuxième instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-249">If the boolean expression yields `false` and if an `else` part is present, control is transferred to the second embedded statement.</span></span> <span data-ttu-id="047cd-250">Lorsqu’et si le contrôle atteint le point de terminaison de cette instruction, le contrôle est transféré au point de terminaison de la `if` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-250">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="047cd-251">Si l’expression booléenne génère `false` et si un `else` partie n’est pas présente, le contrôle est transféré au point de terminaison de la `if` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-251">If the boolean expression yields `false` and if an `else` part is not present, control is transferred to the end point of the `if` statement.</span></span>

<span data-ttu-id="047cd-252">La première instruction d’incorporée une `if` instruction est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `false`.</span><span class="sxs-lookup"><span data-stu-id="047cd-252">The first embedded statement of an `if` statement is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="047cd-253">La deuxième instruction d’incorporée une `if` instruction, le cas échéant, est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.</span><span class="sxs-lookup"><span data-stu-id="047cd-253">The second embedded statement of an `if` statement, if present, is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

<span data-ttu-id="047cd-254">Le point de terminaison d’un `if` instruction est accessible si le point de terminaison d’au moins un de ses instructions incorporées est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-254">The end point of an `if` statement is reachable if the end point of at least one of its embedded statements is reachable.</span></span> <span data-ttu-id="047cd-255">En outre, de point de la fin d’une `if` instruction sans aucune `else` partie est accessible si le `if` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.</span><span class="sxs-lookup"><span data-stu-id="047cd-255">In addition, the end point of an `if` statement with no `else` part is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-switch-statement"></a><span data-ttu-id="047cd-256">L’instruction switch</span><span class="sxs-lookup"><span data-stu-id="047cd-256">The switch statement</span></span>

<span data-ttu-id="047cd-257">L’instruction switch sélectionne l’exécution d’une liste d’instructions ayant une étiquette de commutateur associé correspond à la valeur de l’expression de switch.</span><span class="sxs-lookup"><span data-stu-id="047cd-257">The switch statement selects for execution a statement list having an associated switch label that corresponds to the value of the switch expression.</span></span>

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

<span data-ttu-id="047cd-258">Un *switch_statement* se compose du mot clé `switch`par une expression entre parenthèses (appelée expression de switch), suivi par un *switch_block*.</span><span class="sxs-lookup"><span data-stu-id="047cd-258">A *switch_statement* consists of the keyword `switch`, followed by a parenthesized expression (called the switch expression), followed by a *switch_block*.</span></span> <span data-ttu-id="047cd-259">Le *switch_block* se compose de zéro ou plusieurs *switch_section*s, entre accolades.</span><span class="sxs-lookup"><span data-stu-id="047cd-259">The *switch_block* consists of zero or more *switch_section*s, enclosed in braces.</span></span> <span data-ttu-id="047cd-260">Chaque *switch_section* se compose d’une ou plusieurs *switch_label*s suivi par un *statement_list* ([instruction répertorie](statements.md#statement-lists)).</span><span class="sxs-lookup"><span data-stu-id="047cd-260">Each *switch_section* consists of one or more *switch_label*s followed by a *statement_list* ([Statement lists](statements.md#statement-lists)).</span></span>

<span data-ttu-id="047cd-261">Le ***régissant type*** d’un `switch` instruction est établie par l’expression de switch.</span><span class="sxs-lookup"><span data-stu-id="047cd-261">The ***governing type*** of a `switch` statement is established by the switch expression.</span></span>

*  <span data-ttu-id="047cd-262">Si le type de l’expression de switch est `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, ou un *type_de_liste*, ou si elle est le type nullable correspondant à un de ces types, qui est le régissant le type de la `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-262">If the type of the switch expression is `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, or an *enum_type*, or if it is the nullable type corresponding to one of these types, then that is the governing type of the `switch` statement.</span></span>
*  <span data-ttu-id="047cd-263">Sinon, un seul définis par l’utilisateur une conversion implicite ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)) doit exister entre le type de l’expression de switch à un des régissant les types possibles suivants : `sbyte`, `byte`, `short` , `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, ou un type nullable correspondant à une de ces types.</span><span class="sxs-lookup"><span data-stu-id="047cd-263">Otherwise, exactly one user-defined implicit conversion ([User-defined conversions](conversions.md#user-defined-conversions)) must exist from the type of the switch expression to one of the following possible governing types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, or,  a nullable type corresponding to one of those types.</span></span>
*  <span data-ttu-id="047cd-264">Sinon, si telle conversion implicite n’existe, ou si plus d’une telle conversion implicite existe, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-264">Otherwise, if no such implicit conversion exists, or if more than one such implicit conversion exists, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-265">L’expression constante de chaque `case` étiquette doit indiquer une valeur qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) vers le type de gouvernant le `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-265">The constant expression of each `case` label must denote a value that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the `switch` statement.</span></span> <span data-ttu-id="047cd-266">Une erreur de compilation se produit si deux ou plusieurs `case` étiquettes dans le même `switch` instruction spécifient la même valeur de constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-266">A compile-time error occurs if two or more `case` labels in the same `switch` statement specify the same constant value.</span></span>

<span data-ttu-id="047cd-267">Il peut y avoir au plus une `default` étiquette dans une instruction switch.</span><span class="sxs-lookup"><span data-stu-id="047cd-267">There can be at most one `default` label in a switch statement.</span></span>

<span data-ttu-id="047cd-268">Un `switch` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-268">A `switch` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-269">L’expression de switch est évaluée et convertie vers le type gouvernant.</span><span class="sxs-lookup"><span data-stu-id="047cd-269">The switch expression is evaluated and converted to the governing type.</span></span>
*  <span data-ttu-id="047cd-270">Si une des constantes spécifiée dans un `case` étiquette dans le même `switch` instruction est égale à la valeur de l’expression de switch, le contrôle est transféré à la liste d’instructions suivant la mise en correspondance `case` étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-270">If one of the constants specified in a `case` label in the same `switch` statement is equal to the value of the switch expression, control is transferred to the statement list following the matched `case` label.</span></span>
*  <span data-ttu-id="047cd-271">Si aucune des constantes spécifiées dans `case` étiquettes dans le même `switch` instruction est égale à la valeur de l’expression de switch et si un `default` étiquette est présente, le contrôle est transféré à l’instruction qui suit liste le `default` étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-271">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if a `default` label is present, control is transferred to the statement list following the `default` label.</span></span>
*  <span data-ttu-id="047cd-272">Si aucune des constantes spécifiées dans `case` étiquettes dans le même `switch` instruction est égale à la valeur de l’expression de switch et si aucun `default` étiquette est présente, le contrôle est transféré au point de terminaison de la `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-272">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if no `default` label is present, control is transferred to the end point of the `switch` statement.</span></span>

<span data-ttu-id="047cd-273">Si le point de terminaison de la liste d’instructions d’une section switch est accessible, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-273">If the end point of the statement list of a switch section is reachable, a compile-time error occurs.</span></span> <span data-ttu-id="047cd-274">Il s’agit en tant que la règle « aucune de passage ».</span><span class="sxs-lookup"><span data-stu-id="047cd-274">This is known as the "no fall through" rule.</span></span> <span data-ttu-id="047cd-275">L’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-275">The example</span></span>
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
<span data-ttu-id="047cd-276">est valide car aucune section switch n’a un point de terminaison accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-276">is valid because no switch section has a reachable end point.</span></span> <span data-ttu-id="047cd-277">Contrairement à C et C++, l’exécution d’une section switch est interdit à « fourre-tout » à la section switch suivante et l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-277">Unlike C and C++, execution of a switch section is not permitted to "fall through" to the next switch section, and the example</span></span>
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
<span data-ttu-id="047cd-278">génère une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="047cd-278">results in a compile-time error.</span></span> <span data-ttu-id="047cd-279">Quand l’exécution d’une section switch doit être suivie de l’exécution d’une autre section de commutation, explicite `goto case` ou `goto default` instruction doit être utilisée :</span><span class="sxs-lookup"><span data-stu-id="047cd-279">When execution of a switch section is to be followed by execution of another switch section, an explicit `goto case` or `goto default` statement must be used:</span></span>
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

<span data-ttu-id="047cd-280">Plusieurs étiquettes sont autorisées dans un *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="047cd-280">Multiple labels are permitted in a *switch_section*.</span></span> <span data-ttu-id="047cd-281">L’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-281">The example</span></span>
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
<span data-ttu-id="047cd-282">n’est valide.</span><span class="sxs-lookup"><span data-stu-id="047cd-282">is valid.</span></span> <span data-ttu-id="047cd-283">L’exemple ne viole pas la règle « aucune de passage », car les étiquettes `case 2:` et `default:` font partie du même *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="047cd-283">The example does not violate the "no fall through" rule because the labels `case 2:` and `default:` are part of the same *switch_section*.</span></span>

<span data-ttu-id="047cd-284">La règle « aucune de passage » empêche une classe commune de bogues qui se produisent en C et C++ lorsque `break` instructions sont omises par inadvertance.</span><span class="sxs-lookup"><span data-stu-id="047cd-284">The "no fall through" rule prevents a common class of bugs that occur in C and C++ when `break` statements are accidentally omitted.</span></span> <span data-ttu-id="047cd-285">En outre, en raison de cette règle, les sections switch d’un `switch` instruction peut être réorganisée arbitrairement sans affecter le comportement de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-285">In addition, because of this rule, the switch sections of a `switch` statement can be arbitrarily rearranged without affecting the behavior of the statement.</span></span> <span data-ttu-id="047cd-286">Par exemple, les sections de la `switch` instruction ci-dessus peut être annulée sans affecter le comportement de l’instruction :</span><span class="sxs-lookup"><span data-stu-id="047cd-286">For example, the sections of the `switch` statement above can be reversed without affecting the behavior of the statement:</span></span>
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

<span data-ttu-id="047cd-287">La liste d’instructions d’une section switch se termine généralement par un `break`, `goto case`, ou `goto default` instruction, mais toute construction qui rend le point de terminaison de la liste d’instructions inaccessible est autorisée.</span><span class="sxs-lookup"><span data-stu-id="047cd-287">The statement list of a switch section typically ends in a `break`, `goto case`, or `goto default` statement, but any construct that renders the end point of the statement list unreachable is permitted.</span></span> <span data-ttu-id="047cd-288">Par exemple, un `while` instruction contrôlée par l’expression booléenne `true` est connu pour ne jamais atteindre son point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="047cd-288">For example, a `while` statement controlled by the boolean expression `true` is known to never reach its end point.</span></span> <span data-ttu-id="047cd-289">De même, un `throw` ou `return` instruction toujours transfère le contrôle ailleurs et n’atteint jamais son point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="047cd-289">Likewise, a `throw` or `return` statement always transfers control elsewhere and never reaches its end point.</span></span> <span data-ttu-id="047cd-290">Par conséquent, l’exemple suivant est valide :</span><span class="sxs-lookup"><span data-stu-id="047cd-290">Thus, the following example is valid:</span></span>
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

<span data-ttu-id="047cd-291">Le type gouvernant d’un `switch` instruction peut être de type `string`.</span><span class="sxs-lookup"><span data-stu-id="047cd-291">The governing type of a `switch` statement may be the type `string`.</span></span> <span data-ttu-id="047cd-292">Exemple :</span><span class="sxs-lookup"><span data-stu-id="047cd-292">For example:</span></span>
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

<span data-ttu-id="047cd-293">Comme les opérateurs d’égalité ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)), la `switch` instruction respecte la casse et exécutera une section de commutateur donné uniquement si la chaîne d’expression de switch correspond exactement à un `case` étiquette constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-293">Like the string equality operators ([String equality operators](expressions.md#string-equality-operators)), the `switch` statement is case sensitive and will execute a given switch section only if the switch expression string exactly matches a `case` label constant.</span></span>

<span data-ttu-id="047cd-294">Lorsque le contrôle de type d’un `switch` instruction est `string`, la valeur `null` est autorisée comme une constante d’étiquette case.</span><span class="sxs-lookup"><span data-stu-id="047cd-294">When the governing type of a `switch` statement is `string`, the value `null` is permitted as a case label constant.</span></span>

<span data-ttu-id="047cd-295">Le *statement_list*s d’un *switch_block* peut contenir des instructions de déclaration ([les instructions de déclaration](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="047cd-295">The *statement_list*s of a *switch_block* may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="047cd-296">La portée d’une variable locale ou constante déclarée dans un bloc switch est le bloc switch.</span><span class="sxs-lookup"><span data-stu-id="047cd-296">The scope of a local variable or constant declared in a switch block is the switch block.</span></span>

<span data-ttu-id="047cd-297">La liste d’instructions d’une section de commutateur donné est accessible si le `switch` instruction est accessible et au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-297">The statement list of a given switch section is reachable if the `switch` statement is reachable and at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-298">L’expression de switch est une valeur non constante.</span><span class="sxs-lookup"><span data-stu-id="047cd-298">The switch expression is a non-constant value.</span></span>
*  <span data-ttu-id="047cd-299">L’expression de switch est une valeur constante qui correspond à un `case` étiquette dans la section de commutation.</span><span class="sxs-lookup"><span data-stu-id="047cd-299">The switch expression is a constant value that matches a `case` label in the switch section.</span></span>
*  <span data-ttu-id="047cd-300">L’expression de switch est une valeur constante qui ne correspond à aucune `case` étiquette et la section switch contient le `default` étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-300">The switch expression is a constant value that doesn't match any `case` label, and the switch section contains the `default` label.</span></span>
*  <span data-ttu-id="047cd-301">Une étiquette de la section de commutation switch est référencée par un accessible `goto case` ou `goto default` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-301">A switch label of the switch section is referenced by a reachable `goto case` or `goto default` statement.</span></span>

<span data-ttu-id="047cd-302">Le point de terminaison d’un `switch` instruction est accessible si au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-302">The end point of a `switch` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-303">Le `switch` instruction contient un accessible `break` instruction se termine le `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-303">The `switch` statement contains a reachable `break` statement that exits the `switch` statement.</span></span>
*  <span data-ttu-id="047cd-304">Le `switch` instruction est accessible, l’expression de switch est une valeur non constante et non `default` étiquette n’est présente.</span><span class="sxs-lookup"><span data-stu-id="047cd-304">The `switch` statement is reachable, the switch expression is a non-constant value, and no `default` label is present.</span></span>
*  <span data-ttu-id="047cd-305">Le `switch` instruction est accessible, l’expression de switch est une valeur constante qui ne correspond à aucune `case` étiquette et non `default` étiquette n’est présente.</span><span class="sxs-lookup"><span data-stu-id="047cd-305">The `switch` statement is reachable, the switch expression is a constant value that doesn't match any `case` label, and no `default` label is present.</span></span>

## <a name="iteration-statements"></a><span data-ttu-id="047cd-306">Instructions d’itération</span><span class="sxs-lookup"><span data-stu-id="047cd-306">Iteration statements</span></span>

<span data-ttu-id="047cd-307">Instructions d’itération exécuter à plusieurs reprises une instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-307">Iteration statements repeatedly execute an embedded statement.</span></span>

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a><span data-ttu-id="047cd-308">L’instruction while</span><span class="sxs-lookup"><span data-stu-id="047cd-308">The while statement</span></span>

<span data-ttu-id="047cd-309">La `while` instruction if exécute une instruction incorporée zéro ou plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="047cd-309">The `while` statement conditionally executes an embedded statement zero or more times.</span></span>

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

<span data-ttu-id="047cd-310">Un `while` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-310">A `while` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-311">Le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.</span><span class="sxs-lookup"><span data-stu-id="047cd-311">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="047cd-312">Si l’expression booléenne génère `true`, le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-312">If the boolean expression yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="047cd-313">Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), le contrôle est transféré vers le début de la `while` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-313">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), control is transferred to the beginning of the `while` statement.</span></span>
*  <span data-ttu-id="047cd-314">Si l’expression booléenne génère `false`, le contrôle est transféré au point de terminaison de la `while` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-314">If the boolean expression yields `false`, control is transferred to the end point of the `while` statement.</span></span>

<span data-ttu-id="047cd-315">Dans l’instruction incorporée d’un `while` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `while` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (exécutant ainsi une autre itération de la `while` instruction).</span><span class="sxs-lookup"><span data-stu-id="047cd-315">Within the embedded statement of a `while` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `while` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus performing another iteration of the `while` statement).</span></span>

<span data-ttu-id="047cd-316">L’instruction incorporée d’un `while` instruction est accessible si le `while` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `false`.</span><span class="sxs-lookup"><span data-stu-id="047cd-316">The embedded statement of a `while` statement is reachable if the `while` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="047cd-317">Le point de terminaison d’un `while` instruction est accessible si au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-317">The end point of a `while` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-318">Le `while` instruction contient un accessible `break` instruction se termine le `while` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-318">The `while` statement contains a reachable `break` statement that exits the `while` statement.</span></span>
*  <span data-ttu-id="047cd-319">Le `while` instruction est accessible et que l’expression booléenne n’a pas la valeur de constante `true`.</span><span class="sxs-lookup"><span data-stu-id="047cd-319">The `while` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-do-statement"></a><span data-ttu-id="047cd-320">L’instruction do</span><span class="sxs-lookup"><span data-stu-id="047cd-320">The do statement</span></span>

<span data-ttu-id="047cd-321">La `do` instruction if exécute une instruction incorporée une ou plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="047cd-321">The `do` statement conditionally executes an embedded statement one or more times.</span></span>

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

<span data-ttu-id="047cd-322">Un `do` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-322">A `do` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-323">Le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-323">Control is transferred to the embedded statement.</span></span>
*  <span data-ttu-id="047cd-324">Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), le *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)) est évaluée.</span><span class="sxs-lookup"><span data-stu-id="047cd-324">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span> <span data-ttu-id="047cd-325">Si l’expression booléenne génère `true`, le contrôle est transféré vers le début de la `do` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-325">If the boolean expression yields `true`, control is transferred to the beginning of the `do` statement.</span></span> <span data-ttu-id="047cd-326">Sinon, le contrôle est transféré au point de terminaison de la `do` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-326">Otherwise, control is transferred to the end point of the `do` statement.</span></span>

<span data-ttu-id="047cd-327">Dans l’instruction incorporée d’un `do` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `do` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-327">Within the embedded statement of a `do` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `do` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement.</span></span>

<span data-ttu-id="047cd-328">L’instruction incorporée d’un `do` instruction est accessible si le `do` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-328">The embedded statement of a `do` statement is reachable if the `do` statement is reachable.</span></span>

<span data-ttu-id="047cd-329">Le point de terminaison d’un `do` instruction est accessible si au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-329">The end point of a `do` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-330">Le `do` instruction contient un accessible `break` instruction se termine le `do` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-330">The `do` statement contains a reachable `break` statement that exits the `do` statement.</span></span>
*  <span data-ttu-id="047cd-331">Le point de terminaison de l’instruction incorporée est accessible et l’expression booléenne n’a pas la valeur de constante `true`.</span><span class="sxs-lookup"><span data-stu-id="047cd-331">The end point of the embedded statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-for-statement"></a><span data-ttu-id="047cd-332">L’instruction for</span><span class="sxs-lookup"><span data-stu-id="047cd-332">The for statement</span></span>

<span data-ttu-id="047cd-333">La `for` instruction évalue une séquence d’expressions d’initialisation et puis, lorsqu’une condition a la valeur est true, à plusieurs reprises exécute une instruction incorporée et évalue une séquence d’expressions d’itération.</span><span class="sxs-lookup"><span data-stu-id="047cd-333">The `for` statement evaluates a sequence of initialization expressions and then, while a condition is true, repeatedly executes an embedded statement and evaluates a sequence of iteration expressions.</span></span>

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

<span data-ttu-id="047cd-334">Le *for_initializer*, le cas échéant, se compose d’un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)) ou une liste de *statement_ expression*s ([instructions d’Expression](statements.md#expression-statements)) séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="047cd-334">The *for_initializer*, if present, consists of either a *local_variable_declaration* ([Local variable declarations](statements.md#local-variable-declarations)) or a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span> <span data-ttu-id="047cd-335">La portée d’une variable locale déclarée par un *for_initializer* commence au *local_variable_declarator* pour la variable et s’étend jusqu'à la fin de l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-335">The scope of a local variable declared by a *for_initializer* starts at the *local_variable_declarator* for the variable and extends to the end of the embedded statement.</span></span> <span data-ttu-id="047cd-336">L’étendue inclut la *for_condition* et *for_iterator*.</span><span class="sxs-lookup"><span data-stu-id="047cd-336">The scope includes the *for_condition* and the *for_iterator*.</span></span>

<span data-ttu-id="047cd-337">Le *for_condition*, le cas échéant, doit être un *boolean_expression* ([expressions booléennes](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="047cd-337">The *for_condition*, if present, must be a *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)).</span></span>

<span data-ttu-id="047cd-338">Le *for_iterator*, le cas échéant, se compose d’une liste de *statement_expression*s ([instructions d’Expression](statements.md#expression-statements)) séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="047cd-338">The *for_iterator*, if present, consists of a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span>

<span data-ttu-id="047cd-339">Une instruction for est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-339">A for statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-340">Si un *for_initializer* est présent, les initialiseurs de variable ou d’expressions d’instruction sont exécutées dans l’ordre qu’ils sont écrits.</span><span class="sxs-lookup"><span data-stu-id="047cd-340">If a *for_initializer* is present, the variable initializers or statement expressions are executed in the order they are written.</span></span> <span data-ttu-id="047cd-341">Cette étape est effectuée uniquement une seule fois.</span><span class="sxs-lookup"><span data-stu-id="047cd-341">This step is only performed once.</span></span>
*  <span data-ttu-id="047cd-342">Si un *for_condition* est présent, elle est évaluée.</span><span class="sxs-lookup"><span data-stu-id="047cd-342">If a *for_condition* is present, it is evaluated.</span></span>
*  <span data-ttu-id="047cd-343">Si le *for_condition* n’est pas présent ou si l’évaluation génère `true`, le contrôle est transféré à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-343">If the *for_condition* is not present or if the evaluation yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="047cd-344">Lorsqu’et si le contrôle atteint le point de terminaison de l’instruction incorporée (peut-être après l’exécution d’un `continue` instruction), les expressions de la *for_iterator*, si une, évaluées dans l’ordre, et puis une autre itération est effectuée, en commençant par la version d’évaluation de la *for_condition* à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="047cd-344">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the expressions of the *for_iterator*, if any, are evaluated in sequence, and then another iteration is performed, starting with evaluation of the *for_condition* in the step above.</span></span>
*  <span data-ttu-id="047cd-345">Si le *for_condition* est présent et l’évaluation donne `false`, le contrôle est transféré au point de terminaison de la `for` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-345">If the *for_condition* is present and the evaluation yields `false`, control is transferred to the end point of the `for` statement.</span></span>

<span data-ttu-id="047cd-346">Dans l’instruction incorporée d’un `for` instruction, un `break` instruction ([l’instruction break](statements.md#the-break-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de la `for` instruction (mettant ainsi fin de l’élément incorporé instruction) et un `continue` instruction ([l’instruction continue](statements.md#the-continue-statement)) peut être utilisé pour transférer le contrôle au point de terminaison de l’instruction incorporée (exécutant ainsi le *for_iterator* et exécution d’une autre itération de la `for` instruction, en commençant par le *for_condition*).</span><span class="sxs-lookup"><span data-stu-id="047cd-346">Within the embedded statement of a `for` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `for` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus executing the *for_iterator* and performing another iteration of the `for` statement, starting with the *for_condition*).</span></span>

<span data-ttu-id="047cd-347">L’instruction incorporée d’un `for` instruction est accessible si une des opérations suivantes est remplie :</span><span class="sxs-lookup"><span data-stu-id="047cd-347">The embedded statement of a `for` statement is reachable if one of the following is true:</span></span>

*  <span data-ttu-id="047cd-348">Le `for` instruction est accessible et aucun *for_condition* est présent.</span><span class="sxs-lookup"><span data-stu-id="047cd-348">The `for` statement is reachable and no *for_condition* is present.</span></span>
*  <span data-ttu-id="047cd-349">Le `for` instruction est accessible et une *for_condition* est présent et n’a pas la valeur de constante `false`.</span><span class="sxs-lookup"><span data-stu-id="047cd-349">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `false`.</span></span>

<span data-ttu-id="047cd-350">Le point de terminaison d’un `for` instruction est accessible si au moins une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="047cd-350">The end point of a `for` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="047cd-351">Le `for` instruction contient un accessible `break` instruction se termine le `for` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-351">The `for` statement contains a reachable `break` statement that exits the `for` statement.</span></span>
*  <span data-ttu-id="047cd-352">Le `for` instruction est accessible et une *for_condition* est présent et n’a pas la valeur de constante `true`.</span><span class="sxs-lookup"><span data-stu-id="047cd-352">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `true`.</span></span>

### <a name="the-foreach-statement"></a><span data-ttu-id="047cd-353">L’instruction foreach</span><span class="sxs-lookup"><span data-stu-id="047cd-353">The foreach statement</span></span>

<span data-ttu-id="047cd-354">La `foreach` instruction énumère les éléments d’une collection, en exécutant une instruction incorporée pour chaque élément de la collection.</span><span class="sxs-lookup"><span data-stu-id="047cd-354">The `foreach` statement enumerates the elements of a collection, executing an embedded statement for each element of the collection.</span></span>

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

<span data-ttu-id="047cd-355">Le *type* et *identificateur* d’un `foreach` instruction déclare le ***variable d’itération*** de l’instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-355">The *type* and *identifier* of a `foreach` statement declare the ***iteration variable*** of the statement.</span></span> <span data-ttu-id="047cd-356">Si le `var` identificateur donné comme le *local_variable_type*et aucun type nommé `var` est dans la portée, la variable d’itération est dite un ***variable d’itération implicitement typée***, et son type est considéré comme étant le type d’élément de la `foreach` instruction, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="047cd-356">If the `var` identifier is given as the *local_variable_type*, and no type named `var` is in scope, the iteration variable is said to be an ***implicitly typed iteration variable***, and its type is taken to be the element type of the `foreach` statement, as specified below.</span></span> <span data-ttu-id="047cd-357">La variable d’itération correspond à une variable locale en lecture seule avec une portée qui s’étend sur l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-357">The iteration variable corresponds to a read-only local variable with a scope that extends over the embedded statement.</span></span> <span data-ttu-id="047cd-358">Pendant l’exécution d’un `foreach` instruction, la variable d’itération représente l’élément de collection pour laquelle une itération est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="047cd-358">During execution of a `foreach` statement, the iteration variable represents the collection element for which an iteration is currently being performed.</span></span> <span data-ttu-id="047cd-359">Une erreur de compilation se produit si l’instruction incorporée tente de modifier la variable d’itération (via l’attribution ou le `++` et `--` opérateurs) ou passer la variable d’itération comme un `ref` ou `out` paramètre.</span><span class="sxs-lookup"><span data-stu-id="047cd-359">A compile-time error occurs if the embedded statement attempts to modify the iteration variable (via assignment or the `++` and `--` operators) or pass the iteration variable as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="047cd-360">Dans l’exemple suivant, par souci de concision, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` et `IEnumerator<T>` font référence aux types correspondants dans les espaces de noms `System.Collections` et `System.Collections.Generic`.</span><span class="sxs-lookup"><span data-stu-id="047cd-360">In the following, for brevity, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` and `IEnumerator<T>` refer to the corresponding types in the namespaces `System.Collections` and `System.Collections.Generic`.</span></span>

<span data-ttu-id="047cd-361">Le traitement de la compilation d’une instruction foreach détermine tout d’abord le ***type de collection***, ***type d’énumérateur*** et ***type d’élément*** de l’expression.</span><span class="sxs-lookup"><span data-stu-id="047cd-361">The compile-time processing of a foreach statement first determines the ***collection type***, ***enumerator type*** and ***element type*** of the expression.</span></span> <span data-ttu-id="047cd-362">Cette détermination procède comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-362">This determination proceeds as follows:</span></span>

*  <span data-ttu-id="047cd-363">Si le type `X` de *expression* est un type tableau, il y a une conversion de référence implicite à partir de `X` à la `IEnumerable` interface (dans la mesure où `System.Array` implémente cette interface).</span><span class="sxs-lookup"><span data-stu-id="047cd-363">If the type `X` of *expression* is an array type then there is an implicit reference conversion from `X` to the `IEnumerable` interface (since `System.Array` implements this interface).</span></span> <span data-ttu-id="047cd-364">Le ***type de collection*** est la `IEnumerable` interface, le ***type d’énumérateur*** est la `IEnumerator` interface et le ***type d’élément*** est le type d’élément de la type de tableau `X`.</span><span class="sxs-lookup"><span data-stu-id="047cd-364">The ***collection type*** is the `IEnumerable` interface, the ***enumerator type*** is the `IEnumerator` interface and the ***element type*** is the element type of the array type `X`.</span></span>
*  <span data-ttu-id="047cd-365">Si le type `X` de *expression* est `dynamic` , il y a une conversion implicite de *expression* à la `IEnumerable` interface ([dynamique implicite conversions](conversions.md#implicit-dynamic-conversions)).</span><span class="sxs-lookup"><span data-stu-id="047cd-365">If the type `X` of *expression* is `dynamic` then there is an implicit conversion from *expression* to the `IEnumerable` interface ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)).</span></span> <span data-ttu-id="047cd-366">Le ***type de collection*** est la `IEnumerable` interface et le ***type d’énumérateur*** est le `IEnumerator` interface.</span><span class="sxs-lookup"><span data-stu-id="047cd-366">The ***collection type*** is the `IEnumerable` interface and the ***enumerator type*** is the `IEnumerator` interface.</span></span> <span data-ttu-id="047cd-367">Si le `var` identificateur donné comme le *local_variable_type* le ***type d’élément*** est `dynamic`, sinon il est `object`.</span><span class="sxs-lookup"><span data-stu-id="047cd-367">If the `var` identifier is given as the *local_variable_type* then the ***element type*** is `dynamic`, otherwise it is `object`.</span></span>
*  <span data-ttu-id="047cd-368">Sinon, déterminez si le type `X` a approprié `GetEnumerator` méthode :</span><span class="sxs-lookup"><span data-stu-id="047cd-368">Otherwise, determine whether the type `X` has an appropriate `GetEnumerator` method:</span></span>
   * <span data-ttu-id="047cd-369">Effectuer la recherche de membres sur le type `X` avec l’identificateur `GetEnumerator` sans aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="047cd-369">Perform member lookup on the type `X` with identifier `GetEnumerator` and no type arguments.</span></span> <span data-ttu-id="047cd-370">Si la recherche de membres ne produit pas une correspondance, ou il génère une ambiguïté, ou génère une correspondance qui n’est pas un groupe de méthodes, recherchez une interface énumérable comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="047cd-370">If the member lookup does not produce a match, or it produces an ambiguity, or produces a match that is not a method group, check for an enumerable interface as described below.</span></span> <span data-ttu-id="047cd-371">Il est recommandé qu’un avertissement émis si la recherche de membres produit quoi que ce soit, à l’exception d’un groupe de méthodes ou aucune correspondance.</span><span class="sxs-lookup"><span data-stu-id="047cd-371">It is recommended that a warning be issued if member lookup produces anything except a method group or no match.</span></span>
   * <span data-ttu-id="047cd-372">Effectuer une résolution de surcharge en utilisant le groupe résultant de la méthode et une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="047cd-372">Perform overload resolution using the resulting method group and an empty argument list.</span></span> <span data-ttu-id="047cd-373">Si la résolution de surcharge aboutit à aucune méthode applicable, provoque une ambiguïté ou entraîne une meilleure méthode unique, mais cette méthode est statique ou non public, recherchez une interface énumérable comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="047cd-373">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, check for an enumerable interface as described below.</span></span> <span data-ttu-id="047cd-374">Il est recommandé qu’un avertissement est émis si la résolution de surcharge produit quoi que ce soit, à l’exception d’une méthode d’instance public non équivoque ou aucune méthode applicable.</span><span class="sxs-lookup"><span data-stu-id="047cd-374">It is recommended that a warning be issued if overload resolution produces anything except an unambiguous public instance method or no applicable methods.</span></span>
   * <span data-ttu-id="047cd-375">Si le type de retour `E` de la `GetEnumerator` méthode n’est pas une classe, type de struct ou d’interface, une erreur est généré et aucune autre étape n’est effectuées.</span><span class="sxs-lookup"><span data-stu-id="047cd-375">If the return type `E` of the `GetEnumerator` method is not a class, struct or interface type, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="047cd-376">Recherche de membres est effectuée sur `E` avec l’identificateur `Current` sans aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="047cd-376">Member lookup is performed on `E` with the identifier `Current` and no type arguments.</span></span> <span data-ttu-id="047cd-377">Si la recherche de membres ne produit aucune correspondance, le résultat est une erreur ou le résultat est une valeur autre qu’une propriété d’instance publics qui autorise la lecture, une erreur se produit et aucune autre étape n’est effectuées.</span><span class="sxs-lookup"><span data-stu-id="047cd-377">If the member lookup produces no match, the result is an error, or the result is anything except a public instance property that permits reading, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="047cd-378">Recherche de membres est effectuée sur `E` avec l’identificateur `MoveNext` sans aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="047cd-378">Member lookup is performed on `E` with the identifier `MoveNext` and no type arguments.</span></span> <span data-ttu-id="047cd-379">Si la recherche de membres ne produit aucune correspondance, le résultat est une erreur ou le résultat est une valeur autre qu’un groupe de méthodes, une erreur se produit et aucune autre étape n’est effectuées.</span><span class="sxs-lookup"><span data-stu-id="047cd-379">If the member lookup produces no match, the result is an error, or the result is anything except a method group, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="047cd-380">Résolution de surcharge est effectuée sur le groupe de méthodes avec une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="047cd-380">Overload resolution is performed on the method group with an empty argument list.</span></span> <span data-ttu-id="047cd-381">Si les résultats de résolution de surcharge dans aucune méthodes applicables, les résultats dans une ambiguïté ou entraîne une meilleure méthode unique, mais cette méthode est statique ou non public, ou son type de retour n’est pas `bool`, une erreur se produit et aucune autre étape n’est effectuées.</span><span class="sxs-lookup"><span data-stu-id="047cd-381">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, or its return type is not `bool`, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="047cd-382">Le ***type de collection*** est `X`, le ***type d’énumérateur*** est `E`et le ***type d’élément*** est le type de la `Current` propriété.</span><span class="sxs-lookup"><span data-stu-id="047cd-382">The ***collection type*** is `X`, the ***enumerator type*** is `E`, and the ***element type*** is the type of the `Current` property.</span></span>

*  <span data-ttu-id="047cd-383">Sinon, recherchez une interface énumérable :</span><span class="sxs-lookup"><span data-stu-id="047cd-383">Otherwise, check for an enumerable interface:</span></span>
   * <span data-ttu-id="047cd-384">If parmi tous les types `Ti` pour lequel il existe une conversion implicite de `X` à `IEnumerable<Ti>`, il existe un type unique `T` telles que `T` n’est pas `dynamic` et pour tous les autres `Ti` est un conversion implicite de `IEnumerable<T>` à `IEnumerable<Ti>`, puis le ***type de collection*** est l’interface `IEnumerable<T>`, le ***type d’énumérateur*** est l’interface `IEnumerator<T>`et le ***type d’élément*** est `T`.</span><span class="sxs-lookup"><span data-stu-id="047cd-384">If among all the types `Ti` for which there is an implicit conversion from `X` to `IEnumerable<Ti>`, there is a unique type `T` such that `T` is not `dynamic` and for all the other `Ti` there is an implicit conversion from `IEnumerable<T>` to `IEnumerable<Ti>`, then the ***collection type*** is the interface `IEnumerable<T>`, the ***enumerator type*** is the interface `IEnumerator<T>`, and the ***element type*** is `T`.</span></span>
   * <span data-ttu-id="047cd-385">Sinon, s’il existe plus d’un tel type `T`, une erreur se produit et aucune autre étape n’est effectuées.</span><span class="sxs-lookup"><span data-stu-id="047cd-385">Otherwise, if there is more than one such type `T`, then an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="047cd-386">Sinon, s’il existe une conversion implicite de `X` à la `System.Collections.IEnumerable` interface, puis le ***type de collection*** est cette interface, le ***type d’énumérateur*** est l’interface `System.Collections.IEnumerator`et le ***type d’élément*** est `object`.</span><span class="sxs-lookup"><span data-stu-id="047cd-386">Otherwise, if there is an implicit conversion from `X` to the `System.Collections.IEnumerable` interface, then the ***collection type*** is this interface, the ***enumerator type*** is the interface `System.Collections.IEnumerator`, and the ***element type*** is `object`.</span></span>
   * <span data-ttu-id="047cd-387">Sinon, une erreur se produit et aucune étape supplémentaire n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="047cd-387">Otherwise, an error is produced and no further steps are taken.</span></span>

<span data-ttu-id="047cd-388">Les étapes ci-dessus, en cas de réussite, sans ambiguïté produisent un type de collection `C`, type d’énumérateur `E` et type d’élément `T`.</span><span class="sxs-lookup"><span data-stu-id="047cd-388">The above steps, if successful, unambiguously produce a collection type `C`, enumerator type `E` and element type `T`.</span></span> <span data-ttu-id="047cd-389">Une instruction foreach du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-389">A foreach statement of the form</span></span>
```csharp
foreach (V v in x) embedded_statement
```
<span data-ttu-id="047cd-390">est ensuite étendue à :</span><span class="sxs-lookup"><span data-stu-id="047cd-390">is then expanded to:</span></span>
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

<span data-ttu-id="047cd-391">La variable `e` n’est pas visible à ou accessibles à l’expression `x` ou l’instruction incorporée ou tout autre code source du programme.</span><span class="sxs-lookup"><span data-stu-id="047cd-391">The variable `e` is not visible to or accessible to the expression `x` or the embedded statement or any other source code of the program.</span></span> <span data-ttu-id="047cd-392">La variable `v` est en lecture seule dans l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-392">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="047cd-393">Si n’est pas une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) à partir de `T` (le type d’élément) pour `V` (le *local_variable_type* dans l’instruction foreach), une erreur se produit et aucune étape supplémentaire n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="047cd-393">If there is not an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) from `T` (the element type) to `V` (the *local_variable_type* in the foreach statement), an error is produced and no further steps are taken.</span></span> <span data-ttu-id="047cd-394">Si `x` a la valeur `null`, un `System.NullReferenceException` est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="047cd-394">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

<span data-ttu-id="047cd-395">Une implémentation est autorisée à implémenter une instruction foreach donnée différemment, par exemple, pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="047cd-395">An implementation is permitted to implement a given foreach-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="047cd-396">Le placement de `v` à l’intérieur de le pendant que la boucle est importante pour la façon dont il est capturé par n’importe quelle fonction anonyme qui se produisent dans le *embedded_statement*.</span><span class="sxs-lookup"><span data-stu-id="047cd-396">The placement of `v` inside the while loop is important for how it is captured by any anonymous function occurring in the *embedded_statement*.</span></span>

<span data-ttu-id="047cd-397">Exemple :</span><span class="sxs-lookup"><span data-stu-id="047cd-397">For example:</span></span>
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
<span data-ttu-id="047cd-398">Si `v` a été déclaré en dehors de le pendant que boucle, il est partagé entre toutes les itérations et sa valeur après le pour boucle serait la valeur finale, `13`, c'est-à-dire quel l’appel de `f` imprimerait.</span><span class="sxs-lookup"><span data-stu-id="047cd-398">If `v` was declared outside of the while loop, it would be shared among all iterations, and its value after the for loop would be the final value, `13`, which is what the invocation of `f` would print.</span></span> <span data-ttu-id="047cd-399">Au lieu de cela, étant donné que chaque itération possède sa propre variable `v`, celui capturé par `f` dans la première itération continue à contenir la valeur `7`, qui est la zone d’impression.</span><span class="sxs-lookup"><span data-stu-id="047cd-399">Instead, because each iteration has its own variable `v`, the one captured by `f` in the first iteration will continue to hold the value `7`, which is what will be printed.</span></span> <span data-ttu-id="047cd-400">(Remarque : les versions antérieures de c# déclaré `v` boucle de l’extérieur du while.)</span><span class="sxs-lookup"><span data-stu-id="047cd-400">(Note: earlier versions of C# declared `v` outside of the while loop.)</span></span>

<span data-ttu-id="047cd-401">Le corps de l’enfin le bloc est construit en respectant les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="047cd-401">The body of the finally block is constructed according to the following steps:</span></span>

*  <span data-ttu-id="047cd-402">S’il existe une conversion implicite de `E` à la `System.IDisposable` de l’interface, puis</span><span class="sxs-lookup"><span data-stu-id="047cd-402">If there is an implicit conversion from `E` to the `System.IDisposable` interface, then</span></span>
   *  <span data-ttu-id="047cd-403">Si `E` est un type valeur non nullable l’enfin clause est développée à l’équivalent sémantique de :</span><span class="sxs-lookup"><span data-stu-id="047cd-403">If `E` is a non-nullable value type then the finally clause is expanded to the semantic equivalent  of:</span></span>

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  <span data-ttu-id="047cd-404">Sinon l’enfin clause est développée à l’équivalent sémantique de :</span><span class="sxs-lookup"><span data-stu-id="047cd-404">Otherwise the finally clause is expanded to the semantic equivalent of:</span></span>

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   <span data-ttu-id="047cd-405">à l’exception qu’en cas `E` est un type valeur ou un paramètre de type instancié pour un type valeur, puis le cast de `e` à `System.IDisposable` n’entraîne pas de boxing se produise.</span><span class="sxs-lookup"><span data-stu-id="047cd-405">except that if `E` is a value type, or a type parameter instantiated to a value type, then the cast of `e` to `System.IDisposable` will not cause boxing to occur.</span></span>

*  <span data-ttu-id="047cd-406">Sinon, si `E` est un type sealed, l’enfin clause est développée en un bloc vide :</span><span class="sxs-lookup"><span data-stu-id="047cd-406">Otherwise, if `E` is a sealed type, the finally clause is expanded to an empty block:</span></span>

   ```csharp
   finally {
   }
   ```

*  <span data-ttu-id="047cd-407">Sinon, l’enfin clause est développée pour :</span><span class="sxs-lookup"><span data-stu-id="047cd-407">Otherwise, the finally clause is expanded to:</span></span>

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   <span data-ttu-id="047cd-408">La variable locale `d` n’est pas visible à ou accessibles à n’importe quel code utilisateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-408">The local variable `d` is not visible to or accessible to any user code.</span></span> <span data-ttu-id="047cd-409">En particulier, il ne sont pas en conflit avec toute autre variable dont l’étendue inclut le bloc finally.</span><span class="sxs-lookup"><span data-stu-id="047cd-409">In particular, it does not conflict with any other variable whose scope includes the finally block.</span></span>

<span data-ttu-id="047cd-410">L’ordre dans lequel `foreach` parcourt les éléments d’un tableau, se présente comme suit : Pour les éléments de tableaux unidimensionnels sont parcourus dans l’ordre d’index croissant, en commençant par index `0` et se terminant par index `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="047cd-410">The order in which `foreach` traverses the elements of an array, is as follows: For single-dimensional arrays elements are traversed in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="047cd-411">Pour les tableaux multidimensionnels, les éléments sont parcourus telles que les index de la dimension la plus à droite sont accrues en premier, puis la dimension immédiatement à gauche, et ainsi de suite vers la gauche.</span><span class="sxs-lookup"><span data-stu-id="047cd-411">For multi-dimensional arrays, elements are traversed such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span>

<span data-ttu-id="047cd-412">L’exemple suivant imprime chaque valeur dans un tableau à deux dimensions, dans l’ordre des éléments :</span><span class="sxs-lookup"><span data-stu-id="047cd-412">The following example prints out each value in a two-dimensional array, in element order:</span></span>
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
<span data-ttu-id="047cd-413">La sortie produite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="047cd-413">The output produced is as follows:</span></span>
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

<span data-ttu-id="047cd-414">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-414">In the example</span></span>
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
<span data-ttu-id="047cd-415">le type de `n` est déduite comme étant `int`, le type d’élément de `numbers`.</span><span class="sxs-lookup"><span data-stu-id="047cd-415">the type of `n` is inferred to be `int`, the element type of `numbers`.</span></span>

## <a name="jump-statements"></a><span data-ttu-id="047cd-416">Instructions de saut</span><span class="sxs-lookup"><span data-stu-id="047cd-416">Jump statements</span></span>

<span data-ttu-id="047cd-417">Instructions de saut transfèrent sans condition le contrôle.</span><span class="sxs-lookup"><span data-stu-id="047cd-417">Jump statements unconditionally transfer control.</span></span>

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

<span data-ttu-id="047cd-418">L’emplacement auquel une instruction de saut transfère le contrôle est appelé le ***cible*** de l’instruction de saut.</span><span class="sxs-lookup"><span data-stu-id="047cd-418">The location to which a jump statement transfers control is called the ***target*** of the jump statement.</span></span>

<span data-ttu-id="047cd-419">Lorsqu’une instruction de saut se produit dans un bloc, et la cible de cette instruction de saut est en dehors de ce bloc, l’instruction de saut est dite ***quitter*** le bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-419">When a jump statement occurs within a block, and the target of that jump statement is outside that block, the jump statement is said to ***exit*** the block.</span></span> <span data-ttu-id="047cd-420">Pendant une instruction de saut peut transférer le contrôle hors d’un bloc, il peut transférer le contrôle jamais dans un bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-420">While a jump statement may transfer control out of a block, it can never transfer control into a block.</span></span>

<span data-ttu-id="047cd-421">L’exécution d’instructions de saut est compliquée par la présence d’intervenants `try` instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-421">Execution of jump statements is complicated by the presence of intervening `try` statements.</span></span> <span data-ttu-id="047cd-422">En l’absence de telles `try` instructions, une instruction de saut transfère sans condition le contrôle à partir de l’instruction de saut à sa cible.</span><span class="sxs-lookup"><span data-stu-id="047cd-422">In the absence of such `try` statements, a jump statement unconditionally transfers control from the jump statement to its target.</span></span> <span data-ttu-id="047cd-423">En présence de ce type intervenants `try` instructions, l’exécution est plus complexe.</span><span class="sxs-lookup"><span data-stu-id="047cd-423">In the presence of such intervening `try` statements, execution is more complex.</span></span> <span data-ttu-id="047cd-424">Si l’instruction de saut quitte un ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-424">If the jump statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-425">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-425">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-426">Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-426">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>

<span data-ttu-id="047cd-427">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-427">In the example</span></span>
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
<span data-ttu-id="047cd-428">le `finally` blocs associés à deux `try` instructions sont exécutées avant que le contrôle est transféré à la cible de l’instruction de saut.</span><span class="sxs-lookup"><span data-stu-id="047cd-428">the `finally` blocks associated with two `try` statements are executed before control is transferred to the target of the jump statement.</span></span>

<span data-ttu-id="047cd-429">La sortie produite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="047cd-429">The output produced is as follows:</span></span>
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a><span data-ttu-id="047cd-430">L’instruction break</span><span class="sxs-lookup"><span data-stu-id="047cd-430">The break statement</span></span>

<span data-ttu-id="047cd-431">Le `break` instruction termine l’englobante la plus proche `switch`, `while`, `do`, `for`, ou `foreach` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-431">The `break` statement exits the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
break_statement
    : 'break' ';'
    ;
```

<span data-ttu-id="047cd-432">La cible d’un `break` instruction est le point de terminaison de l’englobante la plus proche `switch`, `while`, `do`, `for`, ou `foreach` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-432">The target of a `break` statement is the end point of the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="047cd-433">Si un `break` instruction n’est pas englobée par un `switch`, `while`, `do`, `for`, ou `foreach` instruction, une erreur de compilation survient.</span><span class="sxs-lookup"><span data-stu-id="047cd-433">If a `break` statement is not enclosed by a `switch`, `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-434">Lorsque plusieurs `switch`, `while`, `do`, `for`, ou `foreach` instructions sont imbriquées dans d’autres, un `break` instruction s’applique uniquement à l’instruction plus profonde.</span><span class="sxs-lookup"><span data-stu-id="047cd-434">When multiple `switch`, `while`, `do`, `for`, or `foreach` statements are nested within each other, a `break` statement applies only to the innermost statement.</span></span> <span data-ttu-id="047cd-435">Pour transférer le contrôle sur plusieurs niveaux d’imbrication, un `goto` instruction ([l’instruction goto](statements.md#the-goto-statement)) doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="047cd-435">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="047cd-436">Un `break` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-436">A `break` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="047cd-437">Quand un `break` instruction se produit dans un `finally` bloquer, la cible de la `break` instruction doit être dans le même `finally` bloquer ; sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-437">When a `break` statement occurs within a `finally` block, the target of the `break` statement must be within the same `finally` block; otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-438">Un `break` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-438">A `break` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-439">Si le `break` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-439">If the `break` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-440">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-440">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-441">Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-441">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="047cd-442">Le contrôle est transféré à la cible de la `break` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-442">Control is transferred to the target of the `break` statement.</span></span>

<span data-ttu-id="047cd-443">Étant donné qu’un `break` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `break` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-443">Because a `break` statement unconditionally transfers control elsewhere, the end point of a `break` statement is never reachable.</span></span>

### <a name="the-continue-statement"></a><span data-ttu-id="047cd-444">L’instruction continue</span><span class="sxs-lookup"><span data-stu-id="047cd-444">The continue statement</span></span>

<span data-ttu-id="047cd-445">Le `continue` instruction démarre une nouvelle itération de l’englobante la plus proche `while`, `do`, `for`, ou `foreach` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-445">The `continue` statement starts a new iteration of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
continue_statement
    : 'continue' ';'
    ;
```

<span data-ttu-id="047cd-446">La cible d’un `continue` instruction est le point de terminaison de l’instruction incorporée de l’englobante la plus proche `while`, `do`, `for`, ou `foreach` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-446">The target of a `continue` statement is the end point of the embedded statement of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="047cd-447">Si un `continue` instruction n’est pas englobée par un `while`, `do`, `for`, ou `foreach` instruction, une erreur de compilation survient.</span><span class="sxs-lookup"><span data-stu-id="047cd-447">If a `continue` statement is not enclosed by a `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-448">Lorsque plusieurs `while`, `do`, `for`, ou `foreach` instructions sont imbriquées dans d’autres, un `continue` instruction s’applique uniquement à l’instruction plus profonde.</span><span class="sxs-lookup"><span data-stu-id="047cd-448">When multiple `while`, `do`, `for`, or `foreach` statements are nested within each other, a `continue` statement applies only to the innermost statement.</span></span> <span data-ttu-id="047cd-449">Pour transférer le contrôle sur plusieurs niveaux d’imbrication, un `goto` instruction ([l’instruction goto](statements.md#the-goto-statement)) doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="047cd-449">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="047cd-450">Un `continue` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-450">A `continue` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="047cd-451">Quand un `continue` instruction se produit dans un `finally` bloquer, la cible de la `continue` instruction doit être dans le même `finally` bloquer ; sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-451">When a `continue` statement occurs within a `finally` block, the target of the `continue` statement must be within the same `finally` block; otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-452">Un `continue` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-452">A `continue` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-453">Si le `continue` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-453">If the `continue` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-454">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-454">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-455">Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-455">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="047cd-456">Le contrôle est transféré à la cible de la `continue` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-456">Control is transferred to the target of the `continue` statement.</span></span>

<span data-ttu-id="047cd-457">Étant donné qu’un `continue` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `continue` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-457">Because a `continue` statement unconditionally transfers control elsewhere, the end point of a `continue` statement is never reachable.</span></span>

### <a name="the-goto-statement"></a><span data-ttu-id="047cd-458">Instruction goto</span><span class="sxs-lookup"><span data-stu-id="047cd-458">The goto statement</span></span>

<span data-ttu-id="047cd-459">La `goto` instruction transfère le contrôle à une instruction qui est marquée par une étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-459">The `goto` statement transfers control to a statement that is marked by a label.</span></span>

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

<span data-ttu-id="047cd-460">La cible d’un `goto` *identificateur* instruction est une instruction étiquetée avec l’étiquette donnée.</span><span class="sxs-lookup"><span data-stu-id="047cd-460">The target of a `goto` *identifier* statement is the labeled statement with the given label.</span></span> <span data-ttu-id="047cd-461">Si une étiquette portant le nom spécifié n’existe pas dans la fonction membre en cours, ou si le `goto` instruction n’est pas dans la portée de l’étiquette, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-461">If a label with the given name does not exist in the current function member, or if the `goto` statement is not within the scope of the label, a compile-time error occurs.</span></span> <span data-ttu-id="047cd-462">Cette règle autorise l’utilisation d’un `goto` instruction pour transférer le contrôle hors d’une étendue imbriquée, mais pas dans une étendue imbriquée.</span><span class="sxs-lookup"><span data-stu-id="047cd-462">This rule permits the use of a `goto` statement to transfer control out of a nested scope, but not into a nested scope.</span></span> <span data-ttu-id="047cd-463">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-463">In the example</span></span>
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
<span data-ttu-id="047cd-464">un `goto` instruction est utilisée pour transférer le contrôle hors d’une étendue imbriquée.</span><span class="sxs-lookup"><span data-stu-id="047cd-464">a `goto` statement is used to transfer control out of a nested scope.</span></span>

<span data-ttu-id="047cd-465">La cible d’un `goto case` instruction est la liste d’instructions dans immédiatement englobante `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)), qui contient un `case` étiquette avec la valeur de constante donnée.</span><span class="sxs-lookup"><span data-stu-id="047cd-465">The target of a `goto case` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `case` label with the given constant value.</span></span> <span data-ttu-id="047cd-466">Si le `goto case` instruction n’est pas englobée par un `switch` instruction, si le *constant_expression* n’est pas implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) vers le type gouvernant de la le plus proche englobante `switch` instruction, ou si l’englobante la plus proche `switch` instruction ne contient-elle pas un `case` étiquette avec la valeur de constante donnée, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-466">If the `goto case` statement is not enclosed by a `switch` statement, if the *constant_expression* is not implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the nearest enclosing `switch` statement, or if the nearest enclosing `switch` statement does not contain a `case` label with the given constant value, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-467">La cible d’un `goto default` instruction est la liste d’instructions dans immédiatement englobante `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)), qui contient un `default` étiquette.</span><span class="sxs-lookup"><span data-stu-id="047cd-467">The target of a `goto default` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `default` label.</span></span> <span data-ttu-id="047cd-468">Si le `goto default` instruction n’est pas englobée par un `switch` instruction, ou si l’englobante la plus proche `switch` instruction ne contient-elle pas un `default` de l’étiquette, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-468">If the `goto default` statement is not enclosed by a `switch` statement, or if the nearest enclosing `switch` statement does not contain a `default` label, a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-469">Un `goto` instruction ne peut pas quitter une `finally` bloc ([l’instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-469">A `goto` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="047cd-470">Quand un `goto` instruction se produit dans un `finally` bloquer, la cible de la `goto` instruction doit être dans le même `finally` bloc, ou sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-470">When a `goto` statement occurs within a `finally` block, the target of the `goto` statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-471">Un `goto` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-471">A `goto` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-472">Si le `goto` instruction quitte une ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-472">If the `goto` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-473">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-473">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-474">Ce processus est répété jusqu'à ce que le `finally` blocs de tous les intervenants `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-474">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="047cd-475">Le contrôle est transféré à la cible de la `goto` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-475">Control is transferred to the target of the `goto` statement.</span></span>

<span data-ttu-id="047cd-476">Étant donné qu’un `goto` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `goto` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-476">Because a `goto` statement unconditionally transfers control elsewhere, the end point of a `goto` statement is never reachable.</span></span>

### <a name="the-return-statement"></a><span data-ttu-id="047cd-477">L’instruction return</span><span class="sxs-lookup"><span data-stu-id="047cd-477">The return statement</span></span>

<span data-ttu-id="047cd-478">Le `return` instruction retourne le contrôle à l’appelant actuel de la fonction dans laquelle la `return` instruction apparaît.</span><span class="sxs-lookup"><span data-stu-id="047cd-478">The `return` statement returns control to the current caller of the function in which the `return` statement appears.</span></span>

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

<span data-ttu-id="047cd-479">Un `return` instruction sans expression peut être utilisée uniquement dans une fonction membre qui ne calcule pas une valeur, autrement dit, une méthode avec le type de résultat ([corps de la méthode](classes.md#method-body)) `void`, le `set` accesseur d’une propriété ou indexeur, le `add` et `remove` accesseurs d’un événement, un constructeur d’instance, un constructeur statique ou un destructeur.</span><span class="sxs-lookup"><span data-stu-id="047cd-479">A `return` statement with no expression can be used only in a function member that does not compute a value, that is, a method with the result type ([Method body](classes.md#method-body)) `void`, the `set` accessor of a property or indexer, the `add` and `remove` accessors of an event, an instance constructor, a static constructor, or a destructor.</span></span>

<span data-ttu-id="047cd-480">Un `return` instruction avec une expression peut uniquement être utilisée dans une fonction membre qui calcule une valeur, autrement dit, une méthode avec un type de résultat de non void, le `get` accesseur d’une propriété ou indexeur ou un opérateur défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-480">A `return` statement with an expression can only be used in a function member that computes a value, that is, a method with a non-void result type, the `get` accessor of a property or indexer, or a user-defined operator.</span></span> <span data-ttu-id="047cd-481">Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l’expression pour le type de retour de la fonction membre conteneur.</span><span class="sxs-lookup"><span data-stu-id="047cd-481">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression to the return type of the containing function member.</span></span>

<span data-ttu-id="047cd-482">Retourner les instructions peuvent également être utilisées dans le corps d’expressions de fonction anonyme ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)) et participer à déterminer quelles conversions existent pour ces fonctions.</span><span class="sxs-lookup"><span data-stu-id="047cd-482">Return statements can also be used in the body of anonymous function expressions ([Anonymous function expressions](expressions.md#anonymous-function-expressions)), and participate in determining which conversions exist for those functions.</span></span>

<span data-ttu-id="047cd-483">Il s’agit d’une erreur lors de la compilation pour un `return` instruction apparaissent dans un `finally` bloc ([l’instruction try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-483">It is a compile-time error for a `return` statement to appear in a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="047cd-484">Un `return` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-484">A `return` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-485">Si la `return` instruction spécifie une expression, l’expression est évaluée et la valeur résultante est convertie au type de retour de la fonction conteneur par une conversion implicite.</span><span class="sxs-lookup"><span data-stu-id="047cd-485">If the `return` statement specifies an expression, the expression is evaluated and the resulting value is converted to the return type of the containing function by an implicit conversion.</span></span> <span data-ttu-id="047cd-486">Le résultat de la conversion devient la valeur de résultat produite par la fonction.</span><span class="sxs-lookup"><span data-stu-id="047cd-486">The result of the conversion becomes the result value produced by the function.</span></span>
*  <span data-ttu-id="047cd-487">Si le `return` instruction est englobée par un ou plusieurs `try` ou `catch` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-487">If the `return` statement is enclosed by one or more `try` or `catch` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-488">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-488">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-489">Ce processus est répété jusqu'à ce que le `finally` bloque tous les caractères entourant `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-489">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="047cd-490">Si la fonction qui le contient n’est pas une fonction asynchrone, le contrôle est retourné à l’appelant de la fonction contenante, ainsi que la valeur de résultat, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="047cd-490">If the containing function is not an async function, control is returned to the caller of the containing function along with the result value, if any.</span></span>
*  <span data-ttu-id="047cd-491">Si la fonction conteneur est une fonction async, contrôle est retourné à l’appelant actuel, et la valeur de résultat, le cas échéant, est enregistrée dans la tâche retournée comme décrit dans ([interfaces d’énumérateur](classes.md#enumerator-interfaces)).</span><span class="sxs-lookup"><span data-stu-id="047cd-491">If the containing function is an async function, control is returned to the current caller, and the result value, if any, is recorded in the return task as described in ([Enumerator interfaces](classes.md#enumerator-interfaces)).</span></span>

<span data-ttu-id="047cd-492">Étant donné qu’un `return` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `return` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-492">Because a `return` statement unconditionally transfers control elsewhere, the end point of a `return` statement is never reachable.</span></span>

### <a name="the-throw-statement"></a><span data-ttu-id="047cd-493">L’instruction throw</span><span class="sxs-lookup"><span data-stu-id="047cd-493">The throw statement</span></span>

<span data-ttu-id="047cd-494">La `throw` instruction lève une exception.</span><span class="sxs-lookup"><span data-stu-id="047cd-494">The `throw` statement throws an exception.</span></span>

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

<span data-ttu-id="047cd-495">Un `throw` instruction avec une expression lève la valeur produite en évaluant l’expression.</span><span class="sxs-lookup"><span data-stu-id="047cd-495">A `throw` statement with an expression throws the value produced by evaluating the expression.</span></span> <span data-ttu-id="047cd-496">L’expression doit indiquer une valeur de type classe `System.Exception`, d’un type de classe qui dérive de `System.Exception` ou d’un type de paramètre de type a `System.Exception` (ou une sous-classe) en tant que sa classe de base efficace.</span><span class="sxs-lookup"><span data-stu-id="047cd-496">The expression must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="047cd-497">Si l’évaluation de l’expression génère `null`, un `System.NullReferenceException` est levée à la place.</span><span class="sxs-lookup"><span data-stu-id="047cd-497">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="047cd-498">Un `throw` instruction sans expression peut être utilisée uniquement dans un `catch` bloquer, auquel cas cette instruction lève à nouveau l’exception qui est gérée par cette `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-498">A `throw` statement with no expression can be used only in a `catch` block, in which case that statement re-throws the exception that is currently being handled by that `catch` block.</span></span>

<span data-ttu-id="047cd-499">Étant donné qu’un `throw` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `throw` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-499">Because a `throw` statement unconditionally transfers control elsewhere, the end point of a `throw` statement is never reachable.</span></span>

<span data-ttu-id="047cd-500">Lorsqu’une exception est levée, le contrôle est transféré à la première `catch` clause dans une englobante `try` instruction qui peut gérer l’exception.</span><span class="sxs-lookup"><span data-stu-id="047cd-500">When an exception is thrown, control is transferred to the first `catch` clause in an enclosing `try` statement that can handle the exception.</span></span> <span data-ttu-id="047cd-501">Le processus qui a lieu à partir du point de l’exception levée au point de transfert de contrôle vers un gestionnaire d’exceptions approprié est appelé ***la propagation d’exception***.</span><span class="sxs-lookup"><span data-stu-id="047cd-501">The process that takes place from the point of the exception being thrown to the point of transferring control to a suitable exception handler is known as ***exception propagation***.</span></span> <span data-ttu-id="047cd-502">La propagation d’une exception se compose de plusieurs fois l’évaluation de la procédure suivante avant un `catch` clause qui correspond à l’exception est trouvé.</span><span class="sxs-lookup"><span data-stu-id="047cd-502">Propagation of an exception consists of repeatedly evaluating the following steps until a `catch` clause that matches the exception is found.</span></span> <span data-ttu-id="047cd-503">Dans cette description, le ***lever point*** est initialement l’emplacement auquel l’exception est levée.</span><span class="sxs-lookup"><span data-stu-id="047cd-503">In this description, the ***throw point*** is initially the location at which the exception is thrown.</span></span>

*  <span data-ttu-id="047cd-504">Dans la fonction membre en cours, chaque `try` instruction qui englobe le point de levée est examinée.</span><span class="sxs-lookup"><span data-stu-id="047cd-504">In the current function member, each `try` statement that encloses the throw point is examined.</span></span> <span data-ttu-id="047cd-505">Pour chaque instruction `S`, en commençant par la plus intérieure `try` instruction et se terminant par le plus externe `try` instruction, les étapes suivantes sont évaluées :</span><span class="sxs-lookup"><span data-stu-id="047cd-505">For each statement `S`, starting with the innermost `try` statement and ending with the outermost `try` statement, the following steps are evaluated:</span></span>

   * <span data-ttu-id="047cd-506">Si le `try` bloquer de `S` englobe le point de levée et que S a un ou plusieurs `catch` clauses, le `catch` clauses sont examinées dans l’ordre d’apparition pour trouver un gestionnaire approprié pour l’exception, selon les règles spécifiées dans Section [l’instruction try](statements.md#the-try-statement).</span><span class="sxs-lookup"><span data-stu-id="047cd-506">If the `try` block of `S` encloses the throw point and if S has one or more `catch` clauses, the `catch` clauses are examined in order of appearance to locate a suitable handler for the exception, according to the rules specified in Section [The try statement](statements.md#the-try-statement).</span></span> <span data-ttu-id="047cd-507">Si une correspondance `catch` clause se trouve, la propagation des exceptions est terminée en transférant le contrôle au bloc de qui `catch` clause.</span><span class="sxs-lookup"><span data-stu-id="047cd-507">If a matching `catch` clause is located, the exception propagation is completed by transferring control to the block of that `catch` clause.</span></span>

   * <span data-ttu-id="047cd-508">Sinon, si le `try` bloc ou une `catch` bloquer de `S` englobe le point de levée et si `S` a un `finally` bloc, le contrôle est transféré vers le `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-508">Otherwise, if the `try` block or a `catch` block of `S` encloses the throw point and if `S` has a `finally` block, control is transferred to the `finally` block.</span></span> <span data-ttu-id="047cd-509">Si le `finally` bloc lève une autre exception, le traitement de l’exception actuelle est interrompu.</span><span class="sxs-lookup"><span data-stu-id="047cd-509">If the `finally` block throws another exception, processing of the current exception is terminated.</span></span> <span data-ttu-id="047cd-510">Sinon, lorsque le contrôle atteint le point de terminaison de la `finally` bloc, la suite de traitement de l’exception actuelle.</span><span class="sxs-lookup"><span data-stu-id="047cd-510">Otherwise, when control reaches the end point of the `finally` block, processing of the current exception is continued.</span></span>

*  <span data-ttu-id="047cd-511">Si un gestionnaire d’exceptions ne se trouvait pas dans l’appel de fonction en cours, l’appel de fonction se termine, et une des actions suivantes se produit :</span><span class="sxs-lookup"><span data-stu-id="047cd-511">If an exception handler was not located in the current function invocation, the function invocation is terminated, and one of the following occurs:</span></span>

   * <span data-ttu-id="047cd-512">Si la fonction actuelle est non asynchrone, les étapes ci-dessus sont répétées pour l’appelant de la fonction avec un point de levée correspondant à l’instruction à partir de laquelle la fonction membre a été appelée.</span><span class="sxs-lookup"><span data-stu-id="047cd-512">If the current function is non-async, the steps above are repeated for the caller of the function with a throw point corresponding to the statement from which the function member was invoked.</span></span>

   * <span data-ttu-id="047cd-513">Si la fonction active est asynchrone et retournant des tâches, l’exception est enregistrée dans la tâche de retournée, qui est placée dans un état défectueux ou annulé, comme décrit dans [interfaces d’énumérateur](classes.md#enumerator-interfaces).</span><span class="sxs-lookup"><span data-stu-id="047cd-513">If the current function is async and task-returning, the exception is recorded in the return task, which is put into a faulted or cancelled state as described in [Enumerator interfaces](classes.md#enumerator-interfaces).</span></span>

   * <span data-ttu-id="047cd-514">Si la fonction active est asynchrone et qui retournent void, le contexte de synchronisation du thread actuel est notifié comme décrit dans [interfaces énumérables](classes.md#enumerable-interfaces).</span><span class="sxs-lookup"><span data-stu-id="047cd-514">If the current function is async and void-returning, the synchronization context of the current thread is notified as described in [Enumerable interfaces](classes.md#enumerable-interfaces).</span></span>

*  <span data-ttu-id="047cd-515">Si le traitement des exceptions met fin à tous les appels de fonctions membres dans le thread actuel, qui indique que le thread n’a aucun gestionnaire pour l’exception, puis le thread est lui-même s’est arrêté.</span><span class="sxs-lookup"><span data-stu-id="047cd-515">If the exception processing terminates all function member invocations in the current thread, indicating that the thread has no handler for the exception, then the thread is itself terminated.</span></span> <span data-ttu-id="047cd-516">L’impact d’un tel arrêt est défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="047cd-516">The impact of such termination is implementation-defined.</span></span>

## <a name="the-try-statement"></a><span data-ttu-id="047cd-517">L’instruction try</span><span class="sxs-lookup"><span data-stu-id="047cd-517">The try statement</span></span>

<span data-ttu-id="047cd-518">La `try` instruction fournit un mécanisme d’interception des exceptions qui se produisent pendant l’exécution d’un bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-518">The `try` statement provides a mechanism for catching exceptions that occur during execution of a block.</span></span> <span data-ttu-id="047cd-519">En outre, le `try` instruction offre la possibilité de spécifier un bloc de code qui est toujours exécuté lorsque le contrôle quitte la `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-519">Furthermore, the `try` statement provides the ability to specify a block of code that is always executed when control leaves the `try` statement.</span></span>

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

<span data-ttu-id="047cd-520">Il existe trois formes possibles de `try` instructions :</span><span class="sxs-lookup"><span data-stu-id="047cd-520">There are three possible forms of `try` statements:</span></span>

*  <span data-ttu-id="047cd-521">Un `try` bloc suivie d’une ou plusieurs `catch` blocs.</span><span class="sxs-lookup"><span data-stu-id="047cd-521">A `try` block followed by one or more `catch` blocks.</span></span>
*  <span data-ttu-id="047cd-522">Un `try` bloc suivi par un `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-522">A `try` block followed by a `finally` block.</span></span>
*  <span data-ttu-id="047cd-523">Un `try` bloc suivie d’une ou plusieurs `catch` blocs suivi par un `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-523">A `try` block followed by one or more `catch` blocks followed by a `finally` block.</span></span>

<span data-ttu-id="047cd-524">Quand un `catch` clause spécifie un *exception_specifier*, le type doit être `System.Exception`, un type qui dérive de `System.Exception` ou un type de paramètre de type qui a `System.Exception` (ou une sous-classe) en tant que son effet classe de base.</span><span class="sxs-lookup"><span data-stu-id="047cd-524">When a `catch` clause specifies an *exception_specifier*, the type must be `System.Exception`, a type that derives from `System.Exception` or a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span>

<span data-ttu-id="047cd-525">Quand un `catch` clause spécifie à la fois un *exception_specifier* avec un *identificateur*, un ***variable d’exception*** du prénom et du type est déclaré.</span><span class="sxs-lookup"><span data-stu-id="047cd-525">When a `catch` clause specifies both an *exception_specifier* with an *identifier*, an ***exception variable*** of the given name and type is declared.</span></span> <span data-ttu-id="047cd-526">La variable d’exception correspond à une variable locale avec une portée qui s’étend sur la `catch` clause.</span><span class="sxs-lookup"><span data-stu-id="047cd-526">The exception variable corresponds to a local variable with a scope that extends over the `catch` clause.</span></span> <span data-ttu-id="047cd-527">Pendant l’exécution de la *exception_filter* et *bloc*, la variable d’exception représente l’exception en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="047cd-527">During execution of the *exception_filter* and *block*, the exception variable represents the exception currently being handled.</span></span> <span data-ttu-id="047cd-528">À des fins de vérification de l’assignation définie, la variable d’exception est considérée comme définitivement assignée dans toute sa portée.</span><span class="sxs-lookup"><span data-stu-id="047cd-528">For purposes of definite assignment checking, the exception variable is considered definitely assigned in its entire scope.</span></span>

<span data-ttu-id="047cd-529">Sauf si un `catch` clause inclut un nom de variable d’exception, il est impossible d’accéder à l’objet d’exception dans le filtre et `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-529">Unless a `catch` clause includes an exception variable name, it is impossible to access the exception object in the filter and `catch` block.</span></span>

<span data-ttu-id="047cd-530">Un `catch` clause qui ne spécifie pas un *exception_specifier* est appelée un général `catch` clause.</span><span class="sxs-lookup"><span data-stu-id="047cd-530">A `catch` clause that does not specify an *exception_specifier* is called a general `catch` clause.</span></span>

<span data-ttu-id="047cd-531">Certains langages de programmation peuvent prendre en charge les exceptions qui ne sont pas qui peut être représentées comme un objet dérivé `System.Exception`, bien que ces exceptions ne peuvent jamais être générées par le code C#.</span><span class="sxs-lookup"><span data-stu-id="047cd-531">Some programming languages may support exceptions that are not representable as an object derived from `System.Exception`, although such exceptions could never be generated by C# code.</span></span> <span data-ttu-id="047cd-532">Un grand `catch` clause peut être utilisée pour intercepter de telles exceptions.</span><span class="sxs-lookup"><span data-stu-id="047cd-532">A general `catch` clause may be used to catch such exceptions.</span></span> <span data-ttu-id="047cd-533">Par conséquent, un grand `catch` clause est sémantiquement différente à partir d’un objet qui spécifie le type `System.Exception`, car la première peut également intercepter des exceptions à partir d’autres langages.</span><span class="sxs-lookup"><span data-stu-id="047cd-533">Thus, a general `catch` clause is semantically different from one that specifies the type `System.Exception`, in that the former may also catch exceptions from other languages.</span></span>

<span data-ttu-id="047cd-534">Afin de trouver le gestionnaire pour une exception, `catch` clauses sont examinées dans l’ordre lexical.</span><span class="sxs-lookup"><span data-stu-id="047cd-534">In order to locate a handler for an exception, `catch` clauses are examined in lexical order.</span></span> <span data-ttu-id="047cd-535">Si un `catch` clause spécifie un type, mais aucun filtre d’exception, c’est une erreur de compilation pour une version ultérieure `catch` clause dans la même `try` instruction pour spécifier un type qui est identique, ou est dérivé, que vous tapez.</span><span class="sxs-lookup"><span data-stu-id="047cd-535">If a `catch` clause specifies a type but no exception filter, it is a compile-time error for a later `catch` clause in the same `try` statement to specify a type that is the same as, or is derived from, that type.</span></span> <span data-ttu-id="047cd-536">Si un `catch` clause spécifie aucun type et aucun filtre, il doit être le dernier `catch` clause pour ce `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-536">If a `catch` clause specifies no type and no filter, it must be the last `catch` clause for that `try` statement.</span></span>

<span data-ttu-id="047cd-537">Au sein d’un `catch` bloc, un `throw` instruction ([l’instruction throw](statements.md#the-throw-statement)) sans expression peut être utilisé pour lever à nouveau l’exception qui a été interceptée par le `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-537">Within a `catch` block, a `throw` statement ([The throw statement](statements.md#the-throw-statement)) with no expression can be used to re-throw the exception that was caught by the `catch` block.</span></span> <span data-ttu-id="047cd-538">Assignations à une variable d’exception ne modifient pas l’exception est levée de nouveau.</span><span class="sxs-lookup"><span data-stu-id="047cd-538">Assignments to an exception variable do not alter the exception that is re-thrown.</span></span>

<span data-ttu-id="047cd-539">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="047cd-539">In the example</span></span>
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
<span data-ttu-id="047cd-540">la méthode `F` intercepte une exception, écrit des informations de diagnostic dans la console, modifie la variable d’exception et lève à nouveau l’exception.</span><span class="sxs-lookup"><span data-stu-id="047cd-540">the method `F` catches an exception, writes some diagnostic information to the console, alters the exception variable, and re-throws the exception.</span></span> <span data-ttu-id="047cd-541">L’exception est levée de nouveau est l’exception d’origine, donc le résultat obtenu est :</span><span class="sxs-lookup"><span data-stu-id="047cd-541">The exception that is re-thrown is the original exception, so the output produced is:</span></span>
```
Exception in F: G
Exception in Main: G
```

<span data-ttu-id="047cd-542">Si le premier bloc catch avait levé `e` au lieu de lever de nouveau l’exception actuelle, le résultat obtenu se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-542">If the first catch block had thrown `e` instead of rethrowing the current exception, the output produced would be as follows:</span></span>
```csharp
Exception in F: G
Exception in Main: F
```

<span data-ttu-id="047cd-543">Il s’agit d’une erreur lors de la compilation pour un `break`, `continue`, ou `goto` instruction pour transférer le contrôle d’un `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-543">It is a compile-time error for a `break`, `continue`, or `goto` statement to transfer control out of a `finally` block.</span></span> <span data-ttu-id="047cd-544">Quand un `break`, `continue`, ou `goto` instruction s’exécute dans un `finally` bloc, la cible de l’instruction doit être dans le même `finally` bloc, ou sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="047cd-544">When a `break`, `continue`, or `goto` statement occurs in a `finally` block, the target of the statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="047cd-545">Il s’agit d’une erreur lors de la compilation pour un `return` instruction peut se produire dans un `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-545">It is a compile-time error for a `return` statement to occur in a `finally` block.</span></span>

<span data-ttu-id="047cd-546">Un `try` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-546">A `try` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-547">Le contrôle est transféré à la `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-547">Control is transferred to the `try` block.</span></span>
*  <span data-ttu-id="047cd-548">Lorsqu’et si le contrôle atteint le point de terminaison de la `try` bloc :</span><span class="sxs-lookup"><span data-stu-id="047cd-548">When and if control reaches the end point of the `try` block:</span></span>
   *  <span data-ttu-id="047cd-549">Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="047cd-549">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
   *  <span data-ttu-id="047cd-550">Le contrôle est transféré au point de terminaison de la `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-550">Control is transferred to the end point of the `try` statement.</span></span>

*  <span data-ttu-id="047cd-551">Si une exception est propagée vers le `try` instruction pendant l’exécution de la `try` bloc :</span><span class="sxs-lookup"><span data-stu-id="047cd-551">If an exception is propagated to the `try` statement during execution of the `try` block:</span></span>
   *  <span data-ttu-id="047cd-552">Le `catch` clauses, le cas échéant, sont examinées dans l’ordre d’apparition pour trouver un gestionnaire approprié pour l’exception.</span><span class="sxs-lookup"><span data-stu-id="047cd-552">The `catch` clauses, if any, are examined in order of appearance to locate a suitable handler for the exception.</span></span> <span data-ttu-id="047cd-553">Si un `catch` clause ne spécifie pas un type, ou spécifie le type d’exception ou un type de base du type d’exception :</span><span class="sxs-lookup"><span data-stu-id="047cd-553">If a `catch` clause does not specify a type, or specifies the exception type or a base type of the exception type:</span></span>
      *  <span data-ttu-id="047cd-554">Si le `catch` clause déclare une variable d’exception, l’objet exception est attribuée à la variable d’exception.</span><span class="sxs-lookup"><span data-stu-id="047cd-554">If the `catch` clause declares an exception variable, the exception object is assigned to the exception variable.</span></span>
      *  <span data-ttu-id="047cd-555">Si le `catch` clause déclare un filtre d’exception, le filtre est évalué.</span><span class="sxs-lookup"><span data-stu-id="047cd-555">If the `catch` clause declares an exception filter, the filter is evaluated.</span></span> <span data-ttu-id="047cd-556">Si elle a la valeur `false`, la clause catch n’est pas une correspondance, et la recherche se poursuit via suivantes `catch` clauses pour un gestionnaire approprié.</span><span class="sxs-lookup"><span data-stu-id="047cd-556">If it evaluates to `false`, the catch clause is not a match, and the search continues through any subsequent `catch` clauses for a suitable handler.</span></span>
      *  <span data-ttu-id="047cd-557">Sinon, le `catch` clause est considéré comme une correspondance, et le contrôle est transféré à la mise en correspondance `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="047cd-557">Otherwise, the `catch` clause is considered a match, and control is transferred to the matching `catch` block.</span></span>
      *  <span data-ttu-id="047cd-558">Lorsqu’et si le contrôle atteint le point de terminaison de la `catch` bloc :</span><span class="sxs-lookup"><span data-stu-id="047cd-558">When and if control reaches the end point of the `catch` block:</span></span>
         * <span data-ttu-id="047cd-559">Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="047cd-559">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         * <span data-ttu-id="047cd-560">Le contrôle est transféré au point de terminaison de la `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-560">Control is transferred to the end point of the `try` statement.</span></span>
      *  <span data-ttu-id="047cd-561">Si une exception est propagée vers le `try` instruction pendant l’exécution de la `catch` bloc :</span><span class="sxs-lookup"><span data-stu-id="047cd-561">If an exception is propagated to the `try` statement during execution of the `catch` block:</span></span>
         *  <span data-ttu-id="047cd-562">Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="047cd-562">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         *  <span data-ttu-id="047cd-563">L’exception est propagée vers la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-563">The exception is propagated to the next enclosing `try` statement.</span></span>
   *  <span data-ttu-id="047cd-564">Si le `try` instruction n’a aucun `catch` clauses ou si aucun `catch` clause correspond à l’exception :</span><span class="sxs-lookup"><span data-stu-id="047cd-564">If the `try` statement has no `catch` clauses or if no `catch` clause matches the exception:</span></span>
      *  <span data-ttu-id="047cd-565">Si le `try` instruction comporte un `finally` bloc, le `finally` bloc est exécuté.</span><span class="sxs-lookup"><span data-stu-id="047cd-565">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
      *  <span data-ttu-id="047cd-566">L’exception est propagée vers la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-566">The exception is propagated to the next enclosing `try` statement.</span></span>

<span data-ttu-id="047cd-567">Les instructions d’un `finally` bloc sont toujours exécutées lorsque le contrôle quitte un `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-567">The statements of a `finally` block are always executed when control leaves a `try` statement.</span></span> <span data-ttu-id="047cd-568">Cela est vrai si le transfert de contrôle se produit suite à une exécution normale, suite à l’exécution un `break`, `continue`, `goto`, ou `return` instruction, ou à la suite de propagation d’une exception de la `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-568">This is true whether the control transfer occurs as a result of normal execution, as a result of executing a `break`, `continue`, `goto`, or `return` statement, or as a result of propagating an exception out of the `try` statement.</span></span>

<span data-ttu-id="047cd-569">Si une exception est levée pendant l’exécution d’un `finally` bloquer et n’est pas interceptée dans le même bloc finally, l’exception est propagée vers la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-569">If an exception is thrown during execution of a `finally` block, and is not caught within the same finally block, the exception is propagated to the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-570">Si une autre exception était en cours de propagation, cette exception est perdue.</span><span class="sxs-lookup"><span data-stu-id="047cd-570">If another exception was in the process of being propagated, that exception is lost.</span></span> <span data-ttu-id="047cd-571">Le processus de propagation d’une exception est abordé plus en détail dans la description de la `throw` instruction ([l’instruction throw](statements.md#the-throw-statement)).</span><span class="sxs-lookup"><span data-stu-id="047cd-571">The process of propagating an exception is discussed further in the description of the `throw` statement ([The throw statement](statements.md#the-throw-statement)).</span></span>

<span data-ttu-id="047cd-572">Le `try` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-572">The `try` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="047cd-573">Un `catch` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-573">A `catch` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="047cd-574">Le `finally` de blocs à un `try` instruction est accessible si le `try` instruction est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-574">The `finally` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="047cd-575">Le point de terminaison d’un `try` instruction est accessible si des opérations suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="047cd-575">The end point of a `try` statement is reachable if both of the following are true:</span></span>

*  <span data-ttu-id="047cd-576">Le point de terminaison de la `try` bloc est accessible ou point de la fin d’au moins une `catch` bloc est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-576">The end point of the `try` block is reachable or the end point of at least one `catch` block is reachable.</span></span>
*  <span data-ttu-id="047cd-577">Si un `finally` bloc est présent, le point de terminaison de la `finally` bloc est accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-577">If a `finally` block is present, the end point of the `finally` block is reachable.</span></span>

## <a name="the-checked-and-unchecked-statements"></a><span data-ttu-id="047cd-578">Les instructions checked et unchecked</span><span class="sxs-lookup"><span data-stu-id="047cd-578">The checked and unchecked statements</span></span>

<span data-ttu-id="047cd-579">Le `checked` et `unchecked` instructions permettent de contrôler le ***contexte de vérification de dépassement de capacité*** pour les conversions et les opérations arithmétiques de type intégral.</span><span class="sxs-lookup"><span data-stu-id="047cd-579">The `checked` and `unchecked` statements are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

<span data-ttu-id="047cd-580">Le `checked` instruction provoque toutes les expressions dans le *bloc* à évaluer dans un contexte vérifié et le `unchecked` instruction provoque toutes les expressions dans le *bloc* à évaluer dans un un contexte non vérifié.</span><span class="sxs-lookup"><span data-stu-id="047cd-580">The `checked` statement causes all expressions in the *block* to be evaluated in a checked context, and the `unchecked` statement causes all expressions in the *block* to be evaluated in an unchecked context.</span></span>

<span data-ttu-id="047cd-581">Le `checked` et `unchecked` instructions sont précisément équivalentes à la `checked` et `unchecked` opérateurs ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)), à ceci près qu’ils opèrent sur les blocs au lieu d’expressions .</span><span class="sxs-lookup"><span data-stu-id="047cd-581">The `checked` and `unchecked` statements are precisely equivalent to the `checked` and `unchecked` operators ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)), except that they operate on blocks instead of expressions.</span></span>

## <a name="the-lock-statement"></a><span data-ttu-id="047cd-582">L’instruction lock</span><span class="sxs-lookup"><span data-stu-id="047cd-582">The lock statement</span></span>

<span data-ttu-id="047cd-583">La `lock` instruction Obtient le verrou d’exclusion mutuelle pour un objet donné, exécute une instruction, puis libère le verrou.</span><span class="sxs-lookup"><span data-stu-id="047cd-583">The `lock` statement obtains the mutual-exclusion lock for a given object, executes a statement, and then releases the lock.</span></span>

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

<span data-ttu-id="047cd-584">L’expression d’un `lock` instruction doit indiquer une valeur d’un type connu pour être un *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="047cd-584">The expression of a `lock` statement must denote a value of a type known to be a *reference_type*.</span></span> <span data-ttu-id="047cd-585">Aucune conversion boxing implicite ([conversions Boxing](conversions.md#boxing-conversions)) n’est jamais effectuée pour l’expression d’un `lock` instruction et par conséquent, il est une erreur de compilation pour l’expression indiquer une valeur d’un *value_type*.</span><span class="sxs-lookup"><span data-stu-id="047cd-585">No implicit boxing conversion ([Boxing conversions](conversions.md#boxing-conversions)) is ever performed for the expression of a `lock` statement, and thus it is a compile-time error for the expression to denote a value of a *value_type*.</span></span>

<span data-ttu-id="047cd-586">Un `lock` instruction du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-586">A `lock` statement of the form</span></span>
```csharp
lock (x) ...
```
<span data-ttu-id="047cd-587">où `x` est une expression d’un *reference_type*, est équivalente à</span><span class="sxs-lookup"><span data-stu-id="047cd-587">where `x` is an expression of a *reference_type*, is precisely equivalent to</span></span>
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
<span data-ttu-id="047cd-588">sauf que `x` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="047cd-588">except that `x` is only evaluated once.</span></span>

<span data-ttu-id="047cd-589">Lorsqu’un verrou d’exclusion mutuelle est appliqué, l’exécution de code dans le même thread d’exécution peut également obtenir et libérer le verrou.</span><span class="sxs-lookup"><span data-stu-id="047cd-589">While a mutual-exclusion lock is held, code executing in the same execution thread can also obtain and release the lock.</span></span> <span data-ttu-id="047cd-590">Toutefois, l’exécution de code dans d’autres threads ne peut pas obtenir le verrou jusqu'à ce que le verrou est libéré.</span><span class="sxs-lookup"><span data-stu-id="047cd-590">However, code executing in other threads is blocked from obtaining the lock until the lock is released.</span></span>

<span data-ttu-id="047cd-591">Verrouillage `System.Type` objets afin de synchroniser l’accès aux données statiques n’est pas recommandée.</span><span class="sxs-lookup"><span data-stu-id="047cd-591">Locking `System.Type` objects in order to synchronize access to static data is not recommended.</span></span> <span data-ttu-id="047cd-592">Tout autre code risque de verrouiller sur le même type, ce qui peut entraîner un blocage.</span><span class="sxs-lookup"><span data-stu-id="047cd-592">Other code might lock on the same type, which can result in deadlock.</span></span> <span data-ttu-id="047cd-593">Une meilleure approche consiste à synchroniser l’accès aux données statiques en verrouillant un objet statique privé.</span><span class="sxs-lookup"><span data-stu-id="047cd-593">A better approach is to synchronize access to static data by locking a private static object.</span></span> <span data-ttu-id="047cd-594">Exemple :</span><span class="sxs-lookup"><span data-stu-id="047cd-594">For example:</span></span>
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

## <a name="the-using-statement"></a><span data-ttu-id="047cd-595">Instruction using</span><span class="sxs-lookup"><span data-stu-id="047cd-595">The using statement</span></span>

<span data-ttu-id="047cd-596">La `using` instruction Obtient une ou plusieurs ressources, exécute une instruction, puis supprime la ressource.</span><span class="sxs-lookup"><span data-stu-id="047cd-596">The `using` statement obtains one or more resources, executes a statement, and then disposes of the resource.</span></span>

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

<span data-ttu-id="047cd-597">Un ***ressource*** est une classe ou un struct qui implémente `System.IDisposable`, qui inclut une méthode sans paramètre unique nommée `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="047cd-597">A ***resource*** is a class or struct that implements `System.IDisposable`, which includes a single parameterless method named `Dispose`.</span></span> <span data-ttu-id="047cd-598">Le code qui utilise une ressource peut appeler `Dispose` pour indiquer que la ressource n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="047cd-598">Code that is using a resource can call `Dispose` to indicate that the resource is no longer needed.</span></span> <span data-ttu-id="047cd-599">Si `Dispose` n’est pas appelée, élimination automatique se produit par la suite par conséquent le garbage collection.</span><span class="sxs-lookup"><span data-stu-id="047cd-599">If `Dispose` is not called, then automatic disposal eventually occurs as a consequence of garbage collection.</span></span>

<span data-ttu-id="047cd-600">Si la forme de *resource_acquisition* est *local_variable_declaration* , le type de la *local_variable_declaration* doit avoir la valeur `dynamic` ou un type qui peut être converti implicitement en `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="047cd-600">If the form of *resource_acquisition* is *local_variable_declaration* then the type of the *local_variable_declaration* must be either `dynamic` or a type that can be implicitly converted to `System.IDisposable`.</span></span> <span data-ttu-id="047cd-601">Si la forme de *resource_acquisition* est *expression* , cette expression doit être implicitement convertible en `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="047cd-601">If the form of *resource_acquisition* is *expression* then this expression must be implicitly convertible to `System.IDisposable`.</span></span>

<span data-ttu-id="047cd-602">Les variables locales déclarées dans un *resource_acquisition* sont en lecture seule et doit inclure un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="047cd-602">Local variables declared in a *resource_acquisition* are read-only, and must include an initializer.</span></span> <span data-ttu-id="047cd-603">Une erreur de compilation se produit si l’instruction incorporée tente de modifier ces variables locales (via l’attribution ou le `++` et `--` opérateurs), qui utilisent l’adresse d'entre eux ou les passer en tant que `ref` ou `out` paramètres.</span><span class="sxs-lookup"><span data-stu-id="047cd-603">A compile-time error occurs if the embedded statement attempts to modify these local variables (via assignment or the `++` and `--` operators) , take the address of them, or pass them as `ref` or `out` parameters.</span></span>

<span data-ttu-id="047cd-604">Un `using` instruction est traduite en trois parties : acquisition, de l’utilisation et de suppression.</span><span class="sxs-lookup"><span data-stu-id="047cd-604">A `using` statement is translated into three parts: acquisition, usage, and disposal.</span></span> <span data-ttu-id="047cd-605">L’utilisation de la ressource est englobée implicitement dans une `try` instruction qui inclut un `finally` clause.</span><span class="sxs-lookup"><span data-stu-id="047cd-605">Usage of the resource is implicitly enclosed in a `try` statement that includes a `finally` clause.</span></span> <span data-ttu-id="047cd-606">Cela `finally` clause supprime la ressource.</span><span class="sxs-lookup"><span data-stu-id="047cd-606">This `finally` clause disposes of the resource.</span></span> <span data-ttu-id="047cd-607">Si un `null` l’acquisition de ressources, puis aucun appel à `Dispose` est effectuée, et aucune exception n’est levée.</span><span class="sxs-lookup"><span data-stu-id="047cd-607">If a `null` resource is acquired, then no call to `Dispose` is made, and no exception is thrown.</span></span> <span data-ttu-id="047cd-608">Si la ressource est de type `dynamic` il est converti dynamiquement via une conversion implicite dynamique ([les conversions implicites dynamiques](conversions.md#implicit-dynamic-conversions)) à `IDisposable` pendant l’acquisition d’afin de garantir que la conversion est réussi avant l’utilisation et la suppression.</span><span class="sxs-lookup"><span data-stu-id="047cd-608">If the resource is of type `dynamic` it is dynamically converted through an implicit dynamic conversion ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)) to `IDisposable` during acquisition in order to ensure that the conversion is successful before the usage and disposal.</span></span>

<span data-ttu-id="047cd-609">Un `using` instruction du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-609">A `using` statement of the form</span></span>
```csharp
using (ResourceType resource = expression) statement
```
<span data-ttu-id="047cd-610">correspond à un des trois expansions possibles.</span><span class="sxs-lookup"><span data-stu-id="047cd-610">corresponds to one of three possible expansions.</span></span> <span data-ttu-id="047cd-611">Lorsque `ResourceType` est un type valeur non nullable, l’expansion est</span><span class="sxs-lookup"><span data-stu-id="047cd-611">When `ResourceType` is a non-nullable value type, the expansion is</span></span>
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

<span data-ttu-id="047cd-612">Sinon, lorsque `ResourceType` est un type valeur nullable ou un type référence autre que `dynamic`, l’expansion est</span><span class="sxs-lookup"><span data-stu-id="047cd-612">Otherwise, when `ResourceType` is a nullable value type or a reference type other than `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="047cd-613">Sinon, lorsque `ResourceType` est `dynamic`, l’expansion est</span><span class="sxs-lookup"><span data-stu-id="047cd-613">Otherwise, when `ResourceType` is `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="047cd-614">Dans les deux expansions, la `resource` variable est en lecture seule dans l’instruction incorporée et le `d` variable est inaccessible dans et invisible à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-614">In either expansion, the `resource` variable is read-only in the embedded statement, and the `d` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="047cd-615">Une implémentation est autorisée à implémenter une instruction using donnée différemment, par exemple, pour des raisons de performances, tant que le comportement est cohérent avec l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="047cd-615">An implementation is permitted to implement a given using-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="047cd-616">Un `using` instruction du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-616">A `using` statement of the form</span></span>
```csharp
using (expression) statement
```
<span data-ttu-id="047cd-617">a les expansions possibles trois mêmes.</span><span class="sxs-lookup"><span data-stu-id="047cd-617">has the same three possible expansions.</span></span> <span data-ttu-id="047cd-618">Dans ce cas `ResourceType` est implicitement le type de compilation de la `expression`, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="047cd-618">In this case `ResourceType` is implicitly the compile-time type of the `expression`, if it has one.</span></span> <span data-ttu-id="047cd-619">Sinon, l’interface `IDisposable` lui-même est utilisé comme le `ResourceType`.</span><span class="sxs-lookup"><span data-stu-id="047cd-619">Otherwise the interface `IDisposable` itself is used as the `ResourceType`.</span></span> <span data-ttu-id="047cd-620">Le `resource` variable est inaccessible dans et invisible à l’instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="047cd-620">The `resource` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="047cd-621">Quand un *resource_acquisition* prend la forme d’un *local_variable_declaration*, il est possible d’acquérir plusieurs ressources d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="047cd-621">When a *resource_acquisition* takes the form of a *local_variable_declaration*, it is possible to acquire multiple resources of a given type.</span></span> <span data-ttu-id="047cd-622">Un `using` instruction du formulaire</span><span class="sxs-lookup"><span data-stu-id="047cd-622">A `using` statement of the form</span></span>
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
<span data-ttu-id="047cd-623">imbriqué avec précision équivalent à une séquence de `using` instructions :</span><span class="sxs-lookup"><span data-stu-id="047cd-623">is precisely equivalent to a sequence of nested `using` statements:</span></span>
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

<span data-ttu-id="047cd-624">L’exemple ci-dessous crée un fichier nommé `log.txt` et écrit deux lignes de texte dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="047cd-624">The example below creates a file named `log.txt` and writes two lines of text to the file.</span></span> <span data-ttu-id="047cd-625">L’exemple ouvre ce même fichier pour la lecture, puis copie les lignes de relation contenant-contenus de texte dans la console.</span><span class="sxs-lookup"><span data-stu-id="047cd-625">The example then opens that same file for reading and copies the contained lines of text to the console.</span></span>
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

<span data-ttu-id="047cd-626">Dans la mesure où le `TextWriter` et `TextReader` classes implémentent le `IDisposable` interface, l’exemple peut utiliser `using` des instructions pour les opérations de lecture ou de vous assurer que le fichier sous-jacent est fermé correctement après l’écriture.</span><span class="sxs-lookup"><span data-stu-id="047cd-626">Since the `TextWriter` and `TextReader` classes implement the `IDisposable` interface, the example can use `using` statements to ensure that the underlying file is properly closed following the write or read operations.</span></span>

## <a name="the-yield-statement"></a><span data-ttu-id="047cd-627">L’instruction yield</span><span class="sxs-lookup"><span data-stu-id="047cd-627">The yield statement</span></span>

<span data-ttu-id="047cd-628">Le `yield` instruction est utilisée dans un bloc itérateur ([blocs](statements.md#blocks)) afin de produire une valeur à l’objet énumérateur ([objets d’énumérateur](classes.md#enumerator-objects)) ou un objet énumérable ([objets énumérables](classes.md#enumerable-objects)) d’un itérateur ou pour signaler la fin de l’itération.</span><span class="sxs-lookup"><span data-stu-id="047cd-628">The `yield` statement is used in an iterator block ([Blocks](statements.md#blocks)) to yield a value to the enumerator object ([Enumerator objects](classes.md#enumerator-objects)) or enumerable object ([Enumerable objects](classes.md#enumerable-objects)) of an iterator or to signal the end of the iteration.</span></span>

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

<span data-ttu-id="047cd-629">`yield` n’est pas un mot réservé ; Il a une signification spéciale uniquement lorsque utilisé qu’immédiatement avant une `return` ou `break` mot clé.</span><span class="sxs-lookup"><span data-stu-id="047cd-629">`yield` is not a reserved word; it has special meaning only when used immediately before a `return` or `break` keyword.</span></span> <span data-ttu-id="047cd-630">Dans d’autres contextes, `yield` peut être utilisé en tant qu’identificateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-630">In other contexts, `yield` can be used as an identifier.</span></span>

<span data-ttu-id="047cd-631">Il existe plusieurs restrictions sur l’emplacement un `yield` instruction peut apparaître, comme décrit dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="047cd-631">There are several restrictions on where a `yield` statement can appear, as described in the following.</span></span>

*  <span data-ttu-id="047cd-632">Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) à apparaître en dehors d’un *method_body*, *operator_body* ou *accessor_body*</span><span class="sxs-lookup"><span data-stu-id="047cd-632">It is a compile-time error for a `yield` statement (of either form) to appear outside a *method_body*, *operator_body* or *accessor_body*</span></span>
*  <span data-ttu-id="047cd-633">Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) à apparaître à l’intérieur d’une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="047cd-633">It is a compile-time error for a `yield` statement (of either form) to appear inside an anonymous function.</span></span>
*  <span data-ttu-id="047cd-634">Il s’agit d’une erreur lors de la compilation pour un `yield` déclaration (de des deux formes) apparaissent dans le `finally` clause d’une `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-634">It is a compile-time error for a `yield` statement (of either form) to appear in the `finally` clause of a `try` statement.</span></span>
*  <span data-ttu-id="047cd-635">Il s’agit d’une erreur lors de la compilation pour un `yield return` instruction à apparaître n’importe où dans un `try` instruction qui contient des `catch` clauses.</span><span class="sxs-lookup"><span data-stu-id="047cd-635">It is a compile-time error for a `yield return` statement to appear anywhere in a `try` statement that contains any `catch` clauses.</span></span>

<span data-ttu-id="047cd-636">L’exemple suivant montre certaines utilisations valides et non valides de `yield` instructions.</span><span class="sxs-lookup"><span data-stu-id="047cd-636">The following example shows some valid and invalid uses of `yield` statements.</span></span>

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

<span data-ttu-id="047cd-637">Une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) doit exister à partir du type de l’expression dans le `yield return` instruction vers le type yield ([Yield type](classes.md#yield-type)) de l’itérateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-637">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression in the `yield return` statement to the yield type ([Yield type](classes.md#yield-type)) of the iterator.</span></span>

<span data-ttu-id="047cd-638">Un `yield return` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-638">A `yield return` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-639">L’expression donnée dans l’instruction est évaluée, implicitement convertie vers le type yield et assignée à la `Current` propriété de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-639">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
*  <span data-ttu-id="047cd-640">L’exécution du bloc itérateur est suspendue.</span><span class="sxs-lookup"><span data-stu-id="047cd-640">Execution of the iterator block is suspended.</span></span> <span data-ttu-id="047cd-641">Si le `yield return` instruction se trouve dans un ou plusieurs `try` bloque associé `finally` blocs ne sont pas exécutées pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="047cd-641">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
*  <span data-ttu-id="047cd-642">Le `MoveNext` méthode de l’objet énumérateur renvoie `true` à son appelant, indiquant que l’objet énumérateur a réussi à avancer jusqu'à l’élément suivant.</span><span class="sxs-lookup"><span data-stu-id="047cd-642">The `MoveNext` method of the enumerator object returns `true` to its caller, indicating that the enumerator object successfully advanced to the next item.</span></span>

<span data-ttu-id="047cd-643">L’appel suivant à l’objet énumérateur `MoveNext` méthode reprend l’exécution du bloc itérateur à partir de l’endroit où il a été suspendue en dernier.</span><span class="sxs-lookup"><span data-stu-id="047cd-643">The next call to the enumerator object's `MoveNext` method resumes execution of the iterator block from where it was last suspended.</span></span>

<span data-ttu-id="047cd-644">Un `yield break` instruction est exécutée comme suit :</span><span class="sxs-lookup"><span data-stu-id="047cd-644">A `yield break` statement is executed as follows:</span></span>

*  <span data-ttu-id="047cd-645">Si le `yield break` instruction est englobée par un ou plusieurs `try` associés de blocs avec `finally` blocs, le contrôle est transféré au départ à la `finally` bloc de la plus intérieure `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-645">If the `yield break` statement is enclosed by one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="047cd-646">Lorsqu’et si le contrôle atteint le point de terminaison d’un `finally` bloc, le contrôle est transféré vers le `finally` bloc de la forme suivante `try` instruction.</span><span class="sxs-lookup"><span data-stu-id="047cd-646">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="047cd-647">Ce processus est répété jusqu'à ce que le `finally` bloque tous les caractères entourant `try` instructions ont été exécutées.</span><span class="sxs-lookup"><span data-stu-id="047cd-647">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="047cd-648">Contrôle est retourné à l’appelant du bloc itérateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-648">Control is returned to the caller of the iterator block.</span></span> <span data-ttu-id="047cd-649">Il s’agit soit du `MoveNext` méthode ou `Dispose` méthode de l’objet énumérateur.</span><span class="sxs-lookup"><span data-stu-id="047cd-649">This is either the `MoveNext` method or `Dispose` method of the enumerator object.</span></span>

<span data-ttu-id="047cd-650">Étant donné qu’un `yield break` instruction inconditionnellement transfère le contrôle par ailleurs, le point de terminaison d’un `yield break` instruction n’est jamais accessible.</span><span class="sxs-lookup"><span data-stu-id="047cd-650">Because a `yield break` statement unconditionally transfers control elsewhere, the end point of a `yield break` statement is never reachable.</span></span>
